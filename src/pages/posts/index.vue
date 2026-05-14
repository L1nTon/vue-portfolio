<template>
    <h1>Posts</h1>
    <div id="filter">
        <h2>Filter</h2>
        <div class="filters-wrapper">
            <select name="userList" id="userList" v-model="filter.userId">
                <option v-for="userId in usersList" :value="userId" :key="userId">User {{ userId }}</option>
            </select>
            <button class="clear-button" :class="{hidden: !filter.userId}" @click="clearFilter()">
                <img src="https://cdn-icons-png.flaticon.com/512/458/458595.png" alt="">
            </button>
        </div>
    </div>
    <div class="loading" v-if="postLoading">
        <span></span>
    </div>
    <div class="posts-wrapper" v-else>
        <router-link v-for="post in posts" :to="`/posts/${post.id}`" class="card" :key="post.id">
            <h2>{{ post.title }}</h2>
            <span class="post-id" >PostID: {{ post.id }}</span>
            <br>
            <span class="user-id" >User: {{ post.userId }}</span>
            <p>{{ post.body }}</p>
        </router-link>
    </div>
</template>

<style scoped>
h1
{
    text-align: center;
    margin: 1rem 0;
}
span
{
    font-size: 14px;
    color: #777;
}
.posts-wrapper
{
    width: 100%;
    display: flex;
    flex-direction: column;
    /* align-items: center; */
    justify-content: center;
    gap: .5rem;
    margin: 2rem 0;
}
.filters-wrapper
{
    display: flex;
    /* justify-content: center; */
    align-items: center;
    gap: 5px;
}
.card {
    width: 100%;
    border: 1px solid #ccc;
    border-radius: 4px;
    padding: 1rem;
    cursor: pointer;
    transition: .3s;
    color: #000;
    text-decoration: none;
}
.card:hover
{
    box-shadow: 0 2.5px 5px 5px rgba( 0 0 0 / .1);
}
.clear-button
{
    width: 25px;
    height: 25px;
    opacity: 1;
    visibility: visible;
    cursor: pointer;
}
.clear-button.hidden
{
    opacity: 0;
    visibility: hidden;
}
.clear-button img
{
    width: 100%;
}
#userList
{
    width: 250px;
}
.loading
{
    width: 100%;
    min-height: 250px;
    display: flex;
    justify-content: center;
    align-items: center;
}
.loading span
{
    width: 100px;
    height: 100px;
    border: 1px solid #0005;
    border-inline: 4px solid #0005;
    border-radius: 50%;
    animation: spin 2s linear infinite;
}

@keyframes spin{
    from{
        transform: rotate(0deg);
    }
    to{
        transform: rotate(360deg);
    }
}
</style>

<script setup lang="ts">

import { api } from '@/api/axios';
import { ref, watch } from 'vue';

const usersList = ref<number[]>([]);
const postLoading = ref<boolean>(true)

const filter = ref<{
    userId?: number | undefined;
}>({})



const posts = ref<{
    id: number | undefined,
    userId: number | undefined,
    title: string | undefined,
    body: string | undefined,
}[]>([])

function fetchPosts(){
    api.get('posts', {
        params: filter.value
    }).then((response) => {
        posts.value = response.data
        if (usersList.value.length === 0) {
            usersList.value = [...new Set<number>(response.data.map((item: any) => item.userId))];
        }
        postLoading.value = false;
    })
}
watch(filter, () => {
    postLoading.value = true;
    fetchPosts()
},
    { deep: true }
);
fetchPosts()

function clearFilter(){
    filter.value = {}
}

</script>