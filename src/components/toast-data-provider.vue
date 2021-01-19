<template>
  <div>
    <slot
      v-bind="{
        displayedMessages,
        messageEvents,
        getMessageBindings,
        isCurrent,
      }"
    ></slot>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Prop, Watch } from "vue-property-decorator";

type Message = unknown;

@Component({
  model: {
    prop: "messages",
    event: "input",
  },
})
export default class ToastDataProvider extends Vue {
  @Prop({ required: false, default: () => [], type: Array })
  messages!: Array<Message>;

  expanded = false;
  current: Message | null = null;

  get displayedMessages(): Message[] {
    return this.messages;
    // if (!this.messages.length) return [];

    // if (this.expanded) return this.messages;

    // const range = 3;
    // const currIndex = this.currentIndex;
    // let startIndex = currIndex - range;
    // startIndex = startIndex < 0 ? 0 : startIndex;
    // return this.messages.slice(startIndex, currIndex + 1 + range);
  }

  get currentIndex(): number {
    return this.messages.findIndex((item) => item === this.current);
  }

  get messageEvents() {
    return {
      wheel: this.onWheel,
      click: this.toggleView,
    };
  }

  isCurrent(item: Message) {
    return item === this.current;
  }

  getNeighbourMessage(index: number): Message | undefined {
    if (!this.messages.length) return undefined;

    if (index < 0) {
      return this.messages[0];
    }

    if (index >= this.messages.length) {
      return this.messages.slice(-1)[0];
    }

    return this.messages[index];
  }

  updateCurrent(message: Message) {
    this.current = message;
  }

  getMessageClass(message: Message, index: number) {
    if (this.expanded) return "message-expanded";

    if (this.isCurrent(message)) return "current-message-collapsed";

    const classes = ["neighbour-message-collapsed"];
    const distance = this.currentIndex - index;

    switch (distance) {
      case -2:
        classes.push("message-below-far");
        break;

      case -1:
        classes.push("message-below-near");
        break;

      case 1:
        classes.push("message-above-near");
        break;

      case 2:
        classes.push("message-above-far");
        break;

      default:
        break;
    }

    return classes.join(" ");
  }

  getMessageBindings(message: Message, index: number) {
    const messageClass = this.getMessageClass(message, index);
    return { class: messageClass };
  }

  onWheel(event: WheelEvent) {
    if (this.expanded) return;

    const isNext = event.deltaY > 0;
    const index = isNext ? this.currentIndex + 1 : this.currentIndex - 1;
    this.updateCurrent(this.getNeighbourMessage(index));
  }

  toggleView() {
    this.expanded = !this.expanded;
    this.setLastMessageCurrent();
  }

  @Watch("messages", {
    immediate: true,
  })
  setLastMessageCurrent() {
    const currentMessage = this.messages.slice(-1)[0];
    this.updateCurrent(currentMessage);
  }
}
</script>

<style scoped lang="scss">
.message-expanded {
  position: relative;
}

.current-message-collapsed {
  position: relative;
  z-index: 100;
}

.neighbour-message-collapsed {
  position: absolute;
  height: 30px;
  overflow: hidden;
  transform: scale(0.2);
  z-index: -1;
  top: 0;

  &:after {
    content: "";
    background: inherit;
    height: inherit;
    width: inherit;
    position: absolute;
    top: 0;
    left: 0;
  }

  &.message-below-far {
    top: initial;
    bottom: -20px;
    transform: scale(0.8);
    z-index: 10;
  }

  &.message-below-near {
    top: initial;
    bottom: -10px;
    transform: scale(0.9);
    z-index: 20;
  }

  &.message-above-far {
    top: -20px;
    transform: scale(0.8);
    z-index: 10;
  }
  &.message-above-near {
    top: -10px;
    transform: scale(0.9);
    z-index: 20;
  }
}
</style>