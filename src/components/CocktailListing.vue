<script setup>
    import { ref, watch, watchEffect, reactive } from 'vue'
    import TheForm from '@/components/TheForm.vue'
    import TheResults from '@/components/TheResults.vue'

    const API_URL = `https://www.thecocktaildb.com/api/json/v1/`
    const API_KEY = 1

    const data = reactive({
        isLoading: false,
        ingredientsList: [],
        filteredDrinks: {},
        picked: []
    });

    function getIngredients() {
        fetch(`${API_URL}/${API_KEY}/list.php?i=list`)
            .then(response => response.json())
            .then(ingredients => {
                data.ingredientsList = ingredients.drinks.map(obj => obj.strIngredient1)
            })
    }

    getIngredients()


    // // const picked = ref(data.selectedIngredients)

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


    // watchEffect(async () => {
    //     const url = `${API_URL}${currentBranch.value}`
    //     commits.value = await (await fetch(url)).json()
    // })

</script>

<template>
    <div class="cocktails">
        <TheForm
            :ingredients="data.ingredientsList"
        />
        <TheResults
            :isLoading="data.isLoading"
        />
    </div>
</template>
