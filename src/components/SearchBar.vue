<template>
    <div class="container">
        <div class="row">
            <div class="col">
                <div class="text-center mb-5">
                    <input type="text" v-model="userInput">
                    <button @click="pkmnCall()" >cerca</button>
                </div>
                <div class="text-center">
                    <img v-if="pokemon.sprites !== undefined" :src="currentSprite" alt="">
                </div>
                <ul>
                    <li>id pokemon: {{ pokemon.id }}</li>
                    <li>nome pokemon: {{ pokemon.name }}</li>
                    <li>altezza pokemon: {{ pokemon.height }}</li>
                    <li>peso pokemon: {{ pokemon.weight }}</li>
                    <li v-for="stat in pokemon.stats">
                        {{stat.stat.name}}: {{ stat.base_stat }}
                        <div class="progress" role="progressbar" aria-label="Basic example" :aria-valuenow="stat.base_stat" aria-valuemin="0" aria-valuemax="150">
                            <div class="progress-bar" :style="{ width: `${(stat.base_stat / 150) * 100}%` }"></div>
                        </div>
                    </li>

                </ul>
                <span>Salva Pokemon</span> <button @click="savePokemon">salva</button>
            </div>
            <div class="col">
                <h2>I miei pokemon</h2>
                <div>
                    <ul>
                        <li v-for="(pokemonSalvato, index) in savedPokemons"><span @click="showPkmn(pokemonSalvato)">{{ pokemonSalvato.name }}</span>  <button @click="deletePokemon(index)">elimina</button></li>
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
                savedPokemons: [],
                showFront: true
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

            showPkmn(pokemonSalvato){
                this.pokemon = pokemonSalvato
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
            },

            startLoop(){
                setInterval(() => {
                    this.showFront = !this.showFront;
                }, 1800)
            }
        },

        created() {
            const saved = localStorage.getItem('savedPokemons');

            if (saved) {
                this.savedPokemons = JSON.parse(saved);
            }
        },

        computed: {
            currentSprite(){
                return this.showFront ? this.pokemon.sprites.front_default : this.pokemon.sprites.back_default
            },

        },

        mounted() {
            this.startLoop();
        }

        
    }
</script>

<style lang="scss" scoped>

</style>