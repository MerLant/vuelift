<template>
	<div class="item">
		<slot />
	</div>
</template>

<style scoped>
.item {
	display: flex;
	align-items: center;
	justify-content: center;

	color: var(--color-text);

	width: 50px;
	height: 50px;
	border-radius: 8px;
	border: 1px solid var(--color-border);
	background: var(--color-background);
}


</style>
