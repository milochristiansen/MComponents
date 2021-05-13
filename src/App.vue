<template>
	<Form :submit="submit">
		<MAddress v-model="valAddress" />
		<p>&nbsp;{{ valAddress }}</p>
		<hr />
		<MCheckbox :rules="notempty" label="Yes or No?" v-model="valCheckbox" />
		<p>&nbsp;{{ valCheckbox }}</p>
		<hr />
		<MDate :rules="notempty" label="Enter a Date" v-model="valDate" />
		<p>&nbsp;{{ valDate }}</p>
		<hr />
		<MFile :rules="notempty" label="Upload Something" v-model="valFile" />
		<p>&nbsp;{{ valFile }}</p>
		<hr />
		<MNumber :rules="notempty" label="Enter a Number" placeholder="123456567890" v-model="valNumber" />
		<p>&nbsp;{{ valNumber }}</p>
		<hr />
		<MPhone :rules="notempty" label="Enter a Phone Number" v-model="valPhone" />
		<p>&nbsp;{{ valPhone }}</p>
		<hr />
		<MSelect :rules="notempty" label="Pick an Option" v-model="valSelect">
			<option value="a">A</option>
			<option value="b">B</option>
			<option value="c">C</option>
		</MSelect>
		<p>&nbsp;{{ valSelect }}</p>
		<hr />
		<MText :rules="notempty" label="Enter Some Text" placeholder="Some Text" v-model="valText" />
		<p>&nbsp;{{ valText }}</p>
		<hr />
		<input type="submit" value="Trigger Submit" />

		<button @click="setvalue">Set</button>
	</Form>
</template>

<script>
import { Form } from "vee-validate";

import MAddress from "./form/MAddress.vue";
import MCheckbox from "./form/MCheckbox.vue";
import MDate from "./form/MDate.vue";
import MFile from "./form/MFile.vue";
import MNumber from "./form/MNumber.vue";
import MPhone from "./form/MPhone.vue";
import MSelect from "./form/MSelect.vue";
import MText from "./form/MText.vue";

export default {
	name: "App",

	components: {
		Form,

		MAddress,
		MCheckbox,
		MDate,
		MFile,
		MNumber,
		MPhone,
		MSelect,
		MText
	},

	data: () => ({
		valAddress: null,
		valCheckbox: null, // null, true, false
		valDate: "",
		valFile: null,
		valNumber: null,
		valPhone: "",
		valSelect: "", // Must be a the empty string or the label doesn't show properly.
		valText: ""
	}),

	methods: {
		notempty(value) {
			if (value === "" || value === null || value === undefined) {
				return "Value is required.";
			}
			return true;
		},
		submit() {
			console.log("ok");
		},
		setvalue() {
			this.valAddress = { line1: "1234 Some Street", line2: "", city: "Someville", state: "Ohio", zip: "12345" };
			this.valCheckbox = true;
			this.valDate = "2020-11-22";
			this.valFile = null; // File inputs cannot have a value set.
			this.valNumber = 1234;
			this.valPhone = 1234567890;
			this.valRadio = true;
			this.valSelect = "b";
			this.valText = "test text";
		}
	}
};
</script>

<style lang="scss">
hr {
	width: 100%;
	border: 5px solid black;
}
</style>
