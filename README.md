## House Price Streamlit App

Panduan ini menjelaskan cara menyiapkan artefak model, menjalankan aplikasi secara lokal, dan mendeploy ke Streamlit Cloud. Model dilatih di notebook `Regression_Home_Pricing.ipynb` menggunakan seluruh kolom numerik pada dataset Ames Housing (contoh: `GrLivArea`, `LotArea`, `TotalBsmtSF`, dst). Pastikan daftar kolom yang diekspor sama dengan input yang Anda kirim dari `app.py`.

### 1. Ekspor artefak dari notebook
- Di bagian akhir notebook, setelah memilih model terbaik (misalnya `RandomForestRegressor` dengan `train_X` dan `train_y`), tambahkan sel berikut untuk menyimpan artefak:
```python
import joblib

joblib.dump(model, "house_price_model.pkl")
joblib.dump(train_X.columns.tolist(), "feature_columns.pkl")
```
- Pindahkan kedua berkas `.pkl` ke root proyek (sejajar dengan `app.py`). Aplikasi Streamlit akan memuatnya secara langsung:
```python
model = joblib.load("house_price_model.pkl")
columns = joblib.load("feature_columns.pkl")
```
- Jika Anda ingin memakai subset fitur (seperti yang ada di `app.py`), pastikan kolom-kolom tersebut juga tersedia pada `train_X` sebelum menyimpan artefak, atau lakukan rekayasa fitur ulang agar urutan dan tipe datanya sama.

### 2. Siapkan environment lokal
1. Gunakan Python 3.11+ (versi yang sama dengan lingkungan notebook `.venv`).
2. Buat environment dan aktifkan:
   ```powershell
   cd "e:\PROJECT AI\Regression_HomePricingStreamlit"
   python -m venv .venv
   .\.venv\Scripts\activate
   ```
3. Instal dependensi minimal:
   ```powershell
   pip install streamlit pandas numpy scikit-learn joblib tensorflow
   ```
   Tambahkan paket lain yang dipakai notebook bila diperlukan (seaborn, matplotlib, dll). Setelah semua terpasang, jalankan `pip freeze > requirements.txt` untuk menyiapkan file dependensi yang diperlukan oleh Streamlit Cloud.

### 3. Jalankan Streamlit secara lokal
1. Pastikan `.venv` aktif dan berkas `.pkl` ada di root.
2. Jalankan:
   ```powershell
   streamlit run app.py
   ```
3. Form input yang muncul:
   - Numeric: `GrLivArea`, `LotArea`, `TotalBsmtSF`, `BedroomAbvGr`, `FullBath`, `TotRmsAbvGrd`, `GarageCars`, `GarageArea`.
   - Slider: `OverallQual`, `OverallCond`, `KitchenQual`.
   - Selectbox: `Neighborhood` (dikodekan sesuai mapping di `app.py`).
4. Isi beberapa nilai contoh dari dataset test notebook untuk memvalidasi. Prediksi akan muncul di panel kanan sebagai `st.success`.

### 4. Deploy ke Streamlit Cloud
1. Inisialisasi git repo dan commit seluruh berkas (`app.py`, `README.md`, `requirements.txt`, `.pkl`, dll), lalu push ke GitHub.
2. Buka [Streamlit Community Cloud](https://share.streamlit.io/), hubungkan dengan repo GitHub, dan pilih branch yang berisi proyek ini.
3. Pastikan:
   - `Main file path` = `app.py`.
   - `Python version` cocok dengan environment lokal (mis. 3.11).
   - `requirements.txt` tercantum seluruh paket (Streamlit otomatis menginstallnya).
   - Tidak diperlukan secret/env tambahan karena model dibaca dari berkas lokal.
4. Klik *Deploy*. Streamlit akan membangun environment dan menampilkan URL publik. Simpan URL tersebut untuk dibagikan.

### 5. Validasi & dokumentasi
- Bandingkan keluaran aplikasi dengan prediksi di notebook (gunakan baris fitur yang sama). Selisih besar biasanya berarti urutan kolom berbeda; cek `columns` dari `feature_columns.pkl`.
- Tambahkan contoh penggunaan pada README (misalnya tabel input dan prediksi) agar memudahkan pengguna lain.
- Setiap ada pembaruan model, ulangi langkah ekspor artefak dan update repo + deploy ulang.
