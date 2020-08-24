<template>
    <div class='container'>
        <el-row>
            <div>
                <el-col :span="4">
                    <label style="height:15px;padding-right:5px;">EMUI</label>
                    <el-select v-model="queryParams.emui">
                        <el-option
                            v-for="(item, index) in EMUI_options"
                            :key="index"
                            :value="item"
                        >
                            {{ item }}
                        </el-option>
                    </el-select>
                </el-col>

                <el-col :span="4">
                    <label style="height:15px;padding-right:5px;">产品</label>
                    <el-select v-model="chanpin_optionsAll" @on-change="handleProductChange">
                        <el-option
                            v-for="(item, index) in chanpin_options"
                            :key="index"
                            :value="item"
                        >
                            {{ item }}
                        </el-option>
                    </el-select>
                </el-col>

                <el-col :span="4">
                    <label style="height:15px;padding-right:5px;">版本</label>
                    <el-select multiple v-model="queryParams.version">
                        <el-option
                            v-for="(item, index) in banben_options"
                            :key="index"
                            :value="item"
                        >
                            {{ item }}
                        </el-option>
                    </el-select>
                </el-col>

                <el-col :span="4">
                    <label style="height:15px;padding-right:5px;">节点分层</label>
                    <el-select v-model="jiedian_optionsAll">
                        <el-option
                            v-for="(item, index) in jiedian_options"
                            :key="index"
                            :value="item"
                        >
                            {{ item }}
                        </el-option>
                    </el-select>
                </el-col>

                <el-col :span="4">
                    <label style="height:15px;padding-right:5px;">领域</label>
                    <el-select v-model="lingyu_optionsAll">
                        <el-option
                            v-for="(item, index) in lingyu_options"
                            :key="index"
                            :value="item"
                        >
                            {{ item }}
                        </el-option>
                    </el-select>
                </el-col>

                <el-col :span="4">
                    <label style="height:15px;padding-right:5px;">工具</label>
                    <el-select multiple v-model="queryParams.targetType">
                        <el-option
                            v-for="(item, index) in gongju_options"
                            :key="index"
                            :value="item"
                        >
                            {{ item }}
                        </el-option>
                    </el-select>
                </el-col>
            </div>
        </el-row>
        <h1 class="topName" >各领域应用统计</h1>
        <el-button class="nextName" v-model="gongju_optionsAll"></el-button>
        <div id='Myecharts' style="height:400px;margin-top:20px"></div>
    </div>
</template>

