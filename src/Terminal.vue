<template>
  <div></div>
  <div class="terminal" :style="terminalSizeStyle">
    <div class="terminal-header">
      <div class="terminal-header-button color-red"></div>
      <div class="terminal-header-button color-yellow"></div>
      <div class="terminal-header-button color-green"></div>
    </div>
    <div class="terminal-body">
      <div class="terminal-line">
        <span>{{ prefix }}</span>
        <span class="terminal-cursor"> {{ cursor }} </span>
      </div>
    </div>
  </div>
</template>
<script>
import { ref, computed } from "vue";
export default {
  props: {
    width: String,
    height: String,
    maxWidth: String,
    maxHeight: String,
  },
  setup(props) {
    const prefix = ref("$");
    const cursor = ref("|");
    const terminalSizeStyle = computed(() => {
      const hyphenate = (str) => str.replace(/\B([A-Z])/g, "-$1").toLowerCase();
      const keys = ["width", "height", "maxHeight", "maxWidth"];
      const strs = keys.map((key) =>
        props[key] ? `${hyphenate(key)}: ${props[key]};` : ""
      );
      return "".concat(...strs);
    });
    return { prefix, cursor, terminalSizeStyle };
  },
};
</script>

<style scoped>
.terminal-body {
  background-color: #424242;
  color: white;
  height: 100%;
}
.terminal-header {
  background-color: #e0e0e0;
  height: 20px;
  border-top-left-radius: 6px;
  border-top-right-radius: 6px;
  display: flex;
  justify-content: start;
  align-items: center;
}
.terminal-line {
  padding: 3px 10px;
}

.terminal-cursor {
  margin-left: 3px;
  animation: blink 1s infinite steps(1, start);
}

.terminal-header-button {
  width: 10px;
  height: 10px;
  border-radius: 50%;
  border-style: none;
  margin: 0px 4px;
}

.color-red {
  background-color: #f44336;
}

.color-yellow {
  background-color: #ffeb3b;
}

.color-green {
  background-color: #66bb6a;
}

@keyframes blink {
  0%,
  100% {
    opacity: 1;
  }
  50% {
    opacity: 0;
  }
}
</style>
