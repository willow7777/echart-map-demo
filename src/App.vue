<template>
    <div id="app">
        <EchartMap :mapData="mapData" style="width: 100%;height: 100vh" :mapOption="{
            'title': '射频信号收发',
			'subtitle':'数据纯属虚构'
        }"></EchartMap>
    </div>
</template>
<script>
    import EchartMap from './components/EchartMap'
    export default {
        components: {
            EchartMap
        },
        data() {
            return {
                mapData: [{
                        "from": "西藏",
                        "from_longitude": "91.11",
                        "from_latitude": "29.97",
                        "to": "成都",
                        "to_longitude": "103.9469985961914",
                        "to_latitude": "30.578500747680664",
                        "num": 483
                    }, {
                        "from": "上海",
                        "from_longitude": "121.4648",
                        "from_latitude": "31.2891",
                        "to": "西藏",
                        "to_longitude": "91.11",
                        "to_latitude": "29.97",
                        "num": 483
                    }
                    /* {
                                        "from": "成都",
                                        "from_longitude": "103.9469985961914",
                                        "from_latitude": "30.578500747680664",
                                        "to": "西藏",
                                        "to_longitude": "91.11",
                                        "to_latitude": "29.97",
                                        "num": 483
                                    }, {
                                        "from": "上海",
                                        "from_longitude": "121.4648",
                                        "from_latitude": "31.2891",
                                        "to": "西藏",
                                        "to_longitude": "91.11",
                                        "to_latitude": "29.97",
                                        "num": 483
                                    } */
                ]
            }
        }
    }
</script>
<style>
    * {
        margin: 0;
        padding: 0;
    }
</style>