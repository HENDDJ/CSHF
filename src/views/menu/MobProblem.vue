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
                :key="item.problemName"
                :title="item.problemName"
                @click="tt(item.problemName)"
            />

        </van-list>

        <van-dialog
            v-model="diashow"
            confirmButtonText="取消"
            :before-close="beforeClose"
            class="dalog"
        >
            <van-cell-group v-for="item in dataList"  class="group" :key="item.problemName">
                <van-field   label="问题名称" v-model="item.problemName" placeholder="请输入problemName" required/>
                <van-field  label="类型" v-model="item.type" placeholder="请输入type"  required/>
                <van-field  label="信息" v-model="item.message" placeholder="请输入message"  required/>
                <van-field  label="提交时间" v-model="item.submissionTime" type="datetime"/>
                <van-field  label="提交人" v-model="item.submitter" placeholder="请输入submitter" />
                <img  :src="item.imgUrl" />
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
        name: "MobProblem",
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
                    problemName : ""
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
            tt(problemName) {
                this.dataList=[];
                let queryForm={};
                queryForm['problemName'] = problemName;
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
                    this.queryForm['problemName'] = this.value;
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
    .dalog{

        overflow :scroll;
    }
    .group{
        label-align :left;
    }
</style>

