<script>
	import { onMount } from 'svelte';
	import { scaleLinear, scaleLog } from 'd3-scale';
	import { axisLeft, axisRight, axisTop, axisBottom } from 'd3-axis';
	import { select } from 'd3-selection';

	let d3 = {
		scaleLinear: scaleLinear,
		scaleLog: scaleLog,
		select: select,
		axisLeft: axisLeft,
		axisRight: axisRight,
		axisBottom: axisBottom,
		axisTop: axisTop
	}

	export let data = {data};
	export let width = {width};
	export let height = {height};
	export let xVar = {xVar};
	export let yVar = {yVar};

	let el;

	const padding = { top: 10, right: 50, bottom: 50, left: 50 };



	$: xScale = d3.scaleLinear()
		.domain([0, Math.max.apply(Math, data.map(function(o) { return o[xVar]; }))])
		.range([0, width - padding.left - padding.right]);

	$: yScale = d3.scaleLinear()
		.domain([0, Math.max.apply(Math, data.map(function(o) { return o[yVar]; }))])
		.range([height - padding.bottom, padding.top]);

	// $: xTicks = width > 180 ?
	// 	[0, 4, 8, 12, 16, 20] :
	// 	[0, 10, 20];
	//
	// $: yTicks = height > 180 ?
	// 	[0, 2, 4, 6, 8, 10, 12] :
	// 	[0, 4, 8, 12];
	onMount(generateScatter);

	function generateScatter() {
		var svg = d3.select(el)
			.append("svg")
			.attr("width", width)
			.attr("height", height)
			.append("g")
			.attr("transform",
				  "translate(" + padding.left + "," + padding.top + ")");

		svg.append("g")
		   .attr("transform", "translate(0," + (height-padding.bottom) + ")")
		   .call(d3.axisBottom(xScale)
				.ticks(10)
				.tickSizeInner(-width)
				.tickSizeOuter(0)
				.tickPadding(3)
			);

		svg.append("g")
			.call(d3.axisLeft(yScale)
				.ticks(10)
				.tickSizeInner(-width)
				.tickSizeOuter(0)
				.tickPadding(3)
			)
			.call(g => g.select(".domain").remove());

		svg.append('g')
	    .selectAll("dot")
	    .data(data)
	    .enter()
	    .append("circle")
	      .attr("cx", function (d) { return xScale(d[xVar]); } )
	      .attr("cy", function (d) { return yScale(d[yVar]); } )
	      .attr("r", 6)
	}
</script>

<style>
	.chart :global(circle)  {
		fill: #d51e2d;
	}


		.chart :global(g.tick line) {
			stroke: #ccc;
		}
</style>

<div bind:this={el} class="chart"></div>
