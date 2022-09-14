<script setup>
    import ListingCard from './ListingCard.vue'
    import { ref, watch, watchEffect, reactive } from 'vue'

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


    async function callbackk() {
        data.cocktails = null
        console.log('zz2')
        const res = await fetch(
            `https://www.thecocktaildb.com/api/json/v1/1/filter.php?i=${data.picked}`
        )
        data.cocktails = await res.json();
    }


    watch(
        () => data.picked, callbackk
    )

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

    // async function filterByIngredient(item) {
    //     try {
    //         const res = await fetch(
    //             `https://www.thecocktaildb.com/api/json/v1/1/filter.php?i=${item}`
    //         )
    //         data.ingredients = await res.json()
    //     } catch(err) {
    //         console.log('errorz', err)
    //     }
    // }


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
                    type="checkbox"
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
                            damn
                        </div>
                        <ul v-else class="listing__list">
                            <li
                                v-for="cocktail in data.cocktails.drinks"

                                class="listing__listItem"
                            >
                                <div class="card">
                                    <div class="card__body">
                                        <div class="card__header">
                                            {{ cocktail.strDrink }}
                                        </div>
                                    </div>
                                </div>
                            </li>
                        </ul>

                    </div>
                </div>
            </div>
        </div>
    </div>

</template>
