# FlavorsDemo

个人观点：学习重在积累，每天一点积累，你成为大神就更进了一步，好了我就不装逼了。

相关博客:
[http://www.cnblogs.com/0616--ataozhijia/p/4203997.html](http://www.cnblogs.com/0616--ataozhijia/p/4203997.html)
[http://blog.csdn.net/maosidiaoxian/article/details/42000913](http://blog.csdn.net/maosidiaoxian/article/details/42000913)

大家一起来看看这样一个需求：项目需要打包成有**支付功能**的和**没有支付功能**的。肯定就会想到多渠道打包，想到多渠道打包自然就会用到`Umeng`的多渠道打包，那么在渠道下面又怎么实现功能控制呢？于是我就想啊....xxx于是乎，我有了这样一个思路。

首先你必须在`AndroidManifest.xml`中的`meta-data`修改以下的样子：‘

```
        <meta-data
            android:name="UMENG_CHANNEL"
            android:value="${UMENG_CHANNEL_VALUE}" />

    </application>
```

这里的`android:name`你可以随意命名，如`android:name="KOOL_POS"`。`android:value`的值就多个`${}`，如`android:value="${KOOL_POS_VALUE}"`。

然后在你项目的`gradle`中添加多渠道：

```
    productFlavors {
        baidu {}
        taobao {}
        qq {}
    }
```

推荐做法：

```
    productFlavors {
        baidu {
            applicationId "com.github.ws.baidu"
        }
        taobao {
            applicationId "com.github.ws.taobao"
        }
        qq {
            applicationId "com.github.ws.qq"
        }
    }
```
为啥要加`applicationId` 呢，是为了防止多渠道打包不会覆盖安装。

记得添加：
```
    productFlavors.all { flavor ->
        flavor.manifestPlaceholders = [KOOL_POS_VALUE: name]
    }
```
注意你需要打`baidu`的包，请把`name`改成`"baidu"`。如：`flavor.manifestPlaceholders = [KOOL_POS_VALUE: "baidu"]`，其他的同理。

最关键的一步，既然我们在`AndroidManifest.xml`中配置了`meta-data`，那么就可以在`Java`代码中获取`meta-data`信息，就可以知道是打谁的包呢。

如：
```
 try {
            ApplicationInfo info = this.getPackageManager().getApplicationInfo(getPackageName(), PackageManager.GET_META_DATA);
            String value = info.metaData.getString("UMENG_CHANNEL");
            switch (value) {
                case "baidu":
                     //处理 baidu  打包功能模块
                    break;
                case "taobao":
                    //处理 taobao 打包功能模块
                    break;
                case "qq":
                    //处理 qq打包功能模块
                    break;
                default:
            }
        } catch (PackageManager.NameNotFoundException e) {
            e.printStackTrace();
        }
```

好了，如果你有更好的方案，请给我留言。也欢迎大家加QQ群：478720016 共同讨论ANDROID相关的技术问题
