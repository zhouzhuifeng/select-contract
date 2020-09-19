<template>
    <el-dialog :visible="visible" width="1300px" title="选择合同" class="selectContract" append-to-body size="medium" :close-on-click-modal="false" @close="close">
        <div>
            <div class="diy-cascader-panel">
                <div class="cascader-container">
                    <div>
                        <div class="cascader-menu">
                            <div class="scrollbar__wrap" :style="{height:currentHeight+'px'}">
                                <ul class="cascader-menu__list">
                                    <div v-for="(v,i) in treeData" :key="i">
                                        <li class="cascader-node">
                                            <el-checkbox v-model="v.checked" style="position:relative" @change="checkboxChange($event,v,i)"></el-checkbox>
                                            <div class="label-value" @click="labelClick(v,i)">
                                                <el-tooltip :open-delay="1000" effect="dark" :content="v.contractName" placement="top-start">
                                                    <span>
                                                        {{v.contractName}}
                                                    </span>
                                                </el-tooltip>
                                            </div>
                                            <i class="el-icon-arrow-right el-cascader-node__postfix" @click="labelClick(v,i)" v-if=" v.children&& v.children.length"></i>
                                        </li>
                                        <div class="nextItem" v-if="v.checked&&i===v.activedIndex">
                                            <div class="cascader-menu">
                                                <div class="scrollbar__wrap" :style="{height:currentHeight+'px'}">
                                                    <ul class="cascader-menu__list">
                                                        <div v-for="(v2,i2) in v.children" :key="i2">
                                                            <li class="cascader-node">
                                                                <el-checkbox v-model="v2.checked" style="position:relative" @change="checkboxChange($event,v2,i2,v)"></el-checkbox>
                                                                <div class="label-value" @click="labelClick(v2,i2,v)">
                                                                    <el-tooltip :open-delay="1000" effect="dark" :content="v2.contractName" placement="top-start">
                                                                        <span>
                                                                            {{v2.contractName}}
                                                                        </span>
                                                                    </el-tooltip>
                                                                </div>
                                                                <i class="el-icon-arrow-right el-cascader-node__postfix" @click="labelClick(v2,i2,v)" v-if=" v2.children&& v2.children.length"></i>
                                                            </li>
                                                            <div class="nextItem" v-if="v2.checked&&i2===v2.activedIndex">
                                                                <div class="cascader-menu">
                                                                    <div class="scrollbar__wrap" :style="{height:currentHeight+'px'}">
                                                                        <ul class="cascader-menu__list">
                                                                            <div v-for="(v3,i3) in v2.children" :key="i3">
                                                                                <li class="cascader-node">
                                                                                    <el-checkbox v-model="v3.checked" style="position:relative" @change="checkboxChange($event,v3,i3,v2)"></el-checkbox>
                                                                                    <div class="label-value" @click="labelClick(v3,i3,v2)">
                                                                                        <el-tooltip :open-delay="1000" effect="dark" :content="v3.contractName" placement="top-start">
                                                                                            <span>
                                                                                                {{v3.contractName}}
                                                                                            </span>
                                                                                        </el-tooltip>
                                                                                    </div>
                                                                                    <i class="el-icon-arrow-right el-cascader-node__postfix" @click="labelClick(v3,i3,v2)" v-if=" v3.children&& v3.children.length"></i>
                                                                                </li>
                                                                                <div class="nextItem" v-if="v3.checked&&i3===v3.activedIndex">
                                                                                    <div class="cascader-menu">
                                                                                        <div class="scrollbar__wrap" :style="{height:currentHeight+'px'}">
                                                                                            <ul class="cascader-menu__list">
                                                                                                <li class="cascader-node" v-for="(v4,i4) in v3.children" :key="i4">
                                                                                                    <el-checkbox v-model="v4.checked" style="position:relative" @change="checkboxChange($event,v4,i4,v3)"></el-checkbox>
                                                                                                    <div class="label-value" @click="labelClick(v4,i4,v3)">
                                                                                                        <el-tooltip :open-delay="1000" effect="dark" :content="v4.contractName" placement="top-start">
                                                                                                            <span>
                                                                                                                {{v4.contractName}}
                                                                                                            </span>
                                                                                                        </el-tooltip>
                                                                                                    </div>
                                                                                                </li>
                                                                                            </ul>
                                                                                        </div>
                                                                                    </div>
                                                                                </div>
                                                                            </div>
                                                                        </ul>
                                                                    </div>
                                                                </div>
                                                            </div>
                                                        </div>
                                                    </ul>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </ul>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <span slot="footer" class="dialog-footer">
            <el-button @click="close" size="medium">取消</el-button>
            <el-button type="primary" @click="save" size="medium">保存</el-button>
        </span>
    </el-dialog>

</template>

