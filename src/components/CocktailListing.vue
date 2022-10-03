<script setup>
    import { reactive, nextTick, computed, watch } from 'vue'
    import Multiselect from '@vueform/multiselect'

    const API_URL = `https://www.thecocktaildb.com/api/json/v2/`
    const API_KEY = ***REMOVED***

    const data = reactive({
        isLoading: false,
        isCapped: false,
        ingredientsPicked: null,
        ingredientsOptions: [],
        filteredDrinks: [],
        error: null,
        resultsMore: true,
        resultsMax: 8
    });

    const multiselectClasses = {
        option: 'multiselect-option the-form__option',
        tagRemove: 'multiselect-tag-remove the-form__tagRemove'
    }

    async function populateIngredients() {
        const response = await fetch(`${API_URL}/${API_KEY}/list.php?i=list`)
        if (response.ok) {
            const json = await response.json()
            data.ingredientsOptions = json.drinks.map(obj => obj.strIngredient1)
        } else {
            data.error = "The Detective is drunk. Try again."
        }
    }

    populateIngredients()


    watch(
        () => data.ingredientsPicked,
        async (picked) => {

            if (picked.length) {
                data.isLoading = true

                let ingredients = picked.join(',')

                fetch(`${API_URL}/${API_KEY}/filter.php?i=${ingredients}`)
                    .then((booze) => {
                        if (booze.ok) {
                            return booze.json()
                        }
                    })
                    .then(booze => {
                        return booze;
                    })
                    .then(async booze => {
                        await Promise.all(booze.drinks.map((e, index, array) => {
                            return fetch(`${API_URL}/${API_KEY}/lookup.php?i=${e.idDrink}`)
                                .then(response => response.json())
                                .then(booze => {
                                    array[index] = {...e, ...booze};
                                })
                        }));

                        data.filteredDrinks = booze.drinks

                        data.isLoading = false
                    });
            } else {
                data.filteredDrinks = []
            }

        }
    )


    const bazz = computed(() => {

        // return data.filteredDrinks.map(elem => elem.idDrink)
        // return 'hi'

        // console.log(`${data.filteredDrinks.map(elem => elem.idDrink)}`)

        // return data.filteredDrinks.map(({drinks}) => {
        //     // elem.drinks.map((elem) => {
        //     //     return elem.idDrink
        //     // })
        //     return drinks.idDrink
        // })

    })


    const hasLoadMore = computed(() => {
        return data.resultsMax > data.filteredDrinks.length ? data.isCapped = false : data.isCapped = true
    })

    const listingCapped = computed(() => {
        return data.filteredDrinks.slice(0, data.resultsMax)
    })

    function loadMore() {
        if (data.resultsMax > data.filteredDrinks.length) {
            return
        }

        data.resultsMax = data.resultsMax + 10
    }


    function getAllValuesForKeysLike(arr, prop) {
        const regex = new RegExp(prop)
        const getValuesForKeys = (obj) => {
            return Object.entries(obj)
            .filter(([key, value]) => regex.test(key) && value)
            .map(([_, value]) => value)
        }
        return arr.map(getValuesForKeys).flat()
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
                        v-model="data.ingredientsPicked"
                        @clear="data.ingredientsPicked = []"
                        mode="tags"
                        :close-on-select="false"
                        :searchable="true"
                        :options="data.ingredientsOptions"
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

        <div
            v-if="data.filteredDrinks.length && !data.isLoading"
        >
            <div class="the-results">
                <ul class="the-results__list">
                    <li
                        v-for="(cocktail, index) in listingCapped"
                        :key="cocktail.idDrink"
                        class="the-results__listItem"
                    >
                        <div class="card">
                            {{ bazz }}
                            <div class="card__body">
                                <div class="card__wrapper">
                                    <div class="card__media">
                                        <img alt="" class="card__image" :src=" cocktail.strDrinkThumb" loading="lazy">
                                    </div>
                                    <div class="card__content">
                                        <div class="card__header">
                                            <div class="card__heading">
                                                {{ cocktail.strDrink }} {{ bazz }}
                                            </div>
                                        </div>
                                        <div class="card__torso">
                                            <div class="card__torsoModule">
                                                <div class="card__subheading">
                                                    Ingredients
                                                </div>
                                                <ul class="card__list">
                                                    <li v-for="item in getAllValuesForKeysLike(cocktail.drinks, 'strIngredient')" class="card__listItem">

                                                        <span>

                                                            xx
                                                        </span>

                                                        <span>
                                                            {{ item }}
                                                        </span>

                                                    </li>
                                                </ul>
                                            </div>
                                            <div class="card__torsoModule">
                                                <div class="card__subheading">
                                                    Directions
                                                </div>
                                                <p>
                                                    {{ cocktail.drinks[0].strInstructions }}
                                                </p>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </li>
                </ul>

                <div v-if="hasLoadMore" class="the-results__wrapper">
                    <div class="the-results__loadMore">
                        <button class="button" @click="loadMore">Load more</button>
                    </div>
                </div>

            </div>
        </div>

        <div
            v-else-if="data.isLoading"
        >
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
            v-else
            class="the-results"
        >
            <div class="the-results__wrapper">
                <div class="the-results__empty">
                    No cocktails found. Select some ingredients.
                </div>
            </div>
        </div>



    </div>
</template>
