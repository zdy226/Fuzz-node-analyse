<template>
    <div class="container">
        <div class="map">
            <div class="quan"></div>
            <div class="quan0"></div>
            <div class="quan1"></div>
            <div class="quan2"></div>
            <div class="quan3"></div>
            <div class="quan4"></div>
            <div class="quan5"></div>
            <div class="quan6"></div>
            <div class="quan7"></div>
            <div class="quan8"></div>
            <div class="quan9"></div>
            <div>
                <div class="title" style="font-size:30px;color:white;">应用威胁检测全网安全态势</div>
                <el-row>
                    <el-col :span="4">
                        <div class="table" style="font-size:15px;color:white;height:330px;overflow:hidden">20XX.XX.XX AI检测TOP10恶意应用
                            <el-table
                                :data="shiyanshidata"
                                :row-style="{height:'20px'}"
                                :cell-style="{padding:'0px'}"
                                style="font-size:10px;color:white"
                            >
                                <el-table-column
                                    type="index"
                                    :index="indexMethod"
                                    label="排名"
                                >
                                </el-table-column>

                                <el-table-column
                                    label="应用名称"
                                    prop="appname"
                                    width="100px"
                                    align="center"
                                >
                                </el-table-column>

                                <el-table-column
                                    label="应用数量"
                                    prop="quantity"
                                    width="100px"
                                    align="center"
                                >
                                </el-table-column>
                            </el-table>
                        </div>
                   </el-col>
                   <el-col :span="16">
                       <div class="grid-content bg-purple"></div>
                   </el-col>
                   <el-col :span="4" style="text-align:right">
                       <div class="table" style="font-size:15px;color:white">20XX.XX.XX恶意威胁种类排名
                           <el-table
                                :data="youshangjiaodata"
                                :row-style="{height:'20px'}"
                                :cell-style="{padding:'0px'}"
                                style="font-size:10px;color:white"
                            >

                                <el-table-column
                                    label="恶意种类"
                                    prop="malicioustype"
                                    width="100px"
                                    align="center"
                                >
                                </el-table-column>

                                <el-table-column
                                    label="检出应用数"
                                    prop="quantity"
                                    width="100px"
                                    align="center"
                                >
                                </el-table-column>
                            </el-table>
                       </div>
                   </el-col>
                </el-row>
                <div class="scroll-board">
                    <div style="font-size:16px;color:white">威胁日志</div>
                    <dv-scroll-board :config="config" />
                </div>
            </div>
        </div>
    </div>
</template>

<script>
// import {
//     AppRankingInfo,
//     MaliciousThreatsInfo,
//     ThreatLogInfo
// } from "../../services/laboratory_services";
// import map from "./map";

