<script>
import { useField } from "vee-validate";
import { nanoid } from "nanoid";

export default {
	name: "MCheckbox",

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
			default: undefined
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
		handleIndeterminate() {
			if (this.modelValue === null || this.modelValue === undefined || this.modelValue === "") {
				if (this.$refs.checkbox != undefined) {
					this.$refs.checkbox.indeterminate = true;
				}
			}
		}
	},

	computed: {
		modeldata: {
			get() {
				this.setvalue(this.modelValue);
				this.handleIndeterminate();
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
		},
		thumbClass() {
			/* eslint-disable indent */
			switch (this.modeldata) {
				case true:
					return "mcheckbox-thumb mcheckbox-thumb-true";
				case false:
					return "mcheckbox-thumb mcheckbox-thumb-false";
				default:
					return "mcheckbox-thumb mcheckbox-thumb-undefined";
			}
			/* eslint-enable indent */
		}
	},

	emits: ["update:modelValue"]
};
</script>

<style scoped lang="scss">
.mcheckbox {
	display: flex;
	flex-direction: column;
}

.minput-element {
	display: flex;
	flex-direction: row;

	border: 1px solid gray;
	border-radius: 2px;

	width: 100%;

	padding: 10px;
	padding-left: 5px;
	padding-right: 5px;
	margin-top: 0.75rem;

	input {
		width: 0;
		height: 0;
		margin: 0;
		padding: 0;
		opacity: 0;
		position: absolute;
	}
}

.mcheckbox-thumb {
	position: relative;

	margin-left: auto;
	cursor: pointer;

	width: 50px;

	border: 1px solid black;
	border-radius: 0.5rem;

	transition: 0.4s;

	background-color: red;

	&:before {
		position: absolute;

		border-radius: 50%;
		background-color: white;

		content: "";

		top: 4px;
		left: 4px;
		bottom: 4px;
		width: 10px;
	}
}

input:checked + .mcheckbox-thumb {
	background-color: green;

	&:before {
		transform: translateX(calc(50px - 1.1rem));
	}
}

input:indeterminate + .mcheckbox-thumb {
	background-color: gray;
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
	<div class="mcheckbox minput">
		<label :for="rid" :class="valid">
			<span class="minput-label">{{ label }}</span>
			<input
				type="checkbox"
				:id="rid"
				:name="name"
				@change="onInput"
				@blur="onBlur"
				v-model="modeldata"
				ref="checkbox"
			/>
			<div :class="thumbClass"></div>
		</label>
		<span class="minput-error">&nbsp;{{ errorMessage }}</span>
	</div>
</template>
