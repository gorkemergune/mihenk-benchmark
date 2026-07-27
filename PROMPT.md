# Claude Code Prompt — MIHENK Soru Üretimi

Bu metni olduğu gibi Claude Code oturumuna (proje klasöründe `claude` komutuyla açtığın terminale) yapıştırabilirsin. Konuştuğumuz her karar (isim, telif/scraping kısıtı, şeffaflık, repo yapısı) bu prompt'a işlendi.

---

## PROMPT

Sen **MIHENK** (Multilingual Intelligence, High-level Evaluation and Neural Knowledge Benchmark) projesi için soru üreten bir ajansın. Görevin, "MIHENK_Tasarim_Dokumani.docx" dosyasında tanımlanan şemaya uygun, **tamamen özgün** benchmark soruları üretmek ve bunları belirtilen dosya yapısına yazmak.

### Kapsam

- **Diller:** Türkçe (`tr`) ve İngilizce (`en`) — her soru iki dilde de üretilecek, ama birebir çeviri olmayacak; her dilin kendi doğal yapısına (deyim, sözdizimi, kültürel bağlam) göre ayrı ayrı kurgulanacak.
- **Formatlar:** Çoktan seçmeli (`multiple_choice`, 4-5 şık) ve kısa cevaplı (`short_answer`, cevap en fazla 7 kelime).
- **Disiplinler (20):** Matematik, Mantık, Programlama, Debugging, Türkçe Dil Bilgisi, İngilizce Dil Bilgisi, Tarih, Coğrafya, Fizik, Kimya, Biyoloji, Hukuk, Ekonomi, Felsefe, Günlük Muhakeme, Sayısal Akıl Yürütme, Tablo ve Veri Yorumu, Grafik Analizi, Çok Adımlı Muhakeme, Bilimsel Makale Anlama.
- **Zorluk seviyeleri:** L1 (temel), L2 (orta), L3 (ileri), L4 (uzman) — dört seviyeye eşit ağırlık ver.

### Üslup ve Zorluk Referansı — ÖSYM Tarzı (SINIR: KOPYALAMA YOK)

Soru kurgusunda **ÖSYM'nin (YKS, KPSS, ALES, DGS) soru yazım tekniklerini ve zorluk kalibrasyonunu** referans al: paragraf içine gömülü sayısal/mantıksal ilişkiler, ince ayrımlı çeldiriciler, "bilgi değil çıkarım" gerektiren kurgular, tablo/grafik yorumlama, sözel mantık zincirleri.

Kesinlikle **yapma**:

- Gerçek ÖSYM sorularını (veya başka telifli sınav bankalarını — dershane yayınları, SAT, GRE vb.) internetten çekme, scraping yapma, kopyalama veya hafif değiştirerek (paraphrase) yeniden kullanma.
- Herhangi bir soruyu mevcut bir sınavın birebir veya yakın türevi olarak üretme — her soru sıfırdan yeni senaryo, yeni sayılar, yeni bağlamla kurulacak.
- Soruların yapay zeka tarafından üretildiğini gizleme veya insan yazımı gibi göstermeye çalışma. `source` alanına her zaman `"orijinal-AI-üretim"` yaz — şeffaflık, benchmark'ın kendi güvenilirliği için de gerekli.

Özetle: **stil ve zorluk seviyesini ÖSYM'den ilham al, içeriği sıfırdan üret, kaynağı gizleme.**

### Soru Tasarım İlkeleri

- Gereksiz/dikkat dağıtıcı bilgi ekle — model bunu elemek zorunda kalsın.
- Çeldiriciler rastgele değil, yaygın hata örüntülerini (hesap hatası, aceleci genelleme, yanlış hatırlama) yansıtsın.
- L3-L4 sorularında çok adımlı çıkarım gereksin, tek adımda tahmin yetmesin.
- Her sorunun **tek** doğru cevabı olsun, belirsizlik olmasın.
- TR-EN çiftlerinde çeviri kaynaklı ipucu sızıntısı olmasın (örn. kelime sayısı cevabı ele vermesin).

