https://segmentfault.com/a/1190000015668383<br>

## 定义：
Ajax用一句话来说就是无须刷新页面即可从服务器取得数据。注意，虽然Ajax翻译过来叫异步JavaScript与XML，但是获得的数据不一定是XML数据，现在服务器端返回的都是JSON格式的文件。

## Ajax的请求过程
创建XMLHttpRequest实例<br>
发出HTTP请求<br>
接收服务器传回的数据<br>
更新网页数据<br>

红宝书上给出的发起Ajax请求的例子

    var xhr = new XMLHttpRequest();  // 创建XMLHttpRequest实例

    xhr.onreadystatechange = function(){
    if (xhr.readyState == 4){   // 判断请求响应过程阶段，4 阶段代表已接收到数据
       if (xhr.status >=200 && xhr.status < 300 || xhr.status==304) { // 校验HTTP状态码
          console.log(xhr.responseText);   // 输出响应的文本
        } else {
              console.error(xhr.status, xhr.statusText); // 打印其他HTTP状态码
          }
        }
    };

    xhr.open('get', 'example.txt', true); // 初始化xhr实例，或者说启动请求
    xhr.send(null);  // 设置HTTP请求携带参数，null为不带参数
## AjAX请求过程解析

#### 1. 创建XMLHttpRequest实例 <br>

  
        if(window.XMLHttpRequest) {

        //在IE6以上的版本以及其他内核的浏览器(Mozilla)等
            var xhr = new XMLHttpRequest();
        }else if(window.ActiveXObject) {
               //在IE6以下的版本
            var xhr = new ActiveXObject();
         }
          
#### 2. 发出HTTP请求<br>
  


    XMLHttpRequest.open(method, url, isAsync)
      // 例如
    xhr.open('get', 'http://www.baidu.com', true)
 
其中参数为http请求使用方法，如（'get'，'post'等），第二是参数是请求的url,  第三个参数代表是否异步发送请求（可选）。调用open()方法后会启动一个http请求，但它不会立即发送请求，处于待命状态。需要注意的是：请求的url必须要跟请求源域(origin)同域，也就是说协议、域名、端口号要一致，跨域请求要使用别的方法。
## 如果跨域：
POST请求跨域问题：
在ajax中加入以下代码：

     // 允许跨域
    crossDomain: true,
     // 下面这句话允许跨域的cookie访问
    xhrFields: {
        withCredentials: true // 带本地的一些cookie信息
    },


完整的ajax代码：

      $.ajax({
             url:apiUrl+'mark',
             dataType:'json',
             // 允许跨域
             crossDomain: true,
             // 下面这句话允许跨域的cookie访问
             xhrFields: {
                withCredentials: true // 带本地的一些cookie信息
              },
                type:'post',
                data:data,
                success:function (res) {
                    console.log(res);
                     if (res.code == 1) {
                        alert('留言提交成功');
                          $(".writeMsg").fadeOut();
                          $(".ok").fadeIn();
                        } else {
                            alert(res.msg);
                            $(".writeMsg").fadeOut();
                            $(".fail").fadeIn();
                        }
                },
                error:function (jqXHR, status, err) {
                      $(".writeMsg").fadeOut();
                      $(".fail").fadeIn();
                    return;
                    }
     })
     
    
    
    
     
 接着调用send()方法就会发出这个http请求。
 
     xhr.open('get', 'http://www.baidu.com', true)
    xhr.send(null) 
    
send()方法接受一个参数，为http请求发送的数据（通常用于'post'方法），如果为null，表示不发送数据。至此，一个异步的http请求就发送到了服务器。

#### 3. 接收服务器传回的数据
###### 3.1 发送同步请求
 如果将open方法的第三个参数设为false，即为同步请求，当收到服务器的响应后，相应的数据会自动填充到XHR对象的属性中，主要包括以下四个：

1. responseText：作为响应主体被返回的文本。
2. responseXML: 响应返回的XML文档，能接收到的前提是，响应的Content-Type字段的值为 text/xml或者application/xml。
3. status: HTTP状态码。
4. statusText: HTTP状态码说明。

