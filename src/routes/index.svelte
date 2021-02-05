<script>
	// TODO: Add more inputs.
	// TODO: Squash failed, redundant or overly verbose commits. Upload to GitHub.
	import { onMount } from "svelte";

	/** @type {typeof import("../components/PiFive.svelte").default} */
	let PiFive;
	let isRu = false;
	onMount(async () => {
		isRu = /^ru\b/.test(navigator.language);

		const module = await import("../components/PiFive.svelte");
		PiFive = module.default;
	});

	let noiseScale = 0.0625;
	let speed = 2;
	let letter = "g";

	const sketch = (p5) => {
		const width = 400;
		const height = 400;

		const pointsNumber = 128;
		const points = Array(pointsNumber);

		// The p5.Graphics that contains foreground (the letter) in it.
		let foregroundGraphics = null;

		const getRandomPointOnForeground = () => {
			const isBackground = (pixels, x, y) => {
				// const pixels = tupledArg[0];
				// const x = tupledArg[1] | 0;
				// const y = tupledArg[2] | 0;
				const i = ((y * height + x) * 4) | 0;
				return ~~pixels[i + 3] < 127;
			};
			let x = 0;
			let y = 0;
			while (
				((x = ~~Math.floor(p5.random(width)) | 0),
				((y = ~~Math.floor(p5.random(height)) | 0),
				isBackground(foregroundGraphics.pixels, x, y)))
			) {}
			return p5.createVector(x, y);
		};

		p5.setup = () => {
			p5.createCanvas(width, height, "p2d");
			p5.background(200);
			p5.noiseSeed(10);
			p5.noStroke();
			p5.fill(0);
			foregroundGraphics = p5.createGraphics(width, height, "p2d");
			foregroundGraphics.textSize(300);
			foregroundGraphics.text(letter, p5.width / 3, p5.height / 1.5);
			foregroundGraphics.loadPixels();
			for (let i_1 = 0; i_1 <= pointsNumber - 1; i_1++) {
				points[i_1] = getRandomPointOnForeground();
			}
		};

		p5.draw = () => {
			p5.image(foregroundGraphics, 0, 0);
			points.forEach((p) => {
				p5.circle(p.x, p.y, 0.5);
			});
			for (let i_2 = 0; i_2 <= pointsNumber - 1; i_2++) {
				let p_1 = points[i_2];
				p_1 = p_1.add(
					p5.createVector(
						speed *
							(p5.noise(
								p_1.x * noiseScale,
								p_1.y * noiseScale,
								0
							) *
								2 -
								1),
						speed *
							(p5.noise(
								p_1.x * noiseScale,
								p_1.y * noiseScale,
								9
							) *
								2 -
								1)
					)
				);
				points[i_2] = p_1;
				if (p5.random() < 0.0625) {
					points[i_2] = getRandomPointOnForeground();
				}
			}
		};
	};

	let engine;
	let manager;
</script>

<svelte:head>
{#if isRu}
	<title>Привет мир</title>
{:else}
	<title>Sapper project template</title>
{/if}
</svelte:head>

<label>
	Letter
	<input type="text" bind:value={letter} minlength="{1}" maxlength="{1}"
		on:change="{manager.restart()}" />
</label>

<label>
	Noise scale
	<input type="range" bind:value={noiseScale} min="{0.0625/4}" max="{0.0625*4}" step="0.01"
		on:change="{manager.restart()}" />
	{noiseScale}
</label>

<label>
	Speed
	<input type="range" bind:value={speed} min="1" max="8" step="0.01"
		on:change="{manager.restart()}" />
	{speed}
</label>

<svelte:component this={PiFive} {sketch}
	bind:engine
	bind:this={manager} />

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
