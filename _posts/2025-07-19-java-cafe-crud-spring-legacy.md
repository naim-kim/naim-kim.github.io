---
layout: post
title: "Java Cafe CRUD Project: Spring MVC Legacy Conversion"
date: 2025-07-20
categories: [Java, Spring, Legacy]
tags: [SpringMVC, JSP, XML, Java]
---

## ☕ Java Café CRUD – Migrating to Spring Legacy (Spring MVC + JSP)

In this final part of the Café CRUD series, we’ll **convert our Spring Boot REST API into a classic Spring MVC (Legacy)** app using:

- Servlet configuration (`web.xml`)
- Spring XML beans (`spring-servlet.xml`)
- JSP-based views
- In-memory DAO instead of Spring Data JPA

➡️ _Missed the previous parts? Start here:_

- 🔹 [Part 1: CLI Version](./2025-07-18-java-mini-crud-project-cafe-menu-management.html)
- 🔹 [Part 2: Spring Boot REST API](./2025-07-19-java-cafe-menu-spring-boot.html)

---

### 📌 What Changes in Spring Legacy?

| Feature         | Spring Boot                                         | Spring Legacy                    |
| --------------- | --------------------------------------------------- | -------------------------------- |
| App config      | `@SpringBootApplication` + `application.properties` | `web.xml` + `spring-servlet.xml` |
| View resolver   | Auto-configured                                     | Manually defined in XML          |
| Template engine | Thymeleaf or REST                                   | JSP with JSTL                    |
| Bean wiring     | `@Component`, `@Service`, `@Repository`             | XML `<bean>` or component-scan   |
| API style       | RESTful JSON                                        | Server-rendered HTML via JSP     |

---

### 🛠️ Tech Stack

- Java 17
- Spring Framework (5.x)
- JSP + JSTL
- Tomcat (external or embedded)
- Maven

---

### 📁 Project Structure

```

src/main/java/com/example/menu/
├── controller/
│   └── MenuController.java
├── model/
│   └── MenuItem.java
├── dao/
│   └── MenuDAO.java
├── service/
│   └── MenuService.java

src/main/webapp/
├── WEB-INF/
│   ├── views/
│   │   ├── index.jsp
│   │   └── form.jsp
│   ├── web.xml
│   └── spring-servlet.xml

```

---

### 🧾 Config Files

#### `web.xml`

```xml
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee" version="3.1">
    <display-name>CafeMenuLegacy</display-name>

    <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>/WEB-INF/spring-servlet.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```

#### `spring-servlet.xml`

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
           http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context
           http://www.springframework.org/schema/context/spring-context.xsd
           http://www.springframework.org/schema/mvc
           http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <context:component-scan base-package="com.example.menu"/>
    <mvc:annotation-driven/>

    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/views/" />
        <property name="suffix" value=".jsp" />
    </bean>
</beans>
```

---

### 🧱 Model + DAO

#### `MenuItem.java`

```java
public class MenuItem {
    private Long id;
    private String name;
    private int price;
    private String category;

    // Getters/Setters
}
```

#### `MenuDAO.java`

```java
public class MenuDAO {
    private static List<MenuItem> list = new ArrayList<>();
    private static Long counter = 1L;

    public List<MenuItem> findAll() { return list; }

    public void save(MenuItem item) {
        item.setId(counter++);
        list.add(item);
    }

    public void update(MenuItem item) {
        delete(item.getId());
        list.add(item);
    }

    public void delete(Long id) {
        list.removeIf(i -> i.getId().equals(id));
    }

    public MenuItem findById(Long id) {
        return list.stream().filter(i -> i.getId().equals(id)).findFirst().orElse(null);
    }
}
```

---

### 💼 Controller + Service

#### `MenuService.java`

```java
public class MenuService {
    private final MenuDAO dao = new MenuDAO();

    public List<MenuItem> findAll() { return dao.findAll(); }
    public void save(MenuItem item) { if (item.getId() == null) dao.save(item); else dao.update(item); }
    public MenuItem findById(Long id) { return dao.findById(id); }
    public void delete(Long id) { dao.delete(id); }
}
```

#### `MenuController.java`

```java
@Controller
public class MenuController {
    private final MenuService service = new MenuService();

    @GetMapping("/")
    public String index(Model model) {
        model.addAttribute("menus", service.findAll());
        return "index";
    }

    @GetMapping("/add")
    public String form(Model model) {
        model.addAttribute("menu", new MenuItem());
        return "form";
    }

    @PostMapping("/save")
    public String save(@ModelAttribute MenuItem item) {
        service.save(item);
        return "redirect:/";
    }

    @GetMapping("/edit")
    public String edit(@RequestParam Long id, Model model) {
        model.addAttribute("menu", service.findById(id));
        return "form";
    }

    @GetMapping("/delete")
    public String delete(@RequestParam Long id) {
        service.delete(id);
        return "redirect:/";
    }
}
```

---

### 🖥️ JSP Views

#### `index.jsp`

```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<h2>카페 메뉴 목록</h2>
<a href="add">+ 새 메뉴 추가</a>
<table border="1">
    <tr><th>ID</th><th>이름</th><th>가격</th><th>카테고리</th><th>수정/삭제</th></tr>
    <c:forEach var="m" items="${menus}">
        <tr>
            <td>${m.id}</td>
            <td>${m.name}</td>
            <td>${m.price}</td>
            <td>${m.category}</td>
            <td>
                <a href="edit?id=${m.id}">✏️</a>
                <a href="delete?id=${m.id}">🗑️</a>
            </td>
        </tr>
    </c:forEach>
</table>
```

#### `form.jsp`

```jsp
<h2>메뉴 ${menu.id == null ? "추가" : "수정"}</h2>
<form action="save" method="post">
    <input type="hidden" name="id" value="${menu.id}" />
    이름: <input type="text" name="name" value="${menu.name}" /><br/>
    가격: <input type="text" name="price" value="${menu.price}" /><br/>
    카테고리: <input type="text" name="category" value="${menu.category}" /><br/>
    <input type="submit" value="저장" />
</form>
```

---
