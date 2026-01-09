# uas---pemrogramanmobile
# 📱 APLIKASI CATATAN HARIAN
**Tugas Pemrograman Mobile**

**Nama:** [Nama Kamu]  
**NIM:** [NIM Kamu]  
**Kelas:** [Kelas Kamu]  

---

## 📌 Deskripsi Project

Aplikasi **Catatan Harian** adalah aplikasi mobile Android yang memungkinkan pengguna untuk:
- ✅ Membuat, membaca, mengupdate, dan menghapus catatan (CRUD)
- ✅ Menambahkan foto dari kamera atau galeri
- ✅ Menandai catatan sebagai berbintang (favorit)
- ✅ Mengunci catatan penting
- ✅ Mencari catatan berdasarkan judul/isi
- ✅ Splash screen dengan deteksi lokasi (Indonesia/US)
- ✅ Design dark theme dengan warna kuning sebagai aksen

---

## 🎯 Fitur Utama

### 1️⃣ **Splash Screen dengan Deteksi Lokasi**
- Mendeteksi lokasi pengguna menggunakan **GPS**
- Jika di **Indonesia**: Tampil bendera 🇮🇩 + "Selamat Datang"
- Jika di **Amerika Serikat**: Tampil bendera 🇺🇸 + "Welcome"
- Durasi splash screen: **3 detik**

### 2️⃣ **CRUD Catatan Lengkap**
- **Create**: Tambah catatan baru dengan judul, isi, foto
- **Read**: Tampilkan semua catatan dalam RecyclerView
- **Update**: Edit catatan yang sudah ada
- **Delete**: Hapus catatan dengan long press + konfirmasi

### 3️⃣ **Fitur Multimedia**
- **Ambil Foto**: Menggunakan kamera device
- **Tambah Gambar**: Dari galeri
- **Simpan Foto**: Di internal storage aplikasi

### 4️⃣ **Fitur Organisasi**
- **Star/Bintang**: Tandai catatan favorit
- **Lock/Kunci**: Tandai catatan penting
- **List Mode**: Buat checklist dalam catatan
- **Filter Menu**: Tampilkan hanya catatan berbintang atau terkunci

### 5️⃣ **Search Catatan**
- Real-time search saat mengetik
- Cari berdasarkan judul atau isi catatan

### 6️⃣ **Database SQLite**
- Penyimpanan lokal di device
- Tabel `notes` dengan 7 kolom (id, title, content, date, starred, locked, image_path)

---

## 📁 Struktur Project
```
app/
├── manifests/
│   └── AndroidManifest.xml          → Deklarasi activity & permission
│
├── java/com.example.catatanharian/
│   ├── AddEditNoteActivity.java     → Tambah/Edit catatan
│   ├── DatabaseHelper.java          → SQLite database (CRUD)
│   ├── MainActivity.java            → Halaman utama (daftar catatan)
│   ├── Note.java                    → Model data catatan
│   ├── NoteAdapter.java             → RecyclerView adapter
│   └── SplashActivity.java          → Splash screen + lokasi
│
├── res/
│   ├── drawable/                    → Icon & background
│   │   ├── buku.jpeg                → Icon aplikasi
│   │   ├── indonesia.png            → Bendera Indonesia
│   │   ├── us.png                   → Bendera Amerika
│   │   ├── bg_toast.xml             → Background toast
│   │   └── dialog_background.xml    → Background dialog
│   │
│   ├── layout/
│   │   ├── activity_add_edit_note.xml    → Layout tambah/edit
│   │   ├── activity_main.xml             → Layout halaman utama
│   │   ├── activity_splash.xml           → Layout splash screen
│   │   ├── custom_toast.xml              → Layout toast custom
│   │   ├── dialog_image_picker.xml       → Dialog pilih foto
│   │   └── item_note.xml                 → Item catatan di RecyclerView
│   │
│   ├── menu/
│   │   └── menu_main.xml            → Menu filter (semua/bintang/kunci)
│   │
│   ├── values/
│   │   ├── colors.xml               → Definisi warna
│   │   ├── strings.xml              → String resource
│   │   └── refs.xml                 → Reference
│   │
│   └── xml/
│       ├── backup_rules.xml
│       └── data_extraction_rules.xml
│
└── Gradle Scripts/
    ├── build.gradle.kts (Module: app)    → Dependencies
    └── build.gradle.kts (Project)
```

