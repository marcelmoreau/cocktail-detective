<script setup>
    import { reactive, watch } from 'vue'
    import TheForm from '@/components/TheForm.vue'
    import TheResults from '@/components/TheResults.vue'

    const API_URL = `https://www.thecocktaildb.com/api/json/v2/`
    const API_KEY = ***REMOVED***

    const data = reactive({
        isLoading: false,
        ingredientsPicked: null,
        ingredientsOptions: [],
        filteredDrinks: [],
        error: null
    });

    // populate ingredients in multiselect's options list
    async function populateIngredients() {
        const response = await fetch(`${API_URL}/${API_KEY}/list.php?i=list`)
        if (response.ok) {
            const json = await response.json()
            data.ingredientsOptions = json.drinks.map(obj => obj.strIngredient1)
        } else {
            this.error = "The Detective is drunk. Try again."
        }
    }

    populateIngredients()


    watch(
        () => data.ingredientsPicked,
        async (picked) => {

            data.isLoading = true

            let ingredients = picked.join(',')

            const response = await fetch(`${API_URL}/${API_KEY}/filter.php?i=${ingredients}`)

            // console.log(response.type)

            if (response.ok) {
                const json = await response.json()
                // console.log()
                data.filteredDrinks = Array.isArray(json.drinks) ? json.drinks: []
                data.isLoading = false
            } else {
                // console.log(response)
                this.error = "WATCH The Detective is drunk. Try again."
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
