<template>
    <div class="isNextItem" :class="[`item-${currentBorderWidth}`]">
        <div v-for="(v,i) in list" :key="i" class="table-item" :ref="`item${i}`">
            <div class="pdlr10">
                <el-tooltip :open-delay="1000" effect="dark" :content="v.contractName==='无'?'':v.contractName" placement="top-start">
                    <el-link type="primary" underline @click.native="showPdf(v)"> {{v.contractName==='无'?'':v.contractName}}</el-link>
                </el-tooltip>
            </div>
            <template v-if="v.children&&v.children.length" :list="v.children">
                <td-item :list="v.children" :borderWidth="currentBorderWidth"></td-item>
            </template>
        </div>
    </div>
</template>

<script>
export default {
    name: 'td-item',
    props: {
        list: {
            type: Array,
            default: () => []
        },
        borderWidth: {
            type: Number,
            default: 1
        }
    },

    data() {
        return {
            currentBorderWidth: 0
        }
    },
    created() {
        let num = this.borderWidth
        this.currentBorderWidth = ++num
    },

    mounted() {
        this.$nextTick(() => {
            // this.$refs.item.style.height= this.$refs.item.firstChild.offsetHeight+'px'
            let arr = Object.keys(this.$refs)
            arr.forEach((v, i) => {
                this.$refs[`item${i}`][0].style.height =
                    (this.$refs[`item${i}`][0].offsetHeight >
                    this.$refs[`item${i}`][0].lastChild.offsetHeight
                        ? this.$refs[`item${i}`][0].offsetHeight
                        : this.$refs[`item${i}`][0].lastChild.offsetHeight) +
                    'px'
            })
        })
    },
    methods: {
        showPdf(v) {
            v.fileId && this.exportFun(`/file/reviewPdf/${v.fileId}`)
        }
    }
}
</script>

<style lang="scss" scoped>
.isNextItem {
    box-sizing: border-box !important;
    .pdlr10 {
        padding-left: 10px;
        padding-right: 10px;
        text-overflow: ellipsis;
        overflow: hidden;
        white-space: nowrap;
    }
    div {
        box-sizing: border-box !important;
    }
    position: absolute;
    top: 0;
    left: 100%;
    width: 100%;
    // margin-left: 200px;
    margin-top: 0;
    .table-item {
        position: relative;
        display: flex;
        align-items: center;
        min-height: 40px;
        color: #606266;
        width: 100%;
        text-align: left;

        border-right: 1px solid #cbced5;
        border-bottom: 1px solid #cbced5;
    }

    .w55 {
        text-align: center !important;
    }

    .tree-body {
        border-left: 1px solid #cbced5;
        .body-item {
            display: flex;
        }
    }
}
.h40 {
    height: 40px;
    position: absolute;
    width: 100%;
    border-right: 1px solid #cbced5;
    top: -40px;
    left: 1px;
}
.item-2 {
    width: calc(100% + 2px);
}
.item-3 {
    width: calc(100% + 1px);
}

.item-4 {
    width: calc(100% + 1px);
}
</style>