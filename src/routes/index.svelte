<script>
	import successkid from "images/successkid.jpg";
	import { onMount } from "svelte";

	let P5;

	onMount(async () => {
		const module = await import("p5-svelte");
		P5 = module.default;
	});

	let width = 55;
	let height = 55;

	const sketch = (p5) => {
		p5.setup = () => {
			p5.createCanvas(400, 400);
		};

		p5.draw = () => {
			p5.ellipse(p5.width / 2, p5.height / 2, width, height);
		};
	};
</script>

<svelte:head>
	<title>Sapper project template</title>
</svelte:head>

<h1>Great success!</h1>

<figure>
	<img alt="Success Kid" src={successkid} />
	<figcaption>Have fun with Sapper!</figcaption>
</figure>

<p>
	<strong
		>Try editing this file (src/routes/index.svelte) to test live reloading.</strong
	>
</p>

<label>
	Width
	<input type="range" bind:value={width} min="100" max="1000" step="0.01" />
	{width}
</label>

<label>
	Height
	<input type="range" bind:value={height} min="100" max="1000" step="0.01" />
	{height}
</label>

<!--<P5 {sketch} />-->
<svelte:component this={P5} {sketch} />

<style>
	h1,
	figure,
	p {
		text-align: center;
		margin: 0 auto;
	}

	h1 {
		font-size: 2.8em;
		text-transform: uppercase;
		font-weight: 700;
		margin: 0 0 0.5em 0;
	}

	figure {
		margin: 0 0 1em 0;
	}

	img {
		width: 100%;
		max-width: 400px;
		margin: 0 0 1em 0;
	}

	p {
		margin: 1em auto;
	}

	@media (min-width: 480px) {
		h1 {
			font-size: 4em;
		}
	}
</style>
