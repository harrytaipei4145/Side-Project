<template>
	<game-page/>
</template>

<script>
import GamePage from './components/game-page.vue'

export default {
	name: 'App',
	components: {
		GamePage
	}
}
</script>

<style>
#app {
	font-family: Avenir, Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	text-align: center;
	color: #2c3e50;
}
</style>
