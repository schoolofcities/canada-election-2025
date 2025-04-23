<script>
    import { PARTY_TAGS, PARTY_COLOURS, PARTY_COLOURS_LIGHT, PARTY_NAMES_SHORT } from "./constants";
    
    export let partySeats = {
        'lib': {
            'lib': 15,
            'con': 5,
            'ndp': 3,
        },
        'con': {
            'lib': 3,
            'con': 5,
            'ndp': 5,
        },
        'ndp': {
            'ndp': 3,
        }
    };

    // Constants for the visualization
    const SQUARE_SIZE = 20;
    const SQUARE_PADDING = 2;
    const SQUARES_PER_ROW = 5;
    const LINE_WIDTH = 2;
    const LINE_SPACING = 8;
    const INNER_SQUARE_RATIO = 0.85;
    const DIAGONAL_OFFSET = 0.5;

    // Prepare data
    $: seatsData = prepareSeatsData(partySeats);
    $: totalSeats = seatsData.reduce((sum, party) => sum + party.total, 0);
    $: rows = Math.ceil(totalSeats / SQUARES_PER_ROW);
    $: svgWidth = SQUARES_PER_ROW * (SQUARE_SIZE + SQUARE_PADDING) + SQUARE_PADDING;
    $: svgHeight = rows * (SQUARE_SIZE + SQUARE_PADDING) + SQUARE_PADDING;
    $: flatSeats = getFlatSeatsArray(seatsData);
    
    // Create properly ordered seat positions (bottom-up, left-to-right)
    $: seatPositions = Array.from({length: totalSeats}, (_, i) => {
        // Calculate visual position from bottom up
        const visualIndex = totalSeats - 1 - i;
        const row = rows - 1 - Math.floor(visualIndex / SQUARES_PER_ROW);
        const col = visualIndex % SQUARES_PER_ROW;
        
        return {
            x: col * (SQUARE_SIZE + SQUARE_PADDING) + SQUARE_PADDING,
            y: row * (SQUARE_SIZE + SQUARE_PADDING) + SQUARE_PADDING,
            seat: flatSeats[i], // Original seat data
            index: i
        };
    });

    function prepareSeatsData(partySeats) {
        return PARTY_TAGS
            .filter(tag => partySeats[tag])
            .map(party => {
                const total = Object.values(partySeats[party]).reduce((a, b) => a + b, 0);
                const seats = [];
                
                for (const [sourceParty, count] of Object.entries(partySeats[party])) {
                    for (let i = 0; i < count; i++) {
                        seats.push({
                            party,
                            sourceParty,
                            isFlipped: sourceParty !== party
                        });
                    }
                }
                
                seats.sort((a, b) => b.isFlipped);
                return { party, total, seats };
            })
            .reverse();
    }

    function getFlatSeatsArray(seatsData) {
        const allSeats = [];
        seatsData.forEach(partyData => {
            allSeats.push(...partyData.seats);
        });
        return allSeats;
    }

    function getDiagonalLines(size) {
        const lineCount = Math.ceil(size * 1.5 / LINE_SPACING);
        const lines = [];
        
        for (let i = -1; i <= lineCount; i++) {
            const offset = i * LINE_SPACING;
            lines.push({
                x1: offset - size,
                y1: 0,
                x2: offset,
                y2: size
            });
        }
        
        return lines;
    }
</script>

<div class="waffle-container">
    <div class="waffle-graph">
        <svg width={svgWidth} height={svgHeight} class="waffle-svg">
            {#each seatPositions as pos}
                {@const seat = pos.seat}
                {@const color = PARTY_COLOURS[seat.party]}
                {@const lightColor = PARTY_COLOURS_LIGHT[seat.party]}
                {@const innerSize = SQUARE_SIZE * INNER_SQUARE_RATIO}
                {@const innerOffset = (SQUARE_SIZE - innerSize) / 2}
                {@const lines = getDiagonalLines(SQUARE_SIZE)}

                <!-- Outer square -->
                <rect
                    x={pos.x}
                    y={pos.y}
                    width={SQUARE_SIZE}
                    height={SQUARE_SIZE}
                    fill={color}
                />

                {#if seat.isFlipped}
                    <!-- Inner square -->
                    <rect
                        x={pos.x + innerOffset}
                        y={pos.y + innerOffset}
                        width={innerSize}
                        height={innerSize}
                        fill={lightColor}
                    />

                    <!-- Diagonal lines -->
                    <g transform={`translate(${pos.x},${pos.y})`}>
                        <defs>
                            <clipPath id={`clip-${pos.index}`}>
                                <rect width={SQUARE_SIZE} height={SQUARE_SIZE} />
                            </clipPath>
                        </defs>
                        <g clip-path={`url(#clip-${pos.index})`}>
                            {#each lines as line}
                                <line
                                    x1={line.x1 - DIAGONAL_OFFSET}
                                    y1={line.y1 - DIAGONAL_OFFSET}
                                    x2={line.x2 - DIAGONAL_OFFSET}
                                    y2={line.y2 - DIAGONAL_OFFSET}
                                    stroke={color}
                                    stroke-width={LINE_WIDTH}
                                />
                            {/each}
                        </g>
                    </g>
                {/if}
            {/each}
        </svg>
    </div>

    <div class="party-totals" style="max-width: {svgWidth}px">
        {#each seatsData.reverse() as partyData}
            <div class="party-total">
                <span class="party-color-box">
                    <span class="party-color" style="background-color: {PARTY_COLOURS[partyData.party]};"></span>
                </span>
                <span class="party-number">{partyData.total}</span>
                <span class="party-name">{PARTY_NAMES_SHORT[partyData.party]}</span>
            </div>
        {/each}
    </div>
</div>

<style>
    .waffle-container {
        display: inline-flex;
        flex-direction: column;
        align-items: flex-start;
        max-width: 100%;
    }
    
    .waffle-graph {
        display: flex;
        justify-content: center;
        width: fit-content;
        margin: 0 auto 8px; /* Added 8px bottom margin for gap */
    }
    
    .waffle-svg {
        border-radius: 4px;
        background-color: #f5f5f5;
    }
    
    .party-totals {
        display: flex;
        flex-direction: column;
        gap: 4px;
        width: 100%;
        align-items: flex-start;
    }
    
    .party-total {
        display: flex;
        align-items: center;
        font-family: sans-serif;
        font-size: 0.9rem;
        width: 100%;
    }
    
    .party-color-box {
        display: inline-block;
        width: 16px; /* Fixed width for color box */
        margin-right: 0px; /* Space after color */
    }
    
    .party-color {
        display: inline-block;
        width: 12px;
        height: 12px;
        border-radius: 2px;
        vertical-align: middle;
    }
    
    .party-number {
        display: inline-block;
        width: 24px; /* Fixed width for numbers */
        text-align: right;
        margin-right: 8px; /* Space after number */
        font-variant-numeric: tabular-nums; /* Ensures consistent number width */
    }
    
    .party-name {
        display: inline-block;
        /* No fixed width, will take remaining space */
    }
</style>