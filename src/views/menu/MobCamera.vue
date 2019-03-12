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
            closeOnClickOverlay="true"
            lockScroll="true"
            message-align="left"

            overflow="scroll"
            class="dalog"
        >
            <van-cell-group v-for="item in dataList" label-align="left" >
                <van-field  label-align="left" input-align="left" label="cameraId" v-model="item.cameraId" placeholder="请输入cameraId" required/>
                <van-field  label="cameraUuid" v-model="item.cameraUuid" placeholder="请输入cameraUuid"  required/>
                <van-field  label="cameraName" v-model="item.cameraName" placeholder="请输入cameraName"  required/>
                <van-field  label="cameraType" v-model="item.cameraType" placeholder="请输入cameraType" />
                <van-field  label="channelNum" v-model="item.cameraChannelNum" placeholder="请输入cameraType" />
                <van-field  label="onlineStatus" v-model="item.onlineStatus" placeholder="请输入cameraType" />
                <van-field  label="xloc" v-model="item.xloc" placeholder="请输入cameraType" />
                <van-field  label="yloc" v-model="item.yloc" placeholder="yloc" />

            </van-cell-group>
            <van-row>
                <van-col span="8"offset="2"><van-button type="danger" @click="submit('form')" style="width:5rem">删除</van-button></van-col>
                <van-col span="8" offset="4"> <van-button type="info" @click="diaClose" style="width:5rem">修改</van-button></van-col>
            </van-row>


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
                console.log(this.dataList[0]);

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
</style>
