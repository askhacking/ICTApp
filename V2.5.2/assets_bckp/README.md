# ICTApp Brand & UI Asset Pack

Assets disusun dari referensi desain yang diberikan untuk ICTApp — ICT Club Management System, SMKN 2 Cikarang Barat.

## Isi paket
- `logos/` — logo utama, lockup ICT Club, contoh varian latar, dan logo ICT Club asli.
- `app-icons/` — contoh ikon aplikasi multi-platform dalam ukuran 512×512.
- `icons/` — ikon SVG outline untuk modul utama.
- `design-system/tokens.css` — warna, radius, shadow, dan font token CSS.

## Palet warna
| Token | Hex | Penggunaan |
|---|---|---|
| Navy | `#293F59` | Sidebar, tombol utama |
| Ink | `#132234` | Teks utama |
| Band | `#081F44` | Navy gelap |
| Blue | `#4D9CBA` | Aksen sekunder |
| Sky | `#9BC9E3` | Latar lembut |
| Yellow | `#F5BA42` | Highlight |
| Orange | `#ED8B34` | CTA dan aksen |
| Grey | `#5F7088` | Teks sekunder |

## Pemakaian
Salin folder `icons/` dan `design-system/` ke proyek web. Contoh:
```html
<img src="/assets/icons/calendar.svg" alt="" width="24" height="24">
```
```css
@import url("/assets/design-system/tokens.css");
body { font-family: var(--ict-font); background: var(--ict-bg); color: var(--ict-ink); }
```

## Catatan kualitas
Logo dan ikon aplikasi berupa PNG hasil crop dari papan brand referensi, sehingga cocok sebagai aset visual awal/prototipe. Untuk kebutuhan produksi cetak atau scaling tanpa batas, gunakan master logo vektor (SVG/AI) dari pembuat logo. Ikon modul adalah SVG vektor dan dapat diskalakan.
