---
title: 2017-06-30-safe-develop
layout: post
date: 2017-06-30 10:13:37 +0800
categories: [safe]
tags: [safe]
---


* content
{:toc}                                                                                                          











###


客户端安全规范
跨站脚本攻击漏洞
跨站脚本漏洞说明
跨站脚本漏洞是系统在处理用户可控数据时，没有对用户可控数据做相关验证或转义，导致用户可以对可控数据中插入恶意代码，并执行，导致对客户端造成攻击。
跨站脚本漏洞可以在多种场景下触发，我们根据触发场景对漏洞进行分类：
当可控数据输出到HTML页面中，所产生的跨站脚本漏洞为HTML跨站；
当可控数据输出到页面JavaScript中，所产生的跨站脚本漏洞为JavaScript跨站；
当可控数据输出到页面CSS样式中，所产生的跨站脚本漏洞为CSS跨站；
当可控数据输出到页面中的URL连接时，所产生的跨站脚本漏洞为URL跨站；
当可控数据为博客或其他富文本页面时，所产生的跨站脚本漏洞为富文本跨站；
当可控数据调用文本对象模型重新写入页面中时，所产生的跨站脚本漏洞为DOM跨站；
我们按照该分类对跨站脚本漏洞进行了典型案例分析及提供了相应的防御规范；
跨站脚本漏洞风险
如果跨站脚本漏洞被恶意利用的话，将会产生如下的安全风险：
盗取用户cookie，然后伪造用户身份登录，泄漏用户个人身份及用户订单信息。
操控用户浏览器，借助其他漏洞可能导致对https加密信息的破解，导致登录传输存在安全风险。
结合浏览器及其插件漏洞，下载病毒木马到浏览者的计算机上执行。
修改页面内容，产生钓鱼攻击效果，例如伪造登录框获取用户明文帐号密码。
跨站脚本漏洞参考案例
以下是跨站脚本漏洞在不同场景的典型案例，案例具有代表意义，案例详情请根据链接参考chmcc安全知识库；
chmccEPT多语言站存在JSON跨站漏洞
该事件是由于chmccEPT多语言站pass.w.chmcc.com在JSON数据为用户可控数据的情况下，用户提交UTF-8及UTF-7编码脚本，可以绕过安全检测及一些初级转码，导致JSON数据跨站脚本攻击。
详见安全知识库：{_}http://study.chmcc.com/?p=28242#ID0x0101_
chmcc云汇HTML场景跨站脚本漏洞
该事件是由于chmcc云汇对用户可控的数据无限制，可导致恶意用户会提交恶意html代码或恶意插入Js脚本，导致展示页面被黑客利用，造成对终端用户的安全风险。
详见安全知识库：{_}http://study.chmcc.com/?p=28255#ID0x0101_
chmcc运营商Javascript场景跨站脚本漏洞
该事件是由于chmcc运营商站在处理用户和服务器交互时没有进行处理，恶意用户会提交恶意Javascript代码插入页面的JavaScript中，导致展示页面被黑客利用，造成对终端用户的安全风险。
详见安全知识库：{_}http://study.chmcc.com/?p=28266#ID0x0101_
chmcc搭配购CSS场景跨站脚本漏洞
该事件是搭配购页面样式CSS中展示用户可控数据时没有进行有效过滤，攻击者会在所提交的CSS中插入脚本，导致浏览此页面的用户浏览器会执行插入的JavaScript脚本。
详见安全知识库：{_}http://study.chmcc.com/?p=28415#ID0x0101_
富文本场景跨站脚本漏洞案例参考
该事件是由于对富文本对象没有正确的编码输出导致。
详见安全知识库：{_}http://study.chmcc.com/?p=28456_ 
DOM场景跨站脚本漏洞案例参考
该事件是由于文档对象模型对用户可控数据没有正确的编码输出导致。
详见安全知识库：{_}{+}http://study.chmcc.com/?p=28469+_
跨站脚本漏洞防御规范
目前针对各类跨站脚本漏洞我们提供了详细防御规范，首先针对主要的跨站脚本漏洞类型开发了chmcc跨站脚本统一防御包来解决；
防御包现在对跨站脚本主要用到了转义和过滤的防范方式：
跨站脚本漏洞类型
防御包提供的防御方法
HTML跨站脚本漏洞
转义
HTML标签跨站脚本漏洞
转义
JS跨站脚本漏洞
转义
URL跨站脚本漏洞
转义
CSS跨站脚本漏洞
转义
JSON跨站脚本漏洞
过滤
富文本跨站脚本漏洞
过滤
目前不能直接通过防御包解决DOM型跨站，针对DOM型跨站的防御方法，请参考针对该漏洞的具体防御方法；
跨站脚本防御包说明
安全管理部联合成都研究院，针对跨站脚本漏洞以Esapi为基础，开发一套专门防御跨站脚本漏洞的补丁包，跨站脚本漏洞防御包提供输出转义方式进行跨站脚本防御。
相关包及配置文件请见附件：
chmcccd-security-filter-mini-0.1.8.jar（跨站脚本防御包）
chmccSecurityMini.xml（跨站脚本分防御包配置文件）
防御包的具体使用方法如下：
HTML跨站脚本漏洞解决规范
当用户可控数据输出到HTML页面中时，必须使用此方法对用户可控数据做相关转义，下面为该方法调用代码实例：
public void xsstest() throws IOException, URISyntaxException {
SecurityEncodersecurityEncoder = new SecurityEncoder();
String XSSstring = servletRequest.getParameter("str");//str为用户可控数据
String cleanValue ="";//转义后变量
cleanValue = securityEncoder.encodeForHTML(XSSstring);//HTML转义方法
pw.write(cleanValue);//输出转义后的数据
} 
HTML标签内跨站脚本漏洞解决规范
当用户可控数据输出到HTML标签内时，必须使用此方法对用户可控数据做相关转义，下面为该方法调用代码实例：
public void xsstest() throws IOException, URISyntaxException {
SecurityEncodersecurityEncoder = new SecurityEncoder();
String XSSstring = servletRequest.getParameter("str");//str为用户可控数据
String cleanValue ="";//转义后变量
cleanValue = securityEncoder.encodeForHTMLAttribute(XSSstring);//HTML标签内数据转义方法
pw.write(cleanValue);//输出转义后的数据
} 
JavaScript跨站脚本漏洞解决规范
当用户可控数据输出到Javascript中时，必须使用此方法对用户可控数据做相关转义，下面为该方法调用代码实例：
public void xsstest() throws IOException, URISyntaxException {
SecurityEncodersecurityEncoder = new SecurityEncoder();
String XSSstring = servletRequest.getParameter("str");//str为用户可控数据
String cleanValue ="";//转义后变量
cleanValue = securityEncoder.encodeForJavaScript(XSSstring);//javascri转义方法
pw.write(cleanValue);//输出转义后的数据
}
CSS跨站脚本漏洞解决规范
当用户可控数据输出到CSS样式中时，必须使用此方法对用户可控数据做相关转义，下面为该方法调用代码实例：
public void xsstest() throws IOException, URISyntaxException {
SecurityEncodersecurityEncoder = new SecurityEncoder();
String XSSstring = servletRequest.getParameter("str");//str为用户可控数据
String cleanValue ="";//转义后变量
cleanValue = securityEncoder.encodeForCSS(XSSstring);//CSS样式输出转义方法
pw.write(cleanValue);//输出转义后的数据
}
URL跨站脚本漏洞解决规范
当用户可控数据输出到HTML页面中或者javascript中的url时，必须使用此方法对用户可控数据做相关转义，下面为该方法调用代码实例：
public void xsstest() throws IOException, URISyntaxException {
SecurityEncodersecurityEncoder = new SecurityEncoder();
String XSSstring = servletRequest.getParameter("str");//str为用户可控数据
String cleanValue ="";//转义后变量
cleanValue = securityEncoder.encodeForURL(XSSstring);//URL中输出转义方法
pw.write(cleanValue);//输出转义后的数据
}
JSON跨站脚本漏洞解决规范
JSON跨站脚本漏洞不同于前几类跨站脚本漏洞，对于JSON跨站脚本攻击采用的是过滤的防御方法，对callback函数名进行过滤，只允许大小写字母，数字和字母，下面为JSON跨站脚本防御方法：
以下两部份代码必须同时使用
1、添加如下JAVA代码，此代码作用是设置HTTP返回头为JSON格式
Java代码response.setContentType("appliaction/json");
按照以上设置，http返回头为appliaction/json 
2、Json参数中对callback函数名做过滤，只允许大小写字母、数字下划线和点
public void xsstest() throws IOException, URISyntaxException {
SecurityEncodersecurityEncoder = new SecurityEncoder();
String XSSstring = servletRequest.getParameter("str");//str为用户可控callback
String cleanValue ="";//转义后变量
cleanValue = securityEncoder.jsonFilter(XSSstring);//过滤callback并输出
pw.write(cleanValue);//输出转义后的数据
}
富文本跨站脚本解决规范
由于富文本本身允许HTML标签代码，但当任意用户输入各种类型的HTML标签时，跨站脚本就出现了，所以副文本跨站脚本漏洞主要采用黑名单过滤的方式进行防御，过滤的字符可能出现在各种位置，例如HTML标签中，HTML标签属性中，CSS样式中等，以下为在各种场景中需要过滤的字符参考：



