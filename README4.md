# 🧩 Основы компонентов Vue.js

> Компоненты — это строительные блоки любого Vue-приложения. Они позволяют разбить интерфейс на независимые, переиспользуемые части.

## 1. 🎯 Что такое компонент?

Компонент — это самодостаточный фрагмент UI со своей логикой, шаблоном и стилями.

```vue
<!-- UserCard.vue - один файл = один компонент -->
<template>
    <div class="user-card">
        <img :src="user.avatar" :alt="user.name">
        <h3>{{ user.name }}</h3>
        <p>{{ user.email }}</p>
        <button @click="followUser">Follow</button>
    </div>
</template>

<script setup>
import { ref } from 'vue'

const props = defineProps(['user'])
const isFollowing = ref(false)

const followUser = () => {
    isFollowing.value = true
    console.log(`Following ${props.user.name}`)
}
</script>

<style scoped>
.user-card {
    border: 1px solid #ddd;
    border-radius: 8px;
    padding: 16px;
}
</style>
2. 📦 Регистрация компонентов
Автоматическая регистрация (рекомендуется)
vue
<!-- ComponentA.vue -->
<template>
    <ComponentB /> <!-- Работает автоматически -->
</template>

<script setup>
import ComponentB from './ComponentB.vue'
// Не нужно ничего регистрировать — импорт уже всё делает
</script>
Глобальная регистрация (редко)
javascript
// main.js
import { createApp } from 'vue'
import App from './App.vue'
import GlobalButton from './components/GlobalButton.vue'

const app = createApp(App)
app.component('GlobalButton', GlobalButton) // Теперь доступен везде
app.mount('#app')
3. 📤 Props — передача данных
Базовое использование
vue
<!-- Parent.vue -->
<template>
    <ChildComponent 
        :title="pageTitle"
        :count="42"
        :is-active="true"
        :user="currentUser"
    />
</template>

<!-- ChildComponent.vue -->
<script setup>
// Простой синтаксис
const props = defineProps(['title', 'count', 'isActive', 'user'])

// Валидация (рекомендуется)
const props = defineProps({
    title: {
        type: String,
        required: true,
        default: 'Default Title'
    },
    count: {
        type: Number,
        default: 0,
        validator: (value) => value >= 0
    },
    isActive: Boolean,
    user: {
        type: Object,
        required: true,
        // Для объектов/массивов default должен быть функцией
        default: () => ({ name: 'Guest', age: 0 })
    },
    tags: {
        type: Array,
        default: () => []
    }
})

// Доступ к props
console.log(props.title)
</script>
Однонаправленный поток данных
vue
<script setup>
const props = defineProps(['initialCount'])

// ❌ НЕЛЬЗЯ мутировать props напрямую
// props.initialCount++

// ✅ Создаём локальную копию
const localCount = ref(props.initialCount)

// ✅ Или отправляем событие родителю
const increment = () => {
    emit('update:count', localCount.value + 1)
}
</script>
4. 📡 Events — общение с родителем
Базовые события
vue
<!-- Child.vue -->
<script setup>
const emit = defineEmits(['update', 'delete', 'close'])

const saveData = () => {
    // Отправка события с данными
    emit('update', { id: 1, name: 'New Name' })
}

const removeItem = () => {
    emit('delete', 123)
}
</script>

<template>
    <button @click="saveData">Save</button>
    <button @click="removeItem">Delete</button>
    <button @click="emit('close')">Close</button>
</template>

<!-- Parent.vue -->
<template>
    <ChildComponent 
        @update="handleUpdate"
        @delete="handleDelete"
        @close="isOpen = false"
    />
</template>

<script setup>
const handleUpdate = (data) => {
    console.log('Update received:', data)
}
</script>
v-model на компонентах (двустороннее связывание)
vue
<!-- CustomInput.vue -->
<script setup>
defineProps(['modelValue'])
defineEmits(['update:modelValue'])

// Вручную
const updateValue = (event) => {
    emit('update:modelValue', event.target.value)
}
</script>

<template>
    <input 
        :value="modelValue"
        @input="$emit('update:modelValue', $event.target.value)"
    />
</template>

<!-- Использование -->
<template>
    <CustomInput v-model="searchQuery" />
    <!-- Эквивалентно: -->
    <CustomInput 
        :modelValue="searchQuery"
        @update:modelValue="searchQuery = $event"
    />
</template>
Несколько v-model (Vue 3)
vue
<!-- UserForm.vue -->
<script setup>
defineProps({
    firstName: String,
    lastName: String
})

defineEmits(['update:firstName', 'update:lastName'])
</script>

<template>
    <input 
        :value="firstName"
        @input="$emit('update:firstName', $event.target.value)"
        placeholder="First name"
    />
    <input 
        :value="lastName"
        @input="$emit('update:lastName', $event.target.value)"
        placeholder="Last name"
    />
</template>

<!-- Использование -->
<template>
    <UserForm 
        v-model:first-name="user.first"
        v-model:last-name="user.last"
    />
</template>
5. 🎭 Слоты — гибкое содержимое
Базовые слоты
vue
<!-- Card.vue -->
<template>
    <div class="card">
        <div class="card-header">
            <slot name="header">Default Header</slot>
        </div>
        
        <div class="card-body">
            <slot>Default body content</slot>
        </div>
        
        <div class="card-footer">
            <slot name="footer">Default Footer</slot>
        </div>
    </div>
</template>

<!-- Использование -->
<template>
    <Card>
        <template #header>
            <h2>Custom Title</h2>
        </template>
        
        <template #default>
            <p>This is the main content</p>
            <button>Click me</button>
        </template>
        
        <template #footer>
            <small>Last updated: today</small>
        </template>
    </Card>
</template>
Scoped Slots (передача данных в слот)
vue
<!-- DataTable.vue -->
<template>
    <div class="table">
        <div v-for="item in data" :key="item.id" class="row">
            <slot name="row" :item="item" :index="index">
                <!-- Fallback отображение -->
                {{ item.name }}
            </slot>
        </div>
    </div>
</template>

<script setup>
defineProps(['data'])
</script>

<!-- Использование с кастомным рендерингом -->
<template>
    <DataTable :data="users">
        <template #row="{ item, index }">
            <div class="custom-row">
                <span>{{ index + 1 }}.</span>
                <strong>{{ item.name }}</strong>
                <span>({{ item.email }})</span>
            </div>
        </template>
    </DataTable>
</template>
6. 🔄 Component Refs (шаблонные ссылки)
Доступ к DOM и компонентам
vue
<script setup>
import { ref, onMounted } from 'vue'
import ChildComponent from './ChildComponent.vue'

// Создаём ref с таким же именем, как атрибут ref в шаблоне
const inputRef = ref(null)
const childRef = ref(null)

onMounted(() => {
    // Доступ к DOM-элементу
    inputRef.value.focus()
    
    // Доступ к публичным методам/свойствам дочернего компонента
    console.log(childRef.value.somePublicMethod())
})

// Определяем публичные методы для родителя
defineExpose({
    focusInput: () => inputRef.value?.focus(),
    reset: () => {
        console.log('Reset called from parent')
    }
})
</script>

<template>
    <input ref="inputRef" type="text" />
    <ChildComponent ref="childRef" />
</template>
7. 🏗️ Композиция компонентов
Динамические компоненты
vue
<template>
    <button 
        v-for="tab in tabs" 
        :key="tab.name"
        @click="currentTab = tab.component"
    >
        {{ tab.name }}
    </button>
    
    <!-- Динамический рендеринг -->
    <component :is="currentTab" :some-prop="sharedData" />
    
    <!-- С сохранением состояния -->
    <KeepAlive>
        <component :is="currentTab" />
    </KeepAlive>
</template>

<script setup>
import { shallowRef } from 'vue'
import HomePage from './HomePage.vue'
import ProfilePage from './ProfilePage.vue'
import SettingsPage from './SettingsPage.vue'

const tabs = [
    { name: 'Home', component: shallowRef(HomePage) },
    { name: 'Profile', component: shallowRef(ProfilePage) },
    { name: 'Settings', component: shallowRef(SettingsPage) }
]

const currentTab = shallowRef(HomePage)
</script>
Асинхронные компоненты (ленивая загрузка)
vue
<script setup>
import { defineAsyncComponent } from 'vue'

// Ленивая загрузка
const AsyncModal = defineAsyncComponent(() => 
    import('./HeavyModal.vue')
)

// С состояниями загрузки/ошибки
const AsyncComponent = defineAsyncComponent({
    loader: () => import('./BigComponent.vue'),
    loadingComponent: LoadingSpinner,
    errorComponent: ErrorDisplay,
    delay: 200,          // задержка показа loading-компонента
    timeout: 3000        // таймаут загрузки
})
</script>

<template>
    <AsyncModal v-if="showModal" />
</template>
8. 🧬 Граничные случаи
Provide / Inject (для глубоких вложений)
vue
<!-- GrandParent.vue -->
<script setup>
import { provide, ref } from 'vue'

// Предоставляем данные всем потомкам
const theme = ref('dark')
const updateTheme = (newTheme) => {
    theme.value = newTheme
}

provide('theme', theme)
provide('updateTheme', updateTheme)
</script>

<!-- DeepChild.vue (любой уровень вложенности) -->
<script setup>
import { inject } from 'vue'

// Инъекция с значением по умолчанию
const theme = inject('theme', 'light')
const updateTheme = inject('updateTheme')

const toggleTheme = () => {
    updateTheme(theme.value === 'dark' ? 'light' : 'dark')
}
</script>

<template>
    <div :class="theme">
        Current theme: {{ theme }}
        <button @click="toggleTheme">Toggle</button>
    </div>
</template>
Рекурсивные компоненты
vue
<!-- TreeView.vue -->
<script setup>
defineProps({
    nodes: {
        type: Array,
        required: true
    }
})
</script>

<template>
    <ul>
        <li v-for="node in nodes" :key="node.id">
            <span>{{ node.name }}</span>
            <!-- Рекурсивный вызов -->
            <TreeView 
                v-if="node.children?.length" 
                :nodes="node.children"
            />
        </li>
    </ul>
</template>
9. 🎨 Стилизация компонентов
Scoped стили
vue
<template>
    <div class="container">
        <h1 class="title">Scoped Title</h1>
    </div>
</template>

<style scoped>
/* Стили только для этого компонента (с атрибутом data-v-xxxx) */
.container {
    padding: 20px;
}

.title {
    color: #42b883;
}

/* Глубокие селекторы (для влияния на дочерние компоненты) */
:deep(.child-class) {
    background: red;
}

/* Слотовые стили */
:slotted(.slotted-class) {
    font-weight: bold;
}
</style>

<style>
/* Глобальные стили (без scoped) */
.global-class {
    margin: 0;
}
</style>
CSS Modules
vue
<template>
    <div :class="$style.container">
        <h1 :class="[$style.title, $style.bold]">Styled with CSS Modules</h1>
    </div>
</template>

<style module>
.container {
    padding: 20px;
    border: 1px solid #ccc;
}

.title {
    font-size: 24px;
}

.bold {
    font-weight: bold;
}
</style>

<!-- Кастомное имя модуля -->
<style module="customName">
/* .customName.container */
</style>
10. 🧪 Тестирование компонентов
Пример с Vitest
javascript
// UserCard.test.js
import { mount } from '@vue/test-utils'
import UserCard from './UserCard.vue'

describe('UserCard', () => {
    it('renders user name', () => {
        const user = { name: 'John Doe', email: 'john@example.com' }
        const wrapper = mount(UserCard, {
            props: { user }
        })
        
        expect(wrapper.text()).toContain('John Doe')
        expect(wrapper.html()).toContain('john@example.com')
    })
    
    it('emits follow event when button clicked', async () => {
        const wrapper = mount(UserCard, {
            props: { user: { name: 'Test' } }
        })
        
        await wrapper.find('button').trigger('click')
        expect(wrapper.emitted()).toHaveProperty('follow')
        expect(wrapper.emitted().follow[0]).toEqual([expect.any(Object)])
    })
})
