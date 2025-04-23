<script>
    import { scaleLinear, scalePoint } from 'd3-scale';
    import { PARTY_TAGS, PARTY_COLOURS, PARTY_NAMES_SHORT } from "./constants";

    // Example data - in your real component, you'll use $props()
    const partyVotes21 = {
        'lib': 35.0,
        'con': 30.0,
        'ndp': 15.0,
        'bloc': 8.0,
        'grn': 2.0,
        'ppc': 5.0,
        'oth': 5.0,
    }

    const partyVotes25 = {
        'lib': 45.0,
        'con': 40.0,
        'ndp': 5.0,
        'bloc': 5.0,
        'grn': 1.0,
        'ppc': 1.0,
        'oth': 3.0,
    }

    const partyDisplay = {
        'lib': true,
        'con': true,
        'ndp': true,
        'bloc': true,
        'grn': true,
        'ppc': true,
        'oth': true,
    }

    // Dimensions
    const width = 400;
    const height = 300;
    const margin = { top: 20, right: 100, bottom: 30, left: 40 };
    const innerWidth = width - margin.left - margin.right;
    const innerHeight = height - margin.top - margin.bottom;

    // Scales
    const xScale = scalePoint()
        .domain(['2021', '2025'])
        .range([0, innerWidth])
        .padding(0.5);

    const yScale = scaleLinear()
        .domain([0, 70])
        .range([innerHeight, 0]);
</script>

<div class="slope-graph" style="width: {width}px; height: {height}px;">
    <svg width={width} height={height}>
        <g transform="translate({margin.left}, {margin.top})">
            <!-- X-axis with labels -->
            <line
                x1={0}
                y1={innerHeight}
                x2={innerWidth}
                y2={innerHeight}
                stroke="black"
                stroke-width="1"
            />
            
            <text
                x={xScale('2021')}
                y={innerHeight + margin.bottom / 2}
                text-anchor="middle"
                font-size="12px"
            >
                2021
            </text>
            
            <text
                x={xScale('2025')}
                y={innerHeight + margin.bottom / 2}
                text-anchor="middle"
                font-size="12px"
            >
                2025
            </text>

            <!-- For each party, draw line and dots if displayed -->
            {#each PARTY_TAGS as party}
                {#if partyDisplay[party]}
                    <!-- Line connecting 2021 and 2025 -->
                    <line
                        x1={xScale('2021')}
                        y1={yScale(partyVotes21[party])}
                        x2={xScale('2025')}
                        y2={yScale(partyVotes25[party])}
                        stroke={PARTY_COLOURS[party]}
                        stroke-width="1.5"
                    />

                    <!-- 2021 dot -->
                    <circle
                        cx={xScale('2021')}
                        cy={yScale(partyVotes21[party])}
                        r={4}
                        fill={PARTY_COLOURS[party]}
                    />

                    <!-- 2025 dot -->
                    <circle
                        cx={xScale('2025')}
                        cy={yScale(partyVotes25[party])}
                        r={4}
                        fill={PARTY_COLOURS[party]}
                    />

                    <!-- 2025 label -->
                    <text
                        x={xScale('2025') + 10}
                        y={yScale(partyVotes25[party])}
                        font-size="12px"
                        font-weight="bold"
                        fill={PARTY_COLOURS[party]}
                        dominant-baseline="middle"
                    >
                        {PARTY_NAMES_SHORT[party]} {partyVotes25[party]}%
                    </text>
                {/if}
            {/each}
        </g>
    </svg>
</div>

<style>
    .slope-graph {
        font-family: sans-serif;
    }
</style>