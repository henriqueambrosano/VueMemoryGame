<template>
  <div :id="card.id" :class="elementClass" @click="flipCard(card.id)">
    <div class="card_front">
      <img :src="`/static/${card.icon}.png`" class="icon" alt="game-card"/>
    </div>
    <div class="card_back">&lt;/&gt;</div>
  </div>
</template>

<script>
export default {
  name: "CardElement",
  props: ["card"],
  data() {
    return {
      dataCard: this.card
    }
  },
  methods: {
    flipCard(id) {
      this.$emit("flip", id);
    }
  },
  computed: {
    elementClass() {
      return this.dataCard.flipped ? 'card flip' : 'card' 
    }
  }
}

</script>