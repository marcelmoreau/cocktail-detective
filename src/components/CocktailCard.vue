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
            <div class="card__wrapper">
                <div class="card__media">
                    <img class="card__image" src="https://picsum.photos/700/700" alt="">
                </div>
                <div class="card__content">
                    <div class="card__header">
                        <div class="card__heading">
                            {{ title }}
                        </div>
                    </div>
                    <div class="card__torso">
                        <div class="card__torsoModule">
                            <div class="card__subheading">
                                Ingredients
                            </div>
                            <ul class="card__list">
                                <li class="card__listItem">
                                    Lorem ipsum dolor sit amet
                                </li>
                                <li class="card__listItem">
                                    Dolor lorem hello world
                                </li>
                                <li class="card__listItem">
                                    Angostura bitters, to taste
                                </li>
                                <li class="card__listItem">
                                    More ingredient here, thanks
                                </li>
                            </ul>
                        </div>
                        <div class="card__torsoModule">
                            <div class="card__subheading">
                                Directions
                            </div>
                            <p>
                                Lorem ipsum dolor sit amet consectetur adipisicing elit. Delectus at quis officia ea libero obcaecati aliquam.
                            </p>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>


</template>
