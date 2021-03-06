<script>
import { useField } from "vee-validate";
import { nanoid } from "nanoid";

export default {
	name: "MRange",

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
		min: {
			type: Number,
			default: 1
		},
		max: {
			type: Number,
			default: 10
		},
		step: {
			type: Number,
			default: 1
		},
		list: {
			// Elements may be a bare number or an object with "k" and "v" keys for the label and value.
			// Note that no browser proper supports labels yet.
			type: Array,
			default: null
		},
		modelValue: {
			default: ""
		}
	},

	setup(props) {
		let rid = props.id == "" ? nanoid(5) : props.id;
		let listid = nanoid(5);

		const { value, errorMessage, handleChange } = useField(rid, props.rules || (() => true), {
			initialValue: props.modelValue,
			type: props.type
		});

		return {
			rid,
			listid,
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
.mrange {
	display: flex;
	flex-direction: column;
}

.minput-element {
	font-size: 1rem;
	cursor: pointer;

	margin: 0;

	/* Takes up space, however it doesn't actually display.
	For now I'm not wasting time figuring out what is wrong with it. */
	border: 1px solid black;
	border-radius: 2px;

	&::placeholder {
		opacity: 0;
		transition: inherit;
	}
}

.minput-label {
	padding-left: 5px;
	font-size: 0.75rem;
	width: 66.66%;
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
	<div class="mrange minput">
		<label :for="rid" class="minput-label">{{ label }}</label>
		<input
			type="range"
			:id="rid"
			:name="name"
			@change="onInput"
			@blur="onBlur"
			v-model="modeldata"
			:class="valid"
			:min="min"
			:max="max"
			:step="step"
			:list="listid"
		/>
		<datalist v-if="list" :id="listid">
			<option v-for="i in list" :key="i.v ? i.v : i" :value="i.v ? i.v : i" :label="i.k ? i.k : ''"></option>
		</datalist>
		<span class="minput-error">&nbsp;{{ errorMessage }}</span>
	</div>
</template>
