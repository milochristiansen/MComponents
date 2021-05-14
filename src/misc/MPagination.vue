<style scoped lang="scss">
.mpagination {
	display: inline-flex;
	flex-direction: row;
	justify-content: center;
	width: 100%;

	&-number,
	&-prev,
	&-next {
		color: black;
		float: left;
		padding: 8px 16px;
		text-decoration: none;
		border: 1px solid #ddd;
		border-top-color: black;
		border-bottom-color: black;
	}

	&-prev {
		border-top-left-radius: 5px;
		border-bottom-left-radius: 5px;
		border-left-color: black;
	}
	&-next {
		border-top-right-radius: 5px;
		border-bottom-right-radius: 5px;
		border-right-color: black;
	}
	&-current {
		color: red;
	}
}
</style>

<template>
	<div class="mpagination">
		<a
			:href="href != '' ? strtmpl(href, { n: current - 1 }) : 'javascript:'"
			@click="fire(current - 1)"
			v-if="current - 1 >= 1"
			class="mpagination-prev"
			rel="prev"
		>
			«
		</a>
		<span v-else class="mpagination-prev">«</span>

		<template v-for="n in numbers" :key="n">
			<span v-if="n == '...'" class="mpagination-number">...</span>
			<a
				v-else
				:href="href != '' ? strtmpl(href, { n: n }) : 'javascript:'"
				@click="fire(n)"
				:class="n == current ? 'mpagination-number mpagination-current' : 'mpagination-number'"
			>
				{{ n }}
			</a>
		</template>

		<a
			:href="href != '' ? strtmpl(href, { n: current + 1 }) : 'javascript:'"
			@click="fire(current + 1)"
			v-if="current + 1 <= end"
			class="mpagination-next"
			rel="next"
		>
			»
		</a>
		<span v-else class="mpagination-next">»</span>
	</div>
</template>

<script>
export default {
	name: "MPagination",

	emits: {
		nav: null
	},

	props: {
		buttons: {
			// How many buttons to display maximum, plus next and prev buttons.
			type: Number,
			default: 9
		},
		current: {
			// The currently active page.
			type: Number,
			default: 1
		},
		end: {
			// The last page
			type: Number,
			default: 1
		},
		href: {
			// This takes a template string. Occurrences of {{n}} will be replaced with the target page number.
			// If not specified navigation will need to be triggered by the "nav" event that is still emitted.
			type: String,
			default: ""
		}
	},

	data() {
		return {
			editing: false,

			name: this.Name
		};
	},

	computed: {
		numbers() {
			let offset = Math.floor(this.buttons / 2);
			let start = this.current - offset;
			let end = this.current + offset;

			// If we have this.buttons or fewer items just return them raw.
			// If we have more than we can display on either side then insert a "..." item separating the first/last
			// two items from the closest ones to the current selection.

			if (start < 1) {
				let t = 0 - start;
				start = 1;
				end += t;
			}

			if (end > this.end) {
				end = this.end;
			}

			let ret = [];
			for (let i = start; i <= end; i++) {
				ret.push(i);
			}

			// OK, we have the ends in range, and we generated the list, now go patch the list to insert
			// the first and last items if needed.

			if (this.end > end) {
				ret[end - start] = this.end;
				ret[end - start - 1] = "...";
			}

			if (start > 1) {
				ret[0] = 1;
				ret[1] = "...";
			}

			return ret;
		}
	},

	methods: {
		strtmpl(expression, valueObj) {
			const templateMatcher = /{{\s?([^{}\s]*)\s?}}/g;
			let text = expression.replace(templateMatcher, (substring, value, index) => {
				value = valueObj[value];
				return value;
			});
			return text;
		},

		fire(n) {
			this.$emit("nav", n);
		}
	}
};
</script>
