# Guava重试例子

### maven依赖
```
<dependency> 
    <groupId>com.github.rholder</groupId> 
    <artifactId>guava-retrying</artifactId> 
    <version>2.0.0</version> 
</dependency> 
```
### 重试代码事例
```
    public static void retryerBuilder(DoCallable doit) {
        Callable<Boolean> callable = doit.getCallMethodResult();
        Retryer<Boolean> retryer = RetryerBuilder.<Boolean>newBuilder()
                .retryIfResult(Predicates.<Boolean>isNull())
                .retryIfExceptionOfType(IOException.class)
                .retryIfRuntimeException()
                .withStopStrategy(StopStrategies.stopAfterAttempt(3))
                .build();
        try {
            retryer.call(callable);
        } catch (RetryException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
```