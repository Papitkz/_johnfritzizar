<template>
  <section id="education" class="section-padding gradient-bg relative overflow-hidden section-transition pro-section">
    <!-- Enhanced Background elements -->
    <div class="bg-blob blob-1"></div>
    <div class="bg-blob blob-2"></div>
    <div class="absolute inset-0 grid-bg opacity-10"></div>
    
    <!-- Geometric shapes for visual interest -->
    <div class="absolute top-20 left-10 w-32 h-32 border-l-2 border-t-2 border-cyan-500/20 transform rotate-45 floating-element"></div>
    <div class="absolute bottom-20 right-10 w-40 h-40 border-r-2 border-b-2 border-purple-500/20 transform rotate-45 floating-element" style="animation-delay: 2s;"></div>
    
    <!-- Floating geometric shapes -->
    <div class="geometric-shape shape-triangle floating-element" style="top: 14%; left: 8%; animation-delay: 0.6s;"></div>
    <div class="geometric-shape shape-hexagon floating-element" style="top: 68%; right: 9%; animation-delay: 2.4s;"></div>
    
    <div class="container mx-auto px-6 relative z-10">
      <div class="text-center mb-16 animate-on-scroll fade-in-up">
        <h2 class="section-title">EDUCATION</h2>
        <div class="w-24 h-1 bg-gradient-to-r from-cyan-400 to-purple-500 mx-auto mt-4 title-line"></div>
        <p class="text-gray-400 max-w-2xl mx-auto mt-4">My academic background and educational qualifications</p>
      </div>
      
      <div class="grid grid-cols-1 md:grid-cols-2 gap-8 max-w-4xl mx-auto">
        <div 
          v-for="(edu, index) in education" 
          :key="index" 
          class="education-card glass-card glass-hover group animate-on-scroll zoom-in"
          :style="{ 'animation-delay': `${index * 0.2}s` }"
        >
          <div class="glass-reflection"></div>
          <!-- Corner accent -->
          <div class="absolute top-0 left-0 w-8 h-8 border-t-2 border-l-2 border-cyan-400 transition-all duration-300 group-hover:w-12 group-hover:h-12"></div>
          <div class="absolute bottom-0 right-0 w-8 h-8 border-b-2 border-r-2 border-purple-400 transition-all duration-300 group-hover:w-12 group-hover:h-12"></div>
          
          <div class="flex flex-col md:flex-row items-start">
            <div class="bg-cyan-400 text-gray-900 w-16 h-16 flex items-center justify-center mr-6 flex-shrink-0 transform transition duration-500 group-hover:rotate-12 group-hover:scale-110">
              <svg class="w-8 h-8" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 14l9-5-9-5-9 5 9 5z"></path>
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 14l6.16-3.422a12.083 12.083 0 01.665 6.479A11.952 11.952 0 0012 20.055a11.952 11.952 0 00-6.824-2.998 12.078 12.078 0 01.665-6.479L12 14z"></path>
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 14l9-5-9-5-9 5 9 5zm0 0l6.16-3.422a12.083 12.083 0 01.665 6.479A11.952 11.952 0 0012 20.055a11.952 11.952 0 00-6.824-2.998 12.078 12.078 0 01.665-6.479L12 14zm-4 6v-7.5l4-2.222"></path>
              </svg>
            </div>
            <div class="flex-1">
              <h3 class="text-2xl font-bold text-white mb-2" style="font-family: 'Orbitron', sans-serif;">{{ edu.degree }}</h3>
              <h4 class="text-xl text-cyan-400 mb-1">{{ edu.institution }}</h4>
              <p class="text-gray-400 mb-2">{{ edu.period }}</p>
              <div class="flex items-center text-gray-300 group-hover:text-cyan-400 transition-colors cursor-pointer location-text" @click="toggleMap(index)">
                <svg class="w-4 h-4 mr-1" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17.657 16.657L13.414 20.9a1.998 1.998 0 01-2.827 0l-4.244-4.243a8 8 0 1111.314 0z"></path>
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 11a3 3 0 11-6 0 3 3 0 016 0z"></path>
                </svg>
                <span>{{ edu.location }}</span>
                <svg class="w-4 h-4 ml-2 transform transition-transform duration-300" :class="activeMap === index ? 'rotate-180' : ''" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7"></path>
                </svg>
              </div>
            </div>
          </div>
          
          <!-- Map Section with smooth expand -->
          <div class="mt-4 overflow-hidden transition-all duration-700 ease-in-out" :style="{ maxHeight: activeMap === index ? '300px' : '0', opacity: activeMap === index ? '1' : '0' }">
            <div class="map-container relative h-64 bg-gray-900/50 rounded-lg overflow-hidden">
              <div class="absolute inset-0 flex items-center justify-center">
                <div class="text-center">
                  <div class="inline-block p-4 bg-gray-800/80 backdrop-blur-sm rounded-lg border border-cyan-400/30">
                    <h4 class="text-white font-bold mb-2">{{ edu.location }}</h4>
                    <p class="text-gray-400 text-sm mb-3">Coordinates: {{ edu.coordinates }}</p>
                    <a 
                      :href="edu.mapUrl" 
                      target="_blank" 
                      class="inline-flex items-center px-4 py-2 bg-cyan-500 text-gray-900 text-sm font-medium hover:bg-cyan-400 transition-all duration-300 hover:scale-105"
                    >
                      <svg class="w-4 h-4 mr-1" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 6H6a2 2 0 00-2 2v10a2 2 0 002 2h10a2 2 0 002-2v-4M14 4h6m0 0v6m0-6L10 14"></path>
                      </svg>
                      View on Map
                    </a>
                  </div>
                </div>
              </div>
              <!-- Decorative map grid -->
              <div class="absolute inset-0 opacity-10" style="background-image: linear-gradient(rgba(0, 212, 255, 0.3) 1px, transparent 1px), linear-gradient(90deg, rgba(0, 212, 255, 0.3) 1px, transparent 1px); background-size: 20px 20px;"></div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  name: 'EducationSection',
  data() {
    return {
      activeMap: null,
      education: [
        {
          degree: 'BACHELOR OF SCIENCE IN COMPUTER SCIENCE',
          institution: 'ASIAN INSTITUTE OF COMPUTER STUDIES',
          period: '2016-2018',
          location: 'General Mariano Alvarez, Philippines',
          coordinates: '14.3833° N, 120.9333° E',
          mapUrl: 'https://www.google.com/maps/search/?api=1&query=General+Mariano+Alvarez+Cavite'
        },
        {
          degree: 'BACHELOR OF SCIENCE IN COMPUTER SCIENCE',
          institution: 'ASIAN INSTITUTE OF COMPUTER STUDIES',
          period: '2018-2020',
          location: 'Upper Bicutan, Metro Manila, Philippines',
          coordinates: '14.4833° N, 121.0500° E',
          mapUrl: 'https://www.google.com/maps/search/?api=1&query=Upper+Bicutan+Taguig'
        }
      ]
    }
  },
  methods: {
    toggleMap(index) {
      if (this.activeMap === index) {
        this.activeMap = null;
      } else {
        this.activeMap = index;
      }
    }
  },
  mounted() {
    // Initialize scroll animations
    const observerOptions = {
      threshold: 0.1,
      rootMargin: '0px 0px -50px 0px'
    };
    
    const observer = new IntersectionObserver((entries) => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          entry.target.classList.add('visible');
          
          // Animate title line
          const titleLine = entry.target.querySelector('.title-line');
          if (titleLine) {
            titleLine.classList.add('active');
          }
          
          observer.unobserve(entry.target);
        }
      });
    }, observerOptions);
    
    document.querySelectorAll('.animate-on-scroll').forEach(el => observer.observe(el));
  }
}
</script>

