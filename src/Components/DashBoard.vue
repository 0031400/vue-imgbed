<script setup lang="ts">
import type { RefSymbol } from '@vue/reactivity';
import { ref } from 'vue'
class Item {
    name: string;
    childs: Item[];
    isDir: boolean;
    parents: Item[];
    constructor(name: string, childs: Item[], isDir: boolean, parents: Item[]) {
        this.name = name;
        this.childs = childs;
        this.isDir = isDir;
        this.parents = parents;
    }
}
class ItemInfo {
    name: string;
    isDir: boolean;
    constructor(name: string, isDir: boolean) {
        this.name = name
        this.isDir = isDir
    }
}
const back = window.localStorage.getItem("back") || ""
const nowItem = ref<Item>(new Item("", [], true, []))
reflesh()
function choose(item: Item) {
    if (item.isDir) {
        nowItem.value = item
        if (nowItem.value.childs.length == 0) {
            reflesh()
        }
    } else {
        nowImg.value = item
    }
}
const nowImg = ref<Item>()
function getRequestBody(item: Item): string {
    let tempList: string[] = []
    for (let i = 0; i < item.parents.length; i++) {
        const e = item.parents[i]
        if (e.name) {
            tempList.push(e.name)
        }
    }
    tempList.push(item.name)
    return '["' + tempList.join('","') + '"]'
}
function reflesh() {
    fetch(back + "/list", {
        method: "POST",
        headers: {
            "Authorization": window.localStorage.getItem("token") || ""
        },
        body: getRequestBody(nowItem.value)
    }).then(async res => {
        if (res.status == 401) {
            alert("未登录")
            return
        }
        const jsonData = await res.json() as { code: number; data: ItemInfo[] }
        if (res.status != 200 || jsonData.code != 0) {
            alert(jsonData.data)
            return
        }

        let tempList: Item[] = []
        let tempBool: boolean
        jsonData.data.forEach(a => {
            tempBool = true
            for (let i = 0; i < nowItem.value.childs.length; i++) {
                const b = nowItem.value.childs[i]
                if (a.name == b.name) {
                    tempList.push(b)
                    tempBool = false
                    break
                }
            }
            if (tempBool) {
                tempList.push(new Item(a.name, [], a.isDir, [...nowItem.value.parents, nowItem.value]))
            }
        })
        nowItem.value.childs = tempList
    })
}
function getImgThumbLink(item: Item): string {
    let tempList: string[] = []
    for (let i = 0; i < item.parents.length; i++) {
        const e = item.parents[i];
        if (e.name) {
            tempList.push(e.name)
        }
    }
    return back + "/thumbnail/" + tempList.join('/') + "/" + item.name
}
function getImgWebpLink(item: Item): string {
    let tempList: string[] = []
    for (let i = 0; i < item.parents.length; i++) {
        const e = item.parents[i];
        if (e.name) {
            tempList.push(e.name)
        }
    }
    return back + "/i/" + tempList.join('/') + "/" + item.name.split('.')[0] + ".webp"
}
function getImgMdLink(item: Item): string {
    return '![img](' + getImgWebpLink(item) + ')'
}
const message = ref<string>()
async function clip(text: string) {
    await navigator.clipboard.writeText(text)
    message.value = "粘贴至剪贴板"
}
async function submitDelete(i: Item) {
    if (!i) {
        return
    }
    fetch(back + "/delete", {
        method: "POST",
        headers: {
            "Authorization": window.localStorage.getItem("token") || ""
        }, body: getRequestBody(i)
    }).then(async res => {
        if (res.status == 401) {
            alert("未登录")
            return
        }
        const jsonData = await res.json() as { code: number; data: string }
        if (res.status != 200 || jsonData.code != 0) {
            alert(jsonData.data)
            return
        }
        message.value = "删除成功"
        reflesh()
    })
}
</script>
<template>
    <div id="container">
        <div id="main">
            <div id="up"><button @click="choose(i)" v-for="i in nowItem.parents">{{ i.name || "/" }}</button><button>{{
                nowItem.name || "/"
                    }}</button><button @click="reflesh">刷新</button>
            </div>
            <div id="down">
                <div @click="choose(i)" class="item" v-for="i in nowItem.childs">
                    <template v-if="i.isDir">
                        {{ i.name }} <img src="/folder.svg" alt="" class="item-pic">
                    </template>
                    <template v-else>
                        <img :src="getImgThumbLink(i)" alt="" class="item-pic">
                    </template>
                </div>
            </div>
            <div id="detail" v-if="nowImg">
                <div id="datail-name">
                    {{ nowImg.name }}
                    <button @click="submitDelete(nowImg)">删除</button>
                </div>
                <div id="detail-link">
                    {{ getImgWebpLink(nowImg) }}
                    <button @click="clip(getImgWebpLink(nowImg))">剪贴</button>
                </div>
                <div id="detail-md">
                    {{ getImgMdLink(nowImg) }}
                    <button @click="clip(getImgMdLink(nowImg))">剪贴</button>
                </div>
                <div ref="messageDiv" id="message">
                    {{ message }} </div>
            </div>
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

#up {
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
}

#up button {
    width: 40px;
    height: 30px;
    margin-right: 5px;
}

#down {
    display: grid;
    grid-template-columns: repeat(auto-fill, 110px);
    width: 500px;
}

.item {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
}

.item-pic {
    width: 100px;
    height: 100px;
}
</style>