<style lang="scss">
body {
	counter-reset: footnotes;
}
</style>
<style scoped lang="scss">
.mfootnote {
	display: none;

	&:checked {
		& + .mfootnote-label {
			color: red;
		}
	}

	&-content {
		display: none;
	}

	&-show {
		font-size: 85%;
		display: inline;
	}

	&-label {
		display: inline;
		cursor: pointer;

		&:hover {
			cursor: pointer;
			color: red;
		}

		sup::after {
			counter-increment: footnotes;
			content: counter(footnotes);
		}
	}
}
</style>

<template>
	<input class="mfootnote" type="checkbox" :id="id" v-model="checked" />
	<label class="mfootnote-label" :for="id">
		<sup></sup>
	</label>
	<span :class="checked ? 'mfootnote-content mfootnote-show' : 'mfootnote-content'"> <br /><slot></slot><br /> </span>
</template>

<script>
import { nanoid } from "nanoid";

/*
This is a component for simple inline footnotes.

Rather than trying to pop up an overlay or some other system that mobile probably won't like, this simply breaks the
line at the footnote and inserts the footnote content inside. This seems to work pretty well in practice.
*/

export default {
	name: "MFootnote",

	data: () => ({
		id: nanoid(5),
		checked: false
	})
};
</script>
