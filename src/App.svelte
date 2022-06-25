<script>
  import { onMount } from "svelte";
  import Line from "svelte-chartjs/src/Line.svelte";

  let tabAselected = false;
  let tabBselected = false;
  let dataIndia = [];
  let dataUK = [];
  let countryData = [];
  let chartData = {
    labels: ["Day 1", "Day 2", "Day 3", "Day 4", "Day 5", "Day 6", "Day 7"],
    datasets: countryData,
  };

  onMount(async () => {
    const firstResponse = await fetch(
      `https://api.covid19api.com/country/india/status/confirmed?from=2022-04-01T00:00:00Z&to=2022-04-07T00:00:00Z`
    );
    // took total cases of particular dates(7 days here)
    dataIndia = await firstResponse.json().then((data) => {
      return data.map((val) => val.Cases);
    });

    const secondResponse = await fetch(
      `https://api.covid19api.com/country/united-kingdom/status/confirmed?from=2022-04-01T00:00:00Z&to=2022-04-07T00:00:00Z`
    );
    dataUK = await secondResponse.json().then((data) => {
      return data.map((val) => val.Cases);
    });
    console.log(dataUK);
  });

  function selectTab(tab) {
    if (tab === "tabA") {
      tabAselected = !tabAselected;
    } else {
      tabBselected = !tabBselected;
    }
    setDataForActive();
  }

  function setDataForActive() {
    countryData = [];
    if (tabAselected && tabBselected) {
      const chartDataInd = getChartJSconfig(
        "India",
        dataIndia,
        "rgba(225, 204,230, .3)",
        "rgb(205, 130, 158)"
      );
      const chartDataUK = getChartJSconfig(
        "UK",
        dataUK,
        "rgba(125, 204,230, .3)",
        "rgb(105, 130, 158)"
      );
      chartData.datasets = [chartDataInd, chartDataUK];
    } else if (tabBselected) {
      const data = getChartJSconfig(
        "UK",
        dataUK,
        "rgba(125, 204,230, .3)",
        "rgb(105, 130, 158)"
      );
      chartData.datasets = [data];
    } else if (tabAselected) {
      const data = getChartJSconfig(
        "India",
        dataIndia,
        "rgba(225, 204,230, .3)",
        "rgb(205, 130, 158)"
      );
      chartData.datasets = [data];
    } else {
      chartData.datasets = [];
    }
  }

  function getChartJSconfig(label, data, bgColor, borderColor) {
    return {
      label: label,
      fill: true,
      lineTension: 0.3,
      backgroundColor: bgColor,
      borderColor: borderColor,
      borderCapStyle: "butt",
      borderDash: [],
      borderDashOffset: 0.0,
      borderJoinStyle: "miter",
      pointBorderColor: "rgb(205, 130,1 58)",
      pointBackgroundColor: "rgb(255, 255, 255)",
      pointBorderWidth: 10,
      pointHoverRadius: 5,
      pointHoverBackgroundColor: "rgb(0, 0, 0)",
      pointHoverBorderColor: "rgba(220, 220, 220,1)",
      pointHoverBorderWidth: 2,
      pointRadius: 1,
      pointHitRadius: 10,
      data: data,
    };
  }
</script>

<div class="tabs">
  <button
    class={tabAselected ? "selected" : ""}
    on:click={() => selectTab("tabA")}>India</button
  >

  <button
    class={tabBselected ? "selected" : ""}
    on:click={() => selectTab("tabB")}>UK</button
  >
</div>
{#if chartData.datasets.length}
  <div class="chart">
    <Line
      data={chartData}
      options={{ responsive: true, maintainAspectRatio: false }}
    />
  </div>
{:else}
  <p>Select a country</p>
{/if}

<div />

<style>
  button {
    margin: 5px;
    border-radius: 5px;
  }

  button:hover {
	box-shadow: 1px 2px 12px 1px #888888;
  }
  p {
    text-align: center;
    font-size: 20px;
    font-weight: 600;
  }

  .tabs {
    display: flex;
    justify-content: center;
  }

  .chart {
    height: 400px;
  }

  .selected {
    background-color: #448b35bd;
    color: #ffffff;
  }
</style>
