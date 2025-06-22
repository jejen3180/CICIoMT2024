Berikut adalah versi lengkap dan telah diperbaiki dari bagian **3. Methodology**, disusun agar **akurat secara ilmiah, konsisten dengan jurnal referensi**, dan siap dipakai dalam publikasi:

---

## **3. Methodology**

This section presents the system architecture, algorithms, preprocessing steps, evaluation strategy, and experimental environment for an online Intrusion Detection System (IDS) that detects concept drift in Internet of Medical Things (IoMT) data streams. The design is inspired by the structure and methodology proposed by Shyaa et al. (2025) for Electric Vehicle Charging Systems, adapted here for binary and multiclass intrusion detection scenarios.

---

### **3.1 System Architecture**

The proposed IDS architecture consists of a streaming-based learning pipeline with two major components:

* **Classifier**: A Hoeffding Adaptive Tree (HAT), which incrementally builds decision trees and adapts to evolving data distributions. It replaces subtrees when significant changes are detected.

* **Drift Detector**: ADWIN (ADaptive WINdowing), a performance-aware sliding window detector that identifies statistically significant changes in prediction accuracy. It maintains two sub-windows, $W_0$ and $W_1$, and raises a drift alert if:

  $$
  |\mu_{W_0} - \mu_{W_1}| > \sqrt{\frac{1}{2} \cdot \ln\left(\frac{4}{\delta}\right) \cdot \left( \frac{1}{|W_0|} + \frac{1}{|W_1|} \right)}
  $$

  where $\mu_{W_0}$ and $\mu_{W_1}$ are the empirical means of the windows, and $\delta$ is the confidence level.

---

### **3.2 Data Source and Preprocessing**

The CICIoMT2024 dataset was selected due to its realistic traffic patterns in IoMT scenarios. It contains benign and multiple classes of attack data.

Preprocessing steps included:

* **Label encoding**: Labels were transformed into integers:


* **Feature normalization**: Each numerical feature $x_j$ was standardized using z-score normalization:


  where $\mu_j$ and $\sigma_j$ are the mean and standard deviation.

* **Dictionary conversion**: Each data instance $\mathbf{x}_i$ was converted to dictionary format:

---

### **3.3 Classification Scenarios**

We evaluated the model in three classification setups:

1. **Binary**: Benign vs Attack
2. **Category-Level**: General attack categories
3. **Attack-Type**: Detailed attack types (e.g., MITM, DDoS)

---
