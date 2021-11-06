<template>
      <div id="chart">
        <apexchart type="line" height="350" ref="chart" :options="chartOptions" :series="series"></apexchart>
      </div>
    
</template>

<script>

var xpto = 30;
var data = []

function getNewSeries() {
  var yz = xpto + 10;
  xpto = yz;
  return data.push(yz);
}

export default {
  name: 'Chart',

  data: function() {
    return {
      chartOptions: {
        chart: {
          id: 'realtime',
          height: 350,
          type: 'line',
          animations: {
          enabled: true,
          easing: 'linear',
          dynamicAnimation: {
            speed: 1000
          }
        },
        },
        xaxis: {
          categories: [1991, 1992, 1993, 1994, 1995, 1996, 1997, 1998]
        }
      },
          series: [{
            data: data.slice()
          }],
    }
  },
mounted: function () {
          var me = this
          window.setInterval(function () {
            getNewSeries(xpto, {
              min: 10,
              max: 90
            })
            
            me.$refs.chart.updateSeries([{
              data: data
            }])
          }, 1000)
        

        },
}
</script>
