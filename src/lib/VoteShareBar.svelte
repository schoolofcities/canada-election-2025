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

    $: segments = (() => {
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
                    start: cumulative
                };
                cumulative += percent;
                return segment;
            });
    })();

</script>

<svg width="100%" height="10" viewBox="0 0 100 20" preserveAspectRatio="none">
    {#each segments as segment}
        <rect
            x={segment.start}
            width={segment.percent}
            y="10"
            height="10"
            fill={segment.color}
        />
    {/each}
</svg>