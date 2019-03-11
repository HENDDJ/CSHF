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
                list: [],
                loading: false,
                finished: false,
                Columns: [],
                value :""
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

                console.log(this.value);
            }
        },
        created() {
            let path = this.apiRoot;
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

