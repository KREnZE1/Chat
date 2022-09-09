<template>
	<Chat></Chat>
</template>

<script>
	import Chat from "./components/Chat.vue";

	export default {
		name: "App",
		components: {
			Chat
		},

	}
</script>

<style>
	body {
		margin: 0px;
	}

	#app {
		font-family: Avenir, Helvetica, Arial, sans-serif;
		-webkit-font-smoothing: antialiased;
		color: #2c3e50;
		margin-top: 10px;
	}
</style>
