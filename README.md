
## **3. Methodology**

This section presents the system architecture, algorithms, preprocessing steps, evaluation strategy, and experimental environment for an online Intrusion Detection System (IDS) that detects concept drift in Internet of Medical Things (IoMT) data streams. The design is inspired by the structure and methodology proposed by Shyaa et al. (2025) for Electric Vehicle Charging Systems, adapted here for binary and multiclass intrusion detection scenarios.

---

### **3.1 System Architecture**

The proposed IDS architecture consists of a streaming-based learning pipeline with two major components:

* **Classifier**: A Hoeffding Adaptive Tree (HAT), which incrementally builds decision trees and adapts to evolving data distributions. It replaces subtrees when significant changes are detected.

* **Drift Detector**: ADWIN (ADaptive WINdowing), a performance-aware sliding window detector that identifies statistically significant changes in prediction accuracy. It maintains two sub-windows, $W_0$ and $W_1$

---

### **3.2 Data Source and Preprocessing**

The CICIoMT2024 dataset was selected due to its realistic traffic patterns in IoMT scenarios. It contains benign and multiple classes of attack data.
https://www.unb.ca/cic/datasets/iomt-dataset-2024.html

Preprocessing steps included:


* **Label encoding**: Labels were transformed into integers:


* **Feature normalization**: Each numerical feature $x_j$ was standardized using z-score normalization:


  where $\mu_j$ and $\sigma_j$ are the mean and standard deviation.

* **Dictionary conversion**: Each data instance $\mathbf{x}_i$ was converted to dictionary format:

---

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

Berikut adalah penjelasan lengkap untuk bagian **3.6 Visualization and Drift Logging**:

---

## 📈 **3.6 Visualization and Drift Logging – Penjelasan Lengkap**

Bagian ini menjelaskan bagaimana sistem mendokumentasikan dan memvisualisasikan **performa klasifikasi** serta **peristiwa concept drift** selama proses pembelajaran online. Proses ini penting untuk memahami *kapan* dan *bagaimana* sistem mengalami degradasi atau pemulihan performa akibat perubahan distribusi data (concept drift).

---

### 🎯 Tujuan Utama

1. Menyediakan gambaran **kuantitatif dan visual** tentang kinerja model.
2. Mengidentifikasi **waktu dan frekuensi drift**.
3. Menganalisis **stabilitas performa sebelum dan sesudah drift**.

---

### 📊 Komponen Visualisasi

#### 1. **Line Plots (Figures 7–10)**

* Metrik yang divisualisasikan:

  * **Accuracy**
  * **Precision**
  * **Recall**
  * **F1-Score**
* **X-axis**: Jumlah instance yang telah diproses
* **Y-axis**: Nilai metrik (dalam skala 0–1)
* **Garis vertikal biru**: Menandai **peristiwa concept drift**

> Tujuan: Memantau fluktuasi performa, dan mengamati apakah sistem **kembali stabil setelah drift**.

---

#### 2. **Bar Plots: Pre vs Post Drift Accuracy**

* Menampilkan **nilai akurasi sebelum dan sesudah** masing-masing peristiwa drift.
* Format batang ganda (side-by-side) untuk setiap drift:

  * Bar kiri: Akurasi sebelum drift
  * Bar kanan: Akurasi sesudah drift

> Tujuan: Menilai **efektivitas adaptasi model** terhadap drift.

---

### 📋 Logging Drift Events

Semua peristiwa drift direkam dalam format tabel, seperti:

| Drift Index | Instance | Timestamp |
| ----------- | -------- | --------- |
| 1           | 193439   | 18:52:48  |
| 2           | 196223   | 18:52:49  |
| …           | …        | …         |
| 21          | 8752447  | 20:24:23  |

**Keterangan kolom:**

* **Drift Index**: Nomor urut deteksi drift
* **Instance**: Indeks instance di mana drift terdeteksi
* **Timestamp**: Waktu (relatif) ketika drift terjadi dalam aliran data

> Tabel ini identik dengan **Tabel 3** dalam dokumen Anda, dan sejalan dengan format di jurnal referensi Shyaa et al. (2025).

---

### ⏱️ Runtime Performance Logging

Selain metrik klasifikasi, sistem juga mencatat:

* Waktu yang dihabiskan untuk:

  * **Prediksi**
  * **Evaluasi metrik**
  * **Deteksi concept drift**

Tabel ringkasan (seperti **Tabel 4**) menampilkan total waktu dalam detik untuk setiap proses tersebut.

---

### ✍️ Ringkasan

Visualisasi dan logging ini memastikan bahwa sistem:

* **Transparan** dalam perubahan performanya,
* **Terdokumentasi** setiap adaptasi terhadap drift,
* Dapat **dianalisis ulang** oleh peneliti atau pengembang untuk tuning lanjutan.

---

Berikut adalah penjelasan lengkap untuk bagian **3.7 Experimental Environment** dalam konteks eksperimen Anda:

---

## 🧪 **3.7 Experimental Environment – Penjelasan Lengkap**

Bagian ini menjelaskan **lingkungan teknis dan perangkat keras** yang digunakan dalam pelaksanaan eksperimen, termasuk bahasa pemrograman, pustaka yang digunakan, serta spesifikasi perangkat eksekusi. Informasi ini penting untuk **reproduksibilitas** dan **evaluasi efisiensi komputasi** dalam sistem deteksi intrusi berbasis streaming.

---

