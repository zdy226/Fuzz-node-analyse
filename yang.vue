<template>
    <div class='container'>
        <el-row>
            <div>
                <el-col :span="4">
                    <label style="height:15px;padding-right:5px;">EMUI</label>
                    <el-select v-model="value">
                        <el-option
                            v-for="item in EMUI_options"
                            :key="item.value"
                            :label="item.label"
                            :value="item.value"
                            :v-model="queryParams.emui"
                        >
                            {{ item }}
                        </el-option>
                    </el-select>
                </el-col>

                <el-col :span="4">
                    <label style="height:15px;padding-right:5px;">产品</label>
                    <el-select v-model="value">
                        <el-option
                            v-for="item in chanpin_options"
                            :key="item.value"
                            :label="item.label"
                            :value="item.value"
                        >
                        </el-option>
                    </el-select>
                </el-col>

                <el-col :span="4">
                    <label style="height:15px;padding-right:5px;">版本</label>
                    <el-select v-model="value">
                        <el-option
                            v-for="item in banben_options"
                            :key="item.value"
                            :label="item.label"
                            :value="item.value"
                        >
                        </el-option>
                    </el-select>
                </el-col>

                <el-col :span="4">
                    <label style="height:15px;padding-right:5px;">节点分层</label>
                    <el-select v-model="value">
                        <el-option
                            v-for="item in jiedian_options"
                            :key="item.value"
                            :label="item.label"
                            :value="item.value"
                        >
                        </el-option>
                    </el-select>
                </el-col>

                <el-col :span="4">
                    <label style="height:15px;padding-right:5px;">领域</label>
                    <el-select v-model="value">
                        <el-option
                            v-for="item in lingyu_options"
                            :key="item.value"
                            :label="item.label"
                            :value="item.value"
                        >
                        </el-option>
                    </el-select>
                </el-col>

                <el-col :span="4">
                    <label style="height:15px;padding-right:5px;">工具</label>
                    <el-select v-model="value">
                        <el-option
                            v-for="item in gongju_options"
                            :key="item.value"
                            :label="item.label"
                            :value="item.value"
                        >
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
            banben_options: [],
            jiedian_options: [],
            lingyu_options: [],
            gongju_options: [],
        }
    },
    computed: {},
    watch:{},
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
                this.lingyu_options.splice(0, 0, "全部领域");
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