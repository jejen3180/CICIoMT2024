
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

Berikut adalah tabel ringkas untuk melengkapi bagian **3.3 Classification Scenarios**, merangkum ketiga skenario klasifikasi yang diuji dalam eksperimen Anda:

---

### 📊 **Table 2. Overview of Classification Scenarios**

| Scenario                  | Description                               | No. of Classes | Example Labels                                    | Purpose                              |
| ------------------------- | ----------------------------------------- | -------------- | ------------------------------------------------- | ------------------------------------ |
| **Binary Classification** | Classifies traffic as benign or malicious | 2              | `Benign`, `Attack`                                | Fast detection of intrusion presence |
| **Category-Level**        | Detects high-level categories of attacks  | 5–8            | `DoS`, `Recon`, `Malware`, `MitM`, `Botnet`, etc. | Categorize threats for triage        |
| **Attack-Type**           | Identifies specific attack types          | 10–25+         | `Heartbleed`, `SQLi`, `XSS`, `BruteForce`, etc.   | Forensic-level threat classification |

---

### ✍️ Narasi Pendukung (untuk dokumen):

You may insert the following text right after the bullet list in Section 3.3:

> Table 2 summarizes the three classification levels explored in this study. Binary classification offers fast detection of malicious activity, while category-level classification supports automated incident triage. The most complex, attack-type classification, enables forensic-level insights by distinguishing between different attack vectors. Each classification task increases the difficulty of learning and drift adaptation, providing a comprehensive evaluation of the system's robustness.

---

Silakan beri tahu jika Anda ingin saya sisipkan langsung ke dokumen atau ingin dilanjutkan ke bagian **4. Results and Discussion**.

## 🔄 **3.4 Online Learning Workflow – Penjelasan Lengkap**

Bagian ini menjelaskan bagaimana sistem Intrusion Detection berbasis **online learning** beroperasi dalam lingkungan **streaming data**, yang meniru kondisi dunia nyata seperti lalu lintas IoMT (Internet of Medical Things) secara real time.

Metodologi ini **tidak menggunakan batch learning**, melainkan memproses **satu instance data setiap kali**, mengikuti urutan langkah-langkah berikut:

---

### 🔁 **Langkah-langkah dalam Streaming Learning**

1. **Retrieve Instance (Input Baru):**
   Sistem menerima satu instance data dari aliran dataset CICIoMT2024 (berformat dictionary). Ini meniru lalu lintas data aktual yang datang secara bertahap.

2. **Prediction by HAT Model:**
   Model **Hoeffding Adaptive Tree (HAT)** memproses fitur dari instance tersebut dan memberikan prediksi kelas (binary atau multiclass) secara instan.

3. **Evaluate Prediction (Compare True vs. Predicted):**
   Prediksi yang dihasilkan dibandingkan dengan label sebenarnya untuk mengetahui apakah hasilnya benar atau salah.

4. **Update Drift Detector (ADWIN):**
   Informasi tentang **akurasi prediksi** (benar/salah) dikirim ke ADWIN.

   * ADWIN memperbarui dua jendela ($W_0$ dan $W_1$) dan menghitung apakah perbedaan rata-rata error cukup signifikan untuk menyatakan **drift** telah terjadi.

5. **Detect Drift (Log if True):**
   Jika perbedaan antara dua jendela melebihi ambang batas statistik (berdasarkan Hoeffding Bound), **drift terdeteksi**, dan indeks instance tersebut dicatat bersama timestamp-nya.

6. **Model Update (Learning):**
   Apapun hasil prediksi, HAT selalu diperbarui menggunakan metode `learn_one()` dari library **River**.

   * Ini memastikan bahwa model belajar secara kontinu dari data baru.

7. **Log Metrics (Accuracy, Precision, etc.):**
   Setelah memproses satu instance, sistem menghitung dan menyimpan metrik performa:

   * **Accuracy**, **Precision**, **Recall**, dan **F1-score**
   * Hal ini dilakukan **per instance**, bukan per batch, untuk memonitor tren perubahan performa dengan sangat detail.

---

### 🔍 **Kelebihan dari Proses Ini**

* **Adaptif:** Model menyesuaikan dengan perubahan data seiring waktu (konsep drift).
* **Real-time:** Tidak perlu menunggu batch data selesai.
* **Ringan:** Cocok untuk IoMT atau sistem real-time dengan keterbatasan sumber daya.

---

Jika Anda menginginkan, saya dapat bantu membuat pseudocode atau visual (diagram alir) untuk menjelaskan proses 3.4 ini secara grafis—seperti yang biasa ada di bagian *Algorithm 1* pada jurnal IEEE.

Ingin saya bantu lanjutkan visualisasinya?
