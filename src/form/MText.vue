<script>
import { useField } from "vee-validate";
import { nanoid } from "nanoid";

export default {
	name: "MText",

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
		value: {
			default: undefined
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
		},
		onBlur(event) {
			this.handleChange(event);
		},
		setvalue(value) {
			this.validateValue = value;
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
		valid() {
			return this.errorMessage === undefined
				? "minput-element minput-element-valid"
				: "minput-element minput-element-invalid";
		}
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

.mtext {
	display: flex;
	flex-flow: column-reverse;

	label,
	input {
		transition: all 0.2s;
		touch-action: manipulation;
	}
}

.minput-element {
	border: 1px solid gray;
	border-radius: 2px;
	padding: 10px;

	font-size: 1rem;
	cursor: text;

	&::placeholder {
		opacity: 0;
		transition: inherit;
	}

	&:focus {
		outline: 0;

		&::placeholder {
			opacity: 1;
		}
	}

	&:placeholder-shown + label {
		cursor: text;

		white-space: nowrap;
		overflow: hidden;
		text-overflow: ellipsis;

		transform-origin: left bottom;
		transform: translate(0, 1.9rem) scale(1.25);
	}

	&:not(:placeholder-shown) + label,
	&:focus + label {
		transform: translate(0, 0) scale(1);
		cursor: pointer;
	}
}

.minput-label {
	padding-left: 5px;
	font-size: 0.75rem;
	width: 66.66%; // When the label is transformed to full size this is basically 100%
}

.minput-element-invalid {
	border-color: red;
}
</style>

<template>
	<div class="mtext minput">
		<span class="minput-error">&nbsp;{{ errorMessage }}</span>
		<input
			type="text"
			:id="id"
			:name="name"
			@change="onInput"
			@blur="onBlur"
			v-model="modeldata"
			:class="valid"
			:placeholder="placeholder"
		/>
		<label :for="id" class="minput-label">{{ label }}</label>
	</div>
</template>
