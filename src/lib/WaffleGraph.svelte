<script>
    import { PARTY_TAGS, PARTY_COLOURS, PARTY_COLOURS_LIGHT, PARTY_NAMES_SHORT } from "./constants";

    export let partySeats = [
        {prevParty: 'lib', curParty: 'lib', isFlipped: false},
        {prevParty: 'lib', curParty: 'lib', isFlipped: false},
        {prevParty: 'con', curParty: 'lib', isFlipped: true},
        {prevParty: 'ndp', curParty: 'lib', isFlipped: true},
        {prevParty: 'con', curParty: 'con', isFlipped: false},
        {prevParty: 'ndp', curParty: 'ndp', isFlipped: false},
    ];

    // Constants for the visualization -- squares
    const SQUARE_SIZE = 16;
    const SQUARE_PADDING = 1;
    const SQUARES_PER_ROW = 6;

    // Constants for the visualization -- diagonal lines and lighter square
    const INNER_SQUARE_RATIO = 0.8;
    const LINE_WIDTH = 2;
    const LINE_SPACING = 6;
    const DIAGONAL_OFFSET = 0.5;
    const INNER_SIZE = SQUARE_SIZE * INNER_SQUARE_RATIO
    const INNER_OFFSET = (SQUARE_SIZE - INNER_SIZE) / 2

    const LINES = getDiagonalLines(SQUARE_SIZE)

    // Set responsive SVG parameters
    $: totalSeats = partySeats.length;
    $: rows = Math.ceil(totalSeats / SQUARES_PER_ROW);
    $: svgWidth = SQUARES_PER_ROW * (SQUARE_SIZE + SQUARE_PADDING) + SQUARE_PADDING;
    $: svgHeight = rows * (SQUARE_SIZE + SQUARE_PADDING) + SQUARE_PADDING;

    // Compute seat toals, and net gains/losses
    $: seatTotals = PARTY_TAGS
        .map(party => {
            const gained = partySeats.filter(seat => (seat.curParty === party && seat.isFlipped)).length;
            const lost = partySeats.filter(seat => (seat.prevParty === party && seat.isFlipped)).length;
            const net = gained - lost;
            
            let sign;
            if (net > 0) sign = "+";
            else if (net < 0) sign = "–";
            else sign = ""

            return {
                party,
                total: partySeats.filter(seat => seat.curParty === party).length,
                gained: gained,
                lost: lost,
                net: net,
                sign: sign,
            }
        });
    $: seatTotals.sort((a, b) => a.total < b.total); // If we want to rank totals by no. seats

    // Sort seat data to be in the order of: total number of seats
    $: sortedPartySeats = [...partySeats].sort((a, b) => {
        // Get seat totals for each party
        const aTotal = seatTotals.find(p => p.party === a.curParty)?.total || 0;
        const bTotal = seatTotals.find(p => p.party === b.curParty)?.total || 0;

        // 1. Sort by total seats (descending)
        if (aTotal !== bTotal) return bTotal - aTotal;

        // 2. Sort alphabetically by party (if totals are equal)
        if (a.curParty !== b.curParty) return a.curParty.localeCompare(b.curParty);

        // 3. Sort by isFlipped (false first)
        return (a.isFlipped ? 1 : 0) - (b.isFlipped ? 1 : 0);
    });
    
    // Sort seat data to be in the order of: PARTY_SEATS
    // $: sortedPartySeats = [...partySeats].sort((a, b) => {
    //     // First sort by PARTY_TAGS order
    //     const aIndex = PARTY_TAGS.indexOf(a.curParty);
    //     const bIndex = PARTY_TAGS.indexOf(b.curParty);
    //     if (aIndex !== bIndex) {
    //         return aIndex - bIndex;
    //     }
        
    //     // If same party, sort by isFlipped (false first)
    //     return (a.isFlipped ? 1 : 0) - (b.isFlipped ? 1 : 0);
    // });

    // Create positions with bottom-up filling
    $: seatPositions = Array.from({length: totalSeats}, (_, i) => {
        const visualIndex = totalSeats - 1 - i;
        const row = rows - 1 - Math.floor(visualIndex / SQUARES_PER_ROW);
        const col = visualIndex % SQUARES_PER_ROW;
        
        return {
            x: col * (SQUARE_SIZE + SQUARE_PADDING) + SQUARE_PADDING,
            y: row * (SQUARE_SIZE + SQUARE_PADDING) + SQUARE_PADDING,
            seat: sortedPartySeats[visualIndex], // Index starts squares from bottom-left, going right, then up
            index: i
        };
    });

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
                {@const colour = PARTY_COLOURS[seat.curParty]}
                {@const lightColour = PARTY_COLOURS[seat.prevParty]}

                <!-- Outer square -->
                <rect
                    x={pos.x}
                    y={pos.y}
                    width={SQUARE_SIZE}
                    height={SQUARE_SIZE}
                    fill={colour}
                />

                {#if seat.isFlipped}
                    <!-- Inner square -->
                    <rect
                        x={pos.x + INNER_OFFSET}
                        y={pos.y + INNER_OFFSET}
                        width={INNER_SIZE}
                        height={INNER_SIZE}
                        fill={lightColour}
                    />

                    <!-- Diagonal lines -->
                    <g transform={`translate(${pos.x},${pos.y})`}>
                        <defs>
                            <clipPath id={`clip-${pos.index}`}>
                                <rect width={SQUARE_SIZE} height={SQUARE_SIZE} />
                            </clipPath>
                        </defs>
                        <g clip-path={`url(#clip-${pos.index})`}>
                            {#each LINES as line}
                                <line
                                    x1={line.x1 - DIAGONAL_OFFSET}
                                    y1={line.y1 - DIAGONAL_OFFSET}
                                    x2={line.x2 - DIAGONAL_OFFSET}
                                    y2={line.y2 - DIAGONAL_OFFSET}
                                    stroke={colour}
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
        {#each seatTotals as partyData}
            {#if partyData.total > 0 || partyData.net !== 0}
                <div class="party-total">
                    <span class="party-color-box">
                        <span class="party-color" style="background-color: {PARTY_COLOURS[partyData.party]};"></span>
                    </span>
                    <span class="party-number">{partyData.total}</span>
                    <span class="party-number-change">({partyData.sign}{Math.abs(partyData.net)})</span>
                    <span class="party-name">{PARTY_NAMES_SHORT[partyData.party]}</span>
                </div>
            {/if}
        {/each}
    </div>
</div>

<style>
    .waffle-container {    
        margin-top: 20px;
        margin-right: -25px;
        flex-grow: 0;      /* Don't expand beyond width */
        flex-shrink: 0;    /* Don't compress below width */
        flex-basis: 137px; /* Must be adjusted if SQUARE_SIZE/PADDING/PER_ROW are adjusted */
    }
    
    .waffle-graph {
        justify-content: center;
        margin-bottom: 2px; /* Add this line */
    }
    
    .waffle-svg {
        border-radius: 4px;
        /* background-color: #f5f5f5; */
    }
    
    .party-totals {
        display: flex;
        flex-direction: column;
        /* gap: 4px; */
        width: 100%;
        align-items: flex-start;
        padding-left: 2px;
    }
    
    .party-total {
        display: flex;
        align-items: center;
        font-size: 0.9rem;
        width: 100%;
    }
    
    .party-color-box {
        display: inline-block;
        width: 6px;
        /* margin-right: 0px; */
    }
    
    .party-color {
        display: inline-block;
        width: 6px;
        height: 16px;
        /* border-radius: 2px; */
        vertical-align: middle;
    }
    
    .party-number {
        display: inline-block;
        width: 20px;
        text-align: right;
        margin-right: 3px;
        font-variant-numeric: tabular-nums;
        font-family: TradeGothicBold;
        font-size: 14px;
    }

    .party-number-change {
        display: inline-block;
        width: 28px;
        text-align: left;
        margin-right: 6px;
        font-variant-numeric: tabular-nums;
        font-family: TradeGothicLTLight;
        font-size: 14px;
    }
    
    .party-name {
        display: inline-block;
        font-family: TradeGothicLTLight;
        font-size: 14px;
    }
</style>