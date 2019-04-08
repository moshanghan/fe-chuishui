本文转载自[session和cookie的区别和联系](https://www.cnblogs.com/muziyun1992/p/6662297.html)  
原文作者：做扎实的PHP开发者

### 区别

#### 1.存放位置：

　　session保存在服务器，cookie保存在客户端

#### 2.存放的形式：

　　session是以对象的形式保存在服务器，cookie以字符串的形式保存在客户端

#### 3.用途：

　　session适合做客户的身份验证，cookie适合保存用户的个人设置，爱好等

#### 4.路径：

　　session不能区分路径，同一用户在访问一个网站期间，所有的session在任何一个地方都可以访问到；cookie中如果设置了参数路径，那么同一个网站下的cookie互相访问不到

#### 5.安全性：

　　cookie不是很安全，别人可以分析存放在本地的cookie并进行cookie欺骗，session较cookie更安全一些

#### 6.大小及数量限制：

1）浏览器允许每个域名所包含的cookie数：  
　　Microsoft指出InternetExplorer8增加cookie限制为每个域名50个，但IE7似乎也允许每个域名50个cookie。  
　　Firefox每个域名cookie限制为50个。  
　　Opera每个域名cookie限制为30个。  
　　Safari/WebKit貌似没有cookie限制。但是如果cookie很多，则会使header大小超过服务器的处理的限制，会导致错误发生。  

  >注：“每个域名cookie限制为20个”将不再正确！

2）当很多的cookie被设置，浏览器如何去响应。  
　　除Safari（可以设置全部cookie，不管数量多少），有两个方法： 

　　最少最近使用（leastrecentlyused(LRU)）的方法：当Cookie已达到限额，自动踢除最老的Cookie，以使给最新的Cookie一些空间。Internet Explorer和Opera使用此方法。  
　　Firefox很独特：虽然最后的设置的Cookie始终保留，但似乎随机决定哪些cookie被保留。似乎没有任何计划（建议：在Firefox中不要超过Cookie限制）。

3）不同浏览器间cookie总大小也不同：

　　Firefox和Safari允许cookie多达4097个字节，包括名（name）、值（value）和等号。  
　　Opera允许cookie多达4096个字节，包括：名（name）、值（value）和等号。  
　　Internet Explorer允许cookie多达4095个字节，包括：名（name）、值（value）和等号。  

  >注：多字节字符计算为两个字节。在所有浏览器中，任何cookie大小超过限制都被忽略，且永远不会被设置。

### 联系：

　　session需要借助cookie才能正常工作，如果客户端完全禁止cookie,session将失效，因为session是由应用服务器维持的一个服务端的存储空间，用户在连接服务器时，会由服务器生成唯一的sesssionid，用该sessionid为标识来存取服务端的session空间。而sessionid存储在cookie中，用户提交页面时会将这个sessionid提交到服务端，来存取session数据.这一过程是不用开发人员干预的，所以一旦客户端禁用cookie,那么session也会失效；

另外：

session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能，如果主要考虑到减轻服务器性能方面，应当使用COOKIE

sessionid是服务器和客户端链接时候随机分配的

　　

