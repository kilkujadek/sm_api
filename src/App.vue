<template>
  <div id="app">
    <sm />
  </div>
</template>

<script>
import sm from "@/components/sm";

export default {
	name: 'App',
	components: {
		sm
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
	max-width: 900px;
	margin: 60px auto 0;
}

.vjs-tree.is-root {
	position: relative;
	text-align: left;
}

</style>
