<template>
	<view style="display: flex;flex-wrap: wrap;padding: 5rpx;">

			<view @click="toDetail(index)" v-for="i,index in typeIndex!=null?imgArrList[typeIndex]:[]" style="width: 33.33%;text-align: center;">
				<view style="margin: 5rpx;border: 1rpx solid lightgray;border-radius: 5rpx;">
					<image style="width: 230rpx;height: 230rpx;" :src="i"></image>
					<view style="color: gray;">{{nameArrList[typeIndex][index]}}</view>
				</view>
			</view>
	

	</view>
</template>

<script>
	export default {
		data() {
			return {
				typeIndex:null,
				nameArrList:[
					[
						'RDO-406 荧光法溶解氧传感器','RDO-206A 荧光法溶解氧传感器','PHG-406 pH传感器',
						'PHG-206A pH传感器','DDM-406 电导率传感器','DDM-206A 电导率传感器',
						'ZS-406 浊度传感器','ZS-206A 浊度传感器','COD-302 COD传感器',
						'NHN-406 铵氮传感器',
						'NHN-206A 铵氮传感器','CL-206A 余氯传感器','ORP-206A ORP传感器',
						'DDM-206A-S 盐度传感器','MLSS-406 污泥浓度传感器','OIL-406 水中油传感器',
						'CHLO-406 叶绿素传感器','BGA-406 蓝绿藻传感器','在线钾离子传感器',
						'在线钙离子传感器',
						'BGA-206A 蓝绿藻传感器','CHLO-206A 叶绿素传感器','CLI-206A 氯离子传感器',
						'FLU-206A 氟离子传感器','NHN-302多参数 铵氮传感器','OIL-206A 水中油传感器',
						'TSS-206A 悬浮物传感器','NON-206A 硝氮传感器','TPS-206 透明度传感器'
					],
					[
						'iCOD-306 COD传感器',
						'iNH-306 铵氮传感器',
						'iZS-306 浊度传感器',
						'iPH-306 PH传感器',
						'iEC-306 电导率传感器',
						'iDO-306 荧光法溶解氧传感器',
						'EXO-mini 1蓝牙无线传输模块（蓝精灵）'
					],
					[
						'iMP-300在线多参数自清洁传感器',
						'MPS-400在线多参数自清洁传感器'
					],
					[
						'TP-300C总磷在线分析仪',
						'TP-300原位总磷分析仪',
						'CLX-300余氯分析仪',
						'TC-300C CODcr在线分析仪',
						'TM-300原位高锰酸盐指数分析仪',
						'TM-300C高锰酸盐指数在线分析仪',
						'TA-300原位氨氮分析仪',
						'TN-300原位总氮分析仪',
						'TN-300C总氮在线分析仪',
						'TA-300C氨氮在线分析仪'
					],
					[
						'多参数水质监测仪',
						'MS-300地下管网水质监测微系统',
						'EGM-200二次供水水质监测微系统',
						'MPC-800小型水质监测与控制站',
						'MS-500微型水质监测站',
						'MF-500水质在线监测浮标'
					],
					[
						'智慧检测信息系统',
						'软件和APP',
						'MPC-202控制器'
					],
					[
						'水下机器人'
					]
					
				],
				imgArrList: [
					[
						'http://oss.chemins-tech.com/uploads/20220125/FhUEVj2DYReVP6QYBz_aoz8NW7eX.png',
						'http://oss.chemins-tech.com/uploads/20220207/FlXONlsOkoaYvsT5LLhvmKCanQ6_.png',
						'http://oss.chemins-tech.com/uploads/20220511/Fq4K0bfcnC7eCpv-2qS3tzxdBE9K.jpg',
						'http://oss.chemins-tech.com/uploads/20220207/FpCRir6Ywt2pQu9gofVZtEOZS2YV.png',
						'http://oss.chemins-tech.com/uploads/20220426/FvEJpqSuGHa8437eS3fx1LWUcccq.png',
						'http://oss.chemins-tech.com/uploads/20220207/FrdiB-qJv_Pz1eTQK0R4yHTUiWzd.png',
						'http://oss.chemins-tech.com/uploads/20220426/FvVOJqRgk0YxrqayBMFLHG4xvts1.png',
						'http://oss.chemins-tech.com/uploads/20220207/FhqpX75QGAbP_KWgMUCPppHDyA17.png',
						'http://oss.chemins-tech.com/uploads/20220207/Fuc5SGFXzrf81rhd3JpO7-ZQx-90.png',
						'http://oss.chemins-tech.com/uploads/20220426/Fnrfj9-nv3PEzCxHPjyJCW1s5hYW.png',
						'http://oss.chemins-tech.com/uploads/20220207/FmxgDBCT8HUbocp151oNFGAHbC7s.png',
						'http://oss.chemins-tech.com/uploads/20220207/Fq3PtBf9tgwFgWgOkTVe5BTX_6il.png',
						'http://oss.chemins-tech.com/uploads/20220207/FtwWW8QAGjMn4O7Yt34Cg7CAAE4d.png',
						'http://oss.chemins-tech.com/uploads/20220207/FtwWW8QAGjMn4O7Yt34Cg7CAAE4d.png',
						'http://oss.chemins-tech.com/uploads/20220426/FvVOJqRgk0YxrqayBMFLHG4xvts1.png',
						'http://oss.chemins-tech.com/uploads/20220823/FvVOJqRgk0YxrqayBMFLHG4xvts1.png',
						'http://oss.chemins-tech.com/uploads/20220426/FvVOJqRgk0YxrqayBMFLHG4xvts1.png',
						'http://oss.chemins-tech.com/uploads/20220426/FvVOJqRgk0YxrqayBMFLHG4xvts1.png',
						'http://oss.chemins-tech.com/uploads/20220222/FmxgDBCT8HUbocp151oNFGAHbC7s.png',
						'http://oss.chemins-tech.com/uploads/20220222/FprtY138z_Yt1DnxZBdS_s2Q8_Vk.png',
						'http://oss.chemins-tech.com/uploads/20220207/Fu7RNzwKx8x-5Nc-5lyDFrXNb8ws.png',
						'http://oss.chemins-tech.com/uploads/20220207/Fmywetal1vEUJvVVDRpriLP_6Xpy.png',
						'http://oss.chemins-tech.com/uploads/20220207/FqaOEdacKOzkBXOEBjuGc_h8SsIN.png',
						'http://oss.chemins-tech.com/uploads/20220207/FoG7dfzGIbD4XST9v2yHNj2j9-rf.png',
						'http://oss.chemins-tech.com/uploads/20220207/FgFU0GOSJAqVSG1aT6h-d6buIN-g.png',
						'http://oss.chemins-tech.com/uploads/20220207/Fu7RNzwKx8x-5Nc-5lyDFrXNb8ws.png',
						'http://oss.chemins-tech.com/uploads/20220207/FvZr7-O57aGW_NHXrb-4hhOIpScF.png',
						'http://oss.chemins-tech.com/uploads/20220207/FrdHcemtydU2vT-3TWy08ciavEQV.png',
						'http://oss.chemins-tech.com/uploads/20220207/FqIif0OrvXTvB0izqxJlXHe9svrJ.png'
					],
					[
						'http://oss.chemins-tech.com/uploads/20220125/FvvHjWjtH15a2ZbcG9_lai89i13y.png',
						'http://oss.chemins-tech.com/uploads/20220805/Fl9zbOuWo9csH4SrY3Ac9uDbMtRV.png',
						'http://oss.chemins-tech.com/uploads/20220805/FsmFRcX80n7GMWuHE1jUUwmnLHoe.png',
						'http://oss.chemins-tech.com/uploads/20220805/FjmD03oS1PRkArCRYav-8RgTJRTM.png',
						'http://oss.chemins-tech.com/uploads/20220801/Fkt-TjRBxz0KwN1XwbFW20JHf14a.png',
						'http://oss.chemins-tech.com/uploads/20220805/FtXU9cO2521n52lOknVhAr8Aq1Yv.png',
						'http://oss.chemins-tech.com/uploads/20220805/FtXU9cO2521n52lOknVhAr8Aq1Yv.png'
					],
					[
						'http://oss.chemins-tech.com/uploads/20220211/Fhj-ZJOuEdpVQQLUr8aF6ei3SuP_.jpg',
						'http://oss.chemins-tech.com/uploads/20220805/FrlJAJXjPCjJTKIqNzpSZyxRnv3X.JPG',
					],
					[
						'http://oss.chemins-tech.com/uploads/20220125/FokBBe35fT2jZw_eeTVBet18BuEy.jpg',
						'http://oss.chemins-tech.com/uploads/20220125/FuiadNZ1QNHN8VXRWAsFhOFScDgU.png',
						'http://oss.chemins-tech.com/uploads/20220125/Fu2CjexSvdd1ilqSQd0PKLDEE-Je.png',
						'http://oss.chemins-tech.com/uploads/20220211/FlwgDQoNMTijyveXmknVV-YrJdBI.png',
						'http://oss.chemins-tech.com/uploads/20220211/Fm4GCf9PlEhf93wjCdLM8olsVwKi.png',
						'http://oss.chemins-tech.com/uploads/20220211/FoUq7LTdZWQ0gEIu8L_PinZt5Bpz.png',
						'http://oss.chemins-tech.com/uploads/20220208/FmySkUxUovcq2r5vbV2xLe1L5iXg.png',
						'http://oss.chemins-tech.com/uploads/20220209/FrLdMbPgMFk1lUM9S8124b5KgEj6.jpg',
						'http://oss.chemins-tech.com/uploads/20220125/FjiI0OeK42zvc3dSOvDY-0fNKYl8.jpg',
						'http://oss.chemins-tech.com/uploads/20220125/FqHvgqt_DzZHlVx70ypc4SRdptZi.jpg'
					],
					[
						'http://oss.chemins-tech.com/uploads/20220512/FstVMwguJxd0VA8JbjRdcitlfiLP.png',
						'http://oss.chemins-tech.com/uploads/20220125/Fne-w04NHnOFDfNYz1amJN3FGLXn.jpg',
						'http://oss.chemins-tech.com/uploads/20230110/FoS_Qqdzwtxiy_FjHUlD01_V_6_D.png',
						'http://oss.chemins-tech.com/uploads/20220125/FuyAxAXiQaV9NGs0jXhU1Ht2FFVT.png',
						'http://oss.chemins-tech.com/uploads/20220125/FqX8GSMqnBiOTsJBDPPduwx5-dR8.jpg',
						'http://oss.chemins-tech.com/uploads/20220721/FqbyWYaD2VaA1bDEdmMwVQD1RsR8.png',
					],
					[
						'http://oss.chemins-tech.com/uploads/20220210/Fv3FBO1g1M6rHcW4SBDmhqDrq7_0.png',
						'http://oss.chemins-tech.com/uploads/20220210/Fk9y0ae4hnppTBAAD6CuW678spCx.png',
						'http://oss.chemins-tech.com/uploads/20220125/FgRPJNn4jZ8zljWjjlfhqGlKZy46.jpg',
					],
					[
						'http://oss.chemins-tech.com/uploads/20220317/FgtkSj_WbPdxGqbQKtMueemOlhDq.png',
					]
					
				]
			}
		},
		methods: {
			toDetail(productIndex){
				console.log(this.typeIndex,productIndex)
				uni.navigateTo({
					url:'./detail/detail?typeId='+this.typeIndex+'&productId='+productIndex
				})
			}
		},
		onLoad(option){
			console.log(option.id)
			this.typeIndex = option.id
			setTimeout(()=>{
				uni.setNavigationBarTitle({
					title:option.title
				})
			},100)

		}
	}
</script>

<style>
/* 	page {
		background-color: #EAEDF2;
	} */
</style>
