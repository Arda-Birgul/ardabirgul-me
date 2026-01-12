<script setup>
import { ref, computed, watch, onMounted } from 'vue'

const aktifSekme = ref('hakkimda')
const mobileMenuOpen = ref(false)
const dropdownOpen = ref(false)

function sekmeDegistir(sekmeAdi) {
  aktifSekme.value = sekmeAdi
  mobileMenuOpen.value = false
  dropdownOpen.value = false
}

onMounted(() => {
  document.addEventListener('click', (e) => {
    const dropdown = document.querySelector('.nav-dropdown')
    if (dropdown && !dropdown.contains(e.target)) {
      dropdownOpen.value = false
    }
  })
})

const filamentFiyati = ref(450)
const harcananGram = ref(100)
const enerjiFiyati = ref(2.5)
const baskiSuresi = ref(5)
const karOrani = ref(30)

const fireOrani = 10
const otvOrani = 20
const gucTuketimi = 350

const filamentMaliyeti = computed(() => {
  return (harcananGram.value / 1000) * filamentFiyati.value * (1 + fireOrani / 100)
})

const enerjiMaliyeti = computed(() => {
  return (gucTuketimi / 1000) * baskiSuresi.value * enerjiFiyati.value
})

const hamMaliyet = computed(() => {
  return filamentMaliyeti.value + enerjiMaliyeti.value
})

const maliyet = computed(() => {
  return hamMaliyet.value * (1 + otvOrani / 100)
})

const satisFiyati = computed(() => {
  return maliyet.value * (1 + karOrani.value / 100)
})

const kar = computed(() => {
  return satisFiyati.value - maliyet.value
})

const animatedPrice = ref(0)
const isPulsing = ref(false)
let animationFrame = null

function animatePrice(targetValue) {
  if (animationFrame) {
    cancelAnimationFrame(animationFrame)
  }

  const startValue = animatedPrice.value
  const difference = targetValue - startValue
  const duration = 600
  const startTime = performance.now()

  function step(currentTime) {
    const elapsed = currentTime - startTime
    const progress = Math.min(elapsed / duration, 1)

    const easeProgress = progress === 1 ? 1 : 1 - Math.pow(2, -10 * progress)

    animatedPrice.value = startValue + difference * easeProgress

    if (progress < 1) {
      animationFrame = requestAnimationFrame(step)
    } else {
      animatedPrice.value = targetValue
      isPulsing.value = true
      setTimeout(() => {
        isPulsing.value = false
      }, 300)
    }
  }

  animationFrame = requestAnimationFrame(step)
}

watch(
  [filamentFiyati, harcananGram, enerjiFiyati, baskiSuresi, karOrani],
  () => {
    animatePrice(satisFiyati.value)
  },
  { immediate: true },
)

// Yardımcı Fonksiyonlar
function formatFiyat(value) {
  if (isNaN(value) || !isFinite(value)) return '0.00'
  return value.toFixed(2)
}
</script>

