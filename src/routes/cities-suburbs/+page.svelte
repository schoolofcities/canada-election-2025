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

    <div class="footnotes">
        <h3>Data & Methods</h3>

        <p>
            We obtained geographic boundaries for cities and metro regions from the <a href='https://www12.statcan.gc.ca/census-recensement/2021/geo/sip-pis/boundary-limites/index2021-eng.cfm?year=21' target="_blank">2021 Canadian census</a>, using Census Subdivisions (CSDs) for cities and Census Metropolitan Areas (CMAs) for regions. Two exceptions were made: for Toronto, we used the <a href='https://en.wikipedia.org/wiki/List_of_municipalities_in_the_Greater_Toronto_Area' target="_blank">Greater Toronto Area</a> instead of the Toronto CMA because it is more widely recognized, and for Montreal, we used the full island due to its complex municipal borders. We selected 9 cities based on population size and geographic diversity, along with the three most populous metro regions.
        </p>

        <p>
            The <a href='https://osdp-psdo.canada.ca/dp/en/search/metadata/NRCAN-FGP-1-2bf02698-f0aa-4758-a538-a01be833d439' target="_blank">riding boundaries</a> for the 2025 election followed the 2022 Canadian federal electoral redistribution. A riding was assigned to a city or metro region if at least 50% of its geographic area fell within that jurisdiction. Ridings within a metro region but outside its core city – such as one in Brampton in the Greater Toronto Area – were classified as suburban.
        </p>

        <p>
            Vote data for the 2021 election came from Elections Canada’s <a href='https://www.elections.ca/content.aspx?section=res&dir=rep/tra/2023rep&document=index&lang=e' target="_blank">transposed results</a>, while 2025 results were sourced from <a href='https://newsinteractives.cbc.ca/elections/federal/2025/results/' target="_blank">the CBC</a> (also derived from Elections Canada). As of writing, 99% of all results were reporting. We aggregated votes and seat counts for each region by summing the results of its constituent ridings. Vote shares were displayed only for larger parties (the Green Party and People's Party did not register significant results in any region), and seat counts were shown only if a party won at least one seat in either 2021 or 2025.
        </p>

        <p>
            You can find the data <a href='https://github.com/schoolofcities/canada-election-2025/tree/main/static/data/results' target="_blank">here</a>, and all the code in the <a href='https://github.com/schoolofcities/canada-election-2025' target="_blank">GitHub repository</a>.
        </p>
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