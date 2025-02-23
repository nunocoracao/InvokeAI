---
title: Others
---

# :fontawesome-regular-share-from-square: Others

## **Google Colab**

Stable Diffusion AI Notebook: <a
href="https://colab.research.google.com/github/lstein/stable-diffusion/blob/main/notebooks/Stable_Diffusion_AI_Notebook.ipynb"
target="_parent">
<img
src="https://colab.research.google.com/assets/colab-badge.svg"
alt="Open In Colab"/></a> <br> Open and follow instructions to use an isolated environment running
Dream.<br>

Output Example: ![Colab Notebook](../assets/colab_notebook.png)

---

## **Seamless Tiling**

The seamless tiling mode causes generated images to seamlessly tile with itself. To use it, add the
`--seamless` option when starting the script which will result in all generated images to tile, or
for each `invoke>` prompt as shown here:

```python
invoke> "pond garden with lotus by claude monet" --seamless -s100 -n4
```

---

## **Shortcuts: Reusing Seeds**

Since it is so common to reuse seeds while refining a prompt, there is now a shortcut as of version
1.11. Provide a `**-S**` (or `**--seed**`) switch of `-1` to use the seed of the most recent image
generated. If you produced multiple images with the `**-n**` switch, then you can go back further
using -2, -3, etc. up to the first image generated by the previous command. Sorry, but you can't go
back further than one command.

Here's an example of using this to do a quick refinement. It also illustrates using the new `**-G**`
switch to turn on upscaling and face enhancement (see previous section):

```bash
invoke> a cute child playing hopscotch -G0.5
[...]
outputs/img-samples/000039.3498014304.png: "a cute child playing hopscotch" -s50 -W512 -H512 -C7.5 -mk_lms -S3498014304

# I wonder what it will look like if I bump up the steps and set facial enhancement to full strength?
invoke> a cute child playing hopscotch -G1.0 -s100 -S -1
reusing previous seed 3498014304
[...]
outputs/img-samples/000040.3498014304.png: "a cute child playing hopscotch" -G1.0 -s100 -W512 -H512 -C7.5 -mk_lms -S3498014304
```

---

## **Weighted Prompts**

You may weight different sections of the prompt to tell the sampler to attach different levels of
priority to them, by adding `:(number)` to the end of the section you wish to up- or downweight. For
example consider this prompt:

```bash
tabby cat:0.25 white duck:0.75 hybrid
```

This will tell the sampler to invest 25% of its effort on the tabby cat aspect of the image and 75%
on the white duck aspect (surprisingly, this example actually works). The prompt weights can use any
combination of integers and floating point numbers, and they do not need to add up to 1.

---

## Thresholding and Perlin Noise Initialization Options

Two new options are the thresholding (`--threshold`) and the perlin noise initialization (`--perlin`) options. Thresholding limits the range of the latent values during optimization, which helps combat oversaturation with higher CFG scale values. Perlin noise initialization starts with a percentage (a value ranging from 0 to 1) of perlin noise mixed into the initial noise. Both features allow for more variations and options in the course of generating images.

For better intuition into what these options do in practice, [here is a graphic demonstrating them both](static/truncation_comparison.jpg) in use. In generating this graphic, perlin noise at initialization was programmatically varied going across on the diagram by values 0.0, 0.1, 0.2, 0.4, 0.5, 0.6, 0.8, 0.9, 1.0; and the threshold was varied going down from
0, 1, 2, 3, 4, 5, 10, 20, 100. The other options are fixed, so the initial prompt is as follows (no thresholding or perlin noise):

```
    a portrait of a beautiful young lady -S 1950357039 -s 100 -C 20 -A k_euler_a --threshold 0 --perlin 0
```

Here's an example of another prompt used when setting the threshold to 5 and perlin noise to 0.2:

```
    a portrait of a beautiful young lady -S 1950357039 -s 100 -C 20 -A k_euler_a --threshold 5 --perlin 0.2
```

Note: currently the thresholding feature is only implemented for the k-diffusion style samplers, and empirically appears to work best with `k_euler_a` and `k_dpm_2_a`. Using 0 disables thresholding. Using 0 for perlin noise disables using perlin noise for initialization. Finally, using 1 for perlin noise uses only perlin noise for initialization.

---

## **Simplified API**

For programmers who wish to incorporate stable-diffusion into other products, this repository
includes a simplified API for text to image generation, which lets you create images from a prompt
in just three lines of code:

```bash
from ldm.generate import Generate
g       = Generate()
outputs = g.txt2img("a unicorn in manhattan")
```

Outputs is a list of lists in the format [filename1,seed1],[filename2,seed2]...].

Please see ldm/generate.py for more information. A set of example scripts is coming RSN.

---

## **Preload Models**

In situations where you have limited internet connectivity or are blocked behind a firewall, you can
use the preload script to preload the required files for Stable Diffusion to run.

The preload script `scripts/preload_models.py` needs to be run once at least while connected to the
internet. In the following runs, it will load up the cached versions of the required files from the
`.cache` directory of the system.

```bash
(ldm) ~/stable-diffusion$ python3 ./scripts/preload_models.py
preloading bert tokenizer...
Downloading: 100%|██████████████████████████████████| 28.0/28.0 [00:00<00:00, 49.3kB/s]
Downloading: 100%|██████████████████████████████████| 226k/226k [00:00<00:00, 2.79MB/s]
Downloading: 100%|██████████████████████████████████| 455k/455k [00:00<00:00, 4.36MB/s]
Downloading: 100%|██████████████████████████████████| 570/570 [00:00<00:00, 477kB/s]
...success
preloading kornia requirements...
Downloading: "https://github.com/DagnyT/hardnet/raw/master/pretrained/train_liberty_with_aug/checkpoint_liberty_with_aug.pth" to /u/lstein/.cache/torch/hub/checkpoints/checkpoint_liberty_with_aug.pth
100%|███████████████████████████████████████████████| 5.10M/5.10M [00:00<00:00, 101MB/s]
...success
```
