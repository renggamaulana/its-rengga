<script setup>
import { onMounted, onBeforeUnmount } from "vue";
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

gsap.registerPlugin(ScrollTrigger);

let moveHandler = null;
let enterHandler = null;
let leaveHandler = null;
let hoverTargets = [];

onMounted(() => {
  // Hero text scale on scroll
  gsap.to(".hero-text", {
    scale: 5,
    opacity: 0,
    filter: "blur(0px)",
    scrollTrigger: {
      trigger: ".hero",
      start: "top top",
      end: "bottom top",
      scrub: true,
    },
  });

  // Section titles pop in
  gsap.utils.toArray(".section-title").forEach((title) => {
    gsap.from(title, {
      y: 100,
      scale: 0.8,
      opacity: 0,
      duration: 1,
      scrollTrigger: {
        trigger: title,
        start: "top 80%",
        toggleActions: "play none none reverse",
      },
    });
  });

  // Skill icons animation
  gsap.utils.toArray(".skill-item").forEach((skill, i) => {
    gsap.from(skill, {
      y: 50,
      opacity: 0,
      scale: 0.8,
      delay: i * 0.1,
      scrollTrigger: {
        trigger: ".skills",
        start: "top 70%",
      },
    });
  });

  // Project cards animation
  gsap.utils.toArray(".project-card").forEach((card, i) => {
    gsap.from(card, {
      y: 150,
      rotate: i % 2 === 0 ? -5 : 5,
      opacity: 0,
      duration: 1,
      scrollTrigger: {
        trigger: card,
        start: "top 85%",
        toggleActions: "play none none reverse",
      },
    });
  });

  // =========================
  // CUSTOM CURSOR + TRAIL
  // =========================
  const cursor = document.getElementById("cursor");
  const cursorDot = document.getElementById("cursor-dot");

  if (!cursor || !cursorDot) return;

  // Buat 10 trail dots
  const trailEls = Array.from({ length: 10 }).map(() => {
    const d = document.createElement("div");
    d.className = "trail-dot";
    document.body.appendChild(d);
    return d;
  });

  // Set anchor transform ke center
  gsap.set([cursor, cursorDot, trailEls], { xPercent: -50, yPercent: -50 });

  moveHandler = (e) => {
    const x = e.clientX;
    const y = e.clientY;

    // Titik tengah (cepat)
    gsap.to(cursorDot, {
      x,
      y,
      duration: 0.12,
      ease: "power3.out",
    });

    // Lingkaran luar (lebih lambat -> trail feel)
    gsap.to(cursor, {
      x,
      y,
      duration: 0.45,
      ease: "expo.out",
    });

    // Jejak neon (stagger)
    gsap.to(trailEls, {
      x,
      y,
      duration: 0.6,
      ease: "expo.out",
      stagger: 0.02,
    });
  };

  window.addEventListener("mousemove", moveHandler);

  // Hover states (membesar & neon)
  hoverTargets = Array.from(document.querySelectorAll("a, button, .project-card"));
  hoverTargets.forEach((el) => {
    el.addEventListener("mouseenter", () => {
      gsap.to(cursor, {
        scale: 1.8,
        borderColor: "rgba(173,255,47,1)",
        boxShadow: "0 0 30px rgba(173,255,47,0.7), inset 0 0 20px rgba(173,255,47,0.25)",
        duration: 0.25,
      });
      gsap.to(cursorDot, { scale: 0.5, background: "#adff2f", duration: 0.25 });
    });
    el.addEventListener("mouseleave", () => {
      gsap.to(cursor, {
        scale: 1,
        borderColor: "rgba(255,255,255,0.5)",
        boxShadow: "0 0 0 rgba(0,0,0,0)",
        duration: 0.3,
      });
      gsap.to(cursorDot, { scale: 1, background: "#ffffff", duration: 0.3 });
    });
  });

  // Sembunyikan cursor saat mouse keluar layar, tampilkan lagi saat masuk
  leaveHandler = () => gsap.to([cursor, cursorDot, ...trailEls], { autoAlpha: 0, duration: 0.15 });
  enterHandler = () => gsap.to([cursor, cursorDot, ...trailEls], { autoAlpha: 1, duration: 0.15 });
  window.addEventListener("mouseleave", leaveHandler);
  window.addEventListener("mouseenter", enterHandler);

  // Simpan untuk cleanup
  cursor.dataset.trailCount = String(trailEls.length);
});

