<script>
    import '../../assets/global-styles.css';

    import { onMount } from 'svelte';

    import TopSofC from '$lib/TopSofC.svelte';
    import SlopeGraph from '$lib/SlopeGraph.svelte';
    import WaffleGraph from '$lib/WaffleGraph.svelte';
    import ResultsPanel from '$lib/ResultsPanel.svelte';
    import VoteShareBar from '$lib/VoteShareBar.svelte';
    import VoteShareBarPolygon from "$lib/VoteShareBarPolygon.svelte";

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
        fetch('./data/results/votes/region_results.json')
            .then((response) => response.json())
            .then((json) => votesRegion = json);
        
        fetch('./data/results/votes/csd_results.json')
            .then((response) => response.json())
            .then((json) => votesCities = json);

        fetch('./data/results/seats/region_seat_flips.json')
            .then((response) => response.json())
            .then((json) => seatsRegion = json);
        
        fetch('./data/results/seats/csd_seat_flips.json')
            .then((response) => response.json())
            .then((json) => seatsCities = json);
    })
</script>


<svelte:head>
	<meta
		name="viewport"
		content="width=device-width, initial-scale=1, minimum-scale=1"
	/>

	<title>Canada Votes 2025: Cities & Suburbs | School of Cities</title>

	<meta name="description" content="">
	<meta name="author" content="Aniket Kali, Jeff Allen">
    
	<meta property="og:title" content="Canada Votes 2025: Cities & Suburbs" />
	<meta property="og:description" content="Charting changes in vote share and seats in major Canadian cities and suburbs" />
	<meta property="og:type" content="website" />
	<meta property="og:url" content="https://schoolofcities.github.io/canada-election-2025" />
	<meta property="og:image" content="https://schoolofcities.github.io/canada-election-2025/web-card-cities-suburbs.png" />
	<meta property="og:locale" content="en_CA">

	<meta name="twitter:card" content="summary_large_image" />
	<meta name="twitter:site" content="https://schoolofcities.github.io/canada-election-2025" />
	<meta name="twitter:creator" content="@UofTCities" />
	<meta name="twitter:title" content="Canada Votes 2025: Cities & Suburbs" />
	<meta name="twitter:description" content="Charting changes in vote share and seats in major Canadian cities and suburbs" />
	<meta name="twitter:image" content="https://schoolofcities.github.io/canada-election-2025/web-card-cities-suburbs.png" /> 
    
</svelte:head>


