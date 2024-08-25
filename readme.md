<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Notifikasi Tren</title>
    <style>
        body {
            font-family: Arial, sans-serif;
        }
        
        .container {
            width: 300px;
            margin: 0 auto;
            text-align: left;
        }

        input[type="text"] {
            width: 100%;
            padding: 8px;
            box-sizing: border-box;
            margin-bottom: 10px;
            font-size: 16px;
        }

        button {
            background-color: #333;
            color: white;
            border: none;
            padding: 10px;
            cursor: pointer;
            font-size: 16px;
            border-radius: 5px;
            margin-bottom: 10px;
        }

        button:hover {
            background-color: #555;
        }

        .row {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 10px;
        }

        .row > button, .row > input[type="text"] {
            flex: 1;
            margin-right: 10px;
        }

        .row > button:last-child {
            margin-right: 0;
        }

        #notifikasi {
            border: 2px solid #333;
            padding: 10px;
            min-height: 60px;
            box-sizing: border-box;
            margin-bottom: 10px;
            font-size: 14px;
        }

        .history-clock-container {
            display: flex;
            justify-content: space-between;
            align-items: flex-start;
            margin-bottom: 10px;
        }

        #riwayat {
            border-top: 2px solid #333;
            margin-top: 10px;
            padding-top: 10px;
            list-style-type: none;
            padding-left: 0;
            width: 70%;
        }

        #riwayat li {
            border-bottom: 1px dotted #333;
            padding: 5px 0;
            font-size: 14px;
        }

        #digitalClock {
            text-align: right;
            font-weight: bold;
            font-size: 16px;
            width: 30%;
        }

        #plusOneBtn {
            width: 80px;
        }

        .center {
            text-align: center;
            margin-bottom: 10px;
        }

        .modal {
            display: none; 
            position: fixed; 
            z-index: 1; 
            left: 0;
            top: 0;
            width: 100%; 
            height: 100%; 
            overflow: auto; 
            background-color: rgba(0,0,0,0.4); 
            padding-top: 60px; 
        }

        .modal-content {
            background-color: #fefefe;
            margin: 5% auto; 
            padding: 20px; 
            border: 1px solid #888;
            width: 80%; 
            max-width: 600px; 
        }

        .close {
            color: #aaa;
            float: right;
            font-size: 28px;
            font-weight: bold;
        }

        .close:hover,
        .close:focus {
            color: black;
            text-decoration: none;
            cursor: pointer;
        }

        .modal img {
            width: 100%;
            height: auto;
        }
    </style>
