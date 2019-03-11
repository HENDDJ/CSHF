<template>
    <div class="mobile-el">
        <van-search placeholder="请输入搜索关键词" v-model="value" />
        <van-list
            v-model="loading"
            :finished="finished"
            finished-text="没有更多了"
            @load="onLoad"

        >
            <van-cell
                v-for="item in list"
                :key="item"
                :title="item"
                @click="tt()"


            />

        </van-list>


    </div>

</template>

<script>
    export default {
        name: "MobCamera",
        data() {
            return {
                list: [],
                loading: false,
                finished: false
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
            tt(){
                this.$toast(`当前值`);
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
