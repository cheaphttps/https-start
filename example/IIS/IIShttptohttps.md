## IIS7 http重定向到https

<div class="admonition note"><p class="admonition-title">准备工作</p><p>SSL证书购买
[点击购买](https://item.taobao.com/item.htm?spm=2013.1.w4023-13283584247.10.4b5569011wMPg1&id=545850275264)</p><p>下载适用于 IIS 7, IIS 7.5, IIS 8, IIS 8.5, IIS 10 的 Microsoft Download URL Rewrite Module(URL 重写模块 2.1) [官网链接](https://www.iis.net/downloads/microsoft/url-rewrite)</p></div>

## 下载URL 重写模块 2.1
### English 下载
<div class="Download"><ul class="repo"><li class="repo-download"><a href=“http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&amp;appid=urlrewrite2” target="_blank" title="Download" data-action="download"><i class="icon icon-download"></i> 点击下载WebPI </a></li><li class="repo-download"><a href="http://download.microsoft.com/download/6/8/F/68F82751-0644-49CD-934C-B52DF91765D1/rewrite_x86_en-US.msi" target="_blank" title="Download" data-action="download"><i class="icon icon-download"></i> 点击下载x86 </a></li><li class="repo-download"><a href="http://download.microsoft.com/download/D/D/E/DDE57C26-C62C-4C59-A1BB-31D58B36ADA2/rewrite_amd64_en-US.msi" target="_blank" title="Download" data-action="download"><i class="icon icon-download"></i> 点击下载x64 </a></li></ul></div>

### 简体中文下载 
<div class="Download"><ul class="repo"><li class="repo-download"><a href=“http://download.microsoft.com/download/C/1/4/C144048E-BB10-4D0A-8EB2-477319F78DC2/rewrite_x86_zh-CN.msi” target="_blank" title="Download" data-action="download"><i class="icon icon-download"></i> 点击下载x86 </a></li><li class="repo-download"><a href="http://download.microsoft.com/download/E/A/9/EA9F19BC-0EEB-49C9-B32D-56852BBE56DA/rewrite_amd64_zh-CN.msi" target="_blank" title="Download" data-action="download"><i class="icon icon-download"></i> 点击下载x64 </a></li></ul></div>

## ASP.NET站点
可直接修改web.config（与下文`IIS配置步骤`效果相同），例如：见`<rewrite>...</rewrite>`节点

```
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <rewrite>
            <rules>
                <rule name="HTTP to HTTPS redirect" stopProcessing="true">
                    <match url="(.*)" />
                    <conditions>
                        <add input="{HTTPS}" pattern="^OFF$" />
                    </conditions>
                    <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" redirectType="SeeOther" />
                </rule>
            </rules>
        </rewrite>
    </system.webServer>
</configuration>
```

## IIS配置步骤
打开所需要配置的站点
![图3](https://cdn.drixn.com/img/src/IIS0000.png)

### 第一步
打开`SSL 设置`
取消勾选`要求 SSL(Q)`

### 第二步
安装URL重写模块2.0，安装完毕之后重启IIS服务，之后打开IIS控制台，发现多了下图`URL 重写`组件

![图3](https://cdn.drixn.com/img/src/IIS0001.png)

### 第三步
双击`URL 重写`，点击右方菜单栏`添加规则`
![图3](https://cdn.drixn.com/img/src/IIS0002.png)

### 第四步
在弹出的界面点击`空白规则`
![图3](https://cdn.drixn.com/img/src/IIS0003.png)

### 第五步
<div class="admonition warning"><p class="admonition-title">编辑入站规则</p><p>名称：`HTTP to HTTPS redirect`</p><p>模式：`(.*)`</p></div>
![图3](https://cdn.drixn.com/img/src/IIS0004.png)

### 第六步
点击条件菜单后边的倒三角
![图3](https://cdn.drixn.com/img/src/IIS0005.png)

### 第七步
在弹出的界面点击`添加`
![图3](https://cdn.drixn.com/img/src/IIS0006.png)

### 第八步
<div class="admonition warning"><p class="admonition-title">添加条件界</p><p>条件输入：`{HTTP}`</p><p>模式：`^OFF$` 或 `off`</p></div>
![图3](https://cdn.drixn.com/img/src/IIS0004.png)
![图3](https://cdn.drixn.com/img/src/IIS0007.png)
或
![图3](https://cdn.drixn.com/img/src/IIS0011.png)

### 第九步
<div class="admonition warning"><p class="admonition-title">编辑`操作类型`和`重定向URL`以及`重定向类型`</p><p>操作类型：`重定向`</p><p>重定向URL：`https://{HTTP_HOST}/{R:1}`</p><p>重定向类型：`参阅其它(303)` 或 `已找到(302)`</p></div>
![图3](https://cdn.drixn.com/img/src/IIS0008.png)
或
![图3](https://cdn.drixn.com/img/src/IIS0010.png)


### 第十步
点击右侧菜单栏`应用`
![图3](https://cdn.drixn.com/img/src/IIS0009.png)

### 第十一步
查看规则
![图3](https://cdn.drixn.com/img/src/IIS0012.png)

### 操作完成