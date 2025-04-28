<script>
    import { scaleLinear, scalePoint } from 'd3-scale';
    import { PARTY_TAGS, PARTY_COLOURS, PARTY_NAMES_SHORT } from "./constants";

    let {
        partyVotes21,
        partyVotes25,
        partyDisplay = {
            'lib': true,
            'con': true,
            'ndp': true,
            'bloc': false,
            'grn': false,
            'ppc': false,
            'oth': false,
        }
    } = $props();

    // Dimensions
    const width = 175; 
    const height = 175; 
    const margin = { top: 10, right: 100, bottom: 30, left: 10 };
    const innerWidth = width - margin.left - margin.right;
    const innerHeight = height - margin.top - margin.bottom;

    // Scales
    const xScale = scalePoint()
        .domain(['2021', '2025'])
        .range([0, innerWidth])
        .padding(0);

    const yScale = scaleLinear()
        .domain([0, 70])
        .range([innerHeight, 0]);

    // Function to adjust label positions with equal spacing
    function getAdjustedLabelPositions() {
        const labelHeight = 15; // Approximate height of a label
        const minSpacing = 5;   // Minimum spacing between labels
        
        // Get all displayed parties with their original y positions
        let parties = PARTY_TAGS
            .filter(party => partyDisplay[party])
            .map(party => ({
                party,
                y: yScale(partyVotes25[party]),
                text: `${PARTY_NAMES_SHORT[party]} ${(Math.round(partyVotes25[party] * 10) / 10).toFixed(1)}%`
            }));
        
        // Sort by original y position
        parties.sort((a, b) => a.y - b.y);
        
        // Identify overlapping groups
        let groups = [];
        let currentGroup = [parties[0]];
        
        for (let i = 1; i < parties.length; i++) {
            const prev = currentGroup[currentGroup.length - 1];
            const curr = parties[i];
            
            // Check if current label overlaps with any in current group
            const overlapsWithGroup = currentGroup.some(item => 
                Math.abs(curr.y - item.y) < labelHeight + minSpacing
            );
            
            if (overlapsWithGroup) {
                currentGroup.push(curr);
            } else {
                groups.push(currentGroup);
                currentGroup = [curr];
            }
        }
        groups.push(currentGroup);
        
        // Adjust each group with equal spacing
        for (const group of groups) {
            if (group.length <= 1) continue;
            
            // Calculate total space needed for this group
            const totalSpaceNeeded = (labelHeight + minSpacing) * group.length - minSpacing;
            const currentSpace = group[group.length - 1].y - group[0].y;
            
            // If we need more space
            if (totalSpaceNeeded > currentSpace) {
                // Calculate how much to expand
                const expansion = totalSpaceNeeded - currentSpace;
                
                // Calculate new positions with equal spacing
                const firstY = group[0].y - (expansion / 2);
                const lastY = group[group.length - 1].y + (expansion / 2);
                
                // Ensure we don't go out of bounds
                const boundedFirstY = Math.max(0, firstY);
                const boundedLastY = Math.min(innerHeight, lastY);
                
                // Calculate actual available expansion
                const actualFirstY = Math.min(firstY, boundedFirstY);
                const actualLastY = Math.max(lastY, boundedLastY);
                const actualExpansion = (actualLastY - actualFirstY) - currentSpace;
                
                // Apply equal spacing -- TODO shrink this?
                const spacing = (actualExpansion + currentSpace) / (group.length - 1);
                for (let i = 0; i < group.length; i++) {
                    group[i].y = actualFirstY + (i * spacing);
                }
            }
        }
        
        return parties;
    }

    // Get the adjusted label positions
    const adjustedLabels = getAdjustedLabelPositions();
</script>

<div class="slope-graph" style="width: {width}px; height: {height}px;">
    <svg class='slope-svg' width={width} height={height}>
        <g transform="translate(0,0)">
        <!-- <g transform="translate({margin.left}, {margin.top})"> -->
            <!-- X-axis with labels -->
            <line
                x1={xScale('2021')}
                y1={innerHeight}
                x2={xScale('2025')}
                y2={innerHeight}
                stroke="black"
                stroke-width="1"
            />
            
            <text
                x={xScale('2021')}
                y={innerHeight + margin.bottom / 2}
                class="year-label"
                text-anchor="start"
                font-size="12px"
            >
                2021
            </text>
            
            <text
                x={xScale('2025')}
                y={innerHeight + margin.bottom / 2}
                class="year-label"
                text-anchor="end"
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
                {/if}
            {/each}

            <!-- Draw labels with adjusted positions -->
            {#each adjustedLabels as { party, y, text }}
                <text
                    x={xScale('2025') + 10}
                    y={y}
                    class="party-label"
                    font-size="12px"
                    font-weight="bold"
                    fill={PARTY_COLOURS[party]}
                    dominant-baseline="middle"
                >
                    {text}
                </text>
            {/each}
        </g>
    </svg>
</div>

<style>
    .slope-graph {
        flex: 1; /* Pushes other contents of row to end */
        width: 175px;
    }

    .slope-svg {
        overflow: visible;
        width: 100%;
        height: 100%;
    }

    .year-label {
        font-family: TradeGothicBold;
    }

    .party-label {
        font-family: TradeGothicBold;
    }
</style>