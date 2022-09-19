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


    // watch(
    //     () => data.picked, filterByIngredient
    // )


    // Lookup full cocktail details by id
    // www.thecocktaildb.com/api/json/v1/1/lookup.php?i=11007

    // watchEffect(async () => {
    //     const url = `${API_URL}${currentBranch.value}`
    //     commits.value = await (await fetch(url)).json()
    // })


    // async function getIngredients() {
    //     try {
    //         const res = await fetch(
    //             `${API_URL}/${API_KEY}/list.php?i=list`
    //         )
    //         data.ingredients = await res.json()
    //     } catch(err) {
    //         console.log('error', err)
    //     }
    // }

    // getIngredients()

</script>

<template>
    <div class="cocktail-listing">
        <div class="cocktail-listing__wrapper">

            <div class="cards">
                <div class="cards__wrapper">
                    <div class="cards__item">
                        <CocktailCard
                            class="cards__card"
                            title="Hello world lorem ipsum doalr mangahttn and then last call amat"
                        />
                    </div>
                    <div class="cards__item">
                        <CocktailCard
                            class="cards__card"
                            title="Hello world"
                        />
                    </div>
                    <div class="cards__item">
                        <CocktailCard
                            class="cards__card"
                            title="Hello world"
                        />
                    </div>
                    <div class="cards__item">
                        <CocktailCard
                            class="cards__card"
                            title="Hello world"
                        />
                    </div>
                    <div class="cards__item">
                        <CocktailCard
                            class="cards__card"
                            title="Hello world"
                        />
                    </div>
                </div>
            </div>

            <div class="listing torso__listing">
                <div class="listing__body">
                    <h1 class="heading listing__heading">
                        Results…
                    </h1>
                    <div class="listing__wrapper">
                        <div>
                            no cocktails found
                        </div>
                        <ul class="listing__list">
                            <li
                                class="listing__listItem"
                            >
                                <CocktailCard
                                    title="Hello world"
                                />

                            </li>
                        </ul>

                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
