<template>
    <div class="container-lg container-pokedex">
        <div class="text-center logo m-5 logo">
            <img src="/public/pokedex-logo.png" alt="" @click="resetPokemon">
        </div>
        <div class="row">
            <div class="col">
                <div class="text-center mb-5">
                    <div class="mb-3 d-flex justify-content-center gap-2">
                        <input class="pokemon-text" type="text" v-model="userInput" @keyup.enter="pkmnCall()">
                        <button @click="pkmnCall()" class="btn ">cerca</button>
                    </div>
                    <button @click="randomPkmnCall()" class="btn">Random Pokemon</button>
                </div>
                <div class="pokemon-card" v-if="pokemon.id"> 
                    <h2 class="text-center m-3">{{ capitalizeFirstLetter(pokemon.name) }}</h2>
                    <div class="text-center pokemon-sprite">
                        <img v-if="pokemon.sprites !== undefined" :src="currentSprite" alt="">
                    </div>
                    <ul class="p-4">
                        <li>ID: {{ pokemon.id }}</li>
                        <li>Altezza: {{ pokemon.height }}</li>
                        <li>Peso: {{ pokemon.weight }} Kg</li>
                        <li v-for="stat in pokemon.stats">
                            {{stat.stat.name}}: {{ stat.base_stat }}
                            <div class="progress" role="progressbar" aria-label="Basic example" :aria-valuenow="stat.base_stat" aria-valuemin="0" aria-valuemax="200">
                                <div class="progress-bar" :style="{ width: `${(stat.base_stat / 200) * 100}%` }"></div>
                            </div>
                        </li>
                    </ul>
                    <div class="text-center mb-3">
                        <button @click="savePokemon" class="catch">Cattura</button>
                    </div>
                </div>

            </div>
            <div class="col text-center" v-if="savedPokemons.length !== 0">
                <h2 >I miei pokemon</h2>
                <div>
                    <ul>
                        <li v-for="(pokemonSalvato, index) in savedPokemons" @click="showPkmn(pokemonSalvato)" class="pokemon-saved">
                            <div class="d-flex align-items-center justify-content-around"> 
                                <div>
                                    <!-- {{ pokemonSalvato.sprites }} -->
                                    <img v-if="pokemonSalvato.sprites !== undefined" :src="pokemonSalvato.sprites.front_default" alt="">
                                </div>
                                <div>
                                    <span>{{ capitalizeFirstLetter(pokemonSalvato.name) }}</span> 
                                </div>
                                <div>
                                    <button @click="deletePokemon(index, $event)" class="free-pokemon">Libera</button>
                                </div> 
                            </div>
                        </li>
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

            randomPkmnCall() {
                let randomIndex = Math.floor(Math.random() * 1010) + 1 
                axios.get(`https://pokeapi.co/api/v2/pokemon/${randomIndex}`,{
                
                }).then((res)=>{
                    this.pokemon = res.data
                    console.log(this.pokemon);
                    
                })
                .catch((error) => {
                    console.log('errore nella chiamata API');
                    
                });
            },

            resetPokemon() {
                this.pokemon = {}
            },

            capitalizeFirstLetter(str) {
                return str.charAt(0).toUpperCase() + str.slice(1);
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
            deletePokemon(index, event) {
                event.stopPropagation();
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
                if (this.pokemon.sprites.back_default !== null){
                    return this.showFront ? this.pokemon.sprites.front_default : this.pokemon.sprites.back_default
                } else {
                    return this.pokemon.sprites.front_default
                }
            },

        },

        mounted() {
            this.startLoop();
        }

        
    }
</script>

<style lang="scss" scoped>

.container-pokedex{
    border: 1px solid black;
    width: 800px;
    background-image: linear-gradient(rgb(255, 196, 0), rgb(255, 94, 0));
    opacity: 0.9;
    border: 12px solid rgb(8, 8, 85);
    border-radius: 18px;

    .logo{
        &:hover{
            cursor: pointer;
        }
    }

    .pokemon-text{
        border-radius: 5px;
        border: none;
        padding: 10px;
    }

    .pokemon-card{
        border: 3px solid rgb(8, 8, 85);
        background-color: rgb(255, 255, 255, 0.9);
        border-radius: 20px;
        margin-bottom: 10px;

        .pokemon-sprite{
            img{
                height: 150px;
            }
        }

        .progress-bar{
            background-color: #0C0C56;
        }

        .catch{
            padding: 7px;
            border: 1px solid grey;
            border: none;
            border: 3px solid rgb(8, 8, 85);
            border-radius: 10px;
            &:hover{
                background-color: green;
                color: #ffffff;
            }
        }
    }
    
    .pokemon-saved{
        border: 1px solid transparent;
        &:hover{
            cursor: pointer;
            background-color: #0C0C56;
            transition: 1000ms smooth;
            color: white;
        }
    
        .free-pokemon{
            padding: 7px;
            border: 1px solid grey;
            border: none;
            border: 3px solid transparent;
            border-radius: 10px;
            &:hover{
                background-color: red;
                border: 3px solid rgb(8, 8, 85);
                color: rgb(255, 196, 0);
            }
        }
    }
}
</style>