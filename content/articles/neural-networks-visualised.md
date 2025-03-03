---
title: Visualising Neural Networks
draft: false
date: 2023-03-11
---

> [!info]-
> This is my attempt to transcribe a talk I gave for the Boole prize lecturing competition at my university back when I was in 1st year. As such, I feel the need to put a disclaimer here, as it was put together overnight, so some of the graphics aren't accurate, and what's written is just my best memory of what I talked about, and was asked about.

When I was first learning about neural networks, I was only just entering secondary school, and didn't know much mathematics. During my attempts to learn about the subject, I came across a geometric intuition for what goes on inside a neural network, which I found incredibly helpful, and which still guides much of how I think about the topic today.

I have since learned that this explanation seems to go relatively unmentioned, mainly known by those who already have strong geometric intuitions about linear algebra. This is aimed at the people who were instead in my shoes, wanting to get a more intuitive understanding of what goes on in neural networks, but lacking the mathematical knowledge for this interpretation to jump out at me.

As a result, this is going to be assume very little knowledge about either maths or machine learning, but I hope it's a useful anyways to even one person starting to learn about deep learning today.

# Neural Networks
There's a lot of resources online that explain neural networks in mathematical detail, which I won't cover here. For our purposes, it's going to be enough to just think about them in very simple terms.

The problem we solve with neural networks is one where we take some set of inputs, whatever those are, and need to produce some related set of outputs. We expect the network to learn how to do this by showing it examples of input and output pairs, and have it somehow represent the pattern between those examples.

In particular, I'll focus on the problem of classification, but everything discussed here applies to regression, which I'll discuss at the end.

# Classification
In classification the set of outputs correspond to classes we'd like to identify the inputs to. We might have a set of images, and want to classify which images are of dogs, and which are of cats. Then our inputs are images, and our outputs are just the appropriate class from the set $\{dog,\, cat\}$. 

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

Mathematically, when we are taking some group of inputs to some group of outputs, we call this a function. While it sounds somewhat abstract to say we have a function that takes images as input, remember that all images are recorded, so we are just working with numbers as we're used to. Writing our data as vectors let us define our functions like so, where each column of nodes corresponds to some list of numbers, with as many numbers as there are nodes: 

![[Pasted image 20250303204307.png]]

We're also used to working with vectors already. Any time you plot something with $(x, y, z)$ coordinates, those coordinates are a list of three numbers, and so are a vector. Vectors form vector spaces, which is where the key geometric intuition is going to come from.

# Linear Separability

For simplicity, say we're looking to classify a bunch of flowers. We might have recorded the lengths and widths of the flower's petals. If we put them in a list, and plot that list like coordinates $(x, y) = (petal\_length, petal\_width)$, then we get the following geometric picture.

![[static/neural-networks-visualised/petal_lengths.png]]

Above, red, green and blue coloured dots represent the three different types of flower measured. This alone provides a pretty good way to identify, say, red flowers; we could just draw a boundary and say anything below it is definitely red.

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

As mentioned before, drawing this line is the same as linearly transforming the points so that this line is the new x-axis. Any sequence of transformations would then be the composition of these linear equations, where we take the output $\boldsymbol{y}$ of the previous transformation, and put it in as the $\boldsymbol{x}$ of the following one. In symbols, the equation for the second line/transformation is

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

which is the same form as the equation we started with! What we're showing here is that composing two linear transformations is equivalent to some other linear transformation. Indeed, for any number of linear transformations, we can just pair up consecutive ones until we end up with just a single linear transformation. Then, any network of linear transformations ends up collapsing into just a single one

![[static/neural-networks-visualised/linear-network.png]]

Clearly then, any network built up from just *linear* transformations isn't going to be enough to tackle data that isn't linearly separable. The solution is then simple, we just add non-linearity!

# Non-Linearity
A non-linear function is just any function which can't be written in the form $\boldsymbol{y} = w\boldsymbol{x} + b$, which is most of them. We'll take a fairly commonly used one, the $tanh$ function, but any choice would work here. In two dimensions, the $tanh$ function looks like the following transformation

![[HyperbolicTangentLoop.gif]]

This squishing and stretching of the space are the kinds of motion that weren't allowed in linear transformations.

Applying this to classification, we'll look at the problem algebraically again first. This time, we'll pass our linear transformation through $tanh$ before passing it to the next linear transformation. In particular, composition now looks like 

$$
\boldsymbol{y} = w_2 \ tanh(w_1 \boldsymbol{x} + b_1) + b_2
$$

which can't be simplified any further. Then we really do get 4 degrees of freedom here, $w_1$, $w_2$, $b_1$ and $b_2$! With more compositions, we then get a more complex function, and so should be able to do more complex transformations.

To verify this, let's look at things geometrically again. 

![[static/neural-networks-visualised/SpiralLinear1_ManimCE_v0.17.2.png]]

If I was allowed to do whatever transformations I wanted to this graph, I might think about grabbing onto the tails of the spiral, and pulling them straight so I have a line of green, and a line of red points. We might expect that a neural network solving this problem will try the same thing, so let's try training one!

