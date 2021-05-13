<script>
import { h } from "vue";

export default {
	name: "Slider",

	props: {
		to: {
			type: [Number],
			default: -1
		}
	},

	data() {
		return {
			width: 0,
			index: 0,
			cards: null
		};
	},

	watch: {
		to: function() {
			this.goto(this.to);
		}
	},

	computed: {
		cardStyle() {
			return `width: ${this.width}px;`;
		},
		cardsStyle() {
			return `width: ${this.width * this.cards.length}px;`;
		},
		slots() {
			return this.$slots.default()[0].children.filter(item => typeof item.type != "symbol");
		}
	},

	methods: {
		onresize() {
			this.width = this.$parent.$el.getBoundingClientRect().width;
			this.$el.style.width = this.width;
			this.goto(this.index);
		},
		isadjacent(x, y, l) {
			if (x < -1 || y < -1 || x > l || y > l) {
				return false;
			}

			if (x - 1 == y || x + 1 == y) {
				return true;
			}
			if ((x == 0 && y == l - 1) || (y == 0 && x == l - 1)) {
				return true;
			}
			return false;
		},
		goto(n) {
			let c = this.slots.length;
			if (c == 0) {
				return;
			}

			let cards = document.querySelector(".slider[" + this.$parent.$options.__scopeId + "] > .viewport > .cards");
			if (this.index == n) {
				cards.style.transitionDuration = "0s";
				cards.style.transform = `translate(-${(n + 1) * this.width}px)`;
				return;
			}

			// Wrap index back into range.
			let rn = n;
			if (n < 0) {
				n = c - 1;
				rn = -1;
			}
			if (n >= c) {
				n = 0;
				rn = c;
			}

			let dots = document.querySelectorAll(
				".slider[" + this.$parent.$options.__scopeId + "] > .slider-dots > button"
			);
			for (let i = 0; i < dots.length; i++) {
				dots[i].className = dots[i].className.replace(" active", "");
			}
			dots[n].className += " active";

			// If we are not adjacent to our target, then teleport directly to it.
			if (!this.isadjacent(n, this.index, c)) {
				cards.style.transitionDuration = "0s";
				cards.style.transform = `translate(-${(n + 1) * this.width}px)`;
				this.index = n;
				return;
			}

			// We are "adjacent" to our target, slide over to it.
			cards.style.transitionDuration = "0.5s";
			cards.style.transform = `translate(-${(rn + 1) * this.width}px)`;

			// Now, were we really adjacent to it, or were we wrapping around the end?
			if ((n == 0 && this.index == c - 1) || (n == c - 1 && this.index == 0)) {
				window.setTimeout(() => {
					cards.style.transitionDuration = "0s";
					cards.style.transform = `translate(-${(n + 1) * this.width}px)`;
				}, 505);
			}

			this.index = n;
		}
	},

	render() {
		let slots = this.slots;
		if (slots.length == 0) {
			console.error("No cards in slider.");
			return h("span", "Error: No cards.");
		}
		let cards = slots.map(slot => {
			return h(
				"div",
				{
					class: "card",
					[`${this.$options.__scopeId}`]: "",
					style: this.cardStyle
				},
				slot
			);
		});
		cards.unshift(cards[cards.length - 1]);
		cards.push(cards[1]); // 0 is the copy of the last card
		this.cards = cards;

		return h(
			"div",
			{
				class: "viewport",
				[`${this.$options.__scopeId}`]: ""
			},
			h(
				"div",
				{
					class: "cards",
					[`${this.$options.__scopeId}`]: "",
					style: this.cardsStyle
				},
				cards
			)
		);
	},

	mounted() {
		this.width = this.$parent.$el.getBoundingClientRect().width;
		window.addEventListener("resize", this.onresize);
		document.addEventListener("DOMContentLoaded", () => {
			this.goto(0);
		});
	}
};
</script>

<style scoped lang="scss">
.viewport {
	overflow: hidden;
}
.cards {
	display: flex;
	flex-direction: row;
}
</style>
