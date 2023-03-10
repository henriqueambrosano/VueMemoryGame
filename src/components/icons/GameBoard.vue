<template>
  <div id="gameBoard" v-if="showBoard">
    <CardElement v-for="(card, index) in cards" :card="card" :key="index" @flip="flipCard" />
  </div>
  <div id="gameOver" v-else>
    <div>Parabéns, você completou o jogo!</div>
    <button id="restart" @click="restart()">Jogue Novamente</button>
  </div>
</template>

<script>
import CardElement from './CardElement.vue';
export default {
  name: "GameBoard",
  components: { CardElement },
  data() {
    return {
      techs: [
        "bootstrap",
        "css",
        "electron",
        "firebase",
        "html",
        "javascript",
        "jquery",
        "mongo",
        "node",
        "react",
      ],
      showBoard: true,
      lockMode: false,
      firstCard: null,
      secondCard: null
    }
  },
  methods: {
    restart() {
      this.clearCards()
      window.location.reload()
    },
    setCard(id) {
      let card = this.cards.filter((card) => card.id === id)[0];
      if (card.flipped || this.lockMode) {
        return false;
      }

      if (!this.firstCard) {
        this.firstCard = card;
        this.firstCard.flipped = true;
        return true;
      } else {
        this.secondCard = card;
        this.secondCard.flipped = true;
        this.lockMode = true;
        return true;
      }
    },
    checkMatch() {
      if (!this.firstCard || !this.secondCard) {
        return false;
      }
      return this.firstCard.icon === this.secondCard.icon;
    },
    clearCards() {
      this.firstCard = null;
      this.secondCard = null;
      this.lockMode = false;
    },
    checkGameOver() {
      return this.cards.filter(card => !card.flipped).length == 0;
    },
    unflipCards() {
      this.firstCard.flipped = false;
      this.secondCard.flipped = false;
      this.clearCards();
    },
    flipCard(id) {
      if (this.setCard(id)) {
        if (this.secondCard) {
          if (this.checkMatch()) {
            this.clearCards()
            if (this.checkGameOver()) {
              this.showBoard = false
            }
          } else {
            setTimeout(() => {
              this.unflipCards()
            }, 1000)
          };
        }
      }
    },
  createIdWithTech(tech) {
    return tech + parseInt(Math.random() * 1000);
  },
  createPairFromTechs: function (tech) {
    return [
      {
        id: this.createIdWithTech(tech),
        icon: tech,
        flipped: false,
      },
      {
        id: this.createIdWithTech(tech),
        icon: tech,
        flipped: false,
      },
    ];
  },
  cardsShuffled(cards) {
    let currentIndex = cards.length;
    let randomIndex = 0;

    while (currentIndex != 0) {
      randomIndex = Math.floor(Math.random() * currentIndex);
      currentIndex--;

      [cards[randomIndex], cards[currentIndex]] = [
        cards[currentIndex],
        cards[randomIndex],
      ];
    }
    return cards
  },
},
computed: {
  cards() {
    let cards = []
    this.techs.forEach((tech) => cards.push(this.createPairFromTechs(tech)))
    cards = cards.flatMap(card => card)
    return this.cardsShuffled(cards)
  },
}
}
</script>