<script>
    import * as d3 from 'd3';
    import { onMount } from 'svelte';
    import Pie from '$lib/Pie.svelte'; // Ensure this path is correct for the Pie component

    let data = [];
    let commits = [];
    let width = 1000, height = 600;
    let margin = { top: 10, right: 10, bottom: 30, left: 50 };

    let svg; // Reference for the SVG element
    let brushSelection = null;

    // Define usable area
    let usableArea = {
        top: margin.top,
        right: width - margin.right,
        bottom: height - margin.bottom,
        left: margin.left,
    };
    usableArea.width = usableArea.right - usableArea.left;
    usableArea.height = usableArea.bottom - usableArea.top;

    // Scales and axes
    let xScale, yScale;
    let xAxis, yAxis, yAxisGridlines;

    // Tooltip variables
    let hoveredIndex = -1; // Tracks the currently hovered dot (index)
    $: hoveredCommit = commits[hoveredIndex] ?? {}; // Dynamically stores the hovered commit data
    let cursor = { x: 0, y: 0 };

    // Load data
    onMount(async () => {
        data = await d3.csv('loc.csv', (row) => ({
            ...row,
            line: Number(row.line),
            depth: Number(row.depth),
            length: Number(row.length),
            language: row.language,
            date: new Date(row.date + 'T00:00' + row.timezone),
            datetime: new Date(row.datetime),
        }));

        commits = d3
            .groups(data, (d) => d.commit)
            .map(([commit, lines]) => {
                let first = lines[0];
                let { author, date, time, timezone, datetime } = first;
                let ret = {
                    id: commit,
                    url: 'https://github.com/vis-society/lab-7/commit/' + commit,
                    author,
                    date,
                    time,
                    timezone,
                    datetime,
                    hourFrac: datetime.getHours() + datetime.getMinutes() / 60,
                    totalLines: lines.length,
                };
                Object.defineProperty(ret, 'lines', {
                    value: lines,
                    configurable: true,
                    writable: true,
                    enumerable: false,
                });
                return ret;
            });
    });

    $: if (commits.length > 0) {
        xScale = d3.scaleTime()
            .domain(d3.extent(commits, (d) => d.datetime))
            .range([usableArea.left, usableArea.right])
            .nice();

        yScale = d3.scaleLinear()
            .domain([0, 24]) // Time of day
            .range([usableArea.bottom, usableArea.top]);

        // Update X and Y axes
        d3.select(xAxis).call(d3.axisBottom(xScale));
        d3.select(yAxis).call(d3.axisLeft(yScale));

        // Add gridlines
        d3.select(yAxisGridlines)
            .call(
                d3.axisLeft(yScale)
                    .tickFormat('') // Remove labels
                    .tickSize(-usableArea.width) // Extend gridlines across the chart
            );
    }

    // Brush function
    function brushed(evt) {
        brushSelection = evt.selection;
    }

    // Check if a commit is selected
    function isCommitSelected(commit) {
        if (!brushSelection) return false;
        let [x0, y0] = brushSelection[0];
        let [x1, y1] = brushSelection[1];
        let x = xScale(commit.datetime);
        let y = yScale(commit.hourFrac);
        return x >= x0 && x <= x1 && y >= y0 && y <= y1;
    }

    // Reactive variables for selected data
    $: selectedCommits = brushSelection ? commits.filter(isCommitSelected) : [];
    $: hasSelection = brushSelection && selectedCommits.length > 0;

    // Language breakdown
    $: selectedLines = (hasSelection ? selectedCommits : commits).flatMap((d) => d.lines);
    $: languageBreakdown = d3.rollups(
        selectedLines,
        (lines) => lines.length,
        (d) => d.language
    );

    // Pie chart data
    $: pieChartData = Array.from(languageBreakdown).map(([language, lines]) => ({
        label: language,
        value: lines,
    }));

    $: {
        const brush = d3.brush()
            .extent([
                [usableArea.left, usableArea.top],
                [usableArea.right, usableArea.bottom],
            ])
            .on('start brush end', brushed);

        d3.select(svg).call(brush);
    }
</script>

<section>
    <h1>Meta</h1>
    <dl class="stats">
        <dt>Total <abbr title="Lines of Code">LOC</abbr></dt>
        <dd>{data.length}</dd>
        <dt>Total commits</dt>
        <dd>{commits.length}</dd>
        <dt>Number of files</dt>
        <dd>{d3.group(data, (d) => d.file).size}</dd>
        <dt>Maximum file length</dt>
        <dd>{d3.max(data, (d) => d.length)}</dd>
    </dl>

    <h2>Commits by Time of Day</h2>
    <svg viewBox={`0 0 ${width} ${height}`} bind:this={svg}>
        <!-- Gridlines -->
        <g class="gridlines" transform={`translate(${usableArea.left}, 0)`} bind:this={yAxisGridlines} />
        <!-- Y-axis -->
        <g transform={`translate(${usableArea.left}, 0)`} bind:this={yAxis} />
        <!-- X-axis -->
        <g transform={`translate(0, ${usableArea.bottom})`} bind:this={xAxis} />
        <!-- Scatterplot -->
        <g class="dots">
            {#each commits as commit, index}
                <circle
                    cx={xScale(commit.datetime)}
                    cy={yScale(commit.hourFrac)}
                    r="5"
                    fill="steelblue"
                    class:selected={isCommitSelected(commit)}
                    on:mouseenter={(evt) => {
                        hoveredIndex = index;
                        cursor = { x: evt.clientX, y: evt.clientY };
                    }} 
                    on:mouseleave={() => hoveredIndex = -1}
                />
            {/each}
        </g>
    </svg>

    <p>{hasSelection ? selectedCommits.length : "No"} commits selected</p>

    <h3>Language Breakdown</h3>
    <ul>
        {#each languageBreakdown as [language, lines]}
            <li>
                {language}: {lines} lines ({d3.format(".1%")(lines / selectedLines.length)})
            </li>
        {/each}
    </ul>

    <!-- Pie Chart -->
    <Pie data={pieChartData} />
</section>

<style>
    svg {
        overflow: visible;
    }
    .gridlines {
        stroke-opacity: 0.2;
    }
    .tooltip {
        position: fixed;
        top: 1em;
        left: 1em;
        background-color: white;
        padding: 8px;
        border: 1px solid #ccc;
        border-radius: 4px;
        box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    }
    .info {
        display: grid;
        grid-template-columns: max-content auto;
        gap: 4px 8px;
    }

    circle {
        transition: 200ms;

        &:hover {
            transform: scale(1.5);
            transform-origin: center;
            transform-box: fill-box;
        }
    }

    circle.selected {
        fill: orange;
        stroke: black;
        stroke-width: 2px;
    }
</style>
