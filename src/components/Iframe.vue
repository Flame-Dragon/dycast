<template>
  <div class="r-box iframe">
    <iframe ref="myIframe" :src="iframeUrl" @load="iframeLoaded"></iframe>
    <!-- <button @click="sendMessage">发送消息给 iframe</button> -->
  </div>
</template>

<script>
import eventBus from "@/utils/event"

export default {
  data() {
    return {
      iframeUrl: "http://10.255.128.4:3000?iszh",
    }
  },

  methods: {
    iframeLoaded() {
      const iframeWindow = this.$refs.myIframe.contentWindow

      // 添加消息事件监听器
      window.addEventListener("message", this.receiveMessage, false)

      // 发送消息给iframe
      this.sendMessageToIframe("Hello from parent!")

      eventBus.on("message", (data) => {
        console.log(data)
        this.sendMessageToIframe(data)
      })
    },

    receiveMessage(event) {
      // 检查消息来源
      if (event.origin !== "http://10.255.128.4:3000?iszh") {
        return
      }

      // 处理从iframe接收到的消息
      const message = event.data
      if (message.type === "onSendMessage") {
        console.log("Received message from iframe:", message.data)
      }
    },

    sendMessageToIframe(message) {
      const iframeWindow = this.$refs.myIframe.contentWindow

      // 发送消息给iframe
      iframeWindow.postMessage(
        {
          type: "onSendMessage",
          data: message,
        },
        "http://10.255.128.4:3000?iszh"
      )
    },

    sendMessage() {
      // 在按钮点击时发送消息给 iframe
      this.sendMessageToIframe("Button clicked!")
    },
  },

  beforeDestroy() {
    // 在组件销毁前移除事件监听器
    window.removeEventListener("message", this.receiveMessage)
  },
}
</script>
