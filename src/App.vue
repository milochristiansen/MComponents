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
		<MMultiSelect
			label="Select Multiple Somethings"
			v-model="valMultiSelect"
			:options="[
				{ text: 'Option 1', value: '1' },
				{ text: 'Option 2', value: '2' },
				{ text: 'Option 3', value: '3' }
			]"
		/>
		<p>&nbsp;{{ valMultiSelect }}</p>
		<hr />
		<MNumber :rules="notempty" label="Enter a Number" placeholder="123456567890" v-model="valNumber" />
		<p>&nbsp;{{ valNumber }}</p>
		<hr />
		<MPhone :rules="notempty" label="Enter a Phone Number" v-model="valPhone" />
		<p>&nbsp;{{ valPhone }}</p>
		<hr />
		<MRange :rules="notempty" label="Select a Number" v-model="valRange" :list="[2, 4, { k: '50%', v: 5 }, 6, 8]" />
		<p>&nbsp;{{ valRange }}</p>
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
	<hr />
	<MLeafletMap />
	<hr />
	<MPagination :current="6" :end="100" />
	<hr />
	<MSlider :speed="7000" :autoplay="true">
		<div class="slide"><img src="http://placekitten.com/500/250" /></div>
		<div class="slide"><img src="http://placekitten.com/501/250" /></div>
		<div class="slide"><img src="http://placekitten.com/500/251" /></div>
		<div class="slide"><img src="http://placekitten.com/501/251" /></div>
	</MSlider>
	<hr />
	<MTabs>
		<template #labels>
			<span>1</span>
			<span>2</span>
			<span>3</span>
		</template>
		<template #tabs>
			<div>
				<p>Page 1</p>
			</div>
			<div>
				<p>Page 2</p>
			</div>
			<div>
				<p>Page 3</p>
			</div>
		</template>
	</MTabs>
</template>

<script>
import { Form } from "vee-validate";

import { MAddress, MCheckbox, MDate, MFile, MMultiSelect, MNumber, MPhone, MRange, MSelect, MText } from "./form";

import { MLeafletMap, MPagination, MSlider, MTabs } from "./misc";

/*
TODO:

Button? Maybe, maybe not. Buttons are pretty simple to do vanilla. Probably a good idea to at least provide a few styles for UI consistency. Possibly add things like icons or groups that visually run together.

Dropdown, especially for auto-complete entry. An evolution of the existing multi-select?

Tool tips. Nice, big, easy-to-read ones.

A loading spinner. Both as a stand alone sizable element and as a piece to use in other controls.

Image container that shows a spinner while it is loading in instead of showing a partly loaded image.

Path-style breadcrumb links

Step displays (step 1, step 2, you are here kind of stuff, for sign up forms or whatever)

Hideable page banners for user alerts.

At some point we will want ad containers

*/

export default {
	name: "App",

	components: {
		Form,

		MAddress,
		MCheckbox,
		MDate,
		MFile,
		MMultiSelect,
		MNumber,
		MPhone,
		MRange,
		MSelect,
		MText,

		MLeafletMap,
		MPagination,
		MSlider,
		MTabs
	},

	data: () => ({
		valAddress: null,
		valCheckbox: null, // null, true, false
		valDate: "",
		valFile: null,
		valMultiSelect: null,
		valNumber: null,
		valPhone: "",
		valRange: 0,
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
			this.valMultiSelect = ["2"];
			this.valNumber = 1234;
			this.valPhone = 1234567890;
			this.valRange = 5;
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
	border-top: 5px solid black;
	border-bottom: 5px solid black;
	border-left: none;
	border-right: none;
}

.mslider {
	width: 50%;
	margin-left: auto;
	margin-right: auto;
	.slide {
		display: flex;
		img {
			margin-left: auto;
			margin-right: auto;
		}
	}
}
</style>
