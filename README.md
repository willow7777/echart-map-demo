# vue-echart-map

在vue中集成echartMap中国地图，展示信号收发情况

# Demo


![demo](./static/展示.png)

# API


名称 | 类型 | 默认值 | 是否必填 | 描述
---|--- |--- |--- | ---
mapData | Array | [] | 是 | 传入的数据
mapOption | Object | 无 | 否 | 传入的配置


### mapData


- from 产生地
- to 收集地
- from_longitude 产生地经度
- to_longitude 收集地经度
- from_latitude 产生地纬度
- to_latitude 收集地纬度
- num 数值


### mapOption

- title 标题
- titleColor 标题颜色
- backgroundColor 背景色
- areaColor 地图颜色
- borderColor 地图线框颜色
- hoverAreaColor 地图高亮颜色
- lineColor 连接线颜色
- trailColor 轨迹颜色
- endColor 收集地颜色
