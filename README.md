# Demo Spring MVC (without maven)

First, if we use intelIJ community (maybe Ultimate), we need to install plugin Smart Tomcat and add Configuration point to our tomcat folder.

## Initialize project
### Create empty project
First, we maybe need create empty project. Then, we unmark src folder and create foler java (mark as source code), resources (mark as resources) and webapp/WEB-INF.
```
_src (unmark if needed)
|__main
    |__java
    |__webapp
        |__WEB-INF
            |__view
```

Move libs and add in folder libs.

### Add config file
We add /src/main/webapp/WEB-INF/spring-mvc-demo-servlet.xml to configure Spring Dispatcher Servlet and add URL mapping for them:
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xsi:schemaLocation="
		http://www.springframework.org/schema/beans
    	http://www.springframework.org/schema/beans/spring-beans.xsd
    	http://www.springframework.org/schema/context
    	http://www.springframework.org/schema/context/spring-context.xsd
    	http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd">

	<!-- Step 3: Add support for component scanning -->
	<context:component-scan base-package="com.luv2code.springdemo" />

	<!-- Step 4: Add support for conversion, formatting and validation support -->
	<mvc:annotation-driven/>

	<!-- Step 5: Define Spring MVC view resolver -->
	<bean
		class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<property name="prefix" value="/WEB-INF/view/" />
		<property name="suffix" value=".jsp" />
	</bean>

</beans>
```
Then, we add We add /src/main/webapp/WEB-INF/spring-mvc-demo-servlet to configure bean:
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xsi:schemaLocation="
		http://www.springframework.org/schema/beans
    	http://www.springframework.org/schema/beans/spring-beans.xsd
    	http://www.springframework.org/schema/context
    	http://www.springframework.org/schema/context/spring-context.xsd
    	http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd">

	<!-- Step 3: Add support for component scanning -->
	<context:component-scan base-package="com.luv2code.springdemo" />

	<!-- Step 4: Add support for conversion, formatting and validation support -->
	<mvc:annotation-driven/>

	<!-- Step 5: Define Spring MVC view resolver -->
	<bean
		class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<property name="prefix" value="/WEB-INF/view/" />
		<property name="suffix" value=".jsp" />
	</bean>

</beans>
```

## Add Controller Class
@Controller' inheritance @Component supporting for scan bean.
```
package com.sang;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class HomeController {

    @RequestMapping("/")
    public String showHomePage(){
        return "main-menu";
    }

}
```

## Add page in view
When add page, we must take care of servlet bean xml config:
```
...
	<bean
		class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<property name="prefix" value="/WEB-INF/view/" />
		<property name="suffix" value=".jsp" />
	</bean>
...
```

It means we must create page with type .jsp in /WEB-INF/view/ and the name of file will return in method of controller if we want to return a page like above (ex: main-menu will be resolved as /WEB-INF/view/main-menu.jsp)

## Parse data from controller to page and page to controller using Model
### Page to controller
First, in page we will create <form></form> tag and <input name="" /> tag to input value and it will parse to request parameter when we submit:

```
<!DOCTYPE html>
<html>
<head>
    <title>Hello World - Input Form</title>
</head>
<body>
    <form action="processForm" method="GET">
        <input type="text" name="studentName" placeholder="What's your name ?" />
        <input type="submit" />
    </form>
</body>
</html>
```

Then, in method process that form (ex: processForm) we add 2 param HttpServletRequest and Model. We will get value studentName by request.getParameter("...");

## From controller to page

In method processing form action:
```
...
    @RequestMapping("/processForm")
    public String processForm(HttpServletRequest request, Model model) {
        String name = request.getParameter("studentName");

        name = name.toUpperCase();

        model.addAttribute("message", "Yo! " + name);

        return "helloworld";
    }
...
```

and then in view, we can use ${attibute-name} get value of mode:
```
<!DOCTYPE html>
<html>
<head>
    <title>Hello World - Input Form</title>
</head>
<body>
    Hello World of Spring !

    <br><br>

    Student name: ${message}
</body>
</html>
```

## Using static resources in Spring MVC

In order to use static resources like Images, CSS or javascript, we config application XML like this:
```
...
<mvc:resources mapping="/resources/**" location="/resources/"></mvc:resources> 
...
```

And create folder resources based on config:
```
_src (unmark if needed)
|__main
    |__java
    |__resources
    |__webapp
        |__WEB-INF
        |    |__view
        |__resources    
```

To get static file, we use src and ${} like this:
```
<img src="${pageContext.request.contextPath}/resources/images/spring-logo.png">
```