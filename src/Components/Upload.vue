<script setup lang="ts">
import { ref } from 'vue';
const fileInput = ref<HTMLInputElement>()
const fileUpload = ref<File>()
const drag = ref<HTMLDivElement>()
const defaultButtonText = "上传"
const defaultText = "文件为空"
const buttonText = ref<string>(defaultButtonText)
const text = ref<string>(defaultText)
const back = window.localStorage.getItem("back") || ""
function choose() {
    if (fileInput.value) {
        textToDefault()
        fileInput.value.click()
    }
}
function textToDefault() {
    buttonText.value = defaultButtonText
    text.value = defaultText
}
function handleDrap(e: DragEvent) {
    textToDefault()
    e.preventDefault()
    if (e.dataTransfer && e.dataTransfer.files.length > 0 && fileInput.value && fileInput.value.files) {
        const theFile = e.dataTransfer.files[0]
        fileUpload.value = theFile
        text.value = theFile.name
    }

}
function handleFileUpload(e: Event) {
    const input = e.target as HTMLInputElement
    const file = input.files?.[0]
    if (file) {
        fileUpload.value = file
        text.value = file.name
    }
}
function submit() {
    const formData = new FormData()
    formData.append("file", fileUpload.value || "")
    fetch(back + '/upload', {
        method: "POST",
        headers: {
            "Authorization": window.localStorage.getItem("token") || ""
        }, body: formData
    }).then(async (res) => {
        if (res.status == 401) {
            window.location.href = "/login"
            alert("未登录")
            return
        }
        const jsonData = await res.json() as { code: number; data: string }
        if (res.status != 200 || jsonData.code != 0) {
            alert(jsonData.data)
            return
        }


        text.value = back + "/i/" + jsonData.data
        buttonText.value = "成功"
    })
}
async function clip() {
    if (text.value == defaultText) {
        return
    }
    await navigator.clipboard.writeText(text.value)
    buttonText.value = "粘贴至剪贴板"
}
</script>

<template>
    <div id="container">
        <div id="main">
            <input type="file" @change="handleFileUpload" ref="fileInput" style="display: none;">
            <div ref="drag" @dragover.prevent @drop="handleDrap" id="drag" @click="choose" class="border radius">拖拉至此处
            </div>
            <button id="submit" @click="submit" class="border radius">{{ buttonText }}</button>
            <div @click="clip">{{ text }}</div>
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
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
}

#drag {
    width: 200px;
    height: 100px;
    background-color: rgb(247, 241, 241);
    line-height: 100px;
    text-align: center;
}

#drag,
#submit {
    margin-bottom: 5px;
}

#submit {
    width: 200px;
    height: 50px;
}
</style>
