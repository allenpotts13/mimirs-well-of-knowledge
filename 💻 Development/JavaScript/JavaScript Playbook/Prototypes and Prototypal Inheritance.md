---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-06
technology: JavaScript
difficulty: Advanced
tags:
  - prototypes
  - inheritance
  - object-oriented-programming
  - classes
aliases:
  - Prototype Chain
  - Prototypal Inheritance
publish: true
permalink: javascript/prototypes-and-prototypal-inheritance
---

# <span class="rune">ᛟ</span> Prototypes and Prototypal Inheritance

> *Every JavaScript object has a hidden link to another object it can delegate to when a property or method isn't found on itself — that chain of links is how JavaScript does inheritance, and `class` is just a friendlier syntax layered on top of it.*

---

## 🎯 Purpose

Most object-oriented languages you might learn first (Java, C++) implement inheritance through classes as a distinct, compile-time concept — a blueprint that objects are stamped from. JavaScript doesn't work that way underneath, even though `class` syntax makes it *look* like it does. JavaScript's actual inheritance model is **prototypal**: objects inherit directly from other, live objects. Understanding this matters because it explains real behavior you'll hit constantly — why `[].map` works on every array without every array carrying its own copy of `map`, why `instanceof` works the way it does, and why `class` in JavaScript sometimes behaves in ways that surprise people coming from classical OOP languages.

---

## 🧠 Key Ideas

