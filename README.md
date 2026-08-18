# AI Enabled Structural Health Monitoring using Piezoelectric Sensors

## 1. Motivation
The structural integrity of aerospace bulkheads and wing-root attachments is paramount to operational safety. Historically, catastrophic failures, such as the explosive decompression of Aloha Airlines Flight 243, have underscored the fatal consequences of undetected fatigue cracks in metallic joints. Modern aircraft architectures, including the Boeing 787 and Airbus A350, increasingly rely on dissimilar multi-material planar joints (e.g., aluminum-to-titanium) to optimize the strength-to-weight ratio. 

However, transitioning from scheduled maintenance (which grounds aircraft and incurs massive operational downtime) to Condition-Based Maintenance (CBM) requires real-time, on-board monitoring. Developing a mathematically robust Digital Twin capable of interpreting active ultrasonic waves in these complex joint geometries is a critical frontier in modern aerospace engineering.

## 2. Problem Statement
While active ultrasonic Lamb wave monitoring is highly effective in isotropic, monolithic plates, its application to dissimilar planar joints introduces severe mathematical and physical complexities. 
*   **Acoustic Impedance Mismatch:** When an ultrasonic wave transitions from an Aluminum 2024-T3 domain to a Titanium Grade 5 (Ti-6Al-4V) domain, the stark difference in material stiffness and density causes severe boundary scattering and wave mode conversion. 
*   **Data Scarcity & Computational Bottlenecks:** High-fidelity finite element modeling of time-dependent wave propagation is computationally exhaustive. Generating the massive datasets required for traditional deep learning is infeasible.
*   **Mean Collapse in Regression:** Standard regression algorithms suffer from mean collapse when attempting to localize anomalies using heavily dispersed, noisy acoustic signals under strict data scarcity, failing to map complex temporal features to spatial coordinates.

## 3. Existing Solutions
Current methodologies in Structural Health Monitoring (SHM) exhibit significant limitations:
*   **Traditional NDT (Non-Destructive Testing):** Techniques like phased-array ultrasonics or C-scan imaging require the aircraft to be out of service and rely heavily on manual operator interpretation.
*   **Threshold-Based Monitoring:** Basic algorithmic approaches rely on static amplitude thresholds to detect damage. These fail entirely in multi-material joints, where harmless geometric boundary reflections dwarf the subtle signal variations caused by micro-cracks.
*   **Deep Neural Networks (DNNs):** While effective, DNNs operate as "black boxes" and require tens of thousands of training parameters, rendering them unsuitable for computationally bounded Digital Twin environments where data is scarce.

## 4. Method and Methodologies
This study constructs a computationally bounded Digital Twin using finite element modeling (COMSOL Multiphysics) to simulate a 400 mm planar butt joint comprising Aluminum 2024-T3 and Titanium Grade 5. 
*   **Excitation:** Active interrogation is achieved via a 7 mm Piezoelectric Wafer Active Sensor (PWAS), injecting a 200 kHz, 5-cycle Hanning-windowed tone burst to isolate the fundamental symmetric ($S_0$) mode.
*   **Environmental Constraints:** The system is constrained to a nominal ambient temperature of 293.15 K to eliminate thermal-induced velocity shifts.
*   **Algorithmic Feature Extraction:** A multi-node sensor array captures the temporal floating potential. To counter wave dispersion, a Hilbert Transform is utilized to extract the analytic signal envelope, ensuring mathematically robust Time-of-Flight (ToF) calculations alongside energy and kurtosis metrics.

## 5. Mathematical Modelling
The acoustic wave propagation through the solid continuum is governed by the Navier-Cauchy equation of motion. The group velocity of the isolated $S_0$ Lamb wave mode within the isotropic domains is approximated by:

$$v_{S0} = \sqrt{\frac{E}{\rho(1-\nu^2)}}$$

where $E$ is Young's modulus, $\rho$ is the mass density, and $\nu$ is Poisson's ratio. 

To overcome data scarcity for spatial localization, the extracted 24-dimensional feature space is modeled using Gaussian Process Regression (GPR). The stochastic spatial mapping relies on a strictly bounded composite kernel, combining a Matérn covariance function (to model physical wave smoothness) with a constrained White Noise kernel (to account for boundary scattering):

$$k(x_i, x_j) = \sigma_f^2 \left( 1 + \frac{\sqrt{3}d}{\rho} \right) \exp\left(-\frac{\sqrt{3}d}{\rho}\right) + \sigma_n^2 \delta_{ij}$$

where $d$ is the Euclidean distance between feature vectors, $\rho$ is the length scale, and $\sigma_n^2$ strictly limits the noise variance to prevent algorithmic mean collapse.

## 6. Proposed Solution
We propose a two-stage, physics-informed machine learning pipeline engineered for high-dimensional spatial regression under extreme data scarcity. 
1.  **Dynamic Classification:** A Support Vector Machine (SVM) evaluates the continuous multi-node sensor stream, calculating the real-time probability of structural degradation.
2.  **Stochastic Triangulation:** Upon triggering the damage threshold, the 24-dimensional feature matrix is passed to the optimized GPR framework. By cross-referencing phase shifts and temporal delays across four distinct sensor nodes simultaneously, the algorithm triangulates the precise spatial coordinate of the structural anomaly.

## 7. Results and Outcome
The proposed methodology successfully digitized the acoustic physics of the dissimilar aerospace joint, yielding a highly deterministic dataset of 601 time steps across 33 distinct spatial damage configurations.
*   **Detection Reliability:** The dynamic monitoring pipeline successfully differentiated between harmless titanium boundary reflections and critical structural cracks, maintaining a stable probability baseline before accurately triggering the damage threshold.
*   **Spatial Localization:** By fusing the sensor nodes and constraining the stochastic optimizer, the model completely eliminated mean collapse. The High-Dimensional GPR successfully tracked the linear progression of the crack coordinates, achieving a Mean Absolute Error (MAE) of 25.09 mm. 
*   **Robustness:** The model demonstrated profound mathematical resilience, accurately mapping the spatial domain utilizing a highly constrained training set, proving its viability for computationally limited Digital Twin ecosystems.

## 8. Conclusion
This research successfully bridges finite element wave physics with advanced stochastic modeling to monitor complex aerospace planar joints. By replacing brute-force data generation with high-dimensional sensor fusion and Hilbert-transformed feature extraction, the proposed pipeline accurately localizes structural anomalies under stringent data constraints. The mathematically optimized GPR framework proves that intelligent algorithm design can overcome acoustic impedance scattering in dissimilar metals. Future investigations will focus on incorporating coupled thermo-mechanical interfaces and exploring Operations Research (OR) methodologies to mathematically optimize the geometric placement of the sensor array.
