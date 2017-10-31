<template>
  <div id="app">
    <h1>{{ win.type }}</h1>
    <h4>Width: {{ win.size }}</h4>
  </div>
</template>

<script>
import xs from "xstream";

export default {
  name: "app",
  data() {
    return {
      win: {
        size: null,
        type: null
      },
      windowListener: {
        next: value => {
          this.setSize(value)
        },
        error: err => {
          console.log("Error from window stream: ", err);
        },
        complete: () => {
          console.log("Window stream is complete.");
        }
      }
    };
  },
  computed: {
    producer() {
      return {
        start(listener) {
          window.onresize = event => {
            listener.next(event);
          };
        },
        stop() {
          console.log("No longer listening to window size.");
        }
      };
    },
    main$() {
      return xs.createWithMemory(this.producer);
    },
    window$() {
      return xs.from(this.main$).map(v => v.target.innerWidth);
    }
  },
  mounted() {
    this.setSize(window.innerWidth);
    this.window$.addListener(this.windowListener);
  },
  methods: {
    setSize(width) {
      this.win.size = width;
      if (this.win.size >= 1408) {
        this.win.type = "FullHD";
      } else if (this.win.size < 1408 && this.win.size >= 1216) {
        this.win.type = "Widescreen";
      } else if (this.win.size < 1216 && this.win.size >= 1024) {
        this.win.type = "Desktop";
      } else if (this.win.size < 1024 && this.win.size >= 769) {
        this.win.type = "Tablet";
      } else {
        this.win.type = "Mobile";
      }
    }
  }
};
</script>

<style>

</style>
