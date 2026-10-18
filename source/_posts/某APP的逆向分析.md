---
title: "某APP的逆向分析"
date: 2023-08-24 22:02:11
---
首先是进行抓包

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20231017200150.png)

抓包发现点击登录之后发出去了一个登录的数据包，复制其中的 `"Encrypt"`在反编译工具中进行查找。搜索到两个疑似的函数

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20231017200507.png)

由于不确定是哪个函数，所以采用hock的方法进行判断。由于手机和电脑都配置了hock的环境。

​

启动手机端的hock程序

```plaintext
.data/local/tmp/fsarm64
```

启动hock脚本

```plaintext
frida -UF -l hock.js
```

hock第一个函数，发现并没有显示结果。

```plaintext
Java.perform(()=>{

    var jsonRequest = Java.use("com.dodonew.online.http.JsonRequest") 
    console.log(jsonRequest)
    jsonRequest.paraMap.implementation = function(a){
        console.log("params1",a)
        this.paraMap(a)
    }
    
})
```

hock第二个函数

```plaintext
Java.perform(()=>{
var jsonRequest = Java.use("com.dodonew.online.http.JsonRequest") 
jsonRequest.addRequestMap.overload('java.util.Map',"int").implementation = function(a,b){
        var bb = Java.cast(a,Java.use("java.util.HashMap"))
        console.log("addRequestMap",a,b)
        console.log(bb.toString())
        this.addRequestMap(a,b)
    }
 })
```

发现是第二个函数

追踪其中的

```plaintext
String code = RequestUtil.paraMap(addMap, Config.BASE_APPEND, "sign");
```

paraMap函数

```plaintext
public static String paraMap(Map<String, String> addMap, String append, String sign) {
        try {
            Set<String> keyset = addMap.keySet();
            StringBuilder builder = new StringBuilder();
            List<String> list = new ArrayList<>();
            for (String keyName : keyset) {
                list.add(keyName + "=" + addMap.get(keyName));
            }
            Collections.sort(list);
            for (int i = 0; i < list.size(); i++) {
                builder.append(list.get(i));
                builder.append("&");
            }
            builder.append("key=" + append);
            String checkCode = Utils.md5(builder.toString()).toUpperCase();
            addMap.put("sign", checkCode);
            String result = new Gson().toJson(sortMapByKey(addMap));
            Log.w(AppConfig.DEBUG_TAG, result + "   result");
            return result;
        } catch (Exception e) {
            e.printStackTrace();
            return "";
        }
    }
```

```plaintext
这段代码是一个用于处理参数映射的方法。它接受一个包含键值对的addMap参数，以及两个附加参数append和sign。

方法首先将addMap中的键值对按照键的字母顺序进行排序，并将排序后的结果存储在list中。

然后，使用StringBuilder来构建一个字符串，将排序后的键值对按照key=value的格式连接起来，并以"&"作为分隔符。最后，将附加参数"key=" + append添加到字符串末尾。

接下来，使用Utils.md5()方法计算字符串的MD5哈希值，并转换为大写形式，存储在checkCode变量中。

然后，将计算得到的checkCode设置为addMap的一个键值对，键为"sign"。

最后，使用Gson库将排序后的addMap转换为JSON格式的字符串，并打印输出。

如果方法成功执行，它将返回一个JSON格式的字符串。如果出现任何异常，它将打印异常信息并返回空字符串。
```

重要的函数为MD5(),但是不确定是不是JAVA的标准库，

编写代码hock md5函数

```plaintext
var utils = Java.use("com.dodonew.online.util.Utils") 
utils.md5.implementation = function(a){
        console.log(a)
        var retval = this.md5(a)
        console.log(retval)
        return retval
    }
```

发现输出的结果

```plaintext
equtype=ANDROID&loginImei=Androidnull&timeStamp=1674128794241&userPwd=123456&username=123456&key=sdlkjsdljf0j2fsjk
```

其中equtype，loginImei，key为不变的，timeStamp是当前的时间戳。userPwd，username是用户自己输入的。最终将结果加密之后发现是MD5加密。

再hock上层函数
