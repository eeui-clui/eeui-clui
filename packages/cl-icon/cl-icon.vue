<template>
	<block>
		<text
			v-if="loadRemote"
			class="icon-font"
			:style="mergeStyle"
		>{{ iconUnicode }}</text>
		<text
			v-else-if="!loadRemote && isAndoird"
			class="icon-font"
			:style="mergeStyle"
		>{{ iconUnicode }}</text>
		<cl-icon-ios
			v-else
			class="icon-font"
			:content="iconUnicode"
			:fontSize="fontSize"
			:color="iconStyle.color ? iconStyle.color : color"
			:fontFamily="fontFamily"
			:fontSrc="fontSrc"
			:style="{ width: size, height: size, fa }"
		></cl-icon-ios>
	</block>
</template>

<script>
const dom = weex.requireModule("dom");
let platform = weex.config.env.platform.toLowerCase();
const stream = weex.requireModule("stream") || {};
const storage = weex.requireModule("storage");
export default {
	name:"cl-icon",
	props: {
		name: {
			default: "success",
			type: String,
		},
		size: {
			default: "36px",
			type: String,
		},
		color: {
			default: "#666",
		},
		iconStyle: {
			type: Object,
			default: () => ({}),
		},
		fontSrc: {
			type: String,
			default: "local:///eeui/font/iconfont.ttf", //对应编译到的原生项目目录
		},
		fontFamily: {
			type: String,
			default: "iconfont",
		},
		iconfontJson: {
			type: Object,
			default: () => ({}),
		},
		iconMap: {
			type: Object,
			default: () => ({}),
		},
	},
	computed: {
		fontSize() {
			var size = this.iconStyle["font-size"]
				? this.iconStyle["font-size"]
				: parseInt(this.size).toString();
			if (size.indexOf("px") == -1) {
				size += "px";
			}
			return size;
		},
		iconUnicode() {
			let icon = this.iconMap[this.name] ? this.iconMap[this.name] : "";
			return icon;
		},
		mergeStyle() {
			const { iconStyle, size, color, fontFamily } = this;
			let fontSize = size;
			return {
				fontSize,
				color,
				fontFamily,
				...iconStyle,
			};
		},
		loadRemote() {
			return this.fontSrc.indexOf("http") != -1 ? true : false;
		},
		isAndoird() {
			return platform == "android";
		},
	},
	created() {
		if (this.fontSrc.indexOf("src/") == 0) {
			this.fontSrc = this.fontSrc.replace("src/", "local:///eeui/");
		}
		dom.addRule("fontFace", {
			fontFamily: this.fontFamily,
			src: "url('" + this.fontSrc + "')",
		});
	},
	mounted() {
		this.getIconMap();
	},
	methods: {
		getIconMap() {
			storage.getItem(this.fontFamily + "Map", (event) => {
				var iconMap = {};
				if (event.result == "success") {
					iconMap = JSON.parse(event.data);
					this.iconMap = iconMap;
				} else {
					if (this.loadRemote) {
						var url = this.fontSrc.replace(".ttf", ".json");
						stream.fetch(
							{
								method: "GET",
								url: url,
								type: "json",
							},
							(ret) => {
								if (ret.ok) {
									const iconfont = ret.data;
									for (
										var i = 0;
										i < iconfont.glyphs.length;
										i++
									) {
										iconMap[
											iconfont.glyphs[i].font_class
										] = eval(
											"'" +
												"\\u" +
												iconfont.glyphs[i].unicode +
												"'"
										);
									}
									this.iconMap = iconMap;
									storage.setItem(
										this.fontFamily + "Map",
										JSON.stringify(iconMap),
										(event) => {}
									);
								}
							}
						);
					} else {
						const iconfont = this.iconfontJson;
						for (var i = 0; i < iconfont.glyphs.length; i++) {
							iconMap[iconfont.glyphs[i].font_class] = eval(
								"'" + "\\u" + iconfont.glyphs[i].unicode + "'"
							);
						}
						this.iconMap = iconMap;
						storage.setItem(
							this.fontFamily + "Map",
							JSON.stringify(iconMap),
							(event) => {}
						);
					}
				}
			});
		},
	},
};
</script>

<style scoped>
.icon-font {
	color: #666;
}
</style>
