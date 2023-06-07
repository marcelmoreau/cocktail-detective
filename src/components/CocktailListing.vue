<script>
    import { computed, watch } from 'vue'
    import Multiselect from '@vueform/multiselect'
    import gsap from 'gsap'
import TheMasthead from './TheMasthead.vue'

    const API_URL = `https://www.thecocktaildb.com/api/json/v2`
    const API_KEY = ***REMOVED***

    export default {
        components: {
            Multiselect
        },
        data() {
            return {
                isLoading: false,
                ingredientsPicked: [],
                ingredientsList: [],
                foundDrinks: [],
                totalDrinks: null,
                noResults: false,
                resultsMax: 8,
                multiselectClasses: {
                    option: 'multiselect-option the-form__option',
                    tagRemove: 'multiselect-tag-remove the-form__tagRemove'
                },
            }
        },

        methods: {
            async getIngredients() {
                const response = await fetch(`${API_URL}/${API_KEY}/list.php?i=list`)
                const json = await response.json()

                this.ingredientsList = json.drinks.map(obj => obj.strIngredient1)
            },

            async fetchCocktails(...ingredients) {
                this.isLoading = true

                const response = await fetch(`${API_URL}/${API_KEY}/filter.php?i=${ingredients}`)
                const jsonSummary = await response.json()

                if (jsonSummary.drinks instanceof Array) {
                    this.foundDrinks = jsonSummary.drinks

                    const delay = ms => new Promise(resolve => setTimeout(resolve, ms));

                    for (const [index, drink] of this.foundDrinks.entries()) {

                        const details = await fetch(`${API_URL}/${API_KEY}/lookup.php?i=${drink.idDrink}`)
                        const jsonDetails = await details.json()

                        this.foundDrinks[index]['instructions'] = jsonDetails.drinks[0]['strInstructions']

                        const pullIngredients = function(arr) {
                            return Object.entries(arr[0])
                                .filter(([k,v]) => k.match(/^str(Ingredient|Measure)\d+$/) && v)
                                .reduce((acc, [k, v]) => {
                                    const [t, n] = k.match(/^str(Ingredient|Measure)(\d+)$/).slice(1);
                                    acc[n-1] = {...acc[n-1], [t]:v};
                                return acc;
                            }, [])
                        }

                        this.foundDrinks[index]['ingredients'] = pullIngredients(jsonDetails.drinks)

                        await delay(30)
                    }

                    this.isLoading = false

                } else {
                    this.noResults = true
                }
            },

            loadMore() {
                if (this.resultsMax > this.foundDrinks) {
                    return
                }
                this.resultsMax = this.resultsMax + this.resultsMax
            },

            onBeforeEnter(el) {
                el.style.opacity = 0
            },

            onEnter(el, done) {
                gsap.to(el, {
                    opacity: 1,
                    delay: el.dataset.index * 0.025,
                    onComplete: done
                })
            },

            onLeave(el, done) {
                gsap.to(el, {
                    opacity: 0,
                    delay: el.dataset.index * 0.025,
                    onComplete: done
                })
            },

            trimTrailingSpace(str) {
                if (str.length || '') {
                    return str.replace(/\s+$/, '')
                } else {
                    return
                }
            }
        },

        computed: {
            drinks() {
                return this.foundDrinks.slice(0, this.resultsMax)
            }
        },


        created() {
            this.getIngredients()
        },

        watch: {
            ingredientsPicked(ingredientsPicked, ingredientsOld) {
                if (ingredientsPicked.length) {
                    this.fetchCocktails(...ingredientsPicked)
                }
            }
        }

    }
</script>

