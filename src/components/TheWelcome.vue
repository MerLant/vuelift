<script setup lang="ts">
import { ref, computed } from 'vue'
import FloorItem from './FloorItem.vue'
import FloorButton from './FloorButton.vue'
import LiftCradle from './LiftCradle.vue'
import { TransitionGroup } from 'vue'

const floors = ref(2)
const maxFlor = 6

const isAddDisabled = computed(() => floors.value >= maxFlor)
const isRemoveDisabled = computed(() => floors.value <= 2)

const currentFloor = ref(1)
const queue = ref<number[]>([])
const preQueue = ref<number[]>([])
const isMoving = ref(false)

function addFloor() {
	if (!isAddDisabled.value) {
		floors.value++
	}
}

function removeFloor() {
	if (!isRemoveDisabled.value) {
		floors.value--
	}

	if (floors.value < currentFloor.value) {
		addToQueue(floors.value) // Исправлено: перемещение лифта на текущий верхний этаж
	}

	if (queue.value.includes(currentFloor.value)) {
		queue.value = queue.value.filter((floor) => floor !== currentFloor.value) // Исправлено: удаление текущего этажа из очереди
	}
}

function addToQueue(floor) {
	if (queue.value.length === 0) {
		queue.value.push(floor)
	} else if (!queue.value.includes(floor) || preQueueueue.value.includes(floor)) {
		if (Math.max(queue.value) > floor) {
			queue.value.push(floor)
			preQueue.value.sort((a, b) => b - a)
		} else {
			preQueue.value.push(floor)
			preQueue.value.sort((a, b) => b - a)
		}
	}
	processQueue()
}

function processQueue() {
	if (isMoving.value) {
		return
	}

	if (queue.value.length === 0) {
		if (currentFloor.value !== 1) {
			setTimeout(() => {
				addToQueue(1)
				processQueue()
			}, 1000)
		} else if (preQueue.value.length > 0) {
			queue.value = preQueue.value.slice()
			preQueue.value = []
			setTimeout(() => {
				processQueue()
			}, 1000)
		}
		return
	}

	isMoving.value = true
	const nextFloor = queue.value[0]

	function moveStep(current, target) {
		if (current === target) {
			queue.value.shift()
			isMoving.value = false
			setTimeout(() => {
				processQueue()
			}, 1000)
			return
		}

		let direction = current < target ? 1 : -1
		setTimeout(() => {
			currentFloor.value += direction
			moveStep(currentFloor.value, target)
		}, 1000)
	}

	setTimeout(() => {
		moveStep(currentFloor.value, nextFloor)
	}, 300)
}
</script>

<template>
	<div class="lift">
		<div class="lift__shaft">
			<TransitionGroup name="floor" tag="div" class="lift__floors">
				<FloorButton v-for="floor in floors" :key="floor" @click="addToQueue(floor)">
					{{ floor }}
				</FloorButton>
			</TransitionGroup>
			<LiftCradle
				:style="{
					bottom: (currentFloor - 1) * 60 + 5 + 'px',
					transition: isMoving ? '' : 'all 1.5s ease-out'
				}"
				class="lift__cradle"
			>
				{{ currentFloor }}
			</LiftCradle>
		</div>
		<div class="lift__button">
			<FloorButton :disabled="isRemoveDisabled" @click="removeFloor"> - </FloorButton>
			<FloorButton :disabled="isAddDisabled" @click="addFloor"> + </FloorButton>
		</div>
	</div>
</template>

<style scoped>
.lift {
	transition: all 0.2s;
	display: flex;
	flex-direction: column;
	align-items: center;
	justify-content: center;
	gap: 10px;
}

.lift__shaft {
	position: relative;
}

.lift__floors {
	display: flex;
	flex-direction: column-reverse;
	gap: 10px;
}

.lift__button {
	border: 1px solid var(--color-border);
	padding: 8px;
	border-radius: 8px;
	display: flex;
	gap: 10px;
}

.lift__cradle {
	position: absolute;
	right: 5px;
	transition: bottom 1s linear;
}

.floor-enter-active,
.floor-leave-active {
	transition:
		opacity 0.2s ease,
		transform 0.2s ease;
}
.floor-enter-from {
	opacity: 0;
	transform: translateY(-30px);
}
.floor-leave-to {
	opacity: 0;
	transform: translateY(30px);
}
</style>
