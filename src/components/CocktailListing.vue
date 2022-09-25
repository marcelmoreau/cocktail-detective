<script setup>
    import { reactive, watch } from 'vue'
    import TheForm from '@/components/TheForm.vue'
    import TheResults from '@/components/TheResults.vue'

    const API_URL = `https://www.thecocktaildb.com/api/json/v2/`
    const API_KEY = ***REMOVED***

    const data = reactive({
        ingredientsPicked: null,
        isLoading: false,
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

    // async getDropdownList(listType){
    //     const response = await fetch(`/jazzhr${listType}.json`)
    //     if(response.ok){
    //         const jsonReturn = await response.json()
    //         this[`all${listType}`] = jsonReturn.data
    //     }
    //     else {
    //         this.errorMessage = "Issues pulling options."
    //     }
    // },
    // async getCareers(){
    //     this.loading = true
    //     const response = await fetch(this.requestURL)
    //     if (response.ok) {
    //         const latestPull = await response.json()
    //         if (latestPull.length < this.maxPerPull) {
    //             this.moreToPull = false
    //         }
    //         this.jobs.push(...latestPull)
    //     } else {
    //         this.errorMessage = "Issue pulling latest careers."
    //     }

    //     this.loading = false
    // },


    watch(
        () => data.ingredientsPicked,
        async (picked) => {
            data.isLoading = true

            let ingredients = picked.join(',')

            const response = await fetch(`${API_URL}/${API_KEY}/filter.php?i=${ingredients}`)

            if (response.ok) {
                const json = await response.json()
                data.filteredDrinks = json.drinks
            } else {
                this.error = "WATCH The Detective is drunk. Try again."
            }

            data.isLoading = false
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
