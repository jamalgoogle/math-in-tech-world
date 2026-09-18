# Applied Trigonometry in Computer Science

Trigonometry—the mathematical study of relationships involving lengths, angles, and periodic oscillations—provides the core foundation for digital signal processing, wireless communication systems, physics simulation engines, and autonomous robotic navigation. This document details four central domains where trigonometric functions and identities are applied in computer systems.

## 1. Signal Processing and Lossy Media Compression

Modern digital audio, image, and video compression rely on decomposing spatial or temporal signal data into linear combinations of trigonometric basis functions (sine and cosine waves).

### 1.1 The Discrete Cosine Transform (DCT) in JPEG and MP3

Lossy compression algorithms transform raw pixel values or spatial audio samples into frequency coefficients, allowing humanly imperceptible high-frequency details to be discarded.

* **2D Discrete Cosine Transform (DCT-II):** Used in JPEG image compression to convert $8 \times 8$ pixel blocks $f(x,y)$ into frequency components $F(u,v)$:

  $$
  F(u,v) = \frac{1}{4} C(u) C(v) \sum_{x=0}^{7} \sum_{y=0}^{7} f(x,y) \cos\left[ \frac{(2x+1)u\pi}{16} \right] \cos\left[ \frac{(2y+1)v\pi}{16} \right]
  $$

  where $C(u), C(v) = \frac{1}{\sqrt{2}}$ for $u,v = 0$, and $1$ otherwise.

* **Modified Discrete Cosine Transform (MDCT):** Used in MP3, AAC, and Vorbis audio codecs. The MDCT processes overlapping blocks of audio data using cosine basis functions to eliminate time-domain block boundary artifacts (time-domain aliasing cancellation).

### 1.2 Harmonic Motion and Spectral Analysis

Any bounded, periodic signal $f(t)$ with period $T = \frac{2\pi}{\omega_0}$ can be expanded as a convergent **Fourier Series** of sines and cosines:

$$
f(t) = \frac{a_0}{2} + \sum_{n=1}^{\infty} \left( a_n \cos(n \omega_0 t) + b_n \sin(n \omega_0 t) \right)
$$

where coefficients $a_n$ and $b_n$ quantify the amplitude of each fundamental frequency and harmonic overtone present in the signal.

### 1.3 Trigonometric Basis Functions in Modern Video Codecs

Video compression standards (e.g., H.264/AVC, H.265/HEVC, AV1) extend block-based DCT concepts using variable size integer transform matrices derived from cosine functions:

* **Frequency Quantization:** High-frequency cosine coefficient values are divided by large quantization steps and rounded to zero, achieving high compression ratios while preserving lower structural spatial frequencies.
* **Intra-Frame Directional Prediction:** Uses angular projections based on trigonometric slopes to extrapolate pixel patterns across adjacent macroblocks.

## 2. Wireless Communications and Network Signal Processing

Wireless protocols (Wi-Fi 6/7, 4G/5G Cellular, Bluetooth, Radar) encode binary data onto high-frequency electromagnetic carrier waves using trigonometric phase and amplitude variations.

### 2.1 The Discrete Fourier Transform (DFT) & Fast Fourier Transform (FFT)

The DFT maps time-domain discrete digital signals $x[n]$ to frequency-domain complex representations $X[k]$ using Euler's trigonometric identity $e^{i\theta} = \cos \theta + i \sin \theta$:

$$
X[k] = \sum_{n=0}^{N-1} x[n] e^{-i 2\pi k n / N} = \sum_{n=0}^{N-1} x[n] \left( \cos\left( \frac{2\pi k n}{N} \right) - i \sin\left( \frac{2\pi k n}{N} \right) \right)
$$

* **Fast Fourier Transform (FFT):** Algorithms such as Cooley-Tukey reduce computation complexity from $O(N^2)$ to $O(N \log N)$, enabling real-time spectrum analysis, filtering, and radio frequency modulation in software-defined radios (SDR).

