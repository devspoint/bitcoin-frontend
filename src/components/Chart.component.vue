<template>
  <div id="chart">
    <apexchart
      type="area"
      height="850"
      ref="chart"
      :options="chartOptions"
      :series="series"
    ></apexchart>
  </div>
</template>

<script>
var data = [];
var read;
var bitcoinData = {
  order: [],
  openingValue: [],
  date: [],
};
var dates = ["2013-04-28 23:59:59"];

export default {
  name: "Chart",

  data: function () {
    return {
      chartOptions: {
        chart: {
          id: "realtime",
          height: 850,
          animations: {
            enabled: true,
            easing: "linear",
            dynamicAnimation: {
              speed: 500,
            },
          },
        },
        colors: ["#B4F8C8"],
        dataLabels: {
          enabled: false,
        },
        xaxis: {
          type: "datetime",
          categories: dates,
        },
        yaxis: {
          opposite: true,
          labels: {
            show: true,
            align: "right",
            minWidth: 0,
            maxWidth: 160,
            style: {
              colors: [],
              fontSize: "20px",
              fontFamily: "Helvetica, Arial, sans-serif",
              fontWeight: 400,
              cssClass: "apexcharts-yaxis-label",
            },
          },
          colors: ["#E91E63"],
        },
      },
      series: [
        {
          data: data.slice(),
        },
      ],
    };
  },
  mounted: function () {
    var me = this;

    fetch("http://localhost:8081/bitcoin/history").then((response) => {
      const reader = response.body.getReader();
      return reader.read().then(
        (read = (result) => {
          var stringResult = new TextDecoder().decode(result.value);

          if (result.done) {
            return data;
          }

          var split = stringResult.split("data:");
          split.forEach((element) => {
            if (element.length > 0) {
              console.log(element);
              var jsonElement = JSON.parse(element);
              bitcoinData.order.push(jsonElement.order);
              bitcoinData.openingValue.push(
                jsonElement.openingValue.toFixed(2)
              );
              dates.push(jsonElement.date);
            }
          });

          return reader.read().then(read);
        })
      );
    });

    window.setInterval(function () {
      me.$refs.chart.updateSeries([
        {
          data: bitcoinData.openingValue,
        },
      ]);
    }, 500);
  },
};
</script>