<template>
  <div class="app-wrapper" :class="aktifSekme">
    <nav class="navbar">
      <div class="navbar-container">
        <a href="#" class="navbar-brand" @click.prevent="sekmeDegistir('hakkimda')">
          <span class="brand-bold">Arda</span><span class="brand-light">.dev</span>
        </a>

        <div class="navbar-menu">
          <ul class="navbar-nav">
            <li class="nav-item">
              <a href="#" class="nav-link" @click.prevent="sekmeDegistir('hakkimda')"> Hakkımda </a>
            </li>

            <li class="nav-item nav-dropdown" @click.stop>
              <a
                href="#"
                class="nav-link"
                :class="{ active: aktifSekme === 'printcost' || aktifSekme === 'baremetal' }"
                @click.prevent="dropdownOpen = !dropdownOpen"
              >
                Projelerim
                <svg
                  class="dropdown-arrow"
                  :class="{ rotated: dropdownOpen }"
                  width="10"
                  height="10"
                  viewBox="0 0 10 10"
                >
                  <path
                    d="M2 3L5 6L8 3"
                    stroke="currentColor"
                    stroke-width="1.5"
                    fill="none"
                    stroke-linecap="round"
                  />
                </svg>
              </a>
              <ul class="dropdown-menu" :class="{ show: dropdownOpen }">
                <li>
                  <a
                    href="#"
                    class="dropdown-item"
                    :class="{ active: aktifSekme === 'printcost' }"
                    @click.prevent="sekmeDegistir('printcost')"
                  >
                    <svg
                      width="16"
                      height="16"
                      viewBox="0 0 24 24"
                      fill="none"
                      stroke="currentColor"
                      stroke-width="2"
                    >
                      <path
                        d="M6 9V2h12v7M6 18H4a2 2 0 01-2-2v-5a2 2 0 012-2h16a2 2 0 012 2v5a2 2 0 01-2 2h-2"
                      />
                      <rect x="6" y="14" width="12" height="8" />
                    </svg>
                    PrintCost <span class="badge-app">App</span>
                  </a>
                </li>
                <li>
                  <a
                    href="#"
                    class="dropdown-item"
                    :class="{ active: aktifSekme === 'baremetal' }"
                    @click.prevent="sekmeDegistir('baremetal')"
                  >
                    <svg
                      width="16"
                      height="16"
                      viewBox="0 0 24 24"
                      fill="none"
                      stroke="currentColor"
                      stroke-width="2"
                    >
                      <path d="M13 2L3 14h9l-1 8 10-12h-9l1-8z" />
                    </svg>
                    Bare Metal <span class="badge-iot">IoT</span>
                  </a>
                </li>
              </ul>
            </li>
          </ul>
        </div>
      </div>
    </nav>

    <!-- Ana İçerik -->
    <main class="main-content">
      <!-- Sayfa: Hakkımda -->
      <section v-if="aktifSekme === 'hakkimda'" class="page page-hakkimda">
        <div class="hakkimda-container">
          <div class="hakkimda-layout">
            <!-- Sol Kolon: Giriş -->
            <div class="intro-col">
              <div class="intro-content">
                <p class="intro-greeting">Merhaba, ben</p>
                <h1 class="intro-title">Arda <span class="highlight">Birgül</span></h1>
                <p class="intro-role">Yazılım Geliştirici & Gömülü Sistemler Tutkunu</p>
                <p class="intro-subtitle">
                  Bir mobil uygulamanın kullanıcı deneyimini tasarlarken, aynı zamanda o uygulamanın
                  kontrol edeceği elektronik devreyi ve dış kasasını da üretebiliyorum.
                </p>
                <p class="intro-subtitle">
                  <strong>'Full-Stack'</strong> kavramını sadece Frontend-Backend olarak değil,
                  <em>Donanım-Yazılım</em> olarak benimsiyorum.
                </p>
                <p class="intro-staj">
                  <strong>3 aylık MYO stajı arıyorum</strong> — Gömülü sistemler veya IoT alanında
                  öğrenmeye ve katkı sağlamaya hazırım.
                </p>
                <div class="intro-cta">
                  <a
                    href="https://github.com/Arda-Birgul"
                    target="_blank"
                    class="btn-primary-custom"
                  >
                    <svg width="18" height="18" viewBox="0 0 24 24" fill="currentColor">
                      <path
                        d="M12 0C5.37 0 0 5.37 0 12c0 5.31 3.435 9.795 8.205 11.385.6.105.825-.255.825-.57 0-.285-.015-1.23-.015-2.235-3.015.555-3.795-.735-4.035-1.41-.135-.345-.72-1.41-1.23-1.695-.42-.225-1.02-.78-.015-.795.945-.015 1.62.87 1.845 1.23 1.08 1.815 2.805 1.305 3.495.99.105-.78.42-1.305.765-1.605-2.67-.3-5.46-1.335-5.46-5.925 0-1.305.465-2.385 1.23-3.225-.12-.3-.54-1.53.12-3.18 0 0 1.005-.315 3.3 1.23.96-.27 1.98-.405 3-.405s2.04.135 3 .405c2.295-1.56 3.3-1.23 3.3-1.23.66 1.65.24 2.88.12 3.18.765.84 1.23 1.905 1.23 3.225 0 4.605-2.805 5.625-5.475 5.925.435.375.81 1.095.81 2.22 0 1.605-.015 2.895-.015 3.3 0 .315.225.69.825.57A12.02 12.02 0 0024 12c0-6.63-5.37-12-12-12z"
                      />
                    </svg>
                    GitHub
                  </a>
                  <a href="mailto:arda.birgul-work@proton.me" class="btn-secondary-custom">
                    <svg
                      width="18"
                      height="18"
                      viewBox="0 0 24 24"
                      fill="none"
                      stroke="currentColor"
                      stroke-width="2"
                    >
                      <path
                        d="M4 4h16c1.1 0 2 .9 2 2v12c0 1.1-.9 2-2 2H4c-1.1 0-2-.9-2-2V6c0-1.1.9-2 2-2z"
                      />
                      <polyline points="22,6 12,13 2,6" />
                    </svg>
                    İletişim
                  </a>
                </div>
              </div>
            </div>

            <!-- Sağ Kolon: Bento Grid -->
            <div class="bento-col">
              <div class="bento-grid">
                <!-- Sol Sütun -->

                <!-- Kart: Vizyon & Felsefe -->
                <div class="bento-card bento-vision">
                  <div class="card-header">
                    <svg
                      width="16"
                      height="16"
                      viewBox="0 0 24 24"
                      fill="none"
                      stroke="currentColor"
                      stroke-width="2"
                    >
                      <path d="M12 2L2 7l10 5 10-5-10-5z" />
                      <path d="M2 17l10 5 10-5" />
                      <path d="M2 12l10 5 10-5" />
                    </svg>
                    <h4>Vizyon & Felsefe</h4>
                  </div>
                  <div class="vision-content">
                    <p class="vision-quote">"Fikir → Prototip → Ürün"</p>
                    <p class="vision-text">
                      Sadece kod yazmıyorum; elektronik devre tasarlıyor, PCB üretiyorum, yazılım
                      geliştiriyorum ve fiziksel ürüne dönüştürüyorum.
                    </p>
                    <div class="vision-tags">
                      <span class="vision-tag">Gömülü</span>
                      <span class="vision-tag">IoT</span>
                      <span class="vision-tag">Otomasyon</span>
                    </div>
                  </div>
                </div>

                <!-- Kart: Eğitim -->
                <div class="bento-card bento-education">
                  <div class="card-header">
                    <svg
                      width="16"
                      height="16"
                      viewBox="0 0 24 24"
                      fill="none"
                      stroke="currentColor"
                      stroke-width="2"
                    >
                      <path d="M22 10v6M2 10l10-5 10 5-10 5z" />
                      <path d="M6 12v5c3 3 9 3 12 0v-5" />
                    </svg>
                    <h4>Eğitim</h4>
                  </div>
                  <div class="edu-content">
                    <span class="edu-degree">Bilgisayar Programcılığı</span>
                    <span class="edu-school">Ön Lisans • 2024 - 2026</span>
                    <span class="edu-university">Pamukkale Üniversitesi</span>
                  </div>
                </div>

                <!-- Kart: Araçlar & Üretim -->
                <div class="bento-card bento-tools">
                  <div class="card-header">
                    <svg
                      width="16"
                      height="16"
                      viewBox="0 0 24 24"
                      fill="none"
                      stroke="currentColor"
                      stroke-width="2"
                    >
                      <path
                        d="M14.7 6.3a1 1 0 0 0 0 1.4l1.6 1.6a1 1 0 0 0 1.4 0l3.77-3.77a6 6 0 0 1-7.94 7.94l-6.91 6.91a2.12 2.12 0 0 1-3-3l6.91-6.91a6 6 0 0 1 7.94-7.94l-3.76 3.76z"
                      />
                    </svg>
                    <h4>Üretim & Araçlar</h4>
                  </div>
                  <div class="tools-showcase">
                    <div class="tool-item">
                      <svg
                        width="20"
                        height="20"
                        viewBox="0 0 24 24"
                        fill="none"
                        stroke="currentColor"
                        stroke-width="2"
                      >
                        <rect x="2" y="6" width="20" height="12" rx="2" />
                        <path d="M12 12h.01" />
                        <path d="M17 12h.01" />
                        <path d="M7 12h.01" />
                      </svg>
                      <span>Bambu Lab P1S</span>
                    </div>
                    <div class="tool-item">
                      <svg
                        width="20"
                        height="20"
                        viewBox="0 0 24 24"
                        fill="none"
                        stroke="currentColor"
                        stroke-width="2"
                      >
                        <path d="M12 19l7-7 3 3-7 7-3-3z" />
                        <path d="M18 13l-1.5-7.5L2 2l3.5 14.5L13 18l5-5z" />
                        <path d="M2 2l7.586 7.586" />
                        <circle cx="11" cy="11" r="2" />
                      </svg>
                      <span>AutoCAD</span>
                    </div>
                    <div class="tool-item">
                      <svg
                        width="20"
                        height="20"
                        viewBox="0 0 24 24"
                        fill="none"
                        stroke="currentColor"
                        stroke-width="2"
                      >
                        <rect x="4" y="4" width="16" height="16" rx="2" />
                        <path d="M4 9h16" />
                        <path d="M9 4v16" />
                      </svg>
                      <span>PCB Tasarımı</span>
                    </div>
                    <div class="tool-item">
                      <svg
                        width="20"
                        height="20"
                        viewBox="0 0 24 24"
                        fill="none"
                        stroke="currentColor"
                        stroke-width="2"
                      >
                        <circle cx="12" cy="12" r="3" />
                        <path
                          d="M12 1v6M12 17v6M4.22 4.22l4.24 4.24M15.54 15.54l4.24 4.24M1 12h6M17 12h6M4.22 19.78l4.24-4.24M15.54 8.46l4.24-4.24"
                        />
                      </svg>
                      <span>Prototipleme</span>
                    </div>
                  </div>
                </div>

                <!-- Sağ Sütun: Tech Stack -->
                <div class="bento-card bento-techstack">
                  <div class="card-header">
                    <svg
                      width="16"
                      height="16"
                      viewBox="0 0 24 24"
                      fill="none"
                      stroke="currentColor"
                      stroke-width="2"
                    >
                      <polyline points="16 18 22 12 16 6" />
                      <polyline points="8 6 2 12 8 18" />
                    </svg>
                    <h4>Tech Stack</h4>
                  </div>

                  <!-- Bölüm: Mobil -->
                  <div class="stack-section">
                    <span class="stack-label">Mobile</span>
                    <div class="stack-items">
                      <div class="stack-item">
                        <span class="stack-icon" style="background: #02569b">D</span>
                        <span class="stack-name">Dart</span>
                      </div>
                      <div class="stack-item">
                        <span class="stack-icon" style="background: #54c5f8">F</span>
                        <span class="stack-name">Flutter</span>
                      </div>
                    </div>
                  </div>

                  <!-- Bölüm: Gömülü -->
                  <div class="stack-section">
                    <span class="stack-label">Gömülü</span>
                    <div class="stack-items">
                      <div class="stack-item">
                        <span class="stack-icon" style="background: #a8b9cc">C</span>
                        <span class="stack-name">C (AVR/Arduino)</span>
                      </div>
                      <div class="stack-item">
                        <span class="stack-icon" style="background: #e7352c">E</span>
                        <span class="stack-name">ESP32</span>
                      </div>
                      <div class="stack-item">
                        <span class="stack-icon" style="background: #00979d">S</span>
                        <span class="stack-name">Sensör Entegrasyonu</span>
                      </div>
                    </div>
                  </div>

                  <!-- Bölüm: Tasarım -->
                  <div class="stack-section">
                    <span class="stack-label">Tasarım</span>
                    <div class="stack-items">
                      <div class="stack-item">
                        <span class="stack-icon" style="background: #c41230">A</span>
                        <span class="stack-name">AutoCAD</span>
                      </div>
                      <div class="stack-item">
                        <span class="stack-icon" style="background: #f5871f">F</span>
                        <span class="stack-name">Fusion 360</span>
                      </div>
                      <div class="stack-item">
                        <span class="stack-icon" style="background: #314cb0">K</span>
                        <span class="stack-name">PCB (KiCad)</span>
                      </div>
                    </div>
                  </div>

                  <!-- Bölüm: Web -->
                  <div class="stack-section">
                    <span class="stack-label">Web</span>
                    <div class="stack-items">
                      <div class="stack-item">
                        <span class="stack-icon" style="background: #42b883">V</span>
                        <span class="stack-name">Vue.js</span>
                      </div>
                      <div class="stack-item">
                        <span class="stack-icon" style="background: #ff2d20">L</span>
                        <span class="stack-name">Laravel</span>
                      </div>
                      <div class="stack-item">
                        <span class="stack-icon" style="background: #4479a1">M</span>
                        <span class="stack-name">MySQL</span>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- Sayfa: PrintCost -->
      <section v-if="aktifSekme === 'printcost'" class="page page-printcost">
        <!-- Animasyonlu Arkaplan -->
        <div class="blob-container">
          <div class="blob blob-1"></div>
          <div class="blob blob-2"></div>
          <div class="blob blob-3"></div>
        </div>

        <div class="printcost-container">
          <!-- Başlık -->
          <div class="printcost-header">
            <h1 class="printcost-title">PrintCost</h1>
            <p class="printcost-subtitle">3D Baskı Maliyet Hesaplayıcı</p>
          </div>

          <!-- Cam Panel -->
          <div class="glass-panel">
            <!-- Kart: Satış Fiyatı -->
            <div class="result-card-top">
              <div class="result-main">
                <span class="result-label">Satış Fiyatı</span>
                <div class="result-value" :class="{ pulsing: isPulsing }">
                  {{ formatFiyat(animatedPrice) }}
                  <span class="currency">₺</span>
                </div>
              </div>
              <div class="result-breakdown">
                <div class="breakdown-item">
                  <span>Ham Maliyet</span>
                  <span class="cost">{{ formatFiyat(maliyet) }} ₺</span>
                </div>
                <div class="breakdown-item">
                  <span>Kâr</span>
                  <span class="profit">+{{ formatFiyat(kar) }} ₺</span>
                </div>
              </div>
            </div>

            <!-- Girdi Alanları -->
            <div class="inputs-grid">
              <div class="input-card">
                <label class="input-label">Filament Fiyatı (TL/kg)</label>
                <input
                  type="number"
                  class="glass-input"
                  v-model.number="filamentFiyati"
                  min="0"
                  step="10"
                />
              </div>

              <div class="input-card">
                <label class="input-label">Harcanan Miktar (g)</label>
                <input type="number" class="glass-input" v-model.number="harcananGram" min="0" />
              </div>

              <div class="input-card">
                <label class="input-label">Enerji Fiyatı (TL/kWh)</label>
                <input
                  type="number"
                  class="glass-input"
                  v-model.number="enerjiFiyati"
                  min="0"
                  step="0.1"
                />
              </div>

              <div class="input-card">
                <label class="input-label">Baskı Süresi (saat)</label>
                <input
                  type="number"
                  class="glass-input"
                  v-model.number="baskiSuresi"
                  min="0"
                  step="0.5"
                />
              </div>
            </div>

            <!-- Slider: Kâr Oranı -->
            <div class="slider-card">
              <label class="input-label">
                Kâr Oranı: <span class="profit-badge">%{{ karOrani }}</span>
              </label>
              <input type="range" class="glass-range" v-model.number="karOrani" min="0" max="200" />
              <div class="range-labels">
                <span>%0</span>
                <span>%100</span>
                <span>%200</span>
              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- Sayfa: Bare Metal -->
      <section v-if="aktifSekme === 'baremetal'" class="page page-baremetal">
        <div class="baremetal-container">
          <!-- Başlık -->
          <div class="baremetal-header">
            <h1 class="baremetal-title">&lt;Bare Metal /&gt;</h1>
            <p class="baremetal-subtitle">ATmega328P Timer1 Kesme Tabanlı LED Yakıp Söndürme</p>
          </div>

          <!-- İçerik -->
          <div class="baremetal-content">
            <!-- Sol: Simülasyon -->
            <div class="simulation-section">
              <div class="simulation-card">
                <div class="simulation-header">
                  <span class="live-badge">● LIVE</span>
                  <span class="sim-title">Wokwi Simülasyon</span>
                </div>
                <div class="simulation-preview">
                  <img src="./assets/wokwi.gif?v=2" alt="Wokwi Simulation" class="simulation-img" />
                </div>
              </div>

              <!-- Donanım Bağlantıları -->
              <div class="hardware-card">
                <h4 class="card-title">Donanım Bağlantıları</h4>
                <div class="hardware-table">
                  <div class="hw-row">
                    <span class="hw-component">LED (Kırmızı)</span>
                    <span class="hw-pin">Pin 13 (PB5)</span>
                  </div>
                  <div class="hw-row">
                    <span class="hw-component">Direnç</span>
                    <span class="hw-pin">220Ω</span>
                  </div>
                  <div class="hw-row">
                    <span class="hw-component">GND</span>
                    <span class="hw-pin">Arduino GND</span>
                  </div>
                </div>
              </div>
            </div>

            <!-- Sağ: Detaylar -->
            <div class="details-section">
              <!-- Teknik Özellikler -->
              <div class="specs-card">
                <h4 class="card-title">$ ./system_info</h4>
                <div class="specs-grid">
                  <div class="spec-item">
                    <span class="spec-key">PLATFORM</span>
                    <span class="spec-value">ATmega328P</span>
                  </div>
                  <div class="spec-item">
                    <span class="spec-key">CLOCK</span>
                    <span class="spec-value">16 MHz</span>
                  </div>
                  <div class="spec-item">
                    <span class="spec-key">TIMER</span>
                    <span class="spec-value">Timer1 CTC</span>
                  </div>
                  <div class="spec-item">
                    <span class="spec-key">PRESCALER</span>
                    <span class="spec-value">1024</span>
                  </div>
                  <div class="spec-item">
                    <span class="spec-key">OCR1A</span>
                    <span class="spec-value">15624</span>
                  </div>
                  <div class="spec-item">
                    <span class="spec-key">INTERVAL</span>
                    <span class="spec-value">1 saniye</span>
                  </div>
                </div>
              </div>

              <!-- Mimari Avantajları -->
              <div class="features-card">
                <h4 class="card-title">Mimari Avantajları</h4>
                <div class="features-list">
                  <div class="feature-item">
                    <span class="feature-icon">▸</span>
                    <div class="feature-text">
                      <strong>Non-blocking</strong>
                      <span>Ana döngü boş, CPU serbest</span>
                    </div>
                  </div>
                  <div class="feature-item">
                    <span class="feature-icon">▸</span>
                    <div class="feature-text">
                      <strong>Hassas Zamanlama</strong>
                      <span>Donanım tabanlı, yazılım gecikmesi yok</span>
                    </div>
                  </div>
                  <div class="feature-item">
                    <span class="feature-icon">▸</span>
                    <div class="feature-text">
                      <strong>Enerji Verimliliği</strong>
                      <span>Uyku modları ile birleştirilebilir</span>
                    </div>
                  </div>
                  <div class="feature-item">
                    <span class="feature-icon">▸</span>
                    <div class="feature-text">
                      <strong>Bare-metal</strong>
                      <span>Arduino kütüphanelerine bağımlılık yok</span>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- Kod Bloğu -->
          <div class="code-section">
            <div class="terminal-card">
              <div class="terminal-header">
                <span class="terminal-dot red"></span>
                <span class="terminal-dot yellow"></span>
                <span class="terminal-dot green"></span>
                <span class="terminal-title">timer_interrupt_blink.c</span>
              </div>
              <pre class="code-block"><code><span class="comment">/*
 * Project: Bare-Metal Timer Interrupt Blink
 * Author: Arda Birgul
 * Target: ATmega328P (Arduino Uno)
 * Description: Non-blocking LED toggle using Timer1 CTC mode.
 * Demonstrates direct register access and hardware-level timing.
 */</span>