We're going to make a model with this structure:
![[NeuralNetwork_ManimCE_v0.17.2 1.png]]
which we interpret as an input vector of 2 coordinates, which gets transformed 3 times. After each transformation, we can see that we still only have two nodes, which correspond to two coordinates. This is so that we can plot out each step of the transformation.

We then [set the model to train](https://www.youtube.com/watch?v=Ilg3gGewQ5U), during which the model tweaks all the degrees of freedom we put in earlier thanks to the non-linear function keeping all our $w$ and $b$ separate. Eventually the model will settle on a transformation which does a good job of splitting the data with the x-axis, and we can see the result of that training here:
![[SpiralNonLinear1Loop.gif]]

Amazingly, it actually does a pretty good job of eventually dragging out those tails into lines, just like we would have!

It's also interesting to note here that we can see the influence of the $tanh$ function in the curved shape the data takes after the first transformation, as well as how we have to zoom in between the grid lines for $-1$ and $1$.

# Practical Consequences

The same basic ideas hold even for incredibly complex networks, and can help us interpret and understand how these models work, and for what problems. For example, one might interpret the [autoencoder](https://en.wikipedia.org/wiki/Autoencoder#Interpretation), often presented as a compression technique, in geometric terms instead.

We record data with a lot of numbers, but for basically any problem we're interested in, the data doesn't just show up just *anywhere* (otherwise there'd be no pattern in our data!), but instead only on some constrained subset of the space (in science, this is typically assumed to be a [[manifolds|manifold]]).

The space we record the data in is then just some containing space, and the real data can be described by embedding the recorded data into a smaller dimensional space that's "on the surface" we're interested in. We can see this even in the toy example of the spiral. While the spiral is embedded in a two dimensional space, all we need to know to classify a point is which arm the points lie on, which forms a one dimensional space.

To be exact, we could draw a curve through the middle of the points, and give each point a coordinate $t$ on this line, just wherever it's nearest to the line. The tip of the green arm would be $t = 1$, the centre of the spiral would be $t = 0$, and the tip of the red arm would be $t = -1$. The classification problem would then simplify to knowing whether, on this lower dimensional space, the point's coordinate $t < 0$, meaning it's red, or $t > 0$, meaning it's green.

The architecture of an autoencoder places a bottleneck, where output of the encoder layer is some smaller dimensional space. The layers preceding this then would be a sequence of transformations projecting the recorded points onto the embedding space outputted by the encoder. Just like in the spiral case, this also implies that these coordinates are more "meaningful", by some sense, than the raw data. On the spiral, $t$ represented a measure of how far along each arm a point was. In an encoder, the output coordinates can mean all sorts of things, for example, the mood someone's face shows.

# Activation Functions
In academic literature, the non-linear function we've put between our linear transformations are called *activation functions*. Seeing the influence of the activation function in the result above, we might ask how exactly we should choose activation functions?

Generally speaking, there are only a few situations where a particular activation function is chosen, and that's usually at the output layer. For example, one might choose to pass the output of a neural network through the sigmoid function
$$
\sigma(\boldsymbol{x}) = \frac{1}{1 + e^{-\boldsymbol{x}}}
$$
which turns a range of numbers between $(-\infty, \infty)$ into a probability distribution between $[0, 1]$. This is useful for classification, as it lets us assign probabilities representing the confidence our model has in a particular answer.

Outside of this, the choice of activation function is largely decided by the process of training. Back-propagation, the process which lets the network learn, requires computing the derivatives of the activation functions, usually many times. The simpler it is to compute the derivative, the faster we can train models, and so generally researchers pick one of the simplest non-linear functions possible, the rectified linear unit (ReLU)
$$
relu(\boldsymbol{x}) = \begin{cases} 0, & x \le 0 \\ \boldsymbol{x} & x > 0\end{cases}
$$
which has the incredibly simple derivative
$$
\frac{d}{d\boldsymbol{x}}\ relu(x) = \begin{cases} 0, & x \le 0 \\ 1 & x > 0 \end{cases}
$$
## Side Note: ReLU
Geometrically, this function might seem *too* simple to be useful:
![[ReluLoop.gif]]

We're throwing away the majority of the information, and leaving only a single quadrant!

This is only true in two dimensions. Consider the ReLU in three dimensions, we'd leave an entire volume bounded by the $xy$, $xz$ and $yz$ planes. This volume only gets larger and larger as we add more and more dimensions, but we only project data onto $(n-1)$ dimensional subspaces, so we only lose one dimension of data. Typically, neural networks operate with data many hundreds of dimensions large. The volume would then be raised to the power of that many hundred of dimensions, so we actually leave a lot of the data alone.

This geometric picture makes ReLU a surprisingly delicate operation, allowing the model to "fold" only the data it needs to. In practice, this might actually be how real models operate; being mostly linear, except near those decision boundaries where it non-linearly moves data. This is possibly observed by Raja Giryes, Guillermo Sapiro, and Alex M. Bronstein in [their paper on neural network weight distributions](https://arxiv.org/pdf/1504.08291), where they write:
> In addition, we observed that a potential main goal of the training of the network is to treat the class boundary points, while keeping the other distances approximately the same

This might be an explanation for the effectiveness of ReLU, which I argue here follows intuitively from a geometric view.