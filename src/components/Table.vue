<template>
  <div v-if="tableData.length == 0">Table is empty</div>
  <div v-else>
    <div class="chart-container">
      <LineChart :chartData="chartData" :height="550" />
    </div>
    <div class="table-container">
      <table>
        <thead>
          <tr>
            <th>Title</th>
            <!-- <th>Start</th> -->
            <th>End</th>
            <th>Num Voters</th>
            <th>Total Holders</th>
            <th class="voter-percent">Voter Percent</th>
            <th>vMTA voted</th>
            <th>vMTA total</th>
            <th class="vmta-percent">vMTA Percent</th>
            <!-- <th>State</th> -->
          </tr>
        </thead>
        <tbody>
          <tr v-for="data in tableData" :key="data._id">
            <td>{{ data.title }}</td>
            <!-- <td>{{ data.start }}</td> -->
            <td>{{ data.end }}</td>
            <td>{{ data.votersNum }}</td>
            <td>{{ data.votersTotal }}</td>
            <td class="voter-percent">{{ data.percentNum }}%</td>
            <td>{{ data.scoreNum }}</td>
            <td>{{ data.scoreTotal }}</td>
            <td class="vmta-percent">{{ data.percentScore }}%</td>
            <!-- <td>{{ data.state }}</td> -->
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import { onMounted, ref } from "@vue/runtime-core";
import LineChart from "./LineChart";
export default {
  name: "Table",
  props: ["data"],
  components: {
    LineChart,
  },
  setup(props) {
    let dataPoints;

    const tableData = ref([]);
    const chartData = ref({});

    onMounted(() => {
      dataPoints = props.data.length - 1;
      chartData.value = {
        labels: [],
        datasets: [
          {
            label: "Percentage of vMTA holders voted",
            data: [],
            borderColor: "#0061E3",
            backgroundColor: "rgba(0, 97, 227, 0.1)",
            pointBackgroundColor: "#0061E3",
            pointRadius: 4,
            pointHoverRadius: 6,
            pointHitRadius: 10,
            tension: 0,
          },
          {
            label: "Percentage of vMTA score voted",
            data: [],
            borderColor: "#FAB41F",
            backgroundColor: "rgba(250, 180, 31, 0.1)",
            pointBackgroundColor: "#FAB41F",
            pointRadius: 4,
            pointHoverRadius: 6,
            pointHitRadius: 10,
            tension: 0,
          },
        ],
      };
      props.data.forEach((row, index) => {
        let percentNum = ((row.votersNum / row.votersTotal) * 100).toFixed(2);
        let percentScore = ((row.scoreNum / row.scoreTotal) * 100).toFixed(2);

        tableData.value.push({
          _id: row._id,
          title: row.title,
          end: row.end,
          votersNum: row.votersNum,
          votersTotal: row.votersTotal,
          percentNum,
          scoreNum: row.scoreNum.toFixed(2),
          scoreTotal: row.scoreTotal.toFixed(2),
          percentScore,
        });

        chartData.value.labels[dataPoints - index] = row.end;
        chartData.value.datasets[0].data[dataPoints - index] = percentNum;
        chartData.value.datasets[1].data[dataPoints - index] = percentScore;
      });
      console.log(chartData.value);
    });

    return { tableData, chartData };
  },
};
</script>

<style>
.chart-container {
  width: calc(100vw - 30px);
  height: 600px;
}
.table-container {
  position: relative;
  overflow: auto;
  height: 80vh;
  border: 1px solid rgba(150, 150, 150, 0.5);
  box-shadow: rgb(0 0 0 / 5%) 0px 4px 12px;
  width: calc(100vw - 80px);
  max-width: 2400px;
  margin: 0 auto;
}
.voter-percent {
  background: rgba(0, 97, 227, 1);
}
.vmta-percent {
  background: rgba(250, 180, 31, 1);
}
td.voter-percent {
  background: rgba(0, 97, 227, 0.1);
}
td.vmta-percent {
  background: rgba(250, 180, 31, 0.1);
}
table {
  border-collapse: collapse;
  table-layout: fixed;
}
td,
th {
  border: 1px solid rgba(150, 150, 150, 0.75);
  border-top: none;
  padding: 10px;
  min-width: 200px;
  background: white;
  box-sizing: border-box;
  text-align: center;
}
thead th {
  position: -webkit-sticky;
  position: sticky;
  top: 0;
  z-index: 2;
  background: rgb(150, 150, 150, 1);
  border-top: none;
  border-bottom: none;
  color: #fff;
}
thead th:first-child {
  left: 0;
  z-index: 3;
  border-left: none;
  text-align: left;
}
tbody tr > :first-child {
  position: -webkit-sticky;
  position: sticky;
  background: #eee;
  left: 0;
  border-left: none;
  text-align: left;
}
tbody tr:last-child td {
  border-bottom: none;
}
table tr th:first-child,
table tr td:first-child {
  border-left: none;
}
table tr th:last-child,
table tr td:last-child {
  border-right: none;
}
</style>