以下为XSS防御包配置示例可参考进行配置：
1、所有参数中的<>"'进行过滤或编码
<PARAMETER name=""filterChar="<>"'" action="Encode"/>*
2、对某一参数或某个目录的请求配置单独的过滤策略，其余参数使用统一过滤策略（注意将统一策略放置到最后）
<URL pattern="/xss/.">*
<PARAMETER name=""filterChar="<>"'=_" action="Remove"/>*
</URL>
<URL pattern="/chmccsec/.">*
<PARAMETER name="username"filterChar="<>"'=_" action="Encode"/>
<PARAMETER name="desc"filterChar="<> =_" action="Encode"/>
</URL>
<URL pattern="(?!login).">*
<PARAMETER name=""filterChar="<>"'=_" action="Encode"/>*
</URL> 
其他跨站漏洞防御说明
DOM型跨站脚本漏洞的解决规范
在用户可控数据变量输出到脚本中时，先要执行一次javascriptEncode（可见Javascript跨站脚本漏洞解决规范），；其次判断用户可控数据输出到HTML页面时，要做一次HTMLEncode（可见HTML跨站脚本漏洞解决规范），如果输出到javascript中需要做一次javascriptEncode，才能防御DOM型跨站；
DOM型跨站脚本漏洞主要是对于DOM输出和输入函数做相关的转义，相关转义思路如下：
HTMLencode转义
HTML转义主要针对用户输入数据中带有HTML会解析的特殊字符的转码，相关转码规则如下：

JSencode转义
Js转义主要针对用户输入的字符中可能被js执行的标志进行转义，相关转义规则如下：