export default {
    name: "Laboratory",
    // components: {
    //     map
    // },
    props:{},
    data() {
        return {
            shiyanshidata:[
                {appname:"asd",quantity:"55"},
                {appname:"asd",quantity:"55"},
                {appname:"asd",quantity:"55"},
                {appname:"asd",quantity:"55"},
                {appname:"asd",quantity:"55"},
                {appname:"asd",quantity:"55"},
                {appname:"asd",quantity:"55"},
                {appname:"asd",quantity:"55"},
                {appname:"asd",quantity:"55"},
                {appname:"asd",quantity:"55"},
                {appname:"asd",quantity:"55"},
                {appname:"asd",quantity:"55"},
                {appname:"asd",quantity:"55"},
                {appname:"asd",quantity:"55"},
                {appname:"asd",quantity:"55"},
                {appname:"asd",quantity:"55"},
                {appname:"asd",quantity:"55"},
                {appname:"asd",quantity:"55"},
                {appname:"asd",quantity:"55"},
                {appname:"asd",quantity:"55"},
                {appname:"asd",quantity:"55"},
            ],
            youshangjiaodata:[
               
            ],
            youxiajiaodata:[],
            config: {
                        header: ["a","b","c","d","e","f"],
                        data: [
                            ['2019-07-01 19:25:00', '路面危害-松散', '5', 'xxxxxxx'],
                            ['2019-07-02 17:25:00', '路面危害-路面油污清理', '13', 'xxxxxxx'],
                            ['2019-07-03 16:25:00', '交安设施-交通标志牌结构', '6', 'xxxxxxx'],
                            ['2019-07-04 15:25:00', '路基危害-防尘网', '2', 'xxxxxxx'],
                            ['2019-07-05 14:25:00', '交安设施-交通标志牌结构', '1', 'xxxxxxx'],
                            ['2019-07-06 13:25:00', '路面危害-松散', '3', 'xxxxxxx'],
                            ['2019-07-07 12:25:00', '路基危害-防尘网', '4', 'xxxxxxx'],
                            ['2019-07-08 11:25:00', '路面危害-路面油污清理', '2', 'xxxxxxx'],
                            ['2019-07-09 10:25:00', '交安设施-交通标志牌结构', '5', 'xxxxxxx'],
                            ['2019-07-10 09:25:00', '路基危害-防尘网', '3', 'xxxxxxx']
                        ],
                        index: false,
                        columnWidth: [220, 200, 160, 100, 150, 120],
                        align: ["center"],
                        rowNum: 7,
                        headerBGC: "#1981f6",
                        headerHeight: 20,
                        oddRowBGC: "rgba(0, 44, 81, 0.8)",
                        evenRowBGC: "rgba(10, 29, 50, 0.8)"
            }
        };
    },
    computed: {},
    watch: {},
    created() {
        // this.AppRanking(),
        // this.MaliciousThreat(),
        // this.youshangjiaodataInfo()
    },
    mounted() {},
    methods: {
        AppRanking() {
            AppRankingInfo().then(res => {
                if (!res.datas.code === 0){
                    throw new Error(res.datas.msg);
                }
                else {
                    this.shiyanshidata = res.datas.list;
                }
            });
        },

        MaliciousThreat() {
            MaliciousThreatsInfo().then(res => {
                if (!res.datas.code === 0){
                    throw new Error(res.datas.msg);
                }
                else {
                    this.youshangjiaodata = res.datas.list;
                }
            });
        },

        youshangjiaodataInfo() {
            ThreatLogInfo().then(res => {
                if (!res.datas.code === 0){
                    throw new Error(res.datas.msg);
                }
                else {
                    this.youxiajiaodata = res.datas.list;
                    let array = [];
                    this.youxiajiaodata.forEach((item, index) => {
                        let arr1 = Object.values(item);
                        let arr2 = arr1.splice(0,6);
                        array.push(arr2);
                    });
                    // console.log(array);
                    // console.log(this.config.data);
                    this.config = {
                        header: [],
                        data: [],
                        index: false,
                        columnWidth: [220, 200, 160, 100, 150, 120],
                        align: ["center"],
                        rowNum: 7,
                        headerBGC: "#1981f6",
                        headerHeight: 20,
                        oddRowBGC: "rgba(0, 44, 81, 0.8)",
                        evenRowBGC: "rgba(10, 29, 50, 0.8)"
                    }
                }
            });
        }
    }
};
</script>

