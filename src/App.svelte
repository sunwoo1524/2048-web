<script>
    import { onMount } from "svelte";
	import Tile from "./components/Tile.svelte"
    import { slide } from "svelte/types/runtime/transition";

	const up = 0;
	const down = 1;
	const left = 2;
	const right = 3;

	const w = 4;
	const h = 4;

	let grid = [
		[0, 0, 0, 0],
		[0, 0, 0, 0],
		[0, 0, 0, 0],
		[0, 0, 0, 0]
	];

	function addRandomTile() { // 빈칸중 랜덤으로 2 또는 4 타일 넣는 함수
		let empty_tiles = [];
		let random_tile;

		grid.forEach((row, y) => row.forEach((el, x) => {
			if (el == 0) empty_tiles.push([x, y])
		}));

		random_tile = Math.floor(Math.random() * empty_tiles.length);
		grid[empty_tiles[random_tile][1]][empty_tiles[random_tile][0]] = Math.floor(Math.random() * 2) == 0 ? 2 : 4;
	}

	function slideTiles(_grid, direction) { // 슬라이드하여 빈칸 없애는 함수
		_grid.forEach((row, y) => {
			_grid[y] = row.filter(el => el != 0);
		});

		_grid.forEach((row, y) => {
			let k = h - row.length; // 채울 빈칸의 개수
			
			for (let i = 0; i < k ; i++) {
				if (direction == left) {
					_grid[y].push(0);
				} else {
					_grid[y].unshift(0);
				}
			}
		});

		return _grid;
	}

	// function rotateGrid(_grid, direction) { // 그리드를 시계방향 도는 반시계방향 90도로 회전시키는 함수
	// 	// TODO
	// }

	function control(direction) { // 화살표 키 누르면 작동
		let changed_grid = JSON.parse(JSON.stringify(grid));

		if (direction == left || direction == right) {
			changed_grid = slide(changed_grid, direction);
		} else {
			let temp = [];
			for (let y = 0; y < changed_grid.length; y++) {
				temp.push([]);
				for (let x = 0; x < changed_grid[0].length; x++) {
					temp[y].push(changed_grid[x][y]);
				}
			}
			
			temp = slide(temp, direction == top ? left : right);
			console.table(temp);
		}

		// addRandomTile();
	}

	onMount(() => {
		document.onkeydown = event => {
			if (event.key == "ArrowUp") {
				control(up);
			} else if (event.key == "ArrowDown") {
				control(down);
			} else if (event.key == "ArrowLeft") {
				control(left);
			} else if (event.key == "ArrowRight") {
				control(right);
			}
		}

		addRandomTile();
		addRandomTile();
	});
</script>

<div class="board">
	{#each grid as row}
	<div class="row">
		{#each row as el}
		<Tile num={el}></Tile>
		{/each}
	</div>
	{/each}
</div>

<style>
	.board {
		width: min-content;
		background-color: lightgray;
		padding: 5px;
		border-radius: 5px;
	}
	.row {
		display: flex;
	}
</style>