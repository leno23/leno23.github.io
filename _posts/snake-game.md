---
title: 一份Canvas实现的贪吃蛇小游戏
index_img: https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e7f403a518464510ab78b143de93dd28~tplv-k3u1fbpfcp-watermark.image
tags: [Canvas]
categories: [Canvas]
comment: 'valine'
---

[预览地址](https://codepen.io/leno23/pen/MWXxMMv)

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta
      http-equiv="X-UA-Compatible"
      content="IE=edge"
    />
    <meta
      name="viewport"
      content="width=device-width, initial-scale=1.0"
    />
    <title>Document</title>
    <style>
      #canvas {
        background-color: grey;
      }
    </style>
  </head>
  <body>
    <canvas id="canvas"></canvas>
    <script>
      // 定义游戏的尺寸
      const width = 400
      const height = 400
      const cellSize = 20

      // 定义贪吃蛇的方向
      const directions = {
        LEFT: { x: -1, y: 0 },
        RIGHT: { x: 1, y: 0 },
        UP: { x: 0, y: -1 },
        DOWN: { x: 0, y: 1 }
      }

      // 初始化 canvas 元素
      const canvas =
        document.getElementById('canvas')
      canvas.width = width
      canvas.height = height
      const ctx = canvas.getContext('2d')

      // 定义贪吃蛇的初始位置和方向
      let snake = [{ x: 0, y: 0 }]
      let direction = directions.RIGHT

      // 定义游戏的状态
      let isPlaying = false
      let score = 0

      // 定义食物的位置
      let food = { x: 0, y: 0 }

      // 在指定位置绘制一个正方形
      function drawCell(x, y) {
        ctx.beginPath()
        ctx.rect(
          x * cellSize,
          y * cellSize,
          cellSize,
          cellSize
        )
        ctx.fill()
      }

      // 绘制贪吃蛇
      function drawSnake() {
        snake.forEach((cell) =>
          drawCell(cell.x, cell.y)
        )
      }

      // 绘制食物
      function drawFood() {
        drawCell(food.x, food.y)
      }

      // 随机生成一个食物
      function generateFood() {
        food.x = Math.floor(
          Math.random() * (width / cellSize)
        )
        food.y = Math.floor(
          Math.random() * (height / cellSize)
        )
      }

      // 检查贪吃蛇是否吃到食物
      function checkFood() {
        if (
          snake[0].x === food.x &&
          snake[0].y === food.y
        ) {
          score++
          snake.push({
            ...snake[snake.length - 1]
          })
          generateFood()
        }
      }
      // 更新贪吃蛇的位置
      function updateSnake() {
        const head = { ...snake[0] }

        head.x += direction.x
        head.y += direction.y

        // 检查贪吃蛇是否碰到了边界
        if (
          head.x < 0 ||
          head.y < 0 ||
          head.x >= width / cellSize ||
          head.y >= height / cellSize
        ) {
          // 如果碰到了边界，游戏结束
          isPlaying = false
          return
        }

        // 检查贪吃蛇是否碰到了自己
        if (
          snake
            .slice(1)
            .some(
              (cell) =>
                cell.x === head.x &&
                cell.y === head.y
            )
        ) {
          // 如果碰到了自己，游戏结束
          isPlaying = false
          return
        }

        // 更新贪吃蛇的位置
        snake.unshift(head)
        snake.pop()
      }

      // 绘制游戏画面
      function draw() {
        // 清空画布
        ctx.clearRect(0, 0, width, height)

        // 绘制贪吃蛇
        drawSnake()

        // 绘制食物
        drawFood()
      }

      // 游戏的主循环
      function tick() {
        if (isPlaying) {
          updateSnake()
          checkFood()
          console.log(12)
          draw()
        } else {
          alert('game over')
          return
        }
        //   requestAnimationFrame(tick);
        setTimeout(tick, 500)
      }

      // 初始化游戏
      function init() {
        generateFood()
        isPlaying = true
        tick()
      }

      // 监听键盘事件，更新贪吃蛇的方向
      document.addEventListener(
        'keydown',
        (event) => {
          console.log(event.key)
          switch (event.key) {
            case 'ArrowUp':
            case 'w':
              direction = directions.UP
              break
            case 'ArrowDown':
            case 's':
              direction = directions.DOWN
              break
            case 'ArrowLeft':
            case 'a':
              direction = directions.LEFT
              break
            case 'ArrowRight':
            case 'd':
              direction = directions.RIGHT
              break
          }
        }
      )

      // 开始游戏
      init()
    </script>
  </body>
</html>

```