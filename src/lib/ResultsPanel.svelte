<script>
    import SlopeGraph from '$lib/SlopeGraph.svelte';
    import WaffleGraph from '$lib/WaffleGraph.svelte';

    export let cityName;
    export let text;
    export let partyVotes21, partyVotes25, partySeats;
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
        />
        <WaffleGraph 
            partySeats={partySeats}
        />
    </div>
</div>

<style>
    .results-panel {
        background: white;
        border-radius: 4px;
        box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        padding: 16px;
        margin-bottom: 24px;
        break-inside: avoid;
    }

    .panel-header {
        margin-bottom: 12px;
    }

    .panel-header h4 {
        margin: 0 0 4px 0;
        font-size: 18px;
    }

    .panel-header p {
        margin: 0;
        font-size: 14px;
        line-height: 1.4;
    }

    .graphs-container {
        display: flex;
        gap: 12px;
    }

    /* Responsive adjustments */
    @media (max-width: 768px) {
        .results-panel {
            width: 100% !important;
        }
        
        .graphs-container {
            flex-direction: column;
        }
    }
</style>