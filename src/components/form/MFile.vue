<script>
import { useField } from "vee-validate";
import { nanoid } from "nanoid";

export default {
	name: "MFile",

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
		accept: {
			type: String,
			default: ""
		},
		multiple: {
			type: Boolean,
			default: false
		},
		modelValue: {
			default: []
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
		displaySize(val) {
			let i = ~~(Math.log2(val) / 10);
			return (
				(val / Math.pow(1024, i)).toFixed(2) +
				["Bytes", "Kb", "Mb", "Gb", "Tb"][Math.floor(Math.log2(val) / 10)]
			);
		},
		onInput(event) {
			this.modeldata = event.target.files;
			this.handleChange(event);
		},
		onBlur(event) {
			this.handleChange(event);
		},
		setdirty() {
			this.meta.dirty = true;
		}
	},

	computed: {
		modeldata: {
			get() {
				return this.modelValue;
			},
			set(value) {
				this.$emit("update:modelValue", value);
			}
		},
		statustext() {
			if (this.modelValue == null || this.modelValue.length == 0) {
				if (this.multiple) {
					return "Click or drag and drop to upload files.";
				}
				return "Click or drag and drop to upload a file.";
			}

			let html = "";
			for (const file of this.modelValue) {
				html += `${file.name} (${this.displaySize(file.size)})<br>`;
			}
			return html;
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
.mfile {
	border: 1px solid gray;
	border-radius: 2px;

	width: 100%;

	margin-top: 0.75rem;

	padding: 2px;
	padding-left: 5px;
	padding-right: 5px;
}

.minput-element {
	display: inline-flex;
	position: relative;

	border: 1px dashed black;
	padding-top: 1rem;
	padding-bottom: 1rem;
	width: calc(100% - 20px);
	margin: 10px;
	margin-bottom: 0;

	span {
		margin: auto;
		padding-left: 5px;
		padding-right: 5px;
	}

	input {
		position: absolute;
		top: 0;
		left: 0;
		bottom: 0;
		width: 100%;
		opacity: 0;

		cursor: pointer;
	}
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
	<div class="mfile minput">
		<span class="minput-label">{{ label }}</span>
		<label :for="id" :class="valid">
			<span v-html="statustext"></span>

			<!-- File inputs are readonly from JS, so no v-model -->
			<input
				type="file"
				:id="id"
				:name="name"
				@change="onInput"
				@blur="onBlur"
				:accept="accept"
				:multiple="multiple"
			/>
		</label>
		<span class="minput-error">&nbsp;{{ errorMessage }}</span>
	</div>
</template>
