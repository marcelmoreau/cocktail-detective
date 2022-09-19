<script setup>
    import { ref, watch, watchEffect, reactive } from 'vue'
</script>

<template>
    <div class="form cocktail-form">
        <div class="cocktail-form__wrapper">
            <div class="form__field form__field--center">
                <label
                    class="form__label cocktail-form__label"
                    for="form"
                >
                    What ingredients do you have?
                </label>
                <select
                    class="form__control form__control--select cocktail-form__control cocktail-form__control--select"
                    id="form"
                >
                    <option value="first">First Value</option>
                    <option value="second" selected>Second Value</option>
                    <option value="third">Third Value</option>
                </select>
            </div>
        </div>
    </div>
</template>
