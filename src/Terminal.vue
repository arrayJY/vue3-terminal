<template>
  <div
    ref="terminal"
    class="terminal"
    tabindex="0"
    :style="terminalSizeStyle"
    @keypress="inputText($event)"
    @keydown.left="leftArrow"
    @keydown.right="rightArrow"
    @keydown.delete="deleteChar($event.key)"
  >
    <div class="terminal-header">
      <div class="terminal-header-button color-red"></div>
      <div class="terminal-header-button color-yellow"></div>
      <div class="terminal-header-button color-green"></div>
    </div>
    <div class="terminal-body">
      <div class="terminal-line">
        <span class="terminal-prefix">{{ prefix }}</span>
        <span class="terminal-text">{{ leftText }}</span>
        <span class="terminal-cursor"> {{ cursor }}</span>
        <span class="terminal-text">{{ rightText }}</span>
      </div>
    </div>
  </div>
</template>
<script>
import { computed, reactive, toRefs } from "vue";
export default {
  props: {
    width: String,
    height: String,
    maxWidth: String,
    maxHeight: String,
  },
  setup(props) {
    const terminal = reactive({
      prefix: "$",
      cursor: "|",
      terminalSizeStyle: computed(() => {
        const hyphenate = (str) =>
          str.replace(/\B([A-Z])/g, "-$1").toLowerCase();
        const keys = ["width", "height", "maxHeight", "maxWidth"];
        const strs = keys.map((key) =>
          props[key] ? `${hyphenate(key)}: ${props[key]};` : ""
        );
        return "".concat(...strs);
      }),
    });

    const text = reactive({
      leftText: "",
      rightText: "",
      text: computed(() => text.leftText + text.rightText),
      inputText(event) {
        const code = event.charCode || event.keyCode;
        text.leftText += String.fromCharCode(code);
      },
      leftArrow() {
        text.rightText = text.leftText.slice(-1) + text.rightText;
        text.leftText = text.leftText.slice(0, -1);
      },
      rightArrow() {
        text.leftText += text.rightText.slice(0, 1);
        text.rightText = text.rightText.slice(1);
      },
      deleteChar(key) {
        if (key === "Delete") {
          text.rightText = text.rightText.slice(1);
        } else {
          text.leftText = text.leftText.slice(0, -1);
        }
      },
    });

    const command = reactive({
      texts: computed(() => text.text.split(" ")),
      command: computed(() => command.texts[0]),
      args: computed(() => command.texts.slice(1)),
    });

    return {
      ...toRefs(terminal),
      ...toRefs(text),
      ...toRefs(command),
    };
  },
};
</script>

<style scoped>
.terminal {
  outline: none;
  font-size: 12px;
  font-family: "JetBrains Mono", "Source Code Pro", Monaco, Menlo, Consolas, "Courier New", Courier, monospace;
}
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
  justify-content: flex-start;
  align-items: center;
}
.terminal-line {
  padding: 5px 10px;
  max-width: 100%;
}

.terminal-prefix {
  margin-right: 5px;
}

.terminal-text {
  max-width: 100%;
  word-wrap: break-word;
}

.terminal-cursor {
  user-select: none;
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
