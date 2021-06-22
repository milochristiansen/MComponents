<script>
import { useField } from "vee-validate";
import { nanoid } from "nanoid";

export default {
	name: "MMultiSelect",

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
		placeholder: {
			type: String,
			default: "Select Items"
		},
		options: {
			default: null
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

	data: () => ({
		focused: false,
		selOptions: {}
	}),

	computed: {
		valid() {
			return this.errorMessage === undefined
				? "minput-element minput-element-valid"
				: "minput-element minput-element-invalid";
		},
		selected() {
			return this.options.filter(i => this.selOptions[i.value]);
		}
	},

	watch: {
		modelValue() {
			this.handleChange(this.modelValue);

			let v = [];
			for (const i of this.modelValue) {
				v[i] = true;
			}
			this.selOptions = v;
		}
	},

	methods: {
		setdirty() {
			this.meta.dirty = true;
		},

		toggle() {
			this.focused = !this.focused;
		},
		hide() {
			this.focused = false;
		},
		select(item) {
			this.selOptions[item.value] = !this.selOptions[item.value];
			this.emitChanges();
		},
		emitChanges() {
			this.$emit(
				"update:modelValue",
				this.options.filter(i => this.selOptions[i.value]).map(i => i.value)
			);
		}
	},

	mounted() {
		document.addEventListener("click", this.hide);
	},
	unmounted() {
		document.removeEventListener("click", this.hide);
	},

	emits: ["update:modelValue"]
};
</script>

<style scoped lang="scss">
.mmultiselect {
	display: flex;
	flex-direction: column;

	position: relative;

	label {
		font-size: 0.75rem;
		opacity: 0;

		&.mmultiselect-show {
			opacity: 1;
		}
	}

	&-body {
		border: 1px solid gray;
		border-radius: 2px;

		width: 100%;

		padding: 10px;
		padding-left: 5px;
		padding-right: 5px;

		button {
			padding: 0;
		}
	}

	&-dropdown {
		position: absolute;
		top: calc(100% - 15px);

		border: 1px solid gray;
		border-radius: 2px;
		border-top: none;

		width: calc(100% - 2px - 10px);

		padding-bottom: 10px;
		padding-left: 5px;
		padding-right: 5px;

		background-color: white;

		display: none;
		flex-direction: column;
		z-index: 2;
	}
	&-focused {
		display: flex;
	}

	&-item {
		padding: 5px;
		margin: 2px;

		border: 1px solid black;
		border-radius: 5px;
	}
	&-selected {
		background-color: lightgray;
	}

	&-itembubble {
		display: inline-block;

		padding: 5px;
		margin: 2px;
		margin-right: 10px;

		border: 1px solid black;
		border-radius: 2px;

		position: relative;

		span {
			display: inline-block;
			padding-right: 8px;
		}

		button {
			display: inline-block;

			position: absolute;
			right: 0;
			top: 0;
			bottom: 0;

			background-color: transparent;

			border: none;
			border-left: solid 1px black;
			border-radius: 5px;
		}
	}

	&-placeholder {
		white-space: nowrap;
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
	<div class="mmultiselect minput">
		<!-- Multi-Select -->
		<label :for="rid" :class="'minput-label' + (selected.length != 0 ? ' mmultiselect-show' : '')">
			{{ label }}
		</label>
		<div :class="'mmultiselect-body ' + valid" @click.stop="toggle">
			<span v-if="selected.length == 0" class="minput-label">{{ label }}</span>
			<div v-else v-for="item in selected" :key="item.text" class="mmultiselect-itembubble">
				<span>{{ item.text }}</span>
				<button @click.stop="select(item)">x</button>
			</div>
			<span v-if="selected.length != 0" class="mmultiselect-placeholder">{{ placeholder }}</span>
		</div>
		<div :class="focused ? 'mmultiselect-dropdown mmultiselect-focused' : 'mmultiselect-dropdown'">
			<span
				v-for="item in options"
				:key="item.text"
				:class="item.selected ? 'mmultiselect-selected mmultiselect-item' : 'mmultiselect-item'"
				@click="select(item)"
			>
				{{ item.text }}
			</span>
		</div>
		<span class="minput-error">&nbsp;{{ errorMessage }}</span>
	</div>
</template>
