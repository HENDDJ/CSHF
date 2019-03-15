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
                cameraId:""
            }
        },
        methods: {
            showCam(){
        alert("11");
    },
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
                            for( let index = 0; index < data.length; index ++){
                                let icon = new BMap.Icon('../static/img/ceamra.png', new BMap.Size(30, 37));
                                icon.imageSize = new BMap.Size(25, 31);
                                let point = new BMap.Point(Number(data[index].xloc),Number(data[index].yloc));
                                let marker = new BMap.Marker(point, {icon: icon});
                                markerCamera.push(marker);
                                this.map.addOverlay(markerCamera[index]);
                                let vueIn=this;
                                let div = document.createElement('div');

                                let p = document.createElement('p');
                                p.textContent = data[index].cameraName;
                                p.style.fontSize = '14px';
                                let button = document.createElement('button');
                                button.addEventListener('click', () => {
                                    console.log(this, "vue")
                                })
                                button.textContent = '按钮';
                                button.style='margin-up:10px';
                                let video=document.createElement('video');
                                let source=document.createElement('source');
                                source.src=data[index].encoderUuid;
                                source.type='application/x-mpegURL';
                                source.class = 'video-js vjs-default-skin vjs-big-play-centered';
                                video.id = data[index].cameraChannelNum;
                                video.append(source);
                                div.append(p);
                                div.append(video);
                                let vue=this;
                                var fourOpts = {
                                    width:320,
                                    height:208
                                }
                                let infoWindow = new BMap.InfoWindow(div,fourOpts);
                                marker.addEventListener("click", function () { this.openInfoWindow(infoWindow);
                                    vue.cameraId=data[index].cameraChannelNum;
                                    vue.initVideo();
                                });
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
                            let infoWindow = new BMap.InfoWindow("<p style='font-size:18px;font-weight:bold'>" + data[index].sensorName + "</p><br>"+
                                "<p style='font-size:14px;'>"+"温度："+ data[index].temperature+ "</p><br>"+
                                "<p style='font-size:14px;'>"+"湿度："+ data[index].humidity+ "</p><br>" +
                                "<p style='font-size:14px;'>"+"水压："+ data[index].waterLevel+ "</p><br>" +
                                "<p style='font-size:14px;'>"+"风力："+ data[index].windPower+ "</p><br>");
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
                    button.style.background="#3498db";
                    button.style.borderradius='28px';
                        button.style.fontfamily='Arial';
                        button.style.color='#ffffff';
                        button.style.fontsize='8px';
                    button.style.textdecoration='none';
                        button.style.padding='6px 15px 6px 15px';
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
                    this.defaultOffset = new BMap.Size(110, 10);
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
                    button.style.background="#3498db";
                    button.style.borderradius='28px';
                    button.style.fontfamily='Arial';
                    button.style.color='#ffffff';
                    button.style.fontsize='8px';
                    button.style.textdecoration='none';
                    button.style.padding='6px 15px 6px 15px';
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
                    this.defaultOffset = new BMap.Size(210, 10);
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
                    button.style.background="#3498db";
                    button.style.borderradius='28px';
                    button.style.fontfamily='Arial';
                    button.style.color='#ffffff';
                    button.style.fontsize='8px';
                    button.style.textdecoration='none';
                    button.style.padding='6px 15px 6px 15px';
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
            initVideo() {
                //初始化视频方法
console.log(String(this.cameraId))
                let myPlayer = this.$video(this.cameraId+'', {
                    //确定播放器是否具有用户可以与之交互的控件。没有控件，启动视频播放的唯一方法是使用autoplay属性或通过Player API。
                    controls: true,
                    //自动播放属性,muted:静音播放
                    autoplay: "muted",
                    //建议浏览器是否应在<video>加载元素后立即开始下载视频数据。
                    preload: "auto",
                    //设置视频播放器的显示宽度（以像素为单位）
                    width: "200px",
                    //设置视频播放器的显示高度（以像素为单位）
                    height: "100px",
                    class :'video-js vjs-default-skin vjs-big-play-centered'
                });
            }




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
    .vjs-tech{
        width:300px;
        height :200px;
    }
   .vjs-control-bar{
       display: none;
       height:0px;
       width :0px;
       visibility: hidden;
   }
    .BMap_center{
        width: 338px;
    }


</style>
