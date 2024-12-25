<template>
  <div ref="snowContainer" class="snow-container"></div>
  <div class="flex flex-col gap-2 w-screen items-center justify-center">
    <h1 class="text-3xl font-bold">Merry Christmas <br/> 🎄✨🎅🎁</h1>

    <div class="flex flex-col items-bottom gap-2">
      <iframe
        class="w-screen md:w-96 h-72 md:h-96"
        src="https://www.youtube.com/embed/OUjprWAg1A8?autoplay=1&loop=1&controls=0&si=EjqfoYymDQhnzhwr"
        title="YouTube video player"
        frameborder="0"
        allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
        referrerpolicy="strict-origin-when-cross-origin"
        allowfullscreen
      ></iframe>
      <div class=" flex justify-center">
        <img src="/gcash.jpg" alt="" srcset="" class="h-96" />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      particlesPerThousandPixels: 0.1,
      fallSpeed: 1.25,
      pauseWhenNotActive: true,
      maxSnowflakes: 200,
      snowflakes: [],
      snowflakeInterval: null,
      isTabActive: true,
    };
  },
  mounted() {
    this.generateSnowflakes();
    window.addEventListener("resize", this.handleResize);
    document.addEventListener("visibilitychange", this.handleVisibilityChange);
  },
  beforeUnmount() {
    clearInterval(this.snowflakeInterval);
    window.removeEventListener("resize", this.handleResize);
    document.removeEventListener(
      "visibilitychange",
      this.handleVisibilityChange
    );
  },
  methods: {
    resetSnowflake(snowflake) {
      const size = Math.random() * 5 + 1;
      const viewportWidth = window.innerWidth - size;
      const viewportHeight = window.innerHeight;

      snowflake.style.width = `${size}px`;
      snowflake.style.height = `${size}px`;
      snowflake.style.left = `${Math.random() * viewportWidth}px`;
      snowflake.style.top = `-${size}px`;

      const animationDuration = (Math.random() * 3 + 2) / this.fallSpeed;
      snowflake.style.animationDuration = `${animationDuration}s`;
      snowflake.style.animationTimingFunction = "linear";
      snowflake.style.animationName =
        Math.random() < 0.5 ? "fall" : "diagonal-fall";

      setTimeout(() => {
        if (parseInt(snowflake.style.top, 10) < viewportHeight) {
          this.resetSnowflake(snowflake);
        } else {
          snowflake.remove();
          this.createSnowflake();
        }
      }, animationDuration * 1000);
    },
    createSnowflake() {
      const snowContainer = this.$refs.snowContainer;
      if (!snowContainer) {
        return;
      }

      if (this.snowflakes.length < this.maxSnowflakes) {
        const snowflake = document.createElement("div");
        snowflake.classList.add("snowflake");
        this.snowflakes.push(snowflake);
        snowContainer.appendChild(snowflake);
        this.resetSnowflake(snowflake);
      }
    },
    generateSnowflakes() {
      const numberOfParticles =
        Math.ceil((window.innerWidth * window.innerHeight) / 1000) *
        this.particlesPerThousandPixels;
      const interval = 5000 / numberOfParticles;

      clearInterval(this.snowflakeInterval);
      this.snowflakeInterval = setInterval(() => {
        if (this.isTabActive && this.snowflakes.length < this.maxSnowflakes) {
          requestAnimationFrame(() => this.createSnowflake());
        }
      }, interval);
    },
    handleVisibilityChange() {
      if (!this.pauseWhenNotActive) return;

      this.isTabActive = !document.hidden;
      if (this.isTabActive) {
        this.generateSnowflakes();
      } else {
        clearInterval(this.snowflakeInterval);
      }
    },
    handleResize() {
      clearInterval(this.snowflakeInterval);
      setTimeout(() => this.generateSnowflakes(), 1000);
    },
  },
};
</script>

<style>
.snow-container {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  display: flex;
  flex-direction: column;
  gap: 2rem;
  justify-content: center;
  align-items: center;
  overflow: hidden;
  z-index: 9999;
  pointer-events: none;
  background: transparent;
}

.snowflake {
  position: absolute;
  background: white;
  border-radius: 50%;
  opacity: 0.8;
  animation-fill-mode: forwards;
}

/* Responsive styles */
@media (max-width: 768px) {
  .snow-container {
    gap: 1rem;
  }

  .snowflake {
    opacity: 0.6;
  }

  iframe {
    width: 100%;
  }
}

@keyframes fall {
  0% {
    transform: translateY(0);
  }
  100% {
    transform: translateY(100vh);
  }
}

@keyframes diagonal-fall {
  0% {
    transform: translate(0, 0);
  }
  100% {
    transform: translate(50vw, 100vh);
  }
}
</style>
