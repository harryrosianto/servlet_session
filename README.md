Manajemen sesi di JAVA aplikasi web dapat dilakukan dengan berbagai cara, termasuk menggunakan cookies, hidden form fields, URL rewriting, dan objek `HttpSession`. Berikut adalah penjelasan masing-masing metode beserta contoh kodingannya.

### 1. Cookies

**Cookies** adalah data kecil yang dikirim oleh server dan disimpan di sisi klien (browser). Cookies sering digunakan untuk melacak sesi pengguna.

#### Contoh Cookies dalam Servlet

**Menyimpan dan Mengambil Cookies:**

#### `SetCookieServlet.java`
```java
import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet("/setCookie")
public class SetCookieServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        Cookie userCookie = new Cookie("username", "JohnDoe");
        userCookie.setMaxAge(60*60*24); // 1 day
        response.addCookie(userCookie);
        response.getWriter().println("Cookie set successfully");
    }
}
```

#### `GetCookieServlet.java`
```java
import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet("/getCookie")
public class GetCookieServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        Cookie[] cookies = request.getCookies();
        if (cookies != null) {
            for (Cookie cookie : cookies) {
                if ("username".equals(cookie.getName())) {
                    response.getWriter().println("Hello, " + cookie.getValue());
                }
            }
        }
    }
}
```

### 2. Hidden Form Fields

**Hidden Form Fields** adalah bidang yang tidak terlihat oleh pengguna dan dapat digunakan untuk menyimpan data sesi dalam formulir HTML.

#### Contoh Hidden Form Fields dalam JSP

**Mengirimkan Data dengan Hidden Form Fields:**

#### `form.jsp`
```jsp
<!DOCTYPE html>
<html>
<head>
    <title>Form</title>
</head>
<body>
    <form action="processForm" method="post">
        <input type="hidden" name="sessionId" value="12345" />
        <input type="submit" value="Submit" />
    </form>
</body>
</html>
```

#### `ProcessFormServlet.java`
```java
import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet("/processForm")
public class ProcessFormServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        String sessionId = request.getParameter("sessionId");
        response.getWriter().println("Session ID: " + sessionId);
    }
}
```

### 3. URL Rewriting

**URL Rewriting** adalah teknik menambahkan informasi sesi ke URL sehingga informasi ini dapat disimpan antara permintaan.

#### Contoh URL Rewriting dalam Servlet

**Menambahkan Data Sesi ke URL:**

#### `URLRewriteServlet.java`
```java
import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

@WebServlet("/urlRewrite")
public class URLRewriteServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        HttpSession session = request.getSession();
        session.setAttribute("username", "JohnDoe");
        String url = response.encodeURL("welcome");
        response.sendRedirect(url);
    }
}
```

### 4. HttpSession

**HttpSession** adalah API bawaan dalam servlet yang menyediakan cara untuk menyimpan data sesi di sisi server.

#### Contoh HttpSession dalam Servlet

**Menggunakan HttpSession untuk Menyimpan dan Mengambil Data:**

#### `LoginSessionServlet.java`
```java
import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

@WebServlet("/loginSession")
public class LoginSessionServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        String username = request.getParameter("username");
        HttpSession session = request.getSession();
        session.setAttribute("username", username);
        response.sendRedirect("welcomeSession");
    }
}
```

#### `WelcomeSessionServlet.java`
```java
import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

@WebServlet("/welcomeSession")
public class WelcomeSessionServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        HttpSession session = request.getSession(false);
        if (session != null) {
            String username = (String) session.getAttribute("username");
            response.getWriter().println("Welcome, " + username);
        } else {
            response.sendRedirect("login.jsp");
        }
    }
}
```

Metode-metode ini memberikan berbagai cara untuk mengelola sesi dalam aplikasi web, masing-masing dengan kelebihan dan kekurangannya sendiri tergantung pada kebutuhan aplikasi.

Kode XML di bawah ini adalah konfigurasi sesi dalam file `web.xml` untuk aplikasi web berbasis servlet di Java.

```xml
<session-config>
  <session-timeout>20</session-timeout>
</session-config>
```

### Penjelasan:

1. **`<session-config>`**:
   - Elemen ini digunakan untuk mengonfigurasi pengaturan sesi untuk aplikasi web.
   
2. **`<session-timeout>`**:
   - Elemen ini digunakan untuk mengatur waktu timeout sesi dalam menit. Waktu timeout menentukan berapa lama sesi harus tidak aktif sebelum server mengakhirinya secara otomatis.
   
### Dalam Konteks:

- **`<session-timeout>20</session-timeout>`**:
  - Ini berarti bahwa sesi pengguna akan otomatis diakhiri setelah 20 menit tidak ada aktivitas. Jika pengguna tidak mengirimkan permintaan ke server dalam periode ini, sesi akan dianggap tidak aktif dan dihapus.
  
### Kapan Ini Digunakan?

Pengaturan ini biasanya digunakan untuk meningkatkan keamanan dan efisiensi sumber daya:

- **Keamanan**: Membatasi waktu sesi dapat membantu mencegah sesi yang terbengkalai digunakan oleh orang yang tidak berwenang.
- **Efisiensi Sumber Daya**: Mengakhiri sesi yang tidak aktif dapat membantu mengurangi penggunaan memori dan sumber daya server.

### Contoh Konfigurasi `web.xml` dengan `session-config`:

```xml
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee 
                             http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <session-config>
        <session-timeout>20</session-timeout>
    </session-config>

    <!-- Other configurations like servlets, filters, listeners -->
</web-app>
```

Dalam contoh di atas, aplikasi web diatur sehingga setiap sesi yang tidak aktif selama 20 menit akan otomatis berakhir. Konfigurasi ini membantu memastikan bahwa sumber daya server tidak digunakan oleh sesi yang tidak aktif untuk waktu yang lama dan meningkatkan keamanan aplikasi.
