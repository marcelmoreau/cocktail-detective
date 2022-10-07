<script setup>
    import { reactive, computed, watch } from 'vue'
    import Multiselect from '@vueform/multiselect'
    import gsap from 'gsap'

    const API_URL = `https://www.thecocktaildb.com/api/json/v2/`
    const API_KEY = ***REMOVED***

    const data = reactive({
        isLoading: false,
        isCapped: false,
        ingredientsPicked: null,
        ingredientsOptions: [],
        filteredDrinks: [],
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
                            // console.log(booze)
                            return booze.json()
                        }
                    })
                    .then(booze => {
                        if (!booze.drinks instanceof Array) {
                            throw new Error('error');
                        }
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

                    })
                    .catch(error => {
                        data.isLoading = false
                        data.filteredDrinks = []
                    })
            } else {
                data.filteredDrinks = []
            }

        }
    )


    const hasLoadMore = computed(() => {
        return data.resultsMax > data.filteredDrinks.length ? data.isCapped = false : data.isCapped = true
    })

    const listingCapped = computed(() => {
        return data.filteredDrinks.slice(0, data.resultsMax)
    })

    const trimTrailingSpace = (str) => {
        if (str.length || '') {
            return str.replace(/\s+$/, '')
        } else {
            return
        }
    }

    function loadMore() {
        if (data.resultsMax > data.filteredDrinks.length) {
            return
        }

        data.resultsMax = data.resultsMax + 10
    }

    function recipe(arr) {
        return Object.entries(arr[0])
            .filter(([k,v]) => k.match(/^str(Ingredient|Measure)\d+$/) && v)
            .reduce((acc, [k, v]) => {
                const [t, n] = k.match(/^str(Ingredient|Measure)(\d+)$/).slice(1);
                acc[n-1] = {...acc[n-1], [t]:v};
            return acc;
        }, [])
    }


    function onBeforeEnter(el) {
        el.style.opacity = 0
    }

    function onEnter(el, done) {
        gsap.to(el, {
            opacity: 1,
            delay: el.dataset.index * 0.025,
            onComplete: done
        })
    }

    function onLeave(el, done) {
        gsap.to(el, {
            opacity: 0,
            delay: el.dataset.index * 0.025,
            onComplete: done
        })
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
                <TransitionGroup
                    :css="false"
                    tag="ul"
                    @before-enter="onBeforeEnter"
                    @enter="onEnter"
                    @leave="onLeave"
                    class="the-results__list"
                >
                    <li
                        v-for="(cocktail, index) in listingCapped"
                        :key="cocktail.idDrink"
                        :data-index="index"
                        class="the-results__listItem"
                    >
                        <div class="card">
                            <div class="card__body">
                                <div class="card__wrapper">
                                    <div class="card__media">
                                        <img alt="" class="card__image" :src=" cocktail.strDrinkThumb" loading="lazy">
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
                                                        v-for="(item, index) in recipe(cocktail.drinks)"
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
                                                    {{ cocktail.drinks[0].strInstructions }}
                                                </p>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </li>
                </TransitionGroup>

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
