<script setup lang="ts">
import { RouterView } from 'vue-router'
import HelloWorld from './components/HelloWorld.vue'
</script>

<template>
	<header>
		<HelloWorld msg="Лифт" />
	</header>

	<RouterView />
</template>

<style scoped>
header {
	margin-bottom: auto;
}
</style>
