<script setup lang="ts">
import { shallowRef } from "vue";
import Upload from './Components/Upload.vue'
import Login from './Components/Login.vue'
import Setting from './Components/Setting.vue'
import Menu from './Components/Menu.vue'
import Dashboard from './Components/Dashboard.vue'
const path = window.location.pathname
const currentComponent = shallowRef()
window.localStorage.setItem("back", "http://127.0.0.1:8080")
switch (path) {
    case '/':
        currentComponent.value = Upload
        break;
    case '/login':
        currentComponent.value = Login
        break;
    case '/setting':
        currentComponent.value = Setting
        break;
    case '/dashboard':
        currentComponent.value = Dashboard
        break;
    default:
        currentComponent.value = Upload
        break;
}
</script>

<template>
    <component :is="currentComponent" />
    <Menu />
</template>

<style scoped></style>
