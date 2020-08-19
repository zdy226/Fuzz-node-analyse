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
                    <el-select v-model="banben_optionsAll">
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
                    <el-select v-model="gongju_optionsAll">
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

            //定义请求参数
            queryParams: {
                emui: "",
                productName: "",
                version: "",
                targetType: "",
                fieldName: "",
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
        }
    },
    created() {
        getAllSelectdata();
    },
    mounted() {},
    methods: {
        getAllSelectdata() {
            getEMUIdataInfo().then((res) => {
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
            this.getProductByVersion(params).then((res) => {
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
            this.getVersionNum(params).then((res) => {
                if (!res.datas.code === 0) {
                    throw new Error(res.datas.message);
                }
                this.banben_options = res.datas.version;
                this.banben_options.splice(0, 0, "所有版本");
                this.banben_optionsAll = this.banben_options[1];
                this.queryParams.version = this.banben_optionsAll[1];
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
                productName:this.queryParams.productName,
                version: value,
            };
            this.initPageBar();
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
                this.queryParams.targetType = this.gongju_optionsAll[1].value;
                this.initPageTable();
            })
             .catch((error) => {
                    this.$Notice.error({
                        title: "错误",
                        desc: `${error}`
                });
            });
        },
    }
};
</script>

<style lang="less" scoped>
</style>
