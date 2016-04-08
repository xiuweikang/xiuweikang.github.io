title: Retrofit分享
date: 2016-03-17 19:39:30
tags: Android
---

## Retrofit简介
Retrofit是Square开发的一个可以用于Android和Java的网络请求封装库。

### 优点
1. Retrofit可以利用接口，方法和注解来创建一个请求，方便管理。
2. Retrofit速度较快![image](http://www.jcodecraeer.com/uploads/20150418/1429351303115820.png)
3. 可将response自动包装成Java对象

### 依赖配置

在1.x默认集成Gson库，不会集成OkHttp,如果需要OkHttp作为HTTP client，则需手动添加依赖

``` java
dependencies {  
    // Retrofit & OkHttp
    compile 'com.squareup.retrofit:retrofit:1.9.0'
    compile 'com.squareup.okhttp:okhttp:2.7.2'
}
```

在2.0时默认集成OkHttp，不会集成Gson

``` java
dependencies {  
    // Retrofit & Gson
    compile 'com.squareup.retrofit2:retrofit:2.0.0'
    compile 'com.squareup.retrofit2:converter-gson:2.0.0'
}
```
<!--more-->

### API声明

每个方法都必须带有注解来表明请求方式和请求的URL路径.

支持的请求方法：GET,POST,DELETE,PUT,HEAD,PATCH,OPTIONS(2.0新增)

#### 支持Url参数替换

``` java
@GET("/group/{id}/users")
List<User> groupList(@Path("id") int groupId);
```
在2.0时，支持Url动态配置
``` java
    @GET
    Call<ResponseData> getWallet2(@Url String url);
```
#### 支持查询参数
``` java
@GET("group/{id}/users")
Call<List<User>> groupList(@Path("id") int groupId, @Query("sort") String sort);
```
多个查询参数时
``` java
@GET("group/{id}/users")
Call<List<User>> groupList(@Path("id") int groupId, @QueryMap Map<String, String> options);
```
#### 请求体
对象会被自动转化成Json格式的字符串
``` java
@POST("users/new")
Call<User> createUser(@Body User user);
```
#### FormUrlEncoded
``` java
@FormUrlEncoded
@POST("user/edit")
Call<User> updateUser(@Field("first_name") String first, @Field("last_name") String last);
```
#### Multipart
1.x
``` java
@Multipart
@POST("/upload")
void upload(@Part("myfile") TypedFile file,
            @Part("description") String description,
            Callback<String> cb);
TypedFile typedFile = new TypedFile("multipart/form-data", new File("path/to/your/file"));

```

2.0

``` java
@Multipart
@PUT("user/photo")
Call<User> updateUser(@Part("photo") RequestBody photo);
RequestBody requestFile =
           RequestBody.create(MediaType.parse("multipart/form-data"), new File("path"));
```

#### Header

```java
//静态
@Headers("Cache-Control: max-age=640000")
@GET("widget/list")
Call<List<Widget>> widgetList();

//动态
@GET("user")
Call<User> getUser(@Header("Authorization") String authorization)


```
 
1.x

```java
public interface WmService1 {

    @GET("api/wallet/getcredit")
    ResponseData getWalletSyn();//同步

    @GET("api/wallet/getcredit")
    void getWalletAsyn(Callback<ResponseData> callback);//异步

}
```
``` java
	   ResponseData data = service.getWalletSyn();//同步

    service.getWalletAsyn(new Callback<ResponseData>() {

        @Override
        public void success(ResponseData responseData, Response response) {
        }

        @Override
        public void failure(RetrofitError error) {

        }
    });
```
2.0时

``` java
public interface WMService2 {

    @GET("api/wallet/getcredit")
    Call<ResponseData> getWallet();
}
```
```java
    Call<ResponseData> call = WmServiceGenerator.createService(WMService.class).getWallet();
    ResponseData data = call.execute().body;//同步
    //异步
    call.enqueue(new Callback<ResponseData>() {
        @Override
        public void onResponse(Call<ResponseData> call, Response<ResponseData> response) {
            ResponseData data = response.body();
        }

        @Override
        public void onFailure(Call<ResponseData> call, Throwable t) {

        }
    });
```
注意：每个call只能执行一次，但可以调用clone()来创建一个一模一样的实例，开销是非常小的，同时，正在执行的请求是可以被取消的，直接调用call.cancel();

### Interceptor
在1.x：

``` java
RestAdapter.Builder builder = new RestAdapter.Builder()

builder.setRequestInterceptor(new RequestInterceptor() {
        @Override
        public void intercept(RequestFacade request) {
            request.addHeader("Accept", "application/json");
        }
    });
RestAdapter adapter = builder.build();

```
如果集成了OkHttp则也可以在OkHttp中设置拦截器。Retrofit在2.0中默认集成OkHttp，Retrofit框架本身并不做网络请求而是交给OkHttp来处理，这样极大的简化了Retrofit。因此在2.0时，无法通过Retrofit来设置拦截器，只能通过OkHttp来实现，不管是在1.x还是2.0，这种方式都是比较推荐的。
``` java
OkHttpClient.Builder okBuilder = new OkHttpClient().newBuilder();
okBuilder.addNetworkInterceptor(new Interceptor() {
@Override
public Response intercept(Chain chain) throws IOException {
        Request original = chain.request();
        Request.Builder requestBuilder = original.newBuilder().addHeader("Accept", "application/json");
        Request request = requestBuilder.build();
        return chain.proceed(request);
        }
        });
okHttpClient = okBuilder.build();
```


### log
1.x
``` java
RestAdapter.Builder builder = new RestAdapter.Builder()  
    .setEndpoint(API_LOCATION)
    .setLogLevel(RestAdapter.LogLevel.FULL) // this is the important line
    .setClient(new OkClient(new OkHttpClient()));

```
logLevel有五种：NONE、BASIC、HEADERS、HEADERS_AND_ARG、FULL

2.x
```java
HttpLoggingInterceptor logging = new HttpLoggingInterceptor();  
// set your desired log level
logging.setLevel(Level.BODY);

OkHttpClient.Builder httpClient = new OkHttpClient.Builder();  
```
logLevel四种：NONE, BASIC, HEADERS, BODY
同时需添加依赖：
```
    compile 'com.squareup.okhttp3:logging-interceptor:3.2.0'

```

如果有很多默认的参数需要每次添加时：
```java
@FormUrlEncoded
@POST("/feedback")
Call<ResponseBody> sendFeedbackSimple(  
    @Field("osName") String osName,
    @Field("osVersion") int osVersion,
    @Field("device") String device,
    @Field("message") String message,
    @Field("userIsATalker") Boolean userIsATalker);
```

``` java
private void sendFeedbackFormSimple(@NonNull String message) {  
    // create the service to make the call, see first Retrofit blog post
    FeedbackService taskService = ServiceGenerator.create(FeedbackService.class);

    // create flag if message is especially long
    boolean userIsATalker = (message.length() > 200);

    Call<ResponseBody> call = taskService.sendFeedbackSimple(
            "Android",
            android.os.Build.VERSION.SDK_INT,
            Build.MODEL,
            message,
            userIsATalker
    );

    call.enqueue(new Callback<ResponseBody>() {
        ...
    });
}

```

```java
@POST("/feedback")
Call<ResponseBody> sendFeedbackConstant(@Body UserFeedback feedbackObject);  


public class UserFeedback {

    private String osName = "Android";
    private int osVersion = android.os.Build.VERSION.SDK_INT;
    private String device = Build.MODEL;
    private String message;
    private boolean userIsATalker;

    public UserFeedback(String message) {
        this.message = message;
        this.userIsATalker = (message.length() > 200);
    }

    // getters & setters
    // ...
}
```

``` java
private void sendFeedbackFormAdvanced(@NonNull String message) {  
    FeedbackService taskService = ServiceGenerator.create(FeedbackService.class);

    Call<ResponseBody> call = taskService.sendFeedbackConstant(new UserFeedback(message));

    call.enqueue(new Callback<ResponseBody>() {
        ...
    });
}
```

### URL处理
在1.x中不存在对Url的检查，在2.0之后通过`HttpUrl`类来解析。主要有一下几个方面：
1. 如果你的baseUrl不是已"/"结尾时
``` java
baseUrl:	http://mpay.meituan.com/api
endpoint:	/wallet/getcredit
result:		http://mpay.meituan.com/wallet/getcredit
```
这样是不符合预期的，所以Retrofit主动做了检查，必须以"/"结尾，否则会crash  `baseUrl must end in /`。

2.如果你的endpoint已“/”开始，代表了一个绝对路径，它将替换掉baseUrl的“/”后面的。
```
baseUrl: 	http://mpay.meituan.com/api6/
endpoint:	/api7/wallet/getcredit
result:		http://mpay.meituan.com/api7/wallet/getcredit
```
3.完全不同的Url时，会被完全替换
```
baseUrl:	http://mpay.meituan.com/
endpoint:	http://waimai.api.com/ or //waimai.api.com
result:		http://waimai.api.com/
```

### 自定义解析

1.x和2.0的其他区别：
在2.0中支持多种Converter并存，在1.x中一个RestAdapter只能绑定一个Converter对象，如果一个Api返回的结果需要Json反序列化，一个需要proto反序列化，则需要写两个接口，分别绑定RestAdapter。
 在2.0中则可以添加多个converter，但是需要注意，判断某个converter是否可以处理该回复是通过最终对象的类型来判断的。例如：当前有一个回复，它需要序列化成protobuf，这个时候需要找到适合的converter，因为Protobuf都是继承Message的，所以只需要判断是否是Message就可以了，而Json对象并不继承什么，所以无法找到适合的converter，因此JSON converter需要放在最后。






