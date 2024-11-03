<script>
  export let name;
  export let image;
  import { fade } from 'svelte/transition';
</script>

<div class="Card" in:fade={{ duration: 300 }}>
  <h2>{name}</h2>
  <div>
    <img src={image} alt="pokeimg" width="150px" height="150px" />
  </div>
</div>

<style>
  .Card {
    position: relative;
    background-color: white;
    border-radius: 5px;
    box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.5);
    padding: 1rem;
    border-radius: 20px;
    transition: transform 0.2s ease;
    margin-bottom: 2rem;
    background-color: #ffffff;
  }
  .Card:hover {
    transform: scale(1.05);
    box-shadow: 0px 0px 15px rgba(0, 0, 0, 0.7);
  }
</style>