### ⚙️ **Lingkungan Pemrograman**

* **Bahasa**: Python 3.10
  Bahasa pemrograman utama yang mendukung pustaka pembelajaran mesin berbasis aliran data.

* **Pustaka (Libraries) yang Digunakan**:

  * `river`: Library utama untuk **streaming machine learning**, digunakan untuk:

    * HAT classifier
    * ADWIN drift detector
    * Metrik evaluasi (Accuracy, Precision, F1, dll.)
  * `pandas`, `numpy`: Untuk pemrosesan data tabular dan numerik
  * `matplotlib`: Untuk visualisasi metrik dan peristiwa drift
  * `sklearn.preprocessing`: Untuk normalisasi fitur dan encoding label
  * `datetime`, `time`: Untuk logging waktu, runtime, dan timestamp drift

---

### 💻 **Spesifikasi Perangkat Keras**

* **CPU**: Intel Core i7 (generasi ke-8)
* **RAM**: 16 GB
* **Threading**: Eksperimen dijalankan dalam **single-threaded mode**
* **Sistem Operasi**: Windows/Linux (tidak spesifik disebut, tetapi fleksibel)

> Penggunaan single-threaded menjamin bahwa pengukuran waktu reflektif terhadap efisiensi algoritma, bukan karena paralelisasi eksternal.

---

### 🔄 **Karakteristik Eksekusi**

* Sistem diprogram untuk memproses **sekitar 8.7 juta instance secara streaming**
* Setiap prediksi, evaluasi metrik, dan deteksi drift dilakukan secara **realtime per instance**
* Runtime total dievaluasi secara kumulatif dan dibagi ke dalam 3 bagian:

  * Waktu prediksi
  * Waktu evaluasi metrik
  * Waktu deteksi drift

> Hal ini mengacu langsung pada struktur pengukuran waktu seperti yang digunakan dalam **jurnal Shyaa et al. (2025)**.

---

### 🧾 **Reproducibility Support**

* Logging dilakukan pada setiap langkah (instance-by-instance)
* Semua visualisasi dan tabel dapat dihasilkan ulang berdasarkan log yang tersedia
* Tidak menggunakan GPU atau distributed framework, sehingga hasil **mudah direplikasi** pada sistem standar

---

### 📌 Ringkasan

Lingkungan eksperimen ini dirancang untuk:

* **Menguji efisiensi dan stabilitas** sistem IDS berbasis online learning
* **Membuktikan bahwa HAT + ADWIN** mampu berjalan dengan baik dalam **lingkungan terbatas**
* **Mendukung kebutuhan real-time detection** untuk sistem IoMT atau infrastruktur edge

---
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

  $$
  \text{class}_{\text{encoded}} = f(\text{class}_{\text{original}}) \in \mathbb{Z}^+
  $$

* **Feature normalization**: Each numerical feature $x_j$ was standardized using z-score normalization:

  $$
  x_j' = \frac{x_j - \mu_j}{\sigma_j}
  $$

  where $\mu_j$ and $\sigma_j$ are the mean and standard deviation.

* **Dictionary conversion**: Each data instance $\mathbf{x}_i$ was converted to dictionary format:

  $$
  \mathbf{x}_i = \{f_1: x_{i1}, f_2: x_{i2}, \ldots, f_d: x_{id} \}, \quad \forall i \in [1, N]
  $$

---

### **3.3 Classification Scenarios**

We evaluated the model in three classification setups:

1. **Binary**: Benign vs Attack
2. **Category-Level**: General attack categories
3. **Attack-Type**: Detailed attack types (e.g., MITM, DDoS)

---

### **3.4 Online Learning Workflow**

Streaming steps:

1. Receive new instance
2. Predict using HAT
3. Evaluate prediction
4. Update ADWIN
5. Detect drift (if any)
6. Update model using `learn_one()`
7. Log metrics per instance

---

### **3.5 Performance Metrics and Formulae**

We use standard classification metrics:

* **Accuracy**:

  $$
  \text{Accuracy} = \frac{TP + TN}{TP + TN + FP + FN}
  $$

* **Precision**:

  $$
  \text{Precision} = \frac{TP}{TP + FP}
  $$

* **Recall**:

  $$
  \text{Recall} = \frac{TP}{TP + FN}
  $$

* **F1-Score**:

  $$
  F1 = 2 \cdot \frac{\text{Precision} \cdot \text{Recall}}{\text{Precision} + \text{Recall}}
  $$

* **Total Processing Time**:

  $$
  T_{\text{total}} = \sum_{i=1}^{N} (t_p^{(i)} + t_m^{(i)} + t_d^{(i)})
  $$

---

### **3.6 Visualization and Drift Logging**

* Metric trends plotted over time (accuracy, precision, recall, F1)
* Vertical lines for drift events
* Bar plots: Accuracy before vs after each drift
* Tables:

  * **Table 3**: Drift event logs
  * **Table 4**: Time contributions

---

### **3.7 Experimental Environment**

* Programming language: Python 3.10
* Libraries: River, NumPy, Pandas, Matplotlib
* System: Single-threaded Intel Core i7 with 16GB RAM
* Dataset: \~8.7 million instances streamed continuously

---

### **3.8 Summary**

This methodology demonstrates the feasibility of combining HAT and ADWIN for real-time, adaptive IDS systems under non-stationary data. The framework accommodates both binary and multiclass classification, offers performance visibility at drift points, and provides metric trends over long streaming sessions.

---

