<script>
	import { onMount } from "svelte";

	/** @type {typeof import("../components/PiFive.svelte").default} */
	let PiFive;
	let isRu = false;
	onMount(async () => {
		isRu = /^ru\b/.test(navigator.language);

		const module = await import("../components/PiFive.svelte");
		PiFive = module.default;
	});

	// An interesting finding: p5.noise() repeats at interval of 16, and mirrors at zero.
	// Also, since it's smooth, 15.9 is similar to 16 (or 0, or 32, or -16).
	// This is not because of mirroring (1 and 16-1 are not the same!), this is just smoothness.

	let noiseScale = 1/64;
	let speed = 1/2;
	let letter = "g";
	let avgLen = 16;
	let showLetter = true;
	let thickness = .5;

	const sketch = (p5) => {
		const width = 400;
		const height = 400;

		const pointsNumber = 128;
		const points = Array(pointsNumber);

		const One = p5.createVector(1, 1);

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
			if (showLetter) p5.image(foregroundGraphics, 0, 0);

			points.forEach((p) => {
				p5.circle(p.x, p.y, thickness);
			});
			for (let i = 0; i <= pointsNumber - 1; i++) {
				let p_1 = points[i];

				// A vector between (0,0) and (1,1).
				const velocity = p5.createVector(
					p5.noise(p_1.x * noiseScale, p_1.y * noiseScale, 0),
					p5.noise( p_1.x * noiseScale, p_1.y * noiseScale, 8));

				// Change it to normalized vector between (-1,-1) and (1,1).
				velocity.mult(2).sub(One).normalize();

				// Apply speed.
				velocity.mult(speed);

				// Add velocity to position.
				p_1.add(velocity);
				points[i] = p_1;
				if (p5.random() < speed / avgLen) {
					points[i] = getRandomPointOnForeground();
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

<div class="example">
	<div class="field-row">
		<label for="letter">Letter</label>
		<input id="letter" type="text" bind:value={letter} minlength="{1}" maxlength="{1}" on:change="{manager.restart()}" />
	</div>
	<div class="field-row">
		<input type="checkbox" id="showLetter" bind:checked={showLetter}
			on:change="{manager.restart()}">
		<label for="showLetter">Show the letter</label>
	</div>
</div>
<div class="example">
	<div class="field-row" style="width: 300px">
		<label for="ignore">Noise scale:</label>
		<label for="noise-scale">Less curvy</label>
		<input id="noise-scale" type="range" bind:value={noiseScale} min="{1/128}" max="{1/32}" step="{1/128}"
			on:change="{manager.restart()}" />
		<label for="ignore">Curvy</label>
	</div>
	<div class="field-row" style="width: 300px">
		<label for="speed">Speed:</label>
		<label for="ignore">Steady</label>
		<input id="speed" type="range" bind:value={speed} min="{1/16}" max="1" step="{1/32}"
			on:change="{manager.restart()}" />
		<label for="ignore">ADHD</label>
	</div>
	<div class="field-row" style="width: 300px">
		<label for="avgLen">Average length:</label>
		<label for="ignore">Short</label>
		<input id="avgLen" type="range" bind:value={avgLen} min="4" max="64" step="1"
			on:change="{manager.restart()}" />
		<label for="ignore">Long</label>
	</div>
	<div class="field-row" style="width: 300px">
		<label for="thickness">Thickness:</label>
		<label for="ignore">Thin</label>
		<input id="thickness" type="range" bind:value={thickness} min="{1/8}" max="8" step="{1/8}"
			on:change="{manager.restart()}" />
		<label for="ignore">Thicc</label>
	</div>
</div>

<svelte:component this={PiFive} {sketch}
	bind:engine
	bind:this={manager} />

<!-- <style>
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
</style> -->