<template>
    <div class="cocktails">

        <div class="form the-form">
            <div class="the-form__wrapper">
                <div class="form__field the-form__formField">
                    <div class="the-form__labelWrapper">
                        <label class="form__label the-form__label" for="form">
                            What ingredients do you have?
                        </label>
                    </div>
                    <Multiselect
                        v-model="ingredientsPicked"
                        @clear="ingredientsPicked = []"
                        mode="tags"
                        :close-on-select="false"
                        :searchable="true"
                        :options="ingredientsList"
                        :classes="multiselectClasses"
                        class="the-form__control the-form__control--select"
                        id="form"
                    >

                        <template #caret>
                            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" class="the-form__caret">
                                <path d="M19.5 5.25l-7.5 7.5-7.5-7.5m15 6l-7.5 7.5-7.5-7.5" />
                            </svg>
                        </template>

                    </Multiselect>
                </div>
            </div>
        </div>

        <div v-if="foundDrinks.length && !isLoading">
            <div class="the-results">
                <div class="the-results__wrapper">
                    <div class="the-results__headingWrapper">
                        <h2 class="heading the-results__heading">
                            You could potentially make <strong>{{ foundDrinks.length }}</strong> {{ foundDrinks.length > 1 ? 'cocktails' : 'cocktail' }}
                        </h2>
                    </div>
                </div>
                <TransitionGroup
                    :css="false"
                    tag="ul"
                    @before-enter="onBeforeEnter"
                    @enter="onEnter"
                    @leave="onLeave"
                    class="the-results__list"
                >
                    <li
                        v-for="(cocktail, index) in drinks"
                        :key="cocktail.idDrink"
                        :data-index="index"
                        class="the-results__listItem"
                    >
                        <div class="card">
                            <div class="card__body">
                                <div class="card__wrapper">
                                    <div class="card__media">
                                        <img
                                            :src="cocktail.strDrinkThumb"
                                            alt=""
                                            class="card__image"
                                            loading="lazy"
                                        >
                                    </div>
                                    <div class="card__content">
                                        <div class="card__header">
                                            <div class="card__heading">
                                                {{ cocktail.strDrink }}
                                            </div>
                                        </div>
                                        <div class="card__torso">
                                            <div class="card__torsoModule">
                                                <div class="card__subheading">
                                                    Ingredients
                                                </div>

                                                <ul class="card__list">
                                                    <li
                                                        v-for="item in cocktail.ingredients"
                                                        class="card__listItem"
                                                    >
                                                        <span v-if="item.Measure">
                                                            <span class="card__listItemUnit" v-html="trimTrailingSpace(`${item.Measure}`)"></span>
                                                        </span>
                                                        <span>{{ item.Ingredient }}</span>
                                                    </li>
                                                </ul>
                                            </div>

                                            <div class="card__torsoModule">
                                                <div class="card__subheading">
                                                    Directions
                                                </div>
                                                <p>
                                                    {{ cocktail.instructions }}
                                                </p>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </li>
                </TransitionGroup>

                <div
                    v-if="this.foundDrinks.length > this.resultsMax"
                    class="the-results__wrapper"
                >
                    <div class="the-results__loadMore">
                        <button
                            @click="loadMore"
                            class="button"
                        >
                            <span class="button__shadow"></span>
                            <span class="button__edge"></span>
                            <span class="button__front">
                                Load more
                            </span>
                        </button>
                    </div>
                </div>
            </div>
        </div>

        <div v-else-if="isLoading && !noResults">
            <div class="the-results">
                <ul class="the-results__list">
                    <li class="the-results__listItem">
                        <div class="skeleton skeleton--dynamic">
                            <div class="skeleton__wrapper">
                                <div class="skeleton__media">
                                    <div class="skeleton__image skeleton__bone"></div>
                                </div>
                                <div class="skeleton__content">
                                    <div class="skeleton__bone skeleton__heading"></div>
                                    <div class="skeleton__bone skeleton__module"></div>
                                    <div class="skeleton__bone skeleton__module"></div>
                                </div>
                            </div>
                        </div>
                    </li>
                    <li class="the-results__listItem">
                        <div class="skeleton skeleton--dynamic">
                            <div class="skeleton__wrapper">
                                <div class="skeleton__media">
                                    <div class="skeleton__image skeleton__bone"></div>
                                </div>
                                <div class="skeleton__content">
                                    <div class="skeleton__bone skeleton__heading"></div>
                                    <div class="skeleton__bone skeleton__module"></div>
                                    <div class="skeleton__bone skeleton__module"></div>
                                </div>
                            </div>
                        </div>
                    </li>
                    <li class="the-results__listItem">
                        <div class="skeleton skeleton--dynamic">
                            <div class="skeleton__wrapper">
                                <div class="skeleton__media">
                                    <div class="skeleton__image skeleton__bone"></div>
                                </div>
                                <div class="skeleton__content">
                                    <div class="skeleton__bone skeleton__heading"></div>
                                    <div class="skeleton__bone skeleton__module"></div>
                                    <div class="skeleton__bone skeleton__module"></div>
                                </div>
                            </div>
                        </div>
                    </li>
                    <li class="the-results__listItem">
                        <div class="skeleton skeleton--dynamic">
                            <div class="skeleton__wrapper">
                                <div class="skeleton__media">
                                    <div class="skeleton__image skeleton__bone"></div>
                                </div>
                                <div class="skeleton__content">
                                    <div class="skeleton__bone skeleton__heading"></div>
                                    <div class="skeleton__bone skeleton__module"></div>
                                    <div class="skeleton__bone skeleton__module"></div>
                                </div>
                            </div>
                        </div>
                    </li>
                    <li class="the-results__listItem">
                        <div class="skeleton skeleton--dynamic">
                            <div class="skeleton__wrapper">
                                <div class="skeleton__media">
                                    <div class="skeleton__image skeleton__bone"></div>
                                </div>
                                <div class="skeleton__content">
                                    <div class="skeleton__bone skeleton__heading"></div>
                                    <div class="skeleton__bone skeleton__module"></div>
                                    <div class="skeleton__bone skeleton__module"></div>
                                </div>
                            </div>
                        </div>
                    </li>
                    <li class="the-results__listItem">
                        <div class="skeleton skeleton--dynamic">
                            <div class="skeleton__wrapper">
                                <div class="skeleton__media">
                                    <div class="skeleton__image skeleton__bone"></div>
                                </div>
                                <div class="skeleton__content">
                                    <div class="skeleton__bone skeleton__heading"></div>
                                    <div class="skeleton__bone skeleton__module"></div>
                                    <div class="skeleton__bone skeleton__module"></div>
                                </div>
                            </div>
                        </div>
                    </li>
                </ul>
            </div>
        </div>

        <div
            v-else-if="noResults"
            class="the-results"
        >
            <div class="the-results__wrapper">
                <div class="the-results__empty">
                    No cocktails found! Modify your ingredients.
                </div>
            </div>
        </div>

        <div
            v-else
            class="the-results"
        >
            <div class="the-results__wrapper">
                <div class="the-results__empty">
                    Select some ingredients!
                </div>
            </div>
        </div>
    </div>
</template>
