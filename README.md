Klasifikasi-Lingkungan-AI/
├── index.html          # Halaman Login dan Halaman Utama
├── style.css           # Gaya CSS untuk tampilan
├── script.js           # Logika JavaScript dan Integrasi AI
├── model/              # Folder untuk Model Teachable Machine yang Dieksport
│   ├── metadata.json
│   ├── model.json
│   └── weights.bin
└── LAPORAN_PROYEK.pdf  # Dokumen laporan akhir

style.css
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI Klasifikasi Lingkungan</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>

    <div id="login-page" class="page active">
        <h2>👋 Masuk ke Sistem Klasifikasi Lingkungan</h2>
        <form id="login-form">
            <input type="text" id="username" placeholder="Nama Pengguna" required>
            <input type="password" id="password" placeholder="Kata Sandi" required>
            <button type="submit">LOGIN</button>
            <p class="note">Gunakan user: **admin** | pass: **123**</p>
        </form>
    </div>

    <div id="main-page" class="page">
        <header>
            <h1>🌱 AI Klasifikasi Sampah & Edukasi Sosial</h1>
            <button onclick="logout()">Logout</button>
        </header>

        <section id="upload-section">
            <h2>Klasifikasi Objek Lingkungan</h2>
            <p>Unggah gambar sampah (Organik, Anorganik, atau B3) untuk dianalisis oleh AI.</p>
            <input type="file" id="file-input" accept="image/*" onchange="predictImage(event)">
            
            <div id="image-preview-container">
                <img id="image-preview" src="" alt="Pratinjau Gambar" style="display: none;">
            </div>
        </section>

        <hr>

        <section id="result-section">
            <h3>🔬 Hasil Klasifikasi AI</h3>
            <div id="ai-output">Hasil klasifikasi dan tingkat keyakinan akan muncul di sini setelah unggah gambar.</div>
        </section>

        <hr>

        <section id="education-section">
            <h2>💡 Edukasi & Dampak Lingkungan</h2>
            <div id="education-content">
                <h4>Pentingnya Pemilahan Sampah</h4>
                <p>Pemilahan yang tepat adalah langkah pertama menuju pengolahan sampah yang berkelanjutan. Hal ini mengurangi beban TPA, menghemat energi dalam daur ulang, dan mencegah pencemaran bahan berbahaya ke lingkungan.</p>
            </div>
        </section>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs@latest/dist/tf.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/@teachablemachine/image@latest/dist/teachablemachine-image.min.js"></script>
    <script src="script.js"></script>
</body>
</html> 

style.css
body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background-color: #e9ecef;
    color: #343a40;
    margin: 0;
    padding: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
}

.page {
    background-color: #ffffff;
    padding: 40px;
    border-radius: 12px;
    box-shadow: 0 10px 20px rgba(0, 0, 0, 0.05);
    max-width: 650px;
    width: 90%;
    margin: 20px;
    display: none; /* Semua halaman disembunyikan secara default */
}

.page.active {
    display: block; /* Tampilkan halaman yang aktif */
}

/* --- Login Page Styles --- */
#login-page {
    text-align: center;
}

#login-form input, #login-form button {
    width: 100%;
    padding: 12px;
    margin-bottom: 15px;
    border: 1px solid #ced4da;
    border-radius: 6px;
    box-sizing: border-box;
    font-size: 16px;
}

#login-form button {
    background-color: #28a745; /* Green */
    color: white;
    font-weight: bold;
    cursor: pointer;
    transition: background-color 0.3s;
}

#login-form button:hover {
    background-color: #218838;
}

.note {
    font-size: 0.9em;
    color: #6c757d;
}

/* --- Main Page Styles --- */
header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    border-bottom: 1px solid #dee2e6;
    padding-bottom: 15px;
    margin-bottom: 25px;
}

header button {
    padding: 8px 15px;
    background-color: #dc3545; /* Red */
    color: white;
    border: none;
    border-radius: 6px;
    cursor: pointer;
}

#upload-section input[type="file"] {
    display: block;
    margin: 15px 0;
    padding: 10px;
    width: 100%;
    box-sizing: border-box;
    border: 1px solid #ced4da;
    border-radius: 6px;
}

#image-preview-container {
    text-align: center;
    margin-top: 20px;
}

#image-preview {
    max-width: 100%;
    max-height: 350px;
    border: 2px dashed #007bff;
    border-radius: 8px;
    padding: 10px;
}

#ai-output {
    padding: 20px;
    background-color: #f8f9fa;
    border: 1px solid #e9ecef;
    border-left: 5px solid #007bff; /* Blue indicator */
    margin-top: 15px;
    font-size: 1.1em;
}

#ai-output strong {
    color: #007bff;
}

#education-section {
    margin-top: 30px;
}

#education-section h4 {
    color: #28a745; /* Green */
    border-bottom: 1px dashed #28a745;
    padding-bottom: 5px;
}

script.js
// --- Konfigurasi Model Teachable Machine ---
// PASTIKAN Anda MENGGANTI path ini jika model berada di lokasi lain!
const URL = "./model/"; 
let model, maxPredictions;

// --- Navigasi Halaman ---

