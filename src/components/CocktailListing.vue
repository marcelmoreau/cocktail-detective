<script setup>
    import { reactive, watch } from 'vue'
    import TheForm from '@/components/TheForm.vue'
    import TheResults from '@/components/TheResults.vue'

    const API_URL = `https://www.thecocktaildb.com/api/json/v1/`
    const API_KEY = 1

    const data = reactive({
        ingredientsPicked: null,
        isLoading: false,
        ingredientsOptions: [],
        filteredDrinks: {},
    });

    function getIngredients() {
        fetch(`${API_URL}/${API_KEY}/list.php?i=list`)
            .then(response => response.json())
            .then(ingredients => {
                data.ingredientsOptions = ingredients.drinks.map(obj => obj.strIngredient1)
            })
    }

    getIngredients()


    // https://www.thecocktaildb.com/api/json/v1/1/filter.php?i=Gin


    watch(
        () => data.ingredientsPicked,
        (picked) => {
            // data.isLoading = true

            let explode = picked.join(',')

            fetch(`${API_URL}/${API_KEY}/filter.php?i=${explode}`)
                .then(response => response.json())
                .then(cocktails => {
                    console.log(cocktails)
                })

            console.log(explode)
        }
    )

</script>

<template>
    <div class="cocktails">
        <TheForm
            v-model="data.ingredientsPicked"
            :ingredients="data.ingredientsOptions"
        />
        <TheResults
            :isLoading="data.isLoading"
        />
    </div>
</template>
