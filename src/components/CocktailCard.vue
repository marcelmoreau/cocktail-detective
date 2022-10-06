<script setup>

    import { reactive, computed } from 'vue'

    const props = defineProps({
        name: String,
        id: String,
        image: String
    })

    const data = reactive({
        cocktailDetails: [],
        ingredientsNeeded: []
    });

    async function getDetails(id) {
        const response = await fetch(`https://www.thecocktaildb.com/api/json/v2/***REMOVED***/lookup.php?i=${id}`)
        if (response.ok) {
            const json = await response.json()

            data.cocktailDetails = json.drinks
        } else {
            this.error = "The Detective is drunk. Try again."
        }
    }

    getDetails(props.id)



    function getAllValuesForKeysLike(arr, prop) {
        const regex = new RegExp(prop)
        const getValuesForKeys = (obj) => {
            return Object.entries(obj)
                .filter(([key, value]) => regex.test(key) && value)
                .map(([_, value]) => value)
        }

        return arr.map(getValuesForKeys).flat()
    }

    const result = getAllValuesForKeysLike(data.cocktailDetails, 'strIngredient')

    // console.log(data.cocktailDetails)


    // const publishedBooksMessage = computed(() => {
    //     return author.books.length > 0 ? 'Yes' : 'No'
    // })


</script>

<template>

    <div class="card">
        <div class="card__body">
            <div class="card__wrapper">
                <div class="card__media">
                    <img alt="" class="card__image" :src="image" loading="lazy">
                </div>
                <div class="card__content">
                    <div class="card__header">
                        <div class="card__heading">
                            {{ name }}
                        </div>
                    </div>
                    <div class="card__torso">
                        <div class="card__torsoModule">
                            <div class="card__subheading">
                                Ingredients
                            </div>
                            <ul class="card__list">
                                <template v-for="item in data.cocktailDetails">
                                    <li class="card__listItem">

                                    </li>
                                </template>
                            </ul>
                        </div>
                        <div class="card__torsoModule">
                            <div class="card__subheading">
                                Directions
                            </div>
                            <p v-for="instruction in data.cocktailDetails">
                                {{ instruction.strInstructions }}
                            </p>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

</template>
