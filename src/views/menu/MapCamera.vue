<template>
    <section>
        <div id="allmap"></div>


    </section>

</template>

<script>
    export default {
        name: "MapCamera",
        data() {
            return {
                map: {},
                cameraPoint :[],
                problemPoint :[],
                sensorPoint :[],
                IP_PORT : "http://122.97.218.162:18080",
                APP_KEY : "a592d676",
                SECRET : "69681c3587194a50a2b11f1335ad6f41",
                opUserUuid : 'c26a811c141a11e79aeeb32ef95273f2',
                netZoneUuid : 'f5816cf43fcc41d880d9f636fa8bc443',
            }
        },
        methods: {
            initMap(){
                // 百度地图API功能
                this.map = new BMap.Map("allmap");    // 创建Map实例
                this.map.centerAndZoom(new BMap.Point(119.080676,31.86981), 12);  // 初始化地图,设置中心点坐标和地图级别
                //添加地图类型控件

                this.map.enableScrollWheelZoom(true);     //开启鼠标滚轮缩放
                var vectorMarker = new BMap.Marker(new BMap.Point(119.080676,31.86981), {
                    // 指定Marker的icon属性为Symbol
                    icon: new BMap.Symbol(BMap_Symbol_SHAPE_POINT, {
                        scale: 1.5,//图标缩放大小
                        fillColor: "orange",//填充颜色
                        fillOpacity: 0.8//填充透明度
                    })
                });

                vectorMarker.setAnimation(BMAP_ANIMATION_BOUNCE);// 创建标注
                this.map.addOverlay(vectorMarker);              // 将标注添加到地图中
                let infoWindow1 = new BMap.InfoWindow("<p style='font-size:14px;'>"+"赤山湖管委会"+"</p>");
                vectorMarker.addEventListener("click", function () { this.openInfoWindow(infoWindow1); });


                this.$http('POST', 'identity/tbCamera/list', false).then(
                    data => {
                        let markerCamera = [];
                        for( var index = 0; index < data.length; index ++){
                            let icon = new BMap.Icon('../static/img/ceamra.png', new BMap.Size(30, 37));
                            icon.imageSize = new BMap.Size(25, 31);
                            let point = new BMap.Point(Number(data[index].xloc),Number(data[index].yloc));
                            let marker = new BMap.Marker(point, {icon: icon});
                            markerCamera.push(marker);
                            this.map.addOverlay(markerCamera[index]);
                            let infoWindow = new BMap.InfoWindow("<p style='font-size:14px;'>" + data[index].cameraName + "</p>"+"<br>"+'999');
                            marker.addEventListener("click", function () { this.openInfoWindow(infoWindow); });
                        }

                    }
                );
            },
        },

        mounted() {
            this.initMap();


        }

    }
</script>


<style>
    #allmap {
        width: 100%;
        height: calc(100vh*0.85);
    }

</style>

