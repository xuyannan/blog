---
layout: default
title: 实战：使用 MixpayAndroidSDK快速集成移动支付功能
---

# 一、聚易付移动支付服务(MixpayAndroidSDK)简介

聚易付移动支付服务(MixpayAndroidSDK)的目标就是使开发者很轻松地在自己的APP中集成支付功能，从而节约时间和精力用在本身的业务当中。

只需15分钟，就可以让你的APP具有支付宝支付、银行卡支付、手机充值卡支付和游戏点卡支付。解决了之前开发者在集成支付时遇到的各种问题，比如：

* 与支付平台签约时间过长
* 支付平台文档坑太多，导到开发过程很不顺畅
* 支付平台技术支持不足，联调困难
* 每一个支付通道都要折腾一翻

MixpayAndroidSDK致力于帮助开发者解决以上问题。

以下通过一个实际项目来说明如何使用MixpayAndroidSDK。

笔者的开发环境：

* Macbook Pro 10.9.4
* Android Studio v0.8.2
* Gradle 1.10
* JDK 1.7

# 二、注册、添加APP

## 2.1 注册

注册地址：https://m.mixpay.cn/register

注册成功之后会收到Mixpay发出的一封确认邮件，点击其中的确认链接即完成注册过程。

## 2.2 添加APP

登录到商户后台，通过菜单导航到 应用->新建应用 页面 

https://m.mixpay.cn/application/add

在表单中填写应用的相关信息，保存即可。如下图所示：

![添加APP][1]

其中的"通知地址"是指，Mixpay在完成一笔支付之后，通过这个地址通知给您，您在接收到进行相应的处理，并返回一个字符串'success'以表示接收成功，整个支付过程也就完成了。

APP保存之后，Mixpay会位APP分配一个唯一标识，即APP_KEY，可以在 APP详情页面中看到：

![APP详情][2]

## 2.3 添加密钥

操作地址：https://m.mixpay.cn/key/add
密钥用来对订单信息进行加密。Mixpay支付两种加密方式，RSA和MD5。
使用RSA，按页面上给出的提示操作即可。
使用MD5，直接保存即可。在密钥详细信息中会看到secretKey。

在这里选择MD5加密方式：

![添加密钥][3]

key信息：

![密钥详情][4]

## 2.4 将密钥绑定至APP

操作地址：https://m.mixpay.cn/key
在key的右边点击"加载到应用"，在打开的页面中选择刚才新建的APP即可。

# 三、APP代码集成

## 3.1 添加MixpayAndroidSDK的依赖

可以通过gradle、maven、jar包三种方式添加依赖，开发者可以根据自身情况任选。这里选择`Gradle`，在build.gradle中添加以下配置：

```
apply plugin: 'maven'
repositories {
    maven {
        url 'https://maven.mixpay.cn'
    }
}
...
dependencies {
    compile ('cn.mixpay:android-sdk:1.0.+'){
        exclude group: 'com.google.android', module: 'android'
    }
}
```

## 3.2 初始化订单

引入MixpayAdroidSDK后，即可通过`cn.mixpay.Order`来表示自己的订单：

```
import cn.mixpay.sdk.*;
...

public static final String MD5_SECRET_KEY = "568bd43af00128ee4d1bce2679*****";
private static final String APP_KEY = "60186724345*****";
Order myOrder = null;

//订单信息
String orderId = "order_id_001";
String userId = "user_id_001";
int amount = 1;
String orderTitle = "瑜伽垫十个";
String orderDesc = "国际健美协会10:00下单";
String productId = "product_id_001";
//初始化订单界面，此处略去代码
//签名
String sign = cn.mixpay.server.MD5SignatureTool.signOrderInfo(amount, APP_KEY, "", orderId, "", orderDesc, orderTitle, "", productId, "", userId
        , MD5_SECRET_KEY);

myOrder = new Order(orderId, userId, amount, orderTitle, orderDesc, productId, sign);
```

其中的参数依次表示：

订单编码、用户编码、金额(单位是分)、订单标题、订单描述、商品编码、订单签名

## 3.3 支付

初始化一个`cn.mixpay.MixpayAPI`对象，这个对象里提供了具体的支付方法：

```
public void pay(View view) {
    MixpayAPI api = new MixpayAPI(this, APP_KEY);
    api.startPay(this, myOrder);
}
```

其中`this`表示当前的Activity，`APP_KEY`是在步骤2.2中得到的应用唯一标识，`myOrder`就是刚刚生成的订单。

将`pay`方法绑定给页面上的支付按钮，在点击按钮时，MixpayAndroidSDK会做以下工作：

* 检测当前环境中是否安装了Mixpay支付插件
* 没有的话会提示用户安装
* 若有更新的Mixpay支付插件，提示用户升级
* 打开支付界面引导用户选择支付方式并完成支付

订单界面：

![订单][5]

支付界面:

![支付][6]


## 3.4 支付结果处理

通过onActivityResult获得支付结果。MixpayAPI中定义了关于支付结果的各种状态：

```
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == MixpayAPI.REQUEST_CODE_START_PAY) {
            Long mixpayOrderId = null;
            try {
                mixpayOrderId = data.getLongExtra(MixpayAPI.GET_MIXPAY_ORDER_ID, 0);
            }catch (Exception e) {
                //mixpayOrderId = new Long(0);
                Log.e(TAG, "no order is returned");
            }
            switch (resultCode) {
                case MixpayAPI.ORDER_STATUS_PAY_SUCCESS:
                    //TODO:支付成功，继续程序逻辑
                    Toast.makeText(this, "支付成功"), Toast.LENGTH_LONG).show();
                    break;
                case MixpayAPI.ORDER_STATUS_UNPAY:
                    ...
                    break;
                case MixpayAPI.ORDER_STATUS_PAYING:
                    ...
                    break;
                case MixpayAPI.ORDER_STATUS_CANCELED:
                    ...
                    break;
                case MixpayAPI.ORDER_STATUS_PAY_FAILURE:
					...
                    break;
                case MixpayAPI.ORDER_STATUS_UNKNOWN:
					...
                    break;
            }
            
        }

    }
```

至此，支付功能集成完毕。

# 四、更多内容

## 4.1 关于订单签名

Mixpay提供的帮助文档中详细说明了如何对订单信息进行签名，同时也提供了`java`、`PHP`、`Ruby`、`Python`、`NodeJS`五种语言的示例，可以直接拿来用。

其中对于java还提供了服务器端的sdk，提供了方便的订单签名方法。具体请参考这里：https://www.mixpay.cn/docs/v1/server/java/

在这个示例中，我直接在APP项目里引入这个服务器端sdk。

## 4.2 相关链接

[Mixpay Android SDK 使用帮助](https://www.mixpay.cn/docs/v1/android/)

[Mixpay Server SDK For JAVA](https://www.mixpay.cn/docs/v1/server/java/)

[Mixpay on Github](https://github.com/mixpay)


  [1]: http://segmentfault.com/img/bVcJGL
  [2]: http://segmentfault.com/img/bVcJGO
  [3]: http://segmentfault.com/img/bVcJGP
  [4]: http://segmentfault.com/img/bVcJGQ
  [5]: http://segmentfault.com/img/bVcJGS
  [6]: http://segmentfault.com/img/bVcJGT
