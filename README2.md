# 📘 Основы работы с JavaScript

> Фундаментальные знания, необходимые для back-end (Node.js) и front-end (React/Vanilla JS) разработки.

## 1. 🏗️ Переменные и типы данных

### Объявление переменных
```javascript
var oldWay = "Устарело";   // функциональная область видимости
let mutable = "Можно менять"; // блочная область видимости
const constant = "Не меняется"; // константа (блочная)
Примитивные типы
javascript
const string = "Hello";     // строка
const number = 42;          // число (целое и с плавающей точкой)
const bigInt = 9007199254740991n;
const boolean = true;       // true/false
const empty = null;         // "ничего" (объектный тип)
let notDefined;             // undefined (значение не присвоено)
const unique = Symbol("id"); // уникальный идентификатор
Ссылочные типы
javascript
const obj = { name: "JS", year: 1995 };  // объект
const arr = [1, 2, 3];                  // массив (разновидность объекта)
function foo() {}                        // функция

2. 🔄 Управление потоком выполнения
Условные операторы
javascript
// if/else
if (score > 90) {
    console.log("A");
} else if (score > 75) {
    console.log("B");
} else {
    console.log("C");
}

// switch
const fruit = "apple";
switch (fruit) {
    case "apple":
        console.log("🍎");
        break;
    default:
        console.log("?");
}

// Тернарный оператор
const access = age >= 18 ? "granted" : "denied";
Циклы
javascript
// for классический
for (let i = 0; i < 5; i++) {
    console.log(i);
}

// for...of (для массивов, строк, Map, Set)
const colors = ["red", "green", "blue"];
for (const color of colors) {
    console.log(color);
}

// for...in (для ключей объекта)
const person = { name: "Alex", age: 30 };
for (const key in person) {
    console.log(key, person[key]);
}

// while
let count = 0;
while (count < 3) {
    count++;
}

// do...while
let x = 0;
do {
    x++;
} while (x < 0);
3. 📦 Функции: основа основ
Function Declaration (всплывает)
javascript
function sum(a, b) {
    return a + b;
}
Function Expression
javascript
const multiply = function(a, b) {
    return a * b;
};
Стрелочные функции (ES6+)
javascript
const divide = (a, b) => a / b;

// с блоком кода
const process = (value) => {
    const result = value * 2;
    return result;
};

// без аргументов
const greet = () => "Hello";
Параметры по умолчанию и rest
javascript
function greet(name = "Guest") {
    return `Hi, ${name}`;
}

function sumAll(...numbers) {  // rest-оператор
    return numbers.reduce((acc, n) => acc + n, 0);
}
4. 🧩 Объекты и массивы (работа 80% времени)
Объекты
javascript
const user = {
    name: "John",
    age: 25,
    "likes JS": true,     // ключ с пробелом
    sayHi() {              // метод
        console.log(this.name);
    }
};

// Доступ
user.name;          // John
user["likes JS"];   // true

// Деструктуризация
const { name, age } = user;
Массивы
javascript
const fruits = ["apple", "banana", "orange"];

// Основные методы (мутирующие)
fruits.push("grape");     // добавить в конец
fruits.pop();             // удалить последний
fruits.unshift("mango");  // добавить в начало
fruits.shift();           // удалить первый

// Немутирующие (создают новый массив)
const upper = fruits.map(f => f.toUpperCase());
const long = fruits.filter(f => f.length > 5);
const found = fruits.find(f => f === "banana");

// Редукция
const total = [1, 2, 3].reduce((acc, val) => acc + val, 0);
5. 🧵 Асинхронность (ключ к Node.js и браузеру)
Callback (традиционный, но ведёт к "callback hell")
javascript
fs.readFile("file.txt", (err, data) => {
    if (err) throw err;
    console.log(data);
});
Promise (современнее)
javascript
const fetchData = () => {
    return new Promise((resolve, reject) => {
        setTimeout(() => resolve("Done"), 1000);
    });
};

fetchData()
    .then(data => console.log(data))
    .catch(error => console.error(error));
Async/Await (рекомендуется)
javascript
async function getData() {
    try {
        const result = await fetchData();
        console.log(result);
    } catch (error) {
        console.error(error);
    }
}
Параллельное выполнение
javascript
const [user, posts] = await Promise.all([
    fetchUser(),
    fetchPosts()
]);

const winner = await Promise.race([fast, slow]); // первый выполнившийся
6. 📐 Прототипы и классы (ООП в JS)
Классы (синтаксический сахар)
javascript
class Animal {
    constructor(name) {
        this.name = name;
    }
    
    speak() {
        console.log(`${this.name} makes a sound`);
    }
    
    static description() {
        return "This is an animal class";
    }
}

class Dog extends Animal {
    constructor(name, breed) {
        super(name);   // вызов родительского конструктора
        this.breed = breed;
    }
    
    speak() {          // переопределение
        console.log(`${this.name} barks`);
    }
}
Прототипное наследование (как под капотом)
javascript
const parent = { type: "mammal" };
const child = Object.create(parent);
child.name = "Baby";
console.log(child.type); // "mammal" (берёт из прототипа)
7. 🌐 Важное для окружения
Браузер (front-end)
javascript
// DOM-манипуляции
const element = document.querySelector("#myId");
element.textContent = "Новый текст";
element.addEventListener("click", () => {
    console.log("Clicked!");
});

// Хранилище
localStorage.setItem("key", "value");
const saved = localStorage.getItem("key");

// Fetch API
const response = await fetch("https://api.example.com/data");
const json = await response.json();
Node.js (back-end)
javascript
// Модули (CommonJS)
const fs = require("fs");
const express = require("express");

// ES Modules (современный)
import { readFile } from "fs/promises";

// HTTP сервер
const http = require("http");
const server = http.createServer((req, res) => {
    res.writeHead(200, { "Content-Type": "text/plain" });
    res.end("Hello World\n");
});
server.listen(3000);
8. ✅ Продвинутые основы (обязательно знать)
Замыкание (closure)
javascript
function createCounter() {
    let count = 0;
    return function() {
        count++;
        return count;
    };
}
const counter = createCounter();
console.log(counter()); // 1
console.log(counter()); // 2
this (контекст выполнения)
javascript
const obj = {
    name: "Object",
    regularFunc: function() { console.log(this.name); },
    arrowFunc: () => { console.log(this.name); } // this не свой
};

// Привязка
function log() { console.log(this.value); }
const bound = log.bind({ value: 42 });
bound(); // 42
