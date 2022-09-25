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
        filteredDrinks: [],
    });


    // populate ingredients in multiselect's options list
    function populateIngredients() {
        fetch(`${API_URL}/${API_KEY}/list.php?i=list`)
            .then(response => response.json())
            .then(ingredients => {
                data.ingredientsOptions = ingredients.drinks.map(obj => obj.strIngredient1)
            })
    }

    populateIngredients()



    watch(
        () => data.ingredientsPicked,
        (picked) => {
            // data.isLoading = true

            let ingredients = picked.join(',')

            if (ingredients.length) {
                fetch(`${API_URL}/${API_KEY}/filter.php?i=${ingredients}`)
                    .then(response => response.json())
                    .then(cocktails => {
                        data.filteredDrinks = cocktails.drinks
                    })
                }
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
            :filteredDrinks="data.filteredDrinks"
        />
    </div>
</template>
