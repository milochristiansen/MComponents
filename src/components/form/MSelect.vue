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

		const { errorMessage, handleChange } = useField(rid, props.rules || (() => true), {
			initialValue: props.modelValue,
			type: props.type
		});

		return {
			rid,
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
		},
		setState() {
			if (!this.$refs.select) {
				// Sometimes this is called before the ref is fully valid. Nothing *seems*
				// to break if you just bail in this case.
				return;
			}
			this.$refs.select.dataset.chosen = this.modelValue;
		}
	},

	computed: {
		modeldata: {
			get() {
				this.handleChange(this.modelValue);
				this.setState();
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
.mselect {
	display: flex;
	flex-flow: column-reverse;
}

.minput-element {
	font-size: 1rem;
	cursor: pointer;

	border-radius: 2px;

	padding: 10px;
	padding-left: 5px;

	background-color: transparent;

	&[data-chosen=""] {
		padding-left: 2px;
	}
}

.minput-element:focus {
	outline: 0;
}

.minput-label {
	padding-left: 5px;
	font-size: 0.75rem;
	width: 66.66%;
}

.minput-element[data-chosen=""] + .minput-label {
	cursor: text;

	white-space: nowrap;
	overflow: hidden;
	text-overflow: ellipsis;

	visibility: hidden;
}

.minput-element:not([data-chosen=""]) + .minput-label,
.minput-element:focus + .minput-label {
	cursor: pointer;
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
	<div class="mselect minput">
		<!-- Select -->
		<span class="minput-error">&nbsp;{{ errorMessage }}</span>
		<select
			:id="rid"
			:name="name"
			@change="onInput"
			@blur="onBlur"
			v-model="modeldata"
			:class="valid"
			data-chosen=""
			ref="select"
		>
			<option value="" disabled hidden>{{ label }}</option>
			<slot />
		</select>
		<label :for="rid" class="minput-label">{{ label }}</label>
	</div>
</template>
