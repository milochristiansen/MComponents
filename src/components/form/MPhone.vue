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
		id: {
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
		modelValue: {
			default: ""
		}
	},

	setup(props) {
		let rid = props.id == "" ? nanoid(5) : props.id;

		const { value, errorMessage, handleChange } = useField(rid, props.rules || (() => true), {
			initialValue: props.modelValue,
			type: props.type
		});

		return {
			rid,
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
			let cursor = -1;
			if (this.$refs.input) {
				cursor = this.$refs.input.selectionStart;
			}
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

			let backspacing = false;
			if (this.mask.entered && this.mask.entered.length > split) {
				backspacing = true;
			}

			this.mask.entered = mask.slice(0, split);
			this.mask.shown = mask.slice(split);

			if (cursor != -1) {
				/* eslint-disable prettier/prettier */
				if (!backspacing) {
					switch (cursor) {
					case 1:
						cursor = 2;
						break;
					case 5:
						cursor = 7;
						break;
					case 10:
						cursor = 11;
					}
				} else {
					switch (cursor) {
					case 10:
						cursor = 9;
						break;
					case 6:
						cursor = 4;
						break;
					case 1:
						cursor = 2;
						break;
					}
				}
				/* eslint-enable prettier/prettier */

				// This avoids a race condition between setting the cursor and setting the value,
				// a race condition that the cursor mostly loses.
				this.$nextTick(() => {
					this.$refs.input.setSelectionRange(cursor, cursor);
				});
			}
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
.mphone {
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
		transform: translate(0, 1.8rem) scale(1.25);
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

.mphone {
	position: relative;
	line-height: 1;
}

.minput-mask {
	border: 1px solid transparent;
	border-radius: 2px;
	padding: 10px;

	display: inline-block;

	font-size: 1rem;
	font-weight: 500;
	color: lightgray;
	background-color: transparent;
	pointer-events: none;
	z-index: -1;

	position: absolute;
	top: 0.75rem;
	bottom: 0.75rem;

	line-height: 1.1;

	opacity: 0;

	i {
		opacity: 0;
	}
}

.minput-element {
	background-color: transparent;
	padding-right: 10px;
	user-select: none;
}

.minput-element:not(:placeholder-shown) ~ span,
.minput-element:focus ~ span {
	opacity: 1;
}

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
</style>

<template>
	<div class="mphone minput">
		<span class="minput-error">&nbsp;{{ errorMessage }}</span>
		<input
			type="tel"
			:id="rid"
			:name="name"
			@change="onInput"
			@blur="onBlur"
			v-model="modeldata"
			:class="valid"
			placeholder=" "
			maxlength="14"
			ref="input"
		/>
		<label :for="rid" class="minput-label">{{ label }}</label>
		<span aria-hidden="true" class="minput-mask" ref="mask">
			<i>{{ mask.entered }}</i
			>{{ mask.shown }}
		</span>
	</div>
</template>
