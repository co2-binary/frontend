<script>
	import Router from 'svelte-spa-router'

	import Header from './Header.svelte';
	import Home from "./Home.svelte";
	import Distribution from "./Distribution.svelte";
	import Summary from './Summary.svelte';

	const routes = {
		'/': Home,
		'/distribution': Distribution,
		'/summary': Summary,
	}

</script>

<body>
	<Header />
	<Router {routes}/>
</body>