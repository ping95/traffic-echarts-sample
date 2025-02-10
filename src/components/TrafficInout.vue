<template>
  <div>
    <div style="margin-bottom: 7px">인터페이스별 트래픽(RX / TX)</div>

    <select style="width: 150px">
      <option>전체</option>
      <option>eth0</option>
      <option>eth1</option>
      <option>eth2</option>
      <option>eth3</option>
    </select>
  </div>
  <div id="if-chart-div" class="inout-chart-container">
    <div class="inout-chart-left" style="height: 300px">
      <div>
        RX<br /><br /><br />
        TX
      </div>
    </div>
    <div class="inout-chart-right">
      <VueEcharts :option="chart_options" style="height: 300px" ref="chart" />
    </div>
  </div>
</template>

<script>
import { VueEcharts } from "vue3-echarts";

const formatTGMKValue = (value, base = 1000) => {
  if (value === "") return "";

  value = Math.abs(value);
  let rtn = "";
  if (value >= base * base * base * base)
    rtn = `${parseFloat((value / (base * base * base * base)).toFixed(2))}T`;
  else if (value >= base * base * base)
    rtn = `${parseFloat((value / (base * base * base)).toFixed(2))}G`;
  else if (value >= base * base)
    rtn = `${parseFloat((value / (base * base)).toFixed(2))}M`;
  else if (value >= base) rtn = `${parseFloat((value / base).toFixed(2))}K`;
  else rtn = `${value}`;

  return rtn;
};

const getTooltip = (arrData) => {
  let rtn = "";
  if (arrData.length === 0) return rtn;
  // if (arrData.length % 2 !== 0) {
  //   return rtn;
  // }

  rtn = `<div style="margin: 0px 0 0;line-height:1;">`;
  rtn += `  <div style="font-size:12px;color:#434343;font-weight:400;line-height:1;">${(rtn =
    arrData[0].axisValue)}</div>`;
  rtn += `  <div style="margin: 10px 0 0;line-height:1;">`;

  for (let i = 0; i < arrData.length; i += 2) {
    rtn += `<div style="margin: 0px 0 0;line-height:1;">`;
    rtn += `  <div style="margin: 6px 0 0;line-height:1;">`;
    rtn += `    <span style="display:inline-block;margin-right:4px;border-radius:10px;width:10px;height:10px;background-color:${arrData[i].color};"></span>`;
    rtn += `    <span style="font-size:11px;color:#434343;font-weight:400;margin-left:2px">${arrData[i].seriesName}</span>`;
    if (arrData[i].value !== "") {
      rtn += `    <span style="float:right;margin-left:20px;font-size:11px;color:#434343;font-weight:700">${formatTGMKValue(
        arrData[i].value
      )}`;
      if (i < arrData.length - 1) {
        rtn += ` / ${formatTGMKValue(arrData[i + 1].value)}</span>`;
      }
    }
    rtn += `    <div style="clear:both"></div>`;
    rtn += `  </div>`;
    rtn += `  <div style="clear:both"></div>`;
    rtn += `</div>`;
  }
  rtn += `  </div>`;
  rtn += `  <div style="clear:both"></div>`;
  rtn += `</div>`;
  console.log(rtn);
  return rtn;
};

