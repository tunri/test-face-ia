<template>
	<div class="body-1 black--text">
		<v-icon color="success" v-if="isCorrect">mdi-check-bold</v-icon>
		<v-icon color="error" v-else>mdi-close</v-icon>
		<slot></slot>
	</div>
</template>

<script>
export default {
	name: "ItemValidation",
	props: ["isCorrect"],
};
</script>

<style></style>