<span class="preprocessor">#include</span> <span class="string">&lt;avr/io.h&gt;</span>
<span class="preprocessor">#include</span> <span class="string">&lt;avr/interrupt.h&gt;</span>

<span class="comment">// 16MHz CPU with 1024 Prescaler for 1Hz frequency</span>
<span class="comment">// Calculation: (16,000,000 / 1024) - 1 = 15624</span>
<span class="preprocessor">#define</span> TIMER1_COMPARE_VALUE  <span class="number">15624</span>

<span class="type">int</span> <span class="function">main</span>(<span class="type">void</span>) {
    <span class="comment">// --- GPIO Configuration ---</span>
    <span class="comment">// Set PB5 (Digital Pin 13) as Output using Data Direction Register B</span>
    DDRB |= (<span class="number">1</span> << DDB5);

    <span class="comment">// --- Timer1 Configuration (CTC Mode) ---</span>
    <span class="comment">// TCCR1A: Reset Control Register A</span>
    TCCR1A = <span class="number">0</span>;

    <span class="comment">// TCCR1B: Set CTC mode (WGM12) and 1024 Prescaler (CS12, CS10)</span>
    TCCR1B = (<span class="number">1</span> << WGM12) | (<span class="number">1</span> << CS12) | (<span class="number">1</span> << CS10);

    <span class="comment">// OCR1A: Set Compare Match Value for exactly 1 second interval</span>
    OCR1A = TIMER1_COMPARE_VALUE;

    <span class="comment">// TIMSK1: Enable Timer1 Compare Match A Interrupt</span>
    TIMSK1 |= (<span class="number">1</span> << OCIE1A);

    <span class="comment">// Enable Global Interrupts (Status Register - I Bit)</span>
    <span class="function">sei</span>();

    <span class="comment">// Main loop remains empty; LED toggling is handled by Hardware ISR.</span>
    <span class="comment">// This architecture allows for power-saving sleep modes in the future.</span>
    <span class="keyword">while</span> (<span class="number">1</span>) {
        <span class="comment">// CPU is free for other non-blocking tasks</span>
    }

    <span class="keyword">return</span> <span class="number">0</span>;
}