### Çıktı Formatı (JSON şeması)

```json
{
  "id": "MIHENK-MATH-TR-L3-0001",
  "language": "tr",
  "discipline": "Matematik",
  "format": "multiple_choice",
  "difficulty": "L3",
  "question": "...",
  "choices": { "A": "...", "B": "...", "C": "...", "D": "..." },
  "answer": "C",
  "answer_short": null,
  "explanation": "Çözümün kısa gerekçesi",
  "tags": ["çok-adımlı", "oran-orantı"],
  "source": "orijinal-AI-üretim",
  "version_added": "1.0"
}
```

`id` alanı `MIHENK-{DISIPLIN_KISALTMA}-{DIL}-{ZORLUK}-{SIRA_NO}` formatında olsun (örn. `MIHENK-LOGIC-EN-L2-0014`).

Kısa cevaplı sorularda `choices` ve `answer` alanlarını `null` bırak; `answer_short` alanına kanonik cevabı, `answer_aliases` dizisine varsa kabul edilebilir eş anlamlı varyasyonları ekle.

### Dosya ve Repo Yapısı

```
/data/tr/{disiplin}/multiple_choice.jsonl
/data/tr/{disiplin}/short_answer.jsonl
/data/en/{disiplin}/multiple_choice.jsonl
/data/en/{disiplin}/short_answer.jsonl
/schema/question_schema.json
/scoring/
/versions/CHANGELOG.md
```

Bu yapı hem GitHub reposuna (`mihenk`) hem Hugging Face dataset reposuna (`mihenk-bench`) taşınacak şekilde tasarlanmalı. Hugging Face'e yüklerken tüm soru bankası değil, disiplin başına küçük bir **public dev/sample split** (~%10-15) ayrılacak; kalan çoğunluk **private holdout** olarak saklanacak (kirlenmeyi önlemek için). Bu ayrımı kolaylaştırmak için her soruya `split: "public"` veya `split: "private"` alanı ekle.

### Çalışma Adımları

1. Disiplin listesinden birini seç; o disiplin için sırayla Türkçe MC → Türkçe short_answer → İngilizce MC → İngilizce short_answer üret, zorluk seviyelerine eşit dağıt.
2. Her soruyu ürettikten sonra JSON şemasına karşı doğrula (zorunlu alanlar eksik mi, tek doğru cevap var mı, 7 kelime sınırı aşılmış mı).
3. Aynı disiplin/dil/format içinde tekrar eden senaryo veya sayı kalıbı olmadığını kontrol et.
4. `split` alanını ata (~%10-15 public, gerisi private).
5. İlgili `.jsonl` dosyasına satır olarak ekle (yoksa oluştur).
6. Bir sonraki disipline geç.

### İlk Çalıştırma Kapsamı (Faz 1 — Pilot Set)

Her disiplin için: 2 dil × 2 format × 10 soru = disiplin başına 40 soru, toplam **800 soru**. Zorluk dağılımı disiplin başına yaklaşık L1:2-3, L2:3-4, L3:2-3, L4:1-2.

Başlamadan önce bir disiplin için 2 örnek soru (biri MC, biri short_answer, TR) üret ve onay için göster; onay alınca kalan tüm disiplinlere aynı kalite standardıyla devam et.

---

## Notlar

- Bu prompt, ÖSYM'nin gerçek sorularının kullanılmasını veya taklit edilerek yakın türevlerinin üretilmesini **istemez** — telif riski nedeniyle. Sadece zorluk/stil referansı olarak kullanılmasını ister.
- `source` alanı her zaman şeffaf tutulur.
- Yeni bir model değerlendirmesi eklendiğinde (leaderboard güncellemesi), bu soru üretim akışına dokunulmaz — sonuçlar ayrı bir `mihenk-leaderboard` reposunda/Space'inde tutulur, böylece benchmark soruları sabit kalır ve karşılaştırmalar tutarlı olur.
