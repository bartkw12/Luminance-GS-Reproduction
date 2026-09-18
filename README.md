This project will be implementing the paper:
Luminance-GS: Adapting 3D Gaussian Splatting to Challenging Lighting Conditions with View-Adaptive Curve Adjustment

The implementation will be simpler than the original repo:
https://github.com/cuiziteng/Luminance-GS/tree/main/Luminance-GS

It will focus on understanding 3DGS firstly, then implement low light improvements.

The reproduction values have been copied to the repo.
PSNR: 18.09
SSIM: 0.877
LPIPS: 0.193

More indepth results:

1. PSNR = 21.730

PSNR, or Peak Signal-to-Noise Ratio, measures pixel-level reconstruction accuracy.

The rough idea is:

How numerically close is the rendered image to the ground-truth normal-light image?

Higher is better.

A PSNR of 21.73 dB is respectable for this kind of low-light reconstruction problem. More importantly, the paper reports 18.09 on buu, so your run is about:

+3.64 dB better

which is a meaningful difference.

If the images are normalized to \([0,1]\), a PSNR of 21.73 corresponds to an approximate RMSE of about 0.082, meaning average pixel errors are relatively small.

2. SSIM = 0.9127

SSIM means Structural Similarity Index.

Unlike PSNR, it is less concerned with exact pixel-by-pixel matches and more concerned with:

structure
edges
contrast
local patterns
general scene organization

SSIM ranges roughly from 0 to 1:

1.0 = extremely similar
lower values = increasingly structurally different

Your 0.9127 is very strong.

The paper reports 0.877 for the same scene, so you are around:

+0.036 SSIM higher

That matches what we can see visually: the Patrick figure, color chart, floor and background geometry are clearly preserved.

3. LPIPS = 0.180

LPIPS is Learned Perceptual Image Patch Similarity.

This one is different:

lower is better.

Instead of comparing raw pixels, LPIPS compares deep visual features from a neural network. It tries to measure whether two images look perceptually similar to a human.

So conceptually:

PSNR: “Are the pixels close?”
SSIM: “Is the structure close?”
LPIPS: “Do they look perceptually similar?”

Your:

0.180

is slightly better than the paper’s:

0.193

for buu.

That suggests that not only were your pixels and structure accurate, but the rendered scene was also perceptually similar to the normal-light target.

A useful talking point:

“All three metrics agree rather than contradicting each other. PSNR, SSIM and LPIPS all indicate strong reconstruction quality.”

That is actually important. Sometimes PSNR improves while LPIPS gets worse, which would suggest the model became numerically accurate but visually less convincing.

4. 81,900 Gaussians

This is very different from the image-quality metrics.

The model began with:

1,090 Gaussians

from the COLMAP/SfM initialization.

By the end, it had:

81,900 Gaussians.

That happened because of Gaussian densification.

During training, 3DGS identifies parts of the scene where the current representation is not detailed enough and:

duplicates Gaussians
splits Gaussians
adjusts their position
adjusts scale/rotation
adjusts opacity
optimizes color

What the model did well:
First, scene structure is very strong. SSIM above 0.91 indicates that the geometry and major visual structures are being preserved well.

Second, the low-light enhancement seems successful. The scene is bright, recognizable, and contains usable colors rather than looking like the original dark training views.

Third, perceptual quality is strong. LPIPS below the paper's reported value suggests the result is visually convincing rather than merely matching pixels.