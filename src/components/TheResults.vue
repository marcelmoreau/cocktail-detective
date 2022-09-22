<script setup>
    import { ref, reactive } from 'vue'
    import CocktailCard from '@/components/CocktailCard.vue'

    const props = defineProps({
        isLoading: Boolean
    })

</script>

<template>
    <div class="the-results">
        <div
            v-if="this.isLoading"
            class="the-results__wrapper"
        >
            <div>
                <h1 class="heading">
                    Loading…
                </h1>
            </div>
        </div>

        <ul
            v-else
            class="the-results__list"
        >
            <li
                class="the-results__listItem"
            >
                <CocktailCard

                />
            </li>
            <li
                class="the-results__listItem"
            >
                <CocktailCard

                />
            </li>

        </ul>
    </div>
</template>
