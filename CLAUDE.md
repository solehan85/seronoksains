# Eduverse — Sains Interaktif (Project Brief)

## 1. Konteks Projek

Ini adalah produk ke-3 dalam ekosistem **Eduverse by Mosobimo**, menyertai:
- **Fun Math** (RM29) — modul matematik berasaskan permainan
- **Seronoknya Bahasa** (RM45) — modul Bahasa Melayu, 16 game, Firebase project `seronoknyabahasa`
- **Seronok Sains** (produk baru — projek ini)

Nama produk: **"Seronok Sains"**

Target pengguna: guru, ibu bapa, reseller — pelajar Tahun 1-6 sekolah rendah, kurikulum **KSSR**.

Konsep teras: **"campur-campur"** — mekanik permainan generik yang boleh diguna pakai merentas semua Tahun dan topik (bukan 1 game per topik), supaya pembangunan lebih scalable dan tidak rigid macam buku teks.

---

## 2. Infrastruktur Sedia Ada (WAJIB REUSE, JANGAN BINA BARU)

- **Firebase project:** `seronoknyabahasa` (region: `asia-southeast1`, Firestore)
- **Sistem login pelajar sedia ada:** Nama + Kelas + PIN 4-digit (di-hash SHA-256)
- **Struktur data pelajar:** `/students/{studentId}` — accessible merentas semua game/subjek
- **Pattern kod rujukan:** "Ninja Math" (canvas-based, procedural question generator, Firebase leaderboard, Web Audio API) dan "Money Master" (dynamic Firebase import dengan graceful offline degradation) — ikut pattern single-file HTML5 self-contained yang sama untuk konsistensi.

**Tugasan baru dalam projek ini:** tambah collection Firestore khusus Sains, dan tambah lapisan **Parent Account** (lihat Seksyen 5).

---

## 3. Breakdown Topik Sains ikut Tahun (KSSR)

| Tahun | Topik Utama |
|---|---|
| 1 | Deria (lihat/dengar/rasa/hidu/sentuh), Haiwan & Tumbuhan sekeliling, Bahagian Badan, Keperluan Asas Hidupan |
| 2 | Pertumbuhan Haiwan & Tumbuhan, Bahan (keras/lembut/kasar/licin), Cahaya & Bayang, Kesihatan Diri |
| 3 | Hidupan (ciri & kepelbagaian), Jirim (pepejal/cecair/gas), Bumi & Angkasa (siang/malam), Tenaga (bunyi) |
| 4 | Sistem Badan Manusia (asas), Interaksi Hidupan (rantai makanan), Sifat Bahan, Elektrik & Litar Ringkas, Daya & Gerakan |
| 5 | Kitaran Hidup, Ekosistem, Perubahan Keadaan Jirim, Tenaga Boleh Diperbaharui, Bumi-Bulan-Matahari |
| 6 | Sistem Badan (lanjutan), Pemuliharaan Alam Sekitar, Kestabilan Fizikal & Kimia, Teknologi & Sains dalam Kehidupan |

Setiap topik → **5 set soalan rotation** (elak pelajar hafal jawapan, random pilih 1 set setiap kali main).

---

## 4. Mekanik Permainan (10 Mekanik, MVP = 4 Core Dahulu)

| # | Mekanik | Contoh Penggunaan | Prioriti MVP |
|---|---------|---|---|
| 1 | **Klasifikasi/Sort** (drag objek ke kategori) | Hidupan vs bukan hidupan, pepejal/cecair/gas | ✅ MVP |
| 2 | **Label Rajah** (drag label ke bahagian gambar) | Bahagian tumbuhan, litar elektrik | ✅ MVP |
| 3 | **Quiz Pantas/Speed Round** | Ulangkaji cepat semua topik | ✅ MVP |
| 4 | **Simulasi Mini Interaktif** | Kitaran air, litar lengkap/terbuka | ✅ MVP |
| 5 | Sebab-Akibat/Padan | Rantai makanan, kitaran hidup | Fasa 2 |
| 6 | Timeline/Turutan | Proses fotosintesis, sistem badan | Fasa 2 |
| 7 | Memory Match | Istilah Sains + gambar | Fasa 2 |
| 8 | Eksperimen Ramalan | Sifat bahan, daya & gerakan | Fasa 2 |
| 9 | Peta Minda Interaktif | Rumusan ekosistem/sistem badan | Fasa 2 |
| 10 | Teka & Pemerhatian | Sifat bahan, cuaca | Fasa 2 |

---

## 5. Struktur Data Firestore (Cadangan)

```
/science_questions/{tahun}/{topik}/{set}/{soalan}
  - soalan: string
  - jenis: "klasifikasi" | "label" | "quiz" | "simulasi"
  - data: { ... } // struktur ikut jenis mekanik
  - jawapanBetul: ...

/parents/{parentId}
  - namaParent: string
  - emailOrPhone: string
  - children: [studentId1, studentId2, ...]

/students/{studentId}  // EXISTING — tambah field baru
  - namaAnak: string
  - kelas: string
  - pinHash: string (SHA-256)
  - parentId: string  // NEW — link ke parent account
  - tahun: number      // NEW — untuk filter kandungan Sains ikut Tahun
```

### Flow Login Baru (Parent → Dropdown Anak)
1. Parent login sekali guna akaun parent (email/no telefon).
2. Dashboard fetch semua `studentId` di bawah `parentId` tersebut.
3. Paparkan sebagai **dropdown/avatar selection** — parent/anak klik nama, terus masuk game.
4. PIN 4-digit kekal **optional** sebagai lapisan kawalan (boleh toggle on/off ikut keperluan — parent tentukan sama ada nak anak leraikan sendiri profil tanpa PIN, atau perlu PIN setiap kali tukar profil).
5. Tak perlu isi nama/kelas berulang — hanya kali pertama semasa daftar anak dalam dashboard.

