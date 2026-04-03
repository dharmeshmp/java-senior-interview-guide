# 10 - Java Web Development

Java powers the backend of millions of web applications through Servlet, JSP, and Spring.

---

## 🏗 Servlets & JSP
Servlets are the foundational Java classes that handle HTTP requests.

```java
@WebServlet("/hello")
public class HelloServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        response.setContentType("text/html");
        response.getWriter().println("<h1>Hello from Servlet!</h1>");
    }
}
```

## 🏗 REST API Design
Creating web services that use HTTP methods like `GET`, `POST`, `PUT`, `DELETE`.

```java
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api")
public class ApiController {

    @GetMapping("/greet")
    public String greet() {
        return "Hello REST!";
    }

    @PostMapping("/post")
    public String create(@RequestBody String data) {
        return "Post received: " + data;
    }
}
```

## 🏗 Spring MVC
Spring MVC provides a Model (M), View (V), and Controller (C) architecture for web applications.
- **Model**: Data objects.
- **View**: UI components (like Thymeleaf or JSP).
- **Controller**: Logic that handles requests and interacts with the model.

---
[⬅ Back to Roadmap](../README.md)
