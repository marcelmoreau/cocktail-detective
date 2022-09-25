<script setup>
    import Multiselect from '@vueform/multiselect'

    const multiselectClasses = {
        option: 'multiselect-option the-form__option',
        tagRemove: 'multiselect-tag-remove the-form__tagRemove'
    }

    defineProps({
        ingredients: Array,
        modelValue: Array
    })

    defineEmits(['update:modelValue'])

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
                    @deselect="$emit('update:modelValue', modelValue)"
                    mode="tags"
                    :close-on-select="false"
                    :searchable="true"
                    :options="ingredients"
                    :classes="multiselectClasses"
                    class="the-form__control the-form__control--select"
                    id="form"
                />
            </div>
        </div>
    </div>
</template>
