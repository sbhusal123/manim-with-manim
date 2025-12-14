# 🎬 Visualizing Mathematics: A High School Course in Manim (v0.19.1)

## 🏫 Pedagogical Philosophy: "Code to Canvas"
This course uses a "Scaffolded Discovery" model designed for students:
1.  **See it:** Show the final animation first.
2.  **Build it:** Write the code step-by-step.
3.  **Break it:** Intentionally change parameters to see what breaks.
4.  **Fix it:** Debugging as a learning tool.

---

## 🛠️ Prerequisites & Setup
**Tools:** Python 3.9+, Manim Community v0.19.1, VS Code (recommended).

```python
# Install manim (Run this in your first cell)
!pip install manim
```

---

## Module 1: The Director's Chair (Scene Architecture)

**Goal:** Overcome "Installation Fear" and render the first shape.
In Manim, the screen is a **Grid**.
* **(0, 0)** is the center.
* **Y-axis:** Goes from -4 (Bottom) to +4 (Top).
* **X-axis:** Goes from -7.1 (Left) to +7.1 (Right).

### 📋 API Reference: The Essentials
| Class / Method | Description | Usage Example |
| :--- | :--- | :--- |
| `Scene` | The canvas for your animation. | `class MyScene(Scene):` |
| `construct()` | The main method where code lives. | `def construct(self):` |
| `add()` | Places an object on screen (instant). | `self.add(circle)` |
| `wait(t)` | Pauses the scene for `t` seconds. | `self.wait(2)` |
| `Circle()` / `Square()` | Basic shapes. | `Circle(radius=2, color=BLUE)` |
| `LEFT`/`RIGHT`/`UP` | Vectors for direction. | `circle.shift(UP * 2)` |

### 💻 Code Example: Basic Shapes
```python
from manim import *

%%manim -qm -v WARNING BasicShapes

class BasicShapes(Scene):
    def construct(self):
        # Create shapes
        circle = Circle(radius=1.5, color=BLUE)
        square = Square(side_length=2, color=YELLOW)

        # Move them apart
        circle.shift(LEFT * 2)
        square.shift(RIGHT * 2)

        # Add them to screen (Static, no animation yet)
        self.add(circle, square)
```

---

## Module 2: Properties, Styling & The `.animate` Syntax

**Goal:** Understanding that animation is just "State A transformed to State B".
We don't just move things; we change their properties (color, stroke, size).

### 🎨 Deep Dive: Object Properties
Every shape has two main components:
1.  **Fill:** The color inside.
2.  **Stroke:** The outline edge.

### 📋 API Reference: Styling & Properties
| Method | Description | Usage Example |
| :--- | :--- | :--- |
| `set_fill(color, opacity)` | Sets the inside color. | `obj.set_fill(RED, opacity=0.5)` |
| `set_stroke(color, width)` | Sets the outline style. | `obj.set_stroke(WHITE, width=4)` |
| `rotate(angle)` | Rotates object (in radians). | `obj.rotate(PI/4)` |
| `scale(factor)` | Resizes the object. | `obj.scale(2.0)` |
| `.animate` | **Crucial:** Accesses the animator. | `obj.animate.set_fill(BLUE)` |
| `Create()` | Draws the object's outline. | `self.play(Create(square))` |
| `Transform(A, B)` | Morphs shape A into shape B. | `self.play(Transform(square, circle))` |

### 💻 Code Example: Styling & Morphing
Notice how we use `.animate.set_fill(...)`. If we just wrote `.set_fill(...)`, it would happen instantly. Adding `.animate` tells Manim to make a movie out of the change.

```python
%%manim -qm -v WARNING StylingAndMorphing

class StylingAndMorphing(Scene):
    def construct(self):
        # Initial State: A clear square with a thick white border
        box = Square(side_length=2)
        box.set_stroke(color=WHITE, width=8)
        box.set_fill(color=BLACK, opacity=0) # Transparent inside

        self.add(box)
        self.wait()

        # Animation 1: Rotate and Fill simultaneously
        self.play(
            box.animate.rotate(PI / 4),       # Rotate 45 degrees
            box.animate.set_fill(BLUE, 0.8),  # Fill with Blue
            box.animate.set_stroke(width=2),  # Thin out the border
            run_time=2
        )
        self.wait()

        # Animation 2: Scale and change color
        self.play(
            box.animate.scale(2),
            box.animate.set_fill(PINK, 1),
            run_time=2
        )
        self.wait()
```

---

## Module 3: Speaking in Math (Advanced Text & LaTeX)

**Goal:** Rendering professional text and smart transformations.
We can target specific parts of a sentence or equation and transform them intelligently.

### 🔠 Text Types
* **`Text`**: Regular sentences. Good for changing fonts/weights.
* **`MathTex`**: Math equations. Renders using LaTeX.

### 📋 API Reference: Advanced Text
| Method | Description | Usage Example |
| :--- | :--- | :--- |
| `MathTex()` | LaTeX math rendering. | `MathTex(r"E = mc^2")` |
| `set_color_by_gradient` | Applies a gradient color. | `text.set_color_by_gradient(RED, YELLOW)` |
| `set_color_by_tex()` | Colors parts matching a string. | `eq.set_color_by_tex("x", RED)` |
| `TransformMatchingTex` | **Magic:** Moves matching parts of an equation to new spots. | `TransformMatchingTex(source, target)` |

