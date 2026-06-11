# Продвинутый JavaScript

Освоив продвинутый JavaScript, вы сможете создавать более крупные, масштабируемые и сложные веб-приложения. В этом руководстве разберем ключевые концепции: классы, промисы, `async/await` и сетевые запросы.

## 1. Классы (Classes)

Классы — это синтаксический сахар над прототипным наследованием. Они позволяют создавать объекты с четкой структурой и переиспользуемой логикой.

### Базовый синтаксис

```
class User {
  // Конструктор вызывается при создании нового экземпляра
  constructor(name, email) {
    this.name = name;
    this.email = email;
    this.createdAt = new Date();
  }

  // Метод класса
  greet() {
    return `Hello, I'm ${this.name}`;
  }

  // Геттер
  get userEmail() {
    return this.email;
  }

  // Сеттер
  set userEmail(newEmail) {
    if (!newEmail.includes('@')) {
      throw new Error('Invalid email');
    }
    this.email = newEmail;
  }
}

const user = new User('Alice', 'alice@example.com');
console.log(user.greet()); // Hello, I'm Alice
Наследование (extends)

class Admin extends User {
  constructor(name, email, permissions) {
    super(name, email); // Вызов конструктора родителя
    this.permissions = permissions;
  }

  deleteUser(userId) {
    console.log(`Admin ${this.name} deleted user ${userId}`);
  }

  // Переопределение метода
  greet() {
    return `${super.greet()} and I'm an admin`;
  }
}

const admin = new Admin('Bob', 'bob@admin.com', ['delete', 'ban']);
console.log(admin.greet()); // Hello, I'm Bob and I'm an admin
Статические методы и поля

class MathHelper {
  static PI = 3.14159;

  static multiply(a, b) {
    return a * b;
  }
}

console.log(MathHelper.multiply(5, 3)); // 15
console.log(MathHelper.PI); // 3.14159
2. Promises (Обещания)
Promise — объект, представляющий результат асинхронной операции, которая может быть выполнена сейчас, в будущем или никогда.

Состояния промиса
pending — ожидание

fulfilled — выполнено успешно

rejected — выполнено с ошибкой

Создание промиса

const fetchData = (shouldSucceed) => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (shouldSucceed) {
        resolve({ data: 'Success payload', status: 200 });
      } else {
        reject(new Error('Something went wrong'));
      }
    }, 1000);
  });
};

// Использование
fetchData(true)
  .then((result) => {
    console.log(result.data);
  })
  .catch((error) => {
    console.error(error.message);
  });
Цепочки промисов (Chaining)
javascript
const getUser = () => Promise.resolve({ id: 1, name: 'John' });
const getPosts = (userId) => Promise.resolve([`Post by ${userId}`, `Another post`]);
const getComments = (postId) => Promise.resolve(['Comment 1', 'Comment 2']);

getUser()
  .then(user => {
    console.log('User:', user);
    return getPosts(user.id);
  })
  .then(posts => {
    console.log('Posts:', posts);
    return getComments(posts[0]);
  })
  .then(comments => {
    console.log('Comments:', comments);
  })
  .catch(err => console.error('Error in chain:', err));
Параллельные промисы

const promise1 = Promise.resolve(3);
const promise2 = 42;
const promise3 = new Promise((resolve) => setTimeout(resolve, 100, 'foo'));

// Ждем все промисы
Promise.all([promise1, promise2, promise3])
  .then((values) => {
    console.log(values); // [3, 42, "foo"]
  });

// Ждем первый выполненный
Promise.race([promise1, promise2, promise3])
  .then((first) => {
    console.log(first); // 3
  });

// Ждем все, но не прерываемся при ошибках
Promise.allSettled([promise1, Promise.reject('error'), promise3])
  .then((results) => {
    console.log(results);
    // [{status: "fulfilled", value: 3}, {status: "rejected", reason: "error"}, ...]
  });
3. Async/Await
Синтаксический сахар над промисами, делающий асинхронный код похожим на синхронный.

Базовое использование

async function loadUserData() {
  try {
    const user = await getUser();    // ждем выполнения getUser()
    const posts = await getPosts(user.id);
    const comments = await getComments(posts[0]);
    
    return { user, posts, comments };
  } catch (error) {
    console.error('Failed to load:', error);
    throw error; // пробрасываем дальше
  }
}

// Вызов
loadUserData()
  .then(data => console.log('Complete:', data))
  .catch(err => console.error('Top level error:', err));
Обработка ошибок

// Вариант 1: try/catch
async function riskyOperation() {
  try {
    const result = await mightFail();
    return result;
  } catch (err) {
    console.log('Caught in function');
    return null;
  }
}

// Вариант 2: .catch на вызове
async function anotherWay() {
  const result = await mightFail().catch(err => {
    console.log('Fallback value');
    return { default: true };
  });
  return result;
}
Циклы с async/await
javascript
const urls = ['url1', 'url2', 'url3'];

// Последовательное выполнение
async function sequentialFetch(urls) {
  const results = [];
  for (const url of urls) {
    const data = await fetch(url);
    results.push(data);
  }
  return results;
}

