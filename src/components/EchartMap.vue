<template>
    <div id="mapChart" class="chart"></div>
</template>
<script>
    import echarts from "echarts";
    import axios from 'axios'
    export default {
        props: {
            mapData: {
                type: Array,
                default: [],
            },
            mapOption: {
                type: Object,
            }

        },
        computed: {
            // 通过计算值，将mapOption的默认值补全，之前的父子值会覆盖这
            chartOption() {
                return {
                    'title': '我是地图',
                    'titleColor': '#8796B0',
                    'backgroundColor': '#2B3648',
                    'areaColor': '#262C38',
                    'borderColor': '#678EE3',
                    'hoverAreaColor': '#16467B',
                    'lineColor': 'rgb(192, 158, 255)',
                    'trailColor': '#fff',
                    'startColor': 'pink',
                    'endColor': 'rgb(192, 158, 255)',
                    ...this.mapOption
                }
            }
        },
        methods: {
            mapChart() {
                var myChart = echarts.init(document.getElementById('mapChart'));

                axios.get("./static/json/china.json", {}).then(response => {
                    echarts.registerMap("china", response.data);
                    var series = [];
                    series.push({
                        type: 'lines',
                        zlevel: 1, //zlevel 大的 Canvas 会放在 zlevel 小的 Canvas 的上面。
                        effect: { //线图的特效,所有带有尾迹特效的图表需要单独放在一个层，也就是需要单独设置 zlevel
                            show: true, //是否显示特效，显示。
                            period: 10, //特效动画的时间，单位为 s，默认为 4s。
                            trailLength: 0.7, //特效尾迹的长度。取从 0 到 1 的值，默认为 0.2，数值越大尾迹越长。
                            color: this.chartOption.trailColor, //特效标记的颜色。白色
                            symbolSize: 3 //特效标记的大小，可以设置成诸如 10 这样单一的数字（默认为 3），
                                //也可以用数组分开表示高和宽，例如 [20, 10] 表示标记宽为20，高为10。
                        },
                        lineStyle: {
                            normal: {
                                width: 0, //有值就会变红色。。
                                curveness: 0.2 //设置弯曲度
                            }
                        },
                        data: this.initDataLines(this.mapData)
                    }, {
                        type: 'lines',
                        zlevel: 2,
                        effect: {
                            show: true,
                            period: 10,
                            trailLength: 0,
                            symbolSize: 10,
                        },
                        lineStyle: {
                            normal: {
                                color: this.chartOption.lineColor, ////决定边的颜色是与起点相同还是与终点相同
                                width: 1,
                                opacity: 0.4,
                                curveness: 0.2
                            }
                        },
                        data: this.initDataLines(this.mapData)
                    }, {
                        type: 'effectScatter', //此为出发地涟漪坐标设置，在提示框组件组件中点击该点打印seriesIndex=2，seriesType='effectScatter'
                        coordinateSystem: 'geo',
                        zlevel: 2,
                        rippleEffect: {
                            brushType: 'stroke',
                            color: 'rgb(108, 92, 231)',
                            scale: 3,
                        },
                        label: {
                            normal: {
                                show: true,
                                position: 'right',
                                formatter: '{b}' //数据名
                            }
                        },

                        itemStyle: {
                            normal: {
                                color: this.chartOption.endColor //this.chartOption.startColor //字体颜色
                            }
                        },
                        data: this.initDataFrom(this.mapData)
                    }, { //终点涟漪配置
                        type: 'effectScatter', //带有涟漪特效动画的,利用动画特效可以将某些想要突出的数据进行视觉突出。
                        coordinateSystem: 'geo', //该系列使用的坐标系,使用地理坐标系
                        zlevel: 2,
                        rippleEffect: { //涟漪特效相关配置
                            brushType: 'stroke', //波纹的绘制方式，可选 'stroke' 和 'fill'
                            color: 'rgb(108, 92, 231)', //涟漪的颜色，默认为散点的颜色。
                            scale: 5,
                        },
                        label: {
                            normal: {
                                show: true,
                                position: 'right',
                                formatter: '{b}'
                            }
                        },
                        /* symbolSize: function(val, params) { //设置涟漪大小,如果需要每个数据的图形大小不一样，可以设置为如下格式的回调函数：
                            console.log('pa', params)
                            return  //参数 value 为 data 中的数据值value,可用params 数据项参数打印出来看
                        }, */
                        itemStyle: {
                            normal: {
                                color: this.chartOption.endColor //字体颜色
                            }
                        },
                        data: this.initDataTo(this.mapData) //需要配置散点坐标
                    });

                    myChart.setOption({
                        backgroundColor: this.chartOption.backgroundColor,
                        title: {
                            text: this.chartOption.title,
                            subtext: this.chartOption.subtitle,
                            left: "center",
                            textStyle: {
                                fontSize: 16,
                                fontFamily: 'PingFangSC-Regular',
                                fontWeight: 'lighter',
                                color: this.chartOption.titleColor
                            }
                        },
                        tooltip: {
                            trigger: 'item',
                            formatter: (params) => {
                                console.log('params', params)
                                if (params.seriesType == 'effectScatter' && params.seriesIndex == 2) {
                                    return "信号产生地：" + params.data.name;
                                } else if (params.seriesType == 'effectScatter' && params.seriesIndex == 3) {
                                    return "信号收集地：" + params.data.name;
                                } else if (params.seriesType == "lines") {
                                    return (
                                        params.data.fromName +
                                        ">" +
                                        params.data.toName +
                                        "<br />" +
                                        params.data.value
                                    );
                                } else {
                                    return params.name;
                                }
                            }
                        },
                        legend: {
                            orient: 'vertical',
                            top: 'bottom',
                            left: 'right',
                            selectedMode: 'single'
                        },
                        geo: {
                            map: 'china',
                            label: {
                                emphasis: {
                                    show: false
                                }
                            },
                            zoom: 1,
                            roam: false,
                            itemStyle: {
                                normal: {
                                    areaColor: this.chartOption.areaColor, //地图颜色
                                    borderColor: this.chartOption.borderColor //区域边框颜色
                                },
                                emphasis: {
                                    areaColor: this.chartOption.hoverAreaColor //区域颜色，鼠标悬停颜色
                                }
                            }
                        },
                        series: series //series：系列列表。我理解为数据列表。这里可以定义每组数据内容，以及数据的展现形式。
                    });

                });
                myChart.on('click', {
                    seriesData: '[91.11, 29.97]'
                }, function() {
                    console.log('beidianjil ')
                        // that.$router.push('/system/rawData')
                });
            },
            initDataLines(data) {
                var result = [];
                for (var i = 0; i < data.length; i++) {
                    var el = data[i];
                    var fromData = `${el.from_longitude},${el.from_latitude}`.split(',')
                    var toData = `${el.to_longitude},${el.to_latitude}`.split(',')
                    var val = []
                    val.push(fromData, toData)
                    result.push({
                        fromName: el.from,
                        toName: el.to,
                        coords: val,
                        value: el.num
                    })
                }
                return result;
            },
            initDataFrom(data) {
                var result = [];
                for (var i = 0; i < data.length; i++) {
                    var el = data[i];
                    // var val = `${el.to_longitude},${el.to_latitude},30`.split(',')
                    var val = `${el.from_longitude},${el.from_latitude},30`.split(',')
                    result.push({
                        name: el.from,
                        value: val //["115.9000015258789", "28.864999771118164", "30"]
                    })
                }
                return result; //存储到得到地名和到的地方经纬度
            },
            initDataTo(data) {
                var result = [];
                for (var i = 0; i < data.length; i++) {
                    var el = data[i];
                    var val = `${el.to_longitude},${el.to_latitude},30`.split(',')
                        //var val = `${el.from_longitude},${el.from_latitude},30`.split(',')
                    result.push({
                        name: el.to,
                        value: val //["115.9000015258789", "28.864999771118164", "30"]
                    })
                }
                return result; //存储到得到地名和到的地方经纬度
            },
        },
        mounted() {
            this.mapChart();
        },
    }
</script>
<style scoped>

</style>