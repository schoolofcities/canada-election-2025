<script>
    import '../../assets/global-styles.css';

    import { onMount } from 'svelte';

    import TopSofC from '$lib/TopSofC.svelte';
    import SlopeGraph from '$lib/SlopeGraph.svelte';
    import WaffleGraph from '$lib/WaffleGraph.svelte';
    import ResultsPanel from '$lib/ResultsPanel.svelte';

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
        <div class="panel-grid">
            {#if (votesRegion && seatsRegion)}
                <!-- Example panel - repeat as needed -->
                <ResultsPanel 
                    cityName={'Greater Toronto Area'}
                    text={'Sample description text'}
                    partyVotes21={votesRegion['Greater Toronto Area']['2021_pct_vote']} 
                    partyVotes25={votesRegion['Greater Toronto Area']['2025_pct_vote']} 
                    partySeats={seatsRegion['Greater Toronto Area']}
                />
                
                <!-- Add more panels here -->
                <ResultsPanel 
                    cityName={'Vancouver'}
                    text={'Sample description text'}
                    partyVotes21={votesRegion['Vancouver']['2021_pct_vote']} 
                    partyVotes25={votesRegion['Vancouver']['2025_pct_vote']} 
                    partySeats={seatsRegion['Vancouver']}
                />
            {/if}
        </div>
    </div>

    <p>This is where a component will go if we wanted to span the full page.</p>
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