# Modul 2 

## Single-Responsibility Principle
[Link zum Kapitel](https://fdiba-clean-code.github.io/module-2/single-responsibility-principle/)

Eine mögliche Lösung für das Refactoring besteht darin, den E-Mail-Code und den Datenbank-Code in separate, eigenständige Module aufzuteilen.
```java
public class UserService {
    private final EmailService emailService;
    private final UserRepository userRepository;

    public UserService(EmailService emailService, UserRepository userRepository) {
        this.emailService = emailService;
        this.userRepository = userRepository;
    }

    public void registerUser(User user) {
        userRepository.save(user);

        emailService.sendRegistrationEmail(user.getEmail());
    }
}
```

Sicherlich bedeutet SRP aber nicht, dass eine Klasse nur eine einzige Funktion ausführen soll. Code, der semantisch zusammengehört und nicht als eigenständiges Modul funktionieren kann, gehört in die Klasse.
Das ist was der Begriff Cohesion beschreibt.

```java
public class UserService {
    private final EmailService emailService;
    private final UserRepository userRepository;

    public UserService(EmailService emailService, UserRepository userRepository) {
        this.emailService = emailService;
        this.userRepository = userRepository;
    }

    public void registerUser(User user) {
      // ...
    }

    public void editUser (User user) {
      // ...
    }

    public void deregisterUser (User user) {
      // ...
    }
}
```

Der obige Code ist in Bezug auf das Coupling noch nicht ‚loosely coupled‘. Unsere UserService-Klasse bleibt weiterhin direkt mit den 
Klassen EmailService und UserRepository verbunden. Das wird besonders dann zum Problem, wenn wir den UserService testen wollen – wir müssten dann Instanzen der anderen beiden 
Klassen mitverwenden. Um ein ‚Loose Coupling‘ zu erreichen, können wir Dependency Inversion einsetzen, um diese Komponenten voneinander zu trennen.

Das können wir durch eine Abstraktion mithilfe von Interfaces erreichen. Wir können dann Instanzen jeder Klasse, die diese Interfaces implementiert, an den UserService übergeben.

```java
public interface NotificationProvider {
    void sendRegistrationNotification(String email);
}

public interface UserStorage {
    void save(User user);
}

public class UserService {
    private final NotificationProvider notificationProvider;
    private final UserStorage userStorage;

    public UserService(NotificationProvider np, UserStorage us) {
        this.notificationProvider = np;
        this.userStorage = us;
    }

    public void registerUser(User user) {
        userStorage.save(user);
        notificationProvider.sendRegistrationNotification(user.getEmail());
    }
}

public class SmtpEmailService implements NotificationProvider {
    private final String username;
    private final String password;
    private final Session session;

  public SmtpEmailService(String host, String port, String user, String pass) {
    // Constructor implementation
  }
  @Override
  public void sendRegistrationNotification(String email) {
    // Method implementation
  }
}


public class UserDataBase implements UserStorage {
    private final String connectionString;

    public UserDataBase(String connectionString) {
      // Constructor implementation
    }

    @Override
    public void save(User user) {
       // Method implementation
    }
}
```
Beachtet, dass wir die Instanzen als Parameter übergeben haben. Das nennt man Dependency Injection.

```java
public class Main {
    public static void main(String[] args) {
        NotificationProvider emailProvider = new SmtpEmailService(
            "smtp.gmail.com", 
            "587", 
            "app-auth@company.com", 
            "secure-password"
        );

        UserStorage database = new UserDataBase("jdbc:mysql://localhost:3306/my_app_db");

        UserService userService = new UserService(emailProvider, database);

        User newUser = new User("developer@example.com");

        userService.registerUser(newUser);
    }
}
```

## If-Inversion

## Exceptions

## Pure Funktionen
