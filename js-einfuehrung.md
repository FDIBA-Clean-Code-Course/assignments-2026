# Einführung in JavaScript

JavaScript ist eine Skriptsprache, die entwickelt wurde, um die Funktionen einer Webseite zu erweitern. Dank Node.js kann sie nicht nur im Browser, sondern auch in einer Serverumgebung ausgeführt werden. 
Es ist eine Single-Threaded-Sprache ohne ein striktes Typensystem.

## Variablen

Es existieren drei Ansätze eine Variable zu deklarieren - **let**, **const** und **var**.

Sowohl **let** als auch **const** deklarieren eine Block-Scoped-Variable (block scope). **Let** kann später initialisiert werden, aber **const** muss bei der Erstellung initalisiert und kann später nicht geändert werden!
Beide kann später nicht erneut deklarieret werden! **Const** soll immer zuerst verwendet und ins **let** umgeformt nur wenn nötig!

``` js
// erlaubt
let speed;
speed = 10;
const speed = 10;

// nicht erlaubt
const speed;
speed = 10;
let speed = 10;
let speed = 15;
```
**var** wird zurzeit nicht verwendet für zwei Gründe - **var** ist Global-Scoped (global scope), was problematisch werden kann, var ist **hoisted** und kann erneut deklarieret werden. **Hoisting** (aus Englisch) wird von JS verwendet, 
um Funktion-Deklarationen am Anfang des Files zu posizionieren, damit diese Funktionen überall verwendbar sind. Deshalb kann **var** vor ihrer Initialisation verwendet werden, ohne einen Fehler zu werfen.

```js
	// keine Fehler
	console.log(5 + myVar);
	var myVar = 8;

	// Fehler aufgetreten
	console.log(5 + myLet);
	let myLet = 8;

	console.log(5 + myConst);
	const myConst = 8;

```
## Datentypen

Es existieren zwei Typen von Variablen - primitive Datentypen und Referenztypen.

Die primitive Typen sind **number** (verwendet für Integers, Doubles, Decimals, etc.), **string** (Chars und Strings), **boolean**, **null** und **undefined**. Diese sind **immutable** (aus Englisch) und Copy-By-Value. **Undefined** wird für nicht initialisierte
Variablen verwendet und **null** gilt als Nicht-Wert (blank value).

```js
	const myNum = 5;
	const myDoubleDigitNum = 5.5;
	const myString = 'Hello World!';
	const myBool = true; // false

	let a = 5;
	let b = a; // b = 5, a = 5
	b = 10; // b = 10, a = 5
```

Referenztypen sind Arrays (arrays) und Objekts (objects). Diese sind **mutable** (aus Englisch) und können geändert werden, und sint auch Copy-By-Reference.

```js
const myArr = [1, 2, true, 'what'];
myArr.push(5); // [1, 2, true, 'what', 5]
const myArr2 = myArr;
myArr2.push(6); // myArr = [1, 2, true, 'what', 5, 6]

const myObj = { name: 'Lenko', job: 'Frontend Developer' };
const myObj2 = myObj;
myObj2.job = 'Village Idiot'; // myObj = { name: 'Lenko', job: 'Village Idiot'
```
Mehr über beiden Typen später.

## Operatoren

- arithmetisch - + , - , / , *
- inkrementieren / dekrementieren - ++ / --
- Zuweisung - =
- logisch - &&, ||, !, ?? (nullish coalescent)
- Vergleich - ==, ===, !=, !==, >, <, >=, <=

```js
	const fiveNum = 5;
	const fiveString = '5';

	// Schwache Gleichheit (loose equality) - Typen nicht berücksichtigt
	console.log(fiveNum == fiveString); // true
	// Starke Gleichheit (strict equality) - Typen berücksichtigt
	console.log(fiveNum === fiveString) // false

	// Nullish coalescing Operator
	console.log(null ?? 'Not a null'); // 'Not a null'
```

- typeof - ein Ansatz den Typ einer Variable zu überprüfen

```js
const myNum = 42;
// Typeof gibt eine String-Repräsentation des Typs zurück.
console.log(typeof myNum); // 'number'

// Typ-Einschränkung (type guard)
if(typeof myNum === 'number'){
// do some calculations
}

if(typeof myNum !== 'undefined'){
// do something
}

// Funktionen, Arrays, Objekts
function myFunc() {}
console.log(typeof myFunc); // 'function'
const myObj = {};
console.log(myObj); // 'object'
const myArr = [0,1];
console.log(typeof myArr); // 'object'
```
## Kontrolfluss (Control flow)