<span class="comment">// --- Interrupt Service Routine (ISR) ---</span>
<span class="comment">// Triggered automatically when Timer1 reaches OCR1A value</span>
<span class="function">ISR</span>(TIMER1_COMPA_vect) {
    <span class="comment">// Toggle PB5 state using Bitwise XOR</span>
    PORTB ^= (<span class="number">1</span> << PORTB5);
}</code></pre>
            </div>
          </div>

          <!-- Formül -->
          <div class="formula-section">
            <div class="formula-card">
              <h4 class="card-title">Frekans Hesaplaması</h4>
              <div class="formula-content">
                <code class="formula">Periyot = (OCR1A + 1) × Prescaler / f_clk</code>
                <div class="formula-example">
                  <span>= (15624 + 1) × 1024 / 16,000,000</span>
                  <span class="formula-result">= 1 saniye</span>
                </div>
              </div>
            </div>
          </div>
        </div>
      </section>
    </main>

    <!-- Alt Bilgi -->
    <footer class="footer">
      <div class="container">
        <div class="row">
          <div class="col-12 text-center">
            <p>&copy; 2025 Arda Birgül. Tüm hakları saklıdır.</p>
          </div>
        </div>
      </div>
    </footer>
  </div>
</template>

<style>
/* Global Değişkenler */
:root {
  /* Hakkımda Theme */
  --bg-beige: #f5f5dc;
  --box-cream: #fcf8f1;

  /* PrintCost Theme */
  --mint-green: #5be3b1;
  --sky-blue: #a2d2ff;

  /* Accent Colors */
  --candy-red: #ff8b8b;
  --sage-green: #8da399;

  /* Bare Metal Theme */
  --dark-bg: #1a1a1a;
  --dark-box: #2d2d2d;

  /* Text */
  --text-dark: #3e362e;
  --text-muted: #8d8377;
  --text-light: #e8e8e8;
}
</style>

