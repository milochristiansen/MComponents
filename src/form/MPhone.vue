<script>
import { useField } from "vee-validate";
import { nanoid } from "nanoid";

export default {
	name: "MPhone",

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
		},
		updateMask() {
			if (this.modelValue == null || this.modelValue == undefined || this.modelValue == "") {
				this.mask.entered = "";
				this.mask.shown = "(___) ___-____";
				return;
			}

			let v = String(this.modelValue);
			let mask = "";
			let split = 0;
			if (v.length <= 3) {
				let f = v.padEnd(3, "_");
				mask = `(${f}) ___-____`;
				split = v.length + 1;
			} else if (v.length <= 6) {
				let f = v.slice(0, 3);
				let m = v.slice(3).padEnd(3, "_");
				mask = `(${f}) ${m}-____`;
				split = v.length + 3;
			} else {
				let f = v.slice(0, 3);
				let m = v.slice(3, 6);
				let e = v.slice(6).padEnd(4, "_");
				mask = `(${f}) ${m}-${e}`;
				split = v.length + 4;
			}

			this.mask.entered = mask.slice(0, split);
			this.mask.shown = mask.slice(split);
		}
	},

	computed: {
		modeldata: {
			get() {
				this.setvalue(this.modelValue);
				this.updateMask();
				return this.mask.entered;
			},
			set(value) {
				if (typeof value == "string") {
					value = value.replace(/[^\d]/g, "");
				}

				this.$emit("update:modelValue", value);
			}
		},
		valid() {
			return this.errorMessage === undefined
				? "minput-element minput-element-valid"
				: "minput-element minput-element-invalid";
		}
	},

	data: () => ({
		mask: {
			shown: "(___) ___-____",
			entered: ""
		}
	}),

	emits: ["update:modelValue"]
};
</script>

<style scoped lang="scss">
.minput-element-invalid {
	border-color: red;
}

.minput-error {
	font-size: 0.75rem;
	color: red;

	white-space: nowrap;
	overflow: hidden;
	text-overflow: ellipsis;
}

.mphone {
	display: flex;
	flex-flow: column-reverse;

	label,
	input {
		transition: all 0.2s;
		touch-action: manipulation;
	}

	input {
		border: 1px solid gray;
		border-radius: 2px;
		padding: 10px;
	}

	input {
		font-size: 1rem;
		cursor: text;

		&::placeholder {
			opacity: 0;
			transition: inherit;
		}
	}

	input:focus {
		outline: 0;

		&::placeholder {
			opacity: 1;
		}
	}

	label {
		padding-left: 5px;
		font-size: 0.75rem;
		width: 66.66%; // When the label is transformed to full size this is basically 100%
	}

	input:placeholder-shown + label {
		cursor: text;

		white-space: nowrap;
		overflow: hidden;
		text-overflow: ellipsis;

		transform-origin: left bottom;
		transform: translate(0, 1.8rem) scale(1.25);
	}

	input:not(:placeholder-shown) + label,
	input:focus + label {
		transform: translate(0, 0) scale(1);
		cursor: pointer;
	}
}

.mphone {
	position: relative;
	line-height: 1;

	.minput-mask {
		font: monospace;
		padding-right: 10px;
		background-color: transparent;
		pointer-events: none;
		z-index: -1;

		position: absolute;
		left: 11px;
		top: 25px;

		opacity: 0;

		i {
			opacity: 0;
		}
	}

	input {
		font: monospace;
		background-color: transparent;
		padding-right: 10px;
		user-select: none;
	}

	input:not(:placeholder-shown) ~ span,
	input:focus ~ span {
		opacity: 1;
	}
}
</style>

<template>
	<div class="mphone minput">
		<span aria-hidden="true" class="minput-mask" ref="mask">
			<i>{{ mask.entered }}</i>
			{{ mask.shown }}
		</span>
		<span class="minput-error">&nbsp;{{ errorMessage }}</span>
		<input
			type="tel"
			:id="id"
			:name="name"
			@change="onInput"
			@blur="onBlur"
			v-model="modeldata"
			:class="valid"
			placeholder=" "
			maxlength="14"
		/>
		<label :for="id" class="minput-label minput-label-short">{{ label }}</label>
		<span aria-hidden="true" class="minput-mask" ref="mask"
			><i>{{ mask.entered }}</i
			>{{ mask.shown }}</span
		>
	</div>
</template>
