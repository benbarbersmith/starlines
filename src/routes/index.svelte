<script lang="ts">
	let mapWidth: number,
		mapHeight: number,
		prevMapWidth: number,
		prevMapHeight: number,
		rows: number,
		cols: number;
	let hexes: Hex[] = [];
	let currentHex: Hex;

	const radius = 40;
	const offset = (Math.sqrt(3) * radius) / 2;

	type Hex = { points: string; id: string };
	type Point = { x: number; y: number };

	function generateHex(point: Point, radius: number): Hex {
		let points = [];
		for (let theta = 0; theta < Math.PI * 2; theta += Math.PI / 3) {
			let x = point.x + radius * Math.sin(theta);
			let y = point.y + radius * Math.cos(theta);

			points.push(`${x},${y}`);
		}

		return { id: `hex-${hexes.length}`, points: points.join(' ') };
	}

	function draw() {
		const origin = { x: -radius, y: -offset };
		for (let row = 0; row < rows; row += 1) {
			for (let col = 0; col < cols - (rows % 2); col += 1) {
				const point = {
					x: origin.x + 40 + offset * col * 2,
					y: origin.y + 40 + offset * row * Math.sqrt(3)
				};

				if (row % 2 !== 0) {
					point.x += offset;
				}

				hexes.push(generateHex(point, radius));
			}
		}
		hexes = [...hexes];
	}

	$: {
		if (prevMapWidth != mapWidth || prevMapHeight != mapHeight) {
			prevMapWidth = mapWidth;
			prevMapHeight = mapHeight;
			rows = Math.ceil(mapHeight / radius);
			cols = Math.ceil(mapWidth / (offset * 2));
			hexes = [];
			draw();
		}
	}
</script>

<div class="w-full h-full p-4 relative">
	<div
		class="w-full h-full border border-gray-100 border-double"
		bind:clientWidth={mapWidth}
		bind:clientHeight={mapHeight}
	>
		<svg
			xmlns="http://www.w3.org/2000/svg"
			id="map"
			class="w-full h-full fill-gray-900 stroke-gray-600"
		>
			{#each hexes as hex}
				<polygon
					class="hover:fill-gray-700 focus:fill-gray-400 focus:outline-none"
					points={hex.points}
					id={hex.id}
					on:focus={() => (currentHex = hex)}
					on:mouseover={() => (currentHex = hex)}
				/>
			{/each}
		</svg>
	</div>
	{#if currentHex}
		<div class="absolute right-5 bottom-4 font-mono">
			<p>{currentHex.id}</p>
		</div>
	{/if}
</div>
