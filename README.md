# 토비의 스프링3 스프링 적용까지

## Code

jdbcContextWithStatementStrategy

```java
public void jdbcContextWithStatementStrategy(StatementStrategy stmt) throws SQLException {
        Connection c = null;
        PreparedStatement ps = null;
        try {
            c = cm.makeConnection();
            ps = stmt.makePreparedStatement(c);
            ps.executeUpdate();
        } catch (SQLException e) {
            throw e;
        } finally {
            if (ps != null) {
                try {
                    ps.close();
                } catch (SQLException e) {
                }
            }
            if (c != null) {
                try {
                    c.close();
                } catch (SQLException e) {
                }
            }
        }
    }
```

add()

```java
public void add(User user) throws SQLException {
        AddStrategy addStrategy = new AddStrategy(user);
        jdbcContextWithStatementStrategy(addStrategy);
    }
```

build.gradle

```.gradle
dependencies {

    implementation 'org.springframework.boot:spring-boot-starter-test:2.7.4'
    implementation 'org.springframework.boot:spring-boot-starter-jdbc:2.7.4'
    implementation 'mysql:mysql-connector-java:8.0.30'
    testImplementation 'org.junit.jupiter:junit-jupiter-api:5.8.1'
    testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine:5.8.1'
}
```

UserDaoFactory.java

```java
@Configurable
public class UserDaoFactory {

    @Bean
    public UserDao awsUserDao() {
        return new UserDao(new AwsConnectionMaker());
    }
}
```

ConnectionMaker.java

```java
import java.sql.Connection;
import java.sql.SQLException;

public interface ConnectionMaker {
    Connection makeConnection() throws SQLException;
}
```

AwsConnectionMaker.java
```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.util.Map;

public class AwsConnectionMaker implements ConnectionMaker {

    @Override
    public Connection makeConnection() throws SQLException {
        Map<String, String> env = System.getenv();
        // DB접속 (ex sql workbeanch실행)
        Connection c = DriverManager.getConnection(env.get("DB_HOST"),
                env.get("DB_USER"), env.get("DB_PASSWORD"));
        return c;
    }
}
```