---

## 6. Arahan Visual & Animasi (WAJIB — "Boleh Dibanggakan", Bukan Kaku)

### Mascot Rasmi: "Burung Sains" 🐦
Watak burung biru comel (ditetapkan selepas beberapa prototaip dibandingkan: burung hantu, gajah, kucing, burung — burung dipilih sebagai rasmi).

- **Warna:** Badan biru cerah `#4FC3E0`, sayap/aksen biru gelap `#3B9FC4`, perut cream `#FFF6EC`, paruh & kaki oren `#FF9F43`
- **Ciri rupa:** Badan bulat gempal, jambul kecil melengkung di atas kepala, ekor kipas kecil di belakang, sepasang sayap di sisi badan, mata besar bulat dengan pupil navy `#1B3358`
- **Animasi Idle:** Sayap kepak perlahan berterusan (delay antara sayap kiri/kanan untuk kesan natural, bukan simetri kaku) + badan bob naik-turun lembut
- **Animasi Gembira (jawapan betul):** Sayap terkepak besar ke atas (macam bertepuk), mata membesar, kening naik, disertai confetti burst + bunyi "ding" melodi + speech bubble pujian rawak
- **Animasi Cuba Lagi (jawapan silap):** Sayap tunduk sedikit, kening kerut simpati, mata mengecil, disertai gentle shake pada bekas jawapan (BUKAN penalti keras) + speech bubble sokongan
- **Prinsip emosi:** Reaksi silap mesti terasa "cuba lagi" bukan "dihukum" — jaga kesan psikologi kanak-kanak

### Prinsip Am (Guna untuk Semua Mekanik)
1. **Micro-interactions pada setiap aksi** — bukan static betul/salah. Betul = bounce + confetti burst + sound pop. Salah = gentle shake.
2. **Easing natural** — guna `cubic-bezier` / spring easing (overshoot sikit sebelum settle), BUKAN linear.
3. **Palette warna arcade konsisten**: Langit `#29ABE2`, Kuning matahari `#FFD23F`, Hijau segar `#4ECB71` (betul), Koral lembut `#FF6B6B` (cuba lagi), Navy `#1B3358` (teks/frame) — ditambah warna mascot burung di atas.
4. **Mascot burung react ikut jawapan pelajar** di SETIAP mekanik (bukan hanya Klasifikasi) — guna struktur SVG & CSS class yang sama (`.wing`, `.brow`, `#pupilL/#pupilR`) supaya senang extend merentas mekanik lain.
5. **Progress bar/stars** — animasi "terisi" satu-satu, bukan terus penuh.
6. Rujuk animasi & sound design pattern yang sama macam "Ninja Math" (Web Audio API) untuk konsistensi across produk Eduverse.

**Fail rujukan prototaip:** `klasifikasi-demo-burung.html` (mekanik Klasifikasi, Tahun 1, topik Hidupan/Bukan Hidupan) — guna fail ini sebagai base pattern (struktur HTML/CSS/JS, warna, easing, mascot SVG) untuk bina 3 mekanik core lain (Label Rajah, Quiz Pantas, Simulasi Mini).

---

## 7. Sprint Plan 7 Hari (MVP — 4 Mekanik Core)

| Hari | Fokus |
|---|---|
| 1 | Setup Firestore Sains + extend sistem login (Parent Account layer) + rangka UI/tema arcade |
| 2 | Mekanik Klasifikasi/Sort + isi set soalan Tahun 1-2 |
| 3 | Mekanik Label Rajah + isi set soalan Tahun 3-4 |
| 4 | Mekanik Quiz Pantas + isi set soalan semua Tahun |
| 5 | Mekanik Simulasi Mini Interaktif (paling kompleks) |
| 6 | Isi baki soalan Tahun 5-6 + polish animasi & sound effect |
| 7 | Testing menyeluruh (semua Tahun/mekanik/dropdown login) + fix bug + sedia soft-launch |

⚠️ **Nota realiti:** 6 Tahun × 5 set × 4 mekanik = **120 set soalan**. Ini selalu jadi bottleneck berbanding coding. Sediakan bank soalan dalam spreadsheet/JSON dahulu (draf AI + review manual) supaya hari coding tak terhenti tunggu content.

---

## 8. Prinsip Editorial (Konsisten dengan Produk Eduverse Lain)

- Semua data hasil, statistik, testimoni mesti **data sebenar sahaja** — tiada rekaan.
- Tiada urgency/scarcity tactics palsu dalam marketing — pricing flat & permanent (ikut pattern Fun Math/Seronoknya Bahasa).
- Reseller program (jika ada untuk produk ini): salespage siap + panduan setup + bahan promosi sahaja — tiada hak rebrand atau sokongan berterusan.

---

## 9. Status & Langkah Seterusnya

- [x] Nama produk final: **Seronok Sains**
- [x] Mascot rasmi: **Burung Sains** (biru comel) — spesifikasi dalam Seksyen 6
- [x] Prototaip 1 mekanik (Klasifikasi) siap — `klasifikasi-demo-burung.html`
- [ ] Setup Firestore collections baru
- [ ] Bina Parent Account layer + dropdown login
- [ ] Sedia bank soalan (120 set) dalam spreadsheet/JSON sebelum mula coding penuh
- [ ] Ikut sprint plan 7 hari untuk 4 mekanik core