<script>
export default {
    name:'',
    components: {},
    props: {},
    data() {
        return {
            EMUI_options: [],
            chanpin_options: [],
            chanpin_optionsAll: [],
            banben_options: [],
            banben_optionsAll: [],
            jiedian_options: [],
            jiedian_optionsAll: [],
            lingyu_options: [],
            lingyu_optionsAll: [],
            gongju_options: [],
            gongju_optionsAll: [],
            chartData: {
                barData: {
                    tools:[],
                    toolsCount:[],
                    fieldName: [],
                },
            },

            //定义请求参数
            queryParams: {
                emui: "",
                productName: "",
                version: [],
                //工具
                targetType: [],
                //领域
                fieldName: "",
                //节点分层
                pointName: "",
            },
        }
    },
    computed: {},
    watch:{
        "queryParams.emui": {
            handler(newval, oldval) {
                this.getProductByVersion(newval);
            }
        },

        "queryParams.productName": {
            handler(newval, oldval) {
                this.getVersionByParam(newval);
            }
        },

        "queryParams.version": {
            handler(newval, oldval) {
                this.getToolsByParam(newval);
            }
        },

        "queryParams.pointName": {
            handler(newval, oldval) {
                this.getToolsByParam(newval);
            }
        }
    },
    created() {
        this.getEmuiAndField();
    },
    mounted() {
        this.$nextTick(() => {
            this.drawLine();
        });
    },
    methods: {
        getEmuiAndField() {
            //查询emui和领域
            getFuzzEmuiVersion().then((res) => {
                if (!res.datas.code === 0){
                    throw new Error(res.datas.message);
                }
                this.EMUI_options = res.datas.emui;
                // 选中第一个版本
                this.queryParams.emui = this.EMUI_options[0];
                // 获取领域列表
                this.lingyu_options = res.datas.fieldName;
                // 增加所有领域选择项
                this.lingyu_options.splice(0, 0, "所有领域");
            })
                .catch((error) => {
                    this.$Notice.error({
                        title: "错误",
                        desc: `${error}`
                    });
                });
        },

        getProductByVersion(value) {
            const params = {
                emui: value,
            };
            //查询产品
            getProductByVersion(params).then((res) => {
                if (!res.datas.code === 0) {
                    throw new Error(res.datas.message);
                }
                this.chanpin_options = res.datas.product;
                this.chanpin_options.splice(0, 0, "所有产品");
                this.chanpin_optionsAll = this.chanpin_options[1];
                this.queryParams.productName = this.chanpin_optionsAll[1];
            });
        },

        getVersionByParam(value) {
            const params = {
                emui: this.queryParams.emui,
                productName: value,
            };
            //查询版本
            getVersionNum(params).then((res) => {
                if (!res.datas.code === 0) {
                    throw new Error(res.datas.message);
                }
                this.banben_options = res.datas.version;
                this.banben_options.splice(0, 0, "所有版本");
                this.banben_optionsAll = this.banben_options[1];
                this.queryParams.version = this.banben_optionsAll;
            })
            .catch((error) => {
                    this.$Notice.error({
                        title: "错误",
                        desc: `${error}`
                    });
            });
        },

        getToolsByParam(value) {
            const params = {
                emui: this.queryParams.emui,
                pointName: value,         
            };
            this.initPageBar();
            this.initPagePie();
            //查询工具
            getToolsList(params).then((res) => {
                if (!res.datas.code === 0) {
                    throw new Error(res.datas.message);
                }
                this.gongju_options = res.datas.tools;
                this.gongju_options.splice(0, 0, {
                    value: "所有工具",
                    label: "所有工具",
                });
                this.gongju_optionsAll = this.gongju_options[1].value;
                this.queryParams.targetType = this.gongju_optionsAll.value;
                // this.initPageTable();
            })
             .catch((error) => {
                    this.$Notice.error({
                        title: "错误",
                        desc: `${error}`
                });
            });
        },

        initPageBar() {
            const params = {
                emui: this.queryParams.emui,
                pointName: this.queryParams.pointName
            };
            //查询工具数量(柱状图)
            getFuzzPointBarData(params).then((res) => {
                
                if (!res.datas.code === 0) {
                    throw new Error(res.datas.message);
                }
                this.chartData.barData.toolsCount = res.datas.num;
                this.chartData.barData.fieldName = this.lingyu_options;
                //echarts?????
                this.drawLine();
            })
            .catch((error) => {
                    this.$Notice.error({
                        title: "错误",
                        desc: `${error}`
                });
            });
        },

        abcd() {
            const arr = [];
            efg(params).then((res) => {
                if (!res.datas.code === 0) {
                    throw new Error(res.datas.message);
                }
                this.arr = res.datas.tools;
                for( var i= 0; arr[i] < arr.length; i++ ){
                    

                }
            });
        },

         drawLine() {
            //基于准备好的dom，初始化echarts实例
            let echarts = this.$echarts.init(document.getElementById("Myecharts"));
            //绘制图表
            window.addEventListener("resize", () => {
                echarts.resize();
            });
            echarts.setOption({
                // title:{
                //     text:'各领域应用统计',  
                // },
                tooltip: {
                    tigger: "axis",
                },
                legend: {
                    data: ["已完成分析", "未完成分析"],
                     x: 'center',
                     y: 'bottom', 
                },
                grid: {
                    left: "0%",
                    right: "0%",
                    bottom: "10%",
                    top: "6%",
                    containLabel: true,
                },
                xAxis: [
                    {
                        type: "category",
                        boundaryGap: true,
                        data: ["苹果","华为","三星","OPPO","锤子"],
                        // data: this.chartData.barData.fieldName,
                        nameLocation: "end",
                        axisTick: {
                            show: false,
                        },
                        axisPointer: {
                            type: "shadow",
                        },
                        axisTick: {
                            alignWithLable: true,
                        },
                        splitLine: {
                            show: false,
                        },
                        axisLable: {
                            interval: 0,
                        },
                        axisLine: {
                            show: true,
                            lineStyle: {
                                opacity: 0.3,
                                color: "black",
                            },
                        },
                    },
                ],
                yAxis: 
                    {
                        show: true,
                        name: "",
                        interval: 5,
                        nameTextStyle: {
                            color: "#9B9B9B",
                            fontSize : 20
                        },
                        position: "left",
                        type: "value",
                        axisLable: {
                            show: true,
                            format: "normal",
                            textStyle: {
                                color: "#9B9B9B",
                            },
                        },
                        axisTick: {
                            show: true,
                        },
                        splitLine: {
                            show: true,
                        },
                        axisLine: {
                            show:true,
                            lineStyle: {
                                opacity: 0.3,
                                color: "black",
                            },
                        },
                    },
                    // 展示的数据柱状图
                series: [
                    {
                        name: "已完成分析",
                        type: "bar",
                        barWidth: 50,
                        data: [10,20,30,40,50],
                        yAxisIndex: 0,
                        itemStyle: {
                            color: "#29B2E5",
                            width: 5,
                        },
                        label: {
                            show: "true",
                            format: "normal",
                            
                        },
                        lineStyle: {
                            type: "solid",
                            width: 3,
                        },
                    },
                    {
                        name: "未完成分析",
                        type: "bar",
                        data: [20,20,30,50,25],
                        barWidth: 50,
                        yAxisIndex: 0,
                        itemStyle: {
                            color: "#A4CD32",
                        },
                        label: {
                            show: "true",
                            format: "normal",
                        },
                        lineStyle: {
                            type: "solid",
                            width: 3,
                        },
                    },
                ],
            });
        }
    }
};
</script>

<style scoped>
</style>
