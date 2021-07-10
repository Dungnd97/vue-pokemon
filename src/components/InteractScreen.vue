<template>
  <div class="screen">
    <div
      class="screen__inner"
      :style="{
        width: `${
          ((((920 - 16 * 4) / Math.sqrt(cardsContext.length) - 16) * 3) / 4 +
            16) *
          Math.sqrt(cardsContext.length)
        }px`,
      }"
    >
      <Card
        v-for="(card, index) in cardsContext"
        :key="index"
        :ref="`card-${index}`"
        :imgBackFaceUrl="`images/${card}.png`"
        :card="{ index, value: card }"
        :cardsContext="cardsContext"
        @onFlip="checkRule($event)"
      />
    </div>
  </div>
</template>
<script>
import Card from "./Card.vue";

export default {
  name: "InteractScreen",

  props: {
    cardsContext: {
      type: Array,
      default: function () {
        return [];
      },
    },
  },

  components: {
    Card,
  },

  data() {
    return {
      rules: [],
    };
  },

  methods: {
    checkRule(card) {
      if (this.rules.length === 2) return false;
      this.rules.push(card);

      if (
        this.rules.length === 2 &&
        this.rules[0].value === this.rules[1].value
      ) {
        //disable click card
        this.$refs[`card-${this.rules[0].index}`].onEnableDisableMode();
        this.$refs[`card-${this.rules[1].index}`].onEnableDisableMode();
        // resert mảng rules = 0
        this.rules = [];

        const disableElements = document.querySelectorAll(
          ".screen .card.disable"
        );
        console.log(disableElements);
        if (
          disableElements &&
          disableElements.length === this.cardsContext.length - 2
        ) {
          setTimeout(() => {
            this.$emit("onFinish");
          }, 820);
        }
      } else if (
        this.rules.length === 2 &&
        this.rules[0].value !== this.rules[1].value
      ) {
        setTimeout(() => {
          // đóng card
          this.$refs[`card-${this.rules[0].index}`].onFlipBackCard();
          this.$refs[`card-${this.rules[1].index}`].onFlipBackCard();

          // resert mảng rules = 0
          this.rules = [];
        }, 600);
      } else return false;
    },
  },
};
</script>
<style lang="css" scoped>
.screen {
  width: 100%;
  height: 100vh;
  position: absolute;
  top: 0;
  left: 0;
  z-index: 2;
  background-color: var(--pink);
  color: var(--light);
}

.screen__inner {
  width: calc(424px);
  display: flex;
  flex-wrap: wrap;
  margin: 2rem auto;
}
</style>