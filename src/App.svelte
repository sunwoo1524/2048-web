<script>
    import { onMount } from "svelte";
	import Tile from "./components/Tile.svelte"

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

	let gameover = false;

	function addRandomTile() { // 빈칸중 랜덤으로 2 또는 4 타일 넣는 함수
		let empty_tiles = [];
		let random_tile;

		grid.forEach((row, y) => row.forEach((el, x) => {
			if (el == 0) empty_tiles.push([x, y])
		}));

		random_tile = Math.floor(Math.random() * empty_tiles.length);
		grid[empty_tiles[random_tile][1]][empty_tiles[random_tile][0]] = Math.floor(Math.random() * 2) == 0 ? 2 : 4;
	}

	function deleteEmpty(_grid, direction) { // 좌/우로 슬라이드하여 빈칸 없애는 함수
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

	function slideTiles(_grid, direction) {
		// 타일 슬라이드
		if (direction == left || direction == right) {
			_grid = deleteEmpty(_grid, direction); // 좌/우 슬라이드

			// 같은 수의 타일 합치기
			if (direction == left) {
				for (let y = 0; y < h; y++) {
					let before_tile = null;
					for (let x = 0; x < w; x++) {
						if (_grid[y][x] != 0) {
							if (before_tile == _grid[y][x]) {
								_grid[y][x - 1] = _grid[y][x] * 2;
								_grid[y][x] = 0;
							}
							before_tile = _grid[y][x];
						}
					}
				}
			} else {
				for (let y = 0; y < h; y++) {
					let before_tile = null;
					for (let x = w - 1; x >= 0; x--) {
						if (_grid[y][x] != 0) {
							if (before_tile == _grid[y][x]) {
								_grid[y][x + 1] = _grid[y][x] * 2;
								_grid[y][x] = 0;
							}
							before_tile = _grid[y][x];
						}
					}
				}
			}
			//

			_grid = deleteEmpty(_grid, direction); // 한번 더 좌/우 슬라이드
		} else {
			let temp = [];
			for (let y = 0; y < h; y++) { // 행과 열을 바꾸기
				temp.push([]);
				for (let x = 0; x < w; x++) {
					temp[y].push(_grid[x][y]);
				}
			}
			_grid = JSON.parse(JSON.stringify(temp));
			_grid = deleteEmpty(temp, direction == up ? left : right); // 바꾼 grid를 슬라이드
			
			// 같은 수의 타일 합치기
			if (direction == up) {
				for (let y = 0; y < h; y++) {
					let before_tile = null;
					for (let x = 0; x < w; x++) {
						if (_grid[y][x] != 0) {
							if (before_tile == _grid[y][x]) {
								_grid[y][x - 1] = _grid[y][x] * 2;
								_grid[y][x] = 0;
							}
							before_tile = _grid[y][x];
						}
					}
				}
			} else {
				for (let y = 0; y < h; y++) {
					let before_tile = null;
					for (let x = w - 1; x >= 0; x--) {
						if (_grid[y][x] != 0) {
							if (before_tile == _grid[y][x]) {
								_grid[y][x + 1] = _grid[y][x] * 2;
								_grid[y][x] = 0;
							}
							before_tile = _grid[y][x];
						}
					}
				}
			}
			//

			_grid = deleteEmpty(temp, direction == up ? left : right); // 바꾼 grid를 한번더 슬라이드
		}
		//

		// 슬라이드 및 타일 합체 후 행과 열 원상복귀 (위, 아래 키를 누른 경우만)
		if (direction == up || direction == down) {
			let temp = [];
			for (let y = 0; y < h; y++) {
				temp.push([]);
				for (let x = 0; x < w; x++) {
					temp[y].push(_grid[x][y]);
				}
			}
			_grid = temp;
		}
		//

		return _grid;
	}

	function isGameover(_grid) {
		// 타일을 어느 방향으로 슬라이드해도 바뀌지 않는지 검사 (바뀌지 않으면 게임오버임)
		let g_up = slideTiles(JSON.parse(JSON.stringify(_grid)), up);
		let g_down = slideTiles(JSON.parse(JSON.stringify(_grid)), down);
		let g_left = slideTiles(JSON.parse(JSON.stringify(_grid)), left);
		let g_right = slideTiles(JSON.parse(JSON.stringify(_grid)), right);
		let gameover = true;
		for (let y = 0; y < h; y++) {
			for (let x = 0; x < w; x++) {
				if (g_up[y][x] != _grid[y][x] || g_down[y][x] != _grid[y][x] || g_left[y][x] != _grid[y][x] || g_right[y][x] != _grid[y][x]) {
					gameover = false;
				}
			}
		}
		return gameover;
		//
	}

	function control(direction) { // 화살표 키 누르면 작동하는 함수
		let changed_grid = JSON.parse(JSON.stringify(grid)); // grid 깊은 복사
		changed_grid = slideTiles(changed_grid, direction); // 타일 슬라이드

		let is_changed_grid = false;
		for (let y = 0; y < h; y++) { // 기존 grid와 바꾼 grid가 같은지 검사
			for (let x = 0; x < w; x++) {
				if (grid[y][x] != changed_grid[y][x]) {
					is_changed_grid = true;
				}
			}
		}
		
		if (!is_changed_grid) {
			return;
		}

		grid = changed_grid;
		
		addRandomTile(); // 끝난 후 랜덤 타일 하나 추가

		if (isGameover(grid)) {
			gameover = true;
			return;
		}
	}

	onMount(() => {
		document.onkeydown = event => {
			if (!gameover) {
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