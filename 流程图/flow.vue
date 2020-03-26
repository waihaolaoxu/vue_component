<template>
    <el-dialog title="流程图" :visible="true" fullscreen append-to-body @close="close">
        <el-form :inline="true">
            <el-form-item label="方向">
                <el-select v-model="rankDir" @change="autoLayout()" size="small" style="width:100px;">
                    <el-option label="从上至下" value="TB"></el-option>
                    <el-option label="从下至上" value="BT"></el-option>
                    <el-option label="从左至右" value="LR"></el-option>
                    <el-option label="从右至左" value="RL"></el-option>
                </el-select>
            </el-form-item>
            <el-form-item label="间距（nodeSep）">
                <el-slider v-model="nodeSep" style="width:100px;" @change="autoLayout()"></el-slider>
            </el-form-item>
            <el-form-item label="间距（edgeSep）">
                <el-slider v-model="edgeSep" style="width:100px;" @change="autoLayout()"></el-slider>
            </el-form-item>
            <el-form-item label="间距（rankSep）">
                <el-slider v-model="rankSep" style="width:100px;" @change="autoLayout()"></el-slider>
            </el-form-item>
            <el-form-item>
                <el-button size="small" @click="toggleFull()">全屏切换</el-button>
            </el-form-item>
            <el-form-item>
                <el-button size="small" @click="resetSet()">重置</el-button>
            </el-form-item>
        </el-form>
        <div class="flow-wrap">
            <div id="flowChart"></div>
            <div v-if="loading" class="loading"><i class="el-icon-loading"></i> loading</div>
        </div>
    </el-dialog>
</template>

