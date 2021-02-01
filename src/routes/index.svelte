<script>
	import { onMount } from "svelte";

	let P5;

	onMount(async () => {
		const module = await import("../components/PiFive.svelte");
		P5 = module.default;
	});

	let noiseScale = 0.0625;
	let height = 55;

	const sketch = (it) => {
		const width = 400;
		const height = 400;
		const pointsNumber = 128;
		const points = Array(pointsNumber);
		let g = null;
		const randomPoint = () => {
			const shouldSkip = (tupledArg) => {
				const pixels = tupledArg[0];
				const x = tupledArg[1] | 0;
				const y = tupledArg[2] | 0;
				const i = ((y * height + x) * 4) | 0;
				return ~~pixels[i + 3] < 127;
			};
			let x_1 = 0;
			let y_1 = 0;
			while (
				((x_1 = ~~Math.floor(it.random(width)) | 0),
				((y_1 = ~~Math.floor(it.random(height)) | 0),
				shouldSkip([g.pixels, x_1, y_1])))
			) {}
			return it.createVector(x_1, y_1);
		};
		it.setup = () => {
			it.createCanvas(width, height, "p2d");
			it.background(200);
			it.noiseSeed(10);
			it.noStroke();
			it.fill(0);
			g = it.createGraphics(width, height, "p2d");
			g.textSize(300);
			g.text("G", it.width / 32, it.height / 1.3);
			g.loadPixels();
			for (let i_1 = 0; i_1 <= pointsNumber - 1; i_1++) {
				points[i_1] = randomPoint();
			}
		};
		it.draw = () => {
			it.image(g, 0, 0);
			points.forEach((p) => {
				it.circle(p.x, p.y, 0.5);
			});
			for (let i_2 = 0; i_2 <= pointsNumber - 1; i_2++) {
				let p_1 = points[i_2];
				p_1 = p_1.add(
					it.createVector(
						2 *
							(it.noise(
								p_1.x * noiseScale,
								p_1.y * noiseScale,
								0
							) *
								2 -
								1),
						2 *
							(it.noise(
								p_1.x * noiseScale,
								p_1.y * noiseScale,
								9
							) *
								2 -
								1)
					)
				);
				points[i_2] = p_1;
				if (it.random() < 0.0625) {
					points[i_2] = randomPoint();
				}
			}
		};
	};

	let engine;
</script>

<svelte:head>
	<title>Sapper project template</title>
</svelte:head>

<label>
	Noise scale
	<input type="range" bind:value={noiseScale} min="{0.0625/4}" max="{0.0625*4}" step="0.01"
		on:change="{engine.frameRate(engine.frameRate()-1)}" />
	{noiseScale}
</label>

<label>
	Height
	<input type="range" bind:value={height} min="100" max="1000" step="0.01" />
	{height}
</label>

<svelte:component this={P5} {sketch} bind:engine={engine} />

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
