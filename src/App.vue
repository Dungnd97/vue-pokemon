<template>
  <div>
    <MainScreen
      v-if="statusMatch === 'default'"
      @onStart="onHandleBeforeStart($event)"
    />

    <InteractScreen
      v-if="statusMatch === 'match'"
      :cardsContext="settings.cardsContext"
      @onFinish="onGetResult"
    />
    
    <Result
      v-if="statusMatch === 'result'"
      :timer="timer"
      @onAgain="statusMatch = 'default'"
    />
  </div>
</template>

<script>
import MainScreen from "./components/MainScreen.vue";
import InteractScreen from "./components/InteractScreen.vue";
import Result from "./components/ResultScreen.vue";

import { shuffled } from "./utils/array";

export default {
  name: "App",

  components: {
    MainScreen,
    InteractScreen,
    Result,
  },

  data() {
    return {
      settings: {
        totalOfBlocks: 0,
        cardsContext: [],
        startedAt: null,
      },

      statusMatch: "default",
      timer: 0,
    };
  },

  methods: {
    onHandleBeforeStart(config) {
      console.log("Running handle before start", config);
      this.settings.totalOfBlocks = config.totalOfBlocks;

      // khởi tạo 1 mảng firstCards có totalOfBlocks/2 phần tử
      const firstCards = Array.from(
        { length: this.settings.totalOfBlocks / 2 },
        (_, i) => i + 1
      );
      // khởi tạo 1 mảng secondCards = mảng firstCards
      const secondCards = [...firstCards];

      // khởi tạo 1 mảng cards  = 2 mảng firstCards và secondCards
      const cards = [...firstCards, ...secondCards];

      // mix mảng cards bằng hàm shuffled
      this.settings.cardsContext = shuffled(
        shuffled(shuffled(shuffled(cards)))
      );

      this.settings.startedAt = new Date().getTime();

      // data ready
      this.statusMatch = "match";
    },

    onGetResult() {
      // get timer
      this.timer = new Date().getTime() - this.settings.startedAt;
      // chuyển trạng thái sang kết quả
      this.statusMatch = "result";
    },
  },
};
</script>

<style>
</style>
