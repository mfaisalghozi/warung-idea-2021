<template>
  <div class="container-chat">
    <div class="card h-100">
      testing - {{ userId }}
      <div v-if="userId !== 0">
        <div id="top-scroll-chat" class="card-body">
          <ChatMessages
            :messages="messages"
          >
          </ChatMessages>
        </div>
        <div class="card-footer">
          <ChatForm
            @sentmessage="addMessage"
            :user="user"
          >
          </ChatForm>
        </div>
      </div>
      <div v-else class="empty-state">
        <h2>Chat Is Empty!</h2>
      </div>
    </div>
  </div>
</template>

<script>
import ChatForm from './ChatForm.vue'
import ChatMessages from './ChatMessages.vue'

import { mapGetters } from 'vuex'
import axios from 'axios'

export default {
  name: 'ChatContainer',
  components: {
    ChatMessages,
    ChatForm,
  },
  data: () => {
    return {
      messages: [],
      chatMsgData: [],
    }
  },
  computed: {
    ...mapGetters({
      user: 'user'
    }),
    userId() {
      const userId = parseInt(this.$route.params.userId || '0')
      return userId
    },
    chatId () {
      const chatId = parseInt(this.$route.params.chatId || '0')
      return chatId
    }
  },
  watch: {
    '$route' (to, from) {
      // Saving Previus User Data Messages
      this.saveChatUser(this.userId, this.messages)
      console.log('Chat Changing Old User is ' + from.params.userId)
      console.log('Chat Changing New User is ' + to.params.userId)


      //Fetching New User Data Messages
      this.fetchMessages()
        //Check chatMsgData if exist then fetch
        this.messages = []
        // if not exist make it empty
    }
  },
  created () {
    this.fetchMessages();
  },
  methods: {
    saveChatUser(userId, chat) {
      if(chat.length <= 0) return

      const chatData = {
        userId: userId,
        messages: chat
      }

      const indexChat = this.chatMsgData.findIndex(x => x.userId === chatData.userId)
      if(indexChat >= 0) {
        chat.forEach((x) => {
          this.chatMsgData[indexChat].messages.push(x)
        })
      } else if (indexChat === -1) {
        this.chatMsgData.push(chatData)
      }
    },
    fetchMessages() {
      if(this.chatId === 0 || this.user.id === 0) return

      const apiUrl = 'chats/messages?id_conversation=' + this.chatId +
                      '&id_user=' + this.user.id
      axios.post(apiUrl)
        .then(response => {
          this.messages = response.data;
        })
        .catch(err => {
          this.messages = []
          console.error(err)
        })
    },
    async addMessage(message) {
      if(this.chatId === 0 || this.user.id === 0) return

      this.messages.push(message)
      const apiUrl= 'chats/post_message?id_conv=' + this.chatId +
                    '&id_user=' + this.user.id +
                    '&content=' + message
      axios.post(apiUrl)
        .then(response => {
          console.log(response.data);
        })
        .catch((err) => {
          console.error(err)
        })
    }
  }
}
</script>

<style lang="less" scoped>
.container-chat {
  padding: 0;
  margin: 0;
  max-width: 100%;
  height: 100%;

  .empty-state {
    height: 87vh;
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
  }
}
</style>
