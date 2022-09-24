<script setup>
    import { ref, reactive } from 'vue'
    import CocktailCard from '@/components/CocktailCard.vue'

    const props = defineProps({
        isLoading: Boolean
    })

</script>

<template>

    <div
        v-if="true"
    >

    </div>

    <div
        v-if="this.isLoading"
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
        <div class="the-results">
            <ul class="the-results__list">
                <li class="the-results__listItem">
                    <CocktailCard
                    />
                </li>
                <li class="the-results__listItem">
                    <CocktailCard
                    />
                </li>
            </ul>
        </div>
    </div>

</template>
