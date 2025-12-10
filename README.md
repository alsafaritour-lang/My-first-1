<!DOCTYPE html>
<html lang="tr">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Karadeniz Yaylalar Turu</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <script src="https://cdn.jsdelivr.net/npm/alpinejs@3.x.x/dist/cdn.min.js" defer></script>
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/flatpickr/dist/flatpickr.min.css">
  <script src="https://cdn.jsdelivr.net/npm/flatpickr"></script>
  <script src="https://cdn.jsdelivr.net/npm/flatpickr/dist/l10n/tr.js"></script>
</head>
<body class="bg-gray-50 min-h-screen" x-data="{
  lang: 'tr',
  currency: 'TRY',
  persons: 1,
  startDate: null,
  endDate: null,
  name: '',
  phone: '',
  notes: '',
  toast: null,
  showToast(msg){ this.toast = msg; setTimeout(()=>this.toast=null, 3500); },

  // GÜNCEL KURLAR (10 Aralık 2025)
  rates: { EUR: 49.69, USD:42.61 },

  tour: {
    title: { tr:'Karadeniz Yaylalar ve Gölleri Turu', en:'Black Sea Highlands & Lakes Tour' },
    shortDescription: { tr:'Pokut, Sal, Ayder, Borçka Karagöl ve daha fazlası… 6 gün doğa ve huzur.', en:'Pokut, Sal, Ayder, Borçka Karagöl and more… 6 days of nature and peace.' },
    priceTL: 8500,
    capacity: 16,
    duration: '6 Gün 5 Gece',
    departure: 'Trabzon Havalimanı',
    image: 'https://images.unsplash.com/photo-1506905925346-21bda4d32df4?w=1600&auto=format&fit=crop'
  },

  t(tr,en){ return this.lang==='tr'?tr:en; },
  formatPrice(tl){
    const sym = {TRY:'₺', EUR:'€', USD:'$'};
    if(this.currency==='TRY') return new Intl.NumberFormat('tr-TR').format(Math.round(tl)) + ' ₺';
    const val = Math.round(tl / this.rates[this.currency]);
    return new Intl.NumberFormat('en-US').format(val) + ' ' + sym[this.currency];
  }
}">
<div class="max-w-7xl mx-auto p-4 md:p-8">

  <!-- Başlık + Dil & Para Birimi -->
  <div class="mb-8 flex flex-col md:flex-row justify-between items-start md:items-center gap-4">
    <div class="flex items-center gap-4">
      <a href="#" class="flex items-center gap-2 text-sky-600 text-sm"><svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7"/></svg> Turlara Dön</a>
      <h1 class="text-2xl md:text-3xl font-bold" x-text="tour.title[lang]"></h1>
    </div>
    <div class="flex items-center gap-4">
      <div class="text-right">
        <div class="text-lg font-semibold" x-text="formatPrice(tour.priceTL)+' / '+t('kişi','person')"></div>
      </div>
      <div class="flex gap-2">
        <button @click="lang='tr'" :class="lang==='tr'?'bg-sky-600 text-white':'bg-gray-200'" class="px-3 py-1 rounded text-sm">TR</button>
        <button @click="lang='en'" :class="lang==='en'?'bg-sky-600 text-white':'bg-gray-200'" class="px-3 py-1 rounded text-sm">EN</button>
        <select x-model="currency" class="border rounded px-3 py-1 text-sm">
          <option value="TRY">₺ TRY</option>
          <option value="EUR">€ EUR</option>
          <option value="USD">$ USD</option>
        </select>
      </div>
    </div>
  </div>

  <div class="grid grid-cols-1 lg:grid-cols-3 gap-8">

    <!-- Sol İçerik -->
    <main class="lg:col-span-2 space-y-8">
      <img :src="tour.image" class="w-full h-96 object-cover rounded-xl shadow-lg">
      <div class="bg-white p-6 rounded-xl shadow">
        <h2 class="text-xl font-bold mb-3" x-text="t('Kısa Açıklama','Overview')"></h2>
        <p x-text="tour.shortDescription[lang]" class="text-zinc-700"></p>
      </div>
    </main>

    <!-- Sağ Rezervasyon (Sticky) -->
    <aside class="lg:col-span-1">
      <div class="sticky top-6 bg-white rounded-xl shadow-xl p-6">
        <h3 class="text-2xl font-bold mb-4" x-text="t('Hemen Rezervasyon','Instant Booking')"></h3>

        <form @submit.prevent="
          if(!name) return alert(t('Ad soyad gerekli','Name required'));
          if(!phone.match(/^05[0-9]{9}$/)) return alert(t('Telefon 05 ile başlamalı, 11 hane','Phone must be 05xxxxxxxxx'));
          if(!startDate || !endDate) return alert(t('Tarih seçin','Select dates'));

          const msg = [
            t('*Rezervasyon Talebi*','*Reservation Request*'),
            `Tur: ${tour.title[lang]}`,
            `Tarih: \( {flatpickr.formatDate(startDate,'d/m/Y')} - \){flatpickr.formatDate(endDate,'d/m/Y')}`,
            `Kişi: ${persons}`,
            `İsim: ${name}`,
            `Telefon: ${phone}`,
            notes ? `Not: ${notes}` : '',
            `Toplam: *${formatPrice(tour.priceTL * persons)}*`,
            '',
            t('İyi günler!','Have a great day!')
          ].join('%0A');

          window.open('https://wa.me/?text='+msg,'_blank');
          showToast(t('WhatsApp açılıyor...','Opening WhatsApp...'));
        ">

          <div class="space-y-4">
            <div>
              <label class="block font-medium mb-1" x-text="t('Kişi Sayısı','Persons')"></label>
              <select x-model="persons" class="w-full border rounded-lg px-4 py-2">
                <template x-for="n in tour.capacity"><option :value="n" x-text="n"></option></template>
              </select>
            </div>

            <div class="grid grid-cols-2 gap-4">
              <div>
                <label class="block font-medium mb-1" x-text="t('Başlangıç','Start')"></label>
                <input x-ref="start" class="w-full border rounded-lg px-4 py-2" placeholder="GG/AA/YYYY">
              </div>
              <div>
                <label class="block font-medium mb-1" x-text="t('Bitiş','End')"></label>
                <input x-ref="end" class="w-full border rounded-lg px-4 py-2" placeholder="GG/AA/YYYY">
              </div>
            </div>

            <div>
              <label class="block font-medium mb-1" x-text="t('Ad Soyad','Full Name')"></label>
              <input x-model="name" required class="w-full border rounded-lg px-4 py-2">
            </div>

            <div>
              <label class="block font-medium mb-1" x-text="t('Telefon','Phone')"></label>
              <input x-model="phone" type="tel" required class="w-full border rounded-lg px-4 py-2" placeholder:text-zinc-400" placeholder="05XXXXXXXXX">
            </div>

            <div>
              <label class="block font-medium mb-1" x-text="t('Notlar (opsiyonel)','Notes (optional)')"></label>
              <textarea x-model="notes" rows="3" class="w-full border rounded-lg px-4 py-2"></textarea>
            </div>

            <div class="bg-gradient-to-r from-zinc-50 to-zinc-100 p-5 rounded-xl text-center">
              <div class="text-sm text-zinc-600" x-text="t('Toplam Tutar','Total')"></div>
              <div class="text-3xl font-bold text-green-600" x-text="formatPrice(tour.priceTL * persons)"></div>
            </div>

            <button type="submit" class="w-full bg-gradient-to-r from-green-500 to-green-600 hover:from-green-600 hover:to-green-700 text-white font-bold py-4 rounded-xl shadow-lg transition">
              WhatsApp ile Rezervasyon Yap
            </button>
          </div>
        </form>
      </div>
    </aside>
  </div>
</div>

<!-- Flatpickr başlat
<script>
document.addEventListener('alpine:init', () => {
  Alpine.store('fp', {
    start: flatpickr('[x-ref=start]', {locale: Alpine.$data(document.body).lang==='tr' ? 'tr' : 'en', dateFormat: 'd.m.Y', onChange: (s)=> Alpine.$data(document.body).startDate = s[0]}),
    end:   flatpickr('[x-ref=end]',   {locale: Alpine.$data(document.body).lang==='tr' ? 'tr' : 'en', dateFormat: 'd.m.Y', onChange: (s)=> Alpine.$data(document.body).endDate = s[0]})
  });
});
</script>

<!-- Toast -->
<div x-show="toast" x-transition class="fixed bottom-8 right-8 bg-green-600 text-white px-6 py-3 rounded-xl shadow-2xl" x-text="toast"></div>

</body>
</html># My-first-1
My first
