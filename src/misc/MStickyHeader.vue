<script>
/*
How this works:

The header has a 1px tall bar across the top and is set to be sticky at -1px. If any part of the header is scrolled out
of view an extra CSS class is added.

And that's it. Well, sorta.

See, if the size of the header was to then change based on that class, the page could "vibrate", bouncing up and down
as it meets and then does not meet the requirements for the class.

So, to solve this issue I do something pretty simple: I add a div that is "tall enough" absolutely positioned at the
top of the page. I have *no idea* why this works. All I know is that is the element is large enough it functions
flawlessly and if not you still get vibration. How large is "large enough"? It seems to be related to how large the
header is? The smallest size I found that worked for some header content was 3px. Other headers needed more. But, if
the height is just set to 100% and the div moved way off the left side of the page it doesn't really matter. Too large
works just as well as just right.
*/

export default {
	name: "MStickyHeader",

	data: () => ({
		isstuck: null
	}),

	computed: {
		stuck() {
			return this.isstuck ? "mstickyheader mstickyheader-stuck" : "mstickyheader";
		}
	},

	mounted() {
		const observer = new IntersectionObserver(
			([e]) => {
				this.isstuck = e.intersectionRatio < 1;
			},
			{ threshold: [1] }
		);

		observer.observe(this.$refs.header);
	}
};
</script>

<style scoped lang="scss">
.mstickyheader-magic {
	position: absolute;
	width: 1px;
	left: -10000;
	height: 100%;
}

.mstickyheader {
	position: sticky;
	top: -1px;

	&-guard {
		height: 1px;
		width: 100%;

		background-color: transparent;
	}
}
</style>

<template>
	<div class="mstickyheader-magic"></div>
	<header :class="stuck" ref="header">
		<div class="mstickyheader-guard"></div>
		<slot></slot>
	</header>
</template>
