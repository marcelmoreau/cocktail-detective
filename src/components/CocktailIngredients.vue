<!-- <script setup>
    // import axios from 'axios'
    import ListingCard from './ListingCard.vue';

    export default {
        components: {
            ListingCard
        },
        data() {
            return {
                API_URL: 'www.thecocktaildb.com/api/json/v1',
                API_KEY: 1,
                cocktails: null,
                filters: null,
                ingredients: null
            }
        },
        mounted() {
            this.getIngredients
        },
        created() {
            // axios.get(
            //     'https://www.thecocktaildb.com/api/json/v1/1/filter.php?c=Cocktail'
            // ).then(response => {
            //     this.cocktails = response.data

            // })
            // .catch(error => {
            //     console.log(error)
            // })


        },
        methods: {
            async getIngredients() {
                return 'foo'
            }
        }
    }

</script>

<template>

    <CocktailIngredients />



    <div class="listing">
        <div class="listing__body">
            <h1 class="heading masthead__heading">
                Results…
            </h1>
            <div class="listing__wrapper">

                <ul class="listing__list">
                    <li
                        v-for="cocktail in cocktails.drinks"
                        :key="cocktail.idDrink"
                        :cocktail="cocktail"
                        class="listing__listItem"
                    >
                        <div class="card">
                            <div class="card__body">
                                <div class="card__header">
                                    {{ cocktail.strDrink }}
                                </div>
                                <div class="card__torso">
                                    torso
                                </div>
                                <div class="card__footer">
                                    foot
                                </div>
                            </div>
                        </div>
                    </li>
                </ul>

            </div>
        </div>
    </div>
</template>


<div class="filters">
    <button>Foo</button>
</div> -->