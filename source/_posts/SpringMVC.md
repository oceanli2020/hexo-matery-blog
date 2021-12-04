---
title: SpringMVC
top: true
cover: true
toc: true
mathjax: true
date: 2021-03-14 19:29:24
password:
summary:
tags:
  - Java
  - SpringMVC
categories:
  - Java
---

## SpringMVC

SpringMVC 架构

![SpringMVC](https://i.loli.net/2021/03/14/szDjE4uThPURAak.jpg)

在 Controller 包下创建类，在类中定义方法，以注解的方式与 url 建立映射

常用注解：

```java
@controller // Controller类 单例
@RequestMapping // 与url建立映射关系
@ResPonseBody // 自动将对象转化为JSON字符串
```

Restful 风格：比较流行的软件架构风格、设计风格，提供了一组设计原则和约束条件

首先，Restful 是面向资源的，URL 的设计只需要暴露资源，对资源的操作与它无关，操作则是通过 HTTP 动词来体现，URL 中只会出现资源对应的名词，例如**http://learn/api/v1/course**

其次，Restful 是基于 HTTP 的，使用了 HTTP 动词来体现资源的操作，其中，GET 是从服务器取出资源，POST 是在服务器创建一个资源吗，PUT 是为服务器更新资源，DELETE 是为服务器删除资源

最后，在实际开发中，我们还规定了固定的返回值统一格式，规定统一的全局状态码，要在正确的场景中返回详细的错误状态

```json
{
    "code":200,
    "message":"成功"
    "data":{"userName":"root","age":20}
}
```

```java
SUCCESS(200, "成功"),
FAIL(-200, "失败"),
USER_NOT_FOUND(-1002, "用户不存在"),
USER_PASS_EDIT_FAIL(-1008, "密码不正确"),
NO_RESOURCES(-4001, "资源不存在");
```

常用注解：

```java
@PathVariable  // 用于将请求URL的模板变量映射到功能处理方法的参数上
```

SpringMVC 的底层原理：url ----- 类/方法建立映射关系，然后通过 DispatchServlet（控制器）、HandlerMapping（键值对存储 url 映射）进行分发，执行完具体方法后，再返回给 DispatchServlet

映射有两种：

url --------> 类的全限定名
url --------> 类的全限定名、方法（方法名、返回类型、形参名、形参类型的全限定名）

**造轮子自己实现一个 SpingMVC**：

两种映射关系

```xml
<bean url="..." class="..."></bean>
```

```xml
<bean class="...">
    <method url="..." name="..." action="json/jsp">
        <param name="...">java.lang.String</param>
        <param name="...">java.lang.Integer</param>
    </method>
</bean>
```

通过 SAX 解析器解析存储映射关系的 XML 文件，将映射关系存储到 HandleMapping 的两个 Map 中

```java
// 利用HashMap键值对的方式存储url映射
public class HandleMapping {
	//第一种映射
	public static HashMap<String,String> beanNameMapping = new HashMap<String,String>();
	//第二种映射
	public static HashMap<String,MethodInfo> annotaionMethodMapping = new HashMap<String,MethodInfo>();
}
```

```java
//通过一个po类MethodInfo对第二种映射中的方法进行封装
public class MethodInfo {
	private String url;
	private String className;
	private String methodName;
	private String action;
	private List<String> paramNames;
	private List<String> paramTypes;
}
```

创建控制器 ControllerServlet，来分发请求并执行具体方法

```java
// 定义接口MyController
public interface MyController {
    public abstract void doGet(HttpServletRequest request, HttpServletResponse response);
    public abstract void doPost(HttpServletRequest request, HttpServletResponse response);
}
```

```java
public class ControllerServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;

    public ControllerServlet() {
        super();
    }

    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String url = request.getRequestURI();
        url = url.substring("/MySpringMVCWork".length(), url.length());
        // 创建HandleMapping这个类通过hashmap对两种映射关系进行封装
        if (HandleMapping.beanNameMapping.get(url) != null)// 不同的映射处理方式不同
        {
            String className = HandleMapping.beanNameMapping.get(url);
            try {
                Class clz = Class.forName(className);// 类的全限定名得到字节码对象
                Object object = clz.newInstance();// 字节码对象得到具体的对象
                if (!(object instanceof MyController))// 判断处理类有没有实现定义的接口
                    // 如果没有，则抛出自定义异常
                    throw new MyMvcException("处理类没有实现MyController接口");
                MyController controller = (MyController) object;// 接口回调
                if (request.getMethod().equals("GET"))// 判断请求方式
                {
                    controller.doGet(request, response);
                } else {
                    controller.doPost(request, response);
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        } else {
            MethodInfo methodinfo = HandleMapping.annotaionMethodMapping.get(url);
            if (methodinfo == null) {
                response.setStatus(404);// url未找到映射，404
                return;
            }
            try {
                // 对MethodInfo进行拆解
                // 类的全限定名得到字节码对象
                Class clz = Class.forName(methodinfo.getClassName());
                // 字节码对象得到具体的对象
                Object object = clz.newInstance();
                // 形参类型的全限定名List
                List<String> paramTypeslist = methodinfo.getParamTypes();
                // 形参名List
                List<String> paramNameslist = methodinfo.getParamNames();
                Class[] paramTypes = new Class[methodinfo.getParamTypes().size()];
                String[] paramNames = new String[methodinfo.getParamNames().size()];
                for (int i = 0; i < paramNames.length; i++) {
                    // 形参类型的全限定名数组
                    paramTypes[i] = Class.forName(paramTypeslist.get(i));
                    // 形参名数组
                    paramNames[i] = paramNameslist.get(i);
                }
                // 通过反射机制可以得到方法
                Method method = clz.getDeclaredMethod(methodinfo.getMethodName(), paramTypes);
                // 创建一个辅助类RequestDataHandler以形参的类型对实参的值进行转化
                Object[] values = RequestDataHandler.handlerRequest(request, response, paramTypes, paramNames);
                Object result = null;
                // 执行具体的方法
                if (methodinfo.getParamNames() == null)
                    result = method.invoke(object, null);
                else
                    result = method.invoke(object, values);// 得到待处理的结果
                if (methodinfo.getAction().equals("json"))// 通过返回类型来处理结果
                {
                    PrintWriter out = response.getWriter();
                    JSONObject json = null;
                    try {
                        json = JSONObject.fromObject(result);
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                    if (json != null)
                        out.write(json.toString());
                    else {
                        JSONArray json2 = JSONArray.fromObject(result);
                        out.write(json2.toString());
                    }
                } else {
                    request.getRequestDispatcher((String) result).forward(request, response);
                }
            } catch (Exception e) {
                e.printStackTrace();
            }

        }

    }


}

```
