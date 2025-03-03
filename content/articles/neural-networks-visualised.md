---
title: Non-Linearity and Neural Networks
draft: false
---
When I was first learning about neural networks, I was shown a geometric intuition which I thought was a fairly commonplace explanation. I have since learned that this explanation seems to be relatively unmentioned, mainly known by those who already have strong geometric intuitions about linear algebra. This is aimed at people who were in my shoes, wanting to get a more intuitive understanding of neural networks, but without any knowledge in linear algebra or statistics.

# Neural Networks
There's a lot of resources online that explain neural networks in mathematical detail, but for our purposes it's going to be enough to just think about them in very simple terms.

The problem we aim to solve with neural networks is taking some set of inputs, whatever those are, and producing some related set of outputs. In particular, I'll focus on the problem of classification, but everything discussed here applies to regression, which I'll discuss at the end.

## Classification
In classification the set of outputs correspond to classes we'd like to identify the inputs to. We might have a set of images, and want to classify which images are of dogs, and which are of cats. Then our inputs are images, and our outputs are just $\{dog,\, cat\}$. 

The key observation is that, in practice, we have to record data somehow. These recordings are some measure of features that we care about. Things like age, hair colour, length, anything goes. Since we can measure these features, we end up giving them numbers.

For images, the data we record are usually red, green and blue colour values for every pixel in the image. For an image with n pixels, the end result would be some recorded list of numbers. For example,
$$
\boldsymbol{x} = \begin{bmatrix}
r_1, \\ g_1, \\ b_1, \\
r_2, \\ g_2, \\ b_2, \\
\vdots \\
r_n, \\ g_n, \\ b_n \end{bmatrix}
= \begin{bmatrix}
255, \\ 102, \\ 3, \\
18, \\ 255, \\ 89, \\
\vdots \\
121, \\ 182, \\ 96 \end{bmatrix}
$$
which when put in the right order on a computer screen give us an image. We call these lists of numbers *vectors*.

Mathematically, when we are taking some group of inputs to some group of outputs, we call this a function. While it sounds somewhat abstract to say we have a function that takes images as input, remember that all images are recorded, so we are just working with numbers as we're used to.

We're also used to working with vectors already. Any time you plot something with $(x, y, z)$ coordinates, those coordinates are a list of three numbers, and so are a vector. Vectors form vector spaces, which is where the key geometric intuition is going to come from.

For simplicity, say we're looking to classify a bunch of flowers. We might have recorded the lengths and widths of the flower's petals. If we put them in a list, and plot that list like $(x, y)$ coordinates, then we get the following geometric picture.

![[static/neural-networks-visualised/petal_lengths.png]]

Above, red, green and blue coloured dots represent the three different types of flower measured. This alone provides a pretty good way to identify flowers, we could just draw a boundary and say anything below it is definitely a red flower.

![[static/neural-networks-visualised/linearly-separable-data.png]]

When we find ourselves able to draw such a line, we call the data *linearly separable*. This is eventually what any neural network classifier aims to do; find a way to draw a line separating the data into two classes.

Before we move on, it'll be helpful to see that instead of drawing a line, we can instead move the points so that they're split by the x-axis. In particular, linear motions are rotations, scaling, reflections, and translations of the points. Mathematically, these operations are exactly the same as drawing a line, and are even written the same way.

![[static/neural-networks-visualised/IrisLinearTransformLoop.gif]]

While some datasets are linearly separable, unfortunately, most are not. This is why we have to use neural networks, after all. A simple example is data that plots into a spiral shape.

![[static/neural-networks-visualised/SpiralLinear1_ManimCE_v0.17.2.png]]

No matter how we try to linearly transform the data, there's just no way we can split the two colours across the x-axis.

![[static/neural-networks-visualised/SpiralLinear1Loop.gif]]

If you're not convinced, it'll also help to look at the problem algebraically. Drawing a single decision boundary has two degrees of freedom; how high up the line is (which we'll call $b$), and how steep the line's slope is (which we'll call $w$). The decision boundary then has the equation

$$
\boldsymbol{y} = w\boldsymbol{x} + b
$$

where we vary the values of $w$ and $b$ to pick where the line goes. $\boldsymbol{x}$ and $\boldsymbol{y}$ are in bold as a conventional way to write vectors. While we just want to think of them as numbers right now, if we replaced them with vectors, the mathematics works out the same.

As we mentioned before, drawing this line is the same as linearly transforming the points so that this line is the new x-axis. Any sequence of transformations would then be the composition of these linear equations, where we take the output $\boldsymbol{y}$ of the previous transformation, and put it in as the $\boldsymbol{x}$ of the following one. In symbols, the equation for the second line/transformation is

$$
\begin{align*}
\boldsymbol{y}_2 &= w_2 \boldsymbol{y}_1 + b_2 \\
&= w_2 \left( w_1\boldsymbol{x} + b_1 \right) + b_2 \\
&= w_2 w_1\boldsymbol{x} + w_2 b_1 + b_2
\end{align*}
$$
Notice however, that $w_2 w_1$ is just a number, as is $w_2 b_1 + b_2$. We'll relabel them $W$ and $B$, giving

$$
\boldsymbol{y}_2 = W \boldsymbol{x} + B
$$

which is the same form as the equation we started with! What we're showing here is that composing two linear transformations is equivalent to some other linear transformation. Indeed, for any number of linear transformations, we can just pair up consecutive ones until we end up with just a single linear transformation. Then, for any network of linear transformations, 