### 1. SOLID Principles

**Definition:** SOLID is a group of five principles for designing maintainable and extensible object-oriented software.

**Interview answer:**

> “SOLID is a set of five design principles that help make software easier to maintain, extend, and test. They are Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, and Dependency Inversion.”

Then explain each:

* **S — Single Responsibility Principle:** A class/module should have one primary responsibility.
* **O — Open/Closed Principle:** Software should be open for extension but closed for modification.
* **L — Liskov Substitution Principle:** A subtype should be usable wherever its base type is expected without breaking behavior.
* **I — Interface Segregation Principle:** Don't force a class to depend on methods it doesn't need.
* **D — Dependency Inversion Principle:** High-level code should depend on abstractions rather than concrete implementations.

---

Object-Oriented Programming (OOP) is a software design paradigm focused on **objects**—units that bundle data and behavior together—rather than sequential logic alone.

Here is a straightforward, single-tone explanation tailored for an interview response.

---

### What is Object-Oriented Programming?

Object-Oriented Programming organizes software around real-world concepts or entities called objects. In procedural programming, functions and data are kept separate, and instructions execute step-by-step. In OOP, data (attributes) and the code that operates on it (methods) are packaged together inside classes to form self-contained objects.

* **Example OOP Languages:** Java, C++, Python, C#, Swift.

---

### Why is it Called "Object-Oriented"?

It is called "Object-Oriented" because the application architecture is oriented directly around objects rather than procedures:

* **Shift in Focus:** Instead of focusing on *what actions are happening*, OOP focuses on *what entities are acting or being acted upon*.
* **Real-World Modeling:** Systems are built by modeling entities that have state, behavior, and the ability to interact with other entities.
* **Data Integration:** Data and its associated functions are bound directly to the object, ensuring data integrity across the system.

> **Analogy:** A **Car** class acts as the blueprint. An individual **car on the road** is the object instance. Its **color and speed** are its attributes, and **accelerating or braking** are its methods.

---

### The 4 Pillars of OOP

1. **Encapsulation:** Bundling data and methods into a single unit while restricting direct outside access to internal states.
2. **Abstraction:** Hiding internal implementation details and exposing only what is necessary through clear interfaces.
3. **Inheritance:** Allowing a class to derive properties and behavior from another class to reuse code.
4. **Polymorphism:** Enabling different classes to be treated through a single interface using method overriding and overloading.

---

### 30-Second Interview Response

> "Object-Oriented Programming is a paradigm where software is built around objects that combine data and behavior, rather than standalone functions. It is called 'object-oriented' because system design is centered on modeling entities and their interactions. Its primary goals are modularity, reusability, and maintainability achieved through Encapsulation, Abstraction, Inheritance, and Polymorphism."

### 2. Encapsulation

**Definition:** Encapsulation means keeping an object's internal state and implementation details hidden behind a controlled interface.

**Interview answer:**

> “Encapsulation means hiding internal implementation details and exposing only what other parts of the application need to interact with.”

**Example:**
A `UserService` exposes `createUser()` rather than allowing every part of the application to directly manipulate password hashing and database operations.

---

### 3. Abstraction

**Definition:** Abstraction means exposing the important behavior of something while hiding unnecessary implementation details.

**Interview answer:**

> “Abstraction allows us to work with something through a simpler interface without needing to know its internal implementation.”

For example:

```ts
paymentService.charge(amount)
```

The caller doesn't need to know whether Stripe, PayPal, or another provider performs the actual payment.

---

### 4. Inheritance

**Definition:** Inheritance allows one class to derive properties and behavior from another class.

**Interview answer:**

> “Inheritance allows a child class to reuse and extend behavior from a parent class. It's useful when there is a genuine is-a relationship, although composition is often preferable when we want more flexibility.”

That last sentence is useful because interviewers often follow up with **“composition vs inheritance?”**

---

### 5. Polymorphism

**Definition:** Polymorphism allows different implementations to be treated through the same interface or abstraction.

**Interview answer:**

> “Polymorphism allows different objects to provide different implementations of the same interface, so the calling code doesn't need to know the concrete implementation.”

Example:

```ts
interface Notification {
  send(message: string): void;
}

class EmailNotification implements Notification {
  send(message: string) {}
}

class SMSNotification implements Notification {
  send(message: string) {}
}
```

Your service can work with `Notification` without caring whether it's email or SMS.

---

### 6. Composition

**Definition:** Composition builds complex behavior by combining smaller independent components rather than relying heavily on inheritance.

**Interview answer:**

> “Composition means building an object or service by combining smaller components. I generally prefer it when behaviors need to be mixed and changed independently.”

This is extremely relevant to modern TypeScript/JavaScript development.

---

### 7. Dependency Injection

**Definition:** Dependency Injection means providing an object's dependencies from outside instead of having the object create them itself.

**Interview answer:**

> “Dependency injection means supplying dependencies from outside the class or module. It reduces coupling and makes components easier to test and replace.”

Instead of:

```ts
class UserService {
  private db = new PostgreSQLDatabase();
}
```

you have:

```ts
class UserService {
  constructor(private db: Database) {}
}
```

Now you can provide PostgreSQL in production and a mock database in tests.

---

### 8. Coupling

**Definition:** Coupling describes how strongly one component depends on another.

**Interview answer:**

> “Coupling measures how dependent one component is on another. Lower coupling generally makes a system easier to modify because changes in one component have less impact on others.”

---

### 9. Cohesion

**Definition:** Cohesion describes how closely related the responsibilities inside a module are.

**Interview answer:**

> “Cohesion measures how closely related the responsibilities of a module are. I generally want high cohesion, where a module focuses on a clear responsibility.”