// Параллельное выполнение
async function parallelFetch(urls) {
  const promises = urls.map(url => fetch(url));
  return await Promise.all(promises);
}
IIFE с async (самовызывающаяся функция)

(async () => {
  const data = await fetchData();
  console.log('IIFE result:', data);
})();
4. Сетевые запросы (HTTP Requests)
Fetch API (современный стандарт)
javascript
// GET запрос
async function fetchUsers() {
  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/users');
    
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    
    const users = await response.json(); // парсим JSON
    return users;
  } catch (error) {
    console.error('Fetch error:', error);
    throw error;
  }
}

// POST запрос
async function createPost(postData) {
  const response = await fetch('https://jsonplaceholder.typicode.com/posts', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': 'Bearer your-token-here'
    },
    body: JSON.stringify(postData)
  });
  
  if (!response.ok) throw new Error('POST failed');
  return await response.json();
}
Различные типы запросов
javascript
const api = {
  // PUT (полное обновление)
  async updateUser(id, data) {
    const res = await fetch(`/api/users/${id}`, {
      method: 'PUT',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(data)
    });
    return res.json();
  },
  
  // PATCH (частичное обновление)
  async patchUser(id, changes) {
    const res = await fetch(`/api/users/${id}`, {
      method: 'PATCH',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(changes)
    });
    return res.json();
  },
  
  // DELETE
  async deleteUser(id) {
    const res = await fetch(`/api/users/${id}`, {
      method: 'DELETE'
    });
    if (res.status === 204) return true;
    return res.json();
  }
};
Обработка разных форматов ответа

async function handleResponse(response) {
  const contentType = response.headers.get('content-type');
  
  if (contentType?.includes('application/json')) {
    return await response.json();
  }
  
  if (contentType?.includes('text/plain')) {
    return await response.text();
  }
  
  // Для бинарных данных (изображения, файлы)
  if (contentType?.includes('image/')) {
    return await response.blob();
  }
  
  return response;
}
Работа с FormData (загрузка файлов)
javascript
async function uploadFile(file) {
  const formData = new FormData();
  formData.append('file', file);
  formData.append('name', file.name);
  
  const response = await fetch('/upload', {
    method: 'POST',
    body: formData // Content-Type автоматически установится как multipart/form-data
  });
  
  return response.json();
}

// Использование с <input type="file">
document.querySelector('#fileInput').addEventListener('change', async (e) => {
  const file = e.target.files[0];
  const result = await uploadFile(file);
  console.log('Upload success:', result);
});
Таймауты и AbortController (отмена запроса)
javascript
function fetchWithTimeout(url, timeout = 5000) {
  const controller = new AbortController();
  const timeoutId = setTimeout(() => controller.abort(), timeout);
  
  return fetch(url, { signal: controller.signal })
    .finally(() => clearTimeout(timeoutId));
}

// Использование
async function searchWithTimeout() {
  try {
    const data = await fetchWithTimeout('/api/search', 3000);
    console.log(data);
  } catch (error) {
    if (error.name === 'AbortError') {
      console.log('Request was aborted (timeout)');
    }
  }
}

5. Практический пример: Weather App

class WeatherService {
  constructor(apiKey) {
    this.apiKey = apiKey;
    this.baseUrl = 'https://api.openweathermap.org/data/2.5';
  }
  
  async getWeather(city) {
    try {
      const response = await fetch(
        `${this.baseUrl}/weather?q=${city}&appid=${this.apiKey}&units=metric`
      );
      
      if (!response.ok) {
        if (response.status === 404) {
          throw new Error(`City "${city}" not found`);
        }
        throw new Error(`API error: ${response.status}`);
      }
      
      const data = await response.json();
      return {
        city: data.name,
        temperature: data.main.temp,
        condition: data.weather[0].description,
        humidity: data.main.humidity,
        windSpeed: data.wind.speed
      };
    } catch (error) {
      console.error('Weather fetch failed:', error);
      throw error;
    }
  }
  
  async getForecast(city, days = 5) {
    const response = await fetch(
      `${this.baseUrl}/forecast?q=${city}&appid=${this.apiKey}&units=metric&cnt=${days * 8}`
    );
    const data = await response.json();
    return this.processForecast(data);
  }
  
  processForecast(data) {
    // Группируем прогноз по дням
    const dailyForecast = {};
    data.list.forEach(item => {
      const date = item.dt_txt.split(' ')[0];
      if (!dailyForecast[date]) {
        dailyForecast[date] = [];
      }
      dailyForecast[date].push(item);
    });
    
    return Object.entries(dailyForecast).map(([date, items]) => ({
      date,
      avgTemp: items.reduce((sum, i) => sum + i.main.temp, 0) / items.length,
      condition: items[0].weather[0].description
    }));
  }
}

// Использование
async function demoWeatherApp() {
  const weather = new WeatherService('YOUR_API_KEY');
  
  try {
    const current = await weather.getWeather('London');
    console.log('Current weather:', current);
    
    const forecast = await weather.getForecast('London', 3);
    console.log('3-day forecast:', forecast);
  } catch (error) {
    console.error('Weather app error:', error.message);
  }
}
