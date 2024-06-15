<template>
	<button class="button">
		<slot />
	</button>
</template>

<style scoped>
.button {
	margin-top: 2rem;
	display: flex;
	position: relative;
	width: 50px;
	height: 50px;

	display: flex;
	align-items: center;
	justify-content: center;

	border-radius: 8px;
	color: var(--color-text);
	border: 1px solid var(--color-border);
	background: var(--color-background);
	margin: 0;
}

.button:hover {
	background: var(--color-border);
}

.button:disabled {
	opacity: 0.5;
}
.button:hover:disabled {
	background: var(--color-background);
}
</style>