CSRF跨站请求伪造漏洞
CSRF跨站请求伪造漏洞说明
CSRF的全称是Cross-site request forgery，即跨站请求伪造，攻击者利用被攻击者的身份发起了某些被攻击者原本不知情的请求，导致被攻击者在不知情的情况下进行了业务操作，如购买商品、发布留言、评论等操作。
CSRF跨站请求伪造漏洞风险
如果CSRF跨站请求伪造漏洞被恶意利用的话，将会产生以下的安全风险：
CSRF漏洞可能导致在用户不知情的情况下替用户添加商品，下单；
在用户不知情的情况下替用户发表评论，发送消息，等操作；
可能对用户京豆、绑定网银帐号等相关资金进行非授权操作；
CSRF跨站请求伪造漏洞参考案例
目前在安全知识库中有多种类型的CSRF的案例类型，下面是典型案例：
chmcc联盟跨站请求伪造可致任意用户退出
该案例是由于退出功能没有进行校验导致可造成用户在不知情的情况下退出系统;
详见安全知识库：{_}http://study.chmcc.com/?p=28346_
CSRF跨站请求伪造漏洞防御规范
由于客户端发起的CSRF攻击，由于其Referer是来自不同的域，所以采用验证Referer字段的方法防止CSRF跨站请求伪造漏洞，只有当白名单域内发起请求，才可以视为合法请求，即通过过滤非白名单域，来完成对CSRF漏洞的防御;
以下是采用白名单模式验证Referer字段的参考代码，其实现方式是通过CheckReferer来判断Referer来源是否为白名单域，首先通过whiteList定义了白名单列表，验证Referer字段合法后，通过循环的方式逐一验证白名单列表中的地址是否与来源Referer字段相同：
importjava.net.URI;
importjava.net.URISyntaxException;
import org.apache.commons.lang3.StringUtils;
public class CheckReferer {
publicbooleaninvalidRefer(String referer) {
// referer域白名单根据实际需求配置，此处需要自行配置
String[] whiteList = { "www.chmcc.com", "chmcc.com", "aaa.chmcc.com","bbb.chmcc.com" };
//如果referer是空返回false
if (StringUtils.isEmpty(referer)) {
return false;
}
if (referer.contains("?")) {
referer = referer.substring(0, referer.indexOf("?"));
}
URI referUri = null;
try {
referUri = new URI(referer);
} catch (URISyntaxException e) {
return false;
}
String domain = referUri.getHost().toLowerCase();
//循环验证遍历白名单
for (int i = 0; i <whiteList.length; i++) {
if (whiteList[i].toLowerCase().equals(domain)) {
return true;
}
}
return false;
}
} 
JSON挟持漏洞
JSON挟持漏洞说明
JSON(JavaScript Object Notation) 是一种轻量级的数据交换格式，JSON传输的数据在两个不同的域；例如，代表了A应用的A域名想获取代表B应用的B域名的数据时，由于在JavaScript里无法跨域获取数据，所以一般采取script标签的方式获取数据，传入一些callback来获取最终的数据。这样导致非法网站也会调用相关接口来实现数据的读取，导致了JSON传输数据的挟持。
JSON挟持漏洞风险
JSON挟持对客户端敏感信息泄漏造成的安全影响较大，JSON挟持主要可以获取用户通过JSON传输的用户敏感数据，例如用户注册IP、身份证、银行卡等相关信息，攻击者可以利用这些信息对受攻击者造成二次攻击，如果攻击者广泛使用JSON挟持漏洞，可以造成网站的大量用户敏感信息泄漏。
JSON挟持漏洞参考案例
以下是安全知识库中选取的JSON劫持漏洞相关案例，该案例是通过JSON劫持漏洞造成客户端敏感信息泄漏：
chmccEPT站JSON挟持漏洞
该案例是由于EPT站存在JSON挟持漏洞打可破同源策略的保护，对于用户所有通过JOSN传输的信息进行窃取，一旦重要信息用JSON格式传输，那么全部会被窃取，导致非常严重的信息泄漏；
详见安全知识库：{_}http://study.chmcc.com/?p=28210_ 
JSON挟持漏洞防御规范
尽量避免跨域的数据传输，对于同域的数据传输使用xmlhttp的方式作为数据获取的方式.。如果使用跨域的数据传输，我们的防御手段为对用户发送JSON请求的Referer进行验证，当用户Referer非白名单内字段，则不返回请求：
以下是采用白名单模式验证Referer字段的参考代码如下，其实现方式是通过CheckReferer来判断Referer来源是否为白名单域，首先通过whiteList定义了白名单列表，验证Referer字段合法后，通过循环的方式逐一验证白名单列表中的地址是否与来源Referer字段相同：
importjava.net.URI;
importjava.net.URISyntaxException;
import org.apache.commons.lang3.StringUtils;
public class CheckReferer {
publicbooleaninvalidRefer(String referer) {
// referer域白名单根据实际需求配置
String[] whiteList = { "www.chmcc.com", "chmcc.com", "aaa.chmcc.com","bbb.chmcc.com" };
//如果referer是空的认为非法？？
if (StringUtils.isEmpty(referer)) {
return false;
}
if (referer.contains("?")) {
referer = referer.substring(0, referer.indexOf("?"));
}
URI referUri = null;
try {
referUri = new URI(referer);
} catch (URISyntaxException e) {
return false;
}
String domain = referUri.getHost().toLowerCase();
//循环验证遍历白名单
for (int i = 0; i <whiteList.length; i++) {
if (whiteList[i].toLowerCase().equals(domain)) {
return true;
}
}
return false;
}
} 
XSIO钓鱼漏洞
XSIO钓鱼漏洞说明
XSIO是因为没有限制图片的position属性为absolute，导致可以控制一张图片出现在网页的任意位置，攻击者可欺骗其他用户，获取信息，此漏洞为XSIO漏洞。
XSIO钓鱼漏洞风险
如果存在XSIO漏洞，可以利用图片覆盖整个页面造成非法的页面覆盖，同时攻击者可以利用该覆盖页面连接，当点击相关被覆盖的连接时，页面会跳转到攻击者预先设置好的页面中，将导致对用户的钓鱼及诈骗；
XSIO钓鱼漏洞参考案例
以下是安全知识库中选取的XSIO钓鱼漏洞典型案例：
chmcc众包市场XSIO钓鱼漏洞
该案例是由于众包市场没有限制图片的position属性为absolute绝对定位图片的位置，这就导致了可以把张图插入到整个页面的任意位置，包括网站的banner，包括一个link、一个button，覆盖页面的连接，甚至覆盖整体页面，导致不知情的用户被钓鱼；
详见安全知识库：{_}http://study.chmcc.com/?p=28441_ 
XSIO钓鱼漏洞防御规范
在i什么场景中上传图片的场景XSIO漏洞防御方法主要结合XSIO漏洞形成的方法而防范，将上传的image标签中的图片的position属性限制为absolute及相关变形。(加入传入标签时候的场景)
XSIO漏洞防御参考代码如下，其实现方式是通过使用正则表达式进行对positio:absolute进行判断和替换：
importjava.util.regex.Matcher;
importjava.util.regex.Pattern; 
public class XSIO {
public static String xsio(String content){
String pat = "position[^a-zA-Z0-9]{0,10}:[^a-zA-Z0-9]{0,10}absolute"; //此正则为position:absolute及变形
String result = content;
Pattern p = Pattern.compile(pat,Pattern.CASE_INSENSITIVE);
for(int i = 0;i < 3;i++){
Matcher m = p.matcher(result);
if(!m.find()){
break;
}
result = m.replaceAll("");
}
return result;
}
} 
URL跳转漏洞
URL跳转漏洞说明
由于应用越来越多的需要和其他的第三方应用交互，以及在自身应用内部根据不同的逻辑将用户引向到不同的页面，所以URL跳转便出现在我们的应用中，当对所跳转的连接没有做验证的情况下可能跳到任何一个网站，导致了安全问题的产生，这种问题就是URL跳转漏洞
URL跳转漏洞风险
恶意用户完全可以借用URL跳转漏洞来欺骗安全意识低的用户，从而导致"中奖"之类的欺诈，这对于一些有在线业务的企业如淘宝等，危害较大，同时借助 URL跳转，也可以突破常见的基于"白名单方式"的一些安全限制，如传统IM里对于URL的传播会进行安全校验，但是对于大公司的域名及URL将直接允许通过并且显示会可信的URL，而一旦该URL里包含一些跳转漏洞将可能导致安全限制被绕过。
URL跳转漏洞参考案例
以下是安全知识库中选取的URL跳转漏洞典型案例：
chmcc社区URL跳转漏洞
该案例是由于chmcc社区在与第三方交互时没有进行验证导致可跳转至非预期页面，可以实现网络钓鱼；
详见安全知识库：{_}http://study.chmcc.com/?p=28301_
URL跳转漏洞防御规范
URL跳转漏洞防御的主要思路为验证跳转后的URL是否合法，但需要注意不能只做字符串匹配，因为类似chmcc.com.hack.com此类域名可能绕过字符串匹配，同时还要检测传入域名中是否含有'@'字符，因为浏览器默认会访问@字符以后数据；（要严格匹配白名单）
URL跳转漏洞防御参考代码如下，其实现方式是通过对跳转的目的URL（requestURLWithoutQueryString）进行判断是否合法，首先判断跳转是否为空，如果为空返回null，不为空则返回其host地址，之后进行判断host地址合法性，如果不合法返回chmcc.com；
importjava.net.URI;
public class NetUtils {
public static String getHost(String requestURLWithoutQueryString) {
if (requestURLWithoutQueryString == null || requestURLWithoutQueryString.trim().length() == 0) {
return null;
}
try {
URI requestUri = new URI(requestURLWithoutQueryString);
returnrequestUri.getHost();
} catch (Exception e) {
return null;
}//判断跳转是否为空
}
public static String getDomain(String requestURLWithoutQueryString) {
if(requestURLWithoutQueryString == null){
return ".chmcc.com";
}//获取合法URL标识
String host = getHost(requestURLWithoutQueryString);
if (host == null) {
return ".chmcc.com";
}
if (host.endsWith(".chmcc.com")) {
return ".chmcc.com";
} else {
return ".chmcc.com";
}
}
} 
HTTP基础认证钓鱼漏洞
HTTP基础认证钓鱼漏洞说明
网站通过外部连接引入文件，并没有远程读取，而是将远程连接写入页面，此时远程服务器如果需要身份认证，即会弹出相关认证页面，导致基础认证钓鱼的产生
HTTP基础认证钓鱼漏洞风险
基础认证钓鱼是对客户端进行欺骗式攻击的一种手段，攻击者可以对用户端做出套取用户名密码的页面，导致可直接获取安全意识不足的用户的敏感信息；
HTTP基础认证钓鱼漏洞参考案例
以下是安全知识库中选取的基础认证钓鱼漏洞典型案例：
chmcc众筹基础认证钓鱼漏洞
该案例是由于chmcc众筹页面可直接引入外部文件，导致不知情的用户被钓鱼；
详见安全知识库：{_}http://study.chmcc.com/?p=29496#ID0x0101_
chmcc视频基础认证钓鱼漏洞
该案例是由于chmcc视频页面可直接引入外部文件，导致不知情的用户被钓鱼；
详见安全知识库：{_}http://study.chmcc.com/?p=29496#ID0x0102_ 
HTTP基础认证钓鱼漏洞防御规范
基础认证钓鱼漏洞防御思路为，服务端收取到图片连接后先判断图片是否可直接访问（此处要注意，图片地址需要做检测，需要对内部网络的域名或者ip做限制），如可以访问，就可以正常显示，如不可以访问，对此图片用其他连接代替，防止基础认证钓鱼；
HTTP响应头注入漏洞
HTTP响应头注入漏洞说明
CRLF就是回车(CR, ASCII 13, \r) 换行(LF, ASCII 10, \n)。 CR和LF组合在一起即CRLF。 Web程序代码中把用户提交的参数未做过滤就直接输出到HTTP响应头中，攻击者可以利用该漏洞来注入HTTP响应头，从而对客户端造成攻击，此漏洞为HTTP响应头注入攻击。
HTTP响应头注入漏洞风险
CRLF漏洞主要是对客户端的攻击，成功利用CRLF漏洞可能导致对受攻击用户的以下影响：
绕过浏览器的保护，跳转到恶意站点，对客户端造成攻击
对受攻击者的网页中插入脚本或HTML代码，造成客户端执行相关脚本
在业务方面对受攻击者可能造成用户的敏感信息泄漏及钓鱼攻击
HTTP响应头注入漏洞参考案例
以下是安全知识库中选取的HTTP响应头注入漏洞典型案例：
chmcc团购HTTP响应头注入漏洞
该案例是由于chmcc团购把用户提交的参数未做过滤就直接输出到HTTP响应头中，攻击者可以利用该漏洞来注入HTTP响应头，可以造成多种攻击、例如跨站和欺骗用户下载恶意可执行文件；
详见安全知识库：{_}http://study.chmcc.com/?p=28431#ID0x0101_ 
HTTP响应头注入漏洞防御规范
目前大多数应用程序服务器都可以防止 HTTP 头文件感染恶意字符。例如，当新行传递到 header() 函数时，最新版本的 PHP 将生成一个警告并停止创建头文件。如果PHP 版本能够阻止设置带有换行符的头文件，则其具备对 HTTP Response Splitting 的防御能力。
目前针对HTTP相应头注入的防御思路为：
1、严格检查变量是否已经初始化
2、在设置HTTP响应头的代码中，过滤回车换行（%0d%0a、%0D%0A)字符
3、禁止header()函数中的参数外界可控
4、不要使用有存在bug版本的apache
FLASH安全规范
Flash服务端和客户端如果在安全配置和文件编码上存在问题，将导致攻击者可以利用客户端的Flash文件发起各种请求或者攻击客户端的页面。
FLASH的安全问题主要分为FLASH服务端安全及FLASH客户端安全两部分；

FLASH客户端安全
本地配置文件错误
客户端在嵌入flash文件的时候没有指定flash文件的客户端限制策略，导致嵌入在客户端的flash文件可以访问HTML页面的DOM数或者发起跨域请求。
错误的配置文件：
<object classid="clsid:d27cdb6e-ae6d-11cf-96b8-444553540000" codebase={*}http://fpdownload.macromedia.com/pub/shockwave/cabs/flash/swflash.cab#version=8,0,0,0*`name="Main" width="1000" height="600" align="middle" id="Main"><embed flashvars="site=&sitename=" src="http://a.com/xxx.swf" name="Main" allowscriptaccess="always" type="application/x-shockwave-flash"pluginspage="http://www.macromedia.com/go/getflashplayer" /></object>
例子中的allowscriptaccess选项为always，这样的配置会使flash对于html的通讯也就是执行javascript不做任何限制，默认情况下值为"SameDomain"，既只允许来自于本域的flash与html通讯，建议设置为never；例子中没有设置allowNetworking选项，需要把allowNetworking设置为none，因为allowNetworking在设置为all（默认是）或者是internal的情况下会存在发生csrf的风险，因为flash发起网络请求继承的是浏览器的会话，而且会带上session cookie和本地cookie。
客户端安全配置规范
针对FLASH安全的客户端防御思路为：
1、禁止设置flash的allowscriptaccess为always，必须设置为never，如果设置为SameDomain，需要客户可以上传的flash文件要在单独的一个域下。
2、设置allowNetworking选项为none。
3、设置allowfullscreen选项为false。
FLASH服务端安全
服务端配置错误
FLASH配置文件crossdomain.xml介绍 flash在跨域时唯一的限制策略就是crossdomain.xml文件，该文件限制了flash是否可以跨域读写数据以及允许从什么地方跨域读写数据。
错误配置如下：
<?xml version="1.0"?> 
<cross-domain-policy> 
<allow-access-from domain="" />* 
</cross-domain-policy>
这样的配置可以导致允许任何来自网络上的请求、不论该请求是来自本域内还是其它域发起的。
正确的配置
<cross-domain-policy> 
<allow-access-from domain=".chmcc.com"/>* 
<allow-access-from domain=".chmcc.com"/>* 
<allow-access-from domain=".3.cn"/>* 
<allow-access-from domain=".minitiao.com"/>* 
<allow-access-from domain=".chmcc.com"/>* 
</cross-domain-policy>

