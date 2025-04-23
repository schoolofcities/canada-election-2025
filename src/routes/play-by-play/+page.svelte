<script>
    import '../../assets/global-styles.css';

    import TopSofC from '$lib/TopSofC.svelte';
    import SlopeGraph from '$lib/SlopeGraph.svelte';
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
    
    <div class="container">
        <SlopeGraph />
    </div>

    <p>This is where a component will go if we wanted to span the full page.</p>
</main>

<style>

</style>