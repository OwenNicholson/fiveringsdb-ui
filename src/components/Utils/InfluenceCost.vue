<template>
    <span class="card-influence" :class="['fg-dark-'+card.clan]">
        <span v-if="div">
            <b v-for="n in div" style="text-decoration: line-through">/////</b>
        </span>
        <span v-if="mod">
            <b v-for="n in mod">/</b>
        </span>
    </span>
</template>

<script>
  export default {
    name: 'utils-influence-cost',
    props: {
      card: {
        type: Object,
        required: true,
      },
      quantity: {
        type: Number,
        default: 1,
      },
    },
    computed: {
      div() {
        return Math.floor((this.quantity * this.card.influence_cost) / 5);
      },
      mod() {
        return (this.quantity * this.card.influence_cost) % 5;
      },
    },
  };
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
</style>
