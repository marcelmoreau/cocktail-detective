<script setup>
    import { reactive, computed } from 'vue'
    import Multiselect from '@vueform/multiselect'

    const props = defineProps({
        ingredients: Array
    })

    const data = reactive({
        value: null
    })

    // const ingredientsOptions = computed(() => {
    //     return ingredients.map(ingredient => 'lorem')
    // })

    // console.log(ingredientsOptions)


</script>

<template>
    <div class="form the-form">
        <div class="the-form__wrapper">
            <div class="form__field the-form__formField">
                <div class="the-form__labelWrapper">
                    <label
                        class="form__label the-form__label"
                        for="form"
                    >
                        What ingredients do you have?
                    </label>
                </div>
                <Multiselect
                    v-model="data.value"
                    mode="tags"
                    :close-on-select="false"
                    :searchable="true"
                    :create-option="true"
                    :options="ingredients"
                    class="form__control form__control--select the-form__control the-form__control--select"
                    id="form"
                />
            </div>
        </div>
    </div>
</template>
