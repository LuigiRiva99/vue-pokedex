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
                    <li v-for="stat in pokemon.stats">
                        {{stat.stat.name}}: {{ stat.base_stat }}
                    </li>

                </ul>
                <span>Salva Pokemon</span> <button @click="savePokemon">salva</button>
            </div>
            <div>
                <h2>pokemon salvati</h2>
                <div>
                    <ul>
                        <li v-for="(pokemonSalvato, index) in savedPokemons">{{ pokemonSalvato.name }} <button @click="deletePokemon(index)">elimina</button></li>
                    </ul>
                </div>
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
                savedPokemons: []
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
            },

            //salvo il pokemon con il localstorage
            savePokemon(){
                //controllo se il pokemon esiste già nella lista
                if (!this.savedPokemons.some(pkmn => pkmn.name === this.pokemon.name)){
                    this.savedPokemons.push(this.pokemon);
                    localStorage.setItem('savedPokemons' , JSON.stringify(this.savedPokemons) )
                }
            },
            
            //elimino il pokemon dalla lista
            deletePokemon(index) {
                this.savedPokemons.splice(index, 1);
                localStorage.setItem('savedPokemons', JSON.stringify(this.savedPokemons));
            }
        },

        created() {
            const saved = localStorage.getItem('savedPokemons');

            if (saved) {
                this.savedPokemons = JSON.parse(saved);
            }
        }
    }
</script>

<style lang="scss" scoped>

</style>