<template>
  <v-chart :forceFit="true" :height="height" :data="data" :scale="scale">
    <v-tooltip :showTitle="false" dataKey="item*percent"/>
    <v-axis/>
    <v-legend dataKey="item"/>
    <v-pie position="percent" color="item" :v-style="pieStyle" :label="labelConfig"/>
    <v-coord type="theta"/>
  </v-chart>
</template>

<script>
  const DataSet = require('@antv/data-set')

  export default {
    props: {
      title: {
        type: String,
        default: ''
      },
      height: {
        type: Number,
        default: 254
      },
      dataSource: {
        type: Array,
        default: () => [
          { item: '示例一', count: 40 },
          { item: '示例二', count: 21 },
          { item: '示例三', count: 17 },
          { item: '示例四', count: 13 },
          { item: '示例五', count: 9 }
        ]
      }
    },
    data() {
      return {
        scale: [{
          dataKey: 'percent',
          min: 0,
          formatter: '.0%'
        }],
        pieStyle: {
          stroke: '#fff',
          lineWidth: 1
        },
        labelConfig: ['percent', {
          formatter: (val, item) => {
            console.log(item)
            return item.point.item + ': ' + item.point.count
          }
        }]
      }
    },
    computed: {
      data() {
        let dv = new DataSet.View().source(this.dataSource)
        console.log("dv"+dv)
        // 计算数据百分比
        dv.transform({
          type: 'percent',
          field: 'count',
          dimension: 'item',
          as: 'percent'
        })
        return dv.rows
      }
    }
  }
</script>