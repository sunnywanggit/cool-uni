<template>
	<view
		class="cl-radio"
		:class="[
			{
				'is-border': isBorder,
				'is-disabled': isDisabled,
				'is-checked': checked
			}
		]"
		@tap="change"
	>
		<view class="cl-radio__input">
			<text class="cl-icon-toast-success" v-show="checked"></text>
		</view>

		<text class="cl-radio__label">
			<slot></slot>
		</text>
	</view>
</template>

<script>
import Emitter from "../../mixins/emitter";
import { getParent } from "../../utils";

/**
 * radio 单选框
 * @description 单选框
 * @tutorial https://docs.cool-js.com/uni/components/form/radio.html
 * @property {String, Number} value 绑定值
 * @property {String, Number} label 标识
 * @property {Boolean} disabled 是否禁用
 * @property {Boolean} border 是否边框样式
 * @event {Function} change 绑定值改变时触发
 * @example <cl-radio label="1"></cl-radio>
 */

export default {
	name: "cl-radio",

	componentName: "ClRadio",

	props: {
		// 绑定值
		value: [String, Number],
		// 标识
		label: [String, Number],
		// 是否禁用
		disabled: Boolean,
		// 是否边框样式
		border: Boolean
	},

	mixins: [Emitter],

	data() {
		return {
			checked: false
		};
	},

	watch: {
		value: {
			immediate: true,
			handler(val) {
				this.checked = val === this.label;
			}
		}
	},

	computed: {
		isGroup() {
			return Boolean(this.parent);
		},

		isBorder() {
			return this.isGroup ? this.parent.border || this.border : this.border;
		},

		isDisabled() {
			return this.isGroup ? this.parent.disabled || this.disabled : this.disabled;
		},

		parent() {
			return getParent.call(this, "ClRadioGroup", ["border", "disabled"]);
		}
	},

	created() {
		// 监听单选框组值的变化
		this.$on("radio-group.change", label => {
			this.checked = label === this.label;
		});
	},

	methods: {
		change() {
			if (this.isDisabled) {
				return false;
			}

			this.checked = true;

			// 回调到组件或者单选框组
			if (this.isGroup) {
				this.dispatch("ClRadioGroup", "radio.change", this.label);
			} else {
				this.$emit("input", this.label);
				this.$emit("change", this.label);
			}
		}
	}
};
</script>
