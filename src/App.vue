<template>
	<MStickyHeader>
		<div class="bar">
			<MMobileMenu>
				<p>In real apps a navigation menu goes here.</p>
			</MMobileMenu>
			<h1>Component Testing</h1>
		</div>
		<div class="cat"></div>
	</MStickyHeader>

	<Form :submit="submit">
		<MAddress v-model="valAddress" />
		<p>&nbsp;{{ valAddress }}</p>
		<hr />

		<MCheckbox :rules="notempty" label="Yes or No?" v-model="valCheckbox" />
		<p>&nbsp;{{ valCheckbox }}</p>
		<hr />

		<MCode
			:rules="notempty"
			label="Enter some Markdown"
			placeholder="Example placeholder"
			language="markdown"
			v-model="valCode"
		/>
		<pre>&nbsp;{{ valCode }}</pre>
		<hr />

		<MDate :rules="notempty" label="Enter a Date" v-model="valDate" />
		<p>&nbsp;{{ valDate }}</p>
		<hr />

		<MFile :rules="notempty" label="Upload Something" v-model="valFile" />
		<p>&nbsp;{{ valFile }}</p>
		<hr />

		<MMultiSelect
			:rules="notempty"
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
	<p>
		Imagine this is a long paragraph.<MFootnote>It really isn't, but whatever.</MFootnote> It goes on and on
		forever, unbroken.<MFootnote>No, no it doesn't.</MFootnote> The longest paragraph you have ever seen.
		<MFootnote>Nope.</MFootnote>
	</p>
	<hr />

	<MLeafletMap />
	<hr />

	<MMarkdown :src="valCode" />
	<hr />

	<MPagination :current="6" :end="100" :buttons="4" />
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

import {
	MAddress,
	MCheckbox,
	MCode,
	MDate,
	MFile,
	MMultiSelect,
	MNumber,
	MPhone,
	MRange,
	MSelect,
	MText
} from "./form";

import { MFootnote, MLeafletMap, MMarkdown, MMobileMenu, MPagination, MSlider, MStickyHeader, MTabs } from "./misc";

/*
TODO:

Button? Maybe, maybe not. Buttons are pretty simple to do vanilla. Probably a good idea to at least provide a few styles for UI consistency. Possibly add things like icons or groups that visually run together.

Dropdown, especially for auto-complete entry. An evolution of the existing multi-select?

*/

export default {
	name: "App",

	components: {
		Form,

		MAddress,
		MCheckbox,
		MCode,
		MDate,
		MFile,
		MMultiSelect,
		MNumber,
		MPhone,
		MRange,
		MSelect,
		MText,

		MFootnote,
		MLeafletMap,
		MMarkdown,
		MMobileMenu,
		MPagination,
		MSlider,
		MStickyHeader,
		MTabs
	},

	data: () => ({
		valAddress: null,
		valCheckbox: null, // null, true, false
		valCode: "",
		valDate: "",
		valFile: null,
		valMultiSelect: null,
		valNumber: null,
		valPhone: "",
		valRange: 0,
		valSelect: "", // Must be the empty string or the label doesn't show properly.
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
			this.valCode = `
## Markdown Document

This is some *Markdown* text (shared between the editor and MD renderer components).

You can do all kinds of fun stuff in here, such as lists:

* Look Ma!
* No cumbersome tags!

[Links](https://commonmark.org/) work perfectly, of course.

The renderer component is *not* safe for displaying user input! Output is **NOT** sanitized!
`.trim();
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
html {
	min-height: 100vh;
}
body {
	min-height: 100vh;

	margin: 0 auto;
	float: none;
}
* {
	/* I always set this in my app-wide CSS, so this component library expects it. */
	box-sizing: border-box;
}

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

.mstickyheader .mstickyheader-guard {
	background-color: black;
}
.mstickyheader-content {
	.bar {
		display: flex;
		flex-direction: row;

		background-color: white;

		.mmobilemenu {
			transition-duration: 0.5s;

			margin-top: auto;
			margin-bottom: auto;
		}

		h1 {
			font-size: 3rem;
			background-color: black;
			color: white;

			flex-grow: 1;

			margin-top: 0;
			margin-bottom: 0;

			transition-duration: 0.5s;
		}
	}

	.cat {
		background-image: url(http://placekitten.com/1000/250);
		background-size: cover;
		transition-duration: 0.5s;

		aspect-ratio: 4 / 1;

		margin-right: auto;
		margin-left: auto;
	}

	&-stuck {
		.bar {
			h1 {
				font-size: 1.5rem;
			}

			.mmobilemenu-button {
				font-size: 0.5rem;
			}
		}

		.cat {
			width: 0;
		}
	}
}
</style>
