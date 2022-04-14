# 用Rxjava对callback形式代码的改造



#  核心:

Observalbe.create() + flatmap



# 问题1:

每个Observalbe.create()内部的emiter必须调用onComplete,否则最终onComplete不会回调

```java
      List<Integer> nums = new ArrayList<>();
        nums.add(1);
        nums.add(9);
        AtomicInteger atomicInteger = new AtomicInteger(2);
        Observable.fromIterable(nums)
                .subscribeOn(Schedulers.io())
                .flatMap(new Function<Integer, ObservableSource<String>>() {
                    @Override
                    public ObservableSource<String> apply(@io.reactivex.annotations.NonNull Integer integer) throws Exception {
                        return Observable.create(new ObservableOnSubscribe<String>() {
                            @Override
                            public void subscribe(@io.reactivex.annotations.NonNull ObservableEmitter<String> emitter) throws Exception {
                                //Thread.sleep(1500);
                                emitter.onNext(integer+"+flatMap(Observable.create)");
                                emitter.onComplete();
                                //todo 每一个都要调用onComplete,而不能自己计数.下面代码是错误的
                                /*int i = atomicInteger.decrementAndGet();
                                if(i ==0){
                                    LogUtils.i("flatMap-emitter.onComplete()");
                                    emitter.onComplete();
                                }*/
                            }
                        }).observeOn(Schedulers.io());//.observeOn(Schedulers.io()
                    }
                }).observeOn(AndroidSchedulers.mainThread())
                .timeout(5, TimeUnit.SECONDS)
                .subscribe(new Observer<String>() {
                    @Override
                    public void onSubscribe(@io.reactivex.annotations.NonNull Disposable d) {

                    }

                    @Override
                    public void onNext(@io.reactivex.annotations.NonNull String s) {
                        LogUtils.w("onNext",s);
                    }

                    @Override
                    public void onError(@io.reactivex.annotations.NonNull Throwable e) {
                        LogUtils.w("onError",e);
                    }

                    @Override
                    public void onComplete() {
                        LogUtils.i("oncomplete");

                    }
                });
```







## 示例

```java
try {
                    requestByType(LocationManager.NETWORK_PROVIDER, locationManager, map, countSet, finalListener1);
                    requestByType(LocationManager.GPS_PROVIDER, locationManager, map, countSet, finalListener1);
                    requestByType(LocationManager.PASSIVE_PROVIDER, locationManager, map, countSet, finalListener1);
                    //requestByType("fused", locationManager, map, countSet, finalListener1);
                    if (isGmsAvaiable(finalContext)) {
                        requestGmsLocation(finalContext, locationManager, map, countSet, finalListener1);
                        //return;
                    }
                } catch (Throwable throwable) {
                    throwable.printStackTrace();
                }
```

改造:

> flatMap内部使用Observable.create

