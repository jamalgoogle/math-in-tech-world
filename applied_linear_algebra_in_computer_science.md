# Applied Linear Algebra in Computer Science

Linear algebra—the mathematical study of vector spaces, matrices, linear transformations, and systems of linear equations—provides the foundational computing engine for artificial intelligence, computer graphics, digital signal processing, search engines, and high-performance hardware acceleration. By formulating multidimensional operations as vector transformations and matrix factorizations, computer systems execute complex mathematical pipelines with extreme parallel efficiency. This document details five central domains where linear algebra is applied across modern computing systems.

## 1. Deep Learning and Neural Network Architectures

Modern deep learning architectures (such as Convolutional Neural Networks, Transformers, and Large Language Models) model intelligence by composing layers of high-dimensional linear transformations interspersed with non-linear activation functions.

### 1.1 Matrix Multiplication in Neural Layers

A standard dense (fully connected) neural network layer converts an input feature vector $\mathbf{x} \in \mathbb{R}^d$ into an output representation $\mathbf{y} \in \mathbb{R}^m$ via a matrix transformation:

$$
\mathbf{y} = \sigma\left( \mathbf{W} \mathbf{x} + \mathbf{b} \right)
$$

where $\mathbf{W} \in \mathbb{R}^{m \times d}$ is the weight matrix, $\mathbf{b} \in \mathbb{R}^m$ is the bias vector, and $\sigma(\cdot)$ is an element-wise non-linear activation function (e.g., ReLU, GELU, or Sigmoid).

* **Batched Inference and Training:** Processing a batch of $B$ samples simultaneously stacks inputs into a matrix $\mathbf{X} \in \mathbb{R}^{B \times d}$, computing all forward outputs in a single matrix-matrix multiplication:

  $$
  \mathbf{Y} = \sigma\left( \mathbf{X} \mathbf{W}^T + \mathbf{1}_B \mathbf{b}^T \right)
  $$

### 1.2 Convolutions as Matrix Operations (Im2Col)

Convolutional layers process multidimensional tensor arrays (e.g., image channels $C \times H \times W$). To leverage hardware-optimized matrix multiplication units, deep learning frameworks reshape 2D spatial image patches into matrix columns using the **Im2Col** transformation:

1. Image feature patches are unrolled into columns of a matrix $\mathbf{X}_{\text{col}} \in \mathbb{R}^{(C \cdot k_h \cdot k_w) \times N}$, where $k_h \times k_w$ is the kernel size and $N$ is the number of sliding window locations.

2. Convolutional filters are flattened into rows of a weight matrix $\mathbf{W}_{\text{flat}} \in \mathbb{R}^{F \times (C \cdot k_h \cdot k_w)}$, where $F$ is the number of output filters.

3. The convolution operation reduces to a General Matrix Multiply (GEMM):

  $$
  \mathbf{Y}_{\text{flat}} = \mathbf{W}_{\text{flat}} \mathbf{X}_{\text{col}}
  $$

### 1.3 Scaled Dot-Product Attention and Low-Rank Adaptation (LoRA)

* **Transformer Attention:** Multi-Head Self-Attention projects input sequence representations into Query ($\mathbf{Q}$), Key ($\mathbf{K}$), and Value ($\mathbf{V}$) matrices using weight projections $\mathbf{W}_Q, \mathbf{W}_K, \mathbf{W}_V \in \mathbb{R}^{d \times d_k}$:

  $$
  \text{Attention}(\mathbf{Q}, \mathbf{K}, \mathbf{V}) = \text{softmax}\left( \frac{\mathbf{Q} \mathbf{K}^T}{\sqrt{d_k}} \right) \mathbf{V}
  $$

  The inner product matrix $\mathbf{Q} \mathbf{K}^T \in \mathbb{R}^{L \times L}$ calculates all pairwise sequence context correlations across sequence length $L$.

