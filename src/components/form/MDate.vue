<script>
import { useField } from "vee-validate";
import { nanoid } from "nanoid";

export default {
	name: "MDate",

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
		updateMask(v) {
			let cursor = -1;
			if (this.$refs.input) {
				cursor = this.$refs.input.selectionStart;
			}
			if (v == "") {
				this.mask.entered = "";
				this.mask.shown = "MM/DD/YYYY";
				return;
			}

			let mask = "";
			let split = 0;
			if (v.length <= 2) {
				let f = v.padEnd(2, "M");
				mask = `${f}/DD/YYYY`;
				split = v.length;
			} else if (v.length <= 4) {
				let f = v.slice(0, 2);
				let m = v.slice(2).padEnd(2, "D");
				mask = `${f}/${m}/YYYY`;
				split = v.length + 1;
			} else {
				let f = v.slice(0, 2);
				let m = v.slice(2, 4);
				let e = v.slice(4).padEnd(4, "Y");
				mask = `${f}/${m}/${e}`;
				split = v.length + 2;
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
					case 3:
						cursor = 4;
						break;
					case 6:
						cursor = 7;
						break;
					}
				} else {
					switch (cursor) {
					case 6:
						cursor = 5;
						break;
					case 3:
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
		},
		storeAndEmit(v) {
			this.datecode = v;

			if (v.length == 8) {
				// We have a full "valid" date, "parse" and emit the result
				let m = v.slice(0, 2);
				let d = v.slice(2, 4);
				let y = v.slice(4);

				let r = new Date(y, Number(m) - 1, d);
				this.validateValue = r;
				this.$emit("update:modelValue", r);
				return;
			}

			let r = new Date(NaN);
			this.validateValue = r;
			this.$emit("update:modelValue", r);
		}
	},

	watch: {
		modelValue() {
			// DO NOT CALL storeAndEmit!

			// This is a special case because when a partial date is entered the model is set
			// to an invalid date, aka NaN.
			if (this.modelValue instanceof Date && isNaN(this.modelValue)) {
				return;
			}

			this.setvalue(this.modelValue);

			// Easy case, it is a 8 digit numeric string. Assume it is our oddball date format already.
			if (
				typeof this.modelValue == "string" &&
				this.modelValue.length == 8 &&
				this.modelValue.length.replace(/[^\d]/g, "") == 8
			) {
				this.datecode = this.modelValue;
				this.updateMask(this.datecode);
				return;
			}

			// Otherwise, assume it is a valid date string.
			let v = new Date(Date.parse(this.modelValue));
			if (isNaN(v)) {
				// It looks like we assumed wrong. Just refuse to ingest the data.
				this.datecode = "";
				this.updateMask(this.datecode);
				return;
			}

			/* eslint-disable prettier/prettier */
			v = `${
				String(v.getUTCMonth() + 1).padStart(2, "0")
			}${
				String(v.getUTCDate()).padStart(2, "0")
			}${
				String(v.getUTCFullYear()).padStart(2, "0")
			}`;
			/* eslint-enable prettier/prettier */

			this.datecode = v;
			this.updateMask(this.datecode);
		}
	},

	computed: {
		modeldata: {
			get() {
				this.updateMask(this.datecode);
				return this.mask.entered;
			},
			set(value) {
				this.storeAndEmit(String(value).replace(/[^\d]/g, ""));
			}
		},
		valid() {
			return this.errorMessage === undefined
				? "minput-element minput-element-valid"
				: "minput-element minput-element-invalid";
		}
	},

	data: () => ({
		datecode: "",
		mask: {
			shown: "MM/DD/YYYY",
			entered: ""
		}
	}),

	emits: ["update:modelValue"]
};
</script>

<style scoped lang="scss">
.mdate {
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

.mdate {
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
	<div class="mdate minput">
		<span class="minput-error">&nbsp;{{ errorMessage }}</span>
		<input
			type="text"
			inputmode="numeric"
			:id="rid"
			:name="name"
			@change="onInput"
			@blur="onBlur"
			v-model="modeldata"
			:class="valid"
			placeholder=" "
			maxlength="10"
			ref="input"
		/>
		<label :for="rid" class="minput-label">{{ label }}</label>
		<span aria-hidden="true" class="minput-mask" ref="mask">
			<i>{{ mask.entered }}</i
			>{{ mask.shown }}
		</span>
	</div>
</template>