Wie anderen Sprache - **if / else / else-if** und **switch** Konstruktionen für Abfragen

Für Schleifen - **for**, **while**, **do-while**

## Funktionen

Es existieren zwei Ansätzen Funktionen zu deklarieren - normale Deklaration und Function-Expressions (aus Englisch)

```js
	// Funktiondeklaration
	function square(num) {
		return num * num;
	}

	// Function-Expression ohne und mit Namen (named functions)
  // Funktionen ohne Namen werden anonyme Funktionen (anonymous functions) gennant
	const expressiveSquare = function(num) {
		return num * num;
	}
	const expressiveSquare2 = function sqr(num) {
		return num * num;
	}

  // Expression mit Namen können sich selbst aufrufen
  const namedFunction = function nfactoriel(n) {
    if(n==1) return 1;
    return n * nfactoriel(n-1);
  }

  // Anonyme Funktionen könen auch als Arrow-Funktionen (arrow functions) geschrieben werden!
  const arrowFunction = () => {
    console.log('arrowFunction');
  }

	// Alle werden gleichfalls aufgeruft
	square(2)
	expressiveSquare(2);
	expressiveSquare2(2);
  arrowFunction();
```

**Hoisting** funktioniert nur mit Funktiondeklarationen!

```js
		// Hoisted
		console.log(square(5));
		function square(num) {
			return num * num;
		}

		// Arbeitet nicht
		console.log(mySquare(5));
		const mySquare = (num) => num * num;
```

Arrow-Funktionen sind bevorzugt, um Fehler zu vermeiden, die mit Hoisting verbunden sind! Das zwingt auch dazu, zuerst zu deklarieren und initialisieren und dann zu verwenden, was logisch auch viel einfacher ist.

## Arrays

Wie es schon gezeigt wurde, können Arrays verschiedene Datentypen speichern, was aber zu vermeiden ist! Sonst sind Arrays auch wie in anderen Sprachen 0-indiziert. Was besonders ist, ist dass Arrays keine feste Größe haben -
in diesem Sinne sind JS-Arrays wie eine Linked-List-Datenstruktur.

Elemente werden in der Regeln hinzugefüft oder gelöscht mit **push** und **pop**.

```js
	const myArr = [1];
	myArr.push(15); // [ 1, 15 ]
	myArr.pop(); // [ 1 ]
```

Zur Verfügung stehen mehrere Funktionen, mit denen wir unsere Elemente manipulieren können. Es wird empfohlen, diese Funktionen zu verwenden, anstatt selbst über die Elemente zu iterieren. Falls aber das nötig ist - verwenden wir **forEach**
statte einer for-Schleife. Unten sind Beispiele mit Erklärungen auf Englisch.

```js
// forEach -> iterate over elements and do something
  const myArr = [1, 2, 3];
  myArr.forEach((element) => console.log(element));

// map -> Returns a new array after modifying the items of the old one via a provided function
	const myArr2 = [1, 2, 3];
	console.log(myArr2); // [ 1, 2, 3]
	const doubledArr = myArr2.map((element) => element * 2);
	console.log(doubledArr); // [ 2, 4, 6 ]
	
	// slice -> Returns selected items from the old array into a new one based on the interval provided by the parameters
	const myArr3 = [ 1, 2, 3, 4, 5];
	const sliced1 = myArr3.slice(2); // [ 3, 4, 5 ]
	console.log(sliced1);
	const sliced2 = myArr3.slice(1, 3) // [ 2, 3 ]
	console.log(sliced2);
	
	// splice -> Modifies the original array according to the interval (and the new value) provided by the parameters
	const months = ["Jan", "March", "April", "June"];
	months.splice(0, 2); // [ "April", "June" ]
	console.log(months);
	months.splice(1, 1, 'August') // [ "April", "August" ]
	console.log(months);
	
	// reduce -> Run some function on all the elements, whilst using its previous calcualtion as part of it. If no initial values is provided, the first element will be considered the initial value.
	const myArr4 = [ 1, 2, 3];
	const reduceResult = myArr4.reduce((accumulator, currentValue) => accumulator + currentValue); // 6
	console.log(reduceResult);
	
	const initValue = 10;
	const reduceResult2 = myArr4.reduce((accumulator, currentValue) => accumulator + currentValue, initValue); // 16
	console.log(reduceResult2); 
```

Arrays können mit dem Spread-Operator (spread operator) kopiert werden.

```js
	const myArr = [ 1, 2, 3 ];
	const copiedArr = [ ...myArr ];
	const refArr = myArr;
	console.log(myArr); // [ 1, 2, 3 ]
	console.log(copiedArr); // [ 1, 2, 3 ]
	console.log(refArr) // [ 1, 2, 3 ]
	console.log(myArr === copiedArr); // false
	console.log(refArr === myArr) // true
```

