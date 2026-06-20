# Yuchan's Portfolio
## 1. Perceptron
**Why did I decide to make it?**<br>
While reading *AI Snake Oil*, a book that critically examines the limitations and overblown claims about AI, I became curious about how AI works beneath the surface. This led me to research the origins of AI, where I discovered Frank Rosenblatt's invention of the perceptron - an early attempt to mimic the human neuron.<br><br>

**How does the perceptron work?**<br>
The perceptron itself can be explained as a function, which receives a specified number of inputs, and outputs either 1 or 0.<br>
All the inputs are *weighted*, which means they are individually multiplied by different numbers. After *weighting* them, they are summed. This can be expressed as the function, a = w<sub>1</sub>x<sub>1</sub> + w<sub>2</sub>x<sub>2</sub> + ... + b = w.x + b, where b is *bias*.<br>
Based on the value of a, the *activation function (step function)* y converts a to either 0 or 1.<br><br>
Provided this information, here is what I built to represent AND and OR gate.<br>
```python
def step_function(x1, w1, x2, w2, b):
    if x1 * w1 + x2 * w2 + b > 0:
        return 1
    else:
        return 0

def AND(x1, x2):
    w1, w2, b = 1, 1, -1
    return step_function(x1, w1, x2, w2, b)

def OR(x1, x2):
    w1, w2, b = 1, 1, 0
    return step_function(x1, w1, x2, w2, b)
```
After this, I tried to build self learning algorithm, so that I don't have to specify the values of w1, w2, and b.
```python
def step_function(x1, w1, x2, w2, b):
    if x1 * w1 + x2 * w2 + b > 0:
        return 1
    else:
        return 0

class Prediction:
    def __init__(self, data):
        self.w1 = 0
        self.w2 = 0
        self.b = 0
        num_data = len(data)
        count = 0
        while count < num_data:
            x1 = data[count][0]
            x2 = data[count][1]
            output = step_function(x1, self.w1, x2, self.w2, self.b)
            error = data[count][2] - output
            if error == 0:
                count += 1
                continue
            elif error == 1:
                if x1 == 1:
                    self.w1 += 0.1
                if x2 == 1:
                    self.w2 += 0.1
                if x1 == 0 and x2 == 0:
                    self.b += 0.1
            else:
                if x1 == 1:
                    self.w1 -= 0.1
                if x2 == 1:
                    self.w2 -= 0.1
                if x1 == 0 and x2 == 0:
                    self.b -= 0.1
            count = 0
```
Unfortunately, I stuck in an infinite loop because the value b (bias) was not updated when it should be.
```python
def step_function(x1, w1, x2, w2, b):
    if x1 * w1 + x2 * w2 + b > 0:
        return 1
    else:
        return 0

class Prediction:
    def __init__(self, data):
        self.w1 = 0
        self.w2 = 0
        self.b = 0
        num_data = len(data)
        count = 0
        while count < num_data:
            x1 = data[count][0]
            x2 = data[count][1]
            output = step_function(x1, self.w1, x2, self.w2, self.b)
            error = data[count][2] - output
            if error == 0:
                count += 1
                continue
            elif error == 1:
                if x1 == 1:
                    self.w1 += 1
                if x2 == 1:
                    self.w2 += 1
                self.b += 1
            else:
                if x1 == 1:
                    self.w1 -= 1
                if x2 == 1:
                    self.w2 -= 1
                self.b -= 1
            count = 0
```
This code worked perfectly well to represent AND, OR, NAND, NOR gate.<br><br>

**What did I learn from it?**<br>
From my actual mistake, I learnt that if bias is not updated everytime in perceptron, the code will be stuck in an infinite loop. Moreover, there was one gate that I couldn't represent: XOR gate, which means it's impossible to draw a straight line to divide the points. Therefore, connecting perceptrons to make XOR gate was my next step.
```python
def step_function(x1, w1, x2, w2, b):
    if x1 * w1 + x2 * w2 + b > 0:
        return 1
    else:
        return 0

def NOT(value):
    if value == 1:
        return 0
    elif value == 0:
        return 1

class Prediction:
    def __init__(self, data):
        self.w1 = 0
        self.w2 = 0
        self.b = 0
        num_data = len(data)
        count = 0
        while count < num_data:
            x1 = data[count][0]
            x2 = data[count][1]
            output = step_function(x1, self.w1, x2, self.w2, self.b)
            error = data[count][2] - output
            if error == 0:
                count += 1
                continue
            elif error == 1:
                if x1 == 1:
                    self.w1 += 1
                if x2 == 1:
                    self.w2 += 1
                self.b += 1
            else:
                if x1 == 1:
                    self.w1 -= 1
                if x2 == 1:
                    self.w2 -= 1
                self.b -= 1
            count = 0

    def operate(self, x1, x2):
        return(step_function(x1, self.w1, x2, self.w2, self.b))

data = [
    (1,1,1),
    (0,1,0),
    (1,0,0),
    (0,0,0)
]
AND = Prediction(data)
data = [
    (1,1,1),
    (0,1,1),
    (1,0,1),
    (0,0,0)
]
OR = Prediction(data)

def XOR(x1, x2):
    first_output = AND.operate(x1, NOT(x2))
    second_output = AND.operate(NOT(x1), x2)
    return(OR.operate(first_output, second_output))
```
This successfully represented XOR gate.<br>
To better represent *real perceptron*, I used NAND gate instead of NOT:
```python
def step_function(x1, w1, x2, w2, b):
    if x1 * w1 + x2 * w2 + b > 0:
        return 1
    else:
        return 0

def NOT(value):
    if value == 1:
        return 0
    elif value == 0:
        return 1

class Prediction:
    def __init__(self, data):
        self.w1 = 0
        self.w2 = 0
        self.b = 0
        num_data = len(data)
        count = 0
        while count < num_data:
            x1 = data[count][0]
            x2 = data[count][1]
            output = step_function(x1, self.w1, x2, self.w2, self.b)
            error = data[count][2] - output
            if error == 0:
                count += 1
                continue
            elif error == 1:
                if x1 == 1:
                    self.w1 += 1
                if x2 == 1:
                    self.w2 += 1
                self.b += 1
            else:
                if x1 == 1:
                    self.w1 -= 1
                if x2 == 1:
                    self.w2 -= 1
                self.b -= 1
            count = 0

    def operate(self, x1, x2):
        return(step_function(x1, self.w1, x2, self.w2, self.b))

data = [
    (1,1,1),
    (0,1,0),
    (1,0,0),
    (0,0,0)
]
AND = Prediction(data)
data = [
    (1,1,1),
    (0,1,1),
    (1,0,1),
    (0,0,0)
]
OR = Prediction(data)
data = [(1,1,0),
        (0,1,1),
        (1,0,1),
        (0,0,1)
]
NAND = Prediction(data)

def XOR(x1, x2):
    return(AND.operate(OR.operate(x1,x2), NAND.operate(x1,x2)))
```
This led me to realize that a single perceptron can only solve linearly separable problems. By connecting multiple perceptrons, we can solve more complex problems — and this is exactly the foundation of MLP.
