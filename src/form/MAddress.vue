<script>
import MText from "./MText.vue";

export default {
	components: {
		MText
	},

	props: {
		modelValue: {
			default: "",
			validator(value) {
				if (typeof value != "object") {
					return false;
				}

				if (typeof value.line1 != "string") {
					return false;
				}
				if (typeof value.line2 != "string") {
					return false;
				}
				if (typeof value.city != "string") {
					return false;
				}
				if (typeof value.state != "string") {
					return false;
				}
				if (typeof value.zip != "string") {
					return false;
				}

				return true;
			}
		}
	},

	setup() {
		return {
			lastauto: new Date()
		};
	},

	data() {
		return {
			acdata: [],
			acshow: false,

			nr_line1: "",
			nr_line2: "",
			nr_city: "",
			nr_state: "",
			nr_zip: ""
		};
	},

	watch: {
		modelValue() {
			this.nr_line1 = this.modelValue.line1;
			this.nr_line2 = this.modelValue.line2;
			this.nr_city = this.modelValue.city;
			this.nr_state = this.modelValue.state;
			this.nr_zip = this.modelValue.zip;
		}
	},

	computed: {
		filtered() {
			return this.acdata.filter(item => item != undefined);
		},

		line1: {
			get() {
				return this.nr_line1;
			},
			set(v) {
				this.nr_line1 = v;
				this.emitValues();
			}
		},
		line2: {
			get() {
				return this.nr_line2;
			},
			set(v) {
				this.nr_line2 = v;
				this.emitValues();
			}
		},
		state: {
			get() {
				return this.nr_state;
			},
			set(v) {
				this.nr_state = v;
				this.emitValues();
			}
		},
		city: {
			get() {
				return this.nr_city;
			},
			set(v) {
				this.nr_city = v;
				this.emitValues();
			}
		},
		zip: {
			get() {
				return this.nr_zip;
			},
			set(v) {
				this.nr_zip = v;
				this.emitValues();
			}
		}
	},

	methods: {
		autocomplete() {
			if (this.line1 == "") {
				return;
			}

			const now = new Date();
			if (now - this.lastauto < 250) {
				return;
			}
			this.lastauto = now;

			// Uncomment this when the geocoder is deployed.
			// fetch("<auto-complete URL>" + encodeURIComponent(this.line1))
			// 	.then(response => response.json())
			// 	.then(data => {
			// 		for (let i = 0; i < 10; i++) {
			// 			this.acdata[i] = data.features[i];
			// 		}
			// 	});
		},
		fill(key) {
			this.line1 = this.acdata[key].properties.name;
			this.city = this.acdata[key].properties.locality;
			this.state = this.acdata[key].properties.region_a;
			this.zip = this.acdata[key].properties.postalcode;
		},

		validateAddr(value) {
			if (value === "") {
				return "Address must be provided.";
			}
			return true;
		},
		validateCity(value) {
			if (value === "") {
				return "City must be provided.";
			}
			return true;
		},
		validateState(value) {
			if (value === "") {
				return "State must be provided.";
			}
			return true;
		},
		validateZip(value) {
			if (value === "") {
				return "Zip code must be provided.";
			}
			return true;
		},
		notempty(value) {
			if (value === "") {
				return "Value must be provided.";
			}
			return true;
		},
		emitValues() {
			this.$emit("update:modelValue", {
				line1: String(this.nr_line1),
				line2: String(this.nr_line2),
				city: String(this.nr_city),
				state: String(this.nr_state),
				zip: String(this.nr_zip)
			});
		}
	},

	emits: ["update:modelValue"]
};
</script>

<style scoped lang="scss">
.maddress {
	display: flex;
	flex-direction: column;

	&-toprow {
		position: relative;

		input {
			width: 100%;
			box-sizing: border-box;
		}
		datalist {
			z-index: 10000;
			display: block;
			position: absolute;
			background: white;
		}
		option {
			z-index: 10001;
			cursor: pointer;
		}
	}

	&-bottomrow {
		display: flex;
		flex-direction: row;

		.city {
			flex: 8 2 auto;
			min-width: 1em;
		}
		.state {
			flex: 3 1 5em;
			min-width: 1em;

			margin-left: 5px;
			margin-right: 5px;
		}
		.zip {
			flex: 4 2 8em;
			min-width: 1em;
		}
	}
}
</style>

<template>
	<div class="maddress">
		<div class="maddress-toprow">
			<MText
				type="address1"
				class="fullrow"
				label="Street Address"
				placeholder="1234 S Main"
				v-model="line1"
				:rules="validateAddr"
				@input="autocomplete"
				@focus="acshow = true"
				@blur="acshow = false"
			/>
			<datalist v-show="acshow">
				<option
					v-for="(item, key) in filtered"
					:key="key"
					@click.prevent="
						acshow = false;
						fill(key);
					"
					@mousedown.prevent
				>
					{{ item.properties.label }}
				</option>
			</datalist>
		</div>

		<MText type="address2" label="Line 2" placeholder="Apt. 5" v-model="line2" />

		<div class="maddress-bottomrow">
			<MText type="city" class="city" label="City" placeholder="Sometown" v-model="city" :rules="validateCity" />
			<MText
				type="state"
				class="state"
				label="State"
				placeholder="Somestate"
				v-model="state"
				:rules="validateState"
			/>
			<MText type="zip" class="zip" label="Zip" placeholder="1234567" v-model="zip" :rules="validateZip" />
		</div>
	</div>
</template>
