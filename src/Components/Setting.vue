<script setup lang="ts">
import { ref } from 'vue'
const back = ref(window.localStorage.getItem("back") || "")
function setting() {
    if (back.value == "") {
        alert("back is null")
        return
    }
    window.localStorage.setItem("back", back.value)
    alert("修改成功")
}
</script>

<template>
    <div id="container">
        <div id="main" class="radius">
            <input class="radius border" v-model="back" type="text" placeholder="后端">
            <button class="radius border" @click="setting">更改</button>
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
