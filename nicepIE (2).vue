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
                    <el-select multiple v-model="gongju_optionsAll">
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
        <div style="margin-top:20px;">
            <el-tabs type="border-card" name="tabs_name" v-model="tabs_name">
                <el-tab-pane
                    :name="item"
                    v-for="{item,index} in gongju_optionsAll"
                    :key="index"
                    :label="item"
                >
                    <div id="MyechartsOne" style="height:400px;margin-top:20px;"></div>
                </el-tab-pane>
            </el-tabs>
        </div>
        <div id="MyechartsOne" style="height:400px;margin-top:20px;"></div>
        <div id="MyechartsTwo" style="height:400px;margin-top:20px;"></div>
        <div style="margin-top:20px;">
            <div style="text-align:center;font-size:20px;font-weight:900;"> 问题类型分布图</div>
            <el-row>
                <el-col :span="12">
                    <div id="PieChartsOne" style="height:350px;"></div>
                </el-col>
                <el-col :span="12">
                    <div id="PieChartsTwo" style="height:350px;"></div>
                </el-col>
            </el-row>
        </div>
        <div style="margin-top:20px;">
            <el-tabs type="border-card">
                <el-tab-pane label="领域测试">aaa</el-tab-pane>
                <el-tab-pane label="合规抽测">bbb</el-tab-pane>
            </el-tabs>
        </div>
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
            gongju_options: ['a','b','c','d'],
            gongju_optionsAll: [],
            chartData: {
                barData: {
                    tools:[],
                    toolsCount:[],
                    fieldName: [],
                },
                pieData: [],
            },
            tabs_name: "",
            

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
    computed: {
        // tabs_name() {
        //     return this.gongju_optionsAll[0]
        // },
    },
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
            this.initBar();
            this.initPie();
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

        initPagePie(){
            const pararms = {
                emui: this.queryParams.emui,
                productName: this.queryParams.productName,
                version: this.queryParams.version  
            };
            //查询工具数量(饼状图)
            getFuzzPointPieData(params).then((res) => {
                if (!res.datas.code === 0) {
                    throw new Error(res.datas.message);
                }
                this.chartData.pieData = res.datas.dataCount;
                this.drawLine();
            })
            .catch((error) => {
                    this.$Notice.error({
                        title: "错误",
                        desc: `${error}`
                });
            });
        },

        drawLine() {
            //基于准备好的dom，初始化echarts实例
            let MyechartsOne = this.$echarts.init(document.getElementById("MyechartsOne"));
            let PieChartsOne = this.$echarts.init(document.getElementById("PieChartsOne"));
            //绘制图表
            window.addEventListener("resize", () => {
                MyechartsOne.resize();
                PieChartsOne.resize();
            });

            MyechartsOne.setOption({
                title:{
                    text:'各领域应用统计',  
                    top:'top',
                },
                tooltip: {
                    trigger: "axis",
                },
                legend: {
                    data: ["工具已识别节点", "已测试节点"],
                    // data: this.chartData.barData.gongju_optionsAll,
                     x: 'center',
                     y: 'bottom', 
                },
                grid: {
                    left: "5%",
                    right: "5%",
                    bottom: "10%",
                    top: "10%",
                    containLabel: true,
                },
                xAxis: [
                    {
                        type: "category",
                        boundaryGap: true,
                        // data: ["苹果","华为","三星","OPPO","锤子"],
                        data: ["智慧化","突进社交","os","系统应用","应用平台","互联互通"],
                        // data: this.chartData.barData.fieldName,
                        nameLocation: "end",
                        axisTick: {
                            show: false,
                        },
                        axisPointer: {
                            // type: "shadow",
                        },
                        axisTick: {
                            alignWithLable: true,
                        },
                        splitLine: {
                            show: false,
                        },
                        axisLabel: {
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
                        axisLabel: {
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
                        barWidth: 30,
                        data: [10,20,30,40,50,25],
                        yAxisIndex: 0,
                        itemStyle: {
                            color: "#29B2E5",
                            width: 5,
                        },
                        label: {
                            show: true,
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
                        barWidth: 30,
                        yAxisIndex: 0,
                        itemStyle: {
                            color: "#A4CD32",
                            width: 5,
                            
                        },
                        label: {
                            show: true,
                            format: "normal",
                        },
                        lineStyle: {
                            type: "solid",
                            width: 3,
                        },
                    },
                ],
            });

            PieChartsOne.setOption({
                backgroundColor: "rgba(0,0,0,0)",
                // title:{
                //     text:'问题类型分布图',  
                //     left:'center',
                // },
                tooltip: {
                    trigger: "item",
                },
                legend: {
                    data: [
                        "java.lang.OutOfMemoryError",
                        "NullPoniterException",
                        "BadParcelableException",
                        "SecurityException"
                        ],
                    orient:'vertical',
                     x: 'left',
                     y: 'center', 
                },
                // 展示的数据饼状图
                series: [
                    {
                        name: "",
                        type: "pie",
                        radius: "50%",
                        center: ["50%","50%"],
                        selectedMode: "single",
                        minAngle: 5,
                        itemStyle: {
                            normal:{
                                label: {
                                    show: true,
                                    formatter: "{b} : {c} ({d}%)",
                                },
                                labelLine: {
                                    show: true,
                                },
                                color: function(params){
                                    let colorList = [
                                        '#29B2E5',
                                        '#A4CD32',
                                        '#FD9B20',
                                        '#F366B3',
                                        '#5C6BC0',
                                        '#C4CCD3',
                                        '#4BABDE',
                                        '#FFDE76',
                                        ];
                                    return colorList[params.dataIndex]
                                },
                            }
                        },
                        data: [
                                {
                                    "name":"java.lang.OutOfMemoryError",
                                    "value": 10 
                                },
                                {
                                    "name":"NullPoniterException",
                                    "value": 1
                                },
                                {
                                    "name":"BadParcelableException",
                                    "value": 2
                                },
                                {
                                    "name":"SecurityException",
                                    "value": 5
                                },
                            ],
                    },
                ],
            });
        },

        initBar() {
            //基于准备好的dom，初始化echarts实例
            let MyechartsTwo = this.$echarts.init(document.getElementById("MyechartsTwo"));
            //绘制图表
            window.addEventListener("resize", () => {
                MyechartsTwo.resize();
            });
            MyechartsTwo.setOption({
                title:{
                    text:'领域提单情况',
                    top: 'top',
                },
                tooltip: {
                    trigger: "axis",
                },
                legend: {
                    data: ["提单数"],
                     x: 'center',
                     y: 'bottom', 
                },
                grid: {
                    left: "5%",
                    right: "5%",
                    bottom: "10%",
                    top: "10%",
                    containLabel: true,
                },
                xAxis: [
                    {
                        type: "category",
                        boundaryGap: true,
                        data: ["系统应用","os","应用平台","智慧化","互联互通","云服务"],
                        nameLocation: "end",
                        axisTick: {
                            show: false,
                        },
                        splitLine: {
                            show: false,
                        },
                        axisLabel: {
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
                        axisLabel: {
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
                        name: "提单数",
                        type: "bar",
                        barWidth: 60,
                        data: [10,2,11,20,5,7],
                        yAxisIndex: 0,
                        itemStyle: {
                            color: "#29B2E5",
                            width: 5,
                        },
                        label: {
                            show: true,
                            format: "normal",
                            
                        },
                        lineStyle: {
                            type: "solid",
                            width: 3,
                        },
                    }
                ]
            });
        },

        initPie() {
            let PieChartsTwo = this.$echarts.init(document.getElementById("PieChartsTwo"));
            window.addEventListener("resize", () => {
                PieChartsTwo.resize();
            });
            PieChartsTwo.setOption({
                backgroundColor: "rgba(0,0,0,0)",
                // title:{
                //     text:'问题类型分布图',  
                //     left:'center',
                // },
                tooltip: {
                    trigger: "item",
                },
                legend: {
                    data: [
                        "引用空指针",
                        "为正确清理特定内容",
                        "缓冲区溢出",
                        "整数溢出或回绕",
                        "其他",
                        ],
                    orient:'vertical',
                     x: 'left',
                     y: 'center', 
                },
                // 展示的数据饼状图
                series: [
                    {
                        name: "",
                        type: "pie",
                        radius: "50%",
                        center: ["50%","50%"],
                        selectedMode: "single",
                        minAngle: 5,
                        itemStyle: {
                            normal:{
                                label: {
                                    show: true,
                                    formatter: "{b} : {c} ({d}%)",
                                },
                                labelLine: {
                                    show: true,
                                },
                                color: function(params){
                                    let colorList = [
                                        '#29B2E5',
                                        '#A4CD32',
                                        '#FD9B20',
                                        '#F366B3',
                                        '#5C6BC0',
                                        '#C4CCD3',
                                        '#4BABDE',
                                        '#FFDE76',
                                        ];
                                    return colorList[params.dataIndex]
                                },
                            }
                        },
                        data: [
                                {
                                    "name":"引用空指针",
                                    "value": 10 
                                },
                                {
                                    "name":"为正确清理特定内容",
                                    "value": 15 
                                },
                                {
                                    "name":"缓冲区溢出",
                                    "value": 3
                                },
                                {
                                    "name":"整数溢出或回绕",
                                    "value": 2
                                },
                                {
                                    "name":"其他",
                                    "value": 14
                                },
                        ],
                    },
                ],
            });

        },
    }
};
</script>

<style scoped>
</style>
