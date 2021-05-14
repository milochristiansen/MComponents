<template>
	<div class="mslider">
		<div class="mslider-controls">
			<svg @click="prev(false)" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 256 512">
				<path
					fill="currentColor"
					d="M31.7 239l136-136c9.4-9.4 24.6-9.4 33.9 0l22.6 22.6c9.4 9.4 9.4 24.6 0 33.9L127.9 256l96.4 96.4c9.4 9.4 9.4 24.6 0 33.9L201.7 409c-9.4 9.4-24.6 9.4-33.9 0l-136-136c-9.5-9.4-9.5-24.6-.1-34z"
					class=""
				></path>
			</svg>
			<svg @click="next(false)" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 256 512">
				<path
					fill="currentColor"
					d="M224.3 273l-136 136c-9.4 9.4-24.6 9.4-33.9 0l-22.6-22.6c-9.4-9.4-9.4-24.6 0-33.9l96.4-96.4-96.4-96.4c-9.4-9.4-9.4-24.6 0-33.9L54.3 103c9.4-9.4 24.6-9.4 33.9 0l136 136c9.5 9.4 9.5 24.6.1 34z"
					class=""
				></path>
			</svg>
		</div>
		<MSliderItems :to="to">
			<slot />
		</MSliderItems>
		<div class="mslider-dots">
			<button v-for="n in itemc" :key="n" @click="goto(n - 1)"></button>
		</div>
	</div>
</template>

<script>
import MSliderItems from "./MSliderItems.vue";

export default {
	name: "MSlider",

	components: {
		MSliderItems
	},

	props: {
		autoplay: {
			type: [Boolean],
			default: false
		},
		speed: {
			type: [Number],
			default: 5000
		}
	},

	data() {
		return {
			to: 0,
			index: 0,
			manual: false
		};
	},

	computed: {
		itemc() {
			return this.$slots.default().filter(item => typeof item.type != "symbol").length;
		}
	},

	methods: {
		goto(n, auto) {
			if (this.autoplay && auto && this.manual) {
				window.setTimeout(() => this.next(true), this.speed);
				return;
			}
			// this.$options.__scopeId is *not documented*. This could break down the line.
			let cards = document.querySelectorAll(
				".mslider[" + this.$options.__scopeId + "] > .mslider-viewport > .mslider-cards > .mslider-card"
			);

			if (cards.length == 0) {
				console.error("No cards in slider.");
				return;
			}

			this.to = n;
			this.index = n;
			if (n >= cards.length - 2) {
				this.index = 0;
			}
			if (n < 0) {
				this.index = cards.length - 3;
			}

			if (this.autoplay && auto && !this.manual) {
				window.setTimeout(() => this.next(true), this.speed);
			}
		},
		prev(auto) {
			this.goto(this.index - 1, auto);

			if (!auto) {
				this.manual = true;
				window.setTimeout(() => (this.manual = false), this.speed * 2);
			}
		},
		next(auto) {
			this.goto(this.index + 1, auto);

			if (!auto) {
				this.manual = true;
				window.setTimeout(() => (this.manual = false), this.speed * 2);
			}
		}
	},

	mounted() {
		this.goto(0, true);
	}
};
</script>

<style scoped lang="scss">
@keyframes fade {
	from {
		opacity: 0;
	}
	to {
		opacity: 1;
	}
}

.mslider {
	position: relative;
	display: flex;
	flex-direction: column;

	min-height: 100px;

	&-controls {
		position: absolute;
		width: 100%;
		z-index: 10;
		top: calc(50% - 25px);

		display: flex;
		justify-content: space-between;
		align-items: flex-start;

		svg {
			height: 50px;
			width: 25px;

			&:hover {
				color: orange;
			}
		}
	}

	&-dots {
		display: flex;
		flex-direction: row;

		& > * {
			border-radius: 50%;
			background-color: gray;
			margin-left: 2px;
			margin-right: 2px;

			&:hover {
				background-color: orange;
			}
		}
		& > .active {
			background-color: black;
		}

		:first-child {
			margin-left: auto;
		}
		:last-child {
			margin-right: auto;
		}
	}
}
</style>
