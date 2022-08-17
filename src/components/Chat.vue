<template>
    <div v-if="!joined" class="parent-container">
        <div class="name-container">
            <input type="text" placeholder="Username" v-model="username" class="user-name">
            <button v-on:click="join" type="button" class="join-button">Join</button>
        </div>
    </div>
    <div v-else>
        <div class="list-container">
            <div v-for="message in messages" :key="message.id">
                <b>
                    {{ message.user }}
                </b>
                : {{ message.text }}
            </div>
        </div>
        <div class="text-input-container">
            <textarea v-model="text" class="text-message" v-on:keyup.enter="sendMessage"></textarea>
        </div>
    </div>
    <!-- <div v-else>
        <div class="list-container">
            <div v-for="message in messages">
            <b>
                {{ message.user }}
            </b>
            : {{ message.text }}
            </div>
        </div>
        <div class="text-input-container">
            <input type="text" placeholder="Send a message" v-model="text" v-on:keyup.enter="sendMessage"
                class="text-message">
        </div>
    </div> -->
</template>


<script>
const io = require("socket.io-client");

export default {
    name: "Chat",
    methods: {
        join: function () {
            this.joined = true;
            this.socketInstance = io("http://localhost:3000", {
                transports: ["websocket", "polling"],
            }),

                this.socketInstance.on(
                    "message:received", (data) => {
                        this.messages = this.messages.concat(data);
                    }
                );
        },
        sendMessage: function () {
            this.addMessage();
            this.text = "";
        },
        addMessage: function () {
            const message = {
                id: new Date().getTime(),
                text: this.text,
                user: this.username
            }
            this.messages = this.messages.concat(message);
            console.log("Message concated"); //Wird gesendet
            this.socketInstance.emit('message', message);
            console.log("Message rausgeschickt"); //Wird gesendet
        }
    },
    data() {
        return {
            joined: false,
            messages: [],
            username: "",
            text: "",
        }
    }
}
</script>


<style scoped>
.parent-container {
    width: 100%;
    height: 100%;
    display: flex;
    justify-content: center;
    position: fixed;
    padding-top: 150px;
}

.name-container {
    display: flex;
    flex-direction: column;
    width: 200px;
}

.user-name {
    height: 30px;
    font-size: 20px;
    padding: 5px;
    margin-bottom: 5px;
    text-align: center;
    font-weight: bold;
}

.join-button {
    height: 30px;
    font-size: 20px;
}

.text-input-container {
    height: 100vh;
}

.text-message {
    width: 100%;
    position: absolute;
    bottom: 0px;
    height: 70px;
    padding: 10px;
    box-sizing: border-box;
}
</style>