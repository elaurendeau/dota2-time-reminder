<template>
  <div id="side-notifications">
    <div v-if="notifications.length > 0">
      <div
        v-for="(n) in notifications"
        :key="n.id"
        class="notification"
      >
        <div class="progress-container">
          <span>{{ getRemainingTime(n) }}</span>
          <progress
            :value="gameTime - n.createdAt"
            :max="n.expireAt - n.createdAt"
          ></progress>
          <img :src="'icons/' + n.icon">
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import { gameInfo, UINotification } from "./Overlay.vue";

export default Vue.extend({
  name: "App",
  components: {
  },
  props: {
    // eslint-disable-next-line vue/require-default-prop
    notifications: {
      type: Array as () => UINotification[]
    },
    expireDecay: { type: Number, default: 3 }
  },
  computed: {
    gameTime(): number {
      return gameInfo.time;
    }
  },
  methods: {
    getRemainingTime(notification: UINotification): number {
      return Math.max(notification.expireAt - this.gameTime, 0);
    }
  },
  watch: {
    gameTime: function(newTime) {
      this.notifications.forEach(n => {
        if (n.wasTriggered === false && n.expireAt <= newTime) {
          this.$emit("upcoming-notification-trigger", n);
          n.wasTriggered = true;
        }
        if (n.expireAt + this.expireDecay <= newTime) {
          this.$emit("upcoming-notification-expired", n);
        }
      });
    }
  }
});
</script>

<style scoped>
  #side-notifications {
    position: absolute;
    top: 10vh;
    right: 1vh;
    overflow: none;
  }
  .notification {
    padding-bottom:5px;
  }
  .progress-container {
    display: flex;
    justify-content: flex-end;
    align-items: center;
  }
  .progress-container > span {
    text-align: center;
    position: relative;
    right:-57%;
    font-weight: bold;
  }
  img {
    height:40px !important;
  }
  progress {
    -webkit-appearance: none;
    appearance: none;
    width:100px;
    height:20px;
    padding-right:5px;
  }
  progress[value]::-webkit-progress-bar {
    background-color: #eee;
    border-radius: 2px;
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.25) inset;
  }
  @-webkit-keyframes animate-stripes {
    100% { background-position: -100px 0px; }
  }

  @keyframes animate-stripes {
    100% { background-position: -100px 0px; }
}

</style>
