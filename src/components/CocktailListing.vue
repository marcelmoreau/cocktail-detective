<script setup>
    import { ref, watch, watchEffect, reactive } from 'vue'

    import CocktailCard from './CocktailCard.vue'


    const API_URL = `https://www.thecocktaildb.com/api/json/v1/`
    const API_KEY = 1

    const data = reactive({
        cocktails: null,
        ingredients: {},
        filteredDrinks: {},
        selectedIngredients: null,
        picked: []
    })

    // const picked = ref(data.selectedIngredients)


    async function filterByIngredient() {
        data.cocktails = null
        try {
            const res = await fetch(
                `https://www.thecocktaildb.com/api/json/v1/1/filter.php?i=${data.picked}`
            )
            data.cocktails = await res.json()
        } catch(err) {
            console.log('error', err)
        }
    }


    watch(
        () => data.picked, filterByIngredient
    )


    // Lookup full cocktail details by id
    // www.thecocktaildb.com/api/json/v1/1/lookup.php?i=11007

    // watchEffect(async () => {
    //     const url = `${API_URL}${currentBranch.value}`
    //     commits.value = await (await fetch(url)).json()
    // })


    async function getIngredients() {
        try {
            const res = await fetch(
                `${API_URL}/${API_KEY}/list.php?i=list`
            )
            data.ingredients = await res.json()
        } catch(err) {
            console.log('error', err)
        }
    }

    getIngredients()

</script>

<template>

    <div class="torso">
        <div class="torso__wrapper">
            <div class="filters torso__filters">
                <div
                    v-for="(ingredient, index) in data.ingredients.drinks"
                    class="filters__item"
                >
                    <input
                        :id="`filters__item--${index}`"
                        :value="ingredient.strIngredient1"
                        v-model="data.picked"
                        class="filters__check"
                        type="radio"
                    >
                    <label
                        :for="`filters__item--${index}`"
                        class="filters__label"
                    >
                        {{ ingredient.strIngredient1 }}
                    </label>
                </div>
            </div>

            <div class="listing torso__listing">
                <div class="listing__body">
                    <h1 class="heading listing__heading">
                        Results…
                    </h1>
                    <div class="listing__wrapper">
                        <div v-if="!data.cocktails">
                            no cocktails found
                        </div>
                        <ul v-else class="listing__list">
                            <li
                                v-for="cocktail in data.cocktails.drinks"
                                class="listing__listItem"
                            >
                                <CocktailCard
                                    :title="cocktail.strDrink"
                                    :id="cocktail.idDrink"
                                />
                            </li>
                        </ul>

                    </div>
                </div>
            </div>
        </div>
    </div>

</template>
