<script>
	export default {
		onLaunch: function() {
			console.log('App Launch')
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		},
		globalData:{
			baseUrl:"http://114.55.141.197:8088/AndroidItem/Android/",
			user:{}, //存储用户信息
			user:{ //测试数据
				"statusId": 1,
				"UpdataTime": "2020-08-03 14:28:45",
				"roleId": 1,
				"userName": "zhaoyuan",
				"userId": 30052,
				"userPwd": "123456",
				"createTime": "2020-08-03 14:28:45",
				"updataTime": "2020-08-03 14:28:45",
				"uerdepartment": ""
			}, 
			deviceArr:[],
			paramMap:new Map([["cod_v","COD"],["ddm_m_v","电导率m"],["ddm_u_v","电导率μ"],["phg_v","pH"],["orp_v","ORP"],["rdo_v","溶解氧"],["nhn_v","氨氮"],["zs_v","浊度"],
				["ddms_v","盐度"],["cl_v","余氯"],["chlo_v","叶绿素"],["bga_v","蓝绿藻"],["tps_v","透明度"],["tss_v","悬浮物"],["oil_v","水中油"],["col_v","色度"],["mlss_v","污泥浓度"],
				["tp_v","总磷"],["tn_v","总氮"],["non_v","硝氮"],["tmn_v","高锰酸盐"],["cli_v","氯离子"],["flu_v","氟离子"],["cai_v","钙离子"],["ki_v","钾离子"],["cui_v","铜离子"],
				["toc_v","湿法COD"],["tan_v","湿法氨氮"],["clx_v","湿法余氯"],["temp","温度"],["bod_v","BOD"],["tds_v","总溶解固体"],["time","时间"]]),
			paramUnitMap:new Map([["cod_v","mg/L"],["ddm_m_v","mS/cm"],["ddm_u_v","μS/cm"],["phg_v",""],["orp_v","mV"],["rdo_v","mg/L"],["nhn_v","mg/L"],["zs_v","NTU"],
				["ddms_v","PSU"],["cl_v","mg/L"],["chlo_v","μg/L"],["bga_v","Kcells/mL"],["tps_v","mm"],["tss_v","mg/L"],["oil_v","mg/L"],["col_v","Hazen"],["mlss_v","g/L"],
				["tp_v","mg/L"],["tn_v","mg/L"],["non_v","mg/L"],["tmn_v","mg/L"],["cli_v","mg/L"],["flu_v","mg/L"],["cai_v","mg/L"],["ki_v","mg/L"],["cui_v","mg/L"],
				["toc_v","mg/L"],["tan_v","mg/L"],["clx_v","mg/L"],["temp","℃"],["bod_v","mg/L"],["tds_v","mg/L"]]),
		}
	}
</script>

<style>
	/*每个页面公共css */
	.shadow{
		box-shadow: 0 1px 2px 2px rgba(0, 0, 0, 0.1);
		border-radius: 10rpx;padding: 20rpx;
	}
	.select_view{
		display: flex;align-items: center;margin-bottom: 20rpx;
	}
	.select_title{
		color: gray;margin-right: 20rpx;
	}
	.select_picker{
		background-color: #eee;padding: 10rpx;border-radius: 10rpx
	}
</style>
