<script>
import { useField } from "vee-validate";
import { nanoid } from "nanoid";

import Prism from "prismjs";
import "prismjs/components/prism-markdown";
import "prismjs/themes/prism.css";

/*
This is a simple code editor.
*/

export default {
	name: "MCode",

	props: {
		name: {
			type: String,
			default: ""
		},
		rules: {
			type: Function
		},
		label: {
			type: String,
			default: ""
		},
		placeholder: {
			type: String,
			default: " " // Not a typo!
		},
		language: {
			type: String,
			default: "markdown"
		},
		height: {
			type: String,
			default: "300px"
		},
		modelValue: {
			default: ""
		}
	},

	setup(props) {
		let id = nanoid(5);

		const { value, errorMessage, handleChange } = useField(id, props.rules || (() => true), {
			initialValue: props.modelValue,
			type: props.type
		});

		return {
			id,
			errorMessage,
			handleChange,
			validateValue: value
		};
	},

	methods: {
		onInput(event) {
			this.handleChange(event);
			this.syncScroll();
		},
		onBlur(event) {
			this.handleChange(event);
		},
		setvalue(value) {
			this.validateValue = value;
		},
		syncScroll() {
			this.$refs.display.scrollTop = this.$refs.edit.scrollTop;
			this.$refs.display.scrollleft = this.$refs.edit.scrollleft;
		},
		checkTab(evnt) {
			if (evnt.keyCode == 9) {
				evnt.preventDefault();
				let ss = this.$refs.edit.selectionStart;
				let se = this.$refs.edit.selectionEnd;

				this.modeldata = this.modeldata.substring(0, ss) + "\t" + this.modeldata.substring(se);
				this.$refs.edit.selectionStart = ss + 1;
				this.$refs.edit.selectionEnd = ss + 1;
			}
		}
	},

	computed: {
		modeldata: {
			get() {
				this.setvalue(this.modelValue);
				return this.modelValue;
			},
			set(value) {
				this.$emit("update:modelValue", value);
			}
		},
		code() {
			return Prism.highlight(this.modeldata, Prism.languages[this.language], this.language);
		},
		valid() {
			return this.errorMessage === undefined
				? "minput-element minput-element-valid"
				: "minput-element minput-element-invalid";
		}
	},

	mounted() {
		this.$refs.root.style.setProperty("--height", this.height);
	},

	emits: ["update:modelValue"]
};
</script>

<style scoped lang="scss">
.minput-error {
	font-size: 0.75rem;
	color: red;

	white-space: nowrap;
	overflow: hidden;
	text-overflow: ellipsis;
}

.mcode {
	display: flex;
	flex-direction: column;

	label,
	input {
		transition: all 0.2s;
		touch-action: manipulation;
	}
}

.minput-element {
	border: 1px solid gray;
	border-radius: 2px;

	font-size: 1rem;
	cursor: text;

	position: relative;

	height: calc(var(--height) + 2px);
	&-interactive,
	&-display {
		padding: 10px;
		border: 0;
		width: 100%;
		height: var(--height);

		position: absolute;
		top: 0;
		left: 0;

		overflow: auto;
	}

	&-interactive,
	&-display,
	&-display * {
		font-size: 16px; // 1rem didn't work correctly.
		font-family: monospace;
		line-height: 20pt;
		tab-size: 4;

		margin: 0;
		border-radius: 2px;
	}

	&-interactive {
		z-index: 1;

		color: transparent;
		background: transparent;
		caret-color: black;

		resize: none;

		white-space: nowrap;
	}

	&-display {
		z-index: 0;
	}

	&:focus {
		outline: 0;

		&::placeholder {
			opacity: 1;
		}
	}
}

.minput-label {
	padding-left: 5px;
	font-size: 0.75rem;
}

.minput-element-invalid {
	border-color: red;
}
</style>

<template>
	<div class="mcode minput" ref="root">
		<label :for="id" class="minput-label">{{ label }}</label>
		<div :class="valid">
			<textarea
				:id="id"
				:name="name"
				@change="onInput"
				@blur="onBlur"
				v-model="modeldata"
				class="minput-element-interactive"
				:placeholder="placeholder"
				ref="edit"
				@scroll="syncScroll"
				@keydown="checkTab"
			>
			</textarea>
			<pre :class="`minput-element-display language-${language}`" ref="display"><!--
				--><code v-html="code"></code><!--
			--></pre>
		</div>
		<span class="minput-error">&nbsp;{{ errorMessage }}</span>
	</div>
</template>
