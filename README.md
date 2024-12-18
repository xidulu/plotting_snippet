# Let jax use float64

```
from jax.config import config; config.update("jax_enable_x64", True)
```

# Align all y labels

```
fig.align_ylabels(ax[:, 0])
```

# Converting float to latex string
```
def float_to_str(x):
    number_sci = f"{x:.1e}"
    base, exponent = number_sci.split('e')
    exponent = int(exponent)  # Convert exponent to integer for proper formatting
    # Proper LaTeX representation
    latex_sci_notation = r"${{{}}}\times10^{{{}}}$".format(base, exponent)
    return latex_sci_notation
```

# plotting_snippet
Some common, easy-to-forget, code snippets I used for making plots.

As always, if you are in a notebook, start with
```
%config InlineBackend.figure_format='retina'
```
to make your plots more eye-friendly

## Smooth the lines

```python
from scipy.ndimage import uniform_filter1d

def smooth(x):
    return uniform_filter1d(x, 10)
```

## Remove upper and right frames

```python
for a in ax:
    a.spines['top'].set_visible(False)
    a.spines['right'].set_visible(False)
```

## Move the legends around

```python
legend = ax[0].legend(loc='center left', bbox_to_anchor=(1, 0.5))
```

## Having a separate figure for legends

```python
legend1 = ax[0].legend(ncol=3) # Legend you got from other plots
# Create a new figure just for the legend
fig_leg = plt.figure(figsize=(2, 0.5))  # You can adjust the size as needed
ax_leg = fig_leg.add_subplot(111)

label_order = [0, 1, 2, 3] # If you wanna switch order for the legneds
leg = ax_leg.legend(
    handles=[legend1.legendHandles[i] for i in label_order],
    # You could also manually write the names for the labels
    labels=[legend1.get_texts()[i].get_text() for i in label_order], 
    ncol=4, loc='center'
)

# Adjust the linewidth if you want
for line in leg.get_lines():
    line.set_linewidth(2)

# Turn off the axis
ax_leg.axis("off")
fig_leg.savefig('./my_legends.pdf', bbox_inches='tight', pad_inches=0.01)
```

Then in latex, you can simply do
```
\begin{subfigure}[t]{\linewidth}
\centering
 \includegraphics[width=\linewidth]{figs/my_legends.pdf}
\caption*{}
\end{subfigure} \\[-3.8ex]
```

## Adjust the layouts (e.g. spacing of subplots)

```python
fig, ax = plt.subplots(3, 6, figsize=(6.75, 3), sharex='col')
bottom = 0.1
top = 0.85
left = 0.075
right = 0.98
fig.subplots_adjust(wspace=0.6, hspace=0.4, left=left, right=right, bottom=bottom, top=top)
```

## Set the figure ratio as 0.618

```python
a.set_box_aspect(aspect=1 / 1.618)
```

## You wanna plot some arrows?

```python
def plot_arrows(X, Y, ax, c, alphas=[0.2, 0.6, 1.0]):
    for i in range(len(X)):
        prop = dict(arrowstyle="simple,head_width=0.5,head_length=0.6",
            shrinkA=0,shrinkB=0,facecolor=c, edgecolor=c, alpha=alphas[i],lw=3)
        ax.annotate("", xy=(X[i, 1], Y[i, 1]), xytext=(X[i, 0],Y[i, 0]), arrowprops=prop,
                   color=c, alpha=alphas[i])
```

## You wanna use scientific notation for the y axis labels and only have the coefficient on the axis but exponent on the top


```python
import matplotlib.ticker as mtick
 # If you would like 1x10-5, set it as False if like 1e10-5
formatter = mtick.ScalarFormatter(useMathText=True)
formatter.set_scientific(True)
# I don't know what (-1, 1) is for :D
formatter.set_powerlimits((-1,1)) 
ax[0].yaxis.set_major_formatter(formatter)
# Change the position and font size of the exponent
ax[0].get_yaxis().get_offset_text().set_position((-0.1,0))
ax[0].get_yaxis().get_offset_text().set_fontsize(8)
```


## Set the xticks ticks and labels

```python
ax[0].xaxis.set_ticks([0,250,500])
ax[0].xaxis.set_ticklabels([0, '25K', '50K'])
```

## Change the fontsize of the tick parameter

```python
ax[0].tick_params(axis='x', labelsize=8)

# or

ax.tick_params(axis='both', labelsize=8)
```

## Move the ylabel away from the axis

```python
ax.set_ylabel('ELBO', fontsize=18, labelpad=12) # The labelpad does the job
```

## Where to get good colors?

A good website to get colors from the same "family", e.g. you could use the same color family for methods for different variations. 
https://colorbrewer2.org/

Or if you want to extract colors from the colormap from matplotlib, you could
```python
from matplotlib import cm
my_colors = cm.tab10([0,1,2,4])
```
