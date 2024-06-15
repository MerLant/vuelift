<template>
	<div class="lift">
		<slot />
	</div>
</template>

<style scoped>
.lift {
	display: flex;
	align-items: center;
	justify-content: center;

	color: var(--color-text);

	width: 40px;
	height: 40px;
	border-radius: 8px;
	background: hsla(160, 100%, 37%, 1);
}
</style>
