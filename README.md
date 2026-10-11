# Splat Studio

A 3D Gaussian Splatting trainer that runs entirely in your browser tab on your GPU (WebGPU). Watch a scene emerge from photos, orbit it, and download it as a .ply or .splat file. Single offline file: demo/index.html.

## What it does

- Loads a pre-trained Lego bulldozer instantly (89.7k Gaussians, 31.0 dB held-out PSNR), or trains from scratch on 100 views in the page.
- Reconstruct your own photos: drop 30+ photos of one object on a plain background. With a transforms.json (NeRF/nerfstudio format) the poses are exact; without one, photos are assumed to circle the object in filename order, and the orbit height and field of view are auto-calibrated and each camera is refined photometrically. This is an orbit-prior pose search, not full structure-from-motion.
- Downloads: standard 3DGS .ply and the compact .splat format.
- GPU-use modes (gentle, balanced, max) and a mobile preset keep the tab light.

## How it works

A custom WGSL tile-based rasterizer (16x16 tiles, per-tile bitonic depth sort) with analytic forward and backward passes and a fused Adam optimizer, with subgroup reductions in the backward pass when the device supports them. Densification (clone, split, prune) runs on the CPU. Gradients were checked against finite differences.

Techniques adopted from the literature: the PCAsplat local-PCA regulariser (K=16 neighbours: flatten surfaces, align normals, surfel term; arXiv:2610.11011), L2 photometric loss and polarized opacity prior ideas (arXiv:2608.22344), random seeding and splitting (arXiv:2607.17965), and subgroup-reduction ideas from VkSplat (arXiv:2605.00219).

## Honest limits

- Colour is SH degree 0 only (no view-dependent effects). No SSIM loss.
- PCAsplat costs about 0.66 dB PSNR on this clean synthetic data in exchange for roughly 3x thinner surfaces and about 96% normal alignment; it should help more on noisy real photos, which was not measured here.
- Per-camera pose refinement is only validated on already-correct poses.
- Needs WebGPU. Speed depends heavily on your GPU.
- The PLY/.splat exports have not been verified in every external viewer.

## Credits and licenses

Research: Vitor Matias, Filipe Nascimento, Kiyohiro Nakayama, Joao Paulo Lima, Marcus Lobo, Gordon Wetzstein, Leonidas Guibas, Afonso Paiva, Tiago Novello, [PCAsplat](https://arxiv.org/abs/2610.11011). No code from the papers is included.

Data: NeRF-synthetic "Lego" (Mildenhall et al., ECCV 2020); the Lego bulldozer model is by Heinzelnisse via BlendSwap, CC-BY-3.0.

## License

MIT for the code. See [LICENSE](LICENSE).
