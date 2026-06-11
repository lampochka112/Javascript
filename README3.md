# 🟢 Знакомство с Vue.js

> Прогрессивный JavaScript-фреймворк для создания пользовательских интерфейсов.   
> Изучаем основы: от подключения до реактивности и компонентов.

## 1. 🚀 Быстрый старт

### Подключение Vue (CDN)
```html
<!DOCTYPE html>
<html>
<head>
    <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
</head>
<body>
    <div id="app">
        {{ message }}
    </div>
    
    <script>
        const { createApp, ref } = Vue;
        
        createApp({
            setup() {
                const message = ref('Hello Vue!');
                return { message };
            }
        }).mount('#app');
    </script>
</body>
</html>
Через Vite (рекомендуется для проектов)
bash
npm create vue@latest my-app
cd my-app
npm install
npm run dev
2. 🧩 Основные концепции
Реактивность (Composition API - современный подход)
vue
<script setup>
import { ref, reactive, computed } from 'vue'

// Примитивы через ref()
const count = ref(0)           // .value в JS, в шаблоне автоматически
const message = ref('Hello')

// Объекты через reactive() (или ref)
const user = reactive({
    name: 'John',
    age: 25
})

// Вычисляемые свойства
const doubleCount = computed(() => count.value * 2)

// Методы
function increment() {
    count.value++
}
</script>

<template>
    <p>{{ count }} × 2 = {{ doubleCount }}</p>
    <p>{{ user.name }} is {{ user.age }}</p>
    <button @click="increment">+1</button>
</template>
Options API (классический стиль)
vue
<script>
export default {
    data() {
        return {
            count: 0,
            user: { name: 'John', age: 25 }
        }
    },
    computed: {
        doubleCount() {
            return this.count * 2
        }
    },
    methods: {
        increment() {
            this.count++
        }
    }
}
</script>
3. 🎨 Директивы и шаблонизация
Основные директивы
vue
<template>
    <!-- Интерполяция -->
    <h1>{{ title }}</h1>
    <p v-text="rawHtml"></p>
    <div v-html="htmlContent"></div>
    
    <!-- Привязка атрибутов -->
    <img v-bind:src="imageUrl" alt="Image">
    <img :src="imageUrl" alt="Image"> <!-- сокращение -->
    <button :disabled="isDisabled">Submit</button>
    
    <!-- Классы и стили -->
    <div :class="{ active: isActive, 'text-bold': isBold }">Dynamic class</div>
    <div :class="[activeClass, errorClass]">Array syntax</div>
    <div :style="{ color: textColor, fontSize: fontSize + 'px' }">Inline style</div>
    
    <!-- Условный рендеринг -->
    <div v-if="isVisible">Visible content</div>
    <div v-else-if="isLoading">Loading...</div>
    <div v-else>Not visible</div>
    <div v-show="isVisible">Always in DOM (display: none)</div>
    
    <!-- Списки -->
    <ul>
        <li v-for="(item, index) in items" :key="item.id">
            {{ index }}: {{ item.name }}
        </li>
    </ul>
    
    <!-- События -->
    <button v-on:click="handleClick">Click</button>
    <button @click="handleClick">Click me</button>
    <button @click="count++">Inline</button>
    <input @keyup.enter="submit" placeholder="Press Enter">
    
    <!-- Двустороннее связывание -->
    <input v-model="username" placeholder="Your name">
    <p>Hello, {{ username }}!</p>
    
    <!-- Модификаторы событий -->
    <form @submit.prevent="onSubmit">Prevents page reload</form>
    <button @click.stop="doThis">Stop propagation</button>
    <button @click.once="doOnce">Only once</button>
</template>
4. 🧬 Компоненты - сердце Vue
Определение компонента
vue
<!-- ChildComponent.vue -->
<template>
    <div class="card">
        <h3>{{ title }}</h3>
        <p>{{ content }}</p>
        <button @click="emit('close')">Close</button>
    </div>
</template>

<script setup>
// Props (передача данных от родителя)
const props = defineProps({
    title: {
        type: String,
        required: true
    },
    content: {
        type: String,
        default: 'No content'
    }
})

// Events (отправка событий родителю)
const emit = defineEmits(['close', 'update'])
</script>
Использование компонента
vue
<!-- ParentComponent.vue -->
<template>
    <ChildComponent 
        title="Welcome" 
        :content="dynamicContent"
        @close="handleClose"
    />
    
    <!-- Слоты (передача HTML) -->
    <Card>
        <template #header>
            <h2>Custom Header</h2>
        </template>
        
        <template #default>
            <p>Main content goes here</p>
        </template>
        
        <template #footer>
            <button>Save</button>
        </template>
    </Card>
</template>

<script setup>
import ChildComponent from './ChildComponent.vue'
import Card from './Card.vue'

const dynamicContent = ref('Some text')
const handleClose = () => console.log('Closed')
</script>
Коммуникация между компонентами
javascript
// 1. Props / Events (родитель ⇄ ребенок)
// 2. Provide / Inject (для глубоких деревьев)
// 3. Pinia (глобальное состояние)

// Provide / Inject
// Grandparent.vue
<script setup>
import { provide, ref } from 'vue'
const sharedData = ref('Shared value')
provide('sharedKey', sharedData)
</script>

// DeepChild.vue
<script setup>
import { inject } from 'vue'
const data = inject('sharedKey')
</script>
5. 🔄 Жизненный цикл компонента
vue
<script setup>
import { onMounted, onUpdated, onUnmounted } from 'vue'

console.log('setup() - инициализация')

onMounted(() => {
    // Компонент добавлен в DOM
    console.log('Mounted - можно работать с DOM')
    fetchData()
})

onUpdated(() => {
    // Компонент перерендерен
    console.log('Updated - реактивные данные изменились')
})

onUnmounted(() => {
    // Компонент удалён из DOM
    console.log('Unmounted - очистить таймеры/слушатели')
})
</script>
6. 🗂️ Управление состоянием (Pinia)
Установка
bash
npm install pinia
Store определение
javascript
// stores/counter.js
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'

export const useCounterStore = defineStore('counter', () => {
    // State
    const count = ref(0)
    const name = ref('Vue Store')
    
    // Getters (computed)
    const doubleCount = computed(() => count.value * 2)
    
    // Actions (methods)
    function increment() {
        count.value++
    }
    
    function reset() {
        count.value = 0
    }
    
    return { count, name, doubleCount, increment, reset }
})
Использование в компоненте
vue
<script setup>
import { useCounterStore } from '@/stores/counter'
import { storeToRefs } from 'pinia'

const store = useCounterStore()

// Для сохранения реактивности (деструктуризация с storeToRefs)
const { count, doubleCount } = storeToRefs(store)
const { increment, reset } = store // actions можно деструктурировать напрямую
</script>

<template>
    <p>Count: {{ count }}</p>
    <p>Double: {{ doubleCount }}</p>
    <button @click="increment">+1</button>
    <button @click="reset">Reset</button>
</template>
7. 🌐 Маршрутизация (Vue Router)
Установка
bash
npm install vue-router@4
Настройка маршрутов
javascript
// router/index.js
import { createRouter, createWebHistory } from 'vue-router'
import HomePage from '@/views/HomePage.vue'
import AboutPage from '@/views/AboutPage.vue'

const routes = [
    { path: '/', component: HomePage, name: 'home' },
    { path: '/about', component: AboutPage, name: 'about' },
    { 
        path: '/users/:id', 
        component: () => import('@/views/UserProfile.vue') // Lazy loading
    }
]

const router = createRouter({
    history: createWebHistory(),
    routes
})

export default router
Использование в приложении и компонентах
vue
<!-- main.js -->
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'

createApp(App).use(router).mount('#app')

<!-- Компонент с навигацией -->
<template>
    <nav>
        <RouterLink to="/">Home</RouterLink>
        <RouterLink :to="{ name: 'about' }">About</RouterLink>
        <RouterLink :to="`/users/${userId}`">User</RouterLink>
    </nav>
    
    <!-- Место рендеринга текущего маршрута -->
    <RouterView />
</template>

<script setup>
import { useRoute, useRouter } from 'vue-router'

const route = useRoute()   // текущий маршрут
const router = useRouter() // для навигации

// Программная навигация
function goToUser(id) {
    router.push(`/users/${id}`)
    // или router.push({ name: 'user', params: { id } })
}

const userId = route.params.id
</script>
8. ✨ Композиция и переиспользование
Composables (кастомная логика)
javascript
// composables/useFetch.js
import { ref } from 'vue'

export function useFetch(url) {
    const data = ref(null)
    const error = ref(null)
    const loading = ref(false)
    
    const fetchData = async () => {
        loading.value = true
        try {
            const response = await fetch(url)
            data.value = await response.json()
        } catch (e) {
            error.value = e
        } finally {
            loading.value = false
        }
    }
    
    fetchData()
    
    return { data, error, loading, refetch: fetchData }
}

// Использование в компоненте
<script setup>
import { useFetch } from './composables/useFetch'

const { data: posts, loading, error } = useFetch('https://api.example.com/posts')
</script>
