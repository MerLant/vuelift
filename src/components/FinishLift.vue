<script setup lang="ts">
import { ref, computed } from 'vue'
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
const isJustStarted = ref(false)
const isNextEnded = ref(false)

function addFloor() {
	if (!isAddDisabled.value) {
		floors.value++
	}
}

function getCurrentSpeed() {
	if (isJustStarted.value || isNextEnded.value) {
		return 1000
	} else return 800
}

function removeFloor() {
	if (isRemoveDisabled.value) {
		return
	}

	if (queue.value.includes(floors.value)) {
		queue.value = queue.value.filter((floor) => floor !== floors.value)
		processQueue()
	}

	if (floors.value < currentFloor.value) {
		addToQueue(floors.value)
	}

	floors.value--
}

function addToQueue(floor: number) {
	if (queue.value.includes(floor) || preQueue.value.includes(floor)) {
		return
	}

	if ((queue.value.length === 0 && currentFloor.value === 1) || floor === 1) {
		queue.value.push(floor)
	} else {
		if (Math.max(...queue.value) > floor) {
			queue.value.push(floor)
			queue.value.sort((a, b) => b - a)
		} else if (currentFloor.value > floor && queue.value[0] < floor) {
			queue.value.unshift(floor)
			queue.value.sort((a, b) => b - a)
		} else {
			preQueue.value.push(floor)
			preQueue.value.sort((a, b) => b - a)
		}
	}
	processQueue()
}

function moveStep() {
	if (currentFloor.value === queue.value[0]) {
		queue.value.shift()
		isMoving.value = false
		isNextEnded.value = false
		setTimeout(() => {
			processQueue()
		}, 1000)
		return
	}

	let direction = currentFloor.value < queue.value[0] ? 1 : -1

	if (currentFloor.value + direction > floors.value || currentFloor.value + direction < 1) {
		isMoving.value = false
		isJustStarted.value = false
		return
	}

	if (currentFloor.value + direction === queue.value[0]) {
		isNextEnded.value = true
	}

	currentFloor.value += direction
	setTimeout(() => {
		isJustStarted.value = false
		moveStep()
	}, getCurrentSpeed())
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
	isJustStarted.value = true
	moveStep()
}
</script>

<template>
	<div class="lift">
		<div class="lift__shaft">
			<TransitionGroup name="lift__floor" tag="div" class="lift__floors">
				<FloorButton v-for="floor in floors" :key="floor" @click="addToQueue(floor)">
					{{ floor }}
				</FloorButton>
			</TransitionGroup>
			<LiftCradle
				:style="{
					bottom: `${(currentFloor - 1) * 60 + 5}px`,
					transition: isNextEnded
						? 'all 1.5s ease-out'
						: isJustStarted
							? 'all 1.2s ease-in'
							: ''
				}"
				class="lift__cradle"
			>
				{{ currentFloor }}
			</LiftCradle>
		</div>
		<div class="lift__buttons">
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

.lift__buttons {
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

.lift__floor-enter-active,
.lift__floor-leave-active {
	transition:
		opacity 0.2s ease,
		transform 0.2s ease;
}

.lift__floor-enter-from {
	opacity: 0;
	transform: translateY(-30px);
}

.lift__floor-leave-to {
	opacity: 0;
	transform: translateY(30px);
}
</style>
