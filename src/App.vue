<script setup>
import { onMounted } from "vue";
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

gsap.registerPlugin(ScrollTrigger);

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
});
</script>

<template>
  <div class="relative min-h-screen bg-gradient-to-b from-gray-950 to-gray-800 text-white overflow-x-hidden">
    <!-- Floating Menu -->
    <div class="fixed top-6 right-6 z-50">
      <div class="bg-white/10 backdrop-blur-lg p-4 rounded-full shadow-lg flex flex-col gap-4">
        <a href="#about" class="hover:scale-125 transition">🌀</a>
        <a href="#projects" class="hover:scale-125 transition">💻</a>
        <a href="#contact" class="hover:scale-125 transition">📩</a>
      </div>
    </div>

    <!-- Hero -->
    <section class="hero h-screen flex items-center justify-center">
      <h1 class="hero-text text-7xl md:text-9xl font-extrabold text-center leading-tight">
        Hello,<br />I’m Rengga.
      </h1>
    </section>

    <!-- About -->
    <section id="about" class="py-32 px-6 text-center">
      <h2 class="section-title text-5xl font-bold mb-10">About Me</h2>
      <p class="max-w-2xl mx-auto text-lg text-gray-300 leading-relaxed">
        I’m a fullstack developer who loves blending creativity with technology. 
        My work focuses on building immersive, user-focused digital experiences.
      </p>
    </section>

    <!-- Projects -->
    <section id="projects" class="py-32 px-6">
      <h2 class="section-title text-5xl font-bold mb-10 text-center">Projects</h2>
      <div class="grid md:grid-cols-2 gap-12">
        <div class="project-card bg-white/5 p-8 rounded-2xl shadow-xl">
          <h3 class="text-2xl font-semibold mb-4">🚀 Futuristic Landing</h3>
          <p class="text-gray-300">Landing page with scroll-based immersive animations.</p>
        </div>
        <div class="project-card bg-white/5 p-8 rounded-2xl shadow-xl">
          <h3 class="text-2xl font-semibold mb-4">🌌 Interactive Portfolio</h3>
          <p class="text-gray-300">A storytelling portfolio with parallax and scaling effects.</p>
        </div>
      </div>
    </section>

    <!-- Contact -->
    <section id="contact" class="py-32 px-6 text-center relative">
      <h2 class="section-title text-5xl font-bold mb-10 neon-yellow">Contact</h2>
      <p class="text-gray-300 mb-6">Let’s build something together!</p>
      <a href="mailto:youremail@example.com" 
         class="mt-6 inline-block glass px-8 py-4 rounded-full text-lg font-semibold hover:scale-110 transition">
        Get in Touch
      </a>
      <div class="mt-8 flex justify-center gap-8 text-2xl">
        <a href="https://instagram.com" target="_blank" class="hover:text-[#adff2f]">Instagram</a>
        <a href="https://linkedin.com" target="_blank" class="hover:text-[#adff2f]">LinkedIn</a>
        <a href="https://github.com" target="_blank" class="hover:text-[#adff2f]">GitHub</a>
      </div>
    </section>

    <!-- Footer -->
    <footer class="py-10 text-center text-gray-500 text-sm">
      © 2025 Rengga Portfolio. All rights reserved.
    </footer>
  </div>
</template>

<style scoped>
/* Tambahan noise background */
.hero {
  background: radial-gradient(circle at center, rgba(255,255,255,0.05) 0%, transparent 70%);
}
</style>
