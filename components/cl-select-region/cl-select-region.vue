<template>
	<view class="cl-select-region">
		<cl-select
			mode="multiSelector"
			:value="sel"
			:options="list"
			:label-key="labelKey"
			:value-key="valueKey"
			:separator="separator"
			:disabled="disabled"
			@column-change="onColumnChange"
			@change="onChange"
		></cl-select>
	</view>
</template>

<script>
let cities = null;

/**
 * select-region 下拉城市选择
 * @description 下拉城市选择
 * @tutorial https://docs.cool-js.com/uni/components/form/select-region.html
 * @property {null} value 绑定值
 * @property {String} api 城市数据接口，默认https://cool-uni.oss-cn-shanghai.aliyuncs.com/comm/city.json
 * @property {Array} options 城市数据列表
 * @property {String} disabled 是否禁用
 * @property {String} labelKey 内容关键字，默认label
 * @property {String} valueKey 值关键字，默认value
 * @property {String} separator 分隔符，默认 -
 * @example <cl-select-region />
 */

export default {
	name: "cl-select-region",

	props: {
		value: Array,
		api: {
			type: String,
			default: "https://cool-uni.oss-cn-shanghai.aliyuncs.com/comm/city.json"
		},
		options: Array,
		disabled: Boolean,
		labelKey: {
			type: String,
			default: "label"
		},
		valueKey: {
			type: String,
			default: "value"
		},
		separator: {
			type: String,
			default: "-"
		}
	},

	data() {
		return {
			list: [[], [], []],
			sel: []
		};
	},

	watch: {
		value: {
			immediate: true,
			handler(val) {
				// 获取城市数据
				if (cities) {
					this.onUpdate(this.value);
				} else {
					uni.request({
						url: this.api,
						success: res => {
							cities = res.data;
							this.onUpdate(this.value);
						}
					});
				}
			}
		}
	},

	methods: {
		onChange(arr) {
			this.$emit("input", (this.sel = arr));
		},

		onUpdate([x, y, z]) {
			this.sel = [x, y, z];

			let a = 0;
			let b = 0;

			if (!x) {
				a = 0;
				b = 0;
			} else {
				a = cities.findIndex(e => e[this.valueKey] == x);
				b = cities[a].children.findIndex(e => e[this.valueKey] == y);
			}

			this.updateList([a, b]);
		},

		onColumnChange({ selects, column }) {
			this.updateList(selects.map(e => (e < 0 ? 0 : e)));
		},

		updateList([a, b]) {
			this.list = [cities, cities[a].children, cities[a].children[b].children];
		}
	}
};
</script>
