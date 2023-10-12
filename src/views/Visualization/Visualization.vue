<script setup lang="ts">
import axios from 'axios'
import { Viewer } from 'cesium'
import * as Cesium from 'cesium'
import { onMounted } from 'vue'
import coordtransform from 'coordtransform'
const hospitalData = [
  {
    序号: 1,
    机构名称: '北京艾丽斯妇科医院',
    地址: '北京市海淀区北四环西路9号—1',
    邮政编码: '100190',
    区县代码: '110108',
    区县名称: '海淀区',
    医院等级: '一级',
    医院等次: '未评',
    大类: 'A.医院',
    中类: 'A5.专科医院',
    小类: 'A518.妇产（科）医院',
    经济类型名称: '其他',
    机构管理名称: '营利性医疗机构'
  },
  {
    序号: 2,
    机构名称: '北京爱尔英智眼科医院',
    地址: '北京市朝阳区潘家园南里12号潘家园大厦',
    邮政编码: '100021',
    区县代码: '110105',
    区县名称: '朝阳区',
    医院等级: '一级',
    医院等次: '未评',
    大类: 'A.医院',
    中类: 'A5.专科医院',
    小类: 'A512.眼科医院',
    经济类型名称: '其它内资',
    机构管理名称: '营利性医疗机构'
  },
  {
    序号: 3,
    机构名称: '北京爱育华妇儿医院',
    地址: '北京经济技术开发区景园南街2号',
    邮政编码: '100176',
    区县代码: '110115',
    区县名称: '大兴区',
    医院等级: '三级',
    医院等次: '未评',
    大类: 'A.医院',
    中类: 'A5.专科医院',
    小类: 'A539.其他专科医院',
    经济类型名称: '其他',
    机构管理名称: '营利性医疗机构'
  },
  {
    序号: 4,
    机构名称: '北京安达医院',
    地址: '北京市昌平区回龙观龙腾苑三区16号楼',
    邮政编码: '102208',
    区县代码: '110114',
    区县名称: '昌平区',
    医院等级: '一级',
    医院等次: '合格',
    大类: 'A.医院',
    中类: 'A1.综合医院',
    小类: 'A100.综合医院',
    经济类型名称: '私有',
    机构管理名称: '非营利性医疗机构'
  },
  {
    序号: 5,
    机构名称: '北京安德中医医院',
    地址: '北京市东城区安德路甲10号4楼101－2',
    邮政编码: '100011',
    区县代码: '110101',
    区县名称: '东城区',
    医院等级: '一级',
    医院等次: '合格',
    大类: 'A.医院',
    中类: 'A2.中医医院',
    小类: 'A210.中医（综合）医院',
    经济类型名称: '其它内资',
    机构管理名称: '营利性医疗机构'
  },
  {
    序号: 6,
    机构名称: '北京安定门中医医院',
    地址: '北京市东城区安定门内大街47号',
    邮政编码: '100011',
    区县代码: '110101',
    区县名称: '东城区',
    医院等级: '一级',
    医院等次: '合格',
    大类: 'A.医院',
    中类: 'A2.中医医院',
    小类: 'A210.中医（综合）医院',
    经济类型名称: '股份有限（公司）',
    机构管理名称: '营利性医疗机构'
  },
  {
    序号: 7,
    机构名称: '北京安娜贝儿妇产医院',
    地址: '北京市通州区工业开发区光华路15号',
    邮政编码: '101113',
    区县代码: '110112',
    区县名称: '通州区',
    医院等级: '二级',
    医院等次: '未评',
    大类: 'A.医院',
    中类: 'A5.专科医院',
    小类: 'A518.妇产（科）医院',
    经济类型名称: '其他',
    机构管理名称: '营利性医疗机构'
  },
  {
    序号: 8,
    机构名称: '北京安琪妇产医院',
    地址: '北京市通州区云景南大街104号',
    邮政编码: '101101',
    区县代码: '110112',
    区县名称: '通州区',
    医院等级: '二级',
    医院等次: '合格',
    大类: 'A.医院',
    中类: 'A5.专科医院',
    小类: 'A518.妇产（科）医院',
    经济类型名称: '其他',
    机构管理名称: '营利性医疗机构'
  },
  {
    序号: 9,
    机构名称: '北京按摩医院',
    地址: '西城区宝产胡同7号',
    邮政编码: '100035',
    区县代码: '110102',
    区县名称: '西城区',
    医院等级: '二级',
    医院等次: '甲等',
    大类: 'A.医院',
    中类: 'A2.中医医院',
    小类: 'A224.按摩医院',
    经济类型名称: '国有全资',
    机构管理名称: '非营利性医疗机构'
  },
  {
    序号: 10,
    机构名称: '北京奥北中医医院',
    地址: '北京市朝阳区大屯路36号',
    邮政编码: '100101',
    区县代码: '110105',
    区县名称: '朝阳区',
    医院等级: '一级',
    医院等次: '未评',
    大类: 'A.医院',
    中类: 'A2.中医医院',
    小类: 'A210.中医（综合）医院',
    经济类型名称: '其它内资',
    机构管理名称: '营利性医疗机构'
  }
]
onMounted(async () => {
  const viewer = new Viewer('cesiumContainer', {
    // 隐藏导航控制按钮
    navigationHelpButton: false,
    // 隐藏全屏按钮
    fullscreenButton: false,
    // 隐藏回到初始视图按钮
    homeButton: false,
    // 隐藏图层选择控件
    baseLayerPicker: false,
    // 隐藏地名查找控件
    geocoder: false,
    // 隐藏时间线控件
    timeline: false,
    // 隐藏动画控件
    animation: false,
    // 隐藏场景模式选择控件
    sceneModePicker: false
  })
  // 隐藏Logo
  const cesiumWidgetElement = viewer.cesiumWidget.creditContainer as HTMLElement
  cesiumWidgetElement.style.display = 'none'

  // 地区定位到北京 放大
  viewer.camera.flyTo({
    destination: Cesium.Cartesian3.fromDegrees(116.3, 39.9, 100000.0)
  })

  // 获取场景对象
  let scene = viewer.scene

  // 创建 OpenStreetMap 图像图层提供者
  let openStreetMapProvider = new Cesium.OpenStreetMapImageryProvider({
    url: 'https://a.tile.openstreetmap.org/'
  })

  // 移除默认的图像图层
  scene.imageryLayers.removeAll()

  // 添加 OpenStreetMap 图像图层
  scene.imageryLayers.addImageryProvider(openStreetMapProvider)
  // 设置imageryProviders为openstreetmap
  const params = {
    key: '8370e5cd24894725cb8bc7c519b7a5db',
    address: '',
    city: '北京市',
    output: 'JSON'
  }
  // forEach循环遍历医院数据，根据 ‘地址’ 获取医院的经纬度
  hospitalData.forEach(async (item) => {
    params.address = item.地址
    const res = await axios.get('https://restapi.amap.com/v3/geocode/geo?', { params })
    // cesium 在地球上添加这个经纬度的点
    // 坐标转换
    const gps = coordtransform.gcj02towgs84(
      res.data.geocodes[0].location.split(',')[0],
      res.data.geocodes[0].location.split(',')[1]
    )
    viewer.entities.add({
      position: Cesium.Cartesian3.fromDegrees(Number(gps[0]), Number(gps[1])),
      point: {
        pixelSize: 10,
        color: Cesium.Color.RED,
        outlineColor: Cesium.Color.WHITE,
        outlineWidth: 2
      },
      label: {
        text: item.机构名称,
        font: '14pt monospace',
        style: Cesium.LabelStyle.FILL_AND_OUTLINE,
        outlineWidth: 2,
        verticalOrigin: Cesium.VerticalOrigin.BOTTOM,
        pixelOffset: new Cesium.Cartesian2(0, -9)
      }
    })
    console.log(res.data.geocodes[0].location)
  })
})
</script>

<template>
  <div id="cesiumContainer" ref="cesiumContainer" class="h-full w-full"></div>
</template>
