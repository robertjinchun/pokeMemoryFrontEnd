<template>
  <li>
    {{ valu }}
  </li> 
</template>

<script>
export default {
  name: 'PokemonList',
  props: {
    value: String,
  }
}
</script>