onBeforeUnmount(() => {
  if (moveHandler) window.removeEventListener("mousemove", moveHandler);
  if (leaveHandler) window.removeEventListener("mouseleave", leaveHandler);
  if (enterHandler) window.removeEventListener("mouseenter", enterHandler);
  // Hapus trail dots
  document.querySelectorAll(".trail-dot").forEach((el) => el.remove());
});

import { ref } from "vue"

const isOpen = ref(false)

const toggleMenu = () => {
  isOpen.value = !isOpen.value
}
</script>

<template>
  <!-- Tambah cursor-none agar cursor default hilang -->
  <div class="relative min-h-screen bg-gradient-to-b from-gray-950 to-gray-800 text-white overflow-x-hidden cursor-none">
       <!-- Floating Navbar -->
    <nav class="fixed top-4 left-7/8 -translate-x-1/2 bg-black/70 backdrop-blur-md text-white px-6 py-3 rounded-2xl shadow-lg z-50 :w-[50%] md:w-auto flex items-center justify-between">
      
      <!-- Hamburger Button -->
      <button 
        class="flex flex-col gap-[5px] focus:outline-none"
        @click="toggleMenu"
      >
        <span class="w-6 h-[2px] bg-white rounded"></span>
        <span class="w-6 h-[2px] bg-white rounded"></span>
        <span class="w-6 h-[2px] bg-white rounded"></span>
      </button>
    </nav>

    <!-- Mobile Menu -->
    <transition name="fade">
      <div 
        v-if="isOpen" 
        class="fixed top-16 left-5/7 -translate-x-1/2 bg-black/80 backdrop-blur-md rounded-2xl shadow-lg p-5 flex flex-col gap-4 text-center md:w-[25%] w-[50%] z-40"
      >
        <a href="#" class="hover:text-yellow-400 transition">Home</a>
        <a href="#about" class="hover:text-yellow-400 transition">About</a>
        <a href="#projects" class="hover:text-yellow-400 transition">Projects</a>
        <a href="#contact" class="hover:text-yellow-400 transition">Contact</a>
      </div>
    </transition>


    <!-- Custom Cursor (elemen DOM harus ada) -->
    <div id="cursor"></div>
    <div id="cursor-dot"></div>

    <!-- Hero -->
    <section
      class="relative hero h-screen flex flex-col items-center justify-center overflow-hidden bg-gradient-to-br from-gray-900 via-black to-gray-800 text-white"
    >
      <!-- Abstract Background Pattern -->
      <div class="absolute inset-0">
        <div class="absolute -top-20 -left-20 w-96 h-96 bg-teal-500/20 rounded-full blur-3xl animate-pulse"></div>
        <div class="absolute bottom-0 right-0 w-[30rem] h-[30rem] bg-indigo-500/20 rounded-full blur-3xl animate-bounce"></div>
      </div>

      <!-- Hero Text -->
      <h1 class="hero-text text-6xl md:text-8xl font-extrabold text-center leading-tight z-10 drop-shadow-lg">
        Hello,<br />
        I’m <span class="text-teal-400">Rengga</span>.
      </h1>

      <!-- Floating 3D-like Shapes -->
      <div class="absolute w-full h-full top-0 left-0 pointer-events-none z-0">
        <div class="absolute top-1/3 left-[15%] w-16 h-16 bg-gradient-to-tr from-teal-400 to-cyan-500 rounded-xl animate-float"></div>
        <div class="absolute bottom-20 right-[20%] w-24 h-24 bg-gradient-to-tr from-pink-500 to-red-500 rounded-full animate-float-slow"></div>
        <div class="absolute top-[20%] right-[10%] w-20 h-20 bg-gradient-to-tr from-yellow-400 to-orange-500 rotate-45 animate-spin-slow"></div>
      </div>
    </section>

    <!-- About -->
    <section id="about" class="py-32 px-6 text-center">
      <h2 class="section-title text-5xl font-bold mb-10">About Me</h2>
      <p class="max-w-2xl mx-auto text-lg text-gray-300 leading-relaxed">
        I’m a fullstack developer who loves blending creativity with technology. My work focuses on building immersive,
        user-focused digital experiences.
      </p>
    </section>

    <!-- Skills -->
    <section id="skills" class="skills py-32 px-6 text-center">
      <h2 class="section-title text-5xl font-bold mb-16">⚡ Skills & Tech Stack</h2>

      <div class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-6 gap-8 max-w-6xl mx-auto">
        <div
          class="skill-item group relative bg-white/5 rounded-xl p-6 flex flex-col items-center justify-center shadow-lg hover:shadow-[0_0_25px_#adff2f] transition-all duration-300 hover:scale-110 cursor-pointer"
        >
          <img
            src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/javascript/javascript-original.svg"
            class="h-12 mb-3 group-hover:scale-125 transition"
          />
          <p class="text-sm text-gray-300 group-hover:text-[#adff2f] transition">JavaScript</p>
        </div>

        <div
          class="skill-item group relative bg-white/5 rounded-xl p-6 flex flex-col items-center justify-center shadow-lg hover:shadow-[0_0_25px_#42b883] transition-all duration-300 hover:scale-110 cursor-pointer"
        >
          <img
            src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/vuejs/vuejs-original.svg"
            class="h-12 mb-3 group-hover:scale-125 transition"
          />
          <p class="text-sm text-gray-300 group-hover:text-[#42b883] transition">Vue.js</p>
        </div>

        <div
          class="skill-item group relative bg-white/5 rounded-xl p-6 flex flex-col items-center justify-center shadow-lg hover:shadow-[0_0_25px_#61dafb] transition-all duration-300 hover:scale-110 cursor-pointer"
        >
          <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/react/react-original.svg" class="h-12 mb-3 group-hover:scale-125 transition" />
          <p class="text-sm text-gray-300 group-hover:text-[#61dafb] transition">React</p>
        </div>

        <div
          class="skill-item group relative bg-white/5 rounded-xl p-6 flex flex-col items-center justify-center shadow-lg hover:shadow-[0_0_25px_#3c873a] transition-all duration-300 hover:scale-110 cursor-pointer"
        >
          <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/nodejs/nodejs-original.svg" class="h-12 mb-3 group-hover:scale-125 transition" />
          <p class="text-sm text-gray-300 group-hover:text-[#3c873a] transition">Node.js</p>
        </div>

        <div
          class="skill-item group relative bg-white/5 rounded-xl p-6 flex flex-col items-center justify-center shadow-lg hover:shadow-[0_0_25px_#00758f] transition-all duration-300 hover:scale-110 cursor-pointer"
        >
          <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/mysql/mysql-original.svg" class="h-12 mb-3 group-hover:scale-125 transition" />
          <p class="text-sm text-gray-300 group-hover:text-[#00758f] transition">MySQL</p>
        </div>

        <div
          class="skill-item group relative bg-white/5 rounded-xl p-6 flex flex-col items-center justify-center shadow-lg hover:shadow-[0_0_25px_#2496ed] transition-all duration-300 hover:scale-110 cursor-pointer"
        >
          <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/docker/docker-original.svg" class="h-12 mb-3 group-hover:scale-125 transition" />
          <p class="text-sm text-gray-300 group-hover:text-[#2496ed] transition">Docker</p>
        </div>
      </div>
    </section>

    <!-- Projects -->
    <section id="projects" class="py-32 px-6">
      <h2 class="section-title text-5xl font-bold mb-10 text-center">Projects</h2>
      <div class="grid md:grid-cols-2 gap-12 max-w-6xl mx-auto">
        <div class="project-card bg-white/5 rounded-2xl shadow-xl overflow-hidden">
          <img
            src="https://images.unsplash.com/photo-1504384308090-c894fdcc538d?w=600&h=400&fit=crop"
            alt="Project preview"
            class="w-full h-48 object-cover"
          />
          <div class="p-6">
            <h3 class="text-2xl font-semibold mb-2">🚀 Futuristic Landing</h3>
            <p class="text-gray-300 mb-4">Landing page with immersive scroll animations, smooth transitions, and futuristic design.</p>
            <div class="flex gap-3 text-sm text-gray-400">
              <span class="px-2 py-1 bg-gray-700 rounded-md">Vue</span>
              <span class="px-2 py-1 bg-gray-700 rounded-md">GSAP</span>
              <span class="px-2 py-1 bg-gray-700 rounded-md">Tailwind</span>
            </div>
          </div>
        </div>

        <div class="project-card bg-white/5 rounded-2xl shadow-xl overflow-hidden">
          <img
            src="https://images.unsplash.com/photo-1519389950473-47ba0277781c?w=600&h=400&fit=crop"
            alt="Workspace"
            class="w-full h-48 object-cover"
          />
          <div class="p-6">
            <h3 class="text-2xl font-semibold mb-2">🌌 Interactive Portfolio</h3>
            <p class="text-gray-300 mb-4">A storytelling portfolio with parallax, scaling effects, and interactive 3D-inspired UI.</p>
            <div class="flex gap-3 text-sm text-gray-400">
              <span class="px-2 py-1 bg-gray-700 rounded-md">React</span>
              <span class="px-2 py-1 bg-gray-700 rounded-md">Framer Motion</span>
              <span class="px-2 py-1 bg-gray-700 rounded-md">Tailwind</span>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- Contact -->
    <section id="contact" class="py-32 px-6 text-center relative">
      <h2 class="section-title text-5xl font-bold mb-10 neon-yellow">Contact</h2>
      <p class="text-gray-300 mb-6">Let’s build something together!</p>
      <a href="mailto:youremail@example.com" class="mt-6 inline-block glass px-8 py-4 rounded-full text-lg font-semibold hover:scale-110 transition">
        Get in Touch
      </a>
      <div class="mt-8 flex justify-center gap-8 text-2xl">
        <a href="https://github.com/renggamaulana" target="_blank" class="hover:text-[#adff2f]">Github</a>
        <a href="https://www.linkedin.com/in/rengga-maulana-93b901194/" target="_blank" class="hover:text-[#adff2f]">LinkedIn</a>
        <a href="https://instagram.com/renggaamln/" target="_blank" class="hover:text-[#adff2f]">Instagram</a>
      </div>
    </section>

    <!-- Footer -->
    <footer class="py-10 text-center text-gray-500 text-sm">© 2025 Rengga Portfolio. All rights reserved.</footer>
  </div>
