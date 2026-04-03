# 08 - JDBC (Java Database Connectivity)

JDBC is the Java API that connects to and interacts with relational databases using SQL.

---

## 🏗 Key Components of JDBC
- **DriverManager**: Manages the JDBC driver.
- **Connection**: Creates a session with the database.
- **Statement**: Executes a static SQL query.
- **PreparedStatement**: For parameterized SQL queries.
- **ResultSet**: Represents the database result set.

### 🖇 Basic Connection Setup
```java
import java.sql.*;

public class DatabaseAccess {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/mydb";
        String username = "root";
        String password = "password";

        try {
            Connection con = DriverManager.getConnection(url, username, password);
            System.out.println("Connection successful!");
            con.close();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

## 🏗 Execute Queries
Executing a `SELECT` statement and iterating over the result.

```java
public void executeSelect() throws SQLException {
    String sql = "SELECT * FROM users";
    Statement st = con.createStatement();
    ResultSet rs = st.executeQuery(sql);

    while (rs.next()) {
        System.out.println(rs.getInt(1) + " " + rs.getString(2));
    }
    rs.close();
    st.close();
}
```

## 🏎 PreparedStatements
For security and performance, always use `PreparedStatement` to avoid SQL Injection.

```java
public void executeInsert(int id, String name) throws SQLException {
    String query = "INSERT INTO users(id, name) VALUES(?, ?)";
    PreparedStatement ps = con.prepareStatement(query);
    ps.setInt(1, id);
    ps.setString(2, name);
    ps.executeUpdate();
    ps.close();
}
```

---
[⬅ Back to Roadmap](../README.md)
