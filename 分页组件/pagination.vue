<template>
    <div class="page-bar">
        <ul class="pagination">
            <li v-if="cur>1"><a v-on:click="cur--,pageClick()">上一页</a></li>
            <li v-if="cur==1"><a class="banclick">上一页</a></li>
            <li v-for="index in indexs"  v-bind:class="{ 'active': cur == index}">
                <a v-on:click="btnClick(index);pageClick()">{{ index }}</a>
            </li>
            <li v-if="cur!=all"><a v-on:click="cur++,pageClick()">下一页</a></li>
            <li v-if="cur == all"><a class="banclick">下一页</a></li>
        </ul>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                cur:this.currentPage,
                all:this.pageCount,
                indexs:1
            }
        },
        props: {
            pageCount: Number,
            currentPage: Number
        },
        watch: {
            pageCount:function(newValue,oldValue){
                this.all = newValue;
                this.pageComputed();
            }
        },
        methods: {
            btnClick: function (data) { //页码点击事件
                if (data != this.cur) {
                    this.cur = data;
                }
            },
            pageClick: function () {
                this.$emit('onChange',this.cur);
                // console.log('现在在' + this.cur + '页');
            },
            pageComputed: function () {
                var left = 1;
                var right = this.all;
                var ar = [];
                if (this.all >= 5) {
                    if (this.cur > 3 && this.cur < this.all - 2) {
                        left = this.cur - 2
                        right = this.cur + 2
                    } else {
                        if (this.cur <= 3) {
                            left = 1
                            right = 5
                        } else {
                            right = this.all
                            left = this.all - 4
                        }
                    }
                }
                while (left <= right) {
                    ar.push(left)
                    left++
                }
                this.indexs = ar
            }
        },
        computed: {
            

        }
    }
</script>

<style lang="scss" scoped>
.pagination {
    display: inline-block;
    -webkit-user-select: none;
    -moz-user-select: none;
    user-select:none;
    padding-left: 0;
    border-radius: 4px;
    li {
        display: inline;
        a{
            color: #666;
            padding:5px 8px;
        }
        &.active>a{
            font-weight: bold;
            color:#337ab7;
        }
    }
}
</style>
