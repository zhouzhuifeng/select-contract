<template>
    <div class="contractContainer">
        <el-button type="primary" @click="selectContract">选择合同</el-button>
        <select-contract :visible.sync="visible" v-if="visible" @closeContract="closeContract" :selectData="selectData" @selectSuccess="selectSuccess"></select-contract>
        <div class="marginT20">
            <tree-table :treeData="treeData" v-if="showTreeTabel"></tree-table>
        </div>

    </div>
</template>

<script>
import SelectContract from './components/select-contract'
import TreeTable from './components/tree-table'
export default {
    data() {
        return {
            visible: false,
            treeData: [],
            showTreeTabel: true,
            selectData: []
        }
    },
    components: {
        SelectContract,
        TreeTable
    },
    methods: {
        selectContract() {
            this.visible = true
            this.selectData = this.deepCopy(this.treeData)
        },
        selectSuccess(data) {
            this.closeContract()
            this.showTreeTabel = false
            let time = setTimeout(v => {
                this.showTreeTabel = true
                this.treeData = data
                clearTimeout(time)
            }, 0)
        },
        closeContract() {
            this.visible = false
        }
    }
}
</script>

<style lang="scss" scoped>
.contractContainer {
    height: 100%;
    box-sizing: border-box;
    padding: 20px;
    background-color: #fff;
    text-align: left;
}
.marginT20 {
    margin-top: 20px;
}
</style>