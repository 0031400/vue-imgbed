<script setup lang="ts">
import { ref } from 'vue'
const token = ref(window.localStorage.getItem("token") || "")
function login() {
    if (token.value == "") {
        alert("token is null")
        return
    }
    window.localStorage.setItem("token", token.value)
    window.location.pathname = "/"
}
</script>

<template>
    <div id="container">
        <div id="main" class="radius">
            <input class="radius border" v-model="token" type="text" placeholder="token">
            <button class="radius border" @click="login">登录</button>
        </div>
    </div>
</template>

<style scoped>
.radius {
    border-radius: 5px;
}

.border {
    border: 1px solid rgba(0, 0, 0, 0.3);
}

#container {
    width: 100vw;
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    background-image: linear-gradient(120deg, #a1c4fd 0%, #c2e9fb 100%);
}

#main {
    background: rgba(255, 255, 255, .5);
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    padding: 20px 20px;
}


input,
button {
    font-size: 20px;
}

input {
    background-color: rgba(255, 255, 255, .5);
    outline: 0;
    padding: 0 5px;
    line-height: 30px;
    margin-bottom: 20px;
    width: 190px;
}

button {
    width: 200px;
    height: 50px;
    background-color: rgba(157, 218, 229, 0.3);

}


button:hover {
    background-color: rgba(157, 218, 229, 0.5);
}

button:active {
    background-color: rgba(157, 218, 229, 1);
}
</style>