## Objekte

Objekte haben Eigenschaften.

```js
	const lenko = {
	    name: 'Lenko',
	    job: 'Frontend Dev'
	}
	// Eigenschaften zugreifen
	console.log(lenko.name); // 'Lenko'
	// Mehrere Eigenschaften nach der Zuweisung hinzufügen
	lenko.age = 23;
	console.log(lenko); // { name: 'Lenko', job: 'Frontend', age: 23}

  // Eigenschaften löschen
	delete lenko.job;
	console.log(lenko) // { name: 'Lenko', age: 23}
```

Eigenschaften könne auch Funktionen oder Referenztypen sein!

```js
	const lenko = {
	    name: 'Lenko',
	    job: 'Frontend',
	    work: () => console.log('Working...'),
	    printFullName:function () { 
	    console.log(this.name + ' Ivanov') } // Mehr über this später
  }
	lenko.work(); // 'Working...'
	lenko.printFullName(); // 'Lenko Ivanov'
```

Spread-Operator funktioniert bei Objekten auch. 

```js
	const myObj = { name: 'Lenko', job: 'Frontend'};
	const refObj = myObj;
	const copyObj = { ...myObj };
	console.log(myObj);
	console.log(refObj);
	console.log(copyObj);
	console.log(myObj === refObj); // true
	console.log(copyObj === myObj); // false

  // Tief verschalteten Referenztypen werden NICHT kopiert
	const myObj = {
	name: 'name',
	list: [1, 2, 3],
	};

	const obj = { ...myObj };
	obj.list.push(42);

	console.log(obj.list); // [1, 2, 3, 42]
	console.log(myObj.list); // [1, 2, 3, 42]

  // In der Browser-Umgebung können wir structuredClone verwenden. Sonst soll ein "Deep Clone"-Funktion erstellt werden.
```

Auch bei Objekten stehen zur Verfügung mehrere Methoden. Unten, auf Englisch:

```js
const lenko = {
	  name: 'Lenko',
	  job: 'Frontend',
	  work: () => console.log('Working...'),
	  printFullName: function () { console.log(this.name + ' Ivanov')}
}

// Returns all keys 
console.log(Object.keys(lenko)); // [ 'name', 'job', 'work', 'printFullName' ]

// Returns all values
console.log(Object.values(lenko)); // [ 'Lenko', 'Frontend', [Function: work], [Function: printFullName] ]

// Returns key-value pairs
console.log(Object.entries(lenko)); // [ [ 'name', 'Lenko' ],[ 'job', 'Frontend' ], [ 'work', [Function: work] ], [ 'printFullName', [Function: printFullName] ] ]

// Copy properties into a new object
const newLenko = {};
Object.assign(newLenko, lenko);
console.log(newLenko);
console.log(newLenko === lenko); // false

lenko.age = 23;

// Cannot add or modify properties
Object.freeze(lenko);
lenko.hobbies = [ 'Books', 'Warhammer' ];
lenko.age = 24;
console.log(lenko);

// Cannot add properties but can modify existing ones
Object.seal(lenko);
lenko.hobbies = [ 'Books', 'Warhammer' ];
lenko.age = 24;
console.log(lenko);
```

## This in JS

In verschiedene Kontexten bedeutet das This-Objekt etwas verschiedenes. Wenn im Browser und außer einer Funktion zeigt This zu dem globalen Window-Objekt. In einer Objektmethode zeigt This zu dem Objekt.
Arrow-Funktionen haben keine This-Kontexte!

```js
// Außer einer Funktion oder in normalen Funktionen zeigt this zu dem globalen Objekt
console.log('Global this: ', this);

function myFunction() {
  console.log('myFunction', this);
}
myFunction();

const myFunc = function() {
  console.log('myFunc', this);
}
myFunc();

const myFunc2 = function funky() {
  console.log('myfunc2', this);
}
myFunc2();

const arrowFunction = () => {
  console.log('arrowFunction', this);
}
arrowFunction();

// In Objekten selbst steht das anders
	const lenko = {
	    name: 'Lenko',
	    job: 'Frontend',
	    work: () => console.log('Working...'),
	    printFullName:function () { 
	      console.log(this.name + ' Ivanov');
      },
      arrowNamePrint: () => {
        console.log(this.name + 'Ivanov');
      }
  }
  
  lenko.printFullName(); // Lenko Ivanov
  lenko.arrowNamePrint(); // Ivanov
```
