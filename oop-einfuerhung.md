# Objektorientierte Programmierung

1. Warum OOP?

OOP ist eine Methode, um Komponenten eines Systems und ihre Beziehungen mithilfe von Objekten zu modellieren. Objekte besitzen bestimmte Eigenschaften (Attribute) und Verhaltensweisen (Methoden), die üblicherweise dazu dienen, mit der „Außenwelt“ (den anderen Komponenten des Systems) zu interagieren.

2. Die vier Kernprinzipien von OOP

- Kapselung (Encapsulation): Die internen Details eines Objekts werden verborgen. Wir entscheiden selbst, was wir über eine Schnittstelle – meist durch Getter- und Setter-Methoden – nach außen hin freigeben. Durch Zugriffsbeschränkungen können wir Details weiter abschirmen. Dies fördert die Modularität, die Sicherheit und reduziert die enge Kopplung (interne Änderungen erfordern keine externen Anpassungen).

- Abstraktion (Abstraction): Dies ist der Prozess, bei dem interne Komplexität „ausgeblendet“ wird. Der Nutzer eines Objekts muss nicht wissen, wie die Methoden implementiert sind. Dies ermöglicht eine hohe Flexibilität bei der Verwendung von Objekten.

- Vererbung (Inheritance): Dies beschreibt die Eigenschaft einer Klasse (Subklasse, abgeleitete Klasse, Kindklasse), Methoden und Eigenschaften von einer anderen Klasse (Superklasse, Basisklasse, Elternklasse) zu erben. Dies erlaubt die Wiederverwendung bestehender Logik, ohne sie kopieren zu müssen, und ermöglicht deren Erweiterung.

- Polymorphie (Polymorphism): Sie erlaubt es, verschiedene Objekte, die von derselben Superklasse abgeleitet wurden, so zu behandeln, als wären sie Instanzen dieser Superklasse. Das bedeutet nicht nur, dass sie die Funktionen der Superklasse nutzen können, sondern auch, dass sie spezifische Methoden mit ihrer eigenen Implementierung überschreiben können.

```ts
export abstract class Item {
  public readonly name: string;

  constructor(name: string) {
    this.name = name;
  }

  protected getDetails(): void {
    console.log(`Item: ${this.name}`);
  }

  getName(): string {
    return this.name;
  }

}

export class Weapon extends Item {
  public power: number;

  constructor(name: string, power: number) {
    super(name);
    this.power = power;
  }

  protected getDetails(): void {
    console.log(`Weapon name: ${this.name}. Weapon power: ${this.power}`);
  }
}

export class Armor extends Item {
  public protection: number;

  constructor(name: string, protection: number) {
    super(name);
    this.protection = protection;
  }

  

  protected getDetails(): void {
    console.log(`Armor name: ${this.name}. Armor protection: ${this.protection}`);
  }

}

export class HealthPotion extends Item {
  public healthGain: number;

  constructor(name: string, healthGain: number) {
    super(name);
    this.healthGain = healthGain;
  }

  protected getDetails(): void {
    console.log(`Potion: ${this.name}. Restores ${this.healthGain} HP.`);
  }
}
```

3. Grundlagen

- Objekte werden üblicherweise mit dem Schlüsselwort new instanziiert – dies reserviert den notwendigen Speicherplatz und ruft die Konstruktor-Methode des Objekts auf.

- Konstruktor: Eine Klasse benötigt einen Konstruktor, um instanziiert zu werden. Er nimmt Parameter entgegen, um das neue Objekt zu initialisieren. Ohne eigenen Konstruktor wird ein interner Standard-Konstruktor aufgerufen. In einer Subklasse muss im Konstruktor immer zuerst super() aufgerufen werden, um den Konstruktor der Elternklasse auszuführen und Zugriff auf deren Eigenschaften und Methoden zu gewährleisten.

- Getter & Setter: Verwende diese Funktionen, um Objektwerte zu manipulieren oder abzufragen. Achte bei Settern darauf, dass sie eine klare semantische Absicht widerspiegeln (z. B. takeDamage statt nur setHealth).

- Abstrakte Klassen: Eine Klasse, von der man nur erben kann und die nicht selbst instanziiert werden kann. Sie dient als Vorlage für Basisverhalten, das in Subklassen mittels Polymorphie angepasst wird.

4. Zugriffsbeschränkungen (Access Modifiers)

Zugriffsbeschränkungen steuern die Sichtbarkeit von Eigenschaften und Methoden innerhalb einer Klasse. In TypeScript (TS) gibt es folgende Typen:

- private: Nur die Klasse selbst hat Zugriff auf diese Eigenschaft oder Methode.

- protected: Nur die Klasse und ihre Subklassen haben Zugriff.

- public: Jeder Teil des Programms kann darauf zugreifen.

Der Standardwert ist public, sofern nichts anderes angegeben wird.

```ts
export class Character extends Entity {
  protected healthPoints: number;

  constructor(name: string, healthPoints: number) {
    super(name);
    this.healthPoints = healthPoints;
  }

  // setter
  takeDamage(amount: number): void {
    this.healthPoints = Math.max(0, this.healthPoints - amount);
  }

  // getter
  getHealthPoints(): number {
    return this.healthPoints;
  }
}


export class Player extends Character {
  private inventory: Item[] = [];
  public level: number;

  
  constructor(name: string, healthPoints: number) {
    super(name, healthPoints);
    this.level = 1;
  }

  pickUp(item: Item): void {
    this.inventory.push(item); // accesible here only
    console.log(`${this.name} picked up ${item.getName()}`);
  }

  consumePotion(potion: Item): void {
    if (potion instanceof HealthPotion) {
      this.healthPoints += potion.healthGain;
      console.log(`${this.name}'s health is now ${this.healthPoints}`); // protected parent property accessible here
    }
    // other potions go here
  }

  attackWithWeapon(): number {
    const equippedWeapon = this.inventory.find((item): item is Weapon => item instanceof Weapon);
    if (equippedWeapon) {
      return equippedWeapon.power;
    }
    return 0;
  }

  defendWithArmor(): number {
    const equippedArmor = this.inventory.find((item): item is Armor => item instanceof Armor);
    if (equippedArmor) {
      return equippedArmor.protection;
    }
    return 0;
  }
}

export class Enemy extends Character {
  public attackPower: number;
  public itemReward: Item;

  
  constructor(name: string, healthPoints: number, attackPower: number, itemReward: Item) {
    super(name, healthPoints);
    this.attackPower = attackPower;
    this.itemReward = itemReward;
  }

  attack(): number {
    return this.attackPower;
  }


  getReward(): Item {
    return this.itemReward;
  }

}

const enemy = new Enemy(...);
enemy.attackPower // public, can be accessed;
enemy.healthPoints // protected, can't be accessed;
```
