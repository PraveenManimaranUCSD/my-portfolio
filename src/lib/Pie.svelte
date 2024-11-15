<script>
    import * as d3 from 'd3';

    // Define `data` and `selectedIndex` as props
    export let data = [];
    export let selectedIndex = -1; // -1 means no wedge is selected

    // Generate pie slice angles based on the `value` property
    const sliceGenerator = d3.pie().value(d => d.value);
    const arcData = sliceGenerator(data);

    // Set up the arc generator for drawing paths
    const arcGenerator = d3.arc()
        .innerRadius(0)  // For a full pie chart
        .outerRadius(50);  // Radius of the pie chart

    // Set up an ordinal color scale
    const colors = d3.scaleOrdinal(d3.schemeTableau10);
</script>

<div class="container">
    <svg viewBox="-50 -50 100 100">
        {#each arcData as arc, index}
            <path
                d="{arcGenerator(arc)}"
                fill="{colors(index)}"
                on:click={() => selectedIndex = selectedIndex === index ? -1 : index}
                class:selected={selectedIndex === index} />
        {/each}
    </svg>

    <ul class="legend">
        {#each data as d, index}
            <li class:selected={selectedIndex === index} style="--color: {colors(index)}">
                <span class="swatch"></span>
                {d.label} <em>({d.value})</em>
            </li>
        {/each}
    </ul>
</div>

<style>
    .container {
        display: flex;
        align-items: center;
        gap: 1em;
    }

    svg {
        max-width: 15em;
        margin-block: 2em;
        overflow: visible;
    }

    .legend {
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(9em, 1fr));
        gap: 0.5em;
        padding: 1em;
        border: 1px solid #ddd;
        border-radius: 8px;
        background-color: #f9f9f9;
        flex: 1;
    }

    .legend li {
        display: flex;
        align-items: center;
        gap: 0.5em;
    }

    .legend .swatch {
        display: inline-block;
        width: 1em;
        height: 1em;
        background-color: var(--color);
        border-radius: 50%;
    }

    .legend em {
        font-style: normal;
        color: #555;
    }

    /* Hover effect for highlighting */
    path {
        transition: opacity 300ms;
        cursor: pointer;
    }

    svg:hover path:not(:hover) {
        opacity: 0.5;
    }

    /* Highlight the selected wedge and legend item */
    .selected {
        --color: okLch(60% 45% 0) !important;
    }

    .selected path {
        fill: var(--color);
    }
</style>
