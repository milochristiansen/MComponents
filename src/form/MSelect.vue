<script>
import { useField } from "vee-validate";
import { nanoid } from "nanoid";

export default {
	name: "MSelect",

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

		const { errorMessage, handleChange } = useField(id, props.rules || (() => true), {
			initialValue: props.modelValue,
			type: props.type
		});

		return {
			id,
			errorMessage,
			handleChange
		};
	},

	methods: {
		onInput(event) {
			this.modeldata = event.target.value;
			this.handleChange(event);
		},
		onBlur(event) {
			//this.handleBlur(event);
			this.handleChange(event);
		},
		setdirty() {
			this.meta.dirty = true;
		}
	},

	computed: {
		modeldata: {
			get() {
				this.handleChange(this.modelValue);
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

.mselect {
	display: flex;
	flex-flow: column-reverse;

	select {
		font-size: 1rem;
		cursor: pointer;

		padding: 10px;
	}

	select:focus {
		outline: 0;
	}

	label {
		padding-left: 5px;
		font-size: 0.75rem;
		width: 66.66%;
	}

	select[data-chosen=""] + label {
		cursor: text;

		white-space: nowrap;
		overflow: hidden;
		text-overflow: ellipsis;

		visibility: hidden;
	}

	select:not([data-chosen=""]) + label,
	select:focus + label {
		cursor: pointer;
	}
}
</style>

<template>
	<div class="mselect minput">
		<!-- Select -->
		<span class="minput-error">&nbsp;{{ errorMessage }}</span>
		<select
			:id="id"
			:name="name"
			@change="onInput"
			@blur="onBlur"
			v-model="modeldata"
			:class="valid"
			data-chosen=""
			onchange="this.dataset.chosen = this.value;"
		>
			<option value="" disabled hidden>{{ label }}</option>
			<slot />
		</select>
		<label :for="id" class="minput-label minput-label-short">{{ label }}</label>
	</div>
</template>