- Every object has an internal, hidden link to another object called its **prototype**. When you access a property that doesn't exist directly on the object, JavaScript automatically looks it up on the prototype instead — and if it's not there either, on *that* object's prototype, and so on, forming the **prototype chain**, which terminates at `null`.
- This internal link is formally called `[[Prototype]]` (written with double brackets to indicate it's an internal slot, not a regular accessible property). You can read it with the standard `Object.getPrototypeOf(obj)`, and historically it was also exposed (non-standard, but supported everywhere) as the `__proto__` accessor property.
- Don't confuse `[[Prototype]]`/`__proto__` with a **function's own `.prototype` property** — these are different things that happen to share a name. Every ordinary function has a `.prototype` property (an actual object) that gets used specifically when that function is called with `new`: the newly created object's internal `[[Prototype]]` gets set to point at the constructor function's `.prototype` object.
- `class` syntax, introduced in ES6, is **syntactic sugar** — under the hood, a `class` is still a function, `class` methods still live on that function's `.prototype` object, and `extends` still just wires up the prototype chain the same way manual prototype-based code always did. It's a fundamentally different *look*, not a fundamentally different *mechanism*.
- `Object.create(proto)` lets you build an object with a specific prototype directly, with no constructor function or `new` involved at all — the purest, most literal expression of "delegate to this other object."
- Methods defined on a prototype are **shared by every instance** rather than duplicated onto each one, which is both a major memory-efficiency win and the reason mutating a shared prototype object at runtime affects every existing instance at once.

---

## ⚙️ How It Works

Picture a new employee (an object) who doesn't personally know every company policy off the top of their head. When they're asked something they don't know, they don't fail — they check with their direct manager (their `[[Prototype]]`). If the manager doesn't know either, the manager checks with *their* manager, and so on up the chain, until someone in that reporting line knows the answer, or you reach the top of the org chart (`null`) and the answer is simply "nobody knows this — it doesn't exist."

This is exactly what happens when you write `myArray.map(...)`. The array `myArray` doesn't have its own personal copy of a `map` method sitting on it — almost no built-in method does. JavaScript checks `myArray` itself, doesn't find `map`, checks its prototype (`Array.prototype`), *does* find `map` there, and uses that. Every array in your entire program delegates to that same single, shared `Array.prototype` object for all its methods — that's why they all behave consistently and why adding a new method to `Array.prototype` would make it instantly available on every array everywhere (a real, if now-frowned-upon, technique).

Now, the confusing part: a **function's own `.prototype` property** is a completely separate concept from that internal delegation link, even though it sounds identical. Think of a function's `.prototype` as a blank employee handbook that the function keeps in its desk, ready to be handed to any new hire it brings on board. When you call `new SomeFunction()`, JavaScript creates a fresh object and wires *that new object's* internal `[[Prototype]]` link to point at `SomeFunction.prototype` — the handbook gets handed to the new hire, becoming the thing *they* delegate to when they don't personally know something.

`class` syntax just gives you a cleaner way to write and hand out that handbook. `class Dog extends Animal { bark() {...} }` still creates a function under the hood, still puts `bark` on `Dog.prototype`, and still wires `Dog.prototype`'s own internal `[[Prototype]]` to point at `Animal.prototype` — creating the exact same chain of managers you'd get if you built it manually with `Object.create()`. `class` didn't introduce a new inheritance model to JavaScript; it made the existing one far more pleasant to read and write.

---

## 💻 Examples

```javascript
// The prototype chain in action — .map exists on Array.prototype, not on the array itself
const numbers = [1, 2, 3];
console.log(numbers.hasOwnProperty("map"));       // false — not directly on the array
console.log(Array.prototype.hasOwnProperty("map")); // true — lives on the shared prototype
console.log(Object.getPrototypeOf(numbers) === Array.prototype); // true — the actual link
```

```javascript
// Manual prototypal inheritance with a constructor function (the "old way")
function Animal(name) {
  this.name = name;
}
Animal.prototype.speak = function () {
  return `${this.name} makes a sound.`;
};

function Dog(name) {
  Animal.call(this, name); // borrow Animal's constructor logic
}
// Wire Dog.prototype's own internal [[Prototype]] to Animal.prototype
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;
Dog.prototype.bark = function () {
  return `${this.name} barks!`;
};

const rex = new Dog("Rex");
console.log(rex.speak()); // "Rex makes a sound." — found up the chain, on Animal.prototype
console.log(rex.bark());  // "Rex barks!" — found directly on Dog.prototype
```

```javascript
// The exact same behavior, expressed with class syntax (the "new way")
class AnimalClass {
  constructor(name) {
    this.name = name;
  }
  speak() {
    return `${this.name} makes a sound.`;
  }
}

class DogClass extends AnimalClass {
  bark() {
    return `${this.name} barks!`;
  }
}

const fido = new DogClass("Fido");
console.log(fido.speak()); // "Fido makes a sound." — same delegation as before
console.log(fido.bark());  // "Fido barks!"

// Proof that it's the same underlying mechanism:
console.log(typeof DogClass); // "function" — a class IS a function
console.log(Object.getPrototypeOf(DogClass.prototype) === AnimalClass.prototype); // true
```

```javascript
// Object.create(): building an object with an explicit prototype, no constructor at all
const vehiclePrototype = {
  describe() {
    return `A ${this.type} with ${this.wheels} wheels.`;
  },
};

const car = Object.create(vehiclePrototype);
car.type = "car";
car.wheels = 4;

console.log(car.describe()); // "A car with 4 wheels." — delegated straight to the prototype
console.log(car.hasOwnProperty("describe")); // false
console.log(Object.getPrototypeOf(car) === vehiclePrototype); // true
```

```javascript
// The gotcha: mutating a shared prototype affects every existing instance
function Widget() {}
const w1 = new Widget();
const w2 = new Widget();

Widget.prototype.sayHi = function () {
  return "hi!";
};

// Both instances immediately gain the new method, even though they were
// created BEFORE the method was added — because they delegate live, at
// lookup time, not at creation time.
console.log(w1.sayHi()); // "hi!"
console.log(w2.sayHi()); // "hi!"
```

---

## 🚀 Real World Applications

- Every built-in JavaScript type — `Array`, `Object`, `String`, `Function` — relies on this mechanism; understanding it demystifies "where do all these methods come from?"
- `class`-based component hierarchies in frameworks (older React class components, for instance) are prototypal inheritance under a cleaner syntax.
- Polyfills for older browsers work by adding methods directly onto a built-in `.prototype` (e.g., patching `Array.prototype.includes` before it was standardized).
- Mixins — sharing a set of methods across otherwise-unrelated classes — are implemented by manipulating the prototype chain directly.
- Debugging "why does this object have a method I never gave it" almost always resolves by walking up its prototype chain.

---

## ⚖️ Advantages

- Memory-efficient: methods live once on a shared prototype rather than being duplicated onto every single instance.
- Flexible: you can change what an object delegates to at runtime (`Object.setPrototypeOf`), something rigid class-based languages don't allow.
- `class` syntax gives most of the readability benefits of classical OOP while keeping the underlying flexibility of a live, delegation-based object model.
- Naturally supports dynamic extension — adding a method to a prototype makes it instantly available to every existing and future instance.

---

## ⚠️ Limitations

- The prototype chain adds a lookup cost — accessing a deeply inherited property is technically slower than accessing an object's own property, though this rarely matters in practice.
- `__proto__` is non-standard-but-universally-supported legacy syntax; `Object.getPrototypeOf()`/`Object.setPrototypeOf()` are the actual standardized way to interact with it, and mixing the two styles in one codebase gets confusing.
- Because prototypes are shared, mutating a prototype object's properties (not just adding methods, but shared mutable state) can produce surprising bugs where all instances are affected at once.
- `class` syntax's resemblance to classical OOP languages sets up false expectations — there's no true "private" state without newer syntax (`#privateFields`), and multiple inheritance isn't supported the way some other languages allow.

---

## 🚨 Common Mistakes

- Confusing a function's `.prototype` property with an object's internal `[[Prototype]]` link — they're related but distinct, and mixing them up is probably the single most common point of confusion in this whole topic.
- Directly mutating `Array.prototype` or `Object.prototype` in application code ("monkey-patching" built-ins), which can silently break other code (like `for...in` loops) that doesn't expect extra enumerable properties on every array or object.
- Assuming `class` in JavaScript is a completely separate, non-prototypal inheritance system, then being surprised when `Object.getPrototypeOf()` reveals it's built on exactly the same chain as manual prototype code.
- Forgetting to set `Dog.prototype.constructor = Dog` after manually reassigning `Dog.prototype = Object.create(Animal.prototype)`, which leaves `.constructor` incorrectly pointing at `Animal`.
- Using `for...in` to iterate an object's properties without an `.hasOwnProperty()` check, accidentally picking up enumerable properties inherited from further up the prototype chain.

---

## 📖 Further Reading

- MDN: "Object prototypes" and "Inheritance and the prototype chain"
- MDN: "Object.create()" and "Object.getPrototypeOf()"
- *You Don't Know JS: this & Object Prototypes* (Kyle Simpson)

---

## 💡 Wisdom from Mímir

I used to think of `class` and "prototypes" as two competing systems and picked whichever one a given codebase seemed to prefer. The real unlock was realizing there's only ever been one system — `class` is just a much nicer set of clothes on the exact same prototypal skeleton. Once you can see the skeleton through the clothes, nothing about JavaScript's object model surprises you anymore, `class` included.

---

## 🔗 Related Notes

- [[Closures and Scope]]
- [[The this Keyword]]
- [[Higher-Order Functions]]
