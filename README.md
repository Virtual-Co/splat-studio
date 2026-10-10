# Splat Island

A floating dream island built from 40,960 soft Gaussian splats, rendered live in your browser by a from-scratch WebGL2 splat renderer. Drag to orbit, pinch or scroll to zoom, slide the time of day from night to dusk to noon, and press Snap to save a picture. Left alone, it gives itself a slow cinematic tour.

The scene is procedural. Surface splats are flattened into discs by a local PCA of their neighbours, the technique from PCAsplat; no model is trained and no third-party code or assets are used. Single offline file: demo/index.html.

Research credit: Vitor Matias, Filipe Nascimento, Kiyohiro Nakayama, João Paulo Lima, Márcus Lobo, Gordon Wetzstein, Leonidas Guibas, and Tiago Novello, [PCAsplat: Gaussian Splatting with Local PCA Regularization](https://arxiv.org/abs/2610.11011), arXiv:2610.11011. Paper license: CC BY 4.0. No code from the paper is included.

## License

MIT. See [LICENSE](LICENSE).
