---
title: Some reflections and research on current view-control models.
published: 2025-09-15
tags: [Blogging]
category: Examples
draft: false
---

# This Article is a Draft

In our previous training runs, we observed a clear problem: the image-based pose (usually joint key-points from an OpenPose-like method) is entangled with the camera-view information. This coupling obviously hampers the model’s grasp of the abstract notion of “viewpoint” and inevitably introduces errors in the results. Therefore, the input to the model should be changed to vertex or SMPL parameters that quantitatively encode the concept of a “human figure”.
A simplified architecture could look like this:

The vertex stream will be encoded by a PointNet-like network.
Text will interact with the vertex features through cross-attention.
Together these two modules act as an encoder, corresponding to the 3D geometry input stage in computer graphics.
In parallel, we will train a dedicated encoder for the camera viewpoint to mimic the perspective-projection process.
For the decoder, we currently treat a diffusion model as the counterpart of graphics rasterization/bilinear interpolation/rendering: it “decodes” the 3D information back into an image.


---