```java
private void byFlatMap(Context context,  MyLocationCallback listener, LocationManager locationManager, List<String> providers, long start, int size) {
        AtomicInteger atomicInteger = new AtomicInteger(0);
        List<Pair<String,Location>> pairList = new ArrayList<>();

        Observable.fromIterable(providers)
                .flatMap(new Function<String, ObservableSource<Pair<String,Location>>>() {
                    @Override
                    public ObservableSource<Pair<String,Location>> apply(@io.reactivex.annotations.NonNull String provider) throws Exception {
                        return Observable.create(new ObservableOnSubscribe<Pair<String,Location>>() {
                            @Override
                            @SuppressLint("MissingPermission")
                            public void subscribe(@io.reactivex.annotations.NonNull ObservableEmitter<Pair<String,Location>> emitter) throws Exception {

                                LogUtils.i("flatMap", "requestSingleUpdate-" + provider);
                                //locationManager.getCurrentLocation(provider,);
                                locationManager.requestSingleUpdate(provider, new LocationListener() {
                                    @Override
                                    public void onLocationChanged(@NonNull Location location) {
                                        LogUtils.d("onLocationChanged", location, provider, "耗时(ms):", (System.currentTimeMillis() - start));
                                        saveLocation2(location);
                                        emitter.onNext(new Pair<>(provider,location));
                                        locationManager.removeUpdates(this);
                                        //todo 重要: flatMap内部的Observable.create: 因为创建了多个Observable,必须每个Observable都调用onComplete,才能触发最终observer的onComplete
                                        emitter.onComplete();
                                    }

                                    @Override
                                    public void onStatusChanged(String provider, int status, Bundle extras) {
                                        LogUtils.d("onStatusChanged", provider, status, extras);
                                    }

                                    @Override
                                    public void onProviderDisabled(@NonNull String provider) {
                                        LogUtils.w("onProviderDisabled", provider);
                                        locationManager.removeUpdates(this);
                                        //用着用着突然关掉了,会触发这里
                                        emitter.onComplete();
                                    }

                                    @Override
                                    public void onProviderEnabled(@NonNull String provider) {
                                        LogUtils.d("onProviderEnabled", provider);
                                    }
                                }, handlerThread.getLooper());
                            }
                            //要这里指定subscribeOn(Schedulers.io()),才能让每个Observable在不同的线程工作
                        }).subscribeOn(Schedulers.io());
                    }
                }).mergeWith(Observable.create(new ObservableOnSubscribe<Pair<String,Location>>() {
                        @SuppressLint("MissingPermission")
                        @Override
                        public void subscribe(@io.reactivex.annotations.NonNull ObservableEmitter<Pair<String,Location>> emitter) throws Exception {
                            //if-else的代码直接在Observable内部实现
                            if (isGmsAvaiable(context)) {
                                LocationServices.getFusedLocationProviderClient(context)
                                        .requestLocationUpdates(new LocationRequest()
                                                .setExpirationDuration(timeOut)
                                                .setNumUpdates(1)
                                                .setMaxWaitTime(timeOut), new LocationCallback() {
                                            @Override
                                            public void onLocationResult(LocationResult result) {
                                                LogUtils.i("gms result", result);
                                                if (result != null && result.getLocations() != null && !result.getLocations().isEmpty()) {
                                                    Location location = result.getLocations().get(0);
                                                    saveLocation2(location);
                                                    emitter.onNext(new Pair<>("gms",location));
                                                }
                                                emitter.onComplete();
                                            }

                                        }, handlerThread.getLooper());
                            } else {
                                //如果没有onNext,那就直接发onComplete,否则最后Observer无法调用onComplete
                                emitter.onComplete();
                            }

                        }})
                .subscribeOn(Schedulers.io()))
                .timeout(timeOut, TimeUnit.MILLISECONDS)
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(new Observer<Pair<String,Location>>() {
                    @Override
                    public void onSubscribe(@io.reactivex.annotations.NonNull Disposable d) {

                    }

                    @Override
                    public void onNext(@io.reactivex.annotations.NonNull Pair<String,Location> location) {
                        LogUtils.i("onNext", location);
                        pairList.add(location);
                        int i = atomicInteger.incrementAndGet();
                        if(i ==1){
                            LogUtils.i("onQuickest", location);
                            listener.onQuickestLocationCallback(location.second, location.first);
                        }
                        listener.onEachLocationChanged(location.second, location.first);
                    }

                    @Override
                    public void onError(@io.reactivex.annotations.NonNull Throwable e) {
                        LogUtils.w("onError", e);

                        dealOnFail(e, pairList,listener);
                    }

                    @Override
                    public void onComplete() {
                        LogUtils.i("onComplete");
                        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.JELLY_BEAN_MR2) {
                            handlerThread.quitSafely();
                        }
                        Pair<String,Location> mostAcurLocation2 = getMostAcurLocation2(pairList);
                        if(mostAcurLocation2 == null){
                            onError(new RuntimeException("getMostAcurLocation2 null"));
                        }else {
                            listener.onSuccess(mostAcurLocation2.second,"from sys api :"+ mostAcurLocation2.first);
                        }

                    }
                });
    }
```

# 问题2:如何连接多个observable?

> 一个方法本身返回Observable,其内部要执行一段逻辑,然后在回调里调用另外一个Observable
>
> 先使用Observable.create, 然后调用flatmap即可

```java
    private static Observable<Pair<String,Location>> checkPermission(Context context, int timeout, boolean showBeforeRequest, boolean showAfterRequest, MyLocationCallback callback) {
        if (PermissionUtils.isGranted(Manifest.permission.ACCESS_COARSE_LOCATION)
                && PermissionUtils.isGranted(Manifest.permission.ACCESS_FINE_LOCATION)) {
          return   doRequestLocation(context, timeout, callback);
        } else {
            return Observable.create(new ObservableOnSubscribe<Pair<String, Location>>() {
                @Override
                public void subscribe(@io.reactivex.annotations.NonNull ObservableEmitter<Pair<String, Location>> emitter) throws Exception {
                    MyPermissions.requestByMostEffort(
                            showBeforeRequest,
                            showAfterRequest,
                            new PermissionUtils.FullCallback() {
                                @Override
                                public void onGranted(@NonNull List<String> granted) {
                                    emitter.onNext(new Pair<>("doRequestLocation",null));
                                    emitter.onComplete();
                                }

                                @Override
                                public void onDenied(@NonNull List<String> deniedForever, @NonNull List<String> denied) {
                                    //callback.onFailed(1, "no permission",true);
                                    emitter.onError(new LocationFailException("no permission").setFailBeforeReallyRequest(true));
                                }
                            }, Manifest.permission.ACCESS_FINE_LOCATION);
                }
            }).subscribeOn(AndroidSchedulers.mainThread())
                    .flatMap(new Function<Pair<String, Location>, ObservableSource<Pair<String, Location>>>() {
                        @Override
                        public ObservableSource<Pair<String, Location>> apply(@io.reactivex.annotations.NonNull Pair<String, Location> stringLocationPair) throws Exception {

                            return doRequestLocation(context, timeout, callback);
                        }
                    });

        }
    }
```

