<template>
  <div>
    <Toast :messages="messages" />
    <input
      type="text"
      :value="message"
      @input="message = $event.target.value"
      @keydown.enter="addMessage"
    />
    <button type="button" @click="getDefaultMessages">
      Get default messages
    </button>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import Toast from "@/components/toast.vue";

const getMessages = () => [
  "10 Some message",
  "9 Good luck",
  "8 Buy cookies this is a word very long and again and again mom take me home",
  "7 Read books",
  "6 Run fast",
  "5 Think quickly",
  "4 Work hard",
  "3 Have fun",
  "2 Watch TV this is a word very long and again and again mom take me home this is a word very long and again and again mom take me home this is a word very long and again and again mom take me home",
  "1 Enjoy time"
];

@Component({
  components: {
    Toast,
  },
})
export default class Home extends Vue {
  messages: string[] = [];
  message = "";

  addMessage() {
    if (!this.message) return;

    this.messages.push(this.message);
    this.message = "";
  }

  getDefaultMessages() {
    this.messages = getMessages();
  }
}
</script>
