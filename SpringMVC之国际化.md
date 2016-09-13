##一.基于浏览器请求的国际化实现

###1.首先在applicationContext.xml文件添加的内容如下
```
<bean id="messageSource" class="org.springframework.context.support.ResourceBundleMessageSource">
    <!-- 国际化信息所在的文件名 -->                     
    <property name="basename" value="messages" />   
    <!-- 如果在国际化资源文件中找不到对应代码的信息，就用这个代码作为名称  -->               
    <property name="useCodeAsDefaultMessage" value="true" /> 
	<!-- 编码格式 -->
    <property name="defaultEncoding" value="UTF-8"/>	
</bean>
```
###2.配置controller语言选择
```
@Controller
public class I18nController {

	@Autowired
	private LocaleResolver localeResolver;
	@RequestMapping(value = "ChangeI18n/{i18nType}")
	public String ChangeI18n(@PathVariable("i18nType") String i18nType, HttpServletRequest req, HttpServletResponse resp) throws Exception {

		Locale currentLocale = null;
		try {
			if (i18nType.equals("cn")) {
				currentLocale = new Locale("zh", "CN");
			} else if (i18nType.equals("us")) {
				currentLocale = new Locale("en", "US");
			}
			localeResolver.setLocale(req, resp, currentLocale);
		
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			return "/index";
		}

	}
}
```
3.index.jsp语言选择设置选项
```
<%@ page language="java" import="java.util.*" pageEncoding="utf-8"%>
<%@ taglib uri="http://www.springframework.org/tags"  prefix="spring" %>

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
  <head>
  </head>
  <body>
  
      <a href="${pageContext.request.contextPath}/ChangeI18n/cn.do">中文</a>  
      <a href="${pageContext.request.contextPath}/ChangeI18n/us.do">英文</a>       <br> <br> <br> <br> 
      
      <spring:message code="welcome"/>
      
      <a href="${pageContext.request.contextPath}/in18n.do" target="_blank">国际化</a>
  </body>
</html>
```
##二.基于Session的国际化实现
###1.在applicationContext.xml文件添加的内容如下
```
<mvc:interceptors>  
    <!-- 国际化操作拦截器 如果采用基于（请求/Session/Cookie）则必需配置 --> 
    <bean class="org.springframework.web.servlet.i18n.LocaleChangeInterceptor" />  
</mvc:interceptors>  

<bean id="localeResolver" class="org.springframework.web.servlet.i18n.SessionLocaleResolver" />
```
###三.基于Cookie的国际化实现：
把实现第二种方法时在项目的applicationContext.xml文件中添加的
```
<bean id="localeResolver" class="org.springframework.web.servlet.i18n.SessionLocaleResolver" />
注释掉，并添加以下内容：

<bean id="localeResolver" class="org.springframework.web.servlet.i18n.CookieLocaleResolver" />
```
关于<bean id="localeResolver" class="org.springframework.web.servlet.i18n.CookieLocaleResolver" />3个属性的说明(可以都不设置而用其默认值)
```
<bean id="localeResolver" class="org.springframework.web.servlet.i18n.CookieLocaleResolver">
    <!-- 设置cookieName名称，可以根据名称通过js来修改设置，也可以像上面演示的那样修改设置，默认的名称为 类名+LOCALE（即：org.springframework.web.servlet.i18n.CookieLocaleResolver.LOCALE-->
    <property name="cookieName" value="lang"/>
    <!-- 设置最大有效时间，如果是-1，则不存储，浏览器关闭后即失效，默认为Integer.MAX_INT-->
    <property name="cookieMaxAge" value="100000">
    <!-- 设置cookie可见的地址，默认是“/”即对网站所有地址都是可见的，如果设为其它地址，则只有该地址或其后的地址才可见-->
    <property name="cookiePath" value="/">
</bean>
```
四.基于URL请求的国际化的实现
###首先添加一个类，内容如下
```
import java.util.Locale;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import org.springframework.web.servlet.DispatcherServlet;
import org.springframework.web.servlet.LocaleResolver;

public class MyAcceptHeaderLocaleResolver extends AcceptHeaderLocaleResolver {

    private Locale myLocal;

    public Locale resolveLocale(HttpServletRequest request) {
        return myLocal;
    } 

    public void setLocale(HttpServletRequest request, HttpServletResponse response, Locale locale) {
        myLocal = locale;
    }
  
}
```

然后把实现第二种方法时在项目的applicationContext.xml文件中添加的
```
<bean id="localeResolver" class="org.springframework.web.servlet.i18n.SessionLocaleResolver" />
```

注释掉，并添加以下内容：

```
<bean id="localeResolver" class="xx.xxx.xxx.MyAcceptHeaderLocaleResolver"/>
```
“xx.xxx.xxx”是刚才添加的MyAcceptHeaderLocaleResolver 类所在的包名。

如果多种语言，修改默认语言可以如下
```
/**
 * 国际化解析验证错误信息
 *
 * @param result
 * @param errorVoList
 */
public static void analyzeErrors(BindingResult result, ResponseEntity response, HttpServletRequest request) {
	// 获取所有验证错误信息
	List<ObjectError> errorList = result.getAllErrors();
	if (!errorList.isEmpty()) {
		// 解析组装
		String message = errorList.get(0).getDefaultMessage();
		// 从后台代码获取国际化信息
		RequestContext requestContext = new RequestContext(request);
		//获得国际化语言
		Locale locate=requestContext.getLocale();
		//判断语言切换
		switch (locate.getLanguage()) {
			case "zh1": break;
			//设置默认语言
			default:
				locate.setDefault(Locale.US);
				break;
		}
		response.setRtMessage(requestContext.getMessage(message));

		response.setRtCode(MsgConstants.PARAM_ERROR_CODE);
	}
}
```

保存之后就可以在浏览器就可以模拟了，语言根据浏览器语言设置

本文内容请参考<a href="http://www.cnblogs.com/liukemng/p/3750117.html">点我</a>




















