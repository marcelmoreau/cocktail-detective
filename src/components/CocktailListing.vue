<script setup>
    import { reactive, watch } from 'vue'
    import TheForm from '@/components/TheForm.vue'
    import TheResults from '@/components/TheResults.vue'

    const API_URL = `https://www.thecocktaildb.com/api/json/v2/`
    const API_KEY = ***REMOVED***

    const data = reactive({
        isLoading: false,
        ingredientsPicked: null,
        ingredientsOptions: [],
        filteredCocktails: [],
        filteredDrinks: [],
        drinkA: [],
        drinkB: [],
        drinkC: [],
        tets: [],
        error: null,
        resultsMore: true,
        resultsMax: 50
    });


    async function populateIngredients() {
        const response = await fetch(`${API_URL}/${API_KEY}/list.php?i=list`)
        if (response.ok) {
            const json = await response.json()
            data.ingredientsOptions = json.drinks.map(obj => obj.strIngredient1)
        } else {
            this.error = "The Detective is drunk. Try again."
        }
    }

    populateIngredients()


    watch(
        () => data.ingredientsPicked,
        async (picked) => {
            data.isLoading = true

            let ingredients = picked.join(',')

            const response = await fetch(`${API_URL}/${API_KEY}/filter.php?i=${ingredients}`)

            if (response.ok) {
                const ingredGrab = await response.json()

                data.drinkA = Array.isArray(ingredGrab.drinks) ? ingredGrab.drinks: []


                if(data.drinkA.length) {



                    // example of id lookup for a cocktail
                    /*


                    {
                        "drinks":[
                            {
                                "idDrink":"11007","strDrink":"Margarita","strDrinkAlternate":null,"strTags":"IBA,ContemporaryClassic","strVideo":null,"strCategory":"Ordinary Drink","strIBA":"Contemporary Classics","strAlcoholic":"Alcoholic","strGlass":"Cocktail glass","strInstructions":"Rub the rim of the glass with the lime slice to make the salt stick to it. Take care to moisten only the outer rim and sprinkle the salt on it. The salt should present to the lips of the imbiber and never mix into the cocktail. Shake the other ingredients with ice, then carefully pour into the glass.","strInstructionsES":null,"strInstructionsDE":"Reiben Sie den Rand des Glases mit der Limettenscheibe, damit das Salz daran haftet. Achten Sie darauf, dass nur der \u00e4u\u00dfere Rand angefeuchtet wird und streuen Sie das Salz darauf. Das Salz sollte sich auf den Lippen des Genie\u00dfers befinden und niemals in den Cocktail einmischen. Die anderen Zutaten mit Eis sch\u00fctteln und vorsichtig in das Glas geben.","strInstructionsFR":null,"strInstructionsIT":"Strofina il bordo del bicchiere con la fetta di lime per far aderire il sale.\r\nAvere cura di inumidire solo il bordo esterno e cospargere di sale.\r\nIl sale dovrebbe presentarsi alle labbra del bevitore e non mescolarsi mai al cocktail.\r\nShakerare gli altri ingredienti con ghiaccio, quindi versarli delicatamente nel bicchiere.","strInstructionsZH-HANS":null,"strInstructionsZH-HANT":null,"strDrinkThumb":"https:\/\/www.thecocktaildb.com\/images\/media\/drink\/5noda61589575158.jpg","strIngredient1":"Tequila","strIngredient2":"Triple sec","strIngredient3":"Lime juice","strIngredient4":"Salt","strIngredient5":null,"strIngredient6":null,"strIngredient7":null,"strIngredient8":null,"strIngredient9":null,"strIngredient10":null,"strIngredient11":null,"strIngredient12":null,"strIngredient13":null,"strIngredient14":null,"strIngredient15":null,"strMeasure1":"1 1\/2 oz ","strMeasure2":"1\/2 oz ","strMeasure3":"1 oz ","strMeasure4":null,"strMeasure5":null,"strMeasure6":null,"strMeasure7":null,"strMeasure8":null,"strMeasure9":null,"strMeasure10":null,"strMeasure11":null,"strMeasure12":null,"strMeasure13":null,"strMeasure14":null,"strMeasure15":null,"strImageSource":"https:\/\/commons.wikimedia.org\/wiki\/File:Klassiche_Margarita.jpg","strImageAttribution":"Cocktailmarler","strCreativeCommonsConfirmed":"Yes","dateModified":"2015-08-18 14:42:59"
                            }
                        ]
                    }

                    */



                    // get the idDrink key's value from each array item's object

                    // hit each id's endpoint /lookup.php?i=11007

                    // get strInstructions and put it in respective filteredDrinks's array item

                    // get strIngredient...


                }


                data.isLoading = false
            } else {
                // console.log(response)
                this.error = "WATCH The Detective is drunk. Try again."
            }
        }


    )

</script>

<template>
    <div class="cocktails">
        <TheForm
            v-model="data.ingredientsPicked"
            :ingredients="data.ingredientsOptions"
            @clear="data.ingredientsPicked = []"
        />
        <TheResults
            :isLoading="data.isLoading"
            :filteredDrinks="data.drinkA"
            :resultsMore="data.resultsMore"
        />
    </div>
</template>