A `PaymentService` containing payment-related functionality has higher cohesion than a giant `UtilsService` containing unrelated operations.

---

### 10. Separation of Concerns

**Definition:** Separation of concerns means dividing software into parts where each part handles a distinct concern.

**Interview answer:**

> “Separation of concerns means keeping different responsibilities separate so that changes in one concern don't unnecessarily affect another.”

For example:

```text
Controller → handles HTTP
Service    → business logic
Repository → database access
```

You've probably already been using this concept in your projects even if you didn't always call it by this name.

---

### 11. DRY

**Definition:** **Don't Repeat Yourself** means avoiding unnecessary duplication of knowledge or logic.

**Interview answer:**

> “DRY means avoiding duplication of the same business logic or knowledge. Instead of maintaining the same logic in multiple places, I try to centralize it where appropriate.”

Important: DRY **doesn't mean every duplicated line must immediately become a shared abstraction**. Premature abstraction can make code worse.

---

### 12. KISS

**Definition:** **Keep It Simple, Stupid** means preferring the simplest solution that correctly solves the problem.

**Interview answer:**

> “KISS means avoiding unnecessary complexity. I prefer the simplest design that satisfies the current requirements while leaving room for reasonable future changes.”

---

### 13. YAGNI

**Definition:** **You Aren't Gonna Need It** means avoiding implementation of functionality until there is a real requirement for it.

**Interview answer:**

> “YAGNI means I shouldn't build speculative features just because I think they might be needed later. I implement what the current requirements actually require unless there is a strong architectural reason to prepare for something.”

---

### 14. Idempotency

**Definition:** An operation is idempotent if performing it multiple times has the same intended result as performing it once.

**Interview answer:**

> “An idempotent operation can safely be repeated without producing additional unintended effects.”

This is particularly important in APIs and payment systems.

For example, a client might send:

```http
POST /payments
Idempotency-Key: abc123
```

If the request times out and the client retries, the server can recognize `abc123` and avoid charging the customer twice.

---

### 15. Concurrency

**Definition:** Concurrency means multiple tasks can make progress during overlapping periods of time.

**Interview answer:**

> “Concurrency means dealing with multiple tasks whose execution overlaps. In Node.js, asynchronous I/O allows the application to handle other work while waiting for operations such as database or network requests.”

This connects directly to what you just learned about Promise combinators.

---

### 16. Race Condition

**Definition:** A race condition occurs when the outcome depends on the timing or ordering of concurrent operations.

**Interview answer:**

> “A race condition occurs when multiple operations access or modify shared state concurrently and the final result depends on which operation happens first.”

This is a **very important backend interview term**.

---

### 17. Transaction

**Definition:** A database transaction groups multiple operations into a single logical unit that follows ACID guarantees.

**Interview answer:**

> “A transaction groups related database operations so they succeed or fail as a unit. For example, when transferring money, I would debit one account and credit another within the same transaction.”

Then mention:

**ACID:**

* **Atomicity** — all or nothing
* **Consistency** — valid state before and after
* **Isolation** — concurrent transactions don't improperly interfere
* **Durability** — committed data persists

---

### 18. Index

**Definition:** A database index is a data structure that helps the database find rows more efficiently.

**Interview answer:**

> “An index improves query performance by allowing the database to locate rows without scanning the entire table. The tradeoff is additional storage and slower writes because the index also has to be maintained.”

That **tradeoff sentence** makes your answer much stronger.

---

### 19. Caching

**Definition:** Caching stores frequently accessed data in a faster storage layer so it can be retrieved without repeatedly performing the expensive original operation.

**Interview answer:**

> “Caching stores frequently accessed or expensive-to-compute data in a faster layer, such as Redis, to reduce latency and database load. The main challenge is keeping cached data consistent with the source of truth.”

---

### 20. REST

**Definition:** REST is an architectural style for designing networked APIs around resources and standard HTTP semantics.

**Interview answer:**

> “REST is an architectural style where APIs expose resources through HTTP and use standard methods such as GET, POST, PUT, PATCH, and DELETE. A good REST API also uses appropriate status codes and keeps requests stateless.”

---

## The answer pattern I want you to practice

For almost every interview term, use this structure:

> **Definition → Purpose → Example → Tradeoff/consideration**

For example, if they ask **“What is caching?”**

Don't just say:

> “Caching stores data.”

Instead:

> “Caching is storing frequently accessed data in a faster layer so we don't repeatedly perform the expensive operation. For example, I might use Redis to cache frequently requested data and reduce PostgreSQL load. The tradeoff is that cached data can become stale, so I need an appropriate invalidation or expiration strategy.”

That's the difference between **knowing the word** and **sounding like someone who has actually used the concept**.

And for you specifically, I'd study these in groups:

**OOP/design:** SOLID, abstraction, encapsulation, polymorphism, composition, inheritance, dependency injection, coupling, cohesion.

**Backend:** REST, HTTP, authentication, authorization, JWT, sessions, transactions, ACID, indexes, normalization, caching, queues, concurrency, race conditions, idempotency.

**Architecture:** separation of concerns, layered architecture, modular architecture, monolith, microservices, event-driven architecture, scalability, availability, fault tolerance.

**JavaScript/Node:** event loop, promises, async/await, Promise combinators, closures, callbacks, streams, worker threads, non-blocking I/O.

**System design:** load balancing, horizontal/vertical scaling, database replication, sharding, rate limiting, message queues, pub/sub, eventual consistency.

That vocabulary is worth learning **because you already have practical experiences to attach to most of it**. The goal isn't to memorize definitions like a textbook; it's to make the terminology available when an interviewer asks you to explain what you've already been doing.
