# Lack: language guide for AI systems

Purpose of this document: teach an AI model to write correct Lack code.
Lack is a small, real programming language. It is NOT Python, JavaScript,
Ruby or Rust, even though it looks similar to Python. Do not guess syntax.
If something is not listed here, Lack probably does not have it.

## Quick summary

- File extensions: `.lack` or `.lc`. Run with `lack run file.lack`.
- Interpreted. Dynamically typed. Variables need no declaration.
- Blocks use `{ }`. There are no semicolons and no indentation rules.
- Comments: `#` or `//`.
- Logic: `&&`, `||`, `not`. Not `and`, `or`, `!` is allowed too.
- Output: `print(...)`. Interpolation inside strings: `"Hi {name}"`.
- Graphics, sound, 3D and keyboard/mouse are built in.

## Common mistakes (do NOT write these)

| Wrong (Python/JS style)            | Correct Lack                          |
|------------------------------------|---------------------------------------|
| `print(f"Hi {name}")`              | `print("Hi {name}")`                  |
| `x = 5;`                           | `x = 5`                               |
| `if x > 1 and y < 2:`              | `if x > 1 && y < 2 { ... }`           |
| `def add(a, b):`                   | `fn add(a, b) { return a + b }`       |
| `for i in range(10):`              | `for i in 0..10 { ... }`              |
| `"abc"[1:3]` (slicing)             | not supported, loop with `s[i]`       |
| `lst.append(x)`                    | `lst.push(x)`                         |
| `d.get("k")`                       | `d.has("k")` then `d["k"]`            |
| `try: ... except:`                 | not supported                         |
| `match x:` / `switch`              | use `if` / `else if`                  |
| `import math`                      | `sqrt`, `abs`, `sin_deg` are built in |
| `None`, `True`, `False`            | `nil`, `true`, `false`                |
| `print("a", end="")`               | not supported                         |
| `x += 1` inside a condition        | assignments are statements only       |

## Syntax

    name = "Lack"              # variable (no declaration keyword)
    count = 0
    count += 1                 # also -= *= /= %=
    ok = true                  # true, false, nil

Numbers: `12` is an int, `3.5` is a float. `10 / 4` is `2` (int division).
`10.0 / 4` is `2.5`. Use `%` for remainder.

Strings: double quotes only. `{expr}` inside a string inserts the value.
Escapes: `\n \t \" \\`. A literal brace is written `\{` or `\}`.

Conditions (no parentheses needed):

    if score > 10 {
        print("high")
    } else if score > 5 {
        print("mid")
    } else {
        print("low")
    }

Loops:

    while lives > 0 { lives -= 1 }
    for i in 1..5 { print(i) }     # 1 2 3 4   (end excluded)
    for i in 1..=5 { print(i) }    # 1 2 3 4 5 (end included)
    for x in my_list { print(x) }
    for k in my_map { print(k, my_map[k]) }   # iterates keys
    for c in "abc" { print(c) }               # iterates characters
    # inside loops: break and continue work

Functions:

    fn area(w, h) {
        return w * h
    }
    print(area(3, 4))          # 12
    # a function without return gives nil. Closures and recursion work.

Lists:

    nums = [1, 2, 3]
    nums.push(4)               # append
    last = nums.pop()          # remove and return last
    nums[0] = 10               # set by index; nums[-1] is the last item
    print(len(nums), nums.len())

Maps:

    ages = {ann: 31, "ben": 27}    # bare names become string keys
    ages["cy"] = 40
    if ages.has("ann") { print(ages["ann"]) }
    print(ages.keys(), ages.values())
    # Reading a missing key is a runtime error. Check with has() first.

Classes:

    class Hero {
        name = "Hero"
        hp = 100
        fn init(n) {            # optional constructor, runs on Hero(...)
            name = n
        }
        fn hurt(amount) {
            hp -= amount        # fields are used directly inside methods
            if hp < 0 { hp = 0 }
        }
    }
    h = Hero("Ann")
    h.hurt(30)
    print(h.name, h.hp)        # Ann 70

## Built-in functions (complete list)

| Name | Meaning |
|---|---|
| `print(a, b, ...)` | print values separated by spaces, then newline |
| `len(x)` | length of list, map or string |
| `str(x)` | convert to text |
| `int(x)` | convert text or float to whole number (error if text is not a number) |
| `float(x)` | convert to decimal |
| `sqrt(x)`, `abs(x)` | square root, absolute value |
| `min(a, b)` | smaller of two whole numbers |
| `type(x)` | "int", "float", "bool", "str", "list", "map", "object", "function", "nil" |
| `push(list, x)` | same as list.push(x) |
| `input("prompt")` | read a line from keyboard as text |
| `random(lo, hi)` | whole number from lo to hi, both included |
| `sleep(ms)` | pause milliseconds |
| `http_get(url)` | download page text (needs curl installed) |
| `links(html)` | list of href values in HTML text |
| `sin_deg(x)`, `cos_deg(x)` | sine/cosine, x in degrees |
| `rgb(r, g, b)` | color value from 0-255 components. Required for all drawing |

List methods: `push`, `pop`, `len`. Map methods: `has`, `keys`, `values`, `len`.
String methods: `len`, `upper`, `lower`.

## Graphics (2D)

