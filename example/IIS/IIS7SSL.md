## IIS7添加SSL证书

## 第一步
打开`服务器证书模块`
<!--more-->
<center>![图3](https://cdn.drixn.com/img/src/IIS00018.png)</center>

## 第二步
点击右侧`导入`按钮
<center>![图3](https://cdn.drixn.com/img/src/IIS00019.png)</center>

## 第三步
在弹出的界面选择你的SSL证书`.pfx`结尾
<center>![图3](https://cdn.drixn.com/img/src/IIS00021.png)</center>

## 第四步
输入证书密码，开始导入
<center>![图3](https://cdn.drixn.com/img/src/IIS00020.png)</center>

## 第五步
选择需要添加SSL证书的网站，然后点击右侧`绑定按钮`
<center>![图3](https://cdn.drixn.com/img/src/IIS00015.jpg)</center>

## 第六步
点击`添加`
<center>![图3](https://cdn.drixn.com/img/src/IIS00017.png)</center>

## 第七步
{% note danger %}
类型：https
端口：443
SSL证书：选择你刚才添加的证书即可
{% endnote %}
<center>![图3](https://cdn.drixn.com/img/src/IIS00016.png)</center>

## 第八步
点击`确定`，SSL证书添加完成