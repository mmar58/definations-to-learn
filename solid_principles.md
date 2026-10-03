# SOLID Principles

SOLID is an acronym that represents five key design principles of object-oriented programming and design. These principles, when combined, make it easier for a programmer to develop software that is easy to maintain and extend. They also make it easier for developers to avoid code smells, easily refactor code, and are also a part of agile or adaptive software development.

The SOLID principles are:
1. **S** - Single Responsibility Principle (SRP)
2. **O** - Open-Closed Principle (OCP)
3. **L** - Liskov Substitution Principle (LSP)
4. **I** - Interface Segregation Principle (ISP)
5. **D** - Dependency Inversion Principle (DIP)

---

## 1. Single Responsibility Principle (SRP)

**Definition:** A class should have one, and only one, reason to change. Meaning that a class should have only one job or responsibility.

If a class has more than one responsibility, it becomes coupled. A change to one responsibility results in modification of the other responsibility.

### Code Example (Violation)

```javascript
class User {
  constructor(name) {
    this.name = name;
  }

  saveToDatabase() {
    // Saves user to database
    console.log(`Saving ${this.name} to database...`);
  }

  generateReport() {
    // Generates a report for the user
    console.log(`Generating report for ${this.name}...`);
  }
}
```
*Why this violates SRP:* The `User` class has two reasons to change. It will change if the database saving logic changes, and it will also change if the report generation format changes.

### Code Example (Good)

```javascript
class User {
  constructor(name) {
    this.name = name;
  }
}

class UserRepository {
  save(user) {
    console.log(`Saving ${user.name} to database...`);
  }
}

class UserReportGenerator {
  generate(user) {
    console.log(`Generating report for ${user.name}...`);
  }
}
```
*Why this is good:* Each class now has a single responsibility. `User` holds data, `UserRepository` handles database operations, and `UserReportGenerator` handles reporting.

---

## 2. Open-Closed Principle (OCP)

**Definition:** Software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification.

This means you should be able to add new functionality without changing existing code, reducing the risk of breaking existing functionality.

### Code Example (Violation)

```javascript
class Rectangle {
  constructor(width, height) {
    this.width = width;
    this.height = height;
  }
}

class Circle {
  constructor(radius) {
    this.radius = radius;
  }
}

class AreaCalculator {
  calculate(shapes) {
    let area = 0;
    for (let shape of shapes) {
      if (shape instanceof Rectangle) {
        area += shape.width * shape.height;
      } else if (shape instanceof Circle) {
        area += Math.PI * Math.pow(shape.radius, 2);
      }
    }
    return area;
  }
}
```
*Why this violates OCP:* If we want to add a new shape (e.g., `Triangle`), we have to modify the `AreaCalculator` class by adding another `else if` statement. It is not closed for modification.

### Code Example (Good)

```javascript
class Shape {
  area() {
    throw new Error("Method 'area()' must be implemented.");
  }
}

class Rectangle extends Shape {
  constructor(width, height) {
    super();
    this.width = width;
    this.height = height;
  }

  area() {
    return this.width * this.height;
  }
}

class Circle extends Shape {
  constructor(radius) {
    super();
    this.radius = radius;
  }

  area() {
    return Math.PI * Math.pow(this.radius, 2);
  }
}

class Triangle extends Shape {
  constructor(base, height) {
    super();
    this.base = base;
    this.height = height;
  }

  area() {
    return (this.base * this.height) / 2;
  }
}

class AreaCalculator {
  calculate(shapes) {
    return shapes.reduce((total, shape) => total + shape.area(), 0);
  }
}
```
*Why this is good:* We can now add as many new shapes as we want without modifying `AreaCalculator`. The system is open for extension but closed for modification.

---

## 3. Liskov Substitution Principle (LSP)

**Definition:** Objects in a program should be replaceable with instances of their subtypes without altering the correctness of that program.

If S is a subtype of T, then objects of type T may be replaced with objects of type S without breaking the application. Derived classes must be substitutable for their base classes.

### Code Example (Violation)

```javascript
class Bird {
  fly() {
    console.log("I can fly");
  }
}

class Duck extends Bird {}

class Penguin extends Bird {
  fly() {
    throw new Error("Cannot fly");
  }
}

function makeBirdFly(bird) {
  bird.fly();
}

makeBirdFly(new Duck()); // Works fine
makeBirdFly(new Penguin()); // Throws Error!
```
*Why this violates LSP:* `Penguin` is a `Bird`, but it cannot substitute `Bird` in the `makeBirdFly` function because it throws an exception instead of flying.

### Code Example (Good)