<style scoped>
/* Temel Ayarlar */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.app-wrapper {
  min-height: 100vh;
  transition: background 0.5s ease;
  font-family:
    'Inter',
    -apple-system,
    BlinkMacSystemFont,
    'Segoe UI',
    Roboto,
    sans-serif;
}

.app-wrapper.hakkimda {
  background: var(--bg-beige);
}

.app-wrapper.printcost {
  background: linear-gradient(135deg, #0f0c29 0%, #302b63 50%, #24243e 100%);
}

.app-wrapper.baremetal {
  background: var(--dark-bg);
}

/* Grid Sistemi */
.container {
  width: 90%;
  max-width: 1400px;
  margin: 0 auto;
  padding: 0 1rem;
}

.row {
  display: flex;
  flex-wrap: wrap;
  margin: 0 -0.75rem;
}

.row > * {
  padding: 0 0.75rem;
}

/* Mobil Öncelikli Kolonlar */
.col-12 {
  flex: 0 0 100%;
  max-width: 100%;
}

.g-3 {
  gap: 1rem;
}
.g-4 {
  gap: 1.5rem;
}

@media (min-width: 768px) {
  .col-md-6 {
    flex: 0 0 50%;
    max-width: 50%;
  }
}

@media (min-width: 992px) {
  .col-lg-5 {
    flex: 0 0 40%;
    max-width: 40%;
  }
  .col-lg-6 {
    flex: 0 0 50%;
    max-width: 50%;
  }
  .col-lg-7 {
    flex: 0 0 60%;
    max-width: 60%;
  }
  .col-lg-10 {
    flex: 0 0 85%;
    max-width: 85%;
  }
}

.justify-content-center {
  justify-content: center;
}
.align-items-center {
  align-items: center;
}
.text-center {
  text-align: center;
}
.mb-4 {
  margin-bottom: 1.5rem;
}

/* Navbar */
.navbar {
  height: 60px;
  padding: 0;
  display: flex;
  align-items: center;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 1000;
  background: #1a1e24;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
}

.navbar-container {
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 100%;
  max-width: 1400px;
  height: 100%;
  margin: 0 auto;
  padding: 0 1.5rem;
}

.navbar-brand {
  text-decoration: none;
  font-size: 1.5rem;
  display: flex;
  align-items: center;
}

.brand-bold {
  color: white;
  font-weight: 700;
}

.brand-light {
  color: rgba(255, 255, 255, 0.7);
  font-weight: 300;
}

/* Navigasyon */
.navbar-menu {
  display: flex;
}

.navbar-nav {
  display: flex;
  flex-direction: row;
  align-items: center;
  list-style: none;
  gap: 0.25rem;
  margin: 0;
  padding: 0;
}

.nav-item {
  list-style: none;
  display: flex;
  align-items: center;
}

.nav-link {
  color: white;
  text-decoration: none;
  padding: 0.5rem 1rem;
  border-radius: 8px;
  font-weight: 500;
  font-size: 0.9rem;
  transition: all 0.2s ease;
  display: flex;
  align-items: center;
  gap: 6px;
  white-space: nowrap;
  min-height: 40px;
}

.nav-link:hover {
  color: white;
  background: rgba(255, 255, 255, 0.1);
}

.nav-link:active,
.nav-link:focus,
.nav-link:visited {
  color: white;
}

.nav-link.active {
  color: white;
  background: transparent;
}

/* Açılır Menü */

/* Taşma Kontrolü */
.navbar,
.navbar-container,
.navbar-menu {
  overflow: visible !important;
}

/* Konumlandırma */
.navbar .nav-item.nav-dropdown {
  position: relative;
  z-index: 10;
}

/* Menü Butonu */
.navbar .nav-item.nav-dropdown > .nav-link {
  color: white !important;
  background: transparent !important;
}

.navbar .nav-item.nav-dropdown > .nav-link:hover {
  color: white !important;
  background: rgba(255, 255, 255, 0.1) !important;
}

/* Ok Animasyonu */
.dropdown-arrow {
  transition: transform 0.18s ease;
  margin-left: 4px;
}

.dropdown-arrow.rotated {
  transform: rotate(180deg);
}

/* Panel Görünürlüğü */
.navbar .nav-item.nav-dropdown .dropdown-menu {
  position: absolute !important;
  top: calc(100% + 8px);
  left: 0;
  right: auto;
  display: block;
  margin: 0;
  padding: 0.5rem;
  min-width: 200px;
  background: rgba(35, 35, 40, 0.98);
  border-radius: 12px;
  list-style: none;
  z-index: 1100;
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.5);
  border: 1px solid rgba(255, 255, 255, 0.08);

  /* Animasyon */
  transform-origin: top left;
  transform: translateY(-6px);
  opacity: 0;
  visibility: hidden;
  pointer-events: none;
  transition:
    opacity 0.18s ease,
    transform 0.18s ease,
    visibility 0.18s ease;
}

.navbar .nav-item.nav-dropdown .dropdown-menu li {
  list-style: none;
}

/* Aktif Durum */
.navbar .nav-item.nav-dropdown .dropdown-menu.show {
  transform: translateY(0);
  opacity: 1;
  visibility: visible;
  pointer-events: auto;
}

.dropdown-item {
  display: flex;
  align-items: center;
  gap: 0.6rem;
  padding: 0.7rem 1rem;
  color: rgba(255, 255, 255, 0.8);
  text-decoration: none;
  border-radius: 8px;
  transition: all 0.2s ease;
  font-size: 0.875rem;
}

.dropdown-item svg {
  opacity: 0.7;
}

.dropdown-item:hover {
  background: rgba(255, 255, 255, 0.1);
  color: white;
}

.dropdown-item:hover svg {
  opacity: 1;
}

.dropdown-item.active {
  background: rgba(91, 227, 177, 0.2);
}

.badge-app {
  font-size: 0.6rem;
  padding: 0.15rem 0.4rem;
  border-radius: 4px;
  background: var(--mint-green);
  color: var(--dark-bg);
  font-weight: 600;
  margin-left: auto;
}

.badge-iot {
  font-size: 0.6rem;
  padding: 0.15rem 0.4rem;
  border-radius: 4px;
  background: var(--candy-red);
  color: white;
  font-weight: 600;
  margin-left: auto;
}

/* Ana İçerik */
.main-content {
  padding-top: 60px;
  min-height: 100vh;
}