</template>

<style scoped>
html {
  scroll-behavior: smooth;
}

.hero {
  background: radial-gradient(circle at center, rgba(173, 255, 47, 0.15) 0%, transparent 70%);
}

/* Floating shapes */
@keyframes float {
  0%, 100% { transform: translateY(0px); }
  50% { transform: translateY(-20px); }
}
@keyframes float-slow {
  0%, 100% { transform: translateY(0px) rotate(0deg); }
  50% { transform: translateY(-30px) rotate(5deg); }
}
@keyframes spin-slow {
  0% { transform: rotate(0deg) translateY(0); }
  100% { transform: rotate(360deg) translateY(0); }
}
.animate-float { animation: float 4s ease-in-out infinite; }
.animate-float-slow { animation: float-slow 6s ease-in-out infinite; }
.animate-spin-slow { animation: spin-slow 15s linear infinite; }

/* Cursor Lingkaran Luar */
#cursor {
  position: fixed;
  top: 0; left: 0;
  width: 42px; height: 42px;
  border: 2px solid rgba(255, 255, 255, 0.5);
  border-radius: 50%;
  pointer-events: none;
  transform: translate(-50%, -50%);
  z-index: 9999;
  mix-blend-mode: difference;
  transition: border 0.3s ease, box-shadow 0.3s ease, transform 0.1s ease;
  will-change: transform;
  backdrop-filter: blur(2px);
}

