# Classes & Prototypes

## Introduction

This document explores how JavaScript handles object-oriented programming (OOP) through different approaches, from basic objects to modern classes. We'll use a `user` object as our example and progress step by step from less optimal to more optimal designs.

---

## Level 1: Plain Objects

```javascript
const user1 = {
  username: "Tim",
  score: 12,
  incrementScore: () => {
    // do something
  },
};
```

**Problem:** If we create users like this, we have to create hundreds of objects one by one. Also, using arrow functions for methods that use `this` is problematic, as arrow functions do not have their own `this`.

---

## Level 2: Factory Function

```javascript
function userCreator(username, score) {
  const newUser = {};
  newUser.username = username;
  newUser.score = score;
  newUser.incrementScore = () => {
    // newUser.score++
  };
  return newUser;
}
```

**Improvement:** We can now create multiple users easily.

**Problem:** Each user gets its own copy of `incrementScore`, which is inefficient in terms of memory.

---

## Level 3: Prototype Chain (Object.create with Function Store)

```javascript
const userFunctionStore = {
  incrementScore: function () {
    this.score++;
  },
};

function userCreator(username, score) {
  const newUser = Object.create(userFunctionStore);
  newUser.username = username;
  newUser.score = score;
  return newUser;
}
```

**Key Detail:**

- Methods in `userFunctionStore` must use the `function` keyword, not arrow functions, so that `this` refers to the user instance.
- If you use an arrow function, `this` will refer to the parent context (often the global object), not the user instance.
- For nested functions inside methods, use arrow functions to inherit the correct `this`.

**Improvement:** Functions are now shared via the prototype chain, saving memory.

---

## Level 4: Constructor Function with `new`

```javascript
function UserCreator(username, score) {
  this.username = username;
  this.score = score;
}

UserCreator.prototype.incrementScore = function () {
  this.score++;
};

// Usage:
const user1 = new UserCreator("Tim", 12);
```

**What does `new` do?**

1. Creates a new empty object.
2. Sets the prototype of the new object to `UserCreator.prototype`.
3. Binds `this` inside the constructor to the new object.
4. Executes the constructor function.
5. Returns the new object.

**If you forget `new`:**

- `this` will refer to the global object (or be undefined in strict mode), and properties will not be set on a new instance.

---

## Level 5: ES6 Classes

```javascript
class User {
  constructor(username, score) {
    this.username = username;
    this.score = score;
  }
  incrementScore() {
    this.score++;
  }
}

const user1 = new User("Tim", 12);
```

**Improvements:**

- All related code is in one block.
- Automatic `new` requirement (throws error if forgotten).
- Cleaner, more readable syntax.
- Built-in inheritance support.

---

## Conclusion

JavaScript's evolution from manual object creation to ES6 classes demonstrates the language's journey toward more intuitive OOP:

1. **Plain Objects:** Manual, repetitive, memory-inefficient.
2. **Factory Functions:** Reusable but still memory-inefficient.
3. **Prototype Chain:** Memory-efficient but complex setup.
4. **Constructor Functions with `new`:** Cleaner but error-prone.
5. **ES6 Classes:** Modern, clean, safe, and feature-rich.

**Key Takeaways:**

- Use the `function` keyword for methods that need proper `this` binding.
- Use arrow functions only for nested functions that should inherit `this`.
- Always use `new` with constructor functions (or use classes to enforce this).
- Prefer ES6 classes for modern JavaScript development.
