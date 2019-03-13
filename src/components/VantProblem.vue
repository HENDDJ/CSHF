<template>
    <div class="mobile-el">
    <van-popup v-model="showPop" position="bottom" :overlay="true">
        <van-picker
            show-toolbar
            title="选择"
            :columns="columns"
            @cancel="onCancel"
            @confirm="onConfirm"
        />
    </van-popup>
        <div v-show="list=='1'">
            <section>
                <MobSensor apiRoot="/identity/tbSensor">
                </MobSensor>
            </section>
        </div>
        <div v-show="list=='2'">
            <section>
                <MobProblem apiRoot="/identity/tbProblem">
                </MobProblem>
            </section>
        </div>
        <div v-show="list=='3'">
            <section>
                <MobCamera apiRoot="/identity/tbCamera">
                </MobCamera>
            </section>
        </div>
        <div v-show="list=='4'">
            <section>
                <MapCamera apiRoot="/identity/tbCamera">
                </MapCamera>
            </section>
        </div>
        <div v-show="list=='5'">
            <section>
                <MapProblem apiRoot="/identity/tbProblem">
                </MapProblem>
            </section>
        </div>
        <div v-show="list=='6'">
            <section>
                <MapSensor apiRoot="/identity/tbSensor">
                </MapSensor>
            </section>
        </div>
    <van-tabbar v-model="active">
        <van-tabbar-item icon="eye-o"  @click='showCamera()'>摄像头</van-tabbar-item>
        <van-tabbar-item icon="question-o" @click='showProblem()'>问题</van-tabbar-item>
        <van-tabbar-item icon="bulb-o" info="" @click='showSensor()'>传感器</van-tabbar-item>
        <van-tabbar-item icon="setting-o" info="" @click=' showData()'>数据</van-tabbar-item>
    </van-tabbar>
        </div>
</template>

<script>

    import MobSensor from "../views/menu/MobSensor";
    import MobCamera from "../views/menu/MobCamera";
    import MapCamera from "../views/menu/MapCamera";
    import MapProblem from "../views/menu/MapProblem";
    import MapSensor from "../views/menu/MapSensor";
    import MobProblem from "../views/menu/MobProblem";
    export default {
        name: "VantProblem",
        components: {MobCamera,MobSensor,MobProblem,MapSensor,MapProblem,MapCamera},

        data() {
            return {
                size: 'large',
                active: 0,
                showPop: false,
                list :'4',
                columns: ['传感器', '问题', '摄像头'],
                actions: [
                    {
                        name: '选项'
                    },
                    {
                        name: '分享',

                    },
                ]
            }

        },
        methods :{
            showData(){
                this.showPop =true;
            },
            onConfirm(value, index) {
                this.list = index+1;
                this.showPop =false;
            },
            onCancel() {
                this.showPop =false;
            },
            showCamera(){
                this.list = '4';
            },
            showProblem(){
                this.list = '5';
            },
            showSensor(){
                this.list = '6';
            }
        }
    }
</script>

<style scoped>
    .qq{
        background-color: black;
    }
    .mobile-el {
        width: 100vw;



    }
</style>
