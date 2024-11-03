<script>
  import { createEventDispatcher } from 'svelte';
  const dispatch = createEventDispatcher();

  const reset = () => dispatch('reset');
</script>

<header>
  <div>
    <h1>Pokedex with the first 151 Pokémons</h1>
    <button on:click={reset}>Reset search</button>
  </div>
</header>

<style>
  button {
    background-color: #3c5aa6;
    border: none;
    color: white;
    padding: 12px 20px;
    text-align: center;
    text-decoration: none;
    font-size: 16px;
    margin: 4px 2px;
    cursor: pointer;
    border-radius: 20px;
  }
  button:hover {
    background-color: #c7a008;
  }
  header,
  h1 {
    margin: 0;
    text-align: center;
  }
</style>
