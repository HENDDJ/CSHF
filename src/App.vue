<template>
    <div id="app">
        <router-view></router-view>
    </div>
</template>

<script>
export default {
    name: 'App',
    data () {
        return {
        };
    },
    components: {
    },
    methods: {
    }
};
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
  }
  .fade-enter-active {
      transition: opacity 2.2s;
  }
  .fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
      opacity: 0;
  }
  .el-input--small .el-input__inner {
      height: 32px;
      line-height: 46px;
  }
    .el-select {
        width: 178px !important;
    }
  .anchorBL{
      display:none;
  }
  .BMap_bubble_title{
      color:black;
      font-size:13px;
      font-weight: bold;
      text-align:left;
  }
  .BMap_pop div:nth-child(1){
      border-radius:7px 0 0 0;
  }
  .BMap_pop div:nth-child(3){
      border-radius:0 7px 0 0;background:#ABABAB;;
      /*background: #ABABAB;*/
      width:23px;
      width:0px;
      height:0px;
  }
  .BMap_pop div:nth-child(3) div{
      border-radius:7px;
  }
  .BMap_pop div:nth-child(5){
      border-radius:0 0 0 7px;
  }
  .BMap_pop div:nth-child(5) div{
      border-radius:7px;
  }
  .BMap_pop div:nth-child(7){
      border-radius:0 0 7px 0 ;
  }
  .BMap_pop div:nth-child(7) div{
      border-radius:7px ;
  }


</style>
