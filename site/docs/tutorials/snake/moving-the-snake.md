import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

# Moving the Snake

To move the snake, you have to change the X and Y position of every body-part of the snake.

For this, you can start at the end of the body and give this part the values of the part ahead.
Since the final piece to get's it's values "stolen" is the head, the head seems to disappear for very brief moment.

After this is done, you simply move the head in the direction of the snake.
Since all of this happens, before the snake is rendered, it appears as a fluid motion.

This is how the logic looks like:

![](images/snake_move_logic.webp)

Keep in mind: This is not what the player sees. This is:

![](images/snake_move_rendered.webp)


## Moving the Body

<Tabs
    groupId="code-language"
    defaultValue="language-typescript"
    values={[
        {label: 'AssemblyScript', value: 'language-typescript'},
        {label: 'C / C++', value: 'language-cpp'},
        {label: 'Rust', value: 'language-rust'},
        {label: 'Go', value: 'language-go'},
    ]}>

<TabItem value="language-typescript">

To achieve the first step (moving the body, excluding the head), a simple loop is all you need:

```typescript
    public update() : void {
        for (let i = this.body.length-1; i > 0; i--) {
            this.body[i] = new Point(this.body[i-1].X, this.body[i-1].Y)
        }
    }
```

Don't forget to call the new function in the main-loop:

```diff
export function update(): void {
+   snake.update()
    snake.draw()
}
```

Now if you execute this, you'd notice that you can't see much. In fact, you might see the snake for a short moment before the head is all that's left.

![](images/snake_move_head_only.webp)

</TabItem>

<TabItem value="language-cpp">

// TODO

</TabItem>

<TabItem value="language-rust">

// TODO

</TabItem>

<TabItem value="language-go">

To achieve the first step (moving the body, excluding the head), a simple loop is all you need:

```go
func (s *Snake) Update() {
	for i := len(s.Body) - 1; i > 0; i-- {
		s.Body[i] = s.Body[i-1]
	}
}
```

Don't forget to call the new function in the main-loop:

```diff
//go:export update
func update() {
+	snake.Update()
	snake.Draw()
}
```

Now if you execute this, you'd notice that you can't see much. In fact, you might see the snake for a short moment before the head is all that's left.

![](images/snake_move_head_only.webp)

</TabItem>

</Tabs>

## Moving the Head

<Tabs
    groupId="code-language"
    defaultValue="language-typescript"
    values={[
        {label: 'AssemblyScript', value: 'language-typescript'},
        {label: 'C / C++', value: 'language-cpp'},
        {label: 'Rust', value: 'language-rust'},
        {label: 'Go', value: 'language-go'},
    ]}>

<TabItem value="language-typescript">

This isn't hard either. Simple add the add the direction to the current head. And then make sure the head stays within the boundaries:

```diff
    public update() : void {
        for (let i = this.body.length-1; i > 0; i--) {
            this.body[i] = new Point(this.body[i-1].X, this.body[i-1].Y)
        }
+
+       this.body[0].X = (this.body[0].X + this.direction.X) % 20
+       this.body[0].Y = (this.body[0].Y + this.direction.Y) % 20
+       if (this.body[0].X < 0) {
+           this.body[0].X = 19
+       }
+       if (this.body[0].Y < 0) {
+           this.body[0].Y = 19
+       }
+   }
```

</TabItem>

<TabItem value="language-cpp">

// TODO

</TabItem>

<TabItem value="language-rust">

// TODO

</TabItem>

<TabItem value="language-go">

This isn't hard either. Simple add the add the direction to the current head. And then make sure the head stays within the boundaries:

```diff
func (s *Snake) Update() {
	for i := len(s.Body) - 1; i > 0; i-- {
		s.Body[i] = s.Body[i-1]
	}
+
+	s.Body[0].X = (s.Body[0].X + s.Direction.X) % 20
+	s.Body[0].Y = (s.Body[0].Y + s.Direction.Y) % 20
+	if (s.Body[0].X < 0) {
+		s.Body[0].X = 19
+	}
+	if (s.Body[0].Y < 0) {
+		s.Body[0].Y = 19
+	}
}
```

</TabItem>

</Tabs>

That's it. Now you should see the snake running from left to right. Maybe a little too fast, though.

![Moving Snake (fast)](images/snake-motion-fast.webp)


## Slowing Down

By default WASM-4 runs at 60 FPS. This means your little snake moves 60 fields in each second. That is 3 times the whole screen.
There are several ways to slow the snake down.

The easiest way is probably to count the frames and update the snake only every X frames.

<Tabs
    groupId="code-language"
    defaultValue="language-typescript"
    values={[
        {label: 'AssemblyScript', value: 'language-typescript'},
        {label: 'C / C++', value: 'language-cpp'},
        {label: 'Rust', value: 'language-rust'},
        {label: 'Go', value: 'language-go'},
    ]}>

<TabItem value="language-typescript">

For this, you'd need a new variable. You can call it whatever you like, just be sure you know what it's purpose is.

```diff
 var snake = new Snake()
+var frameCount = 0
```

This variable in main.ts keeps track of all frames so far. Just increase it's value in the main-update function:

```diff
export function update(): void {
+   frameCount++
    snake.update()
    snake.draw()
}
```

Now all you need is to check if the passed frames are dividable by X:

```diff
export function update(): void {
    frameCount++
-   snake.update()
+   if (frameCount % 15 == 0) {
+      snake.update()
+   }
    snake.draw()
}
```

That's it. Your snake should be quite a bit slower now. This reduces the snake from 60 units per second to 4 units per second (60/15 = 4).

![Moving Snake (slow)](images/snake-motion-slow.webp)

</TabItem>

<TabItem value="language-cpp">

// TODO

</TabItem>

<TabItem value="language-rust">

// TODO

</TabItem>

<TabItem value="language-go">

For this, you'd need a new variable. You can call it whatever you like, just be sure you know what it's purpose is.

```diff
var (
	snake = &Snake{
		Body: []image.Point{
			{X: 2, Y: 0},
			{X: 1, Y: 0},
			{X: 0, Y: 0},
		},
		Direction: image.Pt(1, 0),
	}
+	frameCount = 0
)
```

This variable in main.go keeps track of all frames so far. Just increase it's value in the main-update function:

```diff
//go:export update
func update() {
+	frameCount++
	snake.Update()
	snake.Draw()
}
```

Now all you need is to check if the passed frames are dividable by X:

```diff
//go:export update
func update() {
	frameCount++
-	snake.Update()
+	if frameCount%15 == 0 {
+		snake.Update()
+	}
	snake.Draw()
}
```

That's it. Your snake should be quite a bit slower now. This reduces the snake from 60 units per second to 4 units per second (60/15 = 4).

![Moving Snake (slow)](images/snake-motion-slow.webp)

</TabItem>

</Tabs>
