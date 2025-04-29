<script>
    import SlopeGraph from '$lib/SlopeGraph.svelte';
    import WaffleGraph from '$lib/WaffleGraph.svelte';

    export let cityName;
    export let text;
    export let partyVotes21, partyVotes25, partySeats;

    const quebecRegions = [
        'Greater Montreal',
        'Greater Montreal (suburbs only)',
        'Island of Montreal',
        'Quebec City',
    ]

    let partyDisplay = {
        'lib': true,
        'con': true,
        'ndp': true,
        'bloc': false,
        'grn': false,
        'ppc': false,
        'oth': false,
    }

    if (quebecRegions.includes(cityName)) {
        partyDisplay = {
            'lib': true,
            'con': true,
            'ndp': true,
            'bloc': true,
            'grn': false,
            'ppc': false,
            'oth': false,
        }
    }
</script>

<div class="results-panel">
    <div class="panel-header">
        <h4>{cityName}</h4>
        {#if text}
            <p>{text}</p>
        {/if}
    </div>
    <div class="graphs-container">
        <SlopeGraph 
            partyVotes21={partyVotes21} 
            partyVotes25={partyVotes25} 
            partyDisplay={partyDisplay}
        />
        <WaffleGraph 
            partySeats={partySeats}
        />
    </div>
</div>

<style>
    .results-panel {
        background: white;
        border-radius: 8px;
        border: solid 1px rgba(0,0,0,0.1);
        box-shadow: 0 2px 6px rgba(0,0,0,0.1);
        padding: 16px;
        margin-bottom: 24px;
        break-inside: avoid;
    }

    .panel-header {
        margin-bottom: 12px;
    }

    .panel-header h4 {
        margin: 0 0 4px 0;
        font-size: 20px;
    }

    .panel-header p {
        margin: 0;
        font-size: 14px;
        line-height: 1.4;
    }

    .graphs-container {
        display: flex;
        gap: 2px;
        align-items: flex-start;
    }

    @media (max-width: 370px) {
        .graphs-container {
            flex-direction: column;
        }
    }
</style>