# Exam Questions

**Summary**: Full exam question bank for the A4M33MPV (Computer Vision Methods) course. 43 questions organized by topic with cross-links to relevant wiki pages.

**Course**: computer-vision

**Sources**: courses_mpv_labs_exam_questions [CourseWare Wiki].pdf

**Last updated**: 2026-05-12

---

## Local Features & Interest Points (Q1–Q13)

1. **Wide-baseline matching**. Describe the steps for obtaining correspondences between a pair of images taken from different viewpoints.
   → [[computer-vision/local-features]], [[computer-vision/ransac]], [[computer-vision/learned-matching]]

2. **[[computer-vision/local-features#harris-corner-detector|Harris interest points]]** — definition, algorithm for detection, parameters. Explain the motivation behind the definition. Describe the effects of the parameters on the number of detected points. To which transformation (geometric/photometric) is this detector invariant?
   → [[computer-vision/local-features]]

3. Describe the algorithm for the selection of interest point (region) **scale using the Laplacian**.
   → [[computer-vision/local-features#scale-space-detection]]

4. Describe steps to generalize Harris/Hessian detector to become **affine invariant**.
   → [[computer-vision/local-features#affine-covariant-regions]]

5. Describe ways of **local feature orientation estimation**.
   → [[computer-vision/local-features]]

6. **Hessian and Difference of Gaussian** interest points. Definition, properties.
   → [[computer-vision/local-features#scale-space-detection]]

7. Define **[[computer-vision/local-features#mser|Maximally Stable Extremal Regions (MSER)]]**. Describe the algorithm for their detection. Properties of extremal regions and the maximally stable subset.
   → [[computer-vision/local-features]]

8. The **FAST** interest point detector.
   → [[computer-vision/local-features]]

9. The **[[computer-vision/local-features#sift|SIFT descriptor]]**. Describe the algorithm and its properties.
   → [[computer-vision/local-features]]

10. **RootSIFT** descriptor. Describe the algorithm.
    → [[computer-vision/local-features]]

11. Describe **"Local Binary Patterns"** like descriptors.
    → [[computer-vision/local-features]]

12. How are local descriptors **matched**? What are the ways of filtering out unreliable correspondences?
    → [[computer-vision/local-features]], [[computer-vision/ransac]]

13. **Learning local feature detectors** — describe the possible loss functions (R2D2, SuperPoint) and training data sources.
    → [[computer-vision/learned-matching]]

## Image Retrieval (Q14–Q25)

14. How is **mAP** computed? Discuss relation to precision@k, recall@k for particular k.
    → [[computer-vision/image-retrieval]]

15. How is the **[[computer-vision/image-retrieval#bag-of-visual-words-bovw|Bag-of-Words]]** representation constructed? What is the **idf weighting**, how is it estimated, and what problem is it handling? How is image-to-image similarity estimated with BoW? How is the codebook size affecting the sparsity of the BoW histogram?
    → [[computer-vision/image-retrieval]]

16. What is an **[[computer-vision/image-retrieval#inverted-index|inverted-file structure]]** and how is it used to perform retrieval with BoW? When is it better to use an inverted file instead of directly storing the original BoW vectors? Memory requirements.
    → [[computer-vision/image-retrieval]]

17. How is the **[[computer-vision/image-retrieval#vlad|VLAD descriptor]]** computed and how does it differ from BoW?
    → [[computer-vision/image-retrieval]]

18. How does the **SMK approach** work and how does it extend the BoW approach? Advantages, drawbacks, memory, speed, performance.
    → [[computer-vision/image-retrieval]]

19. How is **[[computer-vision/image-retrieval#spatial-verification|spatial verification]]** used to improve retrieval performance? How are tentative correspondences obtained with BoW? Why is this better than BoW similarity?
    → [[computer-vision/image-retrieval]], [[computer-vision/ransac]]

20. How can retrieval and [[computer-vision/ransac|RANSAC]] be used to perform **[[computer-vision/image-retrieval#zoom-in--zoom-out|zoom-in]]** operation given a query image and a large dataset? How does the first retrieval stage differ from standard BoW retrieval?
    → [[computer-vision/image-retrieval]], [[computer-vision/ransac]]

21. For a retrieval task, what are the benefits of mapping images to a **vector space** and how is retrieval performed then?
    → [[computer-vision/image-retrieval]]

22. How does **NetVLAD** work and what is the difference with VLAD?
    → [[computer-vision/image-retrieval#deep-retrieval]]

23. How are **global image descriptors** obtained using CNNs and **[[computer-vision/image-retrieval#global-descriptors|SPoC, MAC, GeM]]**? What is the relation between GeM and the others? Which are translation invariant and why?
    → [[computer-vision/image-retrieval]], [[computer-vision/cnn-architectures]]

24. How do the **[[computer-vision/deep-metric-learning#contrastive-loss|contrastive]]** and **[[computer-vision/deep-metric-learning#triplet-loss|triplet loss]]** work? What is the role of the margin? What are hard negatives and why are they important?
    → [[computer-vision/deep-metric-learning]]

25. What is a good way to collect **training examples** to train a network for retrieval of buildings and popular landmarks? Why relying on discrete class labels is not good enough for contrastive loss?
    → [[computer-vision/deep-metric-learning]], [[computer-vision/image-retrieval]]

## RANSAC & Robust Estimation (Q26)

26. Describe the **[[computer-vision/ransac|RANSAC algorithm]]**, its properties, advantages and disadvantages. Which parameters does it have?
    → [[computer-vision/ransac]], [[computer-vision/epipolar-geometry]]

## Object Detection & Sliding Windows (Q27–Q29)

27. Describe the steps for object detection using **"sliding windows"**. How is reasonable speed achieved?
    → [[computer-vision/object-detection-segmentation]]

28. Describe how to use an **integral image** for computing the sum of the intensity and the intensity variance for a rectangular region.
    → [[computer-vision/object-detection-segmentation]]

29. Why is **AdaBoost** often used for "sliding window" methods? Give more than one reason.
    → [[computer-vision/object-detection-segmentation]], [[pattern-recognition/adaboost]]

## Visual Tracking (Q30–Q35)

30. For a static scene with horizontal camera movement, draw an image patch useful for tracking using a **[[computer-vision/visual-tracking|gradient method (KLT tracker)]]**. Which properties should such a patch have?
    → [[computer-vision/visual-tracking]]

31. Which image patches are **suitable for tracking**? Why? Which patches are not suitable?
    → [[computer-vision/visual-tracking]], [[computer-vision/local-features#harris-corner-detector]]

32. **Mean-shift algorithm**. Describe the principles and simulate calculation for 1D example.
    → [[computer-vision/visual-tracking]]

33. **Mean-shift algorithm**. Color pixels [R,G,B] represented in 3D space. How to reduce the color-space into 256 color-space?
    → [[computer-vision/visual-tracking]]

34. **[[computer-vision/visual-tracking#kcf|DCT — discriminative (kernel) correlation tracking]]**. The algorithm, representation of the object, the search method.
    → [[computer-vision/visual-tracking]]

35. **DCT tracking** in the presence of rotation and scale change.
    → [[computer-vision/visual-tracking]]

## Hough Transform (Q36–Q38)

36. Describe the **Hough transformation** algorithm for detection of parametrized structure (line, circle, …). Discuss properties (time and memory requirements, parameters).
    → [[computer-vision/ransac]]

37. Compare the Hough transformation with a **brute-force search** algorithm.
    → [[computer-vision/ransac]]

38. Compare the **Hough transformation with RANSAC**.
    → [[computer-vision/ransac]]

## Deep Neural Networks (Q39–Q43)

39. **Deep Neural Nets for image classification**. Structure — convolutional, pooling and fully connected layers. Non-linearities.
    → [[computer-vision/cnn-architectures]], [[shared/cnn-fundamentals]]

40. **Deep Neural Nets** — learning. The cost function, SGD, dropout, batch normalization. SGD parameters.
    → [[computer-vision/cnn-architectures]], [[deep-learning/optimization]], [[deep-learning/normalization]]

41. **Deep Neural Nets** — how do you select learning rate?
    → [[deep-learning/optimization]]

42. **Deep Neural Nets for detection**. Proposal-based and end-to-end methods. Class label and bounding box prediction.
    → [[computer-vision/object-detection-segmentation]]

43. **Deep Neural Nets** — applications in computer vision.
    → [[computer-vision/cnn-architectures]], [[computer-vision/object-detection-segmentation]], [[computer-vision/generative-models]], [[computer-vision/self-supervised-learning]], [[computer-vision/image-retrieval]], [[computer-vision/visual-tracking]]

## Topic Coverage Map

| Topic | Questions | Primary wiki page |
|-------|-----------|-------------------|
| Local features & detectors | Q1–Q12 | [[computer-vision/local-features]] |
| Learned features | Q13 | [[computer-vision/learned-matching]] |
| Image retrieval (classical) | Q14–Q21 | [[computer-vision/image-retrieval]] |
| Image retrieval (deep) | Q22–Q25 | [[computer-vision/image-retrieval]], [[computer-vision/deep-metric-learning]] |
| RANSAC | Q26 | [[computer-vision/ransac]] |
| Sliding window detection | Q27–Q29 | [[computer-vision/object-detection-segmentation]] |
| Visual tracking | Q30–Q35 | [[computer-vision/visual-tracking]] |
| Hough transform | Q36–Q38 | [[computer-vision/ransac]] |
| CNNs & deep learning | Q39–Q43 | [[computer-vision/cnn-architectures]], [[computer-vision/object-detection-segmentation]] |

## Notably Absent Topics

The following wiki pages cover content **not directly examined** in this question bank but taught in the course:
- [[computer-vision/epipolar-geometry]] — fundamental/essential matrix (only indirectly via Q26)
- [[computer-vision/camera-models-calibration]] — pinhole model, DLT, Zhang's method
- [[computer-vision/3d-reconstruction]] — stereo, SfM, bundle adjustment
- [[computer-vision/generative-models]] — GANs, VAEs (only via Q43)
- [[computer-vision/self-supervised-learning]] — SimCLR, DINO, MAE (only via Q43)

## Related pages

- [[computer-vision/course-overview]]
- [[computer-vision/local-features]]
- [[computer-vision/image-retrieval]]
- [[computer-vision/ransac]]
- [[computer-vision/visual-tracking]]
- [[computer-vision/object-detection-segmentation]]
- [[computer-vision/cnn-architectures]]
- [[computer-vision/deep-metric-learning]]
- [[computer-vision/learned-matching]]
