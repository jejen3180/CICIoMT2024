
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



