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
            <el-tabs type="border-card" v-model="tabs_name">
                <el-tab-pane
                    :name="item"
                    v-for="(item,index) in gongju_optionsAll"
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
                <el-tab-pane label="领域测试">
                    <div style="margin-top:30px;">
                        <div style="margin-bottom:10px">
                                <el-row>
                                    <el-col :span="20">
                                        <span style="font-size:20px" class="bjh_h3">
                                            应用表
                                        </span>
                                    </el-col>
                                    <el-col :span="4">
                                        <el-button 
                                        size="small"
                                        @click="exportData"
                                        >
                                            导出表格
                                        </el-button>
                                        <el-button
                                        size="small"
                                        icon="el-icon-s-tools"
                                        @click="sheding"
                                        >
                                            列表设定
                                        </el-button>
                                    </el-col>
                                </el-row>
                        </div>
                        <el-table
                                ref="filterTable"
                                :data="ApplicationTableData"
                                border
                                stripe
                                style="width: 100%;"
                                :header-cell-style="headClass"
                                :cell-style="rowClass">
                            <el-table-column
                                prop="target_name"
                                label="包名"
                                width="180">
                                <template slot-scope="scope">
                                    <router-link to="/ee" style="color:black">{{ scope.row.target_name}}</router-link>
                                </template>
                            </el-table-column>
                            <el-table-column
                                prop="domain"
                                label="所属领域"
                                width="180">
                            </el-table-column>
                            <el-table-column
                                prop="app_version"
                                label="应用版本">
                            </el-table-column>
                            <el-table-column
                                :filters="[{text: '影视剧', value: '影视剧'}, {text: '影视剧1', value: '影视剧1'}, {text: '影视剧2', value: '影视剧2'}, {text: '影视剧3', value: '影视剧3'}]"
                                :filter-method="filterHandler"
                                prop="app_type"
                                label="应用类型">
                            </el-table-column>
                            <el-table-column
                                prop="tested"
                                label="是否测试">
                            </el-table-column>
                            <el-table-column
                                prop="success_components"
                                label="测试完成组件/组件">
                            </el-table-column>
                            <el-table-column
                                prop="problems"
                                label="发现问题数">
                            </el-table-column>
                            <el-table-column
                                prop="subnums"
                                label="提单数">
                            </el-table-column>
                        </el-table>
                        <!-- 分页 -->
                        <div class="block" style="margin-top:20px;width: 100%;" >
                            <span class="demonstration">显示第{{ reqParams.page }}-{{ reqParams.per_page }}条记录，共{{total}}条。每一页{{reqParams. per_page}}条</span>
                            <el-pagination
                                class="pager"
                                background
                                style="display:inline-block;text-align: right;margin-left: 960px;"
                                layout="sizes,prev, pager, next,total"
                                :current-page="reqParams.page"
                                :page-sizes="[10, 20, 30, 40]"
                                @size-change="handleSizeChange"
                                :page-size="reqParams.per_page"
                                @current-change="changePager"
                                :total="100">
                            </el-pagination>
                        </div>
                    </div>
                    <div style="margin-top:30px;">
                        <div style="margin-bottom:10px">
                            <el-row>
                                <el-col :span="20">
                                    <div style="font-size:20px">
                                        应用表
                                    </div>
                                </el-col>
                                <el-col :span="4">
                                    <el-button 
                                    size="small"
                                    @click="exportData"
                                    >
                                        导出表格
                                    </el-button>
                                    <el-button
                                    size="small"
                                    icon="el-icon-s-tools"
                                    @click="sheding"
                                    >
                                        列表设定
                                    </el-button>
                                </el-col>
                            </el-row>
                        </div>
                        <el-table
                            :data="ApplicationTableData"
                            border
                            stripe
                            style="width: 100%;"
                            :header-cell-style="headClass"
                            :cell-style="rowClass">
                            <!-- prop 指定列显示的数据 (通过字段名指定) 
                            label  指定列数据的描述-->
                            <el-table-column
                                prop="target_name"
                                label="包名"
                                width="180">
                                <template slot-scope="scope">
                                    <router-link to="/ee" style="color:black">{{ scope.row.target_name}}</router-link>
                                </template>
                            </el-table-column>
                            <el-table-column
                                prop="domain"
                                label="所属领域"
                                width="180">
                            </el-table-column>
                            <el-table-column
                                prop="app_version"
                                label="应用版本">
                            </el-table-column>
                            <el-table-column
                                prop="app_type"
                                label="应用类型">
                            </el-table-column>
                            <el-table-column
                                prop="tested"
                                label="是否测试">
                            </el-table-column>
                            <el-table-column
                                prop="success_components"
                                label="测试完成组件/组件">
                            </el-table-column>
                            <el-table-column
                                prop="problems"
                                label="发现问题数">
                            </el-table-column>
                            <el-table-column
                                prop="subnums"
                                label="提单数">
                            </el-table-column>
                        </el-table>
                        <!-- 分页 -->
                        <div class="block" style="margin-top:20px;width: 100%;" >
                            <span class="demonstration">显示第{{ reqParams.page }}-{{ reqParams.per_page }}条记录，共{{total}}条。每一页{{reqParams. per_page}}条</span>
                            <el-pagination
                                class="pager"
                                background
                                style="display:inline-block;text-align: right;margin-left: 960px;"
                                layout="sizes,prev, pager, next,total"
                                :current-page="reqParams.page"
                                :page-sizes="[10, 20, 30, 40]"
                                @size-change="handleSizeChange"
                                :page-size="reqParams.per_page"
                                @current-change="changePager"
                                :total="100">
                            </el-pagination>
                        </div>
                    </div>
                    <div style="margin-top:30px;">
                        <div style="margin-bottom:10px">
                            <el-row >
                                <el-col :span="20">
                                    <div style="font-size:20px">
                                        应用表
                                    </div>
                                </el-col>
                                <el-col :span="4">
                                    <el-button 
                                        size="small"
                                        @click="exportData"
                                        >
                                            导出表格
                                    </el-button>
                                    <el-button
                                        size="small"
                                        icon="el-icon-s-tools"
                                        @click="sheding"
                                        >
                                            列表设定
                                    </el-button>
                                </el-col>
                            </el-row>
                        </div>
                        <el-table
                                :data="ApplicationTableData"
                                border
                                stripe
                                style="width: 100%;"
                                :header-cell-style="headClass"
                                :cell-style="rowClass"
                                >
                            <!-- prop 指定列显示的数据 (通过字段名指定) 
                            label  指定列数据的描述-->
                            <el-table-column
                                prop="target_name"
                                label="包名"
                                width="180">
                                <template slot-scope="scope">
                                    <router-link to="/ee" style="color:black">{{ scope.row.target_name}}</router-link>
                                </template>
                            </el-table-column>
                            <el-table-column
                                prop="domain"
                                label="所属领域"
                                width="180">
                            </el-table-column>
                            <el-table-column
                                prop="app_version"
                                label="应用版本">
                            </el-table-column>
                            <el-table-column
                                prop="app_type"
                                label="应用类型">
                            </el-table-column>
                            <el-table-column
                                prop="tested"
                                label="是否测试">
                            </el-table-column>
                            <el-table-column
                                prop="success_components"
                                label="测试完成组件/组件">
                            </el-table-column>
                            <el-table-column
                                prop="problems"
                                label="发现问题数">
                            </el-table-column>
                            <el-table-column
                                prop="subnums"
                                label="提单数">
                            </el-table-column>
                        </el-table>
                        <!-- 分页 -->
                        <div class="block" style="margin-top:20px;width: 100%;" >
                            <span class="demonstration">显示第{{ reqParams.page }}-{{ reqParams.per_page }}条记录，共{{total}}条。每一页{{reqParams. per_page}}条</span>
                            <el-pagination
                                class="pager"
                                background
                                style="display:inline-block;text-align: right;margin-left: 960px;"
                                layout="sizes,prev, pager, next,total"
                                :current-page="reqParams.page"
                                :page-sizes="[10, 20, 30, 40]"
                                @size-change="handleSizeChange"
                                :page-size="reqParams.per_page"
                                @current-change="changePager"
                                :total="100">
                            </el-pagination>
                        </div>
                    </div>
                </el-tab-pane>
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
                exportData:[],
            },
            page: 1,
            pageSize: 10,
            dataCount: 0,
            tabs_name: "",
            // 表格 列表的数据
            ApplicationTableData: [
                {
                target_name: 'www.baidudu.com',
                domain: '啊哈哈',
                app_version: '11.0.0',
                app_type: "影视剧1",
                tested: "是",
                success_components: "10/20",
                problems: "2",
                subnums: "2",
                },  
                {
                target_name: 'www.baidudu.com',
                domain: '啊哈哈',
                app_version: '11.0.0',
                app_type: "影视剧2",
                tested: "是",
                success_components: "10/20",
                problems: "2",
                subnums: "2",
                }, 
                {
                target_name: 'www.baidudu.com',
                domain: '啊哈哈',
                app_version: '11.0.0',
                app_type: "影视剧2",
                tested: "是",
                success_components: "10/20",
                problems: "2",
                subnums: "2",
                }, 
                {
                target_name: 'www.baidudu.com',
                domain: '啊哈哈',
                app_version: '11.0.0',
                app_type: "影视剧3",
                tested: "是",
                success_components: "10/20",
                problems: "2",
                subnums: "2",
                }, 
                {
                target_name: 'www.baidudu.com',
                domain: '啊哈哈',
                app_version: '11.0.0',
                app_type: "影视剧4",
                tested: "是",
                success_components: "10/20",
                problems: "2",
                subnums: "2",
                }, 
                {
                target_name: 'www.baidudu.com',
                domain: '啊哈哈',
                app_version: '11.0.0',
                app_type: "影视剧1",
                tested: "是",
                success_components: "10/20",
                problems: "2",
                subnums: "2",
                }, 
                {
                target_name: 'www.baidudu.com',
                domain: '啊哈哈',
                app_version: '11.0.0',
                app_type: "影视剧2",
                tested: "是",
                success_components: "10/20",
                problems: "2",
                subnums: "2",
                }, 
                {
                target_name: 'www.baidudu.com',
                domain: '啊哈哈',
                app_version: '11.0.0',
                app_type: "影视剧3",
                tested: "是",
                success_components: "10/20",
                problems: "2",
                subnums: "2",
                }, 
                {
                target_name: 'www.baidudu.com',
                domain: '啊哈哈',
                app_version: '11.0.0',
                app_type: "影视剧2",
                tested: "是",
                success_components: "10/20",
                problems: "2",
                subnums: "2",
                }, 
                {
                target_name: 'www.baidudu.com',
                domain: '啊哈哈',
                app_version: '11.0.0',
                app_type: "影视剧1",
                tested: "是",
                success_components: "10/20",
                problems: "2",
                subnums: "2",
                }, 
                {
                target_name: 'www.baidudu.com',
                domain: '啊哈哈',
                app_version: '11.0.0',
                app_type: "影视剧2",
                tested: "是",
                success_components: "10/20",
                problems: "2",
                subnums: "2",
                }, 
                {
                target_name: 'www.baidudu.com',
                domain: '啊哈哈',
                app_version: '11.0.0',
                app_type: "影视剧3",
                tested: "是",
                success_components: "10/20",
                problems: "2",
                subnums: "2",
                }, 
                {
                target_name: 'www.baidudu.com',
                domain: '啊哈哈',
                app_version: '11.0.0',
                app_type: "影视剧2",
                tested: "是",
                success_components: "10/20",
                problems: "2",
                subnums: "2",
                }, 
                {
                target_name: 'www.baidudu.com',
                domain: '啊哈哈',
                app_version: '11.0.0',
                app_type: "影视剧1",
                tested: "是",
                success_components: "10/20",
                problems: "2",
                subnums: "2",
                }, 
                {
                target_name: 'www.baidudu.com',
                domain: '啊哈哈',
                app_version: '11.0.0',
                app_type: "影视剧",
                tested: "是",
                success_components: "10/20",
                problems: "2",
                subnums: "2",
                }, 
                {
                target_name: 'www.baidudu.com',
                domain: '啊哈哈',
                app_version: '11.0.0',
                app_type: "影视剧",
                tested: "是",
                success_components: "10/20",
                problems: "2",
                subnums: "2",
                }, 
            ],
            
            //  给后台传的分页的参数
             // 总条数
            total: 0, 
            reqParams:{
                page: 1, // 默认给后台传递的是第一页的数据
                per_page: 10,  // 每页显示多少条的数据
            },
            currentPage2: 5,

        
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

        headClass() { //表头居中显示
            return "text-align:center"
        },
        rowClass() { //表格数据居中显示
            return "text-align:center"
        },

        // 应用类型进行筛选
        filterHandler(value, row, column) {
            const property = column['property'];
            return row[property] === value;
        },

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

        initPageTable() {
            if (this.chanpin_optionsAll == "所有产品") {
                this.queryParams.productName="";
            } else {
                this.queryParams.productName = this.chanpin_optionsAll;
            }
            if (this.banben_optionsAll == "所有版本") {
                this.queryParams.version="";
            } else {
                this.queryParams.version = this.banben_optionsAll;
            }
            if (this.lingyu_optionsAll == "所有领域") {
                this.queryParams.fieldName="";
            } else {
                this.queryParams.fieldName = this.lingyu_optionsAll;
            }
            if (this.gongju_optionsAll == "所有工具") {
                this.queryParams.targetType="";
            } else {
                this.queryParams.targetType = this.gongju_optionsAll;
            }
            getFuzzPonitTable(this.queryParams).then((res) => {
                if (!res.datas.code ===0) {
                    throw new Error(res.datas.message);
                }
                this.total = res.datas.tableData.count;
                this.fuzzTableData = res.datas.tableData.list;
            })
            .catch((error) => {
                this.$Notice.error({
                    title: "错误",
                    desc: `${error}`,
                })
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

        // 切换分页; 改变分页 触发事件改变当前页码
        changePager (newPage) {
            console.log(newPage)
            console.log(`当前页: ${newPage}`);
            //把提交后台的参数改成新的页码
            this.reqParams.page = newPage
            // 调用此页面数据的接口,把新的数据提交给后台
            this.ApplicationTableData()
        },

        handleSizeChange(val) {
            console.log(`每页 ${val} 条`);
        },
       
        ApplicationTableData () {
            // 获取表格列表数据
            getApplicationTableData(reqParams).then((res) => {
                if (!res.datas.code === 0) {
                    throw new Error(res.datas.message);
                    }
                    // 表格列表数据
                    this.ApplicationTableData= data.results
                    // 总条数
                    this.total = data.total_count
                    }).catch((error) => {
                        this.$Notice.error({
                            title: "错误",
                            desc: `${error}`
                            });
                            });
        },

        exportData () {
            let queryConditions = {

            };
            getFuzzTableData(queryConditions).then((res) => {
                if (!res.datas.code === 0) {
                    throw new Error("导出数据异常！");
                }
                this.downLoad(res.datas.fuzzTableDtsLevelData);
            })
            .catch(error => {
                this.$Notice.error({
                    title: "错误",
                    desc: `${error}`
                });
            });
        },
        changepage(index) {
            this.page = index;
            this.initPageTable();
        },
        downLoad(exceldata) {
            require.ensure([], () => {
                const {export_json_to_excel} = require("../../js/Export2Excel");
                const tHeader = [
                    "包名",
                    "所属领域",
                    "应用版本",
                    "应用类型",
                    "是否测试",
                    "测试完成组件/组件",
                    "发现问题数",
                    "提单数",
                ];
                //对应表格输出的title
                const filterVal = [
                    "target_name",
                    "domain",
                    "app_version",
                    "app_type",
                    "tested",
                    "success_components",
                    "problems",
                    "subnums",
                ];
                //对应表格输出的数据
                const list = exceldata;
                const data = this.format.Json(filterVal, list);
                data.map(item => {
                    item.map((i, index) => {
                        if (!i) {
                            item[index] = "";
                        }
                    });
                });
                export_json_to_excel({
                    tHeader,
                    data,
                    fielname: "应用表数据",
                    autoWith: false,
                    bookType: "xlsx",
                    background: false
                });
            });
        },
        formatJson(filterVal, jsonData) {
            return jsonData.map(v => filterVal.map(j => v[j]))
        },
    }
};
</script>

<style scoped>
/* .bjh_h3 {
    font-size: 18px;
    font-weight: 700;
    color: #000;
    position: relative;
    padding-left: 9px;
};
.bjh-h3:before {
    content: "";
    display: inline-block;
    height: 18px;
    width: 5px;
    background-color: #3c76ff;
    position: absolute;
    top: 0;
    left: 0;
}; */

</style>
