<script setup>
    import { ref, reactive } from 'vue'
    import CocktailCard from '@/components/CocktailCard.vue'

    const props = defineProps({
        isLoading: Boolean,
        filteredDrinks: Array,
    })
</script>

<template>
    <div
        v-if="!this.isLoading && this.filteredDrinks.length"
    >
        <div class="the-results">
            <ul class="the-results__list">
                <li
                    v-for="cocktail in this.filteredDrinks"
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
    >
        else
    </div>
</template>
