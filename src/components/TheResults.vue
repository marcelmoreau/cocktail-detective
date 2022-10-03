<script setup>
    import { ref, reactive } from 'vue'
    import CocktailCard from '@/components/CocktailCard.vue'

    const props = defineProps({
        isLoading: Boolean,
        resultsMore: Boolean,
        filteredDrinks: {
            type: Array,
            default: () => []
        }
    })

</script>

<template>
    <div
        v-if="this.filteredDrinks.length && !this.isLoading"
    >
        <div class="the-results">
            <ul class="the-results__list">
                <li
                    v-for="cocktail in this.filteredDrinks"
                    :key="cocktail.id"
                    class="the-results__listItem"
                >
                    <CocktailCard
                        :name="cocktail.strDrink"
                        :image="cocktail.strDrinkThumb"
                        :id="cocktail.idDrink"
                    />
                </li>
            </ul>

        </div>
    </div>
    <div
        v-else-if="this.isLoading"
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
            </ul>
        </div>
    </div>

    <div
        v-else-if="this.isLoading && this.filteredDrinks.length"
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
                    <div class="skeleton sekelton--dynamic">
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
                    <div class="skeleton">
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
                    <div class="skeleton">
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
                There is nothing to show you.
            </div>

            <ul class=" the-results__list the-results__list--placeholder">
                <li class="the-results__listItem">
                    <div class="skeleton skeleton--static">
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
                    <div class="skeleton">
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
</template>