export default {
  components: {
    VueEcharts,
  },
  data() {
    return {
      chart_options: {
        color: ["#5470c6", "#91cc75", "#fac858", "#888888", "#ff0000"],
        tooltip: {
          trigger: "axis",
          extraCssText: "text-align: left;",
          borderWidth: 1,
          borderColor: "rgb(115, 175, 215)",
          axisPointer: { type: "shadow" },
          alwaysShowContent: false,
          textStyle: {
            fontSize: 12,
            color: "#434343",
            textBorderType: "dotted",
          },
          formatter: getTooltip,
        },
        grid: {
          left: 20,
          top: 10,
          right: 90,
          bottom: 0,
          containLabel: true,
        },
        legend: {
          // data: ["TCP", "UDP", "ICMP", "ETC", "BLOCK"],
          itemGap: 5,
          backgroundColor: "rgba(238,238,238,0.4)",
          borderColor: "#eee",
          borderWidth: 1,
          borderRadius: 2,
          orient: "vertical",
          textStyle: {
            fontSize: 10,
          },
          type: "scroll",
          pageIconSize: 8,
          pageTextStyle: {
            fontSize: 10,
          },
          right: 5,
          top: 5,
          padding: [7, 5],
        },
        xAxis: [
          {
            name: "",
            nameGap: 20,
            nameLocation: "center",
            type: "category",
            nameTextStyle: {
              padding: 5,
              align: "right",
              fontWeight: "bold",
            },
            axisLabel: {
              interval: "auto",
            },
            data: [
              "00:01",
              "00:02",
              "00:03",
              "00:04",
              "00:05",
              "00:06",
              "00:07",
              "00:08",
              "00:09",
              "00:10",
              "00:11",
              "00:12",
              "00:13",
              "00:14",
              "00:15",
              "00:16",
              "00:17",
              "00:18",
              "00:19",
              "00:20",
              "00:21",
              "00:22",
              "00:23",
              "00:24",
              "00:25",
              "00:26",
              "00:27",
              "00:28",
              "00:29",
              "00:30",
            ],
          },
        ],
        yAxis: [
          {
            name: "",
            nameLocation: "center",
            type: "value",
            minInterval: 1,
            nameTextStyle: {
              padding: [0, 0, 60],
              align: "center",
            },
            type: "value",
            min: function (value) {
              return parseInt(Math.max(Math.abs(value.min), value.max) * -1);
            },
            max: function (value) {
              return parseInt(Math.max(Math.abs(value.min), value.max));
            },
            axisLabel: {
              hideOverlap: true,
              showMinLabel: true,
              showMaxLabel: true,
              formatter: (value) => {
                const _value = formatTGMKValue(value);
                return _value + "bps";
              },
            },
            splitLine: {
              show: true,
              lineStyle: {
                type: "solid",
              },
            },
          },
        ],
        series: [
          {
            name: "TCP",
            type: "line",
            stack: "rx",
            showSymbol: false,
            lineStyle: {
              width: 0,
            },
            areaStyle: {
              opacity: 0.5,
            },
            emphasis: {
              focus: "series",
            },
            data: [
              1120, 1132, 1101, 1134, 790, 1230, 1210, 1120, 1132, 1101, 1134,
              790, 1230, 1210, 1120, 1132, 1101, 1134, 790, 1230, 1210, 1120,
              1132, 1101, 1134, 790, 1230, 1210, 1230, 1210,
            ],
          },
          {
            name: "TCP",
            type: "line",
            stack: "rx",
            showSymbol: false,
            lineStyle: {
              width: 0,
            },
            areaStyle: {
              opacity: 0.5,
            },
            emphasis: {
              focus: "series",
            },
            data: [
              -1120, -1132, -1101, -1134, -790, -1230, -1210, -1120, -1132,
              -1101, -1134, -790, -1230, -1210, -1120, -1132, -1101, -1134,
              -790, -1230, -1210, -1120, -1132, -1101, -1134, -790, -1230,
              -1210, -1230, -1210,
            ],
          },
          {
            name: "UDP",
            type: "line",
            stack: "rx",
            showSymbol: false,
            lineStyle: {
              width: 0,
            },
            areaStyle: {
              opacity: 0.5,
            },
            emphasis: {
              focus: "series",
            },
            data: [
              220, 182, 191, 234, 290, 330, 310, 310, 222, 178, 220, 182, 191,
              234, 290, 330, 310, 310, 222, 178, 220, 182, 191, 234, 290, 330,
              310, 310, 222, 178,
            ],
          },
          {
            name: "UDP",
            type: "line",
            stack: "rx",
            showSymbol: false,
            lineStyle: {
              width: 0,
            },
            areaStyle: {
              opacity: 0.5,
            },
            emphasis: {
              focus: "series",
            },
            data: [
              -220, -182, -191, -234, -290, -330, -310, -310, -222, -178, -220,
              -182, -191, -234, -290, -330, -310, -310, -222, -178, -220, -182,
              -191, -234, -290, -330, -310, -310, -222, -178,
            ],
          },
          {
            name: "ICMP",
            type: "line",
            stack: "rx",
            showSymbol: false,
            lineStyle: {
              width: 0,
            },
            areaStyle: {
              opacity: 0.5,
            },
            emphasis: {
              focus: "series",
            },
            data: [
              150, 232, 201, 154, 190, 330, 410, 232, 177, 312, 150, 232, 201,
              154, 190, 330, 410, 232, 177, 312, 150, 232, 201, 154, 190, 330,
              410, 232, 177, 312,
            ],
          },
          {
            name: "ICMP",
            type: "line",
            stack: "rx",
            showSymbol: false,
            lineStyle: {
              width: 0,
            },
            areaStyle: {
              opacity: 0.5,
            },
            emphasis: {
              focus: "series",
            },
            data: [
              -150, -232, -201, -154, -190, -330, -410, -232, -177, -312, -150,
              -232, -201, -154, -190, -330, -410, -232, -177, -312, -150, -232,
              -201, -154, -190, -330, -410, -232, -177, -312,
            ],
          },
          {
            name: "ETC",
            type: "line",
            stack: "rx",
            showSymbol: false,
            lineStyle: {
              width: 0,
            },
            areaStyle: {
              opacity: 0.5,
            },
            emphasis: {
              focus: "series",
            },
            data: [
              320, 332, 301, 334, 390, 330, 320, 390, 330, 320, 320, 332, 301,
              334, 390, 330, 320, 390, 330, 320, 320, 332, 301, 334, 390, 330,
              320, 390, 330, 320,
            ],
          },
          {
            name: "ETC",
            type: "line",
            stack: "rx",
            showSymbol: false,
            lineStyle: {
              width: 0,
            },
            areaStyle: {
              opacity: 0.5,
            },
            emphasis: {
              focus: "series",
            },
            data: [
              -320, -332, -301, -334, -390, -330, -320, -390, -330, -320, -320,
              -332, -301, -334, -390, -330, -320, -390, -330, -320, -320, -332,
              -301, -334, -390, -330, -320, -390, -330, -320,
            ],
          },
          {
            name: "차단",
            type: "line",
            stack: "rx",
            showSymbol: false,
            label: {
              show: true,
              position: "top",
            },
            lineStyle: {
              width: 0,
            },
            areaStyle: {
              opacity: 0.5,
            },
            emphasis: {
              focus: "series",
            },
            data: [
              120, 232, 301, 234, 190, 70, 220, 190, 70, 220, 120, 232, 301,
              234, 190, 70, 220, 190, 70, 220, 120, 232, 301, 234, 190, 70, 220,
              190, 70, 220,
            ],
          },
        ],
      },
    };
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.inout-chart-container {
  display: flex; /* Flexbox 레이아웃을 활성화 */
  height: 100%;
}

.inout-chart-left {
  width: 25px; /* 왼쪽 div의 너비를 20px로 설정 */
  display: flex; /* Flexbox 레이아웃 활성화 */
  align-items: center; /* 수직 중앙 정렬 */
  justify-content: center; /* 수평 중앙 정렬 */
  height: 100%; /* 예시 높이. 필요에 따라 조정해주세요. */
}
.inout-chart-left > div {
  padding-bottom: 16px;
  padding-left: 3px;
  font-size: 12px;
}
.inout-chart-right {
  flex-grow: 1; /* 남은 공간을 모두 차지하도록 설정 */
}
</style>
