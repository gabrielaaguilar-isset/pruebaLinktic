<script lang="ts">
	import AsideMobile from '$lib/components/UI/AsideMobile.svelte';
import '../app.css';
    import Aside from '../lib/components/UI/Aside.svelte';
    import Header from '../lib/components/UI/Header.svelte';
</script>


<Header />
<Aside />
<AsideMobile /> 


<main class=" md:ml-60 pt-20  max-h-screen overflow-auto  "  >
	<slot />
</main>