document.getElementById('login-form').addEventListener('submit', function(e) {
    e.preventDefault();
    const username = document.getElementById('username').value;
    const password = document.getElementById('password').value;

    // Logika Otentikasi Sederhana
    // Anda bisa menggantinya dengan otentikasi yang lebih kompleks
    if (username === 'admin' && password === '123') {
        document.getElementById('login-page').classList.remove('active');
        document.getElementById('main-page').classList.add('active');
        initModel(); // Inisialisasi model saat login sukses
    } else {
        alert('Nama pengguna atau kata sandi salah!');
    }
});

function logout() {
    document.getElementById('main-page').classList.remove('active');
    document.getElementById('login-page').classList.add('active');
    document.getElementById('login-form').reset();
    document.getElementById('ai-output').innerHTML = "Hasil klasifikasi dan tingkat keyakinan akan muncul di sini setelah unggah gambar.";
}

// --- Inisialisasi Model AI ---

async function initModel() {
    const modelURL = URL + "model.json";
    const metadataURL = URL + "metadata.json";

    try {
        model = await tmImage.load(modelURL, metadataURL);
        maxPredictions = model.getTotalClasses();
        console.log("Model AI berhasil dimuat.");
    } catch (error) {
        console.error("Gagal memuat model AI:", error);
        document.getElementById('ai-output').innerHTML = "⚠️ **ERROR:** Gagal memuat model AI. Pastikan folder `model/` ada dan berisi file yang benar.";
    }
}

// --- Fungsi Klasifikasi dan Tampilan Output ---

async function predictImage(event) {
    if (!model) {
        alert("Model AI belum siap. Tunggu atau coba refresh halaman.");
        return;
    }

    const file = event.target.files[0];
    if (!file) return;

    // Tampilkan Pratinjau Gambar
    const imgPreview = document.getElementById('image-preview');
    imgPreview.src = URL.createObjectURL(file);
    imgPreview.style.display = 'block';

    // Buat elemen gambar untuk diprediksi
    const image = new Image();
    image.onload = async () => {
        // Lakukan prediksi
        const prediction = await model.predict(image);
        
        let highestPrediction = { className: "Tidak Dikenal", probability: 0 };

        // Cari hasil prediksi tertinggi
        for (let i = 0; i < maxPredictions; i++) {
            if (prediction[i].probability > highestPrediction.probability) {
                highestPrediction = prediction[i];
            }
        }

        const className = highestPrediction.className;
        const probability = (highestPrediction.probability * 100).toFixed(2);
        
        // Tampilkan Output AI secara JELAS
        document.getElementById('ai-output').innerHTML = `
            Status: ✅ **Klasifikasi Berhasil**<br>
            Objek Diidentifikasi Sebagai: <strong>${className.toUpperCase()}</strong><br>
            Tingkat Keyakinan (Probabilitas): ${probability}%
        `;

        // Tampilkan Informasi Edukasi Sesuai Klasifikasi
        displayEducation(className);
    };
    image.src = URL.createObjectURL(file);
}

// --- Menampilkan Informasi Edukasi Lingkungan ---
function displayEducation(classification) {
    const educationDiv = document.getElementById('education-content');
    let title, info;
    let colorClass = '';

    switch (classification.toUpperCase()) {
        case 'ORGANIK':
            title = "💚 Penanganan Sampah Organik";
            info = `**Organik** adalah sisa makhluk hidup (sisa makanan, kulit buah, daun).
            **Solusi:** Idealnya diolah menjadi kompos (pupuk organik) atau pakan ternak.
            **Dampak Lingkungan:** Pengurangan emisi metana di TPA, penyubur tanah alami.`;
            colorClass = 'green';
            break;
        case 'ANORGANIK':
            title = "💙 Penanganan Sampah Anorganik";
            info = `**Anorganik** adalah bahan non-hayati yang sulit terurai (plastik, kaca, logam, kertas).
            **Solusi:** Dipilah, dibersihkan, dan dikirim ke bank sampah atau fasilitas daur ulang.
            **Dampak Lingkungan:** Mengurangi penumpukan di TPA dan menghemat sumber daya alam melalui daur ulang.`;
            colorClass = 'blue';
            break;
        case 'B3':
            title = "🔴 Penanganan Limbah B3 (Berbahaya & Beracun)";
            info = `**B3** adalah limbah yang mengandung zat berbahaya (baterai, lampu bekas, produk pembersih, elektronik).
            **Solusi:** Harus dikumpulkan terpisah, dilarang dibuang sembarangan, dan diserahkan ke fasilitas pengolahan B3 resmi.
            **Dampak Lingkungan:** Pencegahan kontaminasi air tanah dan rantai makanan oleh logam berat serta zat beracun.`;
            colorClass = 'red';
            break;
        default:
            title = "🔍 Objek Tidak Dikenal";
            info = "Objek tidak dapat diklasifikasikan dengan yakin. Mohon gunakan gambar yang lebih jelas. Lakukan pemilahan secara mandiri berdasarkan jenis materialnya.";
            colorClass = 'gray';
    }

    educationDiv.innerHTML = `
        <h4 class="${colorClass}">${title}</h4>
        <p style="white-space: pre-wrap;">${info}</p>
    `;

    // Opsional: Tambahkan sedikit gaya ke judul edukasi
    educationDiv.querySelector('h4').style.color = (colorClass === 'green' ? '#28a745' : colorClass === 'blue' ? '#007bff' : colorClass === 'red' ? '#dc3545' : '#6c757d');
}
