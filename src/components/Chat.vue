<template>
	<div v-if="!joined" class="parent-container">
		<div class="name-container">
			<input
				id="name-input"
				type="text"
				placeholder="Username"
				v-model="username"
				class="user-name"
				@keyup.enter="tryJoin"
				autofocus
			/>
			<button
				:disabled="!username.trim().length"
				@click="join"
				type="button"
				class="join-button"
			>
				Join
			</button>
		</div>
	</div>
	<div v-else>
		<div class="interface">
			<div class="messages">
				<div
					v-for="(message, index) in messages"
					v-bind:key="index"
					name="index"
				>
					<b> {{ message.user }} </b>: {{ message.text }}
				</div>
			</div>
			<div class="users">
				<div v-for="(user, index) in users" v-bind:key="index">
					{{ user }} <br />
				</div>
			</div>
		</div>
		<div class="text-input-container">
			<input
				type="text"
				placeholder="Send a message"
				v-model="text"
				@keyup.enter="sendMessage"
				class="text-message"
				id="messageSender"
			/>
		</div>
	</div>
</template>

<script>
	import io from "socket.io-client";

	export default {
		name: "Chat",
		methods: {
			tryJoin: function () {
				if (document.getElementById("name-input").value.trim().length > 0) {
					this.join();
				}
			},
			join: function () {
				this.joined = true;

				// this.socketInstance = io("http://192.168.178.135:3000", {
				// 	transports: ["websocket", "polling"],
				// });  //Zu Hause
				this.socketInstance = io("http://192.168.7.209:3000", {
					transports: ["websocket", "polling"],
				});  //Schule



				this.$nextTick(() => {
					document.getElementById("messageSender").focus();
					this.socketInstance.emit("username", this.username);
					this.socketInstance.emit("users");
				})

				this.socketInstance.on("users:sent", (users) => {
					this.users = users;
				})

				this.socketInstance.on("message:received", (data) => {
					this.messages = this.messages.concat(data);
					this.$nextTick(() => {
						this.scroll();
					});
				});

				this.socketInstance.on("ping", () => {
					this.socketInstance.emit("pong", this.username);
				})
			},
			sendMessage: function () {
				if (this.text.length <= 0) {
					return;
				}
				const message = {
					id: new Date().getTime(),
					text: this.text,
					user: this.username,
				};
				this.socketInstance.emit("message", message);
				this.text = "";
			},
			scroll: function () {
				let messages = document.getElementsByName("index");
				let el = messages[messages.length - 1];
				let messageBox = document.getElementById("messageSender");
				let height = window.innerHeight-messageBox.scrollHeight;
				let anzahl = height/el.scrollHeight;
				window.scrollTo(0, el.scrollHeight*(messages.length-anzahl+2));
			},
			updateWidth: function(newWidth) {
				this.textBoxWidth = newWidth;
			},
		},
		data() {
			return {
				joined: false,
				messages: [],
				users: [],
				username: "",
				text: "",
				textBoxWidth: 100,
			};
		},
	};
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

	.interface {
		width: 100%;
		display: flex;
	}


	.messages {
		width: 95%;
	}

	.users{
		width: 5%;
		display: flex;
		justify-content: right;
		position: fixed;
		margin-left: 95%;
	}

	.text-input-container {
		height: 95vh;
		width: 100%;
	}

	.text-message {
		width: 100%;
		position: fixed;
		bottom: 0px;
		height: 70px;
		padding: 10px;
		box-sizing: border-box;
	}

</style>
