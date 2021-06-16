<template>
	<section v-html="html" class="mmarkdown"></section>
</template>

<script>
/*
	I want to make this so that you put markdown source in the default slot, and get HTML dropped into the document.
	I don't think this will work right now. Vue does some weirdness with whitespace in slots.

	So, until they fix their shit we use a property.
*/

import marked from "marked";

export default {
	name: "MMarkdown",

	props: {
		src: {
			type: String,
			default: ""
		},
		filter: {
			// This needs to be a function taking a DOM fragment and returning another (or the same) DOM fragment.
			// Use to sanitize output or do other filtering.
			type: Function
		}
	},

	data: () => ({
		html: ""
	}),

	watch: {
		src() {
			this.update();
		}
	},

	mounted() {
		this.update();
	},

	methods: {
		update() {
			if (this.filter != null || this.filter != undefined) {
				this.html = this.filter(marked(this.src));
				return;
			}
			this.html = marked(this.src);
		}
	}
};
</script>
