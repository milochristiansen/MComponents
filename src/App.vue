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
	<hr />
	<MPagination :current="6" :end="100" />
	<hr />
	<MSlider :speed="7000" :autoplay="true">
		<div class="slide"><img src="http://placekitten.com/500/250" /></div>
		<div class="slide"><img src="http://placekitten.com/501/250" /></div>
		<div class="slide"><img src="http://placekitten.com/500/251" /></div>
		<div class="slide"><img src="http://placekitten.com/501/251" /></div>
	</MSlider>
</template>

<script>
import { Form } from "vee-validate";

import { MAddress, MCheckbox, MDate, MFile, MNumber, MPhone, MSelect, MText } from "./form";

import { MPagination, MSlider } from "./misc";

/*
TODO:

Button? Maybe, maybe not. Buttons are pretty simple to do vanilla. Probably a good idea to at least provide a few styles for UI consistency. Possibly add things like icons or groups that visually run together.

Dropdown, especially for auto-complete entry.

Pagination controls.

Multiple choice select. I saw a cool one that adds the selections as bubbles with individual remove buttons on the control, in a "tag list" kind-of thing. When you select an option is is removed from the list and turns into a bubble.

Input sliders (input type=range).

Tab and accordion containers

Tool tips. Nice, big, easy-to-read ones.

Checkboxes should probably be visually tweaked a bit. Sink the buttons into the control, square them up a bit,
better default colors, etc. Maybe rethink the way the current box works?

A loading spinner. Both as a stand alone sizable element and as a piece to use in other controls.

A leaflet map control.

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
		MNumber,
		MPhone,
		MSelect,
		MText,

		MPagination,
		MSlider
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
