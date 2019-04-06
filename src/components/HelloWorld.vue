<template>
  <section class="section">
    <div class="container">
      <p class="subtitle">Enter code to unlock door</p>
      <div class="field">
        <div class="control">
          <input v-model="theCode" class="input is-large" type="number" placeholder="Enter code">
        </div>
      </div>

      <a
        v-on:click="unlock"
        class="button is-success is-large"
        v-bind:class="{ 'is-loading': isUnlocking}"
      >Lås opp</a>
      <br>
      <br>
      <a class="button is-warning is-large" v-on:click="lock">Lås</a>
    </div>
  </section>
</template>

<script>
import { setTimeout } from "timers";

const baseUrl =
  `https://${process.env.VUE_APP_HOMEY_CLOUD_ID}.` +
  `${process.env.VUE_APP_HOMEY_LOGIC_WEBHOOK}`;

const unlockEvent = process.env.VUE_APP_UNLOCK_EVENT;
const lockEvent = process.env.VUE_APP_LOCK_EVENT;

export default {
  name: "HelloWorld",
  props: {
    msg: String
  },
  mounted: () => {
    console.log(process.env);
  },
  data: () => ({
    isUnlocking: false,
    isLocking: false,
    theCode: ""
  }),
  methods: {
    unlock: function() {
      this.isUnlocking = true;
      fetch(`${baseUrl}/${unlockEvent}?tag=${this.theCode}`)
        .then(function(response) {
          return response.json();
        })
        .then(function(myJson) {
          console.log(JSON.stringify(myJson));
        });
      this.theCode = "";
      setTimeout(() => (this.isUnlocking = false), 1000);
    },
    lock: function() {
      this.isLocking = true;
      fetch(`${baseUrl}/${lockEvent}`)
        .then(function(response) {
          return response.json();
        })
        .then(function(myJson) {
          console.log(JSON.stringify(myJson));
        });
      setTimeout(() => (this.isLocking = false), 1000);
    }
  }
};
</script>