<main>
    <TopSofC/>

    

    <div class="title">

        <VoteShareBarPolygon/>
        
        <h1>Canada Votes 2025: Cities & Suburbs</h1>
        <p>
            <a href='https://www.linkedin.com/in/aniket-k-8a8b9921b/' target='_blank'>Aniket Kali</a>,
            <a href='https://jamaps.github.io/' target='_blank'>Jeff Allen</a>
            |
            April 2025
        </p>
    </div>

   
    <div class="text">
        <p>
            Canadians voted, and were left with the greatest consolidation in the two major parties since 1958. In an historic turnaround the Liberals, led by Mark Carney, find themselves forming government for the fourth time in a row. The Liberals won <span style="border-bottom: solid 4px #DC4633;">43.7%</span> of the national vote, <span style="border-bottom: solid 4px #DC4633;">up by 11.1%</span> from the last election, for <span style="border-bottom: solid 4px #DC4633;">169 seats</span>. As recently as January, they were polling dangerously close to a wipeout – until former Prime Minister Justin Trudeau resigned and threats to sovereignty by the United States came to dominate the national political conversation.
        </p>

        <p>
            They were tailed closely by the Conservatives who won <span style="border-bottom: solid 4px #1E3765">41.3%</span>, <span style="border-bottom: solid 4px #1E3765">up by 7.6%</span>, for <span style="border-bottom: solid 4px #1E3765">143 seats</span>. Led by Pierre Poilievre, they capitalized on a <a href='https://abacusdata.ca/2025-federal-election-final-poll-of-campaign/' target="_blank">desire for change</a>, especially winning over voters upset about the cost-of-living crisis, with notable inroads into <a href='https://thelocal.to/young-conservative-right-wing-voters-poilievre-carney/' target="_blank">young voters</a> and <a href='https://theconversation.com/why-are-so-many-second-generation-south-asian-and-chinese-canadians-planning-to-vote-conservative-253820' target="_blank">visible minorities</a>. 
        </p>

        <p>
            The left-leaning New Democrats (NDP) netted just <span style="border-bottom: solid 4px #EBA00F">6.3%</span>, <span style="border-bottom: solid 4px #EBA00F">down by 11.5%</span> from the last election, for <span style="border-bottom: solid 4px #EBA00F">7 seats</span>. This was their worst performance in the party's history. Despite winning a limited anti-scab law and progress on national dental care and pharmacare programs from the governing Liberals, some voters moved away from the party to rally around the Liberals in the wake of threats to sovereignty, while the Conservatives made <a href='https://www.cbc.ca/news/politics/cpc-ndp-working-class-votes-canada-1.7166294' target="_blank">deliberate</a> <a href='https://www.ekospolitics.com/index.php/2025/04/ekos-predicts-liberal-majority/' target="_blank">inroads</a>  into the NDP's historical working-class base – though not without <a href='https://thetyee.ca/News/2025/04/04/Poilievre-Bid-Woo-Union-Vote-Snags/' target="_blank">dissent</a>.
        </p>

        <p>
            These were the national trends, but how did all of this play out in Canada's cities and suburbs? Below we visualize changes in vote share and total seats in Canada's three largest metros (Vancouver, Toronto, Montreal), and compare between the central cities and the suburbs for each of these regions.
        </p>
        
    </div>
    
    <div class="container" style="max-width: 850px;">
        <div class="panel-grid">
            {#if (votesCities && seatsCities && votesRegion && seatsRegion)}
                {#each metros as [metroName, cityName, metroText, cityText]}
                    <ResultsPanel 
                        cityName={cityName}
                        partyVotes21={votesCities[cityName]['2021_pct_vote']} 
                        partyVotes25={votesCities[cityName]['2025_pct_vote']} 
                        partySeats={seatsCities[cityName]}
                    />
                    <ResultsPanel 
                        cityName={metroName}
                        partyVotes21={votesRegion[metroName]['2021_pct_vote']} 
                        partyVotes25={votesRegion[metroName]['2025_pct_vote']} 
                        partySeats={seatsRegion[metroName]}
                    />
                {/each}
            {/if}
        </div>
    </div>

    <div class="text">
        <p>
            The Conservatives saw clear gains in the suburbs of the Greater Toronto Area, flipping seats in Brampton and Markham. Home to large numbers of South Asian and East Asian Canadians respectively, this is consistent with recent research, supported by the School of Cities, showing that <a href='https://schoolofcities.github.io/gta-immigration/political-shifts' target="_blank">immigrants</a> and <a href='https://www.utsc.utoronto.ca/sociology/utsc-sociologist-breaks-down-surge-south-asian-and-chinese-canadian-support-conservatives' target="_blank">minorities</a> are being won by the right. The Conservatives also saw large gains in Vancouver's suburbs, also home to many immigrant and minority voters, but failed to make a breakthrough.
        </p>
        <p>
            In Montreal, the Liberals and Conservatives gained votes at the expense of the NDP and sovereigntist Bloc Quebecois – though only the Liberals were able to turn that into new seats. Since the tariffs and threats to the economy, many Quebecers have rallied around Canada at the cost of the sovereignty movement. A majority <a href="https://www.theguardian.com/world/2025/apr/22/trump-canada-threats-dampened-quebec-separatist-movement" target="_blank">now say</a> they are proud to be Canadian.
        </p>
        <p>
            We now turn our attention to other major cities in Canada.
        </p>
        <p>
            Both major parties saw decisive gains at the expense of other smaller parties in many cities, but generally gained fewer votes in the cities compared to the rest of the country. In Edmonton and Calgary, historically Conservative-voting cities, the Liberals failed to mount a breakthrough, even though their vote share increased. Similarly, Winnipeg and Halifax followed national trends, but with no changes in seats.
        </p>
        <p>    
            It was in Ottawa and Quebec City that the Conservatives lost votes amid Liberal gains, perhaps as a more pronounced version of what we've seen across the country. In Ottawa, the Liberals not only defended against a <a href="https://ottawacitizen.com/opinion/harden-ndp-ottawa-centre" target="_blank">progressive challenge</a> from the former provincial NDP legislator Joel Harden, but went on to take the only Conservative seat in the city: that of their leader Pierre Poilievre.
        </p>
    </div>

    <div class="container" style="max-width: {850/2 + 850}px;">
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

    <div class="text">
        <p>
            All said and done, urban and suburban Canada mostly rallied around the two major parties. But in the months to come, issues like cost-of-living and affordability will <a href='https://abacusdata.ca/2025-federal-election-final-poll-of-campaign/' target="_blank">climb back up</a> the agenda – issues that Poilievre was set to answer decisively <a href='https://breachmedia.ca/even-if-pierre-poilievre-loses-the-election-he-will-have-jolted-canada-rightward/' target="_blank">from the right</a>, until only very recently. It's an open question who will best seize that moment and to what end – across Canada's cities as much as the rest of the country.
        </p>
    </div>

    <br>
    <br>
    
    
    
    <div class="footnotes">
        <h3>Data & Methods</h3>

        <p>
            We obtained geographic boundaries for cities and metro regions from the <a href='https://www12.statcan.gc.ca/census-recensement/2021/geo/sip-pis/boundary-limites/index2021-eng.cfm?year=21' target="_blank">2021 Canadian census</a>, using Census Subdivisions (CSDs) for cities and Census Metropolitan Areas (CMAs) for regions. Two exceptions were made: for Toronto, we used the <a href='https://en.wikipedia.org/wiki/List_of_municipalities_in_the_Greater_Toronto_Area' target="_blank">Greater Toronto Area</a> instead of the Toronto CMA because it is more widely recognized, and for Montreal, we used the full island due to its complex municipal borders. We selected 9 cities based on population size and geographic diversity, along with the three most populous metro regions.
        </p>

        <p>
            The <a href='https://osdp-psdo.canada.ca/dp/en/search/metadata/NRCAN-FGP-1-2bf02698-f0aa-4758-a538-a01be833d439' target="_blank">riding boundaries</a> for the 2025 election followed the 2022 Canadian federal electoral redistribution. A riding was assigned to a city or metro region if at least 50% of its geographic area fell within that jurisdiction. Ridings within a metro region but outside its core city – such as one in Brampton in the Greater Toronto Area – were classified as suburban.
        </p>

        <p>
            Vote data for the 2021 election came from Elections Canada’s <a href='https://www.elections.ca/content.aspx?section=res&dir=rep/tra/2023rep&document=index&lang=e' target="_blank">transposed results</a>, while 2025 results were sourced from <a href='https://newsinteractives.cbc.ca/elections/federal/2025/results/' target="_blank">the CBC</a> (also derived from Elections Canada). As of writing, 99% of all results were reported. We aggregated votes and seat counts for each region by summing the results of its constituent ridings. Vote shares were displayed only for larger parties (the Green Party and People's Party did not register significant results in any region), and seat counts were shown only if a party won at least one seat in either 2021 or 2025.
        </p>

        <p>
            You can find the data <a href='https://github.com/schoolofcities/canada-election-2025/tree/main/static/data/results' target="_blank">here</a>, and all the code in the <a href='https://github.com/schoolofcities/canada-election-2025' target="_blank">GitHub repository</a>.
        </p>
    </div>

    <div class="text">
        <VoteShareBar year={2021}/>
        <VoteShareBar year={2025}/>
    </div>

    <br>
    <br>

</main>

<style>
    .panel-grid {
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(350px, 1fr));
        gap: 24px;
    }

    @media (max-width: 750px) {
        .panel-grid {
            grid-template-columns: 1fr;
        }
    }
</style>