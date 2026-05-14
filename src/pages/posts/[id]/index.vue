<template>
    <div class="post-details">
        <div v-if="post" class="post-content">
            <h1>{{ post.title }}</h1>
            <div class="post-meta">
                <span><strong>Post ID:</strong> {{ postId }}</span>
                <span><strong>User ID:</strong> {{ post.userId }}</span>
            </div>
            <p class="post-body">{{ post.body }}</p>
        </div>
        <div v-else class="loading">
            <p>Loading post...</p>
        </div>
        <button v-if="!post" @click="fetchPost" class="fetch-btn">Load Post</button>
    </div>
    <div class="post-comments">
        <h2>Comments</h2>
        <div class="comment-wrapper">
            <div v-if="comments" v-for="comment in comments" class="comment">
                <div class="comment-header">
                    <h3>{{ comment.name }}</h3>
                    <span>{{ comment.email }}</span>
                </div>
                <div class="comment-body">
                    <p>
                        {{ comment.body }}
                    </p>
                </div>
            </div>
            <div v-else class="empty-comments">
                <p>Тут пока ничего нет...</p>
            </div>
        </div>
    </div>
</template>

<style scoped>
.post-details {
    background: #fff;
    border-radius: 8px;
    padding: 2rem;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.post-content h1 {
    margin: 0 0 1rem 0;
    color: #333;
}

.post-meta {
    display: flex;
    gap: 2rem;
    margin-bottom: 1.5rem;
    padding-bottom: 1rem;
    border-bottom: 1px solid #eee;
    font-size: 0.9rem;
    color: #666;
}

.post-body {
    line-height: 1.6;
    color: #555;
    margin: 1.5rem 0;
}

.loading {
    text-align: center;
    color: #999;
    padding: 2rem;
}

.fetch-btn {
    background: #42b983;
    color: white;
    border: none;
    padding: 0.75rem 1.5rem;
    border-radius: 4px;
    cursor: pointer;
    font-size: 1rem;
    transition: .3s;
}

.fetch-btn:hover {
    background: #369970;
}
.post-comments
{
    padding: 1.5rem;
}
.comment-wrapper
{
    display: flex;
    flex-direction: column;
    gap: 1rem;
    margin-top: 1rem;
}
.empty-comments
{
    width: 100%;
    text-align: center;
    margin: 2rem;
}
.comment
{
    box-shadow: 0 2px 5px 5px rgba(0 0 0 / .1);   
    padding: 1rem;
    border-radius: 5px;
}
.comment-header
{
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
}
.comment-header h3
{
    font-size: 18px;

}
.comment-header span
{
    background-color: #42b983;
    border-radius: 50px;
    padding: 5px 15px;
    color: #fff;
}
.comment-body p
{
    font-size: 14px;
    color: #777;
}
</style>

<script setup lang="ts">
import { ref } from 'vue';
import { useRoute } from 'vue-router';
import { api } from '@/api/axios';

const route = useRoute();
const postId = ref<number>(0);
const post = ref<any>(null);
const comments = ref<{
    postId: number | undefined,
    id: number | undefined,
    name: string | undefined,
    email: string | undefined,
    body: string | undefined
}[]>([]);

postId.value = Number(route.params.id);

function consoleError(error: any){
    console.error('Error fetching post:', error);
}
function fetchPost() {
    api.get(`posts/${postId.value}`).then((response) => {
        post.value = response.data;
    }).catch((error) => consoleError(error));
}

function fetchComments() {
    api.get(`posts/${postId.value}/comments`).then((response) => {
        comments.value = response.data;
    }).catch((error) => consoleError(error));
}
// Auto-fetch on mount
fetchPost();
fetchComments();
</script>