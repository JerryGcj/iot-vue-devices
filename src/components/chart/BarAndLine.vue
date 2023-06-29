<template>
  <div :style="{ padding: '0 50px 32px 0' }">
    <h4 :style="{ marginBottom: '20px' }">{{ title }}</h4>
    <v-chart :forceFit="true" :height="height" :data="data" :scale="scale" :padding=" padding">
      <v-tooltip/>
      <v-legend/>
      <v-axis/>
      <v-bar position="type*成交量"/>
      <v-bar position="type*交易额"/>
      <v-line position="type*line" color="red" :size="3"/>
    </v-chart>
  </div>
</template>

<script>

  export default {
    name: 'BarMultid',
    props: {
      title: {
        type: String,
        default: ''
      },
      dataSource: {
        type: Array,
        default: () => [
          { type: '10:10', 成交量: 200, 交易额: 50, line: 65 },
          { type: '10:15', 成交量: 600, 交易额: 68, line: 342},
          { type: '10:20', 成交量: 200, 交易额: 76, line: 200},
          { type: '10:25', 成交量: 900, 交易额: 125, line: 900},
          { type: '10:30', 成交量: 200, 交易额: 247, line: 200},
          { type: '10:35', 成交量: 200, 交易额: 98, line: 200},
          { type: '10:40', 成交量: 100, 交易额: 32, line: 100}
        ]
      },
      height: {
        type: Number,
        default: 400
      }
    },
    data() {
      return {
        padding: { top:50, right:50, bottom:100, left:50 },
        scale: [{
          dataKey: '成交量',
          min: 0
        }, {
          dataKey: '交易额',
          min: 0
        },{
          dataKey: 'line',
          min: 0
        }]
      }
    },
    computed: {
      data() {
        return this.dataSource
      }
    }
  }
</script>