* **Low-Rank Adaptation (LoRA):** Parameter-efficient fine-tuning decomposes weight update matrices $\Delta \mathbf{W} \in \mathbb{R}^{d \times k}$ into low-rank factor matrices $\mathbf{B} \in \mathbb{R}^{d \times r}$ and $\mathbf{A} \in \mathbb{R}^{r \times k}$ with rank $r \ll \min(d, k)$:

  $$
  \mathbf{W}_{\text{updated}} = \mathbf{W}_0 + \Delta \mathbf{W} = \mathbf{W}_0 + \frac{\alpha}{r} \mathbf{B} \mathbf{A}
  $$

  This reduces updated parameter counts from $d \cdot k$ to $r(d + k)$, saving memory and compute during LLM fine-tuning.

---

## 2. 3D Computer Graphics, Kinematics, and Spatial Transformations

Computer graphics engines (e.g., Vulkan, DirectX, Metal) use linear transformations to position, orient, scale, and project 3D virtual objects onto 2D display viewports.

### 2.1 Vector Spaces and Change of Basis

An object's geometry is defined relative to local model space coordinates. Placing the object into a global world scene requires a change of basis mapping:

$$
\mathbf{v}_{\text{world}} = \mathbf{M}_{\text{model} \to \text{world}} \, \mathbf{v}_{\text{local}}
$$

where $\mathbf{M}$ is constructed from column vectors representing the orientation axes and origin translation of the local frame relative to the world coordinate system.

### 2.2 Homogeneous Coordinates and Affine Transformations

Translating a 3D point $\mathbf{v} = [x, y, z]^T$ cannot be expressed as a linear transformation using a $3 \times 3$ matrix. To unify linear transformations (rotation, scaling, shear) and non-linear translations into a single matrix algebra, graphics pipelines embed 3D space into 4D **homogeneous coordinates**:

$$
\mathbf{p} = \begin{bmatrix} x \\ y \\ z \\ 1 \end{bmatrix}
$$

An **Affine Transformation Matrix** $\mathbf{T} \in \mathbb{R}^{4 \times 4}$ combines rotation matrix $\mathbf{R} \in \mathbb{R}^{3 \times 3}$ and translation vector $\mathbf{t} \in \mathbb{R}^3$:

$$
\mathbf{T} = \begin{bmatrix} \mathbf{R} & \mathbf{t} \\ \mathbf{0}^T & 1 \end{bmatrix} \implies \mathbf{T} \begin{bmatrix} \mathbf{v} \\ 1 \end{bmatrix} = \begin{bmatrix} \mathbf{R}\mathbf{v} + \mathbf{t} \\ 1 \end{bmatrix}
$$

Composing multiple spatial operations (e.g., Translate $\mathbf{T}_1$, Rotate $\mathbf{R}_2$, Scale $\mathbf{S}_3$) reduces to a single precomputed matrix product:

$$
\mathbf{M}_{\text{composite}} = \mathbf{T}_1 \mathbf{R}_2 \mathbf{S}_3
$$

### 2.3 Perspective Projection and View Transformations

To project 3D camera coordinates onto a 2D screen coordinate grid, perspective projection matrices apply scale factors inverse to depth $z$:

$$
\mathbf{P}_{\text{proj}} = \begin{bmatrix} \frac{f}{\text{aspect}} & 0 & 0 & 0 \\ 0 & f & 0 & 0 \\ 0 & 0 & \frac{z_{\text{far}} + z_{\text{near}}}{z_{\text{near}} - z_{\text{far}}} & \frac{2 z_{\text{far}} z_{\text{near}}}{z_{\text{near}} - z_{\text{far}}} \\ 0 & 0 & -1 & 0 \end{bmatrix}
$$

Multiplying a homogeneous coordinate vector by $\mathbf{P}_{\text{proj}}$ yields $[x', y', z', w']^T$. Dividing by the homogeneous coordinate $w' = -z$ performs **perspective division**, simulating real-world optical convergence where distant objects appear smaller.

---

## 3. Image Processing, Compression, and Feature Extraction

