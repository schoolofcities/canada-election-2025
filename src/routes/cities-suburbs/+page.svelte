<script>
    import '../../assets/global-styles.css';

    import { onMount } from 'svelte';

    import TopSofC from '$lib/TopSofC.svelte';
    import SlopeGraph from '$lib/SlopeGraph.svelte';
    import WaffleGraph from '$lib/WaffleGraph.svelte';
    import ResultsPanel from '$lib/ResultsPanel.svelte';

    // NOTE: Region/city name are also keys to the data! If you want to set the name differently, then add another element in the array.
    const metros = [
        ['Greater Toronto Area (suburbs only)', 'Toronto', 'Sample description text', 'Sample description text'],
        ['Greater Montreal (suburbs only)', 'Island of Montreal', 'Sample description text', 'Sample description text'],
        ['Metro Vancouver (suburbs only)', 'Vancouver', 'Sample description text', 'Sample description text'],
    ]

    const cities = [
        ['Edmonton', 'Sample description text'],
        ['Calgary', 'Sample description text'],
        ['Winnipeg', 'Sample description text'],
        ['Ottawa', 'Sample description text'],
        ['Quebec City', 'Sample description text'],
        ['Halifax', 'Sample description text'],
    ]

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
        <h2>Metro regions: Suburbs vs. cities</h2>
        <div class="panel-grid">
            {#if (votesCities && seatsCities && votesRegion && seatsRegion)}
                {#each metros as [metroName, cityName, metroText, cityText]}
                    <ResultsPanel 
                        cityName={metroName}
                        partyVotes21={votesRegion[metroName]['2021_pct_vote']} 
                        partyVotes25={votesRegion[metroName]['2025_pct_vote']} 
                        partySeats={seatsRegion[metroName]}
                    />

                    <ResultsPanel 
                        cityName={cityName}
                        partyVotes21={votesCities[cityName]['2021_pct_vote']} 
                        partyVotes25={votesCities[cityName]['2025_pct_vote']} 
                        partySeats={seatsCities[cityName]}
                    />
                {/each}
            {/if}
        </div>
    </div>

    <div class="container">
        <h2>Cities</h2>
        <div class="panel-grid">
            {#if (votesCities && seatsCities)}
                {#each cities as [city, text]}
                    <ResultsPanel 
                        cityName={city}
                        partyVotes21={votesCities[city]['2021_pct_vote']} 
                        partyVotes25={votesCities[city]['2025_pct_vote']} 
                        partySeats={seatsCities[city]}
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