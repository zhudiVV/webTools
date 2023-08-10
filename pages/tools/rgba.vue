<template>
	<view style="padding: 20upx;">
		<view>
			<view style="display: flex;align-items: center;">
				<u--input
				    placeholder="情输入"
				    border="surround"
				    v-model="orValue"
				  ></u--input>
					<view style="margin-left: 20upx;">
						<u-button type="primary" text="转换" @click="goTran()"></u-button>
					</view>
			</view>
			<view style="display: flex;align-items: center;gap: 6upx;flex-wrap: wrap;margin-top: 8upx;">
				<span>输入示例：</span>
				<u-tag text="#ee9922" bgColor="#ee9922" borderColor="#ee9922"></u-tag>
				<u-tag text="255, 100, 97" bgColor="rgb(255, 100, 97)" borderColor="rgb(255, 100, 97)"></u-tag>
				<u-tag text="255, 100, 97, .5" bgColor="rgba(255, 100, 97, .5)" borderColor="rgba(255, 100, 97, .5)"></u-tag>
				<u-tag text="#80ee9922" bgColor="#80ee9922" borderColor="#80ee9922" color="#000"></u-tag>
			</view>
		</view>
		<view style="width: 400upx;height: 100upx;margin: 20upx 0;" :style="bgStyle">
			
		</view>
		<view>
			<view class="perItem">
				<span style="width: 30upx;">r</span>
				<u-number-box
					v-model="vr"
					:min="0" :max="255"
					integer
				></u-number-box>
			</view>
			<view class="perItem">
				<span style="width: 30upx;">g</span>
				<u-number-box
					v-model="vg"
					:min="0" :max="255"
					integer
				></u-number-box>
			</view>
			<view class="perItem">
				<span style="width: 30upx;">b</span>
				<u-number-box
					v-model="vb"
					:min="0" :max="255"
					integer
				></u-number-box>
			</view>
			<view v-if="hexModel" class="perItem">
				<span style="width: 30upx;">a</span>
				<u-number-box
					v-model="va"
					step="0.01" decimal-length="2"
					:min="0" :max="1"
				></u-number-box>
			</view>
		</view>
		
		<view style="display: flex;align-items: center;padding: 20upx 0;">
			<span style="width: 120upx;">16进制</span>
			<u--input
			    placeholder="16进制"
			    border="surround"
			    v-model="hexValue"
					disabled=""
			  ></u--input>
		</view>
		<view v-if="!hexModel" style="display: flex;align-items: center;padding: 20upx 0;">
			<span style="width: 120upx;">rgb</span>
			<u--input
			    placeholder="rgb"
			    border="surround"
			    v-model="rgbValueStr"
					disabled=""
			  ></u--input>
		</view>
		<view v-if="hexModel" style="display: flex;align-items: center;padding: 20upx 0;">
			<span style="width: 120upx;">rgba</span>
			<u--input
			    placeholder="rgba"
			    border="surround"
			    v-model="rgbaValueStr"
					disabled=""
			  ></u--input>
		</view>
		
		<u-toast ref="uToast"></u-toast>
	</view>
</template>

