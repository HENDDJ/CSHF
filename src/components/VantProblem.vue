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
                <MobCamera>

                </MobCamera>
            </section>
        </div>
        <div v-show="list=='2'">2</div>
        <div v-show="list=='3'">3</div>
        <div v-show="list=='4'"></div>
        <div v-show="list=='5'"></div>
        <div v-show="list=='6'"></div>
    <van-tabbar v-model="active">
        <van-tabbar-item icon="eye-o"  @click='showCamera()'>摄像头</van-tabbar-item>
        <van-tabbar-item icon="question-o" @click='showProblem()'>问题</van-tabbar-item>
        <van-tabbar-item icon="bulb-o" info="" @click='showSensor()'>传感器</van-tabbar-item>
        <van-tabbar-item icon="setting-o" info="" @click=' showData()'>数据</van-tabbar-item>
    </van-tabbar>
        </div>
</template>

<script>
    import MobCamera from "../views/menu/MobCamera";
    export default {
        name: "VantProblem",
        components: {MobCamera},
        data() {
            return {
                size: 'large',
                active: 0,
                showPop: false,
                list :'1',
                columns: ['杭州', '宁波', '温州', '嘉兴', '湖州'],
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
                this.$toast(`当前值：${value}, 当前索引：${index}`);
            },
            onCancel() {
                this.showPop =false;
            },
            showCamera(){
                this.list = '1';
            },
            showProblem(){
                this.list = '2';
            },
            showSensor(){
                this.list = '3';
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
        height: 100vh;
        overflow-x: hidden;

    }
</style>
