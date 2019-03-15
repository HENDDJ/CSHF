<template>
    <div class="mobile-el">
        <van-search placeholder="请输入搜索关键词" v-model="value"
                    show-action
                    @search="onSearch"
        >
            <div slot="action" @click="onSearch()">搜索</div>
        </van-search>
        <van-list
            v-model="loading"
            :finished="finished"
            finished-text="没有更多了"
            @load="onLoad"
        >
            <van-cell
                v-for="item in tableData"
                :key="item.cameraName"
                :title="item.cameraName"
                @click="tt(item.cameraName)"
            />

        </van-list>

        <van-dialog
            v-model="diashow"
            confirmButtonText="取消"
            :before-close="beforeClose"
            class="dalog"
        >
            <van-cell-group v-for="item in dataList"  class="group" :key="item.cameraId">
                <van-field   label="cameraId" v-model="item.cameraId" placeholder="" required/>
                <van-field  label="cameraUuid" v-model="item.cameraUuid" placeholder=""  required/>
                <van-field  label="cameraName" v-model="item.cameraName" placeholder=""  required/>
                <van-field  label="cameraType" v-model="item.cameraType" placeholder="" />
                <van-field  label="channelNum" v-model="item.cameraChannelNum" placeholder="" />
                <van-field  label="onlineStatus" v-model="item.onlineStatus" placeholder="" />
            </van-cell-group>
        </van-dialog>
    </div>

</template>

<script>
    export default {
        name: "MobCamera",
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
                    cameraName : ""
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
            tt(cameraName) {
                this.dataList=[];
                let queryForm={};
                queryForm['cameraName'] = cameraName;
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
                this.tableData =[];
                this.$http('POST', path, false).then(
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
                    this.queryForm['cameraName'] = this.value;
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
                this.value="";
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

    }
    .dalog{

        overflow :scroll;
    }
    .group{
        label-align :left
    }
</style>