---

## 🔧 Penjelasan File Utama

### 1️⃣ **AndroidManifest.xml**

File konfigurasi aplikasi yang mendeklarasikan:
- **Permission**: Akses lokasi GPS dan kamera
- **Activity**: 3 activity (Splash, Main, AddEdit)
- **Theme**: NoActionBar (custom navbar)
```xml
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.CAMERA" />

<activity android:name=".SplashActivity" android:exported="true">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
```

**Penjelasan:**
- `SplashActivity` sebagai **LAUNCHER** (activity pertama)
- `exported="true"` agar bisa diakses system
- Permission lokasi untuk fitur deteksi negara

---

### 2️⃣ **DatabaseHelper.java** (Model - SQLite)

Class untuk mengelola database SQLite dengan pola **OOP**.

**Struktur Tabel:**
```sql
CREATE TABLE notes (
    id INTEGER PRIMARY KEY,
    title TEXT,
    content TEXT,
    date TEXT,
    starred INTEGER,    -- 0 = false, 1 = true
    locked INTEGER,     -- 0 = false, 1 = true
    image_path TEXT
)
```

**Method Penting:**
```java
public void addNote(Note note)              // Tambah catatan baru
public Note getNote(int id)                 // Ambil 1 catatan by ID
public List<Note> getAllNotes()             // Ambil semua catatan
public List<Note> getStarredNotes()         // Ambil catatan berbintang
public List<Note> getLockedNotes()          // Ambil catatan terkunci
public List<Note> searchNotes(String keyword) // Cari catatan
public int updateNote(Note note)            // Update catatan
public void deleteNote(int id)              // Hapus catatan
```

**Penjelasan:**
- Class extends `SQLiteOpenHelper` (OOP pattern)
- Semua operasi database ada di 1 class
- Data disimpan **lokal** di device (tidak butuh internet)

---

### 3️⃣ **SplashActivity.java** (Splash Screen + Lokasi)

Activity pertama yang muncul saat aplikasi dibuka.

**Fitur:**
1. **Deteksi Lokasi GPS** menggunakan `FusedLocationProviderClient`
2. **Geocoding**: Konversi koordinat → kode negara
3. **Conditional Display**:
   - Jika di US → Bendera 🇺🇸 + "Welcome"
   - Selain US/gagal → Bendera 🇮🇩 + "Selamat Datang"
4. **Auto redirect** ke MainActivity setelah 3 detik

**Flow:**
```
onCreate() 
  → cekPermission() 
  → ambilLokasi() 
  → cekNegara(location) 
  → tampilIndonesia() / tampilAmerika()
  → pindahKeMain() (delay 3 detik)
```

**Code Penting:**
```java
private void cekNegara(Location location) {
    Geocoder geocoder = new Geocoder(this, Locale.getDefault());
    List<Address> list = geocoder.getFromLocation(
        location.getLatitude(),
        location.getLongitude(), 1
    );
    
    String kodeNegara = list.get(0).getCountryCode();
    
    if ("US".equals(kodeNegara)) {
        imgFlag.setImageResource(R.drawable.us);
        txtWelcome.setText("Welcome");
    } else {
        tampilIndonesia();
    }
}
```

---

### 4️⃣ **MainActivity.java** (Halaman Utama)

Activity utama yang menampilkan daftar catatan dalam **RecyclerView**.

**Komponen:**
- `RecyclerView` → Menampilkan list catatan
- `NoteAdapter` → Adapter untuk RecyclerView
- `EditText` → Search box (real-time)
- `ImageView` → Button menu filter
- `LinearLayout` → Button tambah catatan