### 2.2 Quadrature Amplitude Modulation (QAM)

QAM modulates two orthogonal trigonometric carrier waves (a cosine "in-phase" component $I$ and a sine "quadrature" component $Q$) to transmit multiple bits per symbol:

$$
s(t) = I(t) \cos(2\pi f_c t) - Q(t) \sin(2\pi f_c t) = A(t) \cos\left(2\pi f_c t + \phi(t)\right)
$$

where:

* **Amplitude:** $A(t) = \sqrt{I(t)^2 + Q(t)^2}$
* **Phase Angle:** $\phi(t) = \arctan\left(\frac{Q(t)}{I(t)}\right)$

High-density schemes like 1024-QAM map specific bit combinations to individual coordinate locations on a 2D constellation diagram defined by amplitude and phase angle.

### 2.3 Orthogonal Frequency-Division Multiplexing (OFDM)

OFDM splits high-speed data channels into hundreds of subcarrier frequencies. Subcarriers are spaced at precise frequency intervals $\Delta f = \frac{1}{T}$ so that they are mathematically orthogonal over the symbol period $T$:

$$
\int_{0}^{T} \cos(2\pi f_m t) \cos(2\pi f_n t) \, dt = 0 \quad (m \neq n)
$$

This trigonometric orthogonality prevents inter-carrier interference (ICI) even when subcarrier frequency spectra overlap heavily in Wi-Fi and 5G networks.

## 3. Game Physics, Kinematics, and Procedural Animation

Game engines (e.g., Unreal Engine, Unity) rely on trigonometric formulas to handle physical trajectory calculations, angle computations, line-of-sight determination, and procedural wave generation.

### 3.1 Projectile Motion and Harmonic Oscillations

* **Ballistic Trajectories:** The position $(x(t), y(t))$ of a projectile launched with initial velocity $v_0$ at angle $\theta$ relative to the horizontal is given by:

  $$
  x(t) = v_0 \cdot t \cdot \cos \theta
  $$

  $$
  y(t) = v_0 \cdot t \cdot \sin \theta - \frac{1}{2} g t^2
  $$

* **Procedural Water & Oscillations:** Simple harmonic motion and complex ocean surfaces are simulated by layering sine/cosine functions (e.g., Gerstner waves):

  $$
  y(x, z, t) = A \cos\left( k_x x + k_z z - \omega t + \phi \right)
  $$

### 3.2 Vector Angles, Dot Products, and Direction Vectors

* **Angle Between Vectors:** The dot product isolates the cosine of the enclosed angle $\theta$ between two vectors $\vec{u}$ and $\vec{v}$:

  $$
  \cos \theta = \frac{\vec{u} \cdot \vec{v}}{\|\vec{u}\| \|\vec{v}\|} \implies \theta = \arccos\left( \frac{\vec{u} \cdot \vec{v}}{\|\vec{u}\| \|\vec{v}\|} \right)
  $$

* **Normalizing Direction Angles:** Converting a polar heading $\theta$ to a unit directional vector $\vec{d}$:

  $$
  \vec{d} = \begin{bmatrix} \cos \theta \\ \sin \theta \end{bmatrix}
  $$

### 3.3 Inverse Trigonometry and Raycasting

* **2D Four-Quadrant Orientation:** The extended inverse tangent function `atan2(y, x)` resolves the full $[-\pi, \pi]$ radian orientation angle of a point $(x, y)$, avoiding division-by-zero errors when $x = 0$:

  $$
  \theta = \text{atan2}(y, x)
  $$

* **Raycasting Engines:** Classic 2D pseudo-3D engines (e.g., *Wolfenstein 3D*) calculate ray wall intersection distances $d_{\text{correct}}$ and correct fish-eye distortion by scaling distance against the player's field-of-view offset angle $\beta$:

  $$
  d_{\text{correct}} = d_{\text{raw}} \cdot \cos \beta
  $$

