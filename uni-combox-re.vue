<template>
	<view class="uni-combox">
		<view v-if="label" class="uni-combox__label" :style="labelStyle">
			<text>{{label}}</text>
		</view>
		<view class="uni-combox__input-box">
			<input v-show="false" type="text" v-model="inputValTrue"/>
			<input class="uni-combox__input" @input="onInput" type="text" :placeholder="placeholder" v-model="inputVal" @focus="onFocus" @blur="onBlur" />
			<uni-icons class="uni-combox__input-arrow" type="arrowdown" size="14" @click="toggleSelector"></uni-icons>
			<view class="uni-combox__selector" v-if="showSelector">
				<scroll-view scroll-y="true" class="uni-combox__selector-scroll">
					<view class="uni-combox__selector-empty" v-if="filterCandidatesLength === 0">
						<text>{{emptyTips}}</text>
					</view>
					<view class="uni-combox__selector-item" v-for="(item,index) in filterCandidates" :key="index" @click="onSelectorClick(item,index)">
						<text v-if="typeof(item) == 'object'">{{item.name}}</text>
						<text v-else>{{item}}</text>
					</view>
				</scroll-view>
			</view>
		</view>
	</view>
</template>

<script>
	import uniIcons from '../uni-icons/uni-icons.vue'
	/**
	 * Combox 组合输入框
	 * @description 组合输入框一般用于既可以输入也可以选择的场景
	 * @tutorial https://ext.dcloud.net.cn/plugin?id=1261
	 * @property {String} label 左侧文字
	 * @property {String} labelWidth 左侧内容宽度
	 * @property {String} placeholder 输入框占位符
	 * @property {Array} candidates 候选项列表
	 * @property {String} emptyTips 筛选结果为空时显示的文字
	 * @property {String} value 组合框的值
	 */
	export default {
		name: 'uniCombox',
		components: {
			uniIcons
		},
		props: {
			label: {
				type: String,
				default: ''
			},
			labelWidth: {
				type: String,
				default: 'auto'
			},
			placeholder: {
				type: String,
				default: ''
			},
			candidates: {
				type: Array,
				default () {
					return []
				}
			},
			emptyTips: {
				type: String,
				default: '无匹配项'
			},
			value: {
				type: [String, Number],
				default: ''
			}
		},
		data() {
			return {
				showSelector: false,
				inputVal: '',
				inputValTrue: ''
			}
		},
		computed: {
			labelStyle() {
				if (this.labelWidth === 'auto') {
					return {}
				}
				return {
					width: this.labelWidth
				}
			},
			filterCandidates() {
				return this.candidates.filter((item) => {
					return item['name'].toString().indexOf(this.inputVal) > -1
				})
			},
			filterCandidatesLength() {
				return this.filterCandidates.length
			}
		},
		watch: {
			value: {
				handler(newVal) {
					console.log(newVal)
					this.inputValTrue = newVal
					this.inputVal = this.candidates.filter((item) => {
						return item['id'] == newVal
					})[0].name
				},
				immediate: true
			}
		},
		methods: {
			toggleSelector() {
				this.showSelector = !this.showSelector
			},
			onFocus() {
				this.showSelector = true
			},
			onBlur() {
				setTimeout(() => {
					this.showSelector = false
				}, 50)
			},
			onSelectorClick(item,index) {
				console.log(index)
				this.inputValTrue = item.id
				this.inputVal = item.name
				this.$emit('input', this.inputValTrue)
				this.showSelector = false
			},
			onInput() {
				setTimeout(() => {
					this.$emit('input', this.inputValTrue)
				})
			}
		}
	}
</script>

<style scoped>
	.uni-combox {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		height: 40px;
		flex-direction: row;
		align-items: center;
		/* border-bottom: solid 1px #DDDDDD; */
	}

	.uni-combox__label {
		font-size: 16px;
		line-height: 22px;
		padding-right: 10px;
		color: #999999;
	}

	.uni-combox__input-box {
		position: relative;
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex: 1;
		flex-direction: row;
		align-items: center;
	}

	.uni-combox__input {
		flex: 1;
		font-size: 16px;
		height: 22px;
		line-height: 22px;
	}

	.uni-combox__input-arrow {
		padding: 10px;
	}

	.uni-combox__selector {
		box-sizing: border-box;
		position: absolute;
		top: 42px;
		left: 0;
		width: 100%;
		background-color: #FFFFFF;
		border-radius: 6px;
		box-shadow: #DDDDDD 4px 4px 8px, #DDDDDD -4px -4px 8px;
		z-index: 2;
	}

	.uni-combox__selector-scroll {
		max-height: 200px;
		box-sizing: border-box;
	}

	.uni-combox__selector::before {
		content: '';
		position: absolute;
		width: 0;
		height: 0;
		border-bottom: solid 6px #FFFFFF;
		border-right: solid 6px transparent;
		border-left: solid 6px transparent;
		left: 50%;
		top: -6px;
		margin-left: -6px;
	}

	.uni-combox__selector-empty,
	.uni-combox__selector-item {
		/* #ifdef APP-NVUE */
		display: flex;
		/* #endif */
		line-height: 36px;
		font-size: 14px;
		text-align: center;
		border-bottom: solid 1px #DDDDDD;
		margin: 0px 10px;
	}

	.uni-combox__selector-empty:last-child,
	.uni-combox__selector-item:last-child {
		border-bottom: none;
	}
</style>