<template>
    <div class="diy-tree-table">
        <div class="headerSty">
            <div class="table-item header-color w55 pdlr10 borderTop">序号</div>
            <div class="table-item header-color pdlr10 borderTop" v-for="(v,i) in headerData" :key="i">{{v}}</div>
        </div>
        <div class="tree-body" v-if="currentSelectData&&currentSelectData.length">
            <div v-for="(v,i) in currentSelectData" :key="i" class="body-item">
                <div class="w55 table-item">
                    <div class="pdlr10 marginAuto">
                        {{i+1}}
                    </div>
                </div>
                <div class="table-item" :ref="`item${i}`">
                    <div class="pdlr10">
                        <el-tooltip :open-delay="1000" effect="dark" :content="v.contractName==='无'?'':v.contractName" placement="top-start">
                            <el-link type="primary" underline @click.native="showPdf(v)"> {{v.contractName==='无'?'':v.contractName}}</el-link>
                        </el-tooltip>
                    </div>
                    <template v-if="v.children&&v.children.length" :list="v.children">
                        <td-item :list="v.children"></td-item>
                    </template>
                </div>
            </div>
        </div>

    </div>
</template>

<script>
import TdItem from './td-item'
export default {
    name: 'tree-table',
    props: {
        treeData: {
            type: Array,
            default: () => []
        }
    },
    data() {
        return {
            headerData: ['主合同', '附属合同', '合同附件', '补充类'],
            childrenData: [
                {
                    children: [
                        {
                            children: [
                                {
                                    children: [
                                        {
                                            children: []
                                        }
                                    ]
                                }
                            ]
                        }
                    ]
                }
            ],
            currentSelectData: []
        }
    },
    components: {
        TdItem
    },

    mounted() {
        this.completionData()
        setTimeout(() => {
            this.$nextTick(() => {
                let arr = Object.keys(this.$refs)
                arr.forEach((v, i) => {
                    this.$refs[`item${i}`][0].style.height =
                        this.$refs[`item${i}`][0].lastChild.offsetHeight + 'px'
                })
                // console.log(this.$refs, 'item', this.$refs.item0)
            })
        })
    },
    methods: {
        showPdf(v) {
            v.fileId && this.exportFun(`/file/reviewPdf/${v.fileId}`)
        },
        // 补全树形结构数据
        completionData() {
            const selectData = this.deepCopy(this.treeData)
            const fillData = (data1, data2) => {
                data1.forEach(v => {
                    if (v.children && v.children.length) {
                        fillData(v.children, data2[0].children)
                    } else {
                        v.children = data2[0].children
                    }
                })
            }
            fillData(selectData, this.childrenData)
            // selectData.forEach(v => {
            //     if (v.children && v.children.length) {
            //         fillData(v.children, this.childrenData)
            //     } else {
            //         v.children = this.childrenData
            //     }
            // })
            this.currentSelectData = [...selectData]
        }
    }
}
</script>

<style lang="scss" scoped>
.diy-tree-table {
    .marginAuto {
        margin: 0 auto;
    }

    box-sizing: border-box !important;
    div {
        box-sizing: border-box !important;
    }
    .headerSty {
        border-left: 1px solid #cbced5;

        display: flex;
        .header-color {
            color: #303133;
            background-color: #f5f6fa;
        }
        .borderTop {
            border-top: 1px solid #cbced5;
        }
    }
    .table-item {
        position: relative;
        min-height: 40px;
        // line-height: 40px;
        color: #606266;
        display: flex;
        align-items: center;
        width: 24%;
        // width: 200px;
        text-align: left;
        border-right: 1px solid #cbced5;
        border-bottom: 1px solid #cbced5;
    }
    .w55 {
        width: 4% !important;
        text-align: center !important;
    }
    .pdlr10 {
        padding-left: 10px;
        padding-right: 10px;
        text-overflow: ellipsis;
        overflow: hidden;
        white-space: nowrap;
    }
    .tree-body {
        border-left: 1px solid #cbced5;
        .body-item {
            display: flex;
        }
    }
}
</style>