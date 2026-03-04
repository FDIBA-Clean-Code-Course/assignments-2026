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

[Kapitel Refactoring durch Abstraktionen](https://fdiba-clean-code.github.io/module-2/refactoring/)
[Dependency Inversion Kapitel](https://fdiba-clean-code.github.io/module-2/testability/#dependency-inversion)

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
[Link zum Kapitel](https://fdiba-clean-code.github.io/module-2/if-inversion/)

Im fall von JS, wo wir ein dynamisches Typsystem haben, können wir duch If-Inversion "type narrowing" ausführen - wir können den Typ genau
präzesieren. Das ist für Primitiven unnötig aber wenn wir mit Objekten arbeiten, ist dieses besonders hilfreich)

```js
const isDesiredInObject = (objectToValidate) => {
    if(!objectToValidate) {
        return false;
    }
    
    if(typeof objectToValidate !== 'object') {
        return false;
    }

    // Arrays are objects in JS
    if (Array.isArray(objectToValidate)) {
            return false;
    }

    if(!('desired' in objectToValidate)) {
        return false;
    }

    return true;
}

if(isDesiredInObject(objectToValidate)){
    console.log(objectToValidate.desired);
}
```

## Exceptions
[Link zum Kapitel](https://fdiba-clean-code.github.io/module-2/exception-handling/)

Wir können zwischen Exceptions und Errors anhand ihrer Behebbarkeit unterscheiden – Errors führen normalerweise zu einem fatalen Zustand in unserer Anwendung, während Exceptions von unserem Code abgefangen werden sollten. In der Regel erfolgt diese Handhabung in try-catch-Blöcken.

In Bezug auf JS existiert nur der Typ Error, jedoch werden alle Errors als Exceptions behandelt. Je nach Umgebung können unbehandelte Exceptions zu einem fatalen Zustand führen oder auch nicht. Eine unbehandelte Exception in einem Event-Listener wird unsere Website nicht zum Absturz bringen, aber jede unbehandelte Exception in einer Node.js-Umgebung führt zum Programmabbruch. (Wir können durch Custom Errors einen Untreschied
zwischen Exceptions und Errors selbst machen).

Bei der Gestaltung unserer Fehlerbehandlung sollten wir darauf achten, welche Codestücke Exceptions oder Errors auslösen sollten oder nicht. Eine sumTwoNumbers-Funktion zum Beispiel sollte gar nichts auslösen. Beispiele, bei denen ein „throw“ erwartet wird, können asynchrone Operationen für das FE oder die Verbindung mit einer Browser-API wie IndexedDB sein, während dies für das BE I/O-Operationen, DB-Transaktionen usw. sind.

## Pure Funktionen
[Link zum Kapitel](https://fdiba-clean-code.github.io/module-2/testability/#pure-functions)

Pure Funktionen sollen zwei Bedingungen erfüllen - sie sollen deterministisch sein und keine Side Effects haben. Unter Side Effect versteht man I/O, Zustandsveränderungen, etc.

## Testen
Die häufigsten Typen von Tests sind drei - Unit-, Integration- und End-To-End- (E2E) Tests.

1. Unit-Tests testen eine kleine Funktionalität - am häufigsten eine kleine Funktion.
2. Integration-Tests testen wie mehrere Komponenten eines System zusammenarbeiten.
3. E2E-Tests testen unser ganzes System.

Wir betrachten zuerst die drei Grundlagen eines guten Unit-Tests.

### AAA-Regel
Unit-Tests sollen laut der Arrange-Act-Assert-Regel geordnet werden.

```js
function sum(a, b) {
  return a + b;
}

describe('Sum function test suite.', () => {

  test('adds 1 + 2 to equal 3', () => {
    // Arrange
    const a = 1;
    const b = 2;
    const expected = 3;

    // Act
    const result = sum(a, b);

    // Assert
    expect(result).toBe(expected);
  });
}
```

### Ein Testfall für ein Ergebnis

```js
// Anti-Pattern - das sind drei verschiedene Testfälle!!!
test('floating point operations', () => {
    // Arrange
    const zero = 0;
    const floatA = 0.1;
    const floatB = 0.2;

    // Act & Assert
    const zeroSum = sum(zero, zero);
    expect(zeroSum).toBe(0);

    const floatingPointSum = sum(floatA, floatB);
    expect(floatingPointSum).toBeCloseTo(0.3);

    const thirdSum = sum(10, 20);
    expect(thirdSum).not.toBeNull();
  });
```

### Tests sollen deterministisch sein

```js
function getGreeting() {
  const hour = new Date().getHours();
  if (hour < 12) return "Good morning";
  return "Good day";
}

describe('getGreeting test suite', () => {
    test('returns "Good morning"', () => {
      // Act
      const result = getGreeting();

      // Assert
      // Erfolgreich um 9 Uhr, unerfolgreich um 14 Uhr
      expect(result).toBe("Good morning"); 
    });

    // deterministisch
    test('returns "Good morning" when the time is 9 AM', () => {
      // Arrange
      const morningDate = new Date('2026-03-04T09:00:00');
    
      // Act
      const result = getGreeting(morningDate);
    
      // Assert
      expect(result).toBe("Good morning");
    });
})
```