**Fitur:**
1. **Load Data**: Ambil semua catatan dari database
2. **Search Real-time**: Filter saat mengetik
3. **Menu Filter**: Popup menu (semua/bintang/kunci)
4. **Click**: Buka AddEditNoteActivity untuk edit
5. **Long Click**: Dialog konfirmasi hapus

**Code Penting:**
```java
etSearch.addTextChangedListener(new TextWatcher() {
    @Override
    public void onTextChanged(CharSequence s, int start, int before, int count) {
        searchNotes(s.toString());
    }
});

btnAdd.setOnClickListener(v -> {
    Intent intent = new Intent(MainActivity.this, AddEditNoteActivity.class);
    startActivity(intent);
});

@Override
public void onNoteLongClick(Note note) {
    new AlertDialog.Builder(this)
        .setTitle("Hapus Catatan?")
        .setPositiveButton("Hapus", (d, w) -> {
            db.deleteNote(note.getId());
            loadNotes();
        })
        .setNegativeButton("Batal", null)
        .show();
}
```

---

### 5️⃣ **AddEditNoteActivity.java** (Tambah/Edit Catatan)

Activity untuk membuat atau mengedit catatan.

**Mode:**
1. **Add Mode**: Jika tidak ada `note_id` di Intent
2. **Edit Mode**: Jika ada `note_id` di Intent → load data existing

**Fitur:**
- ✅ Input judul & isi catatan
- ✅ Ambil foto dari **kamera**
- ✅ Pilih gambar dari **galeri**
- ✅ Toggle **star** (berbintang)
- ✅ Toggle **lock** (terkunci)
- ✅ **List mode** (checklist dengan bullet `○ - `)
- ✅ Simpan foto ke internal storage
- ✅ Custom toast saat berhasil simpan

**Code Penting:**

**a) Permission & Activity Result Launcher (Modern API):**
```java
private ActivityResultLauncher<Intent> cameraLauncher;
private ActivityResultLauncher<Intent> galleryLauncher;
private ActivityResultLauncher<String> permissionLauncher;

private void setupCameraLauncher() {
    cameraLauncher = registerForActivityResult(
        new ActivityResultContracts.StartActivityForResult(),
        result -> {
            if (result.getResultCode() == RESULT_OK) {
                Bundle extras = result.getData().getExtras();
                Bitmap imageBitmap = (Bitmap) extras.get("data");
                ivPhoto.setImageBitmap(imageBitmap);
                ivPhoto.setVisibility(View.VISIBLE);
                imagePath = saveImageToStorage(imageBitmap);
            }
        }
    );
}
```

**b) Dialog Pilih Foto:**
```java
private void showImagePickerDialog() {
    View dialogView = LayoutInflater.from(this)
        .inflate(R.layout.dialog_image_picker, null);
    
    AlertDialog dialog = new AlertDialog.Builder(this)
        .setView(dialogView)
        .create();
    
    dialog.getWindow().setBackgroundDrawableResource(android.R.color.transparent);
    
    TextView btnAmbilFoto = dialogView.findViewById(R.id.btnAmbilFoto);
    TextView btnTambahGambar = dialogView.findViewById(R.id.btnTambahGambar);
    
    btnAmbilFoto.setOnClickListener(v -> {
        dialog.dismiss();
        checkCameraPermission();
    });
    
    btnTambahGambar.setOnClickListener(v -> {
        dialog.dismiss();
        openGallery();
    });
    
    dialog.show();
}
```

**c) Simpan Foto ke Internal Storage:**
```java
private String saveImageToStorage(Bitmap bitmap) {
    try {
        File directory = new File(getFilesDir(), "images");
        if (!directory.exists()) {
            directory.mkdirs();
        }
        
        String filename = "IMG_" + System.currentTimeMillis() + ".jpg";
        File file = new File(directory, filename);
        
        FileOutputStream fos = new FileOutputStream(file);
        bitmap.compress(Bitmap.CompressFormat.JPEG, 90, fos);
        fos.close();
        
        return file.getAbsolutePath();
    } catch (Exception e) {
        return "";
    }
}
```