</head>
<body>
    <div class="container">
        <input type="text" id="angkaInput" placeholder="MASUKKAN ANGKA">
        <div class="row">
            <button onclick="hitung()">HITUNG</button>
            <button onclick="resetRiwayat()">RESET</button>
        </div>
        <div id="notifikasi">NOTIFIKASI :</div>
        <div class="row">
            <button id="plusOneBtn">+1</button>
            <div id="totalCount">TOTAL = 0</div>
        </div>
        <div class="center">
            <button onclick="showModal()">PATUHI PERATURAN</button>
        </div>
        <div class="history-clock-container">
            <ul id="riwayat">
                <!-- Riwayat akan muncul di sini -->
            </ul>
            <div id="digitalClock">JAM DIGITAL</div>
        </div>
    </div>

    <!-- The Modal -->
    <div id="myModal" class="modal">
        <div class="modal-content">
            <span class="close" onclick="closeModal()">&times;</span>
            <img src="https://jomblolawas.wordpress.com/wp-content/uploads/2024/08/screenshot_20240823-230038_chrome6232631005007113438.jpg" alt="Descriptive Alt Text">
        </div>
    </div>

    <script>
        // Inisialisasi variabel
        let totalCount = 0;
        let riwayat = [];
        let hitunganKategori = {
            kecil: 0,
            besar: 0,
            ganjil: 0,
            genap: 0,
            acak: 0
        };
        let kategoriSebelumnya = {
            kecilBesar: "",
            ganjilGenap: "",
            acak: ""
        };
        let notifikasi = [];
        let lastTap = 0;

        function hitung() {
            const angka = document.getElementById("angkaInput").value.trim();

            if (angka === "") {
                alert("Masukkan angka terlebih dahulu.");
                return;
            }

            // Validasi bahwa input hanya angka
            if (!/^\d+$/.test(angka)) {
                alert("Input harus berupa angka.");
                return;
            }

            const digit = angka.split('').reduce((a, b) => parseInt(a) + parseInt(b), 0);
            const besar = digit >= 11;
            const ganjil = digit % 2 !== 0;
            const acak = riwayat.length > 0 && riwayat[0].angka !== angka;

            const kategori = {
                kecilBesar: besar ? "Besar" : "Kecil",
                ganjilGenap: ganjil ? "Ganjil" : "Genap",
                acak: acak ? "Acak" : "Urut"
            };

            // Update hitunganKategori
            if (kategori.kecilBesar === "Besar") {
                hitunganKategori.besar++;
                hitunganKategori.kecil = 0;
            } else {
                hitunganKategori.kecil++;
                hitunganKategori.besar = 0;
            }

            if (kategori.ganjilGenap === "Ganjil") {
                hitunganKategori.ganjil++;
                hitunganKategori.genap = 0;
            } else {
                hitunganKategori.genap++;
                hitunganKategori.ganjil = 0;
            }

            if (kategori.acak === "Acak") {
                hitunganKategori.acak++;
            } else {
                hitunganKategori.acak = 0;
            }

            // Reset notifikasi sebelum menambahkan yang baru
            notifikasi = [];

            // Logika notifikasi berdasarkan hitunganKategori
            if (hitunganKategori.kecil >= 5) {
                notifikasi.push(`● Kecil muncul ${hitunganKategori.kecil} kali, Entry BESAR`);
            }
            if (hitunganKategori.besar >= 5) {
                notifikasi.push(`● Besar muncul ${hitunganKategori.besar} kali, Entry KECIL`);
            }
            if (hitunganKategori.ganjil >= 5) {
                notifikasi.push(`● Ganjil muncul ${hitunganKategori.ganjil} kali, Entry GENAP`);
            }
            if (hitunganKategori.genap >= 5) {
                notifikasi.push(`● Genap muncul ${hitunganKategori.genap} kali, Entry GANJIL`);
            }
            if (hitunganKategori.acak >= 5) {
                notifikasi.push(`● Acak muncul ${hitunganKategori.acak} kali, Entry Follow akhir`);
            }

            // Tambahkan hasil ke riwayat
            const hasil = {
                angka: angka,
                kategori: `${kategori.kecilBesar}, ${kategori.ganjilGenap}`
            };
            riwayat.unshift(hasil);
            updateRiwayat();
            
            function hitung() {
    const angka = document.getElementById("angkaInput").value;

    if (angka === "") {
        alert("Masukkan angka terlebih dahulu.");
        return;
    }

    const digit = angka.split('').reduce((a, b) => parseInt(a) + parseInt(b), 0);
    const besar = digit >= 11;
    const ganjil = [3, 5, 7, 9, 11, 13, 15, 17].includes(digit);
    const kategori = ganjil ? "Ganjil" : "Genap";

    // Cek pola tren acak
    let trenAcak = false;
    if (riwayat.length >= 5) {
        const lastFive = riwayat.slice(0, 5).map(item => item.angka);
        trenAcak = lastFive.every((v, i, a) => v !== a[0]); // Jika semua angka berbeda
    }

    if (trenAcak) {
        hitunganKategori.acak++;
    } else {
        hitunganKategori.acak = 0;
    }

    // Cek tren urut untuk 6x berturut-turut dengan perubahan pada angka ke-7
    let trenUrutBerubah = false;
    if (riwayat.length >= 6) {
        const trenSebelumnya = riwayat.slice(0, 6).map(item => item.kategori);
        const trenBerubah = riwayat[0].kategori;
        trenUrutBerubah = trenSebelumnya.every(k => k === kategori) && trenBerubah !== kategori;
    }

    notifikasi = [];
    if (hitunganKategori.kecil >= 5) {
        notifikasi.push(` ● Kecil muncul ${hitunganKategori.kecil} kali, Entry BESAR`);
    }
    if (hitunganKategori.besar >= 5) {
        notifikasi.push(` ● Besar muncul ${hitunganKategori.besar} kali, Entry KECIL`);
    }
    if (hitunganKategori.ganjil >= 5) {
        notifikasi.push(` ● Ganjil muncul ${hitunganKategori.ganjil} kali, Entry GENAP`);
    }
    if (hitunganKategori.genap >= 5) {
        notifikasi.push(` ● Genap muncul ${hitunganKategori.genap} kali, Entry GANJIL`);
    }
    if (hitunganKategori.acak >= 5) {
        notifikasi.push(` ● Acak muncul ${hitunganKategori.acak} kali, Entry Follow akhir`);
    }
    if (trenUrutBerubah) {
        notifikasi.push(` ● ${kategori} 6x, 1${riwayat[0].kategori}, Entry ${kategori}`);
    }

    document.getElementById("notifikasi").innerHTML = notifikasi.join("<br>");

    const hasil = {
        angka: angka,
        kategori: kategori
    };
    riwayat.unshift(hasil);
    updateRiwayat();
}

            // Logika notifikasi tambahan untuk 6x tren sama diikuti 1x tren berbeda
            if (riwayat.length >= 7) {
                const trenSebelumnya = riwayat.slice(1, 7).map(item => item.kategori.split(', ')[1]); // Ambil 'Ganjil' atau 'Ganjil'
                const trenSaatIni = riwayat[0].kategori.split(', ')[1]; // 'Ganjil' atau 'Genap'

                const trenSama = trenSebelumnya.every(tren => tren === trenSebelumnya[0]);

                if (trenSama && trenSebelumnya[0] !== trenSaatIni) {
                    // Tren sebelumnya (6 kali) adalah trenSebelumnya[0], dan tren saat ini berbeda
                    notifikasi.push(`● ${trenSebelumnya[0]} 6x, 1 ${trenSaatIni}, Entry ${trenSebelumnya[0]}`);
                }
            }

            // Update tampilan notifikasi
            document.getElementById("notifikasi").innerHTML = notifikasi.length > 0 ? notifikasi.join("<br>") : "NOTIFIKASI :";
        }

        function updateRiwayat() {
            const riwayatContainer = document.getElementById("riwayat");
            riwayatContainer.innerHTML = riwayat.map((item, index) => 
                `<li>${item.angka} (Kategori: ${item.kategori})</li>`).join('');
        }

        function resetRiwayat() {
            riwayat = [];
            document.getElementById("riwayat").innerHTML = '';
            document.getElementById("notifikasi").innerHTML = 'NOTIFIKASI :';
            hitunganKategori = {
                kecil: 0,
                besar: 0,
                ganjil: 0,
                genap: 0,
                acak: 0
            };
            kategoriSebelumnya = {
                kecilBesar: "",
                ganjilGenap: "",
                acak: ""
            };
            notifikasi = [];
        }

        document.getElementById('plusOneBtn').addEventListener('click', function(event) {
            const currentTime = new Date().getTime();
            const tapInterval = currentTime - lastTap;

            if (tapInterval < 300 && tapInterval > 0) {
                // Double tap detected, reset totalCount
                totalCount = 1;
            } else {
                // Single tap detected, increment totalCount
                totalCount++;
            }

            lastTap = currentTime;

            // Update the display
            document.getElementById('totalCount').innerText = `TOTAL = ${totalCount}`;
        });

        function showModal() {
            document.getElementById("myModal").style.display = "block";
        }

        function closeModal() {
            document.getElementById("myModal").style.display = "none";
        }

        // Fungsi untuk memperbarui jam digital setiap detik
        function updateDigitalClock() {
            const clock = document.getElementById("digitalClock");
            const now = new Date();
            const hours = String(now.getHours()).padStart(2, "0");
            const minutes = String(now.getMinutes()).padStart(2, "0");
            const seconds = String(now.getSeconds()).padStart(2, "0");
            clock.innerText = `${hours}:${minutes}:${seconds}`;
        }

        // Memperbarui jam setiap detik
        setInterval(updateDigitalClock, 1000);

        // Inisialisasi jam saat halaman dimuat
        updateDigitalClock();
    </script>
</body>
</html
