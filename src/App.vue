<script setup>
    import { RouterLink, RouterView } from 'vue-router'
    import TheMasthead from './components/TheMasthead.vue'
    import CocktailListing from './components/CocktailListing.vue';
</script>

<template>
    <TheMasthead />

    <RouterView />
</template>

