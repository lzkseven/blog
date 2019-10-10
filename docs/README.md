# 欢迎来到程序员成长之道

> An awesome project.

<template>
  <el-carousel :interval="4000" type="card" height="200px">
    <el-carousel-item  key="1"><img src="http://img2.imgtn.bdimg.com/it/u=3820908958,877260056&fm=26&gp=0.jpg">
    </el-carousel-item>
    <el-carousel-item  key="2"><img src="http://img0.imgtn.bdimg.com/it/u=2767513562,2035496162&fm=26&gp=0.jpg">
        </el-carousel-item>
    <el-carousel-item  key="3"><img src="http://img0.imgtn.bdimg.com/it/u=1908704551,1305980279&fm=26&gp=0.jpg">
            </el-carousel-item>
    <el-carousel-item  key="4"><img src="http://img4.imgtn.bdimg.com/it/u=2681753805,2068286531&fm=26&gp=0.jpg">
    </el-carousel-item>
  </el-carousel>
</template>

<style>
  .el-carousel__item h3 {
    color: #475669;
    font-size: 14px;
    opacity: 0.75;
    line-height: 200px;
    margin: 0;
  }
  
  .el-carousel__item:nth-child(2n) {
    background-color: #99a9bf;
  }
  
  .el-carousel__item:nth-child(2n+1) {
    background-color: #d3dce6;
  }
</style>
