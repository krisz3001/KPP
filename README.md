# Snake

## Programterv
Kígyók kígyóznak.

## Felhasznált technológiák
- HTML
- CSS
- [JavaScript]()

## Telepítés
A program használatához csak egy böngészőre van szükség.

## Érdekesebb megoldások
```javascript
	<html>
    <head>
        <title>Kígyók kígyóznak</title>
		<style>
			#jatek{
				margin: auto;
				text-align: center;
				margin-top: 5vh;
			}
			canvas{
				border: 2px black solid;
			}
			h1, p{
				font-family: Arial;
			}
			#restart{
				border: 1px solid black;
				width: 100px;
				margin: auto;
				font-family: Arial;
				font-weight: bold;
				background-color: darkgray;
			}
			#restart:hover{
				border: 1px solid black;
				width: 100px;
				margin: auto;
				font-family: Arial;
				font-weight: bold;
				background-color: white;
				cursor: pointer;
			}
			p{
				font-weight: bold;
			}
	</style>
    </head>
	<body>
		<div id="jatek">
			<canvas id="board" width="600" height="600"></canvas>
			<h1 id="score">Score: 0</h1>
			<h1 id="hs">Highscore: 0</h1>
			<p>Press &ltP&gt to reset highscore</p>
			<div id="restart" onclick="return window.location.reload(true)">
				<p>Restart</p>
			</div>
			<p>or press &ltR&gt</p>
		</div>
	</body>
    <script>
        const board = document.getElementById("board")
        const board_ctx = board.getContext("2d")

        let snake = [
            {x: 200, y: 200},
            {x: 180, y: 200}
        ]
        
        let dx = 20
        let dy = 0
        let turning = false
        let score = 0
        let hs = 0
        if(localStorage.getItem('hs') != null) hs = localStorage.getItem('hs')
        document.getElementById('hs').innerHTML = "Highscore: " + hs
        let food_x
        let food_y
		let obstacle_x
		let obstacle_y
		let obstacles = []
        main()
        food()
		obstacle()
        function drawPart(part){
            board_ctx.fillStyle = 'lightgreen'
            board_ctx.strokeStyle = 'darkgreen'
            board_ctx.fillRect(part.x, part.y, 20, 20)
            board_ctx.strokeRect(part.x, part.y, 20, 20)
        }
        function draw(){
            snake.forEach(drawPart)
        }
        function clear(){
            board_ctx.fillStyle = "white"
            board_ctx.strokeStyle = "white"
            board_ctx.fillRect(0, 0, board.width, board.height)
        }
        function move(){
            const head = {x: snake[0].x + dx, y: snake[0].y + dy}
            snake.unshift(head)
            const eaten = snake[0].x === food_x && snake[0].y === food_y
            if(eaten){
                food()
                score+=1
				if(score%5==0) obstacle()
                document.getElementById('score').innerHTML = "Score: " + score
                if(score>hs){
					hs = score
					localStorage.setItem('hs',score)
				}
                document.getElementById('hs').innerHTML = "Highscore: " + hs
            }
            else snake.pop()
        }
        document.addEventListener("keydown", change_direction)
        function change_direction(event){
            const keyPressed = event.keyCode
			if(keyPressed == 82) window.location.reload(true)
			if(keyPressed == 80){
				localStorage.setItem('hs',0)
				document.getElementById('hs').innerHTML = "Highscore: " + 0
			}
            if(turning) return
            turning = true
            if(keyPressed == 37 && dx != 20 || keyPressed == 65 && dx != 20){
                dx = -20
                dy = 0
            }
            if(keyPressed == 38 && dy != 20 || keyPressed == 87 && dy != 20){
                dx = 0
                dy = -20
            }
            if(keyPressed == 39 && dx != -20 || keyPressed == 68 && dx != -20){
                dx = 20
                dy = 0
            }
            if(keyPressed == 40 && dy != -20 || keyPressed == 83 && dy != -20){
                dx = 0
                dy = 20
            }
        }
        function end(){
            for(let i = 4; i < snake.length; i++){
                if(snake[i].x == snake[0].x && snake[i].y == snake [0].y) return true
            }
			for(let i = 0; i < obstacles.length; i++){
				let xy = obstacles[i].split(",")
				if(snake[0].x == xy[0] && snake[0].y == xy[1]) return true
			}
            return snake[0].x < 0 || snake[0].x > board.width - 20 || snake[0].y < 0 || snake[0].y > board.height - 20
        }
        function random_food(min,max){
            return Math.round((Math.random()*(max-min)+min)/20)*20
        }
        function food(){
            food_x = random_food(0, board.width - 20)
            food_y = random_food(0, board.height - 20)
            snake.forEach(function snake_eaten(part){
                const has_eaten = part.x == food_x && part.y == food_y
				let obstacle_place = false
				for(let i = 0; i < obstacles.length; i++){
					let xy = obstacles[i].split(",")
					if(xy[0] == food_x && xy[1] == food_y) return true
				}
                if(has_eaten || obstacle_place) food()
            })
        }
        function drawFood(){
            board_ctx.fillStyle = 'red'
            board_ctx.strokeStyle = 'darkred'
            board_ctx.fillRect(food_x, food_y, 20, 20)
            board_ctx.strokeRect(food_x, food_y, 20, 20)
        }
		function obstacle(){
            obstacle_x = random_food(0, board.width - 20)
            obstacle_y = random_food(0, board.height - 20)
            snake.forEach(function snake_eaten(part){
                const has_eaten = part.x == obstacle_x && part.y == obstacle_y
				let obstacle_place = false
				let food_place = food_x == obstacle_x && food_y == obstacle_y
				for(let i = 0; i < obstacles.length; i++){
					let xy = obstacles[i].split(",")
					if(xy[0] == obstacle_x && xy[1] == obstacle_y) return true
				}
                if(has_eaten || obstacle_place || food_place) obstacle()
            })
			obstacles.push(obstacle_x + ',' + obstacle_y)
        }
        function drawObstacle(){
			for(let i = 0; i < obstacles.length; i++){
				let xy = obstacles[i].split(",")
				board_ctx.fillStyle = 'purple'
				board_ctx.strokeStyle = 'black'
				board_ctx.fillRect(xy[0], xy[1], 20, 20)
				board_ctx.strokeRect(xy[0], xy[1], 20, 20)
			}
        }
        function main(){
            if(end()) return
            turning = false
            setTimeout(function onTick()
            {
				clear()
				move()
				draw()
				drawFood()
				drawObstacle()
				main()
            },100)
        }
    </script>
</html>

```
