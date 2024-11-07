<template>
    <div>
        <div>
            <input type="text" v-model="userInput">
            <button @click="pkmnCall()">cerca</button>
            <div>
                <ul>
                    <li>id pokemon: {{ pokemon.id }}</li>
                    <li>nome pokemon: {{ pokemon.name }}</li>
                    <li>altezza pokemon: {{ pokemon.height }}</li>
                    <li>peso pokemon: {{ pokemon.weight }}</li>
                </ul>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios';
    export default {
        data() {
            return {
                userInput : '',
                pokemon: {},
            }
        },

        methods: {
            pkmnCall() {

                let pokemonName = this.userInput.toLowerCase();
                axios.get(`https://pokeapi.co/api/v2/pokemon/${pokemonName}`,{
                
                }).then((res)=>{
                    this.pokemon = res.data
                    console.log(this.pokemon);
                    
                })
                .catch((error) => {
                    console.log('errore nella chiamata API');
                    
                });
            }
        }
    }
</script>

<style lang="scss" scoped>

</style>