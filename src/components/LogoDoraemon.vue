<template>
  <div class="logo-doraemon">
    <div class="container">
      <div class="doraemon">
        <div class="face">
          <div class="white">
            <div class="eye left">
              <div class="eye-black"></div>
            </div>
            <div class="eye right">
              <div class="eye-black"></div>
            </div>
            <div class="nose"></div>
            <div class="mouth"></div>
            <div class="mustache left"></div>
            <div class="mustache two left"></div>
            <div class="mustache three left"></div>
            <div class="mustache right"></div>
            <div class="mustache two right"></div>
            <div class="mustache three right"></div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'LogoDoraemon',
  mounted() {
    window.addEventListener('mousemove', this.moveEyes);
  },
  beforeUnmount() {
    window.removeEventListener('mousemove', this.moveEyes);
  },
  methods: {
    moveEyes(event) {
      const eyes = this.$el.querySelectorAll('.eye');
      eyes.forEach(eye => {
        const pupil = eye.querySelector('.eye-black');
        const rect = eye.getBoundingClientRect();
        // centro del ojo
        const cx = rect.left + rect.width / 2;
        const cy = rect.top + rect.height / 2;
        // vector al cursor
        const dx = event.clientX - cx;
        const dy = event.clientY - cy;
        // ángulo
        const angle = Math.atan2(dy, dx);
        // radio máximo de desplazamiento
        const max = 10;
        // coordenadas finales
        const x = Math.cos(angle) * max;
        const y = Math.sin(angle) * max;
        pupil.style.transform = `translate(${x}px, ${y}px)`;
      });
    }
  }
};
</script>

<style scoped>
.logo-doraemon {
  width: 60px;
  height: 60px;
  overflow: hidden;
  position: relative;
}
.logo-doraemon .container {
  width: 500px;
  height: 500px;
  transform: scale(0.12);
  transform-origin: top left;
  position: absolute;
  top: 0;
  left: 0;
}
.logo-doraemon .container .doraemon {
  width: 100%;
  height: 100%;
}

/* Cara */
.logo-doraemon .face {
  width: 100%;
  height: 100%;
  background-color: var(--main-color, #51a1c4);
  border-radius: 50%;
  border: 2px solid var(--black, #000);
  position: relative;
  transition: background-color 1s ease;
}
.logo-doraemon .face:hover {
  background-color: #d6c2e8;
  transition: background-color 5s ease;
}

/* Parte blanca inferior */
.logo-doraemon .white {
  --size: 400px;
  width: var(--size);
  height: calc(var(--size) - 62px);
  position: absolute;
  bottom: 0;
  left: calc(50% - var(--size) / 2);
  background-color: var(--white, #fff);
  border-radius: 50%;
  border: 2px solid var(--black, #000);
}

/* Ojos */
.logo-doraemon .eye {
  width: 130px;
  height: 150px;
  background-color: var(--white, #fff);
  border-radius: 50%;
  border: 2px solid var(--black, #000);
  position: absolute;
  top: -75px;
}
.logo-doraemon .eye.left  { left: calc(50% - 130px); }
.logo-doraemon .eye.right { right: calc(50% - 130px); }

/* Pupila */
.logo-doraemon .eye-black {
  --size: 60px;
  width: var(--size);
  height: var(--size);
  background-color: var(--black, #000);
  border-radius: 50%;
  position: absolute;
  bottom: 0;
  left: calc(50% - var(--size) / 2);
  transform-origin: center;
}
.logo-doraemon .eye-black::after {
  content: "";
  --size: 20px;
  width: var(--size);
  height: var(--size);
  background-color: var(--white, #fff);
  border-radius: 50%;
  position: absolute;
  left: 10px;
  top: calc(50% - 10px);
}

/* Nariz */
.logo-doraemon .nose {
  --size: 60px;
  width: var(--size);
  height: var(--size);
  background-color: var(--red, #b13f54);
  border: 2px solid var(--black, #000);
  border-radius: 50%;
  position: absolute;
  left: calc(50% - var(--size) / 2);
  top: 50px;
}
.logo-doraemon .nose::after {
  content: "";
  --size: 20px;
  width: var(--size);
  height: var(--size);
  background-color: var(--white, #fff);
  border-radius: 50%;
  position: absolute;
  top: 10px;
  left: 20%;
}

/* Boca */
.logo-doraemon .mouth {
  --size: 30px;
  width: var(--size);
  height: var(--size);
  background-color: var(--red, #b13f54);
  border: 2px solid var(--black, #000);
  border-radius: 50%;
  position: absolute;
  bottom: 90px;
  left: calc(50% - var(--size) / 2);
  transform-origin: top;
  transition: all 1s ease;
}
.logo-doraemon .face:hover .mouth {
  border-bottom: 0;
  width: calc(var(--size) * 6);
  left: calc(50% - var(--size) * 6 / 2);
  transition: all 4s ease;
}
.logo-doraemon .mouth::before {
  content: "";
  width: 2px;
  height: 106px;
  background-color: var(--black, #000);
  position: absolute;
  bottom: 100%;
  left: calc(50% - 1px);
}

/* Bigotes */
.logo-doraemon .mustache {
  position: absolute;
  height: 2px;
  width: 150px;
  top: 120px;
  background-color: var(--black, #000);
}
.logo-doraemon .mustache.left  { left: 0;  transform-origin: right; transform: rotate(20deg); }
.logo-doraemon .mustache.right { right: 0; transform-origin: left;  transform: rotate(-20deg); }
.logo-doraemon .mustache.two   { top: 145px; }
.logo-doraemon .mustache.three { top: 170px; }
.logo-doraemon .mustache.left.two   { left: -10px;  transform: rotate(0deg); }
.logo-doraemon .mustache.left.three { left: 0;      transform: rotate(-20deg); }
.logo-doraemon .mustache.right.two  { right: -10px; transform: rotate(0deg); }
.logo-doraemon .mustache.right.three{ right: 0;     transform: rotate(20deg); }

@keyframes mustache-wave {
  0%,100% { transform: none; }
  20%      { transform: rotate(3deg) translate(2px,-1px); }
  40%      { transform: rotate(-2deg) translate(-1px,1px); }
  60%      { transform: rotate(2deg) translate(1px,-1px); }
  80%      { transform: rotate(-3deg) translate(-2px,1px); }
}
.logo-doraemon .container:hover .mustache {
  animation: mustache-wave 1.8s infinite ease-in-out;
}
</style>