FLASH开发安全规范
由于FLASH开发涉及的开发环境复杂，而且其开发与所属应用开发相对独立，所以在开发过程中应该不能忽视其安全性，具体可以参考如下思路：
1、移除敏感信息
确认没有包含像用户名、密码、SQL查询或者其他认证信息在swf文件里面，因为swf文件能够被简单的反编译而使信息泄露
2、客户端的验证
客户端的验证能够通过反编译软件轻易的去除后重新编译，必须在客户端和服务端都做一次验证，但是服务端的验证不能少
3、去除调试信息
去除类似于"trace"和其他一些调试语句，因为他们能够暴露代码或数据的功能，如下代码片段暴露了该段代码的验证功能：
If(checklogin) 
{ 
         Userlogin = ture; 
} 
trace("管理员验证成功");
4、参数传入
如果有加载外部数据的需求，尽量不要在html中用"params"标签或者是querystring这种形式来注入数据到swf文件中。可以的办法是通过sever端的一个http请求来得到参数。
5、allowDomain()的正确用法
flash文件如果有和其他swf文件通信的需求，需要在swf中配置allowDomain()为制定的来源，禁止用*符号来允许任意来源
如下AS代码被严格禁止：
System.security.allowDomain("");* 
loadMovie(param1,param2);
6、ActionScript2.0未初始化全局变量
AS2.0中接受用户通过FlashVars和Querystring中传入的数据并放到全局变量空间中，如果利用不当会引发变量未初始化漏洞从而绕过部分认证，如下AS代码片段所示：
If(checklogin) 
{ 
         Userlogin = ture; 
} 
If(Userlogin) 
{ 
         ShowData(); 
}
如果用户在GET请求或者在HTML中作为一个对象参数将userLoggedIn设为true，如下所示： http://www.xxxx.com/cn_ben/login.swf?Userlogin=ture
  解决方案：AS2.0使用_resolve属性捕获未定义的变量或函数，如下所示：
// instantiate a new object 
varmyObject:Object = new Object(); 

// define the __resolve function 
myObject.__resolve = function (name) { 
return "未定义的变量"; 
};
7、加载调用外部文件
当FLASH加载调用外部文件的时候需要过滤掉里面的恶意内容，
主要有metadata里面的数据和flash mp3 player里的Mp3 ID3 Data，可以引发XSS漏洞（使攻击者可以执行任意javascript），如下代码片段所示：
this.createTextField("txtMetadata",this.getNextHighestDepth(), 10,10, 500, 500); 
txtMetadata.html = true; 
varnc:NetConnection = new NetConnection(); 
nc.connect(null); 
varns:NetStream = new NetStream(nc); 
ns.onMetaData = function(infoObject:Object) { 
for (varpropName:String in infoObject) { 
txtMetadata.htmlText += propName + " = " + 
infoObject[propName]; 
}}; 
ns.play("http://localhost/test.flv");
如果test.flv中包含了js代码将被执行；
onID3 = function() { 
my_text.htmlText = this.id3.author; 
};
8、禁止直接调用ExternalInterface.call来接受外部参数
ExternalInterface.call可以直接调用客户端的js脚本，如下as代码片段所示：
Import flash.external.;* 

……省略代码…… 
ExternalInterface.call(用户输入变量，用户数据参数); 
……省略代码……
如果用户提交变量eval，提交参数为任意js语句，那么用户提交的代码就会被执行。 

服务端安全规范
SQL注入漏洞
程序在查询数据库时，开发人员可能用字符串拼接的办法来处理sql语句，如果语句中部分参数为客户端可控数据，那么可能产生sql注入攻击，下面是针对sql注入攻击的开发规范。
SQL查询本着调用数据库框架进行查询的原则，禁止开发人员自行拼接SQL语句。
SQL注入漏洞说明
应用为了和数据库进行沟通完成必要的管理和存储工作，必须和数据库保留一种接口。目前的数据库一般都是提供api以支持管理，应用使用底层开发语言如Php，Java，asp，Python与这些api进行通讯。对于数据库的操作，目前普遍使用一种SQL语言（Structured Query Language语言，SQL语言的功能包括查询、操纵、定义和控制，是一个综合的、通用的关系数据库语言，同时又是一种高度非过程化的语言，只要求用户指出做什么而不需要指出怎么做），SQL作为字符串通过API传入给数据库，数据库将查询的结果返回，数据库自身是无法分辨传入的SQL是合法的还是不合法的，它完全信任传入的数据，如果传入的SQL语句被恶意用户控制或者篡改，将导致数据库以当前调用者的身份执行预期之外的命令并且返回结果，导致安全问题。
SQL注入漏洞风险
恶意用户利用SQL注入可以做到： 
SQL注入漏洞参考案例
以下是安全知识库中选取的SQL注入漏洞典型案例：
chmcc调查平台SQL注入漏洞
该漏洞是由于chmcc调查平台使用Limesurvey调查系统，该系统登录接口中Sid没有进行过滤导致可执行SQL语句，造成数据库泄漏；
详见安全知识库：{_}http://study.chmcc.com/?p=28143#ID0x0101_
chmcc应用商店网关SQL注入漏洞
该漏洞是由于应用网关在更新应用接口没有进行过滤导致可执行SQL语句，造成数据库泄漏；
详见安全知识库：{_}http://study.chmcc.com/?p=28143#ID0x0102_
代码库SQL注入漏洞
该漏洞是由于chmcc代码库的问题反馈功能，POST问题时，标签参数issueTag没有进行过滤导致可执行SQL语句，造成数据库泄漏；
详见安全知识库：{_}http://study.chmcc.com/?p=28143#ID0x0103_ 
SQL注入漏洞防御规范
SQL注入防御思路主要为使用占位符的方式防御，在JAVA中，先把SQL语句加载到内存，用户提交的数据是以变量形式存入内存并执行，此方法可彻底防止SQL注入漏洞，相关各种调用方式的参考代码如下：
chmccBC方式
正常查询 
conn = createConnection(); 
String sql = "select name,password from manager where name=? and password=?"; 
stat = conn.prepareStatement(sql); 
stat.setString(1, name); 
stat.setString(2, password); 
stat.executeQuery(sql); 
模糊查询 
conn = createConnection(); 
String sql = "select * from table where urllike ?"; 
stat = con.prepareStatement(sql); 
String data="data"; 
stat.setString(1, "%"data"%"); 
stat.executeQuery(sql);
Hibernate框架
1、按参数名称绑定：
在HQL语句中定义命名参数要用":"开头，形式如下
Query query=session.createQuery("from User user where user.name=:customername and user:customerage=:age "); 
query.setString("customername",name); 
query.setInteger("customerage",age);
上面代码中用:customername和:customerage分别定义了命名参数customername和customerage，然后用 Query接口的setXXX()方法设定名参数值，setXXX()方法包含两个参数，分别是命名参数名称和命名参数实际值。
2、按参数位置绑定：
在HQL查询语句中用"?"来定义参数位置，形式如下：
Query query=session.createQuery("from User user where user.name=? anduser.age =? "); 
query.setString(0,name); 
query.setInteger(1,age); 
同样使用setXXX()方法设定绑定参数，只不过这时setXXX()方法的第一个参数代表邦定参数在HQL语句中出现的位置编号（由0开始编号），第二个参数仍然代表参数实际值。
注：在实际开发中，提倡使用按名称邦定命名参数，因为这不但可以提供非常好的程序可读性，而且也提高了程序的易维护性，因为当查询参数的位置发生改变时，按名称邦定名参数的方式中是不需要调整程序代码的。
3、setParameter()方法：
在Hibernate的HQL查询中可以通过setParameter()方法邦定任意类型的参数，如下代码：
String hql="from User user where user.name=:customername "; 
Query query=session.createQuery(hql); 
query.setParameter("customername",name,Hibernate.STRING);
上面代码所示，setParameter()方法包含三个参数，分别是命名参数名称，命名参数实际值，以及命名参数映射类型。对于某些参数类型setParameter()方法可以更具参数值的Java类型，猜测出对应的映射类型，因此这时不需要显示写出映射类型，像上面的例子，可以直接这样写：query.setParameter("customername",name);但是对于一些类型就必须写明映射类型，比如java.util.Date类型，因为它会对应Hibernate的多种映射类型，比如Hibernate.DATA或者Hibernate.TIMESTAMP。
4、setProperties()方法：
在Hibernate中可以使用setProperties()方法，将命名参数与一个对象的属性值绑定在一起，如下程序代码：
Customer customer=new Customer(); 
customer.setName("pansl"); 
customer.setAge(80); 
Query query=session.createQuery("from Customer c where c.name=:name and c.age=:age "); 
query.setProperties(customer);