**d) Toggle Star & Lock:**
```java
private void toggleStar() {
    isStarred = !isStarred;
    updateStarIcon();
    Toast.makeText(this, 
        isStarred ? "Berhasil ditambahkan ke berbintang" : "Bintang dihapus", 
        Toast.LENGTH_SHORT).show();
}

private void updateStarIcon() {
    if (isStarred) {
        btnStar.setImageResource(android.R.drawable.star_big_on);
    } else {
        btnStar.setImageResource(android.R.drawable.star_big_off);
    }
}
```

**e) List Mode (Checklist):**
```java
private void toggleListMode() {
    String currentContent = etContent.getText().toString();
    
    if (currentContent.isEmpty()) {
        etContent.setText(CHECK_BULLET);  // "○ - "
    } else {
        if (!currentContent.endsWith("\n")) {
            etContent.append("\n");
        }
        etContent.append("\n" + CHECK_BULLET);
    }
    
    etContent.setSelection(etContent.getText().length());
}
```

**f) Simpan Catatan:**
```java
private void saveNote() {
    String title = etTitle.getText().toString().trim();
    String content = etContent.getText().toString().trim();
    
    // Jika kosong semua, jangan simpan
    if (title.isEmpty() && content.isEmpty() && imagePath.isEmpty()) {
        Toast.makeText(this, "Catatan kosong, tidak disimpan.", Toast.LENGTH_SHORT).show();
        finish();
        return;
    }
    
    // Auto title dari isi jika judul kosong
    if (title.isEmpty()) {
        title = content.substring(0, Math.min(content.length(), 20));
    }
    
    Note note = new Note();
    note.setTitle(title);
    note.setContent(content);
    note.setDate(getCurrentDate());
    note.setStarred(isStarred);
    note.setLocked(isLocked);
    note.setImagePath(imagePath);
    
    if (isEditMode) {
        note.setId(noteId);
        db.updateNote(note);
        showSuccessDialog("Catatan diperbarui");
    } else {
        db.addNote(note);
        showSuccessDialog("Berhasil ditambahkan");
    }
}
```

---

### 6️⃣ **Note.java** (Model Data)

Class POJO (Plain Old Java Object) untuk merepresentasikan 1 catatan.

**Attribute:**
```java
private int id;
private String title;
private String content;
private String date;
private boolean isStarred;
private boolean isLocked;
private String imagePath;
```

**Method:**
- Constructor kosong & full
- Getter & Setter untuk semua attribute

---

### 7️⃣ **NoteAdapter.java** (RecyclerView Adapter)

Adapter untuk menampilkan list catatan di RecyclerView.

**Fitur Custom:**
- **Background bulet** (rounded corner) menggunakan `GradientDrawable`
- **Jarak antar item** 36dp untuk tampilan lebih lega
- **Click listener**: Edit catatan
- **Long click listener**: Hapus catatan

**Code Penting:**
```java
@Override
public void onBindViewHolder(@NonNull NoteViewHolder holder, int position) {
    Note note = noteList.get(position);
    
    holder.tvTitle.setText(note.getTitle());
    holder.tvContent.setText(note.getContent());
    holder.tvDate.setText(note.getDate());
    
    // Background rounded
    GradientDrawable bg = new GradientDrawable();
    bg.setColor(Color.parseColor("#2C2C2C"));
    bg.setCornerRadius(40f);
    holder.itemView.setBackground(bg);
    
    // Padding dalam card
    holder.itemView.setPadding(40, 32, 40, 32);
    
    // Margin antar card
    RecyclerView.LayoutParams params = 
        (RecyclerView.LayoutParams) holder.itemView.getLayoutParams();
    params.setMargins(0, 0, 0, 36);
    holder.itemView.setLayoutParams(params);
    
    // Click listener
    holder.itemView.setOnClickListener(v -> listener.onNoteClick(note));
    holder.itemView.setOnLongClickListener(v -> {
        listener.onNoteLongClick(note);
        return true;
    });
}
```

