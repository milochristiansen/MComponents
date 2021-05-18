<script>
import "leaflet/dist/leaflet.css";
import L from "leaflet";

import "leaflet-gesture-handling/dist/leaflet-gesture-handling.css";
import { GestureHandling } from "leaflet-gesture-handling";

import { nanoid } from "nanoid";

L.Map.addInitHook("addHandler", "gestureHandling", GestureHandling);

// Needed for the disabled geocoder functionality.
// function stringTemplateParser(expression, valueObj) {
// 	const templateMatcher = /{{\s?([^{}\s]*)\s?}}/g;
// 	let text = expression.replace(templateMatcher, (substring, value, index) => {
// 		value = valueObj[value];
// 		return value;
// 	});
// 	return text;
// }

export default {
	props: {
		overlay: {
			type: Object,
			default: function() {
				return null;
			}
		},
		overlayStyle: {
			type: Object,
			default: function() {
				return {
					fillColor: "#555555",
					fillOpacity: 0.5,
					weight: 0
				};
			}
		},
		maxbounds: {
			type: Array,
			default: function() {
				return [
					[42.4 + 1, -84.97 - 1],
					[38.2 - 1, -80.4 + 1]
				];
			}
		},
		center: {
			type: Array,
			default: function() {
				return [41.0, -81.2];
			}
		},
		zoom: {
			type: Number,
			default: 9
		},
		pinurl: {
			type: String,
			default:
				"data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABkAAAApCAYAAADAk4LOAAAFgUlEQVR4Aa1XA5BjWRTN2oW17d3YaZtr2962HUzbDNpjszW24mRt28p47v7zq/bXZtrp/lWnXr337j3nPCe85NcypgSFdugCpW5YoDAMRaIMqRi6aKq5E3YqDQO3qAwjVWrD8Ncq/RBpykd8oZUb/kaJutow8r1aP9II0WmLKLIsJyv1w/kqw9Ch2MYdB++12Onxee/QMwvf4/Dk/Lfp/i4nxTXtOoQ4pW5Aj7wpici1A9erdAN2OH64x8OSP9j3Ft3b7aWkTg/Fm91siTra0f9on5sQr9INejH6CUUUpavjFNq1B+Oadhxmnfa8RfEmN8VNAsQhPqF55xHkMzz3jSmChWU6f7/XZKNH+9+hBLOHYozuKQPxyMPUKkrX/K0uWnfFaJGS1QPRtZsOPtr3NsW0uyh6NNCOkU3Yz+bXbT3I8G3xE5EXLXtCXbbqwCO9zPQYPRTZ5vIDXD7U+w7rFDEoUUf7ibHIR4y6bLVPXrz8JVZEql13trxwue/uDivd3fkWRbS6/IA2bID4uk0UpF1N8qLlbBlXs4Ee7HLTfV1j54APvODnSfOWBqtKVvjgLKzF5YdEk5ewRkGlK0i33Eofffc7HT56jD7/6U+qH3Cx7SBLNntH5YIPvODnyfIXZYRVDPqgHtLs5ABHD3YzLuespb7t79FY34DjMwrVrcTuwlT55YMPvOBnRrJ4VXTdNnYug5ucHLBjEpt30701A3Ts+HEa73u6dT3FNWwflY86eMHPk+Yu+i6pzUpRrW7SNDg5JHR4KapmM5Wv2E8Tfcb1HoqqHMHU+uWDD7zg54mz5/2BSnizi9T1Dg4QQXLToGNCkb6tb1NU+QAlGr1++eADrzhn/u8Q2YZhQVlZ5+CAOtqfbhmaUCS1ezNFVm2imDbPmPng5wmz+gwh+oHDce0eUtQ6OGDIyR0uUhUsoO3vfDmmgOezH0mZN59x7MBi++WDL1g/eEiU3avlidO671bkLfwbw5XV2P8Pzo0ydy4t2/0eu33xYSOMOD8hTf4CrBtGMSoXfPLchX+J0ruSePw3LZeK0juPJbYzrhkH0io7B3k164hiGvawhOKMLkrQLyVpZg8rHFW7E2uHOL888IBPlNZ1FPzstSJM694fWr6RwpvcJK60+0HCILTBzZLFNdtAzJaohze60T8qBzyh5ZuOg5e7uwQppofEmf2++DYvmySqGBuKaicF1blQjhuHdvCIMvp8whTTfZzI7RldpwtSzL+F1+wkdZ2TBOW2gIF88PBTzD/gpeREAMEbxnJcaJHNHrpzji0gQCS6hdkEeYt9DF/2qPcEC8RM28Hwmr3sdNyht00byAut2k3gufWNtgtOEOFGUwcXWNDbdNbpgBGxEvKkOQsxivJx33iow0Vw5S6SVTrpVq11ysA2Rp7gTfPfktc6zhtXBBC+adRLshf6sG2RfHPZ5EAc4sVZ83yCN00Fk/4kggu40ZTvIEm5g24qtU4KjBrx/BTTH8ifVASAG7gKrnWxJDcU7x8X6Ecczhm3o6YicvsLXWfh3Ch1W0k8x0nXF+0fFxgt4phz8QvypiwCCFKMqXCnqXExjq10beH+UUA7+nG6mdG/Pu0f3LgFcGrl2s0kNNjpmoJ9o4B29CMO8dMT4Q5ox8uitF6fqsrJOr8qnwNbRzv6hSnG5wP+64C7h9lp30hKNtKdWjtdkbuPA19nJ7Tz3zR/ibgARbhb4AlhavcBebmTHcFl2fvYEnW0ox9xMxKBS8btJ+KiEbq9zA4RthQXDhPa0T9TEe69gWupwc6uBUphquXgf+/FrIjweHQS4/pduMe5ERUMHUd9xv8ZR98CxkS4F2n3EUrUZ10EYNw7BWm9x1GiPssi3GgiGRDKWRYZfXlON+dfNbM+GgIwYdwAAAAASUVORK5CYII="
		},
		tileserver: {
			type: String,
			default: "http://tiles.raswith.com/tile/{z}/{x}/{y}.png"
		},
		attribution: {
			type: String,
			default:
				'Map data (c) <a href="https://www.openstreetmap.org/">OpenStreetMap</a> contributors, <a href="https://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>'
		}
	},

	setup() {
		let id = nanoid(5);
		return {
			id,
			mapDiv: null,
			marker: null
		};
	},
	created() {
		this.marker = new L.Icon({
			iconUrl: this.pinurl,
			iconSize: [25, 41],
			iconAnchor: [13, 40]
		});
	},

	data() {
		return {
			lat: 0,
			lon: 0,

			cpin: null,
			lastsearch: null
		};
	},

	computed: {
		filtered() {
			return this.acdata.filter(item => item != undefined);
		},

		coords() {
			if (this.lat == 0 || this.lon == 0) {
				return null;
			}
			return [this.lon, this.lat];
		}
	},

	methods: {
		// pinAddr(street, state, city, zip) {
		// 	const now = new Date();
		// 	if (now - this.lastsearch < 512) {
		// 		return;
		// 	}
		// 	this.lastsearch = now;

		// 	fetch(
		// 		stringTemplateParser("<geocoder URL>", {
		// 			street: encodeURIComponent(street),
		// 			state: encodeURIComponent(state),
		// 			city: encodeURIComponent(city),
		// 			zip: encodeURIComponent(zip)
		// 		})
		// 	)
		// 		.then(response => response.json())
		// 		.then(data => {
		// 			this.lat = data.features[0].geometry.coordinates[0];
		// 			this.lon = data.features[0].geometry.coordinates[1];
		// 			this.showSpot();
		// 		});
		// },

		setupLeafletMap() {
			this.mapDiv = L.map(this.id, {
				center: this.center,
				maxBounds: this.maxbounds,
				zoom: this.zoom,
				gestureHandling: true
			});

			L.tileLayer(this.tileserver, {
				attribution: this.attribution,
				maxZoom: 18
			}).addTo(this.mapDiv);

			if (this.overlay != null) {
				L.geoJSON(this.overlay, {
					style: this.overlayStyle
				}).addTo(this.mapDiv);
			}
		},

		showSpot() {
			if (this.coords == null) {
				return;
			}

			if (this.cpin != null) {
				this.cpin.remove();
				this.cpin = null;
			}

			this.cpin = L.marker(this.coords, { icon: this.marker });
			this.cpin.addTo(this.mapDiv);
		}
	},

	mounted() {
		this.setupLeafletMap();
	}
};
</script>

<style scoped lang="scss">
.mleafletmap {
	height: 500px;
}
</style>

<template>
	<div :id="id" class="mleafletmap"></div>
</template>
