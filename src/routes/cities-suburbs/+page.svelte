<script>
    import '../../assets/global-styles.css';

    import { onMount } from 'svelte';

    import TopSofC from '$lib/TopSofC.svelte';
    import SlopeGraph from '$lib/SlopeGraph.svelte';
    import WaffleGraph from '$lib/WaffleGraph.svelte';
    import ResultsPanel from '$lib/ResultsPanel.svelte';

    const cities = {
        "Vancouver": "Sample description text",
        "Edmonton": "Sample description text",
        "Calgary": "Sample description text",
        "Winnipeg": "Sample description text",
        "Toronto": "Sample description text",
        "Ottawa": "Sample description text",
        "Island of Montreal": "Sample description text",
        "Québec": "Sample description text",
        "Halifax": "Sample description text",
    }

    const regions = {
        "Greater Toronto Area": "Sample description text",
        "Montréal": "Sample description text",
        "Vancouver": "Sample description text",
    }

    let votesRegion = $state(null);
    let votesCities = $state(null);

    let seatsRegion = $state(null);
    let seatsCities = $state(null);

    onMount(() => {
        fetch('/data/results/votes/region_results.json')
            .then((response) => response.json())
            .then((json) => votesRegion = json);
        
        fetch('/data/results/votes/csd_results.json')
            .then((response) => response.json())
            .then((json) => votesCities = json);

        fetch('/data/results/seats/region_seat_flips.json')
            .then((response) => response.json())
            .then((json) => seatsRegion = json);
        
        fetch('/data/results/seats/csd_seat_flips.json')
            .then((response) => response.json())
            .then((json) => seatsCities = json);
    })
</script>

<main>
    <TopSofC/>

    <div class="title">
        <h1>Canada Votes 2025: Cities and Suburbs</h1>
        <p>
            <a href='https://www.linkedin.com/in/aniket-k-8a8b9921b/' target='_blank'>Aniket Kali</a>,
            <a href='https://jamaps.github.io/' target='_blank'>Jeff Allen</a>
            |
            April 2025
        </p>
    </div>

    <div class="text">
        <p>
            hello world
        </p>
    </div>
    
    <div class="container">
        <h2>Cities</h2>
        <div class="panel-grid">
            {#if (votesCities && seatsCities)}
                {#each Object.entries(cities) as [city, text]}
                    <ResultsPanel 
                        cityName={city}
                        text={text}
                        partyVotes21={votesCities[city]['2021_pct_vote']} 
                        partyVotes25={votesCities[city]['2025_pct_vote']} 
                        partySeats={seatsCities[city]}
                    />
                {/each}
            {/if}
        </div>
    </div>

    <div class="container">
        <h2>Metros and Suburbs</h2>
        <div class="panel-grid">
            {#if (votesRegion && seatsRegion)}
                {#each Object.entries(regions) as [region, text]}
                    <ResultsPanel 
                        cityName={region}
                        text={text}
                        partyVotes21={votesRegion[region]['2021_pct_vote']} 
                        partyVotes25={votesRegion[region]['2025_pct_vote']} 
                        partySeats={seatsRegion[region]}
                    />
                {/each}
            {/if}
        </div>
    </div>
</main>

<style>
    .panel-grid {
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(400px, 1fr));
        gap: 24px;
    }

    @media (max-width: 768px) {
        .panel-grid {
            grid-template-columns: 1fr;
        }
    }
</style>