.page {
  animation: fadeIn 0.4s ease;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* Sayfa: Hakkımda */
.page-hakkimda {
  color: var(--text-dark);
  min-height: calc(100vh - 60px);
  display: flex;
  align-items: center;
  padding: 2rem 0;
}

.hakkimda-container {
  width: 90%;
  max-width: 1400px;
  margin: 0 auto;
  padding: 0 1rem;
}

.hakkimda-layout {
  display: grid;
  grid-template-columns: 1fr 1.4fr;
  gap: 3rem;
  align-items: center;
}

/* Giriş Kolonu */
.intro-col {
  order: 1;
}

.intro-content {
  max-width: 480px;
}

.intro-greeting {
  font-size: 1rem;
  color: var(--text-muted);
  margin-bottom: 0.25rem;
  font-weight: 500;
}

.intro-title {
  font-size: clamp(2.5rem, 5vw, 3.5rem);
  font-weight: 800;
  margin-bottom: 0.5rem;
  line-height: 1.1;
  color: var(--text-dark);
}

.intro-title .highlight {
  background: linear-gradient(135deg, var(--mint-green), var(--sky-blue));
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.intro-role {
  font-size: 1.1rem;
  font-weight: 600;
  color: var(--sage-green);
  margin-bottom: 1rem;
}

.intro-subtitle {
  font-size: 0.95rem;
  line-height: 1.7;
  color: var(--text-muted);
  margin-bottom: 1rem;
}

.intro-subtitle strong {
  color: var(--text-dark);
}

.intro-staj {
  font-size: 0.95rem;
  line-height: 1.7;
  color: var(--sage-green);
  margin-bottom: 1.5rem;
  padding: 0.75rem 1rem;
  background: rgba(97, 147, 124, 0.1);
  border-radius: 8px;
  border-left: 3px solid var(--sage-green);
}

.intro-staj strong {
  color: var(--text-dark);
}

.intro-cta {
  display: flex;
  gap: 0.75rem;
  flex-wrap: wrap;
  margin-bottom: 1.5rem;
}

.btn-primary-custom {
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.75rem 1.25rem;
  background: var(--text-dark);
  color: white;
  text-decoration: none;
  border-radius: 10px;
  font-weight: 600;
  font-size: 0.875rem;
  transition: all 0.3s ease;
}

.btn-primary-custom:hover {
  transform: translateY(-2px);
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.2);
}

.btn-secondary-custom {
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.75rem 1.25rem;
  background: transparent;
  color: var(--text-dark);
  text-decoration: none;
  border-radius: 10px;
  font-weight: 600;
  font-size: 0.875rem;
  border: 2px solid var(--text-dark);
  transition: all 0.3s ease;
}

.btn-secondary-custom:hover {
  background: var(--text-dark);
  color: white;
}

/* Bento Grid */
.bento-col {
  order: 2;
}

.bento-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-template-rows: auto auto auto;
  gap: 0.75rem;
}

.bento-card {
  background: var(--box-cream);
  border-radius: 14px;
  padding: 1.25rem;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.06);
  transition: all 0.3s ease;
}

.bento-card:hover {
  transform: translateY(-3px);
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.1);
}

.card-header {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  margin-bottom: 0.75rem;
}

.card-header svg {
  color: var(--sage-green);
}

.card-header h4 {
  font-size: 0.75rem;
  font-weight: 700;
  color: var(--text-dark);
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

/* Kart: Vizyon */
.bento-vision {
  grid-column: 1;
  grid-row: 1;
}

.vision-content {
  display: flex;
  flex-direction: column;
  gap: 0.6rem;
}

.vision-quote {
  font-size: 1rem;
  font-weight: 700;
  color: var(--sage-green);
  font-style: italic;
  margin: 0;
}

.vision-text {
  font-size: 0.75rem;
  color: var(--text-muted);
  line-height: 1.5;
  margin: 0;
}

.vision-tags {
  display: flex;
  gap: 0.4rem;
  flex-wrap: wrap;
  margin-top: 0.25rem;
}

.vision-tag {
  padding: 0.25rem 0.5rem;
  background: rgba(91, 227, 177, 0.15);
  border-radius: 4px;
  font-size: 0.65rem;
  font-weight: 600;
  color: #2d8a6e;
}

/* Kart: Eğitim */
.bento-education {
  grid-column: 1;
  grid-row: 2;
}

.edu-content {
  display: flex;
  flex-direction: column;
  gap: 0.2rem;
}

.edu-degree {
  font-size: 0.85rem;
  font-weight: 600;
  color: var(--text-dark);
}

.edu-school {
  font-size: 0.7rem;
  color: var(--text-muted);
}

.edu-university {
  font-size: 0.75rem;
  font-weight: 500;
  color: var(--sage-green);
  margin-top: 0.15rem;
}

/* Kart: Araçlar */
.bento-tools {
  grid-column: 1;
  grid-row: 3;
}

.tools-showcase {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 0.5rem;
}

.tool-item {
  display: flex;
  align-items: center;
  gap: 0.4rem;
  padding: 0.4rem 0.6rem;
  background: rgba(141, 163, 153, 0.1);
  border-radius: 8px;
}

.tool-item svg {
  color: var(--sage-green);
  flex-shrink: 0;
}

.tool-item span {
  font-size: 0.7rem;
  font-weight: 600;
  color: var(--text-dark);
}

/* Kart: Tech Stack */
.bento-techstack {
  grid-column: 2;
  grid-row: 1 / 4;
  display: flex;
  flex-direction: column;
}

.stack-section {
  margin-bottom: 1rem;
}

.stack-section:last-child {
  margin-bottom: 0;
}

.stack-label {
  display: block;
  font-size: 0.65rem;
  font-weight: 700;
  color: var(--sage-green);
  text-transform: uppercase;
  letter-spacing: 0.5px;
  margin-bottom: 0.5rem;
  padding-bottom: 0.25rem;
  border-bottom: 1px solid rgba(141, 163, 153, 0.2);
}

.stack-items {
  display: flex;
  flex-direction: column;
  gap: 0.4rem;
}

.stack-item {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.35rem 0;
}

.stack-icon {
  width: 26px;
  height: 26px;
  border-radius: 6px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 0.6rem;
  font-weight: 700;
  color: white;
  flex-shrink: 0;
}

.stack-name {
  font-size: 0.8rem;
  font-weight: 600;
  color: var(--text-dark);
}

/* Responsive */
@media (max-width: 1100px) {
  .hakkimda-layout {
    grid-template-columns: 1fr;
    gap: 2rem;
  }

  .intro-col {
    order: 1;
  }

  .bento-col {
    order: 2;
  }

  .intro-content {
    max-width: 100%;
    text-align: center;
  }

  .intro-cta {
    justify-content: center;
  }

  .bento-grid {
    grid-template-columns: 1fr 1fr;
    grid-template-rows: auto;
  }

  .bento-vision,
  .bento-education,
  .bento-location,
  .bento-tools {
    grid-column: auto;
    grid-row: auto;
  }

  .bento-techstack {
    grid-column: span 2;
    grid-row: auto;
  }
}

@media (max-width: 768px) {
  .bento-grid {
    grid-template-columns: 1fr;
  }

  .bento-techstack {
    grid-column: 1;
  }

  .tools-showcase {
    grid-template-columns: 1fr;
  }

  .hakkimda-container {
    width: 95%;
  }
}

@media (max-width: 991px) {
  .navbar-menu {
    position: relative;
  }
}

@media (max-width: 480px) {
  .bento-grid {
    grid-template-columns: 1fr;
  }

  .bento-software,
  .bento-hardware,
  .bento-education,
  .bento-tools,
  .bento-interests {
    grid-column: span 1;
  }

  .navbar-container {
    padding: 0 1rem;
  }

  .nav-link {
    padding: 0.4rem 0.6rem;
    font-size: 0.8rem;
    height: 36px;
  }
}

/* Sayfa: PrintCost */
.page-printcost {
  position: relative;
  overflow: hidden;
  min-height: calc(100vh - 60px);
  display: flex;
  align-items: center;
  padding: 2rem 0;
}

/* Animasyonlu Arkaplan */
.blob-container {
  position: absolute;
  inset: 0;
  overflow: hidden;
  pointer-events: none;
}

.blob {
  position: absolute;
  border-radius: 50%;
  filter: blur(80px);
  opacity: 0.4;
  animation: blobFloat 20s infinite ease-in-out;
}

.blob-1 {
  width: 400px;
  height: 400px;
  background: rgba(100, 80, 180, 0.5);
  top: -100px;
  left: -100px;
  animation-delay: 0s;
}

.blob-2 {
  width: 300px;
  height: 300px;
  background: rgba(48, 43, 99, 0.6);
  bottom: -50px;
  right: -50px;
  animation-delay: -7s;
}

.blob-3 {
  width: 250px;
  height: 250px;
  background: rgba(80, 60, 140, 0.5);
  top: 50%;
  left: 50%;
  animation-delay: -14s;
}

@keyframes blobFloat {
  0%,
  100% {
    transform: translate(0, 0) scale(1);
  }
  25% {
    transform: translate(30px, -30px) scale(1.1);
  }
  50% {
    transform: translate(-20px, 20px) scale(0.9);
  }
  75% {
    transform: translate(20px, 30px) scale(1.05);
  }
}

/* Konteyner */
.printcost-container {
  width: 90%;
  max-width: 800px;
  margin: 0 auto;
  padding: 0 1rem;
  position: relative;
  z-index: 1;
}

.printcost-header {
  text-align: center;
  margin-bottom: 1.5rem;
}

.printcost-title {
  font-size: clamp(2rem, 5vw, 3rem);
  font-weight: 800;
  color: white;
  text-shadow: 0 4px 20px rgba(0, 0, 0, 0.2);
  margin-bottom: 0.25rem;
}

.printcost-subtitle {
  font-size: 1rem;
  color: rgba(255, 255, 255, 0.9);
}

/* Cam Panel */
.glass-panel {
  background: rgba(255, 255, 255, 0.25);
  backdrop-filter: blur(20px);
  border: 1px solid rgba(255, 255, 255, 0.4);
  border-radius: 28px;
  padding: 1.5rem;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.1);
}

