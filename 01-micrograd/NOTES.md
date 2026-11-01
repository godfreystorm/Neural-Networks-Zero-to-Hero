# The spelled-out intro to neural networks and backpropagation: building micrograd

**Status:** in progress (stopped at: ____ in the video)
**Rebuild from memory:** Value class (autograd engine) → `from_memory.py`

## What I built

- Tested derivatives by hand with the nudge method: change the input by a tiny `h`, see how much the output moves, divide by `h`.
- Plotted `3x² - 4x + 5` and saw the parabola. That one has a single input, so it's a curve I can draw.
- Moved to `d = a*b + c` with `a = 2, b = -3, c = 10`. It has 3 inputs, so it's not a parabola. You nudge each input separately instead.
- Started the `Value` class: it wraps one number (`self.data`) and has a `__repr__` so it prints nicely.

## What confused me

- How fast Karpathy got the slopes. He'd say "obviously it's 1" or "obviously it's 2" and I didn't see how. Turns out he just knows the shortcuts. I can get the same answers by working out the nudge myself.
- Why make a class that just holds the same number? Not answered yet. It should make sense once he adds `grad` and `__add__`/`__mul__`.
- What `__repr__` was for. It decides how the object itself (`a`) shows up when printed, not `a.data`.

## What clicked

- `h` is the size of the nudge. Dividing by it gives "change in output per 1 unit of nudge."
- **Adding:** nudge `c` and only `c` moved, so `d` goes up by exactly `h`. `h / h` = slope of **1**. Always.
- **Multiplying:** nudge `b` and `a*(b+h) = ab + ah`. The change is `ah`, so the slope is `ah / h` = `a`. The slope is the *other* number.
- Slopes here: `a` → -3, `b` → 2, `c` → 1. `d` is most sensitive to `a`, but the slope is negative, so nudging `a` up makes `d` go down. That depends on the current values, not a rule.
- Classes = a blueprint, objects = one thing made from it (like player cards). `__init__` fills in the card, `self` is the card being filled. In `a = Value(2.0)`, `self` is `a`.
- In Jupyter, a bare `a` on the last line of a cell displays it (using `__repr__`). In a .py file I need `print(a)`.

## Things I feel are important to remember

- `x + y` means `x.__add__(y)`, the left side is `self`, and the right side is `other`:

```python
def __add__(self, other):
    out = Value(self.data + other.data)
    return out
x + y
```

- `ClassName(...)` **= make a new object = run** `__init__`**.** That's true whether you type `Value(2.0)` yourself or it happens inside `__add__`.

```python
class Value:
    def __init__(self, data, _children=()):
        self.data = data
        self._prev = set(_children)

    def __repr__(self):
        return f"Value(data={self.data})"

    def __add__(self, other):
        # creates a NEW Value (runs __init__ for it), which remembers self and other as its _prev
        out = Value(self.data + other.data, (self, other)) 
        return out
```

## What I changed (my own experiment)

- 