Always open a window first. Draw inside a `while present()` loop.

    window(640, 480, "Title")
    while present() {                  # false once the window is closed
        clear(rgb(20, 20, 40))         # clear the frame
        rect(x, y, w, h, rgb(80, 200, 255))
        circle(cx, cy, r, rgb(255, 210, 0))
        text(20, 20, "Score: {score}", 24, rgb(255, 255, 255))
        if button(20, 100, 200, 44, "Start") { print("clicked") }
        if key_down("left") { x -= 5 }
        m = mouse()                    # [x, y] or nil
        if mouse_down() { print("pressed") }
        if key_down("escape") { break }
    }

Key names for key_down: a-z, 0-9, space, enter, escape, tab, backspace,
left, right, up, down, shift, ctrl.

`text_width(s, size)` gives width in pixels. `font("file.ttf")` picks a font.
`image(path, x, y)` or `image(path, x, y, w, h)` draws a PNG/JPEG.

## 3D graphics

    window(640, 480, "3D")
    while present() {
        clear(rgb(135, 190, 240))
        camera3d(0, 3, -8, 0, -10)            # x y z, yaw degrees, pitch degrees
        plane3d(0, 8, rgb(70, 140, 70))       # y, half-size, color
        box3d(-1, 0, 0, 2, 2, 2, rgb(230, 80, 60))   # x y z, w h d, color
        tri3d(0,0,0, 1,0,0, 0,1,0, rgb(255,255,0))   # 3 corners, color
    }

Yaw 0 looks along +z. Positive pitch looks up. Save a frame: `save_image("a.png")`.

## Keyboard and mouse control (sends input to the whole computer)

    key_press("enter")        # press and release
    type_text("hello")
    mouse_move(100, 200)      # screen position
    mouse_click()             # left click

## Sound

    tone(440, 500)                    # play 440 Hz for 500 ms, in background
    sound_wait()                      # wait for queued tones to finish
    save_tone("beep.wav", 880, 300)   # write WAV file

## Modules

    import util                       # util.lack or util/main.lack next to this file
    include "helpers.lack"            # run another file by path
    import github.com/user/repo       # download a GitHub repo (needs git)

## C library calls (Linux only at present)

    extern "libm.so.6" fn cos(float) -> float
    print(cos(0.0))

## Errors

Errors stop the program and print: `error: line N: message`.
Common ones: undefined variable, division by zero, index out of range,
missing map key, wrong argument count, open a window first.
There is no try/catch.

## Limits (do not use these)

- No static type checking. No `match`/`switch`, `try`/`catch`, `async`.
- No default or keyword arguments. No slicing. No file read/write (only
  `save_tone` and `save_image`). No package registry.
- Deep recursion beyond about 2000 calls is an error.

## Complete examples (tested)

### 1. Hello, functions and loops

    fn greet(name) {
        print("Hello " + name)
    }
    greet("World")
    for i in 1..=3 {
        print("count", i)
    }

### 2. Class and map

    class Player {
        name = ""
        gold = 0
        fn init(n) { name = n }
        fn earn(x) { gold += x }
    }
    scores = {}
    p = Player("Ann")
    p.earn(15)
    scores[p.name] = p.gold
    print(scores)            # {Ann: 15}

### 3. Window with a clickable counter

    window(480, 320, "Clicker")
    score = 0
    while present() {
        clear(rgb(18, 18, 30))
        text(20, 20, "Score: {score}", 28, rgb(255, 255, 255))
        if button(20, 100, 200, 44, "Click") {
            score += 1
            tone(440 + score * 10, 60)
        }
        if key_down("escape") { break }
    }
    print("Final score: {score}")

## Added in 0.9.0 (tested)

Graphics and game helpers:

- `round_rect(x, y, w, h, radius, color)`: filled rounded rectangle.
- `line(x0, y0, x1, y1, thickness, color)`: straight line.
- `icon(name, x, y, size)`: pixel icon. Names: "heart", "star", "coin", "key", "sword", "potion".
- `collide(ax, ay, aw, ah, bx, by, bw, bh)`: true when two boxes overlap.
- `save_text(path, text)` and `load_text(path)`: read and write a text file. `load_text` returns nil if the file does not exist.

Window icon: if a file named `icon.ico` sits next to the program, the window uses it as its icon (Windows only).

AI (needs the ANTHROPIC_API_KEY environment variable; the program uses the system `curl`):

- `ai(question)`: returns the AI's reply as a string.
- `ai_chat(list)`: the list alternates your messages and the replies, and ends with your message. The AI sees the whole conversation.

Example:

    history = ["What is 2 + 2?"]
    reply = ai_chat(history)
    print(reply)

Both functions raise an error when the key is missing or the request fails.

## Machine learning (0.9.0, tested)

A small neural network with one hidden layer (tanh) and a linear output, trained with gradient descent on squared error:

- `mlp_train(xs, ys, hidden, epochs, learning_rate)`: xs and ys are lists of rows. Returns a model.
- `mlp_predict(model, xs)`: returns the output row for each input row.

Example (learns y = 2x + 1):

    xs = [[0], [1], [2], [3]]
    ys = [[1], [3], [5], [7]]
    model = mlp_train(xs, ys, 8, 3000, 0.1)
    print(mlp_predict(model, [[1.5]]))   # about [[4]]

Use learning rates around 0.1 to 0.2. A rate of 0.5 diverged to NaN on the XOR example.

This is not PyTorch: there is no GPU, no automatic differentiation and no tensor type. It is for small models.

## Checklist before answering with Lack code

1. Every block uses `{ }`. No colons, no semicolons.
2. Logic uses `&&`, `||`, `not`.
3. Strings use double quotes and `{}` for interpolation.
4. Drawing calls use `rgb(...)`, and graphics come after `window(...)`.
5. Only built-ins from the tables above are used.