当客户端收到以上信息后，首先要判断HTTP状态码来确认响应是否成功，状态码在200-300之间表示请求成功，同时304代表请求资源未被修改，可使用浏览器本地缓存。如果成功就可以获取响应报文主体中的数据了。
   
    
    
    xhr.open('get', 'http://www.baidu.com', false)
    xhr.send(null)
     
    if (xhr.status >=200 && xhr.status < 300 || xhr.status == 304) {  // 校验HTTP状态码304也是重定位，直接使用缓存
    
       console.log(xhr.responseText);   // 输出响应的文本
     } else {
       console.error(xhr.status, xhr.statusText); // 打印其他HTTP状态码
    }

##### 发送异步请求
如果将open方法的第三个参数设为true，即为异步请求。那么就需要一个事件来通知程序异步请求的结果是否返回。XHR对象中的readyState属性，表示请求/响应整个过程所处的阶段，它有五个值分为对应五个阶段：

0：未初始化。未调用open()方法。<br>
1：启动。已经调用open()方法，但未调用send()方法。<br>
2：发送。已调用send()方法，但未收到响应。<br>
3：接收。已经接收到部分响应数据。<br>
4：完成。已经接受到全部响应数据。<br>

readyState的值每变化一次，都会触发一次readStatechange事件，我们定义一个事件处理函数onreadStatechange()，并监听readyState == 4状态，就可以得知响应数据已全部收到，并进行下一步操作。那么就是文章开头给出的代码：<br>
   

    var xhr = new XMLHttpRequest();  // 创建XMLHttpRequest实例

    xhr.onreadystatechange = function(){
       if (xhr.readyState == 4){   // 判断请求响应过程阶段，4 阶段代表已接收到数据
           if (xhr.status >=200 && xhr.status < 300 || xhr.status == 304) {  // 校验HTTP状态码
               console.log(xhr.responseText);   // 输出响应的文本
             } else {
                  console.error(xhr.status, xhr.statusText); // 打印其他HTTP状态码
             }
          }
     };

    xhr.open('get', 'example.txt', true); // 初始化xhr实例，或者说启动请求
    xhr.send(null);  // 设置HTTP请求携带参数，null为不带参数
 
##  补充XHR中三个有用的事件
### timeout事件
当超出了设置时间还未收到响应，就会触发timeout事件，进而调用ontimeout事件处理程序。同时timeout也是XHR的一个属性，用于设置这个时间阈值。下面是用法：<br>

     xhr.ontimeout = function() {
     alert('timeout!')
    }

    xhr.open('get', 'http://www.baidu.com', true)
    xhr.timeout = 1000 // 时间阈值设为1秒
    xhr.send(null)
### load事件
load事件用于简化对readState值的判断，响应数据全部接收完毕后（也就是readState == 4）会触发load事件，使用onload事件处理函数进行后续操作，onload会接收一个event对象，它的target属性等于XHR对象，当然我们在定义这个事件处理函数时也可以不传入这个参数，来看下面的用法：<br>

    var xhr = new XMLHttpRequest()
    xhr.onload = function () {
        if(xhr.status >=200 && xhr.status < 300 || xhr.status == 304) {
     console.log(xhr.responseText);   // 输出响应的文本
        } else {
           console.error(xhr.status, xhr.statusText); // 打印其他HTTP状态码
        }
    }
    xhr.open('get', 'http://www.baidu.com', true)
    xhr.send(null)
    
这样就不用去关心readyState值的变化情况了。当然如果想在特定readyState值上做一些逻辑处理，还是要用之前的方法。<br>
## 面试考点
AJAX应用和传统Web应用有什么不同？<br>
传统的web前端与后端的交互中，浏览器直接访问Tomcat的Servlet来获取数据。Servlet通过转发把数据发送给浏览器。<br>
当我们使用AJAX之后，浏览器是先把请求发送到XMLHttpRequest异步对象之中，异步对象对请求进行封装，然后再与发送给服务器。服务器并不是以转发的方式响应，而是以**流的方式**把数据返回给浏览器====<br>
XMLHttpRequest异步对象**会不停监听服务器状态的变化**，得到服务器返回的数据，就写到浏览器上【因为不是转发的方式，所以是无刷新就能够获取服务器端的数据】（获取验证码，1分钟内没有填写正确，将可以重新发送短信）