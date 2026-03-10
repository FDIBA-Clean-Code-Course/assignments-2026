# Einführung in JavaScript Teil 2

## Asynchrone JS

JS verfügt über einen bestimmten Typ von Objekt, das uns erlaubt, mit asynchronem Code zu arbeiten. Wie schon erwähnt, ist JS single-threaded, das heißt, es kann im Moment nur eine Operation ausgeführt werden.

Diese speziellen Objekte sind Promises – sie erlauben uns, einen Platzhalter für einen asynchronen Wert zu erstellen (z. B. das Ergebnis einer asynchronen Abfrage an eine API). Promises haben ein Statusfeld und ein Wertfeld. Sobald der Status erfüllt ist, wird das Wertfeld befüllt. Wenn ein Fehler auftritt, wird ein Fehlerfeld befüllt.

Es ist jedoch unklar, wann und ob der Erfüllungs-Status eintritt. Deshalb implementieren Promises eine then-Funktion – dieser kann eine Callback-Funktion (oder mehrere durch then-Verkettung) übergeben werden, die sofort ausgeführt wird, wenn das Wertfeld bereit ist.

```js
fetch('https://api.example.com/user/1')
  .then(response => response.json()) 
  .then(user => {
    console.log("User found:", user.name);
    return fetch(`https://api.example.com/posts/${user.id}`);
  })
  .then(postResponse => postResponse.json())
  .catch(error => {
    console.error("An error occurred in the chain:", error);
  });
```

Was passiert, wenn wir zwei abhängige Promises haben? Dann können wir die zweite mit der ersten verketten. Das führt jedoch zu einem Code-Zustand, der schwer lesbar ist. Deshalb wurde eine neue Methode für die Promise-Behandlung eingeführt – nämlich async / await.

```js
getUser(1, (user) => {
  getProfile(user.id, (profile) => {
    getPosts(profile.id, (posts) => {
      updateUI(posts, (result) => {
        console.log("Finally finished updating!");
        // This keeps growing to the right...
      }, (err) => console.error(err));
    }, (err) => console.error(err));
  }, (err) => console.error(err));
}, (err) => console.error(err));
```

Das Schlüsselwort await kann nur in Funktionen verwendet werden, die mit async markiert sind. Wenn JS dieses Schlüsselwort liest, wird die Ausführung dieser Funktion "pausiert", bis sich der Status ändert. Während dieser Zeit kann JS Event-Listener oder andere Funktionen verarbeiten. Diesen Prozess nennt man den Event-Loop.

```js
async function fetchUserData(userId) {
  try {
    console.log("Fetching data...");
    
    const response = await fetch(`https://jsonplaceholder.typicode.com/users/${userId}`);
    
    if (!response.ok) {
      throw new Error(`HTTP error! Status: ${response.status}`);
    }

    const data = await response.json();
    console.log("User Name:", data.name);
    return data;

  } catch (error) {
    console.error("Failed to fetch user:", error.message);
  } finally {
    console.log("Operation complete.");
  }
}

fetchUserData(1);
```

## OOP in JavaScript

JavaScript verfügt auch über die Möglichkeit, traditionelle OOP-Klassen zu erstellen und Objekte daraus zu instanziieren.

Die Konstruktor-Methode wird verwendet, um ein Objekt zu erstellen. Diese Methode wird aufgerufen, wenn wir das new-Schlüsselwort verwenden, um Objekte zu instanziieren.

```js
class Product {
  constructor(name, price, stock) {
    this.name = name;
    this.price = price;
    this.stock = stock;
  }

  // Method to update stock after a sale
  processSale(quantity) {
    if (quantity <= this.stock) {
      this.stock -= quantity;
      return `Sold ${quantity} ${this.name}(s). Remaining stock: ${this.stock}`;
    }
    return `Insufficient stock for ${this.name}!`;
  }
}

// Usage
const laptop = new Product('Laptop', 1200, 10);
console.log(laptop.processSale(2));
```

In JavaScript können Klassen mittels des extends-Schlüsselworts von anderen Klassen erben. Dabei muss im Konstruktor der Kind-Klasse zwingend der Konstruktor der Elternklasse via super() aufgerufen werden, bevor auf this zugegriffen werden kann. Zudem ermöglicht Vererbung das Überschreiben von Methoden, um spezifisches Verhalten zu definieren, während man weiterhin auf die Logik der Elternklasse zugreifen kann.

```js
class Vehicle {
  constructor(brand, speed) {
    this.brand = brand;
    this.speed = speed;
  }

  accelerate() {
    this.speed += 10;
    console.log(`${this.brand} is now going ${this.speed} km/h.`);
  }
}

// Subclass (Child) inheriting from Vehicle
class ElectricCar extends Vehicle {
  constructor(brand, speed, batteryLevel) {
    // 'super' calls the constructor of the Parent class (Vehicle)
    super(brand, speed); 
    this.batteryLevel = batteryLevel;
  }

  // A specific method only for ElectricCar
  charge() {
    this.batteryLevel = 100;
    console.log(`${this.brand} is now fully charged.`);
  }
}

const myTesla = new ElectricCar('Tesla', 0, 50);
myTesla.accelerate(); // "Tesla is now going 10 km/h."
myTesla.charge();     // "Tesla is now fully charged."
```

Mehr über OOP in JS hier --> [MDN](https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Advanced_JavaScript_objects/Classes_in_JavaScript)

## TypeScript

TypeScript ist ein Superset von JS, was bedeutet, dass jeder Code, der in JS valide ist, auch in TS valide ist. TypeScript bietet ein statisches Typsystem an. Das erlaubt Entwicklern, sicherer mit Typen zu arbeiten.

Die gesamte Validierung erfolgt nur während der Entwicklung. TypeScript wird zu JavaScript kompiliert, bevor es in den Browser geladen wird. Deshalb werden Laufzeit-Typfehler (Runtime-Errors) von TS nicht verhindert.

TypeScript bietet uns die primitiven Typen string, number, boolean usw. an. Arrays werden mit [] definiert. Funktionen könne auch typisiert werden.

```ts
const myNumber: number = 5;

const myString: string = 'hello';

// Signifies an array 
const myNumberArray: number[] = [1, 2, 3, 4, 5, 6];

// TS can be used in function as well
const isActive = (activeFlag: number): boolean => {
    if activeFlag === '0' return false;
    if activeFlaf === '1' return true;
    return false; // any other return type besides boolean will result in a type error
}
```

In TS existieren zwei Arten, Objekte zu typisieren: Interfaces und Types. Interfaces beschreiben die Form eines Objekts und können über das Schlüsselwort extends erweitert werden. Types hingegen werden oft für Union-Types oder Aliase genutzt und können durch Intersections (&) kombiniert werden. Zudem existieren Utility-Types, die es uns erlauben, neue Typen aus bestehenden zu komponieren.

```ts
interface Product {
  id: string;
  name: string;
  price: number;
  description: string;
  category: string;
}

type SuccessResponse = {
  status: "success";
  data: string[];
};

type ErrorResponse = {
  status: "error";
  message: string;
};

type ApiResponse = SuccessResponse | ErrorResponse;

// Omit: Create a type without 'id' and 'category' (useful for a creation form)
type ProductForm = Omit<Product, 'id' | 'category'>;

// Pick: Create a type with ONLY 'name' and 'price' (useful for a summary list)
type ProductSummary = Pick<Product, 'name' | 'price'>;

// Partial: Makes all properties optional (useful for update/patch requests)
type UpdateProduct = Partial<Product>;

const preview: ProductSummary = {
  name: "Mechanical Keyboard",
  price: 150
};
```
