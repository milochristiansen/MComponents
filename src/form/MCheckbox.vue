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
			default: undefined
		}
	},

	setup(props) {
		let id = nanoid(5);

		const { value, errorMessage, handleChange } = useField(id, props.rules || (() => true), {
			initialValue: props.modelValue,
			type: props.type,
			valueProp: props.value
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
		labelClass(state) {
			/* eslint-disable indent */
			switch (this.modeldata) {
				case true:
					return state ? "mcheckbox-active mcheckbox-yes" : "mcheckbox-inactive mcheckbox-no";
				case false:
					return state ? "mcheckbox-inactive mcheckbox-yes" : "mcheckbox-active mcheckbox-no";
				default:
					return state ? "mcheckbox-undefined mcheckbox-yes" : "mcheckbox-undefined mcheckbox-no";
			}
			/* eslint-enable indent */
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
				this.$emit("update:modelValue", Boolean(value == "true"));
			}
		},
		valid() {
			return this.errorMessage === undefined
				? "input-element input-element-valid"
				: "input-element input-element-invalid";
		},
		thumbClass() {
			/* eslint-disable indent */
			switch (this.modeldata) {
				case true:
					return "mcheckbox-inner-slider-thumb mcheckbox-inner-slider-thumb-active";
				case false:
					return "mcheckbox-inner-slider-thumb mcheckbox-inner-slider-thumb-inactive";
				default:
					return "mcheckbox-inner-slider-thumb mcheckbox-inner-slider-thumb-undefined";
			}
			/* eslint-enable indent */
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

.mcheckbox {
	display: flex;
	flex-direction: column;

	font-size: 1rem;

	&-inner {
		display: flex;
		flex-direction: row;

		border: 1px solid gray;
		border-radius: 2px;

		padding: 10px;

		width: 100%;

		margin-top: 0.75rem;

		padding: 10px;
		padding-left: 5px;

		span {
			flex: 1;
		}
		&-slider {
			position: relative;
			display: flex;
			flex-direction: row;

			background-color: white;
			border-radius: 5px;

			&-thumb {
				position: absolute;
				width: 50%;
				height: 100%;
				//height: calc(100% + 10px);
				//top: -5px;

				border-radius: 5px;

				transition: left 1s ease;

				&-active {
					background-color: lightgreen;
				}
				&-inactive {
					left: 50%;
					background-color: red;
				}
			}

			label {
				width: 45px;
				text-align: center;
				overflow: hidden;
				position: relative;

				display: flex;
				justify-content: center;
				align-content: center;
				flex-direction: column;
			}
		}

		input {
			width: 0;
			height: 0;
			margin: 0;
			padding: 0;
			opacity: 0;
			position: absolute;
		}
	}

	label.mcheckbox-active.mcheckbox-yes {
		cursor: pointer;
	}
}
</style>

<template>
	<div class="mcheckbox minput">
		<div class="mcheckbox-inner minput-element">
			<span class="minput-label minput-label-long">{{ label }}</span>
			<div class="mcheckbox-inner-slider">
				<div :class="thumbClass"></div>
				<label :for="id + '-yes'" :class="labelClass(true)">
					Yes
					<input
						type="radio"
						:id="id + '-yes'"
						:name="name"
						@change="onInput"
						@blur="onBlur"
						v-model="modeldata"
						:class="valid"
						value="true"
					/>
				</label>
				<label :for="id + '-no'" :class="labelClass(false)">
					No
					<input
						type="radio"
						:id="id + '-no'"
						:name="name"
						@change="onInput"
						@blur="onBlur"
						v-model="modeldata"
						:class="valid"
						value="false"
					/>
				</label>
			</div>
		</div>
		<span class="minput-error">&nbsp;{{ errorMessage }}</span>
	</div>
</template>
