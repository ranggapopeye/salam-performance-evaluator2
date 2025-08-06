# Sistem Evaluasi Kinerja Karyawan dengan Metode SAW (Simple Additive Weighting)

Aplikasi web modern untuk mengevaluasi kinerja karyawan menggunakan metode SAW (Simple Additive Weighting) yang dibangun dengan React, TypeScript, dan Supabase. Sistem ini dirancang khusus untuk Yayasan As-Salam Joglo dengan fitur evaluasi yang fleksibel dan terstruktur.

## 📋 Daftar Isi

- [Fitur Utama](#-fitur-utama)
- [Teknologi yang Digunakan](#️-teknologi-yang-digunakan)
- [Prasyarat](#-prasyarat)
- [Instalasi dan Setup](#-instalasi-dan-setup)
- [Konfigurasi Database](#️-konfigurasi-database)
- [Menjalankan Aplikasi](#️-menjalankan-aplikasi)
- [Struktur Database](#-struktur-database)
- [Arsitektur Aplikasi](#-arsitektur-aplikasi)
- [Cara Penggunaan](#-cara-penggunaan)
- [Metode SAW](#-metode-saw)
- [API Documentation](#-api-documentation)
- [Deployment](#-deployment)
- [Testing](#-testing)
- [Troubleshooting](#-troubleshooting)
- [Contributing](#-contributing)
- [License](#-license)

## 🚀 Fitur Utama

### 📊 Manajemen Data
- **Manajemen Karyawan**: CRUD lengkap untuk data karyawan dengan validasi email dan tanggal
- **Manajemen Kriteria Fleksibel**: Sistem kriteria yang dapat disesuaikan dengan auto-adjustment bobot
- **Evaluasi Kinerja Dinamis**: Input nilai evaluasi berdasarkan kriteria yang telah ditentukan
- **Sistem Kategori Terstruktur**: Kriteria diorganisir dalam kategori (Kinerja Inti, Kedisiplinan, Faktor Tambahan)

### 🧮 Perhitungan SAW
- **Algoritma SAW Otomatis**: Implementasi lengkap metode Simple Additive Weighting
- **Normalisasi Data**: Normalisasi otomatis berdasarkan tipe kriteria (Benefit/Cost)
- **Ranking Otomatis**: Perangkingan karyawan berdasarkan skor akhir
- **Penyimpanan Hasil**: Menyimpan hasil perhitungan untuk analisis historis

### 📈 Dashboard dan Laporan
- **Dashboard Interaktif**: Statistik real-time dengan grafik dan visualisasi
- **Laporan Cetak**: Fitur cetak laporan dengan format profesional
- **Analisis Rekomendasi**: Rekomendasi otomatis berdasarkan skor evaluasi
- **Grafik Perbandingan**: Bar chart dan pie chart untuk analisis visual

### 🔐 Keamanan dan Autentikasi
- **Autentikasi Supabase**: Sistem login/register yang aman
- **Row Level Security (RLS)**: Keamanan data tingkat baris
- **Protected Routes**: Proteksi halaman dengan autentikasi

### 📱 User Experience
- **Responsive Design**: Optimal di desktop, tablet, dan mobile
- **Real-time Updates**: Sinkronisasi data real-time
- **Toast Notifications**: Feedback interaktif untuk setiap aksi
- **Loading States**: Indikator loading yang informatif

## 🛠️ Teknologi yang Digunakan

### Frontend
- **React 18**: Library UI dengan hooks modern
- **TypeScript**: Type safety dan developer experience
- **Vite**: Build tool yang cepat dan modern
- **Tailwind CSS**: Utility-first CSS framework
- **shadcn/ui**: Komponen UI yang konsisten dan accessible

### Backend & Database
- **Supabase**: Backend-as-a-Service dengan PostgreSQL
- **PostgreSQL**: Database relasional dengan fitur advanced
- **Row Level Security**: Keamanan data tingkat baris

### State Management & Data Fetching
- **TanStack React Query**: Server state management dan caching
- **React Hook Form**: Form handling dengan validasi
- **Zod**: Schema validation

### UI/UX Libraries
- **Lucide React**: Icon library yang konsisten
- **Recharts**: Library untuk grafik dan visualisasi
- **React Router DOM**: Client-side routing
- **Sonner**: Toast notifications yang elegant

### Development Tools
- **ESLint**: Code linting dan quality assurance
- **TypeScript ESLint**: TypeScript-specific linting rules
- **PostCSS**: CSS processing
- **Autoprefixer**: CSS vendor prefixes

## 📋 Prasyarat

Sebelum memulai, pastikan Anda memiliki:

- **Node.js** (versi 18.0.0 atau lebih baru)
- **npm** (versi 8.0.0 atau lebih baru) atau **yarn** (versi 1.22.0 atau lebih baru)
- **Git** (untuk version control)
- **Akun Supabase** (gratis di [supabase.com](https://supabase.com))
- **Browser modern** (Chrome 90+, Firefox 88+, Safari 14+, Edge 90+)

### Verifikasi Prasyarat

```bash
# Cek versi Node.js
node --version

# Cek versi npm
npm --version

# Cek versi Git
git --version
```

## 🔧 Instalasi dan Setup

### 1. Clone Repository

```bash
git clone <URL_REPOSITORY>
cd sistem-evaluasi-karyawan
```

### 2. Install Dependencies

```bash
npm install
```

atau jika menggunakan yarn:

```bash
yarn install
```

### 3. Konfigurasi Environment Variables

Buat file `.env.local` di root project dan tambahkan konfigurasi Supabase:

```env
VITE_SUPABASE_URL=your_supabase_project_url
VITE_SUPABASE_ANON_KEY=your_supabase_anon_key
```

**Cara mendapatkan Supabase credentials:**
1. Login ke [Supabase Dashboard](https://app.supabase.com)
2. Pilih project Anda atau buat project baru
3. Pergi ke Settings > API
4. Copy `Project URL` dan `anon public` key

### 4. Verifikasi Instalasi

```bash
# Jalankan development server
npm run dev

# Aplikasi akan berjalan di http://localhost:8080
```

## 🗄️ Konfigurasi Database

### 1. Setup Database Schema

Jalankan SQL migration berikut di Supabase SQL Editor secara berurutan:

#### A. Tabel Utama

```sql
-- 1. Tabel Karyawan
CREATE TABLE IF NOT EXISTS public.employees (
  id UUID NOT NULL DEFAULT gen_random_uuid() PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  position VARCHAR(50) NOT NULL,
  department VARCHAR(50) NOT NULL,
  email VARCHAR(100) UNIQUE NOT NULL,
  hire_date DATE NOT NULL,
  created_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT now(),
  updated_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT now()
);

-- 2. Tabel Kriteria
CREATE TABLE IF NOT EXISTS public.criteria (
  id UUID NOT NULL DEFAULT gen_random_uuid() PRIMARY KEY,
  name VARCHAR(50) NOT NULL,
  type VARCHAR(10) NOT NULL CHECK (type IN ('Benefit', 'Cost')),
  weight DECIMAL(5,2) NOT NULL CHECK (weight >= 0 AND weight <= 100),
  category VARCHAR(30) NOT NULL,
  scale VARCHAR(20) NOT NULL,
  created_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT now(),
  updated_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT now()
);

-- 3. Tabel Skor Evaluasi (Sistem Fleksibel)
CREATE TABLE IF NOT EXISTS public.evaluation_scores (
  id UUID NOT NULL DEFAULT gen_random_uuid() PRIMARY KEY,
  employee_id UUID NOT NULL REFERENCES public.employees(id) ON DELETE CASCADE,
  criteria_id UUID NOT NULL REFERENCES public.criteria(id) ON DELETE CASCADE,
  score DECIMAL(10,2) NOT NULL DEFAULT 0,
  created_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT now(),
  updated_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT now(),
  UNIQUE(employee_id, criteria_id)
);
```

#### B. Tabel SAW (Simple Additive Weighting)

```sql
-- 4. Tabel Metadata Perhitungan SAW
CREATE TABLE IF NOT EXISTS public.saw_calculations (
  id UUID NOT NULL DEFAULT gen_random_uuid() PRIMARY KEY,
  total_employees INTEGER NOT NULL,
  total_criteria INTEGER NOT NULL,
  calculation_date TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT now(),
  created_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT now()
);

-- 5. Tabel Matriks Normalisasi SAW
CREATE TABLE IF NOT EXISTS public.saw_normalized_matrix (
  id UUID NOT NULL DEFAULT gen_random_uuid() PRIMARY KEY,
  employee_id UUID NOT NULL REFERENCES public.employees(id) ON DELETE CASCADE,
  criteria_code VARCHAR(5) NOT NULL,
  raw_value DECIMAL(10,4) NOT NULL,
  normalized_value DECIMAL(10,6) NOT NULL,
  weight DECIMAL(5,4) NOT NULL,
  calculation_date TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT now(),
  created_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT now()
);

-- 6. Tabel Hasil SAW
CREATE TABLE IF NOT EXISTS public.saw_results (
  id UUID NOT NULL DEFAULT gen_random_uuid() PRIMARY KEY,
  employee_id UUID NOT NULL REFERENCES public.employees(id) ON DELETE CASCADE,
  final_score DECIMAL(10,6) NOT NULL,
  converted_score DECIMAL(4,2) NOT NULL,
  rank INTEGER NOT NULL,
  recommendation VARCHAR(50) NOT NULL,
  note VARCHAR(255),
  calculation_date TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT now(),
  created_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT now()
);
```

#### C. Indeks untuk Optimasi Performa

```sql
-- Indeks untuk optimasi query
CREATE INDEX IF NOT EXISTS idx_evaluation_scores_employee_id ON public.evaluation_scores(employee_id);
CREATE INDEX IF NOT EXISTS idx_evaluation_scores_criteria_id ON public.evaluation_scores(criteria_id);
CREATE INDEX IF NOT EXISTS idx_evaluation_scores_employee_criteria ON public.evaluation_scores(employee_id, criteria_id);
CREATE INDEX IF NOT EXISTS idx_saw_results_employee_id ON public.saw_results(employee_id);
CREATE INDEX IF NOT EXISTS idx_saw_results_calculation_date ON public.saw_results(calculation_date);
CREATE INDEX IF NOT EXISTS idx_saw_normalized_matrix_employee_id ON public.saw_normalized_matrix(employee_id);
CREATE INDEX IF NOT EXISTS idx_saw_normalized_matrix_calculation_date ON public.saw_normalized_matrix(calculation_date);
```

#### D. Row Level Security (RLS)

```sql
-- Enable RLS untuk semua tabel
ALTER TABLE public.employees ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.criteria ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.evaluation_scores ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.saw_calculations ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.saw_normalized_matrix ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.saw_results ENABLE ROW LEVEL SECURITY;

-- Policies untuk akses publik (sesuaikan dengan kebutuhan keamanan)
CREATE POLICY "Allow all operations on employees" ON public.employees FOR ALL USING (true);
CREATE POLICY "Allow all operations on criteria" ON public.criteria FOR ALL USING (true);
CREATE POLICY "Allow all operations on evaluation_scores" ON public.evaluation_scores FOR ALL USING (true);
CREATE POLICY "Allow all operations on saw_calculations" ON public.saw_calculations FOR ALL USING (true);
CREATE POLICY "Allow all operations on saw_normalized_matrix" ON public.saw_normalized_matrix FOR ALL USING (true);
CREATE POLICY "Allow all operations on saw_results" ON public.saw_results FOR ALL USING (true);
```

### 2. Insert Data Awal

```sql
-- Insert kriteria default dengan struktur kategori
INSERT INTO public.criteria (name, type, weight, category, scale) VALUES
-- A. Kinerja Inti (Total: 60%)
('Kualitas Kerja', 'Benefit', 15.00, 'A. Kinerja Inti', '1-5'),
('Tanggung Jawab', 'Benefit', 15.00, 'A. Kinerja Inti', '1-5'),
('Kuantitas Kerja', 'Benefit', 10.00, 'A. Kinerja Inti', '1-5'),
('Pemahaman Tugas', 'Benefit', 10.00, 'A. Kinerja Inti', '1-5'),
('Inisiatif', 'Benefit', 5.00, 'A. Kinerja Inti', '1-5'),
('Kerjasama', 'Benefit', 5.00, 'A. Kinerja Inti', '1-5'),

-- B. Kedisiplinan (Total: 25%)
('Jumlah Hari Alpa', 'Cost', 10.00, 'B. Kedisiplinan', 'Hari'),
('Jumlah Keterlambatan', 'Cost', 7.00, 'B. Kedisiplinan', 'Kali'),
('Jumlah Hari Izin', 'Cost', 3.00, 'B. Kedisiplinan', 'Hari'),
('Jumlah Hari Sakit', 'Cost', 3.00, 'B. Kedisiplinan', 'Hari'),
('Pulang Cepat', 'Cost', 2.00, 'B. Kedisiplinan', 'Kali'),

-- C. Faktor Tambahan (Total: 15%)
('Prestasi', 'Benefit', 10.00, 'C. Faktor Tambahan', '0/1'),
('Surat Peringatan', 'Cost', 5.00, 'C. Faktor Tambahan', '0/1');

-- Insert data karyawan contoh
INSERT INTO public.employees (name, position, department, email, hire_date) VALUES
('Budi Santoso', 'Staff Admin', 'Administrasi', 'budi.santoso@as-salam.org', '2020-01-15'),
('Citra Dewi', 'Guru Kelas', 'Pendidikan', 'citra.dewi@as-salam.org', '2019-08-20'),
('Dedi Rahman', 'Staff Keuangan', 'Keuangan', 'dedi.rahman@as-salam.org', '2021-03-10'),
('Eka Putri', 'Guru Bahasa', 'Pendidikan', 'eka.putri@as-salam.org', '2022-07-01'),
('Fajar Nugroho', 'Staff IT', 'Teknologi', 'fajar.nugroho@as-salam.org', '2023-02-15');
```

## 🏃‍♂️ Menjalankan Aplikasi

### Development Mode

```bash
# Jalankan development server
npm run dev

# Aplikasi akan tersedia di http://localhost:8080
```

### Build untuk Production

```bash
# Build aplikasi
npm run build

# Output akan tersedia di folder dist/
```

### Preview Build

```bash
# Preview build production
npm run preview
```

### Linting

```bash
# Jalankan ESLint
npm run lint
```

## 📊 Struktur Database

### Tabel Utama

#### `employees` - Data Karyawan
| Kolom | Tipe | Batasan | Deskripsi |
|-------|------|---------|-----------|
| `id` | UUID | PRIMARY KEY | Identifier unik karyawan |
| `name` | VARCHAR(100) | NOT NULL | Nama lengkap karyawan |
| `position` | VARCHAR(50) | NOT NULL | Jabatan karyawan |
| `department` | VARCHAR(50) | NOT NULL | Departemen karyawan |
| `email` | VARCHAR(100) | UNIQUE, NOT NULL | Email karyawan |
| `hire_date` | DATE | NOT NULL | Tanggal bergabung |
| `created_at` | TIMESTAMPTZ | DEFAULT now() | Waktu pembuatan record |
| `updated_at` | TIMESTAMPTZ | DEFAULT now() | Waktu update terakhir |

#### `criteria` - Kriteria Evaluasi
| Kolom | Tipe | Batasan | Deskripsi |
|-------|------|---------|-----------|
| `id` | UUID | PRIMARY KEY | Identifier unik kriteria |
| `name` | VARCHAR(50) | NOT NULL | Nama kriteria |
| `type` | VARCHAR(10) | CHECK ('Benefit', 'Cost') | Jenis kriteria |
| `weight` | DECIMAL(5,2) | CHECK (0-100) | Bobot kriteria (%) |
| `category` | VARCHAR(30) | NOT NULL | Kategori kriteria |
| `scale` | VARCHAR(20) | NOT NULL | Skala penilaian |
| `created_at` | TIMESTAMPTZ | DEFAULT now() | Waktu pembuatan record |
| `updated_at` | TIMESTAMPTZ | DEFAULT now() | Waktu update terakhir |

#### `evaluation_scores` - Skor Evaluasi (Sistem Fleksibel)
| Kolom | Tipe | Batasan | Deskripsi |
|-------|------|---------|-----------|
| `id` | UUID | PRIMARY KEY | Identifier unik skor |
| `employee_id` | UUID | FK to employees | Referensi ke karyawan |
| `criteria_id` | UUID | FK to criteria | Referensi ke kriteria |
| `score` | DECIMAL(10,2) | DEFAULT 0 | Nilai evaluasi |
| `created_at` | TIMESTAMPTZ | DEFAULT now() | Waktu pembuatan record |
| `updated_at` | TIMESTAMPTZ | DEFAULT now() | Waktu update terakhir |

### Tabel SAW (Simple Additive Weighting)

#### `saw_calculations` - Metadata Perhitungan
| Kolom | Tipe | Deskripsi |
|-------|------|-----------|
| `id` | UUID | Identifier unik perhitungan |
| `total_employees` | INTEGER | Jumlah karyawan yang dihitung |
| `total_criteria` | INTEGER | Jumlah kriteria yang digunakan |
| `calculation_date` | TIMESTAMPTZ | Waktu perhitungan |
| `created_at` | TIMESTAMPTZ | Waktu pembuatan record |

#### `saw_normalized_matrix` - Matriks Normalisasi
| Kolom | Tipe | Deskripsi |
|-------|------|-----------|
| `id` | UUID | Identifier unik |
| `employee_id` | UUID | Referensi ke karyawan |
| `criteria_code` | VARCHAR(5) | Kode kriteria (C1, C2, dst) |
| `raw_value` | DECIMAL(10,4) | Nilai asli |
| `normalized_value` | DECIMAL(10,6) | Nilai setelah normalisasi |
| `weight` | DECIMAL(5,4) | Bobot kriteria |
| `calculation_date` | TIMESTAMPTZ | Waktu perhitungan |
| `created_at` | TIMESTAMPTZ | Waktu pembuatan record |

#### `saw_results` - Hasil Akhir SAW
| Kolom | Tipe | Deskripsi |
|-------|------|-----------|
| `id` | UUID | Identifier unik |
| `employee_id` | UUID | Referensi ke karyawan |
| `final_score` | DECIMAL(10,6) | Skor akhir SAW |
| `converted_score` | DECIMAL(4,2) | Skor konversi (skala 1-5) |
| `rank` | INTEGER | Peringkat karyawan |
| `recommendation` | VARCHAR(50) | Rekomendasi |
| `note` | VARCHAR(255) | Catatan tambahan |
| `calculation_date` | TIMESTAMPTZ | Waktu perhitungan |
| `created_at` | TIMESTAMPTZ | Waktu pembuatan record |

## 🏗️ Arsitektur Aplikasi

### Struktur Folder

```
src/
├── components/           # Komponen React
│   ├── ui/              # Komponen UI dasar (shadcn/ui)
│   ├── AuthProvider.tsx # Provider autentikasi
│   ├── ProtectedRoute.tsx # Route protection
│   ├── Navbar.tsx       # Navigation bar
│   ├── EmployeeForm.tsx # Form input evaluasi
│   ├── EmployeeManagement.tsx # CRUD karyawan
│   ├── CriteriaTable.tsx # Tampilan kriteria
│   ├── CriteriaManagement.tsx # CRUD kriteria
│   ├── SAWCalculator.tsx # Perhitungan SAW
│   ├── ResultsDisplay.tsx # Tampilan hasil
│   └── EditEmployeeDialog.tsx # Dialog edit evaluasi
├── pages/               # Halaman utama
│   ├── Index.tsx        # Dashboard utama
│   ├── Auth.tsx         # Halaman autentikasi
│   └── NotFound.tsx     # Halaman 404
├── types/               # Type definitions
│   └── database.ts      # Database types
├── hooks/               # Custom hooks
│   ├── use-toast.ts     # Toast notifications
│   └── use-mobile.tsx   # Mobile detection
├── integrations/        # Integrasi eksternal
│   └── supabase/        # Konfigurasi Supabase
├── lib/                 # Utilities
│   └── utils.ts         # Helper functions
└── main.tsx            # Entry point aplikasi
```

### Komponen Utama

#### 1. AuthProvider
- Mengelola state autentikasi global
- Menyediakan context untuk komponen child
- Handle login/logout functionality

#### 2. Index (Dashboard)
- Komponen utama dengan tab navigation
- Mengelola state global aplikasi
- Koordinasi antar komponen

#### 3. EmployeeForm
- Form input evaluasi karyawan
- Sistem evaluasi fleksibel berdasarkan kriteria database
- CRUD evaluasi dengan validasi

#### 4. SAWCalculator
- Implementasi algoritma SAW
- Normalisasi data otomatis
- Penyimpanan hasil ke database

#### 5. ResultsDisplay
- Visualisasi hasil evaluasi
- Grafik dan chart interaktif
- Fitur cetak laporan

## 📱 Cara Penggunaan

### 1. Login ke Sistem
1. Buka aplikasi di browser
2. Masukkan email dan password
3. Klik "Masuk" atau daftar akun baru

### 2. Manajemen Kriteria
1. Akses tab "Kelola Kriteria"
2. Tambah kriteria baru dengan:
   - Nama kriteria
   - Jenis (Benefit/Cost)
   - Bobot (%)
   - Kategori
   - Skala penilaian
3. Sistem akan otomatis menyesuaikan bobot untuk mempertahankan total 100%

### 3. Manajemen Karyawan
1. Akses tab "Kelola Karyawan"
2. Tambah karyawan baru dengan data:
   - Nama lengkap
   - Jabatan
   - Departemen
   - Email
   - Tanggal bergabung
3. Edit atau hapus data karyawan yang sudah ada

### 4. Input Data Evaluasi
1. Akses tab "Input Data"
2. Pilih karyawan yang akan dievaluasi
3. Isi nilai untuk setiap kriteria berdasarkan skala yang ditentukan
4. Simpan evaluasi

### 5. Perhitungan SAW
1. Akses tab "Perhitungan SAW"
2. Pastikan data kriteria dan evaluasi sudah lengkap
3. Klik "Hitung SAW" untuk memulai perhitungan
4. Sistem akan:
   - Membuat decision matrix
   - Melakukan normalisasi
   - Menghitung skor akhir
   - Menentukan ranking
   - Menyimpan hasil ke database

### 6. Melihat Hasil
1. Akses tab "Hasil & Rekomendasi"
2. Lihat ranking karyawan
3. Analisis grafik perbandingan
4. Cetak laporan jika diperlukan

## 🧮 Metode SAW

### Algoritma Simple Additive Weighting

#### 1. Normalisasi Data

**Untuk Kriteria Benefit (semakin tinggi semakin baik):**
```
Rij = Xij / Max(Xi)
```

**Untuk Kriteria Cost (semakin rendah semakin baik):**
```
Rij = Min(Xi) / Xij
```

#### 2. Perhitungan Skor Akhir
```
Vi = Σ(Wj × Rij)
```

Dimana:
- `Rij` = Nilai normalisasi
- `Xij` = Nilai asli
- `Wj` = Bobot kriteria
- `Vi` = Skor akhir

#### 3. Aturan Khusus Aplikasi

**Normalisasi Benefit (Skala 1-5):**
```
Rij = nilai_asli / 5
```

**Normalisasi Cost:**
```
Rij = 0.000 jika nilai > 0
Rij = 1.000 jika nilai = 0
```

**Konversi Skor:**
```
Skor Konversi = Skor SAW × 5
```

**Kategori Skor:**
- 5.0: Baik Sekali
- 4.1-4.9: Baik
- 3.1-4.0: Cukup
- 2.1-3.0: Kurang
- 0.0-2.0: Kurang Sekali

**Rekomendasi:**
- Skor ≥ 4.0: "Dapat diperpanjang" + "Kandidat promosi"
- Skor ≥ 3.0: "Dapat diperpanjang"
- Skor < 3.0: "Diberhentikan"
- Alpa > 10 hari: "Diberhentikan" (otomatis)

## 📡 API Documentation

### Supabase Client Methods

#### Employees
```typescript
// Get all employees
const { data, error } = await supabase
  .from('employees')
  .select('*')
  .order('name');

// Insert new employee
const { data, error } = await supabase
  .from('employees')
  .insert([employeeData]);

// Update employee
const { data, error } = await supabase
  .from('employees')
  .update(employeeData)
  .eq('id', employeeId);

// Delete employee
const { data, error } = await supabase
  .from('employees')
  .delete()
  .eq('id', employeeId);
```

#### Criteria
```typescript
// Get all criteria
const { data, error } = await supabase
  .from('criteria')
  .select('*')
  .order('category');

// Upsert criteria
const { data, error } = await supabase
  .from('criteria')
  .upsert([criteriaData]);
```

#### Evaluation Scores
```typescript
// Get evaluation scores with joins
const { data, error } = await supabase
  .from('evaluation_scores')
  .select(`
    *,
    employees!inner(id, name, position, department),
    criteria!inner(id, name, type, weight, category, scale)
  `);

// Upsert evaluation scores
const { data, error } = await supabase
  .from('evaluation_scores')
  .upsert(evaluationScoresData, { 
    onConflict: 'employee_id,criteria_id' 
  });
```

#### SAW Results
```typescript
// Save SAW results
const { data, error } = await supabase
  .from('saw_results')
  .insert(sawResultsData);

// Get latest SAW results
const { data, error } = await supabase
  .from('saw_results')
  .select(`
    *,
    employees!inner(id, name, position, department)
  `)
  .order('rank');
```

## 🚀 Deployment

### Deploy ke Netlify

1. **Build Project**
   ```bash
   npm run build
   ```

2. **Deploy Manual**
   - Login ke [Netlify](https://netlify.com)
   - Drag & drop folder `dist` ke dashboard
   - Tambahkan environment variables di Site Settings

3. **Deploy via Git**
   - Connect repository GitHub ke Netlify
   - Set build command: `npm run build`
   - Set publish directory: `dist`
   - Tambahkan environment variables:
     - `VITE_SUPABASE_URL`
     - `VITE_SUPABASE_ANON_KEY`

### Deploy ke Vercel

1. **Push ke GitHub**
   ```bash
   git add .
   git commit -m "Deploy to production"
   git push origin main
   ```

2. **Deploy ke Vercel**
   - Login ke [Vercel](https://vercel.com)
   - Import repository dari GitHub
   - Framework preset: Vite
   - Build command: `npm run build`
   - Output directory: `dist`
   - Tambahkan environment variables

### Environment Variables untuk Production

```env
VITE_SUPABASE_URL=your_production_supabase_url
VITE_SUPABASE_ANON_KEY=your_production_supabase_anon_key
```

## 🧪 Testing

### Manual Testing Checklist

#### Autentikasi
- [ ] Login dengan email/password valid
- [ ] Login dengan kredensial invalid
- [ ] Register akun baru
- [ ] Logout dari sistem

#### Manajemen Karyawan
- [ ] Tambah karyawan baru
- [ ] Edit data karyawan
- [ ] Hapus karyawan
- [ ] Validasi email unique

#### Manajemen Kriteria
- [ ] Tambah kriteria baru
- [ ] Edit kriteria existing
- [ ] Hapus kriteria
- [ ] Auto-adjustment bobot

#### Input Evaluasi
- [ ] Input evaluasi karyawan baru
- [ ] Edit evaluasi existing
- [ ] Validasi range nilai
- [ ] Hapus evaluasi

#### Perhitungan SAW
- [ ] Hitung SAW dengan data lengkap
- [ ] Muat hasil tersimpan
- [ ] Hapus data tersimpan
- [ ] Validasi hasil perhitungan

#### Hasil & Laporan
- [ ] Tampilan ranking
- [ ] Grafik dan chart
- [ ] Cetak laporan
- [ ] Analisis rekomendasi

### Performance Testing

```bash
# Lighthouse audit (install lighthouse CLI)
npm install -g lighthouse
lighthouse http://localhost:8080 --view

# Bundle analyzer
npm run build
npx vite-bundle-analyzer dist
```

## 🔧 Troubleshooting

### Error: Supabase connection failed
**Penyebab:**
- URL atau API key Supabase salah
- Project Supabase tidak aktif
- Network connectivity issues

**Solusi:**
1. Verifikasi credentials di `.env.local`
2. Cek status project di Supabase Dashboard
3. Test koneksi dengan:
   ```bash
   curl -H "apikey: YOUR_ANON_KEY" "YOUR_SUPABASE_URL/rest/v1/"
   ```

### Error: Build failed
**Penyebab:**
- Dependencies tidak terinstall
- TypeScript errors
- Environment variables missing

**Solusi:**
1. Clean install dependencies:
   ```bash
   rm -rf node_modules package-lock.json
   npm install
   ```
2. Check TypeScript errors:
   ```bash
   npx tsc --noEmit
   ```
3. Verify environment variables

### Error: Database query failed
**Penyebab:**
- RLS policies tidak diatur
- Tabel tidak ada
- Foreign key constraints

**Solusi:**
1. Verifikasi RLS policies di Supabase Dashboard
2. Jalankan ulang migration scripts
3. Check foreign key relationships

### Performance Issues
**Optimasi:**
1. Enable React Query caching
2. Implement component memoization:
   ```typescript
   const MemoizedComponent = React.memo(Component);
   ```
3. Use lazy loading:
   ```typescript
   const LazyComponent = React.lazy(() => import('./Component'));
   ```
4. Optimize database queries dengan proper indexing

### Memory Leaks
**Pencegahan:**
1. Cleanup useEffect:
   ```typescript
   useEffect(() => {
     const subscription = supabase
       .channel('changes')
       .on('postgres_changes', callback)
       .subscribe();
     
     return () => {
       subscription.unsubscribe();
     };
   }, []);
   ```

## 🤝 Contributing

### Development Workflow

1. **Fork repository**
2. **Create feature branch**
   ```bash
   git checkout -b feature/nama-fitur
   ```
3. **Make changes**
4. **Test thoroughly**
5. **Commit with conventional commits**
   ```bash
   git commit -m "feat: tambah fitur evaluasi batch"
   ```
6. **Push and create PR**

### Code Style

- Gunakan TypeScript untuk type safety
- Follow ESLint rules
- Gunakan Prettier untuk formatting
- Tulis komentar untuk logika kompleks
- Gunakan naming convention yang konsisten

### Commit Message Convention

```
feat: menambah fitur baru
fix: memperbaiki bug
docs: update dokumentasi
style: perubahan formatting
refactor: refactoring code
test: menambah atau memperbaiki test
chore: maintenance tasks
```

## 📄 License

Project ini menggunakan MIT License.

```
MIT License

Copyright (c) 2024 Yayasan As-Salam Joglo

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

## 📞 Support & Contact

### Technical Support
- **Email**: support@as-salam.org
- **GitHub Issues**: [Link ke repository issues]
- **Documentation**: [Link ke dokumentasi lengkap]

### Development Team
- **Lead Developer**: [Nama]
- **Database Administrator**: [Nama]
- **UI/UX Designer**: [Nama]

### Feedback & Suggestions
Kami sangat menghargai feedback dan saran untuk pengembangan sistem ini. Silakan buat issue di GitHub repository atau hubungi tim development.

---

## 🎯 Roadmap

### Version 2.0 (Planned)
- [ ] Multi-tenant support
- [ ] Advanced reporting dashboard
- [ ] Email notifications
- [ ] Bulk import/export
- [ ] API endpoints untuk integrasi
- [ ] Mobile app (React Native)

### Version 1.1 (In Progress)
- [ ] Unit testing dengan Vitest
- [ ] Integration testing
- [ ] Performance optimization
- [ ] Enhanced error handling

---

**Selamat menggunakan Sistem Evaluasi Kinerja Karyawan!** 🎉

*Dikembangkan dengan ❤️ untuk Yayasan As-Salam Joglo*