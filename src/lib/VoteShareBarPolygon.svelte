<script>

	import { PARTY_TAGS, PARTY_COLOURS } from "./constants";

	export let year = 2025;

	const PARTY_CONFIG = {
		colors: PARTY_COLOURS,
		order: PARTY_TAGS
	};

	const electionData = {
		2025: { lib: 43.7, con: 41.3, ndp: 6.3, bloc: 6.3, grn: 1.3, ppc: 0.5, oth: 0.6 },
		2021: { lib: 33.1, con: 34.3, ndp: 16, bloc: 7.6, grn: 2.3, ppc: 4.9, oth: 1.8 }
	};


    export let year1 = 2025;
    export let year2 = 2021;
    export let height = 30;

    $: segments1 = calculateSegments(year1);
    $: segments2 = calculateSegments(year2);
    $: connections = calculateConnections();

    function calculateSegments(year) {
        const votes = electionData[year] || {};
        let cumulative = 0;
        
        return PARTY_CONFIG.order
            .filter(party => votes[party] > 0)
            .map(party => {
                const percent = votes[party];
                const segment = {
                    party,
                    percent,
                    color: PARTY_CONFIG.colors[party],
                    start: cumulative,
                    end: cumulative + percent
                };
                cumulative += percent;
                return segment;
            });
    }

    function calculateConnections() {
        return segments1.map(seg1 => {
            const seg2 = segments2.find(s => s.party === seg1.party);
            return seg2 ? { seg1, seg2, color: seg1.color } : null;
        }).filter(Boolean);
    }
</script>

<svg width="100%" height={height} viewBox="0 0 100 {height}" preserveAspectRatio="none">
	{#each segments1 as seg}
        <rect x={seg.start} y={height - 10} width={seg.percent} height="5" fill={seg.color} />
    {/each}

    {#each segments2 as seg}
        <rect x={seg.start} y="5" width={seg.percent} height="5" fill={seg.color} />
    {/each}

    {#each connections as conn}
        <polygon
            points="
                {conn.seg1.start},{height - 10}
                {conn.seg1.end},{height - 10}
                {conn.seg2.end},10
                {conn.seg2.start},10
            "
            fill={conn.color}
            stroke="#ffffff"
            stroke-width="0.0"
			opacity=0.2
        />
    {/each}
</svg>