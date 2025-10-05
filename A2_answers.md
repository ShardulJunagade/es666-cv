## Question 1

📖 Theory Q: Question: Which filtering method works best for gaussian noise and which works for salt and pepper noise? Why?

Ans:

For Gaussian noise, the Gaussian filter and the Bilateral filter gave similar PSNR and SSIM scores but Gaussian started performing better than the bilateral one as we increased the noise level.  Intuitively, Gaussian noise is zero-mean and spread around each pixel; convolving with a Gaussian kernel performs a weighted local averaging that reduces variance of the noise while preserving low-frequency structure. The mean filter also reduced the noise, but it removed fine facial details because it weighs all neighbours equally. A bilateral filter often gave better visual results near edges because it combines spatial and intensity weighting (so it smooths within regions but preserves edges).

For salt-and-pepper, the median filter worked the best. Salt-and-pepper creates isolated extreme pixels (0 or 1) that are statistical outliers. The median filter ignored these spikes as long as they don’t dominate the window. This helps removing impulses while keeping the edges. The mean and gaussian filters are averaging filters so they try to average in the outliers and are heavily skewed by the outlier values. The bilateral performed the worst with impulse noise because the corrupted pixel’s intensity difference can still attract some weight locally.


## Question 2
📖 Theory Q: What mathematical property of the Harris matrix allows it to distinguish between edges and corners?  

Ans.
Harris Corner Detector uses the second-moment (structure) matrix $M$ built from local gradient products $I_x, I_y$:  

$$
M = \begin{pmatrix}
\sum I_x^2 & \sum I_x I_y \\
\sum I_x I_y & \sum I_y^2
\end{pmatrix}
$$


The eigenvalues $\lambda_1, \lambda_2$ of $M$ show us how gradient energy spreads in two orthogonal directions.

- If both $\lambda_1$ and $\lambda_2$ are small $\implies$ almost no gradient $\implies$ patch is **flat** (no structure).  
- If one eigenvalue is large and the other small ($\lambda_1 \gg \lambda_2$) $\implies$ strong variation in one direction $\implies$ **edge**.  
- If both eigenvalues are large $\implies$ strong variation in two perpendicular directions $\implies$ **corner**. 

The determinant $(λ1 * λ2)$ and trace $(λ1+λ2)$ combine into the Harris response $R$.
$$
R = \det(M) - k \cdot (\mathrm{trace}(M))^2 = \lambda_1 \lambda_2 - k(\lambda_1 + \lambda_2)^2
$$
where k usually lies between 0.04 and 0.06.

$R$ is high only when both eigenvalues are large (so determinant is large) and not dominated by just one eigenvalue (since $trace^2$ term penalizes the single‑eigenvalue case).

Intuitively, corners produce significant intensity changes in two perpendicular directions. This is the result of large, balanced eigenvalues of the Harris matrix. This is the mathematical property that lets the Harris corner detector separate corners from edges and flat zones.




## Question 3

📖 Theory Q: What are potential weaknesses of SSD-based matching compared to modern descriptors like SIFT/ORB?  
Ans.

SSD (sum-of-squared-differences) on raw intensity patches is a simple and intuitive method for matching features but weak compared to SIFT (Scale-Invariant Feature Transform) and ORB (Oriented FAST and Rotated BRIEF).

1. Scale and rotation invariant: If the philosopher’s face appears a bit scaled or rotated, plain patches no longer align and this increases the SSD score and leads to a failed match even if the feature is correct. SIFT builds scale‑space keypoints and rotates descriptors to a dominant orientation. ORB handles orientation and uses binary tests. This makes SIFT and ORB rotation and scale invariant.

2. Sensitive to illumination: A slight brightness or contrast shift between the key patch and the mosaic region can increase SSD even if the structure matches. SIFT uses gradient orientations and normalize histograms and ORB uses binary tests that make them more lighting invariant.

3. Noise invariant: If we add noise, many pixel intensities change directly, and the SSD distances grow. Gradient/histogram or binary comparison descriptors are more stable to this.

4. Curse of Dimensionality: A raw 15×15 patch gives 225 float values. Distance over lots of keypoints is more expensive than comparing short binary ORB descriptors with Hamming distance.

So, SSD on raw patches works as a simple baseline but it is invariant to geometric and photometric transformations and this is where SIFT and ORB prove to be better than SSD.
