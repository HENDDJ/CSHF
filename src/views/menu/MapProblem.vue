<template>
    <section>
        <div id="allmap2" v-show="NewProblem=='1'"></div>
        <div v-show="NewProblem=='2'">
            <van-nav-bar
                title="问题新增"
                left-text="返回"
                left-arrow
                @click-left="onClickLeft"
            />
            <van-cell-group class="group" >
                <van-field   label="问题名称" v-model="queryForm['problemName']" placeholder="请输入问题名称" required/>
                <van-field  label="类型" v-model="queryForm['type']" placeholder="请输入类型"  required/>
                <van-field  label="信息" v-model="queryForm['message']" placeholder="请输入信息"  required/>
                <van-field  label="提交人" v-model="queryForm['submitter']" placeholder="请输入提交人" />
                <van-row>
                <img v-if="queryForm['imgUrl']" :src="queryForm['imgUrl']" /></van-row>
                <van-row>
                <van-uploader :after-read="onRead">
                    <van-icon name="photograph" />
                </van-uploader>
                    </van-row>
            </van-cell-group>
            <van-button plain type="primary" @click="add">确定</van-button>

        </div>
    </section>

</template>
<script>
    import CommonUpload from '@/components/UpLoad';
    import { Notify } from 'vant';
    import lrz from 'lrz';
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
                NewProblem : '1',
                queryForm: {
                    problemName : "2",
                    type:"",
                    message :"",
                    submissionTime : "",
                    submitter :"",
                    imgUrl :"",
                    xloc : 1,
                    yloc : 1
                },
                imageList: [
                ]

            }
        },
        methods: {
            initMap(){
                // 百度地图API功能
                this.map = new BMap.Map("allmap2");    // 创建Map实例
                this.map.centerAndZoom(new BMap.Point(118.970676,31.99981), 12);  // 初始化地图,设置中心点坐标和地图级别
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

                            }
                            let infoWindow = new BMap.InfoWindow("<p style='font-size:18px;font-weight:bold'>" + data[index].problemName + "</p><br>"+
                                "<image src='"+data[index].imgUrl+"' style='width:200px;height:113px'/><br>"+"<p style='font-size:14px;'>"+"类别："+ data[index].type+ "</p><br>"+
                                "<p style='font-size:14px;'>"+"描述："+ data[index].message+ "</p><br>" +
                                "<p style='font-size:14px;'>"+"提交时间："+ data[index].submissionTime+ "</p><br>" +
                                "<p style='font-size:14px;'>"+"提交人："+ data[index].submitter+ "</p><br>" ,fourOpts);

                            marker.addEventListener("click", function () { this.openInfoWindow(infoWindow); });

                        }

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
                    button.appendChild(document.createTextNode("当前位置新增问题"));
                    // 设置样式
                    button.style.cursor = "pointer";
                    button.style.border = "1px solid gray";


                    button.onclick = () => {
                        this.ttoo(this.map);
                    }

                    map.getContainer().appendChild(button);
                    // 将DOM元素返回
                    return button;
                }

                // 创建控件
                var myZoomCtrl = new ZoomControl();
                // 添加到地图当中
                this.map.addControl(myZoomCtrl);
            },
            ttoo(map){
                this.NewProblem ='2';
                let geolocation = new BMap.Geolocation();
                geolocation.getCurrentPosition((r)=>{
                    if(r){
                        this.queryForm['xloc']=r.point.lng;
                        this.queryForm['yloc']=r.point.lat;
                        let myDate = new Date();
                        this.queryForm['submissionTime'] = myDate;
                    }
                    else {
                        alert('failed');
                    }
                },{enableHighAccuracy: true})
            },
            onClickLeft(){
                this.NewProblem ='1';
            },
            add(){
                this.$http('POST', 'identity/tbProblem/',this.queryForm, false).then(
                    data =>{
                        this.initMap();
                        this.NewProblem ='1';
                    }

                );

            },
            onRead(file) {
                console.log(file);
                lrz(file.file, {width: 200,height :110,quality :0.7}).then(
                    (result)=>{
                let formData = new FormData();
                    formData.append('file', new File([result.file],file.file.name));


                    this.$http('POST', '/identity/accessory/', formData, false).then(
                        res => {

                            this.queryForm['imgUrl']= res.path;
                            alert(res.path);
                            this.imageList[0] = res.path
                            // this.images.push({path: res.path, active: false});
                            // this.$emit('getValue', this.images.map(item => item.path).join(','));
                        }
                    ).catch(errors=> {
                        alert(errors);
                })
            }
            )
            },

        },

        mounted() {
            this.initMap();


        }

    }
</script>


<style>
    #allmap2 {
        width: 100%;
        height: calc(100vh*0.85);
    }
    .group{
        label-align :left;
    }

</style>


