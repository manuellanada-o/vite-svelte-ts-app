<script lang="ts">
    const blockDimensions = 32;
    const width = window.innerWidth;
    const height = window.innerHeight;
    const xGrid =
        ~~(width / blockDimensions) % 2 === 0
            ? ~~(width / blockDimensions)
            : ~~(width / blockDimensions) - 1;
    const yGrid =
        ~~(height / blockDimensions) % 2 === 0
            ? ~~(height / blockDimensions)
            : ~~(height / blockDimensions) - 1;
    let direction = "";
    let gameStarted = false;

    const getDirection = (e: any) => {
        const snakeY = parseInt(snake[0].split(",")[0]);
        const snakeX = parseInt(snake[0].split(",")[1]);
        const targetY = parseInt(e.target.id.split(",")[0]);
        const targetX = parseInt(e.target.id.split(",")[1]);
        const getYDistance = Math.abs(snakeY - targetY);
        const getXDistance = Math.abs(snakeX - targetX);
        
        if (getYDistance > getXDistance) {
            if (snakeY > targetY) {
                direction = "up";
            } else {
                direction = "down";
            }
        } else {
            if (snakeX > targetX) {
                direction = "left";
            } else {
                direction = "right";
            }
        }
    };

    const getRandomApple = (): string => {
        let randomY;
        let randomX;
        do {
            randomY = Math.floor(Math.random() * yGrid);
            randomX = Math.floor(Math.random() * xGrid);
        } while (snake.includes(`${randomY},${randomX}`));
        return `${randomY},${randomX}`;
    };

    let snake = [`${yGrid / 2},${xGrid / 2}`];
    let apple = [getRandomApple()];
    let intervalId: number | undefined = undefined;
    let speedInterval = 500;

    const startInterval = (speedInterval: number) => {
        gameStarted = true;
        intervalId = setInterval(() => {
            switch (direction) {
                case "left":
                    if (parseInt(snake[0].split(",")[1]) - 1 < 0) {
                        const head = `${parseInt(snake[0].split(",")[0])},${xGrid - 1}`;
                        const tail = snake.length > 1 ? snake.slice(0, -1) : [];
                        snake = [head, ...tail];
                    } else {
                        const head = `${parseInt(snake[0].split(",")[0])},${parseInt(snake[0].split(",")[1]) - 1}`;
                        const tail = snake.length > 1 ? snake.slice(0, -1) : [];
                        snake = [head, ...tail];
                    }
                    break;
                case "right":
                    if (parseInt(snake[0].split(",")[1]) + 1 > xGrid - 1) {
                        const head = `${parseInt(snake[0].split(",")[0])},0`;
                        const tail = snake.length > 1 ? snake.slice(0, -1) : [];
                        snake = [head, ...tail];
                    } else {
                        const head = `${parseInt(snake[0].split(",")[0])},${parseInt(snake[0].split(",")[1]) + 1}`;
                        const tail = snake.length > 1 ? snake.slice(0, -1) : [];
                        snake = [head, ...tail];
                    }
                    break;
                case "up":
                    if (parseInt(snake[0].split(",")[0]) - 1 < 0) {
                        const head = `${yGrid - 1},${parseInt(snake[0].split(",")[1])}`;
                        const tail = snake.length > 1 ? snake.slice(0, -1) : [];
                        snake = [head, ...tail];
                    } else {
                        const head = `${parseInt(snake[0].split(",")[0]) - 1},${parseInt(snake[0].split(",")[1])}`;
                        const tail = snake.length > 1 ? snake.slice(0, -1) : [];
                        snake = [head, ...tail];
                    }
                    break;
                case "down":
                    if (parseInt(snake[0].split(",")[0]) + 1 > yGrid - 1) {
                        const head = `0,${parseInt(snake[0].split(",")[1])}`;
                        const tail = snake.length > 1 ? snake.slice(0, -1) : [];
                        snake = [head, ...tail];
                    } else {
                        const head = `${parseInt(snake[0].split(",")[0]) + 1},${parseInt(snake[0].split(",")[1])}`;
                        const tail = snake.length > 1 ? snake.slice(0, -1) : [];
                        snake = [head, ...tail];
                    }
                    break;
            }
            if (snake[0] === apple[0]) {
                snake.push(apple[0]);
                apple = [getRandomApple()];
                clearInterval(intervalId);
                speedInterval =
                    speedInterval - 25 < 50 ? 50 : speedInterval - 25;
                startInterval(speedInterval);
            }
        }, speedInterval);
    };

    window.addEventListener("keydown", (e) => {
        if (!gameStarted) {
            startInterval(speedInterval);
        }
    });

    window.addEventListener("mousedown", (e: any) => {
        if (!gameStarted) {
            startInterval(speedInterval);
        }
    });

    window.addEventListener("keydown", (e) => {
        switch (e.key) {
            case "ArrowUp":
                if (direction !== "down") direction = "up";
                break;
            case "ArrowDown":
                if (direction !== "up") direction = "down";
                break;
            case "ArrowLeft":
                if (direction !== "right") direction = "left";
                break;
            case "ArrowRight":
                if (direction !== "left") direction = "right";
                break;
        }
    });
</script>

<!-- svelte-ignore a11y_no_static_element_interactions -->
<div class="grid">
    {#each Array.from({ length: yGrid }) as _, i}
        <div class="row">
            {#each Array.from({ length: xGrid }) as _, j}
                <!-- svelte-ignore a11y_click_events_have_key_events -->
                <!-- svelte-ignore a11y_no_static_element_interactions -->
                <div
                    id={`${i},${j}`}
                    class={snake.includes(`${i},${j}`)
                        ? "snake"
                        : apple.includes(`${i},${j}`)
                          ? "apple"
                          : "block"}
                    onmousedown={getDirection}
                ></div>
            {/each}
        </div>
    {/each}
</div>