setProperties()方法会自动将customer对象实例的属性值匹配到命名参数上，但是要求命名参数名称必须要与实体对象相应的属性同名。
这里还有一个特殊的setEntity()方法，它会把命名参数与一个持久化对象相关联，如下面代码所示：
Customer customer=(Customer)session.load(Customer.class,"1"); 
Query query=session.createQuery("from Order order where order.customer=:customer "); 
query. setEntity("customer",customer); 
List list=query.list();
上面的代码会生成类似如下的SQL语句：
Select * from order where customer_ID='1';
Mybatis框架
<select id="getBlogById" resultType="Blog" parameterType="int"> 
selectid,title,author,contentfrom blog where id=#{id} 
</select> 
模糊查询（适用于MySQL数据库） 
<isNotEmpty prepend="and" property="name"> 
selectid,name,age from user where name like concat('%',#{name},'%') 
</isNotEmpty>






XML注入漏洞
XML注入漏洞说明
很多web应用数据及配置文件会以多种形式保存到服务器，其中数据库方式，文档方式，XML方式，文件方式等多种方式，其中以XML文件保存配置最为常见，XML文件的易用性也体现于此；
当脚本把用户可控数据写入XML文件时，用户在可控数据内插入XML标签，可能导致xml注入。
XML注入漏洞风险
当XML为配置文件时，XML注入可能导致对XML文件的重新配置或破坏性操作
XML注入可能导致越权访问，把低权限用户提升为高权限用户
XML注入可能对权限配置文件做相关操作，对不可访问的目录进行非授权访问
XML注入漏洞防御规范
XML注入对系统及相关应用影响较大，主要防御思路为对用户可控数据插入到XML文件中时，对特殊字符做转码操作；
无论在任何环境中，用户可控数据写入XML文件中时，必须进行HTMLencode转码操作，相关转码字符可按照以下思路换吗：
Special Character
Escape Sequence
Purpose
&
&
Ampersand sign
'
'
Single quote
"
"
Double quote
> 
>
Greater than
< 
<
Less than
相关转码函数可参考HTMLencode函数。
代码注入漏洞
代码注入漏洞说明
web应用代码中，允许接收用户输入一段代码，之后在web应用服务器上执行这段代码，并返回给用户。
由于用户可以自定义输入一段代码，在服务器上执行，所以恶意用户可以写一个远程控制木马，直接获取服务器控制权限，所有服务器上的资源都会被恶意用户获取和修改，甚至可以直接控制数据库。 
代码注入漏洞风险
代码注入攻击风险极高，对于应用的攻击也也是可获得较高操作权限，主要的风险如下：
攻击者可利用代码注入漏洞执行任意代码，来操作服务器
攻击者可利用代码注入漏洞操作数据库，插入恶意数据，可能获取系统权限
攻击者可利用代码注入攻击修改系统配置，修改网络配置，可能对服务器及网络造成影响
代码注入攻击后可以进一步对网络渗透，由于代码注入攻击多半可获取系统权限，对网络的进一步渗透难度大大降低
总之，代码注入攻击风险极大，直接获取web服务器权限，直接操作数据库，直接修改网络配置，进一步渗透内网，此漏洞为极高风险安全漏洞。 
代码注入漏洞防御规范
针对代码注入漏洞的防御，当应用程序必须执行代码环境中，代码必须以参数选择形式传入应用程序，禁止直接调用用户输入信息，相关操作可变为用户选择执行id对数据库做映射关系来执行代码，相关参考代码如下：
此段代码中，根据id传输的变量值来执行系统命令，用户只能选则id，id在程序中被写死，用户不可直接操作，代码如下 
public void doGet(HttpServletRequest request, HttpServletResponse response) 
throws ServletException, IOException { 
String id = request.getParameter("id"); 
//根据用户输入的编号，执行实现写入的代码 
switch(Integer.parseInt(id)){ 
case 1: 
Runtime.getRuntime().exec("whoami"); 
break; 
case 2: 
Runtime.getRuntime().exec("ifconfig"); 
break; 
} 
}

文件上传漏洞
文件上传漏洞说明
Web应用程序在处理用户上传的文件操作时，如果用户上传文件的路径、文件名、扩展名成为用户可控数据，就会导致直接上传脚本木马到web服务器上，直接控制web服务器。
文件上传漏洞风险
文件上传漏洞，一旦被攻击者成功利用，可以导致攻击者，直接上传WebShell到Web服务器，攻击者可以直接获得Web服务器的权限，还可以进一步获取数据库中的数据、攻击内网其他主机，如果服务器存在提权漏洞，攻击者可以提升权限完全控制整个服务器。 
文件上传漏洞参考案例
以下是安全知识库中选取的文件上传漏洞典型案例：
chmcc视频任意文件上传漏洞
该案例是由于chmcc视频服务器对上传的图片头部和尾部均做了校验，但未对图片中的内容做过滤。上传后的图片仍保持其原后缀名，所以上传后的文件可以被解析，当其中包含恶意代码时，即可按照攻击者的意图执行；
详见安全知识库：{_}http://study.chmcc.com/?p=29509#ID0x0101_ 
IIS6.0上传漏洞案例
Microsoft IIS可以ASP或者任何其他可执行扩展执行任何扩展名文件，如"malicioius.asp;.jpg"就以ASP文件方式在服务器上执行，需要文件上传程序通过检查文件名的最后一段作为扩展名来保护系统。利用这个漏洞，攻击者可以绕过保护把危险的可执行文件上传到服务器上。
PHP上传漏洞实例
当上传文件名为x.php%00.jpg时，php验证程序后缀名为JPG允许上传，然而当php取文件名时，读取到%00会认为是截断符号，便会以x.php进行存储，最终导致php文件上传。 
文件上传漏洞防御规范
目前云平台提供了统一上传接口用来存储文件，对于没有采用该接口的上传接口应该参考以下的防御思路：
检查上传文件扩展名白名单，不属于白名单内，不允许上传。
上传文件路径及文件名不可取用户可控数据。
上传文件的目录不能提供脚本解析。
图片上传，要通过处理（转码、缩略图、水印等），无异常后才能保存到服务器。
任意文件下载漏洞
任意文件下载漏洞说明
当用户通过web下载文件时，所下载文件及路径为变量形式传给程序，程序未对下载文件及路径做检测，导致用户可随意输入下载地址，恶意用户可能构造跨路径文件名，当程序执行后，返回相关文件内容，造成任意文件下载漏洞的出现。
任意文件下载漏洞为高风险漏洞，可以读取服务器配置文件，相关用户名密码等，造成非常大的安全隐患。 
任意文件下载漏洞风险
任意文件下载漏洞为较高的安全漏洞，造成的影响较大，主要为以下几点：
当用户可控下载路径及文件时，恶意用户可能下载系统重要配置文件，从而知道系统内部网络地址，服务器帐号密码等信息
当用户可控服务器下载内容时，恶意用户可通过相关路径下载到应用源代码，从而导致源代码泄漏的安全风险；
但用户可控服务器下载内容时，恶意用户可能传入网络地址，导致对内部网络的非授权访问及攻击
任意文件下载漏洞参考案例
以下是安全知识库中选取的任意文件下载漏洞典型案例：
chmccME任意文件下载漏洞
该案例是由于chmccME对用户端输入检验不严导致，可通过规律化的猜解可读取文件程序源码、系统允许访问的一些系统配置文件密码等。
详见安全知识库：{_}http://study.chmcc.com/?p=29517#ID0x0101_
任意文件下载漏洞防御规范
防止任意文件下载方式非常多，下面给出建议性解决方案如下连接
此方式的思路为对用户下载文件名进行对应编码，此处用的编码为MD5,用户只能传入文件对应的MD5值，程序把相关值带入数据库进行查询，当程序返回文件存在时，才可下载。 http://www.chmcc.com/download.action?file=(32位md5串)
md5传在数据库中对应下载地址
此解决思路为参考思路，场景为普通下载，用户只能通过传入的md5值进行下载，程序通过md5值在数据库中对应的文件路径进行返回，当传入值不存在时，无法下载文件。
package com.message; 
importjavax.servlet.;* 
importjavax.servlet.http.;* 
import java.io.;* 
importjava.util.;* 
public class FileDownServlet extends HttpServlet { 
private static final String CONTENT_TYPE = "text/html; charset=GBK"; 
public void init() throws ServletException { 
} 
//Process the HTTP Get request 
public void doGet(HttpServletRequest request, HttpServletResponse response) throws 
ServletException, IOException { 
response.setContentType(CONTENT_TYPE); 
String filemd5=request.getParameter("file"); 
String fileName = //根据md5 从数据库获取相应的文件名 
//如果找不到相应的文件名则return 
If(null == fileName){ 
Return; 
} 
File file=new File(fileName); 
response.setContentType("application/x-msdownload"); 
response.setContentLength((int)file.length()); 
response.setHeader("Content-Disposition","attachment;filename="+filename); 
FileInputStreamfis=new FileInputStream(file); 
BufferedInputStream buff=new BufferedInputStream(fis); 
byte [] b=new byte[1024]; 
long k=0; 
OutputStreammyout=response.getOutputStream(); 
while(k<file.length()){ 
int j=buff.read(b,0,1024); 
k+=j; 
myout.write(b,0,j); 
} 
myout.flush(); 
} 
public void doPost(HttpServletRequest request, HttpServletResponse response) throws 
ServletException, IOException { 
doGet(request, response); 
} 
//Clean up resources 
public void destroy() { 
} 
}

信息泄露漏洞
信息泄露漏洞说明
信息收集往往作为实施攻击的首要工作，不管是个人还是应用程序还是服务器，一丝一毫的暴露都会成为恶意者利用的出发点。如利用程序所用框架、服务器所在版本去找寻相关的EXP或解析漏洞来开展攻击。
目前我们面临的信息泄露有很多种，如后台管理地址泄漏，SVN信息泄漏，程序错误信息泄漏，版本泄漏，应用程序框架泄漏，数据库报错信息泄漏，源代码泄露等等；
信息泄露漏洞风险
信息泄露漏洞的危害根据泄露内容的关键性来决定，例如网站的源代码泄露有可能会影响业务的隐私安全。SVN信息泄漏可能进一步导致网站源代码的泄漏，应用程序错误信息泄露则可能会将目前使用的应用程序配置信息泄露，所以每一个存在隐患和风险的点我们都要做到坚实的预防。
信息泄露漏洞参考案例
以下是安全知识库中选取的信息泄漏漏洞典型案例，典型案例是根据不同类型的信息泄漏进行介绍：
chmcc多个系统存在svn信息泄露漏洞
详见安全知识库：{_}http://study.chmcc.com/?p=28592#ID0x00_
chmcc多个系统测试页面信息泄露漏洞
详见安全知识库：{_}http://study.chmcc.com/?p=28592#ID0x03_
chmcc多个系统源代码信息泄露漏洞
详见安全知识库：{_}http://study.chmcc.com/?p=28592#ID0x05_
chmcc多个solr后台泄露漏洞
详见安全知识库：{_}http://study.chmcc.com/?p=28592#ID0x08_
chmcc多个系统错误信息泄露漏洞
详见安全知识库：{_}http://study.chmcc.com/?p=28592#ID0x07_ 
信息泄露漏洞防御规范
由于信息泄露存在的原因各不相同，具体针对漏洞的防御方法可参考上面案例中的解决防范来防御，下面针对chmcc常见到额几种信息泄漏做出如下规定：
1、GITHUB禁止传与chmcc有关的代码，其他任何代码中禁止包含chmccerp信息、邮箱信息，通讯录信息等敏感信息
2、上线程序必须删除SVN目录
3、应用程序上线后必须在代码中做容错处理，错误不能直接暴露于页面中
4、重要的后台程序禁止互联网访问，禁止使用简单容易猜解的目录，例如admin/manage/manager/houtai/login等 
框架安全规范
Struts2框架安全
Struts2远程代码执行漏洞说明
Struts2 是第二代基于Model-View-Controller (MVC)模型的java企业级web应用框架。它是WebWork和Struts社区合并后的产物Apache Struts2的action:、redirect:和redirectAction:前缀参数在实现其功能的过程中使用了Ognl表达式，并将用户通过 URL提交的内容拼接入Ognl表达式中，从而造成攻击者可以通过构造恶意URL来执行任意Java代码，进而可执行任意命令redirect:和redirectAction:此两项前缀为Struts默认开启功能，目前Struts 2.3.15.1以下版本均存在此漏洞；
Struts2远程代码执行漏洞影响
Strust2远程代码执行漏洞风险极高，发现即为严重漏洞，必须立刻处理，struts2远程代码执行漏洞主要风险如下：
struts2远程代码执行漏洞触发后可在服务器执行任意代码；
struts2远程代码执行漏洞导致服务器被上传木马，对服务器本身所有代码存在泄漏风险；
struts2远程代码执行漏洞会导致数据库的泄漏，对应用所连接的数据库会有严重的安全隐患；
struts2远程代码执行漏洞可能被黑客利用对内部网络的进一步攻击，导致整个网络沦陷，大量数据泄漏；

Struts2远程代码执行漏洞参考案例
以下是安全知识库中选取的Struts2远程代码执行漏洞典型案例：
chmcc移动版Struts2远程代码执行漏洞
该案例是由于chmcc移动版使用了存在漏洞的struts版本，导致其可被执行任意命令，如上传shell等；
详见安全知识库：{_}http://study.chmcc.com/?p=28426#ID0x0101_
chmcc企业版Struts2远程代码执行漏洞
该案例是由于chmcc企业版使用了存在漏洞的struts版本，导致其可被执行任意命令，如上传shell等；
详见安全知识库：{_}http://study.chmcc.com/?p=28426#ID0x0102_
防御规范
使用最新版本struts2框架（目前为2.3.16.3 GA），并且配置文件（struts.xml）,添加如下配置属性：
<constant name="struts.devMode" value="false"/>
并且，针对struts2远程代码执行漏洞，请安装chmccstruts2补丁包：
_插件JAR包：http://artifactory.chmcc-develop.com/webapp/search/artifact?2&q=struts2-security-plugin_
插件使用手册：
_http://jpcloud.chmcc.com/pages/viewpage.action?pageId=6881645_
注意：如果没有特别需求，开发程序时，应该避免使用ValueStack类中的setValue、getValue等方法，这些方法会将传入的参数作为表达式执行，如果参数的值可以被攻击者控制，可以导致代码执行。 
SpringMVC框架安全
SpringMVC远程代码执行漏洞说明
影响版本：
SpringSource Spring Framework 3.0.0 – 3.0.2SpringSource Spring Framework 2.5.0 – 2.5.7
Spring框架提供了允许使用客户端所提供的数据来更新对象属性的机制，而该机制允许攻击者修改用于通过class.classloader加载对象的类加载器的属性，这可能导致执行任意命令。例如，攻击者可以将类加载器所使用的URL修改到受控的位置。导致代码执行。
SpringMVC远程代码执行漏洞参考案例
以下是安全知识库中选取的SpringMVC远程代码执行漏洞典型案例：
SpringMVC远程代码执行漏洞
_详见安全知识库：http://study.chmcc.com/?p=28754_
防御规范
不要使用存在漏洞的版本。
框架安全版本规范
框架安全中，部分框架存在致命安全漏洞，相关框架安全版本与需要安装的补丁如下表所示，每个系统上线必须遵循以下版本上线


业务安全规范
登录安全规范
登录安全风险说明
登录安全是指需要用户进行登录的业务系统在登录操作时涉及到的安全问题；用户登录是业务逻辑中最比较重要的一部分，同时往往是最容易受到攻击的节点；如果登录接口存在安全风险，将导致业务所使用账号系统被撞库，扫号，暴力破解密码等，事件一旦发生不仅会对业务用户造成极高的安全风险，也将对业务本身的安全造成极大的威胁。
造成登录安全风险的主要原因是由于登录接口存在一个或多个脆弱点造成，如：没有对登录操作进行签名；登录操作无验证码，或存在验证码失效等缺陷导致验证码被绕过；服务端没用对IP、账号的限制策略或策略不完善等；
登录安全风险参考案例
以下是安全知识库中选取的登录安全相关案例，其中案例一是由于APP登录接口存在缺陷导致可对chmcc账户系统进行撞库；案例二是由于邮箱登录接口存在缺陷导致针对员工邮箱账户系统的恶意撞库及暴力破解；
chmcc云盘APP缺乏登录验证导致撞库及暴力破解
该事件是由于云盘APP所使用的登录网关没有进行加密通信，也没有验证码等安全策略，在服务器端也无验证；
详见安全知识库：{_}http://study.chmcc.com/?p=29621#ID0x02_
chmcc企业邮箱登录安全缺陷导致员工账号密码被重置
该事件是由于企业邮箱使用微软Exchange套件，在登录时没有验证码服务器端也没有对失败次数客户端进行相关验证失败锁定策略；
详见安全知识库：{_}http://study.chmcc.com/?p=29611#ID0x01_
登录安全风险防御规范
目前chmcc业务登录接口主要分布在WEB端和移动APP端，我们建议在可以使用相关账号系统统一登录接口登录时必须使用统一登录接口，其保证用户帐号安全及业务安全的同时，还减少各业务在登录功能上的重复开发投入。
安全管理部针对登录安全风险的防御已发布了相关规范；
请参考附件《chmcc外网登录安全管理规范》
权限控制安全规范
权限控制安全风险说明
权限控制是指业务系统要根据不同的用户限制其可操作或查看的页面或功能，该限制涉及到的操作即权限控制。例如在论坛系统里游客只能查看信息、注册会员可以查看及发表信息、管理员可以修改他人信息，这类限制就是基于数据权限的访问控制；如果在系统设计阶段没有合理设计权限控制，导致这类访问控制存在缺陷，就会发生游客可以发表信息，注册会员可以修改他人信息等等这类越权操作的发生；
权限控制安全风险存在于各类系统中，其根本原因是由于系统没有能对用户的权限进行合理分配，后端没有判断数据的所属人，或判断所属人是否可以使用相应的功能，最终导致客户端可以完成伪造请求，导致权限被滥用；
根据权限失效的影响范围将权限控制场景分为垂直权限控制和水平权限控制：
垂直权限控制：是指需要针对不同用户角色的高低来划分对数据内容的访问控制，如果垂直权限控制失效将导致未授权用户访问管理页面，修改订单内容，篡改数据等等，直接危害核心系统安全；
水平权限控制：是指需要针对相同用户角色之间数据内容的访问控制关系，如果水平权限控制失效将导致任意查看其他人数据，修改其他人订单信息，对用户财产和隐私造成威胁；
权限控制安全参考案例
以下是安全知识库中选取的垂直权限相关案例，其中案例一是比较具有代表意义，chmcco2o任意用户都可使用管理员的重置任意用户密码功能：
chmcco2o权限登录系统可重置任意用户密码
该事件是由于o2o权限登录系统没有进行有效的垂直权限控制，在用户修改密码界面非管理员用户也可以通过更改用户名进行重置任意用户密码；
详见安全知识库：{_}{+}http://study.chmcc.com/?p=29463#ID0x0101+_
chmcc社区任意文件读取
该事件是由于chmcc社区没有进行有效的垂直权限控制，任意用户可以读取chmcc社区系统文件，可以进行浏览和下载操作；
详见安全知识库：{_}{+}http://study.chmcc.com/?p=28856+_ 
以下是安全知识库中选取的水平权限相关案例，这些案例都是由于水平权限控制缺失所导致，例如可以查看或操作他人订单，查看他人隐私信息等：
苏宁易购可越权操作他人订单
该事件是由于商城没有对用户水平权限进行有效的控制，导致用户在查看个人订单信息时，可以通过修改订单号查看和操作其他用户订单；
详见安全知识库：{_}http://study.chmcc.com/?p=28673_
chmcc商城查看任意用户浏览记录漏洞说明
该事件同样是由于没有对用户水平权限进行有效的控制，用户可查看任意用户浏览记录，导致用户隐私泄漏；
详见安全知识库：{_}http://study.chmcc.com/?p=28832_
chmcc智能云可查看及管理任意智能设备漏洞说明
该事件是由于商城客服管理的代用户下单功能没有对用户水平权限进行有效控制，导致可以通过key值访问任意账户；
详见安全知识库：{_}http://study.chmcc.com/?p=29463#ID0x0101_
权限控制安全风险防御规范
针对垂直权限安全风险和水平权限安全风险的分别采用不同的防御方法：
垂直权限的控制方法：
垂直权限的管理目前比较普遍的方法是采用建立用户与权限之间的对应关系，定义不同的角色即权限的集合。用户分配到不同的角色中，系统验证权限时，验证用户所属的角色，根据该角色所拥有的权限进行授权了。
目前可以参考Spring Security中针对垂直权限管理比较成熟的解决方案，并且还分别提供了基于URL的控制方式及基于表达式的控制方式：
基于URL的用户权限控制：
<sec:http> 
<sec:intercept-url pattern="/president_portal.do" access="ROLE_PRESIDENT" /> 
<sec:intercept-url pattern="/manager_portal.do" access="ROLE_MANAGER" /> 
<sec:intercept-url pattern="/ **" access=" ROLE_USER" /> 
<sec:form-login /> 
<sec:logout /> 
</sec:http>
基于表达式的用户权限控制：
<http use-expressions="true"> 
<intercept-url pattern="/admin"access="hasRole('admin')and hasIpAddress('192.168.1.1')"/>* 
</http>
水平权限的控制方法
相对于垂直权限控制管理，由于水平权限基于实际业务，所以没有统一的方法进行控制；水平权限控制主要偏向对当前权限用户操作当前数据进行所属权限的验证；
例如水平权限参考案例中的典型案例chmcc智能云可查看及管理任意智能设备漏洞，该漏洞出现的原因就是在调用设备时没有验证该设备是否属于调用设备用户；其解决方法就是在控制器中加入验证函数，验证调用设备是否所属调用用户，如果调用异常返回错误页面；
逻辑设计安全规范
逻辑设计缺陷风险说明
逻辑设计缺陷是业务系统在业务功能上出现的缺陷，导致利用该缺陷可以对系统造成威胁，如资源滥用、信息泄漏等，例如，注册或重置密码时通过手机或邮箱验证验证码进行验证，支付交易时需要使用二次支付密码，或者需要使用动态口令等；这类验证操作也往往存在逻辑缺陷漏洞，导致功能被恶意滥用，对业务系统造成验证影响；
由于业务逻辑设计缺陷与实际业务的关系很紧密，在例行的安全检查和测试中非常不容易发现安全隐患；由于其产生的问题危害比较严重，直接造成损失也较大；
逻辑设计缺陷参考案例
以下是安全知识库中选取的逻辑设计缺陷相关案例，其中涉及到多种业务在逻辑设计上存在的严重缺陷，其他类型的逻辑缺陷案例请参考安全知识库：
chmcc电商云免费订购续订收费服务
该事件是由于电商云订购服务功能逻辑设计缺陷，导致可以将免费产品订单中的serviceId，改为收费产品的serviceId，导致用户可以免费使用收费服务，其可造成较高的业务损失；
详见安全知识库：{_}http://study.chmcc.com/?p=29671_
网银钱包密码找回功能手机验证码回显在网页
该事件是由于没有正确处理手机验证码，导致网银钱包的通过手机找回密码功能中的手机验证码都直接返回在页面中，可以造成重置任何人的密码，严重威胁客户资产；
详见安全知识库：{_}http://study.chmcc.com/?p=29689#ID0x03_
网易手机邮箱任意密码重置强行绑定
该事件是由于网易手机邮箱对用户输入数据没有做后台验证，导致修改任意帐号密码和强制手机绑定；
详见安全知识库：{_}http://study.chmcc.com/?p=28909_ 
逻辑设计缺陷安全风险防御规范
业务逻辑问题是一种设计缺陷，首先在产品设计阶段需要开发团队与业务团队、安全团队进行充分交流；
由于业务逻辑的安全防御依赖业务的实现方式、流程等多方面因素，所以目前没有针对业务逻辑设计权限的统一解决方案，可以参考目前chmcc商场主要业务的逻辑设计权限防御方法：
需要进行验证时，针对验证码使用需要严格控制其过期时间，及复杂度；发送验证码过程中还要正确处理其内容防止被恶意获取；对于短信发送次数限制还要根据业务实际需求进行前、后端限制，防止短信包多次发送；
在处理交易时，不要直接在数据包中加入金额和数量等敏感数值、严格控制购买数量的大小，不允许数量为负数，控制总支付金额是一个正常的数。
还需要对关键操作进行签名保护，并且注意签名算法不可被猜测到，保证攻击者修改数据的时候验证不会通过。
同时根据业务的需要来调整异常处理的方案，发现异常，可以直接报错，拒绝继续运行。
隐私保护安全规范
隐私保护安全风险说明
隐私保护是指在隐私数据在传输过程中和存储过程中，保证数据不会被人获取后直接识别；业务系统在为用户提供服务的同时会获得各种各样的用户隐私数据，如何保护这些用户的隐私数据也是业务系统运行过程中最重要的一步；如果用户数据隐私保护得当，就算系统被入侵，也不会因为隐私数据泄漏造成较大损失；
隐私保护缺失主要为隐私数据在传输过程中使用明文传输，在存储过程中使用明文或使用单次MD5加密、BASE64编码等方式存储，这类问题均会存在较大的隐私泄漏安全隐患。
隐私保护安全参考案例
以下是安全知识库中选取的典型隐私保护缺失案例，其中比较典型的案例为使用单次MD5算法存储隐私文件，该类事件也曾发生在很多互联网公司，都造成了一定程度的损失:
chmcc手机客户端单次MD5存储密码文件
该事件是由于手机客户端本地存储了一次MD5算法的用户明密码文件，一旦配置文件被获取，将直接导致用户密码泄漏；
详见安全知识库：{_}http://study.chmcc.com/?p=28446#ID0x0101_
口袋校园使用单次MD5算法存储密码文件导致用户数据泄漏
该事件与CSDN数据泄漏事件相似，其存储用户密码的时候只采用了一次MD5算法进行存储，黑客通过入侵系统获取文件后，进行简单解密就获得全部数据；
详见安全知识库：{_}http://study.chmcc.com/?p=28716#ID0x0101_
网银钱包密保信息未加密显示
该事件是由于网银钱包的手机充值界面没有将密保手机加*显示，导致密保信息数据可被窥视及截取，导致被利用；
详见安全知识库：{_}http://study.chmcc.com/?p=28856_
隐私保护安全风险防御规范
首先，保护隐私数据需要界定哪些数据属于用户的隐私数据，并非所有数据都需要进行保护，举个例子，客户的手机号一般来说是非常隐私的数据，如果泄露了可能会使用户遭受垃圾短信和推销电话的骚扰。但是一些商业网站可以帮助客户进行商业宣传，其会将客户手机号码广而告之，这些手机号码又不属于隐私数据；
全部密码信息都需要作为隐私数据进行加密保护，密码信息包括但不限于用户登录密码，云汇私有代码库密码，云盘隐私空间密码等等；
用户个人资料分为公开和非公开，用户需要可自行设置自己的个人资料是否公开；
如果确定数据为隐私数据在算法方面，推荐使用随机加盐算法：
加盐法的最大特色是增加加密强度，它是一种动态方法。经过加盐处理可以做到，即使用同样的明文、同样的用户密码每次加密得到的密文都不一样，密码、明文、密文没有固定的对应关系；
具体实现方式，请参考以下代码：
import java.security.SecureRandom;//安全随机数类 
import org.apache.commons.codec.digest.DigestUtils; 
public class hash { 
public static void main(String[] args) { 
hash h = new hash(); 
System.out.println(h.chmcc_hash("password"));//接收未加密字符串 
} 
//生成随机salt函数 
public String randomkeys(int length){ 
String str = "1234567890abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLOMNOPQRSTUVWXYZ"; 
char[] strchr = str.toCharArray(); 
String key = ""; 
for(int i=0;i<length;i++){ 
SecureRandom r = new SecureRandom(); 
key += strchr[r.nextInt(35)]; 
} 
return key; 
} 
//加密函数 
public String chmcc_hash(String password){ 
SecureRandom r = new SecureRandom(); 
int rand = 5 + r.nextInt(25); 
String salt = randomkeys(rand); 
String md5Pass = password + salt; 
String finalPass = DigestUtils.md5Hex(DigestUtils.md5Hex(DigestUtils.md5Hex(md5Pass))); 
return finalPass + salt; 
} 
}
云业务相关安全规范
云业务安全风险说明
chmcc云平台是致力于打造公共、开放、安全的云计算服务平台。随着chmcc云的慢慢成熟，用户群体增长，其业务安全风险也逐渐显现。
例如，宙斯、云擎、云峰等系统在提供大量的网络接口和API为客户提供业务同时，相当于给恶意攻击开放了多个攻击接口；由于缺乏验证和控制措施导致云资源的虚拟池化和共享资源的来源，甚至将资源用作攻击其他目标业务的手段等等
云业务安全问题同样属于业务逻辑设计的范围，其与实际业务的关系很紧密，由于云业务的特殊性，其对业务安全的要求更高一些，但在例行的安全检查和测试中非常不容易发现这类安全隐患；由于其产生的问题危害比较严重，直接造成损失也较大；
云业务安全参考案例
以下是安全知识库中选取的云业务相关参考案例，这些案例都是云业务相关的安全案例，其对业务的影响都是非常严重的:
chmccJAE数据库任意文件读取
该漏洞是由于JAE上Mysql数据库没有禁用load_file函数，导致可以读取服务器上任意文件，造成敏感信息泄露，严重威胁系统安全；
详见安全知识库：{_}{+}http://study.chmcc.com/?p=28856#ID0x03+_
chmccJAE绕过限制读取任意系统文件
该漏洞是由于打包机在通过Git部署应用的时候，没有校验代码中的软链文件，导致用户在发布应用时可通过软链读取宿主机中的任意文件；
详见安全知识库：{_}{+}http://study.chmcc.com/?p=28856#ID0x04+_
chmcc智能云可查看及管理任意智能设备漏洞说明
该事件是由于商城客服管理的代用户下单功能没有对用户水平权限进行有效控制，导致可以通过key值访问任意账户；
详见安全知识库：{_}http://study.chmcc.com/?p=29463#ID0x0101_
chmcc电商云免费订购续订收费服务
该事件是由于电商云订购服务功能逻辑设计缺陷，导致可以将免费产品订单中的serviceId，改为收费产品的serviceId，导致用户可以免费使用收费服务，其可造成较高的业务损失；
详见安全知识库：{_}http://study.chmcc.com/?p=29671_ 
云业务安全风险防御规范
云业务安全涉及到的传统安全，如业务系统的客户端、服务端安全问题可以参考通用的WEB安全参考规范；
但是针对业务逻辑的安全风险同样由于其过度依赖业务的功能，不容易在传统的安全检查中发现和防御；我们建议在相关业务设计和上线阶段，与安全管理部一同对业务进行透彻地调查安全威胁、有针对性地设计相应的安全控制和方案。
同时，在业务运行阶段，业务负责人也需要了解自己业务目前面临的安全威胁、和相应的安全控制措施。
员工安全意识
员工安全意识规范
员工安全意识风险说明
在所有的信息安全事故中，目前最主要的来源还是员工自身安全意识不足导致，例如：
员工响应漏洞不及时，没有按照规定的时间对漏洞进行修复，导致漏洞被多次恶意利用；
员工利用工作之便进行营私舞弊活动；
员工将开发代码等敏感资料上传至Github、网盘等开放平台导致被公开；
员工访问了挂马的网站或点击了未知的邮件终端被植入木马，黑客以这个终端为跳板，可以远程对系统进行恶意操作；
其不仅仅会威胁到员工个人隐私信息的安全，如果涉及到公司的敏感数据，将会直接威胁到公司核心生产业务、用户资产等；如果数据被竞争对手获得，造成的损失更是不可估量；相关信息被媒体纰漏的话甚至面临退市、倒闭的风险；
员工安全意识风险参考案例
以下是员工缺乏安全意识引起的安全事件典型案例，其中案例一是比较典型的由于员工无意泄漏公司隐私信息造成的严重攻击:
chmcc员工安全意识薄弱导致入侵chmcc集团内网
由于某员工将用户信息配置文件误上传至Github，导致邮箱被登录，泄露其全部敏感信息，通过获得的信息对堡垒机、日志平台等可进一步操作，直接威胁整个集团数据和业务安全；
详见安全知识库：{_}http://study.chmcc.com/?p=29713_
小米员工安全意识薄弱导致公司大量数据库、服务器信息泄露
由于小米员工安全意识不足，对已公开的漏洞没有及时修复，导致通用密码被泄露，黑客通过在弃用数据库中找到密码，成功登录邮箱、VPN等，最终入侵小米内网，获得操作数据库、服务器权限；
详见安全知识库：{_}http://study.chmcc.com/?p=29713_
安全公司员工安全意识薄弱导致公司敏感信息泄露
黑客发送钓鱼邮件，导致员工点击了携带木马的文件，导致其邮箱用户名密码信息泄露，黑客通过邮件获取到的多个员工大量的敏感信息；
详见安全知识库：{_}http://study.chmcc.com/?p=29713_
员工安全意识风险防御规范
员工安全意识涉及物理安全意识、数据安全意识、口令安全意识、社会工程意识等多方面内容，员工日常行为规范严格按照《chmcc商城信息安全规范》的要求来执行；
对于员工使用互联网进行资料查询、工作交流时，需要严格按照《chmcc商城互联网操作指南》的要求来执行；
同时安全管理部也会在员工入职前、工作中定期组织安全意识培训学习，全面提升员工安全意识；
《chmcc商城互联网操作指南》见附件；
《chmcc商城信息安全规范》请至ERP中查看：
{_}http://v1.erp.chmcc.com/newhrm/download/xxaqgf8.pdf_ 