<script setup lang="ts">
import { ref } from 'vue'
import Lift from '../components/FinishLift.vue'
import FloorButton from '../components/FloorButton.vue'

const lifts = ref(1)
const maxLifts = ref(3)

function addLift() {
	if (lifts.value > maxLifts.value) {
		return
	}

	lifts.value++
}
function removeLift() {
	if (lifts.value - 1 < 1) {
		return
	}

	lifts.value--
}
</script>

<template>
	<main>
		<FloorButton @click="removeLift" :disabled="lifts - 1 < 1">-</FloorButton>
		<div class="lift-block">
			<Lift v-for="(lift, index) in lifts" :key="index" />
		</div>
		<FloorButton @click="addLift" :disabled="lifts > maxLifts">+</FloorButton>
	</main>
</template>
<style scoped>
main {
	display: flex;
	align-items: flex-end;
	gap: 40px;
}

.lift-block {
	display: flex;
	flex-direction: row;
	align-items: flex-end;
	gap: 10px;
}
</style>