/* Sonuç Kartı */
.result-card-top {
  background: rgba(255, 255, 255, 0.95);
  border-radius: 20px;
  padding: 1.5rem 2rem;
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.1);
  margin-bottom: 1rem;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 2rem;
}

.result-main {
  text-align: left;
}

.result-label {
  font-size: 0.8rem;
  color: var(--text-muted);
  text-transform: uppercase;
  letter-spacing: 1px;
  font-weight: 600;
}

.result-value {
  font-size: clamp(2rem, 6vw, 3rem);
  font-weight: 800;
  color: var(--mint-green);
  margin-top: 0.25rem;
  transition: transform 0.3s ease;
}

.result-value.pulsing {
  animation: pulse 0.3s ease;
}

@keyframes pulse {
  0%,
  100% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.05);
  }
}

.currency {
  font-size: 0.5em;
  opacity: 0.7;
}

.result-breakdown {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  min-width: 150px;
}

.breakdown-item {
  display: flex;
  justify-content: space-between;
  font-size: 0.85rem;
  gap: 1rem;
  color: var(--text-dark);
}

.breakdown-item .cost {
  color: var(--candy-red);
  font-weight: 600;
}

.breakdown-item .profit {
  color: #2d8a6e;
  font-weight: 600;
}

/* Girdi Izgarası */
.inputs-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1rem;
  margin-bottom: 1rem;
}

.input-card {
  background: rgba(255, 255, 255, 0.95);
  border-radius: 16px;
  padding: 1rem 1.25rem;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.05);
}

.input-label {
  display: block;
  font-size: 0.8rem;
  font-weight: 600;
  color: var(--text-dark);
  margin-bottom: 0.5rem;
}

.glass-input {
  width: 100%;
  padding: 0.75rem 1rem;
  background: rgba(91, 227, 177, 0.15);
  border: 1px solid rgba(91, 227, 177, 0.3);
  border-radius: 10px;
  color: var(--text-dark);
  font-size: 1rem;
  font-weight: 600;
  transition: all 0.2s ease;
}

.glass-input:focus {
  outline: none;
  background: rgba(91, 227, 177, 0.25);
  border-color: var(--mint-green);
  box-shadow: 0 0 0 3px rgba(91, 227, 177, 0.2);
}

.glass-input::placeholder {
  color: var(--text-muted);
}

/* Slider Kartı */
.slider-card {
  background: rgba(255, 255, 255, 0.95);
  border-radius: 16px;
  padding: 1rem 1.25rem;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.05);
}

.slider-card .input-label {
  color: var(--text-dark);
}

.profit-badge {
  background: var(--candy-red);
  color: white;
  padding: 0.2rem 0.6rem;
  border-radius: 20px;
  font-size: 0.8rem;
  margin-left: 0.5rem;
}

.glass-range {
  width: 100%;
  height: 8px;
  -webkit-appearance: none;
  appearance: none;
  background: rgba(91, 227, 177, 0.3);
  border-radius: 4px;
  outline: none;
  margin-top: 0.5rem;
}

.glass-range::-webkit-slider-thumb {
  -webkit-appearance: none;
  width: 24px;
  height: 24px;
  background: var(--mint-green);
  border-radius: 50%;
  cursor: pointer;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
}

.glass-range::-moz-range-thumb {
  width: 24px;
  height: 24px;
  background: var(--mint-green);
  border-radius: 50%;
  cursor: pointer;
  border: none;
}

.range-labels {
  display: flex;
  justify-content: space-between;
  font-size: 0.7rem;
  color: var(--text-muted);
  margin-top: 0.5rem;
}

/* Responsive */
@media (max-width: 600px) {
  .result-card-top {
    flex-direction: column;
    text-align: center;
    gap: 1rem;
  }

  .result-main {
    text-align: center;
  }

  .result-breakdown {
    width: 100%;
    border-top: 1px solid rgba(0, 0, 0, 0.1);
    padding-top: 1rem;
  }

  .inputs-grid {
    grid-template-columns: 1fr;
  }
}

/* Sayfa: Bare Metal */
.page-baremetal {
  color: var(--text-light);
  min-height: calc(100vh - 60px);
  padding: 2rem 0;
}

.baremetal-container {
  width: 90%;
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 1rem;
}

.baremetal-header {
  text-align: center;
  margin-bottom: 2rem;
}

.baremetal-title {
  font-family: 'Courier New', 'Fira Code', monospace;
  font-size: clamp(2rem, 5vw, 3rem);
  font-weight: 700;
  color: var(--candy-red);
  text-shadow: 0 0 30px rgba(255, 139, 139, 0.4);
  margin-bottom: 0.5rem;
}

.baremetal-subtitle {
  color: rgba(255, 255, 255, 0.7);
  font-size: 1rem;
}

