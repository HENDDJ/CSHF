<template>
    <div class="mobile-el">
        <van-search placeholder="请输入搜索关键词" v-model="value"
                    show-action
                    @search="onSearch"
        >
            <div slot="action" @click="onSearch">搜索</div>
        </van-search>
        <van-list
            v-model="loading"
            :finished="finished"
            finished-text="没有更多了"
            @load="onLoad"
        >
            <van-cell
                v-for="item in tableData"
                :key="item.sensorName"
                :title="item.sensorName"
                @click="tt(item.sensorName)"
            />

        </van-list>
        <van-dialog
        v-model="diashow"
        confirmButtonText="取消"
        :before-close="beforeClose"
        class="dalog"
        >
        <van-cell-group v-for="item in dataList"  class="group" :key="item.sensorName">
            <van-field   label="传感器名" v-model="item.sensorName" placeholder="" required/>
            <van-field  label="温度" v-model="item.temperature" placeholder=""  />
            <van-field  label="湿度" v-model="item.humidity" placeholder=""  />
            <van-field  label="水位" v-model="item.waterLevel" placeholder=""/>
            <van-field  label="风力" v-model="item.windPower" placeholder="" />
        </van-cell-group>
        </van-dialog>

    </div>

</template>

<script>
    export default {
        name: "MobSensor",
        props: {
            apiRoot: String,
        },
        data() {
            return {
                tableData: [],
                dataList :[],
                list: [],
                loading: false,
                finished: false,
                Columns: [],
                value :"",
                queryForm: {
                    sensorName : ""
                },
                form: {},
                diashow :false
            };
        },
        methods: {
            onLoad() {
                // 异步更新数据
                setTimeout(() => {
                    for (let i = 0; i < 10; i++) {
                        this.list.push(this.list.length + 1);
                    }
                    // 加载状态结束
                    this.loading = false;
                    // 数据全部加载完成
                    if (this.list.length >= 40) {
                        this.finished = true;
                    }
                }, 500);
            },
            tt(sensorName) {
                this.dataList=[];
                let queryForm={};
                queryForm['sensorName'] = sensorName;
                let path = `${this.apiRoot}/list`;
                this.$http('POST', path, queryForm, false).then(
                    data => {
                        for (var index = 0; index < data.length; index++) {
                            this.dataList.push(data[index]);
                        }
                    }
                );


                this.diashow=true
            },
            loadTableData(path){

                this.$http('POST', path, this.queryForm, false).then(
                    data => {
                        for (var index = 0; index < data.length; index++) {
                            this.tableData.push(data[index]);
                        }
                    }
                );
            },
            onSearch(){
                this.tableData =[];
                this.queryForm={};
                if(this.value){
                    console.log(this.value)
                    this.queryForm['sensorName'] = this.value;
                }
                console.log(this.queryForm)
                let path = `${this.apiRoot}/list`;
                this.$http('POST', path, this.queryForm, false).then(
                    data => {
                        for (var index = 0; index < data.length; index++) {
                            this.tableData.push(data[index]);
                        }
                    }
                );
            },
            beforeClose(action, done) {
                if (action === 'confirm') {
                    // setTimeout(done, 1000);
                    done();
                } else {
                    done();
                }
            },
            diaClose(action, done) {

                this.diashow = false;

            }
        },
        created() {
            let path = `${this.apiRoot}/list`;
            console.log(path);
            this.loadTableData(path);

        },
        computed: {
            path() {
                return `${this.apiRoot}`;
            }
        }

    }

</script>

<style scoped>

    .mobile-el {
        width: 100vw;
        height: 100vh;

    }
</style>