<style>
    .title {
        background: #5b9bd5;
        font-size: 10px;
        width: 700px;
        height: 40px;
        text-align: center;
        margin: 0 auto;
    }

    .table {
        text-align: center;
        font-weight: 700;
    }

    .el-table::before {
        left: 0;
        bottom :0;
        width: 100%;
        height: 0px;
    }

    .el-table {
        color: black;
    }

    .table /deep/ .el-table--fit {
        padding: 20px;
    }

    .table /deep/ .el-table, .el-table__expanded-cell {
        background-color:transparent;
    }

    .table /deep/ .el-table tr {
        background-color:transparent !important;
    }

    .table /deep/ .el-table--enabke--row-transition .el-table__body td, .el-table .cell {
        background-color: transparent;
    }

    .el-row {
        margin-bottom:20px;
    }

    .el-row :last-child {
        margin-bottom:0;
    }

    .el-col {
        border-radius: 4px;
    }

    .bg-purple-light {
        background: #e5e9f2;
    }

    .grid-content {
        border-radius: 4px;
        min-height: 36px;
    }

    .row-bg {
        padding: 10px 0;
        background-color: #f9fafc;
    }
    
    .scroll-board {
        position: absolute;
        right: 0;
        top: 500px;
        width: 40%;
        box-sizing: border-box;
        margin-left: 20px;
        height: 400px;
        overflow: hidden;
        margin-right: 10px;
    }

    .map {
        width: 100%;
        height: 900px;
        background: url('../../assets/aaa.jpg') no-repeat center/cover;
    }

    img {
        width: 100%;
        height: 100%;
    }

    @keyframes fangda {
      75% {
        width:5px;
        height: 5px;
        transform:  translate(0) scale(1);
        opacity: 0;
      }
      100% {
        width: 5px;
        height: 5px;
        transform:  translate(100) scale(2);
        opacity: 1;
      }
    }
    .quan {
      width: 5px;
      height: 5px;
      background: yellow;
      box-shadow: 0 0 5px yellow;
      border-radius: 50%;
      position: absolute;
      top: 50%;
      left: 90%;
      animation: fangda 4s linear infinite;
      animation-delay: 2s;
    }

    .quan0 {
            width: 5px;
            height: 5px;
            background: yellow;
            box-shadow: 0 0 5px yellow;
            border-radius: 50%;
            position: absolute;
            top: 55%;
            left: 45%;
            animation: fangda 4s linear infinite;
            animation-delay: 4s;
        }
    .quan1 {
      width: 5px;
      height: 5px;
      background: yellow;
      box-shadow: 0 0 5px yellow;
      border-radius: 50%;
      position: absolute;
      top: 30%;
      left: 30%;
      animation: fangda 4s linear infinite;
      animation-delay: 6s;
    }

    .quan2 {
      width: 5px;
      height: 5px;
      background: yellow;
      box-shadow: 0 0 5px yellow;
      border-radius: 50%;
      position: absolute;
      top: 30%;
      left: 70%;
      animation: fangda 4s linear infinite;
      animation-delay: 8s;
    }

    .quan3 {
      width: 5px;
      height: 5px;
      background: yellow;
      box-shadow: 0 0 5px yellow;
      border-radius: 50%;
      position: absolute;
      top: 60%;
      left: 30%;
      animation: fangda 4s linear infinite;
      animation-delay: 10s;
    }
    
    .quan4 {
        width: 5px;
        height: 5px;
        background: yellow;
        box-shadow: 0 0 5px yellow;
        border-radius: 50%;
        position: absolute;
        top: 90%;
        left: 60%;
        animation: fangda 4s linear infinite;
        animation-delay: 12s;
    }
    .quan5 {
        width: 5px;
        height: 5px;
        background: yellow;
        box-shadow: 0 0 5px yellow;
        border-radius: 50%;
        position: absolute;
        top: 59%;
        left: 32%;
        animation: fangda 4s linear infinite;
        animation-delay: 14s;
    }
    .quan6 {
        width: 5px;
        height: 5px;
        background: yellow;
        box-shadow: 0 0 5px yellow;
        border-radius: 50%;
        position: absolute;
        top: 50%;
        left: 96%;
        animation: fangda 4s linear infinite;
        animation-delay: 16s;
    }
    .quan7 {
        width: 5px;
        height: 5px;
        background: yellow;
        box-shadow: 0 0 5px yellow;
        border-radius: 50%;
        position: absolute;
        top: 70%;
        left: 58%;
        animation: fangda 4s linear infinite;
        animation-delay: 18s;
    }
    .quan8 {
        width: 5px;
        height: 5px;
        background: yellow;
        box-shadow: 0 0 5px yellow;
        border-radius: 50%;
        position: absolute;
        top: 45%;
        left: 62%;
        animation: fangda 4s linear infinite;
        animation-delay: 20s;
    }
    .quan9 {
        width: 5px;
        height: 5px;
        background: yellow;
        box-shadow: 0 0 5px yellow;
        border-radius: 50%;
        position: absolute;
        top: 30%;
        left: 50%;
        animation: fangda 4s linear infinite;
        animation-delay: 6.6s;
    }
    
</style>
