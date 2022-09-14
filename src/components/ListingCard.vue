<script>
    export default {
        props: {
            cocktail: {
                type: Object,
                required: true
            }
        }
    }
</script>

<template>
    <div class="card">
        <div class="card__body">
            <div class="card__wrapper">
                <div class="card__header">
                    {{ cocktail.strDrink }}
                </div>
                <div class="card__torso">
                    torso
                </div>
                <div class="card__footer">
                    foot
                </div>

            </div>
        </div>
    </div>
</template>
