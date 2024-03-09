<!DOCTYPE html>
<html>
<head>
  <title>Carrier Type by Airline and Year</title>
  <script src="https://d3js.org/d3.v7.min.js"></script>
  <style>
    .bar {
      fill: steelblue;
    }
  </style>
</head>
<body>
  <h2>Carrier Type by Airline and Year</h2>
  <svg width="800" height="400"></svg>
  <script>
    // Data
    var data = [
      { "AIRLINE_NAME": "AIR INDIA", "CARRIER_TYPE": "DOMESTIC", "YEAR": 2015 },
      // Add all your data here
    ];

    // Group data by airline name and carrier type
    var nestedData = d3.nest()
      .key(d => d.AIRLINE_NAME)
      .key(d => d.CARRIER_TYPE)
      .rollup(v => v.length)
      .entries(data);

    // Extract carrier types
    var carrierTypes = nestedData[0].values.map(d => d.key);

    // Define color scale
    var colorScale = d3.scaleOrdinal()
      .domain(carrierTypes)
      .range(d3.schemeCategory10);

    // SVG
    var svg = d3.select("svg"),
        margin = {top: 20, right: 20, bottom: 30, left: 40},
        width = +svg.attr("width") - margin.left - margin.right,
        height = +svg.attr("height") - margin.top - margin.bottom,
        g = svg.append("g")
            .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

    // X Scale
    var x = d3.scaleBand()
        .rangeRound([0, width])
        .padding(0.1)
        .domain(nestedData.map(function(d) { return d.key; }));

    // Y Scale
    var y = d3.scaleLinear()
        .rangeRound([height, 0])
        .domain([0, d3.max(nestedData, function(d) {
          return d3.sum(d.values, function(v) { return v.value; });
        })]);

    // X Axis
    g.append("g")
        .attr("class", "axis axis--x")
        .attr("transform", "translate(0," + height + ")")
        .call(d3.axisBottom(x));

    // Y Axis
    g.append("g")
        .attr("class", "axis axis--y")
        .call(d3.axisLeft(y).ticks(10, "s"))
      .append("text")
        .attr("transform", "rotate(-90)")
        .attr("y", 6)
        .attr("dy", "0.71em")
        .attr("text-anchor", "end")
        .text("Count");

    // Bars
    var bars = g.selectAll(".bar")
      .data(nestedData)
      .enter().append("g")
      .attr("transform", function(d) { return "translate(" + x(d.key) + ",0)"; });

    bars.selectAll("rect")
      .data(function(d) { return d.values; })
      .enter().append("rect")
      .attr("class", "bar")
      .attr("x", function(d) { return x.bandwidth() / 4 + x.bandwidth() / 2 * (d.key === "FOREIGN" ? 1 : -1); })
      .attr("y", function(d) { return y(d.value); })
      .attr("width", x.bandwidth() / 4)
      .attr("height", function(d) { return height - y(d.value); })
      .attr("fill", function(d) { return colorScale(d.key); });
  </script>
</body>
</html>
