<template>
    <header :class="{scrolled: isScrolled}">
        <div class="wrapper">
            <router-link to="/" class="logo-link">
                <img src="https://commons.wikimedia.org/wiki/File:Vue.js_Logo_2.svg" alt="Logo" class="logo" />
            </router-link>
            <nav>
                <router-link to="/posts">Posts</router-link>
            </nav>
        </div>
    </header>
</template>


<style scoped>
header {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    padding: 2rem 1rem;
    backdrop-filter: blur(10px);
    transition: .3s;
}
header.scrolled {
    background-color: rgba(255, 255, 255, 0.8);
    box-shadow: 0 0 10px 10px rgba( 0 0 0 / .1);
    padding: 1rem;
}

.wrapper {
    max-width: 1200px;
    margin: 0 auto;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.logo {
    height: 40px;
}

nav {
    display: flex;
    gap: 1rem;
}

nav a {
    text-decoration: none;
    color: #333;
}
nav a:hover
{
    text-decoration: underline;
}

nav a.router-link-exact-active {
    font-weight: bold;
}
</style>


<script setup lang=""ts>

import {ref, onMounted, onUnmounted} from 'vue';

const isScrolled = ref(false)

function handleScroll() {
    isScrolled.value = window.scrollY > 0;
}

onMounted(() => {
    window.addEventListener('scroll', handleScroll);
})
onUnmounted(() => {
    window.removeEventListener('scroll', handleScroll);
})

</script>