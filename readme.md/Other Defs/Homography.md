## Homography

Homography is a transformation that occurs between two planes, mapping one planar projection to another. It is represented by a 3x3 transformation matrix in a homogeneous coordinate space. In the context of computer vision, homography is used to relate two images of the same planar surface in space, assuming a pinhole camera model.

<img src="https://imgs.search.brave.com/ykKAFFoL4cMw76-Eo9yPFmtf27a1PAvuGmrBu1cc67s/rs:fit:860:0:0:0/g:ce/aHR0cHM6Ly9kb2Nz/Lm9wZW5jdi5vcmcv/My40LjAvaG9tb2dy/YXBoeV90cmFuc2Zv/cm1hdGlvbl9leGFt/cGxlMy5qcGc">

### Types of Homography

There are different types of homography, including:

- Affine Homography: A special type of homography whose last row is fixed to [0, 0, 1].
- Projective Homography: A general homography that maps lines to lines.

### Applications of Homography

Homography has many practical applications in computer vision, including:

- Image Rectification: Warping one image to match another.
- Image Registration: Aligning two images of the same scene.
- Camera Motion Estimation: Estimating the rotation and translation between two images.
- Augmented Reality: Inserting 3D objects into an image or video, so they appear to be part of the original scene.

### Estimating Homography

Homography can be estimated using various algorithms, such as the Direct Linear Transform (DLT) algorithm. In computer vision, homography is often estimated using feature matching and RANSAC (Random Sample Consensus) algorithm.

### Conclusion

In summary, homography is a transformation that maps one planar projection to another, and it has many applications in computer vision. Understanding homography is essential for tasks such as image rectification, image registration, and camera motion estimation.