<style scoped>
/* Enhanced glass effect without rounded corners */
.glass-card {
  background: linear-gradient(135deg, rgba(10, 10, 10, 0.7) 0%, rgba(26, 26, 26, 0.7) 100%);
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  border: 1px solid rgba(0, 212, 255, 0.2);
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.2);
  transition: all 0.5s cubic-bezier(0.175, 0.885, 0.32, 1.275);
  position: relative;
  overflow: hidden;
  padding: 1.5rem;
  border-radius: 0;
  transform-style: preserve-3d;
  transform: translateZ(0);
}
.glass-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(45deg, transparent, rgba(255, 255, 255, 0.1), transparent);
  transform: translateX(-100%);
  transition: transform 0.6s;
}
.glass-card:hover::before {
  transform: translateX(100%);
}
.glass-card:hover {
  background: linear-gradient(135deg, rgba(0, 212, 255, 0.1) 0%, rgba(255, 0, 255, 0.1) 100%);
  border-color: rgba(0, 212, 255, 0.4);
  box-shadow: 0 8px 30px rgba(0, 212, 255, 0.2);
  transform: translateY(-5px) translateZ(20px);
}

/* Glass reflection effect */
.glass-reflection {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(135deg, rgba(255, 255, 255, 0.1) 0%, transparent 50%);
  opacity: 0;
  transition: opacity 0.3s ease;
  pointer-events: none;
  border-radius: inherit;
}
.glass-hover:hover .glass-reflection {
  opacity: 1;
}

