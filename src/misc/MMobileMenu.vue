<template>
	<nav role="navigation" class="mmobilemenu">
		<input :id="id" type="checkbox" class="mmobilemenu-magic" />
		<label :for="id" class="mmobilemenu-button"></label>

		<div class="mmobilemenu-items">
			<slot></slot>
		</div>
	</nav>
</template>

<style scoped lang="scss">
.mmobilemenu {
	position: relative;
	width: fit-content;

	&-button {
		width: 2em;
		margin: 0.3em;
		display: block;

		position: relative;
		z-index: 2;

		&:before,
		&:after {
			content: "";
			display: block;

			height: 0.3em;
			width: 100%;
			z-index: 2;

			border-top: 0.3em solid black;
			border-bottom: 0.3em solid black;
		}
		&:before {
			margin-top: 0.3em;
		}
		&:after {
			border-top: none;
		}
	}

	&-magic {
		position: absolute;
		opacity: 0;
		margin: 0;
		width: 0px;
		height: 0px;
		top: 0;
	}

	&-items {
		position: absolute;

		left: -300px;
		transition: left 0.3s ease;

		width: 300px;
		margin: 0;
		margin-top: -100%;

		padding: 10px;
		padding-top: 100%;

		background: lightgray;
	}

	&-magic:checked ~ &-items {
		left: 0;
	}
}
</style>

<script>
import { nanoid } from "nanoid";

export default {
	name: "MMobileMenu",

	data: () => ({
		id: nanoid(5)
	})
};
</script>
