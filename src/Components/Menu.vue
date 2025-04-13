<script setup lang="ts">
const data = [{
    "text": "上传", "link": "/"
},
{ "text": "登录", "link": "/login" }, {
    "text": "设置", "link": "/setting"
}, { "text": "面板", "link": "/dashboard" }
]
function toLink(link: string) {
    if (window.location.pathname != link) {
        window.location.href = link
    }
}
</script>

<template>
    <div id="container">
        <button class="btn" v-for="item in data" @click="toLink(item.link)">{{ item.text }}</button>
    </div>
</template>

<style scoped>
#container {
    position: fixed;
    right: 0;
    top: 0;
    display: flex;
    flex-direction: column;
}

.btn {
    border: 1px solid rgba(0, 0, 0, .5);
    border-radius: 5px;
    margin-right: 5px;
    margin-top: 5px;
    width: 80px;
    height: 30px;
}
</style>