/* Map container styles */
.map-container {
  background: linear-gradient(135deg, rgba(10, 10, 10, 0.8) 0%, rgba(26, 26, 26, 0.8) 100%);
  backdrop-filter: blur(5px);
  -webkit-backdrop-filter: blur(5px);
  border: 1px solid rgba(0, 212, 255, 0.3);
  position: relative;
  overflow: hidden;
  border-radius: 0;
}

/* Education card specific styles */
.education-card {
  height: 100%;
  transition: all 0.5s cubic-bezier(0.175, 0.885, 0.32, 1.275);
}

/* Location text hover effect */
.location-text {
  transition: all 0.3s ease;
  cursor: pointer;
  position: relative;
  display: inline-flex;
  align-items: center;
}
.location-text::after {
  content: '';
  position: absolute;
  bottom: 0;
  left: 0;
  width: 0;
  height: 1px;
  background: linear-gradient(90deg, #00d4ff, #ff00ff);
  transition: width 0.3s ease;
}
.location-text:hover::after {
  width: 100%;
}

/* Enhanced button styles */
.map-button {
  display: inline-flex;
  align-items: center;
  padding: 0.5rem 1rem;
  background: linear-gradient(45deg, rgba(0, 212, 255, 0.1), rgba(255, 0, 255, 0.1));
  backdrop-filter: blur(5px);
  -webkit-backdrop-filter: blur(5px);
  border: 1px solid rgba(0, 212, 255, 0.3);
  color: #00d4ff;
  font-family: 'Orbitron', sans-serif;
  font-weight: 600;
  letter-spacing: 1px;
  text-transform: uppercase;
  font-size: 0.75rem;
  transition: all 0.3s ease;
  position: relative;
  overflow: hidden;
  border-radius: 0;
}
.map-button::before {
  content: '';
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(0, 212, 255, 0.3), transparent);
  transition: left 0.5s ease;
}
.map-button:hover::before {
  left: 100%;
}
.map-button:hover {
  background: linear-gradient(45deg, rgba(0, 212, 255, 0.2), rgba(255, 0, 255, 0.2));
  border-color: rgba(0, 212, 255, 0.5);
  color: white;
  transform: translateY(-2px);
  box-shadow: 0 5px 15px rgba(0, 212, 255, 0.3);
}

/* Scroll animations */
.animate-on-scroll {
  opacity: 0;
  transition: all 0.8s cubic-bezier(0.175, 0.885, 0.32, 1.275);
}
.fade-in-up {
  transform: translateY(30px);
}
.zoom-in {
  transform: scale(0.9);
}
.animate-on-scroll.visible {
  opacity: 1;
  transform: translate(0) scale(1);
}

/* Title line animation */
.title-line {
  width: 0;
  transition: width 1.5s cubic-bezier(0.175, 0.885, 0.32, 1.275);
}
.title-line.active {
  width: 6rem;
  box-shadow: 0 0 20px rgba(0, 212, 255, 0.6);
}

/* Floating elements */
.floating-element {
  animation: float 8s ease-in-out infinite;
}
@keyframes float {
  0%, 100% { transform: translateY(0) rotate(45deg); }
  50% { transform: translateY(-20px) rotate(45deg); }
}

/* Geometric shapes */
.geometric-shape {
  position: absolute;
  opacity: 0.1;
  pointer-events: none;
}
.shape-triangle {
  width: 60px;
  height: 60px;
  background: linear-gradient(135deg, #00d4ff, #ff00ff);
  clip-path: polygon(50% 0%, 0% 100%, 100% 100%);
}
.shape-hexagon {
  width: 60px;
  height: 60px;
  background: linear-gradient(135deg, #ff00ff, #00d4ff);
  clip-path: polygon(50% 0%, 100% 25%, 100% 75%, 50% 100%, 0% 75%, 0% 25%);
}

/* Responsive */
@media (max-width: 768px) {
  .education-card {
    padding: 1rem;
  }
  .map-container {
    height: 200px;
  }
}
</style>