# DraftlyCV - Resume Builder

Resume Builder yang berjalan 100% dalam browser tanpa backend atau database.

## Ciri-ciri

✅ **Auto-Save** - Data resume disimpan secara automatik ke localStorage
✅ **Export/Import JSON** - Download dan upload resume draft dalam format JSON
✅ **Live Preview** - Preview resume yang update secara real-time
✅ **Download PDF** - Export resume siap dalam format PDF
✅ **Mobile-Friendly** - Responsive design untuk semua device
✅ **Modern Design** - Template profesional mengikut standard 2025

## Cara Guna

1. Buka fail `index.html` dalam browser (Chrome, Firefox, Edge, Safari)
2. Isi borang resume dengan maklumat anda
3. Preview akan update secara automatik di sebelah kanan
4. Data akan auto-save ke browser anda

## Fungsi Utama

### 📥 Export Resume Draft
- Klik butang "Export Resume Draft" untuk download fail JSON
- Fail JSON mengandungi semua data resume anda
- Boleh simpan sebagai backup

### 📤 Upload Resume Draft
- Klik butang "Upload Resume Draft" untuk upload fail JSON
- Boleh sambung editing dari device lain
- Import data dari backup sebelumnya

### 📄 Download PDF
- Klik butang "Download PDF" untuk download resume siap
- Format professional, ready to send

### 💾 Auto-Save
- Semua perubahan disimpan automatik ke localStorage browser
- Tutup browser dan buka balik, data masih ada
- Tiada keperluan untuk save manual

## Format JSON

```json
{
  "name": "Nama Penuh",
  "email": "email@example.com",
  "phone": "+60 12-345 6789",
  "summary": "Professional summary...",
  "experience": [
    {
      "title": "Job Title",
      "company": "Company Name",
      "startDate": "Jan 2020",
      "endDate": "Present",
      "description": "Job description..."
    }
  ],
  "education": [
    {
      "degree": "Degree Name",
      "institution": "University Name",
      "startDate": "2018",
      "endDate": "2022",
      "description": "Additional info..."
    }
  ],
  "skills": ["Skill 1", "Skill 2", "Skill 3"]
}
```

## Teknologi

- HTML5
- CSS3 (Modern Design dengan Gradients & Animations)
- Vanilla JavaScript (No Framework)
- html2pdf.js (untuk PDF generation)

## Browser Support

- Chrome (Recommended)
- Firefox
- Safari
- Edge

## Tips

1. Isi semua bahagian untuk resume yang lengkap
2. Gunakan "Export Resume Draft" untuk backup secara berkala
3. Professional summary sebaiknya 2-3 ayat
4. Gunakan bullet points untuk experience description
5. Download PDF bila sudah siap untuk hantar

---

**DraftlyCV** - Build Your Professional Resume in Minutes
