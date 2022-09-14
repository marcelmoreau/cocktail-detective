<script setup>
    import { RouterLink, RouterView } from 'vue-router'
    import HelloWorld from './components/HelloWorld.vue'
    import TheMasthead from './components/TheMasthead.vue'
    import CocktailListing from './components/CocktailListing.vue';
</script>

<template>
    <TheMasthead />

    <nav>
        <RouterLink to="/">Home</RouterLink>
        <RouterLink to="/about">About</RouterLink>
    </nav>


    <RouterView />
</template>

