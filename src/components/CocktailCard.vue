<script setup>
    import { ref, watch, watchEffect, reactive } from 'vue'

    defineProps(['title', 'id'])


    // const API_URL = `https://www.thecocktaildb.com/api/json/v1/`
    // const API_KEY = 1

    // const data = reactive({
    //     cocktails: null,
    //     ingredients: {},
    //     filteredDrinks: {},
    //     selectedIngredients: null,
    //     picked: []
    // })

    // const picked = ref(data.selectedIngredients)


    // async function filterByIngredient() {
    //     data.cocktails = null
    //     try {
    //         const res = await fetch(
    //             `https://www.thecocktaildb.com/api/json/v1/1/filter.php?i=${data.picked}`
    //         )
    //         data.cocktails = await res.json()
    //     } catch(err) {
    //         console.log('error', err)
    //     }
    // }

</script>

<template>

    <div class="card">
        <div class="card__body">
            <div class="card__header">
                {{ title }}
            </div>
        </div>
    </div>


</template>
