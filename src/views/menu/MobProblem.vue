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
                list: [],
                loading: false,
                finished: false,
                Columns: [],
                value :"",
                queryForm: {
                    problemName : ""
            }
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
            tt(path) {
                this.$toast(path);
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

