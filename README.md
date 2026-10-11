# Splat Studio

A 3D Gaussian Splatting trainer and viewer that runs entirely in the browser tab on WebGPU. Photos or a phone video go in; an explorable 3D scene comes out, and can be downloaded as `.ply` or `.splat`. Nothing is uploaded.

Live in the Virtual Lab at https://lab.juskowicz.com (entry `demo/index.html`).

## What it does
- **Demos:** a vintage camera (156k blobs, 30.8 dB on held-out photos) and a real scanned teddy bear (176k blobs, 36.7 dB). Both are trained from 100 posed renders of the 3D model (the camera is a CC0 model; the teddy is a real-object scan), so there is a ground truth to score against. "Watch it learn" retrains the selected demo live from random fuzz.
- **Your own media:** 30+ photos or one phone video. The page solves the cameras itself (structure from motion), picks the sharpest video frames, and either cuts out one object or keeps a whole room. Quality presets train at 200, 400 or 800 px.
- **Viewer:** orbit, pan, zoom, touch pinch, and a first-person walk mode (F key; mouse look, WASD, Shift to run, Q/E down/up, scroll for speed; no collision).
- **Everything explained:** a Techniques tab (every method with its paper and how it is used here), a searchable glossary, and honest limits.

## Techniques (all written from scratch for this page)
PCAsplat local-PCA regulariser (arXiv 2610.11011, the paper this was built around); 3D Gaussian Splatting and its tile rasteriser (2308.04079) with analytic backward pass; subgroup-sum gradient reduction (adapted from VkSplat 2605.00219); SSIM loss; SH degree 1 view-dependent colour; importance pruning; random seeding/splitting adapted from Blur Trap (2607.17965); squared-error loss idea from Polarized Opacity Prior (2608.22344); anti-aliasing low-pass filter; joint camera-pose refinement; structure from motion (Harris + oriented BRIEF, 8-point RANSAC, DLT-PnP, LM bundle adjustment); sharp-frame picking (variance of Laplacian); sharpness-weighted view sampling (simplified from ClearGS 2609.31509); per-photo exposure/white-balance fit (adapted from Robust Gaussian Splatting 2404.04211; for your own photos, benefit not yet measured on real captures); automatic subject cut-out (RANSAC planes, multi-view vote, masked retraining, idea from Object-Centric 2DGS 2501.08174); compact demo files (Morton ordering, per-channel quantisation, byte planes, delta + deflate; ideas from KISS-GS 2608.26948 and Self-Organizing Gaussians 2312.13299).

## Measured results (author's NVIDIA GPU, Chrome)
- Vintage camera, 800 px, up to 300k blobs, 12k steps: **30.8 dB** on held-out photos. Teddy bear, 800 px, 14k steps (11 min on the author's GPU): **36.7 dB**. Earlier Lego test scene: 34.55 dB at 800 px.
- Compact demo encoding: 9.1 MB to 3.96 MB on a 175k-blob test scene with 0.07 dB loss; camera demo 3.6 MB, teddy 4.0 MB.
- Camera solver on real photos with known poses: LLFF fern 1.5% camera-position error (27.2 dB held-out in scene mode); a real handheld fox video places all 50 frames with 3,000 features and 20 match partners (31 of 50 with the earlier lighter setting).

## Honest limits
- The camera solver is compact and home-made, not COLMAP: it wants smooth, overlapping, textured captures (about 5 degrees or less between neighbouring photos). Flat scenes, big jumps and rotating on the spot can fail; a `transforms.json` can be supplied instead.
- Object cut-out is automatic and imperfect. Moving things in scene mode are not removed.
- Detail cannot exceed the source photos.
- Not implemented: the paper's DTU/Tanks and Temples/mesh/Poisson experiments, learned pruning masks, truncated-gradient blob revival (TruncGradGS), SH above degree 1. Sparse pixel sampling (TurboGS) was implemented and tested, then turned off because it did not help.
- Needs a WebGPU browser and a real GPU. Camera solving runs on the main thread and can take minutes for 50 photos.
- Downloads (`.ply`, `.splat`) need a browser context that allows file downloads; the lab site's sandbox does not currently allow them.

## Credits
- PCAsplat: Matias, Nascimento, Nakayama, Lima, Lobo, Wetzstein, Guibas, Paiva, Novello (arXiv 2610.11011).
- Camera demo: "Camera 01" by Rajil Jose Macatangay via Poly Haven, CC0 (public domain).
- Teddy demo: "Lovable Huggable Cuddly Boutique Teddy Bear Beige" from Google Research Scanned Objects, (c) 2020 Google LLC, CC BY 4.0 (Downs et al., ICRA 2022, arXiv 2204.11918). Rendered images are derived from the scan.
- Built by Claude (Anthropic) for Michael Juskowicz's Virtual Lab.
