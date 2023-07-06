<script>
import Multiselect from "@vueform/multiselect";
import gsap from "gsap";

const API_URL = `https://www.thecocktaildb.com/api/json/v2`;
const API_KEY = import.meta.env.VITE_COCKTAIL_API_KEY;

export default {
    components: {
        Multiselect,
    },
    data() {
        return {
            isLoading: false,
            ingredientsPicked: [],
            ingredientsList: [],
            foundDrinks: [],
            outputDrinks: [],
            counter: 0,
            batchSize: 10,
            noResults: false,
            multiselectClasses: {
                option: "multiselect-option the-form__option",
                tagRemove: "multiselect-tag-remove the-form__tagRemove",
            },
        };
    },

    methods: {
        async getIngredients() {
            try {
                const response = await fetch(
                    `${API_URL}/${API_KEY}/list.php?i=list`
                );
                const json = await response.json();

                this.ingredientsList = json.drinks.map((obj) => obj.strIngredient1);
            } catch (error) {
                console.error("Error occurred while fetching ingredients:", error);
            }
        },

        loadMore() {
            this.fetchCocktailDetails(this.counter);
        },

        async fetchCocktails(...ingredients) {
            this.isLoading = true;

            this.foundDrinks = [];
            this.outputDrinks = [];

            try {
                const response = await fetch(
                    `${API_URL}/${API_KEY}/filter.php?i=${ingredients}`
                );
                const jsonSummary = await response.json();

                this.counter = 0;

                if (jsonSummary.drinks instanceof Array) {
                    const batchSize = this.batchSize;

                    for (let i = 0; i < jsonSummary.drinks.length; i += batchSize) {
                        this.foundDrinks.push(
                            jsonSummary.drinks.slice(i, i + batchSize)
                        );
                    }

                    this.fetchCocktailDetails(0);
                } else {
                    this.noResults = true;
                }

            } catch (error) {
                console.error("Error occurred while fetching cocktails:", error);
            }
        },

        async fetchCocktailDetails(count) {
            try {
                for (const [index, drink] of this.foundDrinks[count].entries()) {
                    const details = await fetch(
                        `${API_URL}/${API_KEY}/lookup.php?i=${drink.idDrink}`
                    );

                    const jsonDetails = await details.json();

                    this.foundDrinks[count][index]["instructions"] =
                        jsonDetails.drinks[0]["strInstructions"];

                    const pullIngredients = function (arr) {
                        return Object.entries(arr[0])
                            .filter(
                                ([k, v]) =>
                                    k.match(/^str(Ingredient|Measure)\d+$/) && v
                            )
                            .reduce((acc, [k, v]) => {
                                const [t, n] = k
                                    .match(/^str(Ingredient|Measure)(\d+)$/)
                                    .slice(1);
                                acc[n - 1] = { ...acc[n - 1], [t]: v };
                                return acc;
                            }, []);
                    };

                    this.foundDrinks[count][index]["ingredients"] = pullIngredients(
                        jsonDetails.drinks
                    );
                }

                this.outputDrinks.push(...this.foundDrinks[this.counter]);
                this.isLoading = false;
                this.counter++;
            } catch (error) {
                console.error("Error occurred while fetching cocktail details:", error);
            }
        },

        onBeforeEnter(el) {
            el.style.opacity = 0;
        },

        onEnter(el, done) {
            gsap.to(el, {
                opacity: 1,
                delay: el.dataset.index * 0.025,
                onComplete: done,
            });
        },

        onLeave(el, done) {
            gsap.to(el, {
                opacity: 0,
                delay: el.dataset.index * 0.025,
                onComplete: done,
            });
        },

        trimTrailingSpace(str) {
            if (str.length || "") {
                return str.replace(/\s+$/, "");
            } else {
                return;
            }
        },

        clear() {
            this.ingredientsPicked = [];
            this.outputDrinks = [];
        },

        deselected() {
            this.$refs.multiselect.close();
            this.outputDrinks = [];
        },
    },

    computed: {
        drinksTotal() {
            return this.foundDrinks
                .map((innerArray) => innerArray.length)
                .reduce(
                    (accumulator, currentValue) => accumulator + currentValue,
                    0
                );
        },
    },

    created() {
        this.getIngredients();
    },

    watch: {
        ingredientsPicked(ingredients) {
            if (ingredients.length) {
                this.fetchCocktails(...ingredients);
            }
        },
    },
};
</script>

