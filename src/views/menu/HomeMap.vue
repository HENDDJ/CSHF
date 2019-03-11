<template>
    <section>
        <div id="allmap"></div>
        <div id="">

        </div>
    </section>

</template>

<script>
    export default {
        name: "HomeMap",
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
                this.map.centerAndZoom(new BMap.Point(119.080676,31.86981), 14);  // 初始化地图,设置中心点坐标和地图级别
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
                            this.cameraPoint = markerCamera;

                        }
                    );
                this.$http('POST', 'identity/tbProblem/list', false).then(
                    data => {
                        let markerProblem = [];
                        for( var index = 0; index < data.length; index ++){
                            let point = new BMap.Point(Number(data[index].xloc),Number(data[index].yloc));
                            let marker = new BMap.Marker(point);
                            markerProblem.push(marker);
                            this.map.addOverlay(markerProblem[index]);
                            var fourOpts = {
                                width:200,
                                height:300
                            }
                            let infoWindow = new BMap.InfoWindow("<p style='font-size:18px;font-weight:bold'>" + data[index].problemName + "</p><br>"+
                            "<image src='"+data[index].imgUrl+"' style='width:200px;height:113px'/><br>"+"<p style='font-size:14px;'>"+"类别："+ data[index].type+ "</p><br>"+
                                "<p style='font-size:14px;'>"+"描述："+ data[index].message+ "</p><br>" +
                                "<p style='font-size:14px;'>"+"提交时间："+ data[index].submissionTime+ "</p><br>" +
                                "<p style='font-size:14px;'>"+"提交人："+ data[index].submitter+ "</p><br>" ,fourOpts);

                            marker.addEventListener("click", function () { this.openInfoWindow(infoWindow); });
                            marker.hide();
                        }
                        this.problemPoint = markerProblem;

                    }
                );
                this.$http('POST', 'identity/tbSensor/list', false).then(
                    data => {
                        let markerCamera = [];
                        for( var index = 0; index < data.length; index ++){
                            let point = new BMap.Point(Number(data[index].xloc),Number(data[index].yloc));
                            let marker = new BMap.Marker(point);
                            markerCamera.push(marker);
                            this.map.addOverlay(markerCamera[index]);
                            let infoWindow = new BMap.InfoWindow("<p style='font-size:14px;'>" + data[index].yloc + "</p>");
                            marker.addEventListener("click", function () { this.openInfoWindow(infoWindow); });
                            marker.hide();
                        }
                        this.sensorPoint = markerCamera;

                    }
                );

                function ZoomControl(){
                    // 默认停靠位置和偏移量
                    this.defaultAnchor = BMAP_ANCHOR_TOP_LEFT;
                    this.defaultOffset = new BMap.Size(10, 10);
                }
                // 通过JavaScript的prototype属性继承于BMap.Control
                ZoomControl.prototype = new BMap.Control();

                ZoomControl.prototype.initialize = (map) => {
                    // 创建一个DOM元素
                    var button = document.createElement("button");
                    // 添加文字说明
                    button.appendChild(document.createTextNode("摄像头"));
                    // 设置样式
                    button.style.cursor = "pointer";
                    button.style.border = "1px solid gray";


                    button.onclick = () => {
                        this.showCamera();
                    }

                    map.getContainer().appendChild(button);
                    // 将DOM元素返回
                    return button;
                }
                // 创建控件
                var myZoomCtrl = new ZoomControl();
                // 添加到地图当中
                this.map.addControl(myZoomCtrl);



                function ZoomControl1(){
                    // 默认停靠位置和偏移量
                    this.defaultAnchor = (BMAP_ANCHOR_TOP_LEFT);
                    this.defaultOffset = new BMap.Size(70, 10);
                }
                // 通过JavaScript的prototype属性继承于BMap.Control
                ZoomControl1.prototype = new BMap.Control();

                ZoomControl1.prototype.initialize = (map) => {
                    // 创建一个DOM元素
                    var button = document.createElement("button");
                    // 添加文字说明
                    button.appendChild(document.createTextNode("问题"));
                    // 设置样式
                    button.style.cursor = "pointer";
                    button.style.border = "1px solid gray";
                    // 绑定事件,点击一次放大两级
                    button.onclick = () =>{
                        this.showProblem();
                    }

                    map.getContainer().appendChild(button);
                    // 将DOM元素返回
                    return button;
                }
                // 创建控件
                var myZoomCtrl1 = new ZoomControl1();
                // 添加到地图当中
                this.map.addControl(myZoomCtrl1);



                function ZoomControl2(){
                    // 默认停靠位置和偏移量
                    this.defaultAnchor = (BMAP_ANCHOR_TOP_LEFT);
                    this.defaultOffset = new BMap.Size(130, 10);
                }
                // 通过JavaScript的prototype属性继承于BMap.Control
                ZoomControl2.prototype = new BMap.Control();

                ZoomControl2.prototype.initialize = (map) => {
                    // 创建一个DOM元素
                    var button = document.createElement("button");
                    // 添加文字说明
                    button.appendChild(document.createTextNode("传感器"));
                    // 设置样式
                    button.style.cursor = "pointer";
                    button.style.border = "1px solid gray";
                    // 绑定事件,点击一次放大两级
                    button.onclick = () =>{
                        this.showSensor();
                    }

                    map.getContainer().appendChild(button);
                    // 将DOM元素返回
                    return button;
                }
                // 创建控件
                var myZoomCtrl2 = new ZoomControl2();
                // 添加到地图当中
                this.map.addControl(myZoomCtrl2);

            },
            showCamera(){
                let a1=[];
                a1=this.cameraPoint;
                for(var index=0;index<a1.length;index++){
                    a1[index].show();
                };
                let a2=[];
                a2=this.problemPoint;
                for(var index=0;index<a2.length;index++){
                    a2[index].hide();
                };
                let a3=[];
                a3=this.sensorPoint;
                for(var index=0;index<a3.length;index++){
                    a3[index].hide();
                }

            },
            showProblem(){
                let a1=[];
                a1=this.cameraPoint;
                for(var index=0;index<a1.length;index++){
                    a1[index].hide();
                };
                let a2=[];
                a2=this.problemPoint;
                for(var index=0;index<a2.length;index++){
                    a2[index].show();
                };
                let a3=[];
                a3=this.sensorPoint;
                for(var index=0;index<a3.length;index++){
                    a3[index].hide();
                }
            },

            showSensor(){
                let a1=[];
                a1=this.cameraPoint;
                for(var index=0;index<a1.length;index++){
                    a1[index].hide();
                };
                let a2=[];
                a2=this.problemPoint;
                for(var index=0;index<a2.length;index++){
                    a2[index].hide();
                };
                let a3=[];
                a3=this.sensorPoint;
                for(var index=0;index<a3.length;index++){
                    a3[index].show();
                }
            },



            SPV() {
                var spv = window.parent.document.getElementById("spv");
                // 初始化
                InitSpvx(spv);
                // 设置本地参数
                SetLocalParam(spv);
            }

        },

        mounted() {
            this.initMap();
            this.SPV();

        }

    }
</script>


<style>
    #allmap {
        width: 100%;
        height: calc(100vh*0.85);
    }

</style>
