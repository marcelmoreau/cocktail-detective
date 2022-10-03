<script setup>
    import { ref, watch, onMounted } from 'vue'
    import Multiselect from '@vueform/multiselect'

    const multiselectClasses = {
        option: 'multiselect-option the-form__option',
        tagRemove: 'multiselect-tag-remove the-form__tagRemove'
    }

    defineProps({
        ingredients: Array,
        modelValue: null
    })

    defineEmits(['update:modelValue', 'clear'])

</script>

<template>
    <div class="form the-form">
        <div class="the-form__wrapper">
            <div class="form__field the-form__formField">
                <div class="the-form__labelWrapper">
                    <label class="form__label the-form__label" for="form">
                        What ingredients do you have?
                    </label>
                </div>
                <Multiselect
                    v-model="modelValue"
                    @select="$emit('update:modelValue', modelValue)"
                    @clear="$emit('clear')"
                    @deselect="$emit('update:modelValue', modelValue)"
                    mode="tags"
                    :close-on-select="false"
                    :searchable="true"
                    :options="ingredients"
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
</template>
