<script>
  import { BarLoader } from 'svelte-loading-spinners';
  import { onMount } from 'svelte';
  import PokemonCard from './PokemonCard.svelte';
  import Header from './Header.svelte';
  import Footer from './Footer.svelte';

  let pokemons = [];
  let color = '#F5E063';
  let size = '125';
  onMount(async () => {
    // Tällä tehdään alkuun 3 sekunnin viive ennenkun "sivu" avautuu
    await new Promise((resolve) => setTimeout(resolve, 3000));
    //Fetchillä haetaan pokeAPI endpointista 151 pokemoniare
    const response = await fetch('https://pokeapi.co/api/v2/pokemon?limit=151');
    if (!response.ok) {
      throw new Error('Failed to fetch pokemons');
    }
    const data = await response.json();
    // Käydään mapilla jokainen 151 jotta saadaan niiden url
    pokemons = await Promise.all(
      data.results.map(async (pokemon) => {
        const pokemonResponse = await fetch(pokemon.url);
        if (!pokemonResponse.ok) {
          throw new Error(`Failed to fetch url's`);
        }
        // PokemonDta sisältää nyt yksittäisen pokemonien tiedot
        const pokemonData = await pokemonResponse.json();
        return {
          // palautetaan objekti joka sisältää nimen sekä kuvan
          // Tässä luodaan objekti yksittäiselle pokemonille
          name: pokemon.name,
          image: pokemonData.sprites.front_default,
        }; // Lopuksi promise All palauttaa taulukon missä on objekteja joissa on pokemonin nimi ja kuvien urlit
      })
    );
  });

  let searchString = '';
  let searchError = '';

  // funktio millä haetaan pokemoneita sidottu input elementtiin, tarkistetaan myös on onko käyttäjän syöte sopiva eli numeroita ei hyväksytä.
  // Kun käyttäjä syöttää numeroita tulee error viesti
  function searchForPokemon(p) {
    const input = p.target.value.trim();
    // jos ei ole syötettä searcherror on tyhjä, kun tulee numeroita tekstikenttään tulee virheitä.
    if (input.length === 0) {
      searchError = '';
      //Tarkistetaan koostuuko syöte vain kirjaimista ei väliä onko pieniä vai isoja
    } else if (!/^[a-zA-Z-]+$/.test(input)) {
      searchError =
        'Please enter a valid Pokémon name, the Pokémon name does not inlcude numbers!';
    } else {
      searchError = '';
    }
  }
  // Kun käyttäjää hakee pokemonia selectedPokemons muuttujaa näyttää ne pokemonit mitä käyttäjä hakee
  $: selectedPokemons = pokemons.filter((pokemon) => {
    return pokemon.name.toLowerCase().includes(searchString.toLowerCase());
  }); // Filtteröidaan pokemon taulukko mitä käyttäjä kirjoittaa, eli kun käyttäjä on kirjoittanut esim cha tulee
  // ne pokemonit mitkä sisältävät kirjaimet cha

  // Nollaa hakupalkin haut sekä errorit
  function resetSearch() {
    searchString = '';
    searchError = '';
  }
</script>

<body>
  <main>
    <Header on:reset={resetSearch} />
    <input
      class="search"
      type="text"
      on:input={searchForPokemon}
      bind:value={searchString}
      placeholder="Pokemon Name"
    />
    <!--on:input kutsuu searchforPokemon funkiota aina kun käyttäjä
		kirjoittaa hakupalkkiin-->
    <!--sidotaan syötetty arvo searchstring muuttujaan-->

    <!--Jos searchError on true tulee virheteksti-->
    {#if searchError}
      <p class="error" style="color:red;">{searchError}</p>
    {/if}
    <div class="container">
      <ul class="grid">
        <!--Jos selectedPokemons taulukkossa ei ole mitään näytetään kaikki 151 pokemonia-->
        <!--Eli kun ei ole hauttu mitään pokemonuia, näytetään kaikki 151 pokemonia-->
        {#if selectedPokemons.length > 0}
          <!--Käydään eachilla taulukko läpi ja tulostetaan PokemonCard komponentille jokaisen 151 pokemonin nimi sekä kuva-->
          {#each selectedPokemons as { name, image }}
            <PokemonCard {name} {image} />
          {/each}
          <!-- Jos pokemoina ei löydy tulee nämä tekstit
		      tai siinä on numeroita-->
        {:else if searchString}
          <div class="center">
            <h1>No Pokemons found</h1>
            <p>
              Sorry, we could not find any Pokemons matching your search. Please
              try again with a different name.
            </p>
          </div>
        {:else}
          <!--Kolme sekunttia kestävä "latausruutu"-->
          <div class="center">
            <h1>First generation Pokémons are loading...</h1>
            <BarLoader {color} {size} />
          </div>
        {/if}
      </ul>
    </div>
    <Footer />
  </main>
</body>

<style>
  body {
    background-image: url('https://www.ungeek.ph/wp-content/uploads/2020/04/25.jpg');
  }
  .search {
    font-size: 1.2rem;
    padding: 0.5rem;
    border: none;
    border-radius: 20px;
    background-color: #f5f5f5;
    box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
    width: 100%;
    max-width: 500px;
    margin: 1rem auto;
    outline: none;
    margin-top: 1rem;
    margin-bottom: 2rem;
  }

  .search:focus {
    background-color: white;
    box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.2);
  }

  .grid {
    list-style: none;
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, max-content));
    gap: 5rem;
    justify-content: center;
  }

  main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
    background-image: url('https://www.ungeek.ph/wp-content/uploads/2020/04/25.jpg');
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
  .center {
    display: flex;
    justify-content: center;
    height: 50vh;
    align-items: center;
    flex-direction: column;
    margin-top: 3rem;
  }
</style>
