
# What's Special About Communications Algorithms

Digital communications is a very interesting area of study.  

### Hard Encoder/Decoder

Differential encoding is a technique commonly used in noncoherent modulation.  At the bit-level, 
the algorithm is extremely simple.  Given a sequence of input bits, ${ x_{i} }$ the differentially
encoded stream of bits is defined by the following recurrence relation:

\begin{equation}
    y_{i} = y_{i-1} \wedge x_{i}
\end{equation}

The differential decoder runs in the receiver and recovers the original sequence from the encoded sequence as follows:

\begin{equation}
    z_{i} = y_{i-1} \wedge y_{i}
\end{equation}

So, why does this work?  Let's prove to ourselves that differentially decoding a differentially encoded bit sequence.
For each $i$,

\begin{align}
    z_{i} &= y_{i-1} \wedge y_{i} \\
          &= y_{i-1} \wedge \left( y_{i-1} \wedge x_{i} \right) \\
          &= x_{i} \wedge \left( y_{i-1} \wedge y_{i-1} \right) \\
          &= x_{i} \wedge 0 \\
          &= x_{i}
\end{align}

It's also very important to notice that this holds true for when two consecutive bits are flipped.  

There are a variety of ways to implement a differential encoder/decoder.  I'm really after designing my algorithms in efficient and elegant ways.  One thing I want to try to avoid is being tied to a particular type of input.  I don't want to implement separate differential encoders when the bits are coming in one at a time and when they are given to me all at once.  I want to capture the *essence* of the algorithm in a flexible and reusable module.  And if I can do this using functional programming techniques, then that's just icing on the cake.  


The first thing I need is some way to maintain state during the course of the algorithm.  Here's one option.


```python
class Register(object):
    def __init__(self,val=None):
        self.val = val
    def push(self, new_value):
        self.val = new_value
    def peek(self):
        return self.val
```

Okay, now let's try to capture the essence of a differential encoder.  We are going to use a concept from functional programming called *currying* to do it.  All this really means is taking advantage of the mathematical fact that a function of two arguments
can be interpreted as a function of one argument that returns another function of one argument.  Let's look at a simple example before getting to the differential encoder.  Consider a simple `add` function that takes two numbers and adds them together:


```python
def add1(x,y):
    return x + y
```


```python
def add2(x):
    def inner(y):
        return x + y
    return inner

```


```python
def diff_enc():
    state = Register(0)
    def step(x=None):
        output = None
        if x == None:
            state.push(0)
        else:
            curr   = state.peek()
            output = curr ^ x
            state.push(output)
        return output
    return step
```


```python
def diff_dec():
    state = Register(0)
    def step(x=None):
        output = None
        if x == None:
            state.push(0)
        else:
            curr   = state.peek()
            output = curr ^ x
            state.push(x)
        return output
    return step
```


```python
enc = diff_enc()
dec = diff_dec()
```


```python
bits_in = list(map(enc,[0,1,1,0,0,1,1,0]))
```


```python
bits_out = list(map(dec,bits_in))
bits_out
```




    [0, 1, 1, 0, 0, 1, 1, 0]




```python
foo()
```


```python
list(map(foo,[1,1,0,1,0,1,1,0,1,0,0]))
```




    [1, 0, 0, 1, 1, 0, 1, 1, 0, 0, 0]




```python

```