---

## 🎨 UI/UX Design

### **Color Palette**
```xml
<color name="black">#FF000000</color>         → Background utama
<color name="white">#FFFFFFFF</color>         → Text utama
<color name="dark_gray">#2B2B2B</color>       → Search box background
<color name="card_gray">#3A3A3A</color>       → Card background (tidak dipakai)
<color name="yellow">#FFC107</color>          → Accent color (button, icon)
<color name="text_gray">#9E9E9E</color>       → Text hint/secondary
```

### **Typography**
- **Judul Navbar**: 25sp, Bold, White
- **Judul Catatan**: 16sp, Bold, White
- **Isi Catatan**: 13sp, Regular, #CCCCCC
- **Tanggal**: 12sp, Regular, White
- **Button**: 14sp-22sp, Yellow

### **Spacing**
- **Card Padding**: 40dp (horizontal), 32dp (vertical)
- **Card Margin**: 36dp (bottom)
- **Screen Padding**: 16dp
- **Element Spacing**: 8-16dp

### **Layout Design**

#### 1️⃣ **Splash Screen** (`activity_splash.xml`)
- **Background**: Black (#000000)
- **Layout**: Vertical LinearLayout, center gravity
- **Components**:
  - `ImageView`: Bendera (140dp × 90dp)
  - `TextView`: "Selamat Datang" (24sp, Bold, White)

**Screenshot:** Saat aplikasi pertama kali dibuka

---

#### 2️⃣ **Main Activity** (`activity_main.xml`)
- **Background**: Black gradient
- **Layout**: RelativeLayout
- **Components**:
  
  **Header:**
  - `TextView`: "Catatan" (25sp, Bold, White)
  - `ImageView`: Menu icon (40dp × 40dp, Yellow)
  
  **Search Box:**
  - `CardView`: Rounded (25dp), Dark Gray background
  - `EditText`: Hint "cari" (Text Gray)
  - `ImageView`: Search icon (24dp)
  
  **RecyclerView:**
  - Item layout: `item_note.xml`
  - Scroll vertical
  - Padding: 6dp
  
  **Button Add:**
  - `ImageView`: Plus icon (30dp, Yellow)
  - `TextView`: "Tambahkan Catatan" (14sp, White)

**Screenshot:**
- Tampilan daftar catatan kosong
- Tampilan dengan beberapa catatan
- Tampilan saat search
- Menu filter (popup)

---

#### 3️⃣ **Add/Edit Note** (`activity_add_edit_note.xml`)
- **Background**: Black
- **Layout**: RelativeLayout
- **Components**:
  
  **Header:**
  - `TextView btnBack`: "< Catatan" (22sp, Yellow)
  - `TextView btnDone`: "done" (19sp, Bold, Yellow)
  
  **Content (ScrollView):**
  - `EditText etTitle`: Judul (24sp, Bold, White)
  - `ImageView ivPhoto`: Foto (200dp height, gone by default)
  - `EditText etContent`: Isi (18sp, White, multiline)
  
  **Footer:**
  - `ImageView btnList`: List icon (32dp, Yellow)
  - `ImageView btnCamera`: Camera icon (32dp, Yellow)
  - `ImageView btnLock`: Lock icon (32dp, Yellow)
  - `ImageView btnStar`: Star icon (32dp, Yellow)

**Screenshot:**
- Mode tambah (kosong)
- Mode edit (ada data)
- Dengan foto
- Dialog pilih foto
- Toast success

---

#### 4️⃣ **Item Note** (`item_note.xml`)
- **Background**: #2C2C2C (Dark Gray)
- **Corner Radius**: 20dp (dalam code: 40f)
- **Padding**: 40dp horizontal, 32dp vertical
- **Margin**: 36dp bottom
- **Components**:
  - `TextView tvTitle`: Judul (16sp, Bold, White, maxLines=1)
  - `TextView tvContent`: Isi preview (13sp, #CCCCCC, maxLines=2)
  - `TextView tvDate`: Tanggal (12sp, White, right aligned)

**Screenshot:** Tampilan list catatan

---

#### 5️⃣ **Dialog Image Picker** (`dialog_image_picker.xml`)
- **Background**: Custom drawable (rounded, dark)
- **Layout**: Vertical LinearLayout
- **Components**:
  - `TextView`: "Ambil Foto" (18sp, White, centered, padding 20dp)
  - `View`: Divider line (1dp, #444444)
  - `TextView`: "Tambahkan Gambar" (18sp, White, centered, padding 20dp)

**Screenshot:** Dialog saat klik icon camera

---

#### 6️⃣ **Custom Toast** (`custom_toast.xml`)
- **Background**: Custom drawable (rounded)
- **Layout**: Horizontal LinearLayout
- **Components**:
  - `ImageView`: Icon (24dp)
  - `TextView`: Message (16sp, White)

**Screenshot:** Toast saat berhasil simpan/update

---

## 📊 Storyboard Aplikasi

Berdasarkan dokumen storyboard, aplikasi memiliki flow sebagai berikut:

### **Flow 1: Splash & Lokasi**
1. **Splash Screen** → Logo aplikasi (buku)
2. **Deteksi Lokasi** → GPS mengecek posisi
3. **Hasil Deteksi** → Tampil bendera + greeting

### **Flow 2: Daftar Catatan**
4. **Halaman Utama** → List catatan (grouped by date)
5. **Search** → Real-time filtering
6. **Menu Filter** → Popup (Semua/Bintang/Kunci)

### **Flow 3: Tambah/Edit Catatan**
7. **Halaman Isi Catatan** → Input judul & isi
8. **Fitur List** → Checklist mode
9. **Pilih Foto** → Dialog (Kamera/Galeri)
10. **Toggle Star/Lock** → Mark catatan
11. **Konfirmasi Simpan** → Toast "Berhasil ditambahkan"

### **Flow 4: Filter Catatan**
12. **Catatan Berbintang** → Filter icon ★
13. **Catatan Terkunci** → Filter icon 🔒

---

## 📸 Screenshot Guide

### **Ambil Screenshot Saat:**

1. **Splash Indonesia**
   - Buka aplikasi (device di Indonesia)
   - Bendera 🇮🇩 + "Selamat Datang"

2. **Splash Amerika** (Opsional)
   - Buka aplikasi (device di US / fake location)
   - Bendera 🇺🇸 + "Welcome"

3. **Halaman Utama Kosong**
   - Setelah splash, list catatan kosong
   - Ada search box & button "Tambahkan Catatan"

4. **Halaman Utama dengan Data**
   - Setelah ada beberapa catatan
   - Tampil dalam card dark gray

5. **Search Catatan**
   - Ketik di search box
   - List terfilter real-time

6. **Menu Filter**
   - Klik icon menu (☰) di kanan atas
   - Popup muncul (Semua/Bintang/Kunci)

7. **Halaman Tambah Catatan**
   - Klik button "Tambahkan Catatan"
   - Form kosong dengan icon di bawah

8. **Checklist Mode**
   - Klik icon list
   - Muncul bullet "○ - " di content

9. **Dialog Pilih Foto**
   - Klik icon camera
   - Dialog muncul (Ambil Foto / Tambahkan Gambar)

10. **Catatan dengan Foto**
    - Setelah pilih foto
    - Foto muncul di atas content

11. **Toggle Star**
    - Klik icon star
    - Icon berubah (filled)
    - Toast "Berhasil ditambahkan ke berbintang"

12. **Toggle Lock**
    - Klik icon lock
    - Icon berubah (locked)
    - Toast "Berhasil ditambahkan ke kunci"

13. **Toast Simpan**
    - Klik "done"
    - Custom toast muncul di bawah

14. **Halaman Edit**
    - Klik salah satu catatan
    - Data ter-load di form

15. **Filter Berbintang**
    - Klik menu → "Catatan berbintang"
    - Hanya tampil catatan dengan star

16. **Filter Terkunci**
    - Klik menu → "Catatan di kunci"
    - Hanya tampil catatan dengan lock

17. **Konfirmasi Hapus**
    - Long press catatan
    - Dialog "Hapus Catatan?"

---

## 🗄️ Database Schema

**Nama Database:** `notesManager`  
**Versi:** 1

**Tabel `notes`:**

| Kolom | Tipe Data | Keterangan |
|-------|-----------|------------|
| `id` | INTEGER | Primary Key, Auto Increment |
| `title` | TEXT | Judul catatan |
| `content` | TEXT | Isi catatan |
| `date` | TEXT | Tanggal dibuat (dd/MM/yyyy) |
| `starred` | INTEGER | 0 = tidak berbintang, 1 = berbintang |
| `locked` | INTEGER | 0 = tidak dikunci, 1 = dikunci |
| `image_path` | TEXT | Path foto (nullable) |

**Query Create Table:**
```sql
CREATE TABLE notes (
    id INTEGER PRIMARY KEY,
    title TEXT,
    content TEXT,
    date TEXT,
    starred INTEGER,
    locked INTEGER,
    image_path TEXT
)
```

---

## 🚀 Cara Menjalankan Aplikasi

### **Prasyarat:**
1. **Android Studio** (versi Hedgehog atau lebih baru)
2. **JDK 11** atau lebih tinggi
3. **Device Android** (API 36 / Android 15) atau Emulator

### **Langkah-langkah:**

1. **Clone/Download Project**
```
   File → Open → Pilih folder catatanharian
```

2. **Sync Gradle**
   - Android Studio akan otomatis sync dependencies
   - Tunggu sampai selesai (lihat progress bar bawah)

3. **Izinkan Permission** (di AndroidManifest.xml sudah ada)
   - `ACCESS_FINE_LOCATION` → GPS
   - `ACCESS_COARSE_LOCATION` → Network location
   - `CAMERA` → Ambil foto

4. **Build Project**
```
   Build → Make Project (Ctrl+F9)
```

5. **Run di Device/Emulator**
```
   Run → Run 'app' (Shift+F10)
```

6. **Izinkan Permission di Device**
   - Saat pertama kali buka, akan minta izin lokasi
   - Klik "Allow"
   - Saat klik camera, akan minta izin kamera
   - Klik "Allow"

---

## 📱 Dependency yang Digunakan
```kotlin
dependencies {
    // Core Android
    implementation("androidx.appcompat:appcompat:1.6.1")
    implementation("com.google.android.material:material:1.11.0")
    implementation("androidx.activity:activity:1.8.2")
    implementation("androidx.constraintlayout:constraintlayout:2.1.4")
    
    // RecyclerView & CardView
    implementation("androidx.recyclerview:recyclerview:1.3.2")
    implementation("androidx.cardview:cardview:1.0.0")
    
    // Google Play Services (Location)
    implementation("com.google.android.gms:play-services-location:21.0.1")
    
    // Testing
    testImplementation("junit:junit:4.13.2")
    androidTestImplementation("androidx.test.ext:junit:1.1.5")
    androidTestImplementation("androidx.test.espresso:espresso-core:3.5.1")
**Penjelasan:**
- **RecyclerView**: Untuk list catatan yang efficient
- **CardView**: Untuk card design item catatan
- **play-services-location**: Untuk GPS & Geocoding
- **Material**: Design modern (AlertDialog, dll)

---

## 🎓 Konsep Pemrograman yang Diterapkan

### 1️⃣ **Object-Oriented Programming (OOP)**
- **Encapsulation**: Class `Note` dengan getter/setter
- **Inheritance**: `DatabaseHelper extends SQLiteOpenHelper`
- **Abstraction**: Interface `OnNoteClickListener`

### 2️⃣ **Design Pattern**
- **MVC (Model-View-Controller)**:
  - **Model**: `Note.java`, `DatabaseHelper.java`
  - **View**: XML layout files
  - **Controller**: Activity classes
- **Adapter Pattern**: `NoteAdapter` untuk RecyclerView
- **Singleton**: DatabaseHelper instance

### 3️⃣ **Android Components**
- **Activity Lifecycle**: onCreate, onResume
- **Intent**: Passing data antar activity
- **RecyclerView**: Efficient list display
- **SQLite**: Local database
- **SharedPreferences**: (bisa ditambahkan untuk settings)

### 4️⃣ **Modern Android API**
- **ActivityResultLauncher**: Pengganti `startActivityForResult()` (deprecated)
- **FusedLocationProviderClient**: GPS modern
- **ViewBinding**: (bisa ditambahkan untuk best practice)

### 5️⃣ **File Management**
- **Internal Storage**: Simpan foto di `getFilesDir()`
- **Bitmap Compression**: Optimasi ukuran gambar

---

## ✅ Fitur yang Berhasil Diimplementasikan

- ✅ Splash screen dengan deteksi lokasi GPS
- ✅ CRUD catatan lengkap (Create, Read, Update, Delete)
- ✅ RecyclerView dengan custom adapter
- ✅ Search real-time
- ✅ Filter catatan (semua/bintang/kunci)
- ✅ Ambil foto dari kamera
- ✅ Pilih gambar dari galeri
- ✅ Simpan foto ke internal storage
- ✅ Toggle star (berbintang)
- ✅ Toggle lock (terkunci)
- ✅ List mode (checklist)
- ✅ Custom toast notification
- ✅ Dialog pilih foto
- ✅ Konfirmasi hapus
- ✅ Dark theme dengan accent yellow
- ✅ Responsive layout
- ✅ Permission handling (lokasi & kamera)

---

## 🐛 Known Issues & Improvement

**Potential Issues:**
1. Foto tidak otomatis terhapus saat catatan dihapus
2. Tidak ada kompresi foto otomatis (bisa bikin storage penuh)
3. Tidak ada fitur backup/export catatan
4. Search tidak case-insensitive

**Future Improvements:**
1. ✨ Tambah fitur **backup to cloud** (Firebase)
2. ✨ Tambah **reminder/notification** untuk catatan penting
3. ✨ Tambah **kategori/tag** catatan
4. ✨ Tambah **password/PIN** untuk catatan terkunci
5. ✨ Tambah **voice-to-text** untuk isi catatan
6. ✨ Tambah **share catatan** ke social media
7. ✨ Tambah **dark/light theme toggle**

---

## 📝 Kesimpulan

Project **Aplikasi Catatan Harian** berhasil mengimplementasikan:
1. ✅ **CRUD lengkap** dengan SQLite database
2. ✅ **Multimedia integration** (foto dari kamera/galeri)
3. ✅ **GPS & Geocoding** untuk splash screen
4. ✅ **Modern Android API** (ActivityResultLauncher)
5. ✅ **Custom UI** (dark theme, rounded card, custom toast)
6. ✅ **Filter & Search** untuk organisasi catatan
7. ✅ **OOP & Design Pattern** yang baik

Aplikasi ini memenuhi semua requirement tugas pemrograman mobile dan siap untuk dipresentasikan.

---

## 👨‍💻 Teknologi yang Digunakan

| Teknologi | Versi | Kegunaan |
|-----------|-------|----------|
| Android Studio | Hedgehog+ | IDE |
| Java | 11 | Programming Language |
| Gradle | 8.2 | Build Tool |
| SQLite | - | Local Database |
| Google Play Services | 21.0.1 | Location API |
| RecyclerView | 1.3.2 | List Display |
| Material Design | 1.11.0 | UI Components |

---

## 📄 Lisensi

Project ini dibuat untuk keperluan tugas akademik **Pemrograman Mobile**.

---

**© 2025 - Aplikasi Catatan Harian**