```javascript
class Bird {
  // Common bird properties/methods
}

class FlyingBird extends Bird {
  fly() {
    console.log("I can fly");
  }
}

class NonFlyingBird extends Bird {
  walk() {
      console.log("I can walk");
  }
}

class Duck extends FlyingBird {}

class Penguin extends NonFlyingBird {}

function makeBirdFly(bird) {
  bird.fly();
}

makeBirdFly(new Duck()); // Works fine
// makeBirdFly(new Penguin()); // We wouldn't even pass a penguin here as it expects a FlyingBird, preventing runtime errors.
```
*Why this is good:* We separated the flying behavior into a specific subclass. Now, subtypes can safely be substituted for their base types where expected.

---

## 4. Interface Segregation Principle (ISP)

**Definition:** Many client-specific interfaces are better than one general-purpose interface. Clients should not be forced to depend upon interfaces that they do not use.

While JavaScript doesn't have formal interfaces like Java or C#, the principle still applies to the design of our objects and classes. Avoid creating "fat" classes that force clients to implement or depend on methods they don't need.

### Code Example (Violation)

```javascript
// Simulating a "fat" interface
class Machine {
  print(doc) {
      throw new Error("Not implemented");
  }
  fax(doc) {
      throw new Error("Not implemented");
  }
  scan(doc) {
      throw new Error("Not implemented");
  }
}

class MultiFunctionPrinter extends Machine {
  print(doc) { console.log("Printing..."); }
  fax(doc) { console.log("Faxing..."); }
  scan(doc) { console.log("Scanning..."); }
}

class OldFashionedPrinter extends Machine {
  print(doc) { console.log("Printing..."); }
  
  fax(doc) {
    throw new Error("Cannot fax");
  }
  
  scan(doc) {
    throw new Error("Cannot scan");
  }
}
```
*Why this violates ISP:* `OldFashionedPrinter` is forced to depend on `fax` and `scan` methods it does not support, throwing errors.

### Code Example (Good)

```javascript
// Simulating segregated interfaces/capabilities
class Printer {
  print(doc) {
      throw new Error("Not implemented");
  }
}

class Scanner {
  scan(doc) {
      throw new Error("Not implemented");
  }
}

class Fax {
  fax(doc) {
      throw new Error("Not implemented");
  }
}

class MultiFunctionPrinter {
  constructor() {
      this.printer = new PrinterImpl();
      this.scanner = new ScannerImpl();
      this.fax = new FaxImpl();
  }
  // Delegation
  print(doc) { this.printer.print(doc); }
  scan(doc) { this.scanner.scan(doc); }
  fax(doc) { this.fax.fax(doc); }
}

class OldFashionedPrinter extends Printer {
  print(doc) {
      console.log("Printing...");
  }
}
```
*Why this is good:* Classes only implement the behaviors they actually support. `OldFashionedPrinter` only implements `print` and isn't burdened with unsupported methods.

---

## 5. Dependency Inversion Principle (DIP)

**Definition:**
1. High-level modules should not depend on low-level modules. Both should depend on abstractions (e.g., interfaces).
2. Abstractions should not depend on details. Details (concrete implementations) should depend on abstractions.

This principle allows for decoupling.

### Code Example (Violation)

```javascript
class MySQLDatabase {
  save(data) {
    console.log("Saving data to MySQL...");
  }
}

class PasswordReminder {
  constructor() {
    // High-level module depends directly on low-level module
    this.dbConnection = new MySQLDatabase(); 
  }

  remind() {
    this.dbConnection.save("reminder info");
  }
}
```
*Why this violates DIP:* `PasswordReminder` (high-level) is tightly coupled to `MySQLDatabase` (low-level). If we want to switch to a MongoDB database, we have to modify the `PasswordReminder` class.

### Code Example (Good)

```javascript
// Abstraction (Conceptually)
class DatabaseInterface {
  save(data) {
      throw new Error("Not implemented");
  }
}

class MySQLDatabase extends DatabaseInterface {
  save(data) {
    console.log("Saving data to MySQL...");
  }
}

class MongoDatabase extends DatabaseInterface {
  save(data) {
    console.log("Saving data to MongoDB...");
  }
}

class PasswordReminder {
  constructor(database) {
    // High-level module depends on abstraction (via dependency injection)
    this.dbConnection = database;
  }

  remind() {
    this.dbConnection.save("reminder info");
  }
}

// Usage
const mysqlDB = new MySQLDatabase();
const reminder1 = new PasswordReminder(mysqlDB);
reminder1.remind();

const mongoDB = new MongoDatabase();
const reminder2 = new PasswordReminder(mongoDB);
reminder2.remind();
```
*Why this is good:* `PasswordReminder` no longer depends on a specific database implementation. It depends on an abstraction (provided via dependency injection). We can easily swap out databases without changing the `PasswordReminder` code.