<script>
window.$ = require("jquery");
window.joint = require("jointjs");
let dagre = require("dagre");
let graphlib = require("graphlib");
import "jointjs/css/layout.css";
export default {
    data() {
        return {
            dataList: [
                {
                    parent: {
                        id: 100639,
                        name: "\u9996\u6b21\u7b7e\u7ea6",
                        color: "398464",
                        update_date: "2019-12-23 16:10:32",
                        update_user_name: "\u5434\u8fea"
                    },
                    son_list: [
                        {
                            id: 100639,
                            name: "\u9996\u6b21\u7b7e\u7ea6",
                            color: "398464"
                        },
                        {
                            id: 100637,
                            name: "\u552e\u540e\u670d\u52a1",
                            color: "7441DC"
                        }
                    ]
                },
                {
                    parent: {
                        id: 100633,
                        name: "\u9700\u6c42\u5f85\u786e\u8ba4",
                        color: "E86D2B",
                        update_date: "2019-12-23 14:31:47",
                        update_user_name: "\u5434\u8fea"
                    },
                    son_list: [
                        {
                            id: 100633,
                            name: "\u9700\u6c42\u5f85\u786e\u8ba4",
                            color: "E86D2B"
                        },
                        {
                            id: 100638,
                            name: "\u65e0\u6548\u5ba2\u6237",
                            color: "CCCCCC"
                        },
                        {
                            id: 100634,
                            name: "\u6709\u6548\u5ba2\u6237",
                            color: "2D98E3"
                        }
                    ]
                },
                {
                    parent: {
                        id: 100635,
                        name:
                            "\u91cd\u70b9\u8ddf\u8fdb/\u5f85\u7b7e\u7ea6\u5ba2\u6237",
                        color: "4C8F36",
                        update_date: "2019-12-23 14:09:08",
                        update_user_name: "\u5434\u8fea"
                    },
                    son_list: [
                        {
                            id: 100634,
                            name: "\u6709\u6548\u5ba2\u6237",
                            color: "2D98E3"
                        },
                        {
                            id: 100635,
                            name:
                                "\u91cd\u70b9\u8ddf\u8fdb/\u5f85\u7b7e\u7ea6\u5ba2\u6237",
                            color: "4C8F36"
                        },
                        {
                            id: 100640,
                            name: "\u6218\u8d25/\u653e\u5f03/\u65ad\u7ea6",
                            color: "999999"
                        },
                        {
                            id: 100639,
                            name: "\u9996\u6b21\u7b7e\u7ea6",
                            color: "398464"
                        }
                    ]
                },
                {
                    parent: {
                        id: 100679,
                        name: "\u7eed\u7ea6\u7b7e\u7ea6",
                        color: "21C6A5",
                        update_date: "2019-12-23 12:08:55",
                        update_user_name: "\u5434\u8fea"
                    },
                    son_list: [
                        {
                            id: 100679,
                            name: "\u7eed\u7ea6\u7b7e\u7ea6",
                            color: "21C6A5"
                        },
                        {
                            id: 100637,
                            name: "\u552e\u540e\u670d\u52a1",
                            color: "7441DC"
                        },
                        {
                            id: 100808,
                            name: "\u589e\u8d2d\u7b7e\u7ea6",
                            color: "0DBCD6"
                        }
                    ]
                },
                {
                    parent: {
                        id: 100808,
                        name: "\u589e\u8d2d\u7b7e\u7ea6",
                        color: "0DBCD6",
                        update_date: "2019-12-23 11:05:20",
                        update_user_name: "\u5434\u8fea"
                    },
                    son_list: [
                        {
                            id: 100808,
                            name: "\u589e\u8d2d\u7b7e\u7ea6",
                            color: "0DBCD6"
                        },
                        {
                            id: 100637,
                            name: "\u552e\u540e\u670d\u52a1",
                            color: "7441DC"
                        },
                        {
                            id: 100679,
                            name: "\u7eed\u7ea6\u7b7e\u7ea6",
                            color: "21C6A5"
                        }
                    ]
                },
                {
                    parent: {
                        id: 100637,
                        name: "\u552e\u540e\u670d\u52a1",
                        color: "7441DC",
                        update_date: "2019-12-23 11:05:08",
                        update_user_name: "\u5434\u8fea"
                    },
                    son_list: [
                        {
                            id: 100808,
                            name: "\u589e\u8d2d\u7b7e\u7ea6",
                            color: "0DBCD6"
                        },
                        {
                            id: 100637,
                            name: "\u552e\u540e\u670d\u52a1",
                            color: "7441DC"
                        },
                        {
                            id: 100679,
                            name: "\u7eed\u7ea6\u7b7e\u7ea6",
                            color: "21C6A5"
                        }
                    ]
                },
                {
                    parent: {
                        id: 100640,
                        name: "\u6218\u8d25/\u653e\u5f03/\u65ad\u7ea6",
                        color: "999999",
                        update_date: "2019-12-23 10:50:18",
                        update_user_name: "\u5434\u8fea"
                    },
                    son_list: [
                        {
                            id: 100634,
                            name: "\u6709\u6548\u5ba2\u6237",
                            color: "2D98E3"
                        },
                        {
                            id: 100640,
                            name: "\u6218\u8d25/\u653e\u5f03/\u65ad\u7ea6",
                            color: "999999"
                        }
                    ]
                },
                {
                    parent: {
                        id: 100636,
                        name: "\u5df2\u89c1\u9762",
                        color: "02B01B",
                        update_date: "2019-12-22 21:04:49",
                        update_user_name: "\u5434\u8fea"
                    },
                    son_list: [
                        {
                            id: 100635,
                            name:
                                "\u91cd\u70b9\u8ddf\u8fdb/\u5f85\u7b7e\u7ea6\u5ba2\u6237",
                            color: "4C8F36"
                        },
                        {
                            id: 100640,
                            name: "\u6218\u8d25/\u653e\u5f03/\u65ad\u7ea6",
                            color: "999999"
                        },
                        {
                            id: 100634,
                            name: "\u6709\u6548\u5ba2\u6237",
                            color: "2D98E3"
                        }
                    ]
                },
                {
                    parent: {
                        id: 100634,
                        name: "\u6709\u6548\u5ba2\u6237",
                        color: "2D98E3",
                        update_date: "2019-12-22 21:01:17",
                        update_user_name: "\u5434\u8fea"
                    },
                    son_list: [
                        {
                            id: 100634,
                            name: "\u6709\u6548\u5ba2\u6237",
                            color: "2D98E3"
                        },
                        {
                            id: 100635,
                            name:
                                "\u91cd\u70b9\u8ddf\u8fdb/\u5f85\u7b7e\u7ea6\u5ba2\u6237",
                            color: "4C8F36"
                        },
                        {
                            id: 100636,
                            name: "\u5df2\u89c1\u9762",
                            color: "02B01B"
                        },
                        {
                            id: 100640,
                            name: "\u6218\u8d25/\u653e\u5f03/\u65ad\u7ea6",
                            color: "999999"
                        }
                    ]
                },
                {
                    parent: {
                        id: 100638,
                        name: "\u65e0\u6548\u5ba2\u6237",
                        color: "CCCCCC",
                        update_date: "2019-12-22 21:00:49",
                        update_user_name: "\u5434\u8fea"
                    },
                    son_list: [
                        {
                            id: 100638,
                            name: "\u65e0\u6548\u5ba2\u6237",
                            color: "CCCCCC"
                        }
                    ]
                }
            ],
            loading: true,
            graph: null, //模型
            paper: null, //画布
            nodeSep: 60, //相同等级相邻节点之间间隔的像素数量
            edgeSep: 60, //同一等级相邻边缘之间间隔的像素数量
            rankSep: 60, //等级之间间隔的像素数量
            marginX: 50,
            marginY: 50,
            rankDir: "LR"
        };
    },
    props: {
        // dataList: {
        //     type: Array,
        //     default: function() {
        //         return [];
        //     }
        // }
    },
    mounted() {
        this.$nextTick(() => {
            setTimeout(() => {
                this.init();
            });
        });
    },
    methods: {
        // 关闭
        close() {
            this.$emit("close");
        },
        // 切换全屏
        toggleFull() {
            joint.util.toggleFullScreen(document.querySelector(".el-dialog"));
        },
        // 重置
        resetSet() {
            this.nodeSep = 60;
            this.edgeSep = 60;
            this.rankSep = 60;
            this.rankDir = "LR";
            this.autoLayout();
        },
        // 初始化
        init() {
            var graph = new joint.dia.Graph();
            var paper = new joint.dia.Paper({
                el: document.getElementById("flowChart"),
                model: graph,
                width: 600,
                height: 600,
                gridSize: 20,
                drawGrid: true,
                background: {
                    color: "#c1fabe"
                }
            });
            this.graph = graph;
            this.paper = paper;
            // 创建节点
            var nodeList = {};
            function addNode(data) {
                var rect = new joint.shapes.standard.Rectangle();
                var color = "#" + data.color;
                rect.resize(data.name.length * 16, 25);
                rect.attr({
                    body: {
                        fill: color,
                        stroke: color
                    },
                    rect: {
                        fill: color
                    },
                    label: {
                        text: data.name,
                        fill: "#fff"
                    }
                });
                rect.addTo(graph);
                nodeList[data.id] = rect;
            }

            // 渲染数据
            this.dataList.forEach(item => {
                if (item.parent) {
                    addNode(item.parent);
                }
            });
            this.dataList.forEach(function(item) {
                item.son_list.forEach(d => {
                    if (!nodeList[d.id]) {
                        addNode(d);
                    }
                    var link = new joint.shapes.standard.Link();
                    link.attr('line/stroke', '#'+item.parent.color);
                    // link.attr('wrapper/title', '#'+item.parent.color);
                    link.source(nodeList[item.parent.id],{
                        anchor: {
                            name: 'midSide',
                            args: {
                                rotate: true,
                                // padding: 5
                            }
                        }
                    });
                    link.target(nodeList[d.id]);
                    if(item.parent.id == d.id){
                        link.router("orthogonal"); //manhattan metro orthogonal oneSide
                    }
                    link.addTo(graph);
                    link.connector("rounded"); //jumpover rounded smooth
                });
            });
            this.autoLayout();
            this.loading = false;
        },
        // 自动布局
        autoLayout() {
            var layout = joint.layout.DirectedGraph.layout(this.graph, {
                nodeSep: this.nodeSep,
                edgeSep: this.edgeSep,
                rankSep: this.rankSep,
                marginX: this.marginX,
                marginY: this.marginY,
                rankDir: this.rankDir,
                dagre: dagre,
                graphlib: graphlib
            });
            this.paper.setDimensions(
                layout.width + layout.x * 2,
                layout.height + layout.y * 2
            );
        }
    }
};
</script>

<style lang="scss" scoped>
.flow-wrap {
    background: #c1fabe;
    overflow: auto;
}
.loading {
    text-align: center;
    padding: 100px 0;
}
</style>