<script>
	export default{
		data() {
			return {
				vr: 1,
				vg: 0,
				vb: 0,
				va: 0,
				hexValue: '',
				orValue: '',
				hexModel: false,
				rgbValue: [],
				rgbaValue: [],
				rgbValueStr: '',
				rgbaValueStr: ''
			}
		},
		
		computed: {
			bgStyle() {
				return 'background: ' + this.hexValue + ';'
			}
		},
		watch: {
			rgbValue(val) {
					this.vr = val[0]
					this.vg = val[1]
					this.vb = val[2]
					this.rgbValueStr = val.join(',')
			},
			rgbaValue(val) {
					this.vr = val[0]
					this.vg = val[1]
					this.vb = val[2]
					this.va = val[3]
					this.rgbaValueStr = val.join(',')
			},
				// vr(val) {
				// 	this.hexValue = this.rgbaToHex(val, this.vg, this.vb, this.va)
				// },
				// vg(val) {
				// 	this.hexValue = this.rgbaToHex(this.vr, val, this.vb, this.va)
				// },
				// vb(val) {
				// 	this.hexValue = this.rgbaToHex(this.vr, this.vg, val, this.va)
				// },
				// va(val) {
				// 	this.hexValue = this.rgbaToHex(this.vr, this.vg, this.vb, val)
				// }
		},
		
		mounted() {
			this.hexValue = this.rgbaToHex(this.vr, this.vg, this.vb, this.va)
		},
		methods: {
			goTran() {
				console.log('------goTran---')
				// console.dir(this.orValue.indexOf('#'))
				if(this.orValue.indexOf('#') === 0) {
					console.log(this.orValue.length)
					if (Number(this.orValue.length) !== 7 && Number(this.orValue.length) !== 9) {
						this.showError()
						return
					} else {
						if(!this.isHex(this.orValue.substr(1))) {
							this.showError()
							return 
						}
						
						this.hexModel = Number(this.orValue.length) === 9
						
						this.hexValue = this.orValue
						if(this.orValue.length === 7) {			
							this.rgbValue = this.hexToRGB(this.orValue.substr(1))
						}
						if(this.orValue.length === 9) {
							this.rgbaValue = this.hexToRGBA(this.orValue.substr(1))
						}
					}
				} else {
					let separator = /,|，/

					let tmp = this.orValue.split(separator).map(item => item.trim())
					if(!this.funcValue(tmp)) { 
						this.showError()
						return 
					}
					
					this.hexModel = tmp.length === 4
					
					if(tmp.length === 3) {
						this.rgbValue = tmp
						this.hexValue = this.rgbToHex(tmp[0], tmp[1], tmp[2])
						// this.rgbValue = '(' + tmp.join(',') +')'
					}
					if(tmp.length === 4) {
						this.rgbaValue = tmp
						this.hexValue = this.rgbaToHex(tmp[0], tmp[1], tmp[2], tmp[3])
						// this.rgbaValue = '(' + tmp.join(',') +')'
					}
				}
			},
			valChange(e) {
				console.log('当前值为: ' + e.value)
			},
			
			funcValue(arr) {
				// 成功返回1 否则返回0
					if(arr.length !== 3 && arr.length !== 4) {
						return 0
					} else {
						if(arr.length ===4 && !arr[3]) {
							// 12,23,34,
							return 0
						}
						if(arr[3] && (arr[3] > 1 || arr[3] < 0)) {
							return 0
						}
						for(let i = 0; i < 3; i++) {
							if( Number(arr[i]) > 255 || Number(arr[i]) < 0) {
								return 0
							}
						}
					}
					
					return 1
			},
			isHex(val) { //判断是否16进制字符，是返回1，否返回0
				
				for(let i = 0; i < val.length; i++) {
					if(!this.hex(val[i])) {
						return 0
					}
				}
				return 1
			},
			hex(ch){ //判断字符ch是否16进制字符，是返回1，否返回0
			    if ( ch >='0' && ch <='9' ) //属于0-9集合，返回是
			        return 1;
			    if ( ch >='A' && ch <='F' ) //属于A-F集合，返回是
			        return 1;
			    if ( ch >='a' && ch <='f' ) //属于a-f集合，返回是
			        return 1;
			    return 0; //否则，返回不是
			},
			showError() {
				this.$refs.uToast.show({
					type: 'error',
					icon: false,
					message: "输入有误"
				})
			},
			
			rgbToHex(r, g, b) {
			            // 使用位运算符将三个 8 位的数值组合为一个 24 位的数值
			            // const combinedValue = (r << 16) + (g << 8) + b;
									let hexStr = (r << 16 | g << 8 | b).toString(16)
			            if(hexStr.length < 6) {
										hexStr = `0${new Array((6 - hexStr.length)).join("0")}${hexStr}`
									}
									return `#${hexStr}`
			
			},
			
			rgbaToHex(r, g, b, a) {
				r = Math.round(r)
				g = Math.round(g)
				b = Math.round(b)
				if(a) {
					a = Math.round(a * 255)
					return "#" + ((1 << 24) + (r << 16) + (g << 8) + b).toString(16).slice(1) + a.toString(16).toUpperCase().padStart(2, '0')
				} else {
					return "#" + ((1 << 24) + (r << 16) + (g << 8) + b).toString(16).slice(1)
				}
				
				
			},
			
			hexToRGB(hex) {
				var r = parseInt(hex.substring(0, 2), 16);
				  var g = parseInt(hex.substring(2, 4), 16);
				  var b = parseInt(hex.substring(4, 6), 16);
				
				  return [r, g, b];
				// // 16进制颜色值的正则
				//         var reg = /^#([0-9a-fA-f]{3}|[0-9a-fA-f]{6})$/;
				//         // 把颜色值变成小写
				//         var color = val.toLowerCase();
				//         var result = '';
				//         if (reg.test(color)) {
				//             // 如果只有三位的值，需变成六位，如：#fff => #ffffff
				//             if (color.length === 4) {
				//                 var colorNew = "#";
				//                 for (var i = 1; i < 4; i += 1) {
				//                     colorNew += color.slice(i, i + 1).concat(color.slice(i, i + 1));
				//                 }
				//                 color = colorNew;
				//             }
				//             // 处理六位的颜色值，转为RGB
				//             var colorChange = [];
				//             for (var i = 1; i < 7; i += 2) {
				//                 colorChange.push(parseInt("0x" + color.slice(i, i + 2)));
				//             }
				//             result = "rgb(" + colorChange.join(",") + ")";
				//             return { rgb: result, r: colorChange[0], g: colorChange[1], b: colorChange[2] };
				//         } else {
				//             result = '无效';
				//             return { rgb: result };
				//         }
			},
			hexToRGBA(hex) {
				let rgba = [];
				        hex = hex.replace('#', '').padEnd(8, 'F');
				        for (let i = 0; i < 6; i+=2) {
				            rgba.push(parseInt(hex.slice(i, i+2), 16))
				        }

								rgba.push(Number('0x' + hex.slice(6, 8)) / 255)
				        return rgba;
			}
		}
	}
</script>

<style lang="scss" scoped>
	.perItem{
		display: flex;
		align-items: center;
		padding: 10upx 0;
	}
</style>