### 💻 Code Example: The "Khan Academy" Effect
Watch how the `x` and `y` terms float to their new positions automatically using `TransformMatchingTex`.

```python
%%manim -qm -v WARNING SmartText

class SmartText(Scene):
    def construct(self):
        # 1. Typography Styling
        title = Text("Algebra Magic", font="Arial", weight=BOLD)
        title.to_edge(UP)
        title.set_color_by_gradient(BLUE, PURPLE) # Gradient!
        
        self.play(Write(title))

        # 2. Equation Logic
        # We separate terms so Manim knows they are distinct "blocks"
        eq1 = MathTex("2x", "+", "3y", "=", "12")
        eq2 = MathTex("2x", "=", "12", "-", "3y")

        # Style the equations
        eq1.set_color_by_tex("x", RED)
        eq1.set_color_by_tex("y", YELLOW)
        eq2.set_color_by_tex("x", RED)
        eq2.set_color_by_tex("y", YELLOW)

        # Animation
        self.play(Write(eq1))
        self.wait()

        # The Magic Transform
        # path_arc makes the terms curve as they move, looking more natural
        self.play(
            TransformMatchingTex(eq1, eq2, path_arc=90 * DEGREES),
            run_time=2
        )
        self.wait()
```

---

## Module 4: Grouping & Layout (VGroups)

**Goal:** Managing complexity. Don't animate 100 objects manually; use loops and groups.

### 📋 API Reference: Layout Engine
| Class / Method | Description | Usage Example |
| :--- | :--- | :--- |
| `VGroup()` | Groups multiple VMobjects. | `g = VGroup(circle, square)` |
| `arrange()` | Automatically lays out group members. | `g.arrange(RIGHT, buff=0.5)` |
| `next_to(obj, DIR)` | Places one object beside another. | `label.next_to(dot, UP)` |
| `align_to(obj, DIR)` | Aligns edges of two objects. | `line.align_to(box, LEFT)` |

### 💻 Code Example: Group Styling
```python
%%manim -qm -v WARNING GroupStyling

class GroupStyling(Scene):
    def construct(self):
        # Create 5 circles using a Python list comprehension
        circles = VGroup(*(Circle(radius=0.5) for _ in range(5)))
        
        # Arrange them
        circles.arrange(RIGHT, buff=0.2)
        
        # Apply a gradient across the WHOLE group
        circles.set_color_by_gradient(PINK, YELLOW)
        
        self.add(circles)
        
        # Animate the whole group pulsing
        self.play(
            circles.animate.scale(1.5),
            run_time=1,
            rate_func=there_and_back # Scales up then down
        )
```

---

## Module 5: The Graphing Calculator (2D Graphs)

**Goal:** Visualizing functions and data.

### 📋 API Reference: Graphing
| Class / Method | Description | Usage Example |
| :--- | :--- | :--- |
| `Axes()` | Creates X/Y axis. | `Axes(x_range=[0, 10], y_range=[0, 5])` |
| `coords_to_point()` | Converts graph values to screen (x,y). | `ax.coords_to_point(2, 4)` |
| `plot()` | Draws a function graph. | `ax.plot(lambda x: np.sin(x))` |
| `ValueTracker()` | A variable you can animate. | `v = ValueTracker(0)` |

### 💻 Code Example: Plotting Parabola
```python
%%manim -qm -v WARNING GraphingTime

class GraphingTime(Scene):
    def construct(self):
        axes = Axes(
            x_range=[-3, 3],
            y_range=[-1, 9],
            axis_config={"include_numbers": True}
        )

        # Plot Parabola (y = x^2)
        graph = axes.plot(lambda x: x**2, color=BLUE)
        label = axes.get_graph_label(graph, "x^2")

        self.play(Create(axes), Create(graph))
        self.play(FadeIn(label))
        self.wait()
```

---

## Module 6: Entering the Third Dimension (3D)

**Goal:** Spatial reasoning and camera control.

### 📋 API Reference: 3D Engine
| Class / Method | Description | Usage Example |
| :--- | :--- | :--- |
| `ThreeDScene` | Required parent class for 3D. | `class My3D(ThreeDScene):` |
| `set_camera_orientation` | Sets initial camera angle. | `self.set_camera_orientation(phi=60*DEGREES)` |
| `move_camera()` | Animates the camera angle. | `self.move_camera(theta=45*DEGREES)` |
| `ThreeDAxes()` | Adds Z-axis to the graph. | `ThreeDAxes()` |

### 💻 Code Example: 3D Camera Move
```python
%%manim -qm -v WARNING Cool3D

class Cool3D(ThreeDScene):
    def construct(self):
        axes = ThreeDAxes()
        sphere = Sphere(center=(0,0,1), radius=1, resolution=(20, 20))

        # Set initial view (Top down)
        self.set_camera_orientation(phi=0*DEGREES, theta=0*DEGREES)

        self.add(axes, sphere)

        # Animate camera to side view
        self.play(
            self.camera.animate.set_phi(75 * DEGREES),
            run_time=3
        )
        # Spin around
        self.begin_ambient_camera_rotation(rate=0.5)
        self.wait(3)
```

---

## 🎓 Capstone Project: "Teach a Concept"
**Requirement:** Pick a topic from Biology, History, or Physics and explain it in 30 seconds.
* Must use a **VGroup**.
* Must use **Text** labels.
* Must use a **Transform** animation.