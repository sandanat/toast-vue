<template>
  <div class="messages-container">
    <ToastDataProvider
      v-slot="{
        displayedMessages,
        messageEvents,
        getMessageBindings,
        isCurrent,
      }"
      v-model="messages"
      class="messages-group"
    >
      <div
        v-for="(item, index) in displayedMessages"
        v-on="messageEvents"
        v-bind="getMessageBindings(item, index)"
        :key="index"
        class="message"
      >
        {{ item }}
        <span v-if="isCurrent(item)" style="color: white">Current</span>
      </div>
    </ToastDataProvider>
    <!-- <ToastDataProvider
      v-slot="{
        displayedMessages,
        messageEvents,
        getMessageBindings,
        isCurrent,
      }"
      v-model="messages"
      class="messages-group"
    >
      <div
        v-for="(item, index) in displayedMessages[0]"
        v-on="messageEvents"
        v-bind="getMessageBindings(item)"
        :key="index"
        class="message-2"
      >
        <span>{{ item }}</span>
        <span v-if="isCurrent(item)" style="color: white">Current</span>
      </div>
      <h1>Test</h1>
    </ToastDataProvider> -->
  </div>
</template>

<script lang="ts">
import { Component, Vue, Prop } from "vue-property-decorator";
import ToastDataProvider from "@/components/toast-data-provider.vue";

type Message = unknown;

@Component({
  components: {
    ToastDataProvider,
  },
})
export default class Toast extends Vue {
  @Prop({ required: false, default: () => [], type: Array })
  messages!: Array<Message>;
}
</script>

<style scoped lang="scss">
.current-message-collapsed {
  box-shadow: "0px 0px 5px 3px rgba(50, 50, 50, 0.8)";
}

.messages-container {
  position: fixed;
  bottom: 0;
  right: 0;
  width: 30vw;
  height: 100vh;
  padding: 10px;
  overflow: auto;
  display: flex;
  flex-direction: column-reverse;
  padding-bottom: 20px;
}

.messages-group {
  position: relative;
}

.message,
.message-2 {
  margin: 10px 0;
  padding: 10px;
  background: #c76b62;
  border-radius: 10px;
  transition: all 0.5s ease 0s;
  width: 30vw;
  box-sizing: border-box;
}

.current-message-collapsed {
  box-shadow: 0px 0px 3px 3px rgba(50, 50, 50, 0.5);
}

.message-above-near,
.message-below-near {
  background: #dc9c96;
}

.message-above-far,
.message-below-far {
  background: #ffbdb6;
}

.message-2 {
  background: lightblue;
}

::-webkit-scrollbar {
  width: 5px;
}

::-webkit-scrollbar-track {
  background: #f1f1f1;
}

::-webkit-scrollbar-thumb {
  background: #888;
}

::-webkit-scrollbar-thumb:hover {
  background: #555;
}
</style>