/* Cursor Dot (Titik Tengah) */
#cursor-dot {
  position: fixed;
  top: 0; left: 0;
  width: 10px; height: 10px;
  background: #fff;
  border-radius: 50%;
  pointer-events: none;
  transform: translate(-50%, -50%);
  z-index: 10000;
  mix-blend-mode: difference;
  transition: background 0.3s ease, transform 0.1s ease;
  will-change: transform;
}

/* Trail Dots (global karena dibuat dinamis di <body>) */
:global(.trail-dot) {
  position: fixed;
  top: 0; left: 0;
  width: 10px; height: 10px;
  border-radius: 50%;
  pointer-events: none;
  transform: translate(-50%, -50%);
  z-index: 9998;
  mix-blend-mode: screen;
  background: radial-gradient(circle at center, rgba(173,255,47,0.9), rgba(173,255,47,0.0) 70%);
  filter: blur(2px);
  opacity: 0.75;
  will-change: transform, opacity;
}

/* Sembunyikan cursor custom di device touch */
@media (pointer: coarse) {
  #cursor, #cursor-dot, :global(.trail-dot) { display: none; }
}

/* Glassmorphism util */
.glass {
  background: rgba(255, 255, 255, 0.05);
  backdrop-filter: blur(16px);
  border: 1px solid rgba(255, 255, 255, 0.1);
}

/* Neon Accent (opsional untuk judul contact) */
.neon-yellow {
  color: #f7ff0a;
  text-shadow: 0 0 10px #f7ff0a, 0 0 20px #ffee32;
}

.fade-enter-active, .fade-leave-active {
  transition: all 0.3s ease;
}
.fade-enter-from, .fade-leave-to {
  opacity: 0;
  transform: translateY(-10px);
}
</style>