Digital images are matrices $\mathbf{A} \in \mathbb{R}^{m \times n}$ where entries represent pixel intensities. Matrix factorization algorithms extract features, eliminate noise, and compress storage requirements.

### 3.1 Singular Value Decomposition (SVD) and Image Compression

Any real image matrix $\mathbf{A} \in \mathbb{R}^{m \times n}$ can be factored into orthogonal matrices $\mathbf{U} \in \mathbb{R}^{m \times m}$ and $\mathbf{V} \in \mathbb{R}^{n \times n}$, and a diagonal matrix $\boldsymbol{\Sigma} \in \mathbb{R}^{m \times n}$ containing non-negative singular values $\sigma_1 \ge \sigma_2 \ge \dots \ge \sigma_r > 0$:

$$
\mathbf{A} = \mathbf{U} \boldsymbol{\Sigma} \mathbf{V}^T = \sum_{i=1}^{r} \sigma_i \mathbf{u}_i \mathbf{v}_i^T
$$

* **Truncated Low-Rank Reconstruction:** According to the **Eckart-Young-Mirsky Theorem**, the optimal rank-$k$ approximation ($k < r$) of matrix $\mathbf{A}$ that minimizes reconstruction error under the Frobenius norm is:

  $$
  \mathbf{A}_k = \sum_{i=1}^{k} \sigma_i \mathbf{u}_i \mathbf{v}_i^T
  $$

  Instead of storing $m \cdot n$ pixel values, storing the truncated rank-$k$ matrices requires only $k(m + n + 1)$ floating-point numbers, discarding high-frequency visual noise while retaining primary structural features.

### 3.2 Principal Component Analysis (PCA) for Dimensionality Reduction

Given a zero-centered data matrix $\mathbf{X} \in \mathbb{R}^{N \times d}$ representing $N$ data samples across $d$ dimensions:

1. **Covariance Matrix Construction:**

   $$
   \boldsymbol{\Sigma}_X = \frac{1}{N-1} \mathbf{X}^T \mathbf{X} \in \mathbb{R}^{d \times d}
   $$

2. **Eigendecomposition:** Compute the eigenvalues $\lambda_i$ and eigenvectors $\mathbf{v}_i$ satisfying:

   $$
   \boldsymbol{\Sigma}_X \mathbf{v}_i = \lambda_i \mathbf{v}_i
   $$

3. **Dimensionality Reduction:** Sorting eigenvectors by descending eigenvalue magnitude constructs a projection matrix $\mathbf{W}_k = [\mathbf{v}_1, \mathbf{v}_2, \dots, \mathbf{v}_k] \in \mathbb{R}^{d \times k}$. Projecting data onto this basis:

   $$
   \mathbf{Z} = \mathbf{X} \mathbf{W}_k \in \mathbb{R}^{N \times k}
   $$

   preserves the maximum data variance in $k$ dimensions while removing redundant feature correlations (e.g., Eigenfaces in computer vision).

### 3.3 Linear Image Filtering and Convolutions

Applying spatial image filters (e.g., Gaussian blur, Sobel edge detection) evaluates 2D linear combinations of neighboring pixels using convolution kernels $\mathbf{K} \in \mathbb{R}^{(2k+1) \times (2k+1)}$:

$$
\mathbf{I}_{\text{filtered}}(x, y) = \sum_{a=-k}^{k} \sum_{b=-k}^{k} \mathbf{K}(a, b) \, \mathbf{I}(x - a, y - b)
$$

Linear filtering operations are equivalent to multiplying flattened image vectors by block Toeplitz / block Circulant matrices.

---

## 4. Search Ranking, Recommendation Systems, and Graph Spectra

Large-scale internet platforms use matrix algebraic structures to analyze network topology, extract implicit user preferences, and process multi-relational graphs.

### 4.1 PageRank and Stationary Distributions

Google's PageRank models web navigation as a Markov chain over a directed web graph with adjacency matrix $\mathbf{A}$, where $A_{ij} = 1$ if page $j$ links to page $i$.