<template>
    <div class="cocktails">
        <div class="the-form">
            <div class="the-form__wrapper">
                <div class="the-form__formField">
                    <label class="the-form__label" for="form">
                        What ingredients do you have?
                    </label>
                    <Multiselect
                        v-model="ingredientsPicked"
                        @clear="clear"
                        @deselect="deselected"
                        mode="tags"
                        :close-on-select="true"
                        :close-on-deselect="true"
                        :searchable="true"
                        :options="ingredientsList"
                        :classes="multiselectClasses"
                        ref="multiselect"
                        class="the-form__control the-form__control--select"
                        id="form"
                    >
                        <template #caret>
                            <svg
                                xmlns="http://www.w3.org/2000/svg"
                                viewBox="0 0 24 24"
                                class="the-form__caret"
                            >
                                <path
                                    d="M19.5 5.25l-7.5 7.5-7.5-7.5m15 6l-7.5 7.5-7.5-7.5"
                                />
                            </svg>
                        </template>
                    </Multiselect>
                </div>
            </div>
        </div>

        <div v-if="this.outputDrinks.length && !isLoading">
            <div class="the-results">
                <div class="the-results__wrapper">
                    <h2 class="heading the-results__heading">
                        You could potentially make
                        <span class="the-results__amount">{{
                            drinksTotal
                        }}</span>
                        {{ drinksTotal > 1 ? "cocktails" : "cocktail" }}
                    </h2>
                </div>
                <div class="the-results__wrapper the-results__wrapper--wide">
                    <TransitionGroup
                        :css="false"
                        tag="ul"
                        @before-enter="onBeforeEnter"
                        @enter="onEnter"
                        @leave="onLeave"
                        class="the-results__list"
                    >
                        <li
                            v-for="cocktail in this.outputDrinks"
                            :key="cocktail.idDrink"
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
                                            />
                                        </div>
                                        <div class="card__content">
                                            <div class="card__header">
                                                <div class="card__heading">
                                                    {{ cocktail.strDrink }}
                                                </div>
                                            </div>
                                            <div class="card__torso">
                                                <div class="card__torsoModule">
                                                    <div
                                                        class="card__subheading"
                                                    >
                                                        Ingredients
                                                    </div>

                                                    <ul class="card__list">
                                                        <li
                                                            v-for="item in cocktail.ingredients"
                                                            class="card__listItem"
                                                        >
                                                            <span
                                                                v-if="
                                                                    item.Measure
                                                                "
                                                            >
                                                                <span
                                                                    class="card__listItemUnit"
                                                                    v-html="
                                                                        trimTrailingSpace(
                                                                            `${item.Measure}`
                                                                        )
                                                                    "
                                                                ></span>
                                                            </span>
                                                            <span>{{
                                                                item.Ingredient
                                                            }}</span>
                                                        </li>
                                                    </ul>
                                                </div>

                                                <div class="card__torsoModule">
                                                    <div
                                                        class="card__subheading"
                                                    >
                                                        Directions
                                                    </div>
                                                    <p>
                                                        {{
                                                            cocktail.instructions
                                                        }}
                                                    </p>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </li>
                    </TransitionGroup>
                </div>

                <div
                    v-if="this.outputDrinks.length < this.drinksTotal"
                    class="the-results__wrapper"
                >
                    <div class="the-results__loadMore">
                        <button @click="loadMore" class="button">
                            Load more
                        </button>
                    </div>
                </div>
            </div>
        </div>

        <div v-else-if="isLoading && !noResults">
            <div class="loading">
                <svg
                    class="loading__loader"
                    xmlns="http://www.w3.org/2000/svg"
                    xml:space="preserve"
                    viewBox="5 5 90 90"
                >
                    <circle
                        class="loading__loaderMatte"
                        cx="50"
                        cy="50"
                        r="40"
                    />
                    <path
                        class="loading__loaderParts"
                        d="M53.272 33.294a1.023 1.023 0 0 0 1.233-.754l1.512-6.278a1.022 1.022 0 1 0-1.987-.479l-1.512 6.278a1.02 1.02 0 0 0 .754 1.233zM47.948 32.707a1.023 1.023 0 0 0 1.961-.579l-1.83-6.193a1.023 1.023 0 0 0-1.962.579l1.831 6.193zM57.538 35.513c.27 0 .54-.107.741-.318l4.448-4.681a1.021 1.021 0 1 0-1.482-1.408l-4.448 4.681a1.021 1.021 0 0 0 .741 1.726zM52.881 39.856l-21.633-7.965-.354.959a11.45 11.45 0 0 0 .352 8.815 11.437 11.437 0 0 0 5.65 5.63L32.98 58.079l-4.015-1.458a1.022 1.022 0 1 0-.698 1.922l4.975 1.807h.002l4.975 1.807a1.022 1.022 0 1 0 .698-1.922l-4.015-1.458 3.919-10.789c.957.249 1.923.373 2.878.373 4.694 0 9.114-2.89 10.829-7.544l.353-.961zm-20.37-5.32 15.578 5.736H32.882a9.39 9.39 0 0 1-.371-5.736zM72.344 68.014a1.022 1.022 0 0 0-1.31-.612l-4.015 1.458-3.916-10.783a11.435 11.435 0 0 0 5.65-5.631 11.458 11.458 0 0 0 .352-8.815l-.353-.96-21.633 7.965.353.959a11.453 11.453 0 0 0 5.984 6.482 11.487 11.487 0 0 0 4.826 1.068c.971 0 1.943-.129 2.897-.378l3.918 10.789-4.015 1.458a1.023 1.023 0 0 0 .699 1.924l9.951-3.614c.531-.193.805-.779.612-1.31zm-4.951-17.792H54.167l13.321-4.904a9.425 9.425 0 0 1-.095 4.904z"
                    />
                </svg>
            </div>
        </div>

        <div
            v-else-if="noResults || !this.ingredientsPicked.length"
            class="the-results"
        >
            <div class="the-results__wrapper">
                <div class="the-results__empty">
                    No cocktails found! Modify your ingredients.
                </div>
            </div>
        </div>

        <div v-else class="the-results">
            <div class="the-results__wrapper">
                <div class="the-results__empty">
                    <div>Select some ingredients above!</div>
                </div>
            </div>
        </div>
    </div>
</template>