/* Düzen */
.baremetal-content {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1.5rem;
  margin-bottom: 1.5rem;
  align-items: stretch;
}

/* Simülasyon */
.simulation-section {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.simulation-card {
  background: rgba(30, 30, 30, 0.9);
  border-radius: 16px;
  overflow: hidden;
  border: 1px solid rgba(255, 255, 255, 0.1);
  display: flex;
  flex-direction: column;
}

.simulation-header {
  display: flex;
  align-items: center;
  gap: 1rem;
  padding: 0.75rem 1rem;
  background: rgba(0, 0, 0, 0.3);
}

.live-badge {
  background: var(--candy-red);
  color: white;
  padding: 0.25rem 0.75rem;
  border-radius: 20px;
  font-size: 0.7rem;
  font-weight: 600;
  animation: livePulse 2s infinite;
}

@keyframes livePulse {
  0%,
  100% {
    opacity: 1;
  }
  50% {
    opacity: 0.6;
  }
}

.sim-title {
  font-family: 'Courier New', monospace;
  font-size: 0.85rem;
  color: rgba(255, 255, 255, 0.6);
}

.simulation-preview {
  padding: 1rem;
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
}

.simulation-img {
  width: 100%;
  height: auto;
  max-height: 300px;
  object-fit: contain;
  display: block;
  border-radius: 8px;
  border: 2px solid rgba(255, 139, 139, 0.3);
}

/* Donanım */
.hardware-card {
  background: rgba(30, 30, 30, 0.9);
  border-radius: 16px;
  padding: 1.25rem;
  border: 1px solid rgba(255, 255, 255, 0.1);
  flex: 1;
}

.card-title {
  font-family: 'Courier New', monospace;
  font-size: 0.9rem;
  color: var(--mint-green);
  margin-bottom: 1rem;
}

.hardware-table {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.hw-row {
  display: flex;
  justify-content: space-between;
  font-size: 0.85rem;
  padding: 0.5rem 0;
  border-bottom: 1px solid rgba(255, 255, 255, 0.05);
}

.hw-row:last-child {
  border-bottom: none;
}

.hw-component {
  color: rgba(255, 255, 255, 0.8);
}

.hw-pin {
  color: var(--candy-red);
  font-family: 'Courier New', monospace;
}

/* Detaylar */
.details-section {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  height: 100%;
}

.specs-card {
  background: rgba(30, 30, 30, 0.9);
  border-radius: 16px;
  padding: 1.25rem;
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.features-card {
  background: rgba(30, 30, 30, 0.9);
  border-radius: 16px;
  padding: 1.25rem;
  border: 1px solid rgba(255, 255, 255, 0.1);
  flex: 1;
}

.specs-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 0.75rem;
}

.spec-item {
  display: flex;
  flex-direction: column;
  gap: 0.2rem;
}

.spec-key {
  font-size: 0.65rem;
  color: var(--candy-red);
  font-family: 'Courier New', monospace;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.spec-value {
  font-size: 0.9rem;
  color: rgba(255, 255, 255, 0.9);
  font-weight: 600;
}

/* Özellikler */
.features-list {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}

.feature-item {
  display: flex;
  gap: 0.75rem;
  align-items: flex-start;
}

.feature-icon {
  color: var(--mint-green);
  font-size: 1rem;
  line-height: 1;
}

.feature-text {
  display: flex;
  flex-direction: column;
  gap: 0.15rem;
}

.feature-text strong {
  font-size: 0.85rem;
  color: rgba(255, 255, 255, 0.9);
}

.feature-text span {
  font-size: 0.75rem;
  color: rgba(255, 255, 255, 0.5);
}

/* Kod */
.code-section {
  margin-bottom: 1.5rem;
}

.terminal-card {
  background: #1e1e1e;
  border-radius: 16px;
  overflow: hidden;
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.terminal-header {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 0.75rem 1rem;
  background: #2d2d2d;
}

.terminal-dot {
  width: 12px;
  height: 12px;
  border-radius: 50%;
}

.terminal-dot.red {
  background: #ff5f56;
}
.terminal-dot.yellow {
  background: #ffbd2e;
}
.terminal-dot.green {
  background: #27ca40;
}

.terminal-title {
  margin-left: 1rem;
  font-family: 'Courier New', monospace;
  font-size: 0.8rem;
  color: rgba(255, 255, 255, 0.5);
}

.code-block {
  padding: 1.25rem;
  margin: 0;
  overflow-x: auto;
  font-family: 'Courier New', 'Fira Code', monospace;
  font-size: 0.8rem;
  line-height: 1.7;
  background: #1e1e1e;
}

.code-block .comment {
  color: #6a9955;
}
.code-block .preprocessor {
  color: #c586c0;
}
.code-block .keyword {
  color: #569cd6;
}
.code-block .type {
  color: #4ec9b0;
}
.code-block .number {
  color: #b5cea8;
}
.code-block .function {
  color: #dcdcaa;
}
.code-block .string {
  color: #ce9178;
}

/* Formül */
.formula-section {
  margin-bottom: 1rem;
}

.formula-card {
  background: rgba(30, 30, 30, 0.9);
  border-radius: 16px;
  padding: 1.25rem;
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.formula-content {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}

.formula {
  font-family: 'Courier New', monospace;
  font-size: 0.9rem;
  color: var(--mint-green);
  background: rgba(0, 0, 0, 0.3);
  padding: 0.75rem 1rem;
  border-radius: 8px;
  display: block;
}

.formula-example {
  display: flex;
  flex-direction: column;
  gap: 0.25rem;
  padding-left: 1rem;
  font-family: 'Courier New', monospace;
  font-size: 0.85rem;
  color: rgba(255, 255, 255, 0.6);
}

.formula-result {
  color: var(--candy-red);
  font-weight: 600;
}

/* Responsive */
@media (max-width: 900px) {
  .baremetal-content {
    grid-template-columns: 1fr;
  }

  .specs-grid {
    grid-template-columns: 1fr 1fr;
  }
}

@media (max-width: 500px) {
  .specs-grid {
    grid-template-columns: 1fr;
  }
}

/* Alt Bilgi */
.footer {
  padding: 1.5rem 0;
  text-align: center;
}

.app-wrapper.hakkimda .footer {
  background: rgba(0, 0, 0, 0.03);
  color: var(--text-muted);
}

.app-wrapper.printcost .footer {
  background: rgba(0, 0, 0, 0.1);
  color: rgba(255, 255, 255, 0.8);
}

.app-wrapper.baremetal .footer {
  background: #151515;
  color: rgba(255, 255, 255, 0.5);
  border-top: 1px solid #2a2a2a;
}

.footer p {
  font-size: 0.85rem;
  margin: 0;
}

/* General Responsive */
@media (max-width: 767px) {
  .page {
    padding: 2rem 0;
  }

  .glass-card {
    padding: 1.5rem;
  }

  .result-card {
    padding: 1.5rem;
  }

  .terminal-body {
    padding: 1rem;
  }

  .spec-item {
    flex-direction: column;
  }

  .spec-key {
    width: auto;
    margin-bottom: 0.25rem;
  }
}
</style>