* **Transition Probability Matrix:** Construct column-stochastic matrix $\mathbf{M}$ where $M_{ij} = \frac{A_{ij}}{\text{out-degree}(j)}$. To guarantee ergodicity and convergence, the **Google Matrix** $\mathbf{G}$ incorporates a damping factor $\alpha \approx 0.85$:

  $$
  \mathbf{G} = \alpha \mathbf{M} + \frac{1 - \alpha}{N} \mathbf{E}
  $$

  where $\mathbf{E}$ is an $N \times N$ matrix of all ones.

* **Power Iteration Method:** The stationary PageRank probability vector $\mathbf{r}$ satisfies the eigenvector equation $\mathbf{r} = \mathbf{G} \mathbf{r}$ for eigenvalue $\lambda = 1$. It is calculated iteratively via matrix-vector multiplication:

  $$
  \mathbf{r}^{(k+1)} = \mathbf{G} \mathbf{r}^{(k)}
  $$

### 4.2 Collaborative Filtering via Matrix Factorization

Recommendation engines model user ratings as a sparse user-item interaction matrix $\mathbf{R} \in \mathbb{R}^{m \times n}$ (for $m$ users and $n$ items).

* **Low-Rank Factorization:** Approximates $\mathbf{R}$ as the product of dense latent feature matrices $\mathbf{U} \in \mathbb{R}^{m \times k}$ and $\mathbf{V} \in \mathbb{R}^{n \times k}$ ($k \ll \min(m, n)$):

  $$
  \hat{\mathbf{R}} = \mathbf{U} \mathbf{V}^T \implies \hat{R}_{ij} = \mathbf{u}_i^T \mathbf{v}_j
  $$

* **Alternating Least Squares (ALS) Optimization:** Minimizes squared error over known rating entries $\Omega$ with L2 regularization:

  $$
  \mathcal{L} = \sum_{(i,j) \in \Omega} \left( R_{ij} - \mathbf{u}_i^T \mathbf{v}_j \right)^2 + \lambda \left( \sum_{i} \Vert{}\mathbf{u}_i\Vert{}_2^2 + \sum_{j} \Vert{}\mathbf{v}_j\Vert{}_2^2 \right)
  $$

  ALS solves for $\mathbf{u}_i$ while holding $\mathbf{V}$ constant, then solves for $\mathbf{v}_j$ holding $\mathbf{U}$ constant, utilizing closed-form linear system updates.

### 4.3 Spectral Graph Theory and the Graph Laplacian

Properties of network topologies are analyzed via the spectrum of the **Graph Laplacian Matrix** $\mathbf{L} = \mathbf{D} - \mathbf{A}$, where $\mathbf{D}$ is the diagonal degree matrix ($\mathbf{D}_{ii} = \sum_j A_{ij}$).

* **Symmetric Normalized Laplacian:**

  $$
  \mathbf{L}_{\text{sym}} = \mathbf{D}^{-1/2} \mathbf{L} \mathbf{D}^{-1/2} = \mathbf{I} - \mathbf{D}^{-1/2} \mathbf{A} \mathbf{D}^{-1/2}
  $$

* **Spectral Clustering and Fiedler Vector:** The second smallest eigenvalue $\lambda_2$ of $\mathbf{L}$ (the algebraic connectivity) and its corresponding eigenvector $\mathbf{v}_2$ (the **Fiedler Vector**) identify optimal graph bi-partitions that minimize normalized edge cuts between sub-communities.

---

## 5. Parallel GPU Computing, BLAS, and Hardware Acceleration

Modern hardware accelerators (GPUs, TPUs, and NPU Neural Engines) achieve massive execution throughput by optimizing linear algebra routines at the silicon layer.

### 5.1 Basic Linear Algebra Subprograms (BLAS)

High-performance linear algebra software is structured into standard operational tiers:

| Tier | Operation Class | Computational Complexity | Memory Access Ratio | Examples |
| :--- | :--- | :--- | :--- | :--- |
| **BLAS Level 1** | Vector-Vector | $O(N)$ operations | $O(N)$ data transfer | Vector Dot Product ($\mathbf{x}^T \mathbf{y}$), Axpy ($\mathbf{y} \leftarrow \alpha \mathbf{x} + \mathbf{y}$) |
| **BLAS Level 2** | Matrix-Vector | $O(N^2)$ operations | $O(N^2)$ data transfer | Matrix-Vector Multiply ($\mathbf{y} \leftarrow \alpha \mathbf{A} \mathbf{x} + \beta \mathbf{y}$) |
| **BLAS Level 3** | Matrix-Matrix | $O(N^3)$ operations | $O(N^2)$ data transfer | General Matrix Multiply GEMM ($\mathbf{C} \leftarrow \alpha \mathbf{A} \mathbf{B} + \beta \mathbf{C}$) |

Because BLAS Level 3 algorithms perform $O(N^3)$ arithmetic operations on $O(N^2)$ data elements, memory access latency is hidden behind parallel arithmetic throughput (high compute intensity).

### 5.2 Systolic Arrays and Tensor Cores

To maximize matrix multiplication throughput, hardware units employ **Systolic Arrays**: two-dimensional grids of specialized Processing Elements (PEs) that pass intermediate accumulated matrix terms directly to adjacent neighbors.

```
       B[0,0]   B[0,1]   B[0,2]
         |        |        |
         v        v        v
A[0,0] -> [ PE ] -> [ PE ] -> [ PE ]
         |        |        |
A[1,0] -> [ PE ] -> [ PE ] -> [ PE ]
         |        |        |
         v        v        v
```

* Each PE performs a single **Fused Multiply-Add (FMA)** operation per clock cycle:

  $$
  D_{\text{out}} = D_{\text{in}} + (A \times B)
  $$

* Eliminating global memory registers during matrix accumulation allows Tensor Cores to evaluate thousands of $16 \times 16$ matrix multiplications per clock cycle.

### 5.3 Distributed Matrix Multiplication (Cannon's and Strassen's Algorithms)

* **Strassen's Algorithm:** Subdivides $N \times N$ matrices into $2 \times 2$ block structures and uses 7 recursive block multiplications instead of 8, reducing asymptotic runtime complexity:

  $$
  O(N^{\log_2 7}) \approx O(N^{2.807})
  $$

* **Cannon's Algorithm:** A distributed-memory algorithm that partitions matrices $\mathbf{A}$ and $\mathbf{B}$ across an $N \times N$ toroidal mesh of processor nodes. By cyclically shifting matrix block sub-arrays across grid rows and columns, Cannon's algorithm computes large-scale parallel matrix multiplication with zero memory contention.

---

## Summary Matrix

| Domain / Concept | Primary Linear Algebra Basis | Core CS Application |
| :--- | :--- | :--- |
| **Deep Learning** | Matrix Multiplications, Tensor Outer Products, Low-Rank Decompositions | Batched Neural Network Training, Convolution Im2Col, Transformer Attention, LoRA |
| **3D Graphics & Kinematics** | Homogeneous Coordinates ($4 \times 4$), Change of Basis, Perspective Projection | Viewport Projection, Model Coordinate Framing, Unified Affine Transformations |
| **Image & Data Processing** | Singular Value Decomposition (SVD), Covariance Eigendecomposition | Low-Rank Image Compression, Principal Component Analysis (PCA), Feature Extraction |
| **Search & Recommendations** | Stochastic Matrices, Matrix Factorization, Graph Laplacian Spectrum | PageRank Iteration, Collaborative Filtering (ALS), Spectral Graph Partitioning |
| **GPU Computing & Acceleration** | BLAS Level 3 GEMM, Systolic Array FMA, Strassen/Cannon Algorithms | Hardware Acceleration, Tensor Core Execution, Distributed Parallel Compute |
