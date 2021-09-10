<script>
	import Router from 'svelte-spa-router'

	import Header from './Header.svelte';
	import Home from "./Home.svelte";
	import Distribution from "./Distribution.svelte";

	const routes = {
		'/': Home,
		'/distribution': Distribution,
	}

</script>

<body>
	<Header />
	<Router {routes}/>
</body>