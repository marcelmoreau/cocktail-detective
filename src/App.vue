<script>
    import { RouterView } from 'vue-router'
    import TheMasthead from './components/TheMasthead.vue'

    export default {
        components: {
            TheMasthead
        }
    }
</script>

<template>
    <TheMasthead />

    <RouterView />
</template>