<script>
export default {
    props: {
        visible: {
            type: Boolean,
            default: false
        },
        selectData: {
            type: Array,
            default: () => []
        },
        params: {
            type: Object,
            default: () => {
                return {}
            }
        }
    },
    data() {
        return {
            currentHeight: 300,
            treeData: [],
            options: []
        }
    },
    created() {},
    mounted() {
        this.getContractTemplate()
    },

    methods: {
        close() {
            this.$emit('closeContract')
        },
        save() {
            let data = this.getSelectData()
            if (!data.length) {
                return this.$message.error('请选择合同')
            }
            this.$emit('selectSuccess', data)
        },
        // 获取所有合同种类
        getContractTemplate() {
            //主合同
            const mainContractList = this.getContractData('主合同')
            //附属合同
            const auxiliaryContractList = this.getContractData('附属合同')
            //合同附件
            const contractAccessoryList = this.getContractData('合同附件')
            //补充类合同
            const contractReplenishList = this.getContractData('补充类合同')
            this.options = [
                mainContractList,
                auxiliaryContractList,
                contractAccessoryList,
                contractReplenishList
            ]
            // 构建树形数据
            this.createTreeData()
            this.treeData = this.deepCopy(this.options[0])
            // 添加checked activeindex
            this.addCheckItem(this.treeData)
            // 数据回填
            this.setData()
        },
        // 构建合同数据
        getContractData(contractName) {
            const arr = [1, 1, 1, 1, 1, 1, 1]
            return arr.map((v, i) => {
                return {
                    contractName: `${contractName}-${i + 1}`,
                    contractId: i + 1
                }
            })
        },
        // 生成 树形
        createTreeData(max = 2) {
            let next = max + 1
            let pev = max
            const arr = this.options[next]
            this.options[pev].forEach(v => {
                v.children = arr
            })
            max--
            if (max < 0) return
            this.createTreeData(max)
        },
        // 数据回填
        setData() {
            const data1 = this.selectData
            const data2 = this.treeData
            const fn = (data1, data2) => {
                data1.forEach(v => {
                    data2.forEach(item => {
                        if (v.contractId === item.contractId) {
                            item.checked = true
                            if (v.children && v.children.length) {
                                fn(v.children, item.children)
                            }
                        }
                    })
                })
            }
            fn(data1, data2)
            this.treeData = [...this.treeData]
        },
        setHeight() {
            this.$nextTick(() => {
                const namesArr = Array.from(
                    document.querySelectorAll(
                        '.diy-cascader-panel .cascader-menu__list'
                    )
                )
                const arrNum = namesArr.map(v => v.offsetHeight)
                this.currentHeight = Math.max(...arrNum)
            })
        },
        addCheckItem(data) {
            data.forEach((v, i) => {
                v.checked = false
                v.activedIndex = -1
                if (v.children) {
                    this.addCheckItem(v.children)
                }
            })
        },

        checkboxChange(flag, v, i, parentObj) {
            if (!flag) {
                // 去掉该节点下所有子的checkbox
                this.isClearFn(v, i, parentObj, 'checked')
            }
            this.isClearFn(v, i, parentObj, 'activedIndex')
            // console.log(v.checked, v.activedIndex, 'v.checked')
        },
        // 节点点击事件
        labelClick(v, i, parentObj) {
            // 清除该层下的所有activedIndex
            this.isClearFn(v, i, parentObj, 'activedIndex')
            // console.log(this.currentIndex, 'activedIndex')
        },
        isClearFn(v, i, parentObj, clearItem) {
            this.clearActivedIndex(v, i, parentObj, clearItem)
            v.activedIndex = i
            this.treeData = [...this.treeData]
            this.setHeight()
        },
        // 清除该层下的所有activedIndex
        clearActivedIndex(v, i, parentObj, clearItem) {
            // 清除索引或者checked 如果是清除checked只用清除该checked下的子集合，不能清除兄弟的集合
            let itemValue = clearItem === 'activedIndex' ? -1 : false
            let data
            if (clearItem === 'checked') {
                data = v.children
            } else {
                data = parentObj ? parentObj.children : this.treeData
            }
            const fn = (data = []) => {
                data.forEach(v => {
                    v[clearItem] = itemValue
                    if (v.children && v.children.length) {
                        fn(v.children, clearItem)
                    }
                })
            }
            fn(data)
        },
        // 获取勾选的数据
        getSelectData() {
            const data = this.deepCopy(this.treeData)
            const fn = data => {
                for (let i = 0; i < data.length; i++) {
                    if (!data[i].checked) {
                        data.splice(i, 1)
                        i--
                    } else {
                        if (data[i].children && data[i].children.length) {
                            fn(data[i].children)
                        }
                    }
                }
            }
            fn(data)
            return data
        }
    }
}
</script>

<style lang="scss" scoped>

.nextItem {
    position: absolute;
    border: 1px solid #e4e7ed;
    left: 100%;
    top: -1px;
}
.diy-cascader-panel {
    display: flex;
    .cascader-container {
        border: 1px solid #e4e7ed;
        // border-radius: 4px 0 0 4px;
        display: flex;
        font-size: 14px;
        .cascader-menu {
            width: 314px;
            max-width: 314px;
            box-sizing: border-box;
            color: #606266;
            border-right: 1px solid #e4e7ed;
            // overflow: hidden;
            position: relative;
            &:last-child {
                border-right: none;
            }
            .scrollbar__wrap {
                // margin-bottom: -17px;
                // margin-right: -17px;
                height: 300px;
                // overflow: scroll;
            }
        }
        .notBorder {
            border-right-width: 0px;
        }
        .cascader-menu__list {
            position: relative;
            min-height: 100%;
            margin: 0;
            padding: 6px 0;
            list-style: none;
            box-sizing: border-box;
            .cascader-node {
                position: relative;
                display: flex;
                align-items: center;
                padding: 0 30px 0 20px;
                height: 34px;
                line-height: 34px;
                outline: none;
                .label-value {
                    text-overflow: ellipsis;
                    overflow: hidden;
                    width: 100%;
                    padding-left: 10px;
                    line-height: 35px;
                    height: 34px;
                    font-size: 14px;
                    white-space: nowrap;
                }
                &:hover {
                    cursor: pointer;
                    background: #f5f7fa;
                }
                &.isActived {
                    color: #409eff;
                    font-weight: 700;
                }
                .el-icon-arrow-right {
                    width: 30px;
                    text-align: center;
                    line-height: 34px;
                    right: 0px;
                    top: 0px;
                }
            }
        }
    }
}
</style>