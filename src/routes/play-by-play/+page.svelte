<script>
    import '../../assets/global-styles.css';

    import { onMount } from 'svelte';

    import TopSofC from '$lib/TopSofC.svelte';
    import SlopeGraph from '$lib/SlopeGraph.svelte';
    import WaffleGraph from '$lib/WaffleGraph.svelte';

    let votesRegion = $state(null);
    let votesCities = $state(null);

    onMount(() => {
        fetch('/data/results/votes/region_results.json')
            .then((response) => response.json())
            .then((json) => votesRegion = json);
        
        fetch('/data/results/votes/csd_results.json')
            .then((response) => response.json())
            .then((json) => votesCities = json);
    })
</script>

<main>
    <TopSofC/>

    <div class="title">
        <h1>Canada Votes 2025: A Play-by-Play in the Cities</h1>
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
    
    {#if votesRegion}
        <div class="container">
            <SlopeGraph 
                partyVotes21={votesRegion['Greater Toronto Area']['2021_pct_vote']} 
                partyVotes25={votesRegion['Greater Toronto Area']['2025_pct_vote']} 
            />
            <WaffleGraph />
        </div>
    {/if}

    <p>This is where a component will go if we wanted to span the full page.</p>
</main>

<style>

</style>