## 4. Sensors, Drones, and Autonomous Navigation

Robotic platforms, quadcopters, and mobile devices process sensor streams from Inertial Measurement Units (IMUs), GPS, and LiDAR using trigonometric state estimation algorithms.

### 4.1 IMUs and Attitude Estimation (Roll, Pitch, Yaw)

An IMU combines a 3-axis accelerometer and 3-axis gyroscope to track body orientations in 3D space.

* **Roll ($\phi$) and Pitch ($\theta$) from Gravity Vector:** Static accelerations $(a_x, a_y, a_z)$ are converted to angular tilt relative to Earth's gravity vector:

  $$
  \text{Roll } (\phi) = \text{atan2}\left(a_y, a_z\right)
  $$

  $$
  \text{Pitch } (\theta) = \text{atan2}\left(-a_x, \sqrt{a_y^2 + a_z^2}\right)
  $$

* **Sensor Fusion (Complementary Filter):** Combines high-frequency gyroscope integration with low-frequency accelerometer tilt angles:

  $$
  \theta_{\text{fused}} = \alpha \left( \theta_{\text{prev}} + \omega_{\text{gyro}} \Delta t \right) + (1 - \alpha) \theta_{\text{accel}}
  $$

### 4.2 Triangulation and Distance Estimation

* **LiDAR / Sonar Ranging:** Converts time-of-flight distance $r$ and mirror scanning angles $(\theta, \phi)$ to 3D Cartesian point clouds $(x, y, z)$:

  $$
  x = r \sin \theta \cos \phi, \quad y = r \sin \theta \sin \phi, \quad z = r \cos \theta
  $$

* **Geometric Triangulation:** Determines an object's location given known baseline distance $d$ between two cameras and observed azimuth angles $\alpha$ and $\beta$:

  $$
  x = d \cdot \frac{\sin \beta}{\sin(\alpha + \beta)}, \quad y = d \cdot \frac{\sin \alpha \sin \beta}{\sin(\alpha + \beta)}
  $$

### 4.3 Spherical Navigation and Great-Circle Headings

* **Forward Azimuth (Bearing Calculation):** Computes initial compass bearing angle $\theta$ required to navigate from coordinate point $(\phi_1, \lambda_1)$ to $(\phi_2, \lambda_2)$ on a spherical Earth model:

  $$
  \theta = \text{atan2}\left( \sin(\Delta \lambda) \cos \phi_2, \, \cos \phi_1 \sin \phi_2 - \sin \phi_1 \cos \phi_2 \cos(\Delta \lambda) \right)
  $$

* **Dead Reckoning:** Estimates a mobile robot's updated position $(x_{t+1}, y_{t+1})$ given previous coordinates $(x_t, y_t)$, linear distance moved $\Delta s$, and heading angle $\theta$:

  $$
  x_{t+1} = x_t + \Delta s \cdot \cos \theta
  $$

  $$
  y_{t+1} = y_t + \Delta s \cdot \sin \theta
  $$

## Summary Matrix

| Domain / Concept | Primary Trigonometric Basis | Core CS Application |
| ----- | ----- | ----- |
| **Media Compression (JPEG/MP3/H.265)** | Discrete Cosine Transform (DCT/MDCT), Fourier Series | Frequency domain lossy compression, quantization |
| **Wireless Communications (5G/Wi-Fi)** | Discrete Fourier Transform (FFT), Orthogonal Subcarriers, QAM | OFDM modulation, Phase-Amplitude signal encoding |
| **Game Engines & Physics** | Sine/Cosine Oscillations, Inverse Trigonometry (`atan2`), Dot Product Angle | Ballistic trajectories, procedural waves, raycasting, FOV correction |
| **Robotics, Drones & Navigation** | Euler angle roll/pitch/yaw formulas, Triangulation, Spherical Azimuth | IMU tilt sensing, sensor fusion, LiDAR cloud reconstruction, dead reckoning |
