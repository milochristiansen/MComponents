<script>
import { h } from "vue";

import { nanoid } from "nanoid";

export default {
	name: "MTabs",

	props: {
		// This tracks the visible tab.
		modelValue: {
			type: [Number],
			default: 0
		}
	},

	data: () => ({
		ids: []
	}),

	computed: {
		tabSlots() {
			return this.$slots.tabs().filter(item => typeof item.type != "symbol");
		},
		buttonSlots() {
			return this.$slots.labels().filter(item => typeof item.type != "symbol");
		},
		show: {
			get() {
				//console.log("receive:", this.modelValue)
				this.updateClasses(this.modelValue);
				return this.modelValue;
			},
			set(value) {
				//console.log("send:", value)
				this.updateClasses(value);
				this.$emit("update:modelValue", value);
			}
		}
	},

	methods: {
		updateClasses(idx) {
			for (let i = 0; i < this.ids.length; i++) {
				let el = document.getElementById(this.ids[i] + "-t");
				if (el != null) {
					el.className = `mtabs-tab ${idx == i ? "mtabs-tab-active" : "mtabs-tab-hidden"}`;
				}

				el = document.getElementById(this.ids[i] + "-b");
				if (el != null) {
					el.className = `mtabs-button ${idx == i ? "mtabs-button-active" : "mtabs-button-hidden"}`;
				}
			}
		}
	},

	render() {
		if (this.tabSlots.length == 0) {
			console.error("No tabs in container.");
			return h("span", "Error: No tabs.");
		}

		if (this.tabSlots.length != this.buttonSlots.length) {
			console.error("Tab/header length mismatch.");
			return h("span", "Error: Tab/header mismatch.");
		}

		let buttons = this.buttonSlots.map((slot, i) =>
			h(
				"button",
				{
					class: ["mtabs-button", this.show == i ? "mtabs-button-active" : "mtabs-button-hidden"],
					id: this.ids[i] + "-b",
					onClick: () => {
						this.show = i;
					}
				},
				slot
			)
		);

		let tabs = this.tabSlots.map((slot, i) =>
			h(
				"div",
				{
					class: ["mtabs-tab", this.show == i ? "mtabs-tab-active" : "mtabs-tab-hidden"],
					id: this.ids[i] + "-t"
				},
				slot
			)
		);

		return h(
			"div",
			{
				class: "mtabs"
			},
			[buttons, tabs]
		);
	},

	created() {
		this.ids = this.tabSlots.map(slot => nanoid(5));
	}
};
</script>

<style scoped lang="scss">
.mtabs {
	border: 1px solid black;
	border-radius: 5px;

	padding: 10px;
	padding-top: 0;

	&-button {
		border: 1px solid black;
		border-top: none;
		border-bottom-left-radius: 5px;
		border-bottom-right-radius: 5px;

		margin-right: 5px;
		margin-bottom: 10px;

		padding: 10px;
		padding-bottom: 5px;

		background-color: transparent;

		&-active {
			background-color: lightgray;
		}
	}

	&-tab {
		&-hidden {
			display: none;
		}
	}
}
</style>
