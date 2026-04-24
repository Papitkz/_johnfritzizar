<template>
  <div class="gradient-bg">
    <!-- Background Music Player -->
    <audio ref="backgroundMusic" loop>
      <source :src="currentTrack" type="audio/mpeg">
      Your browser does not support the audio element.
    </audio>

    <!-- Music Toggle Button -->
    <button 
      @click="toggleMusic" 
      class="fixed bottom-24 right-8 glass-effect p-3 rounded-full shadow-lg hover:bg-cyan-400/20 transition-all duration-300 z-50 transform hover:scale-110 magnetic-btn"
      :class="{ 'pulse-glow': isMusicPlaying }"
      aria-label="Toggle music"
    >
      <svg v-if="isMusicPlaying" class="w-6 h-6 text-cyan-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 9v6m4-6v6m7-3a9 9 0 11-18 0 9 9 0 0118 0z" />
      </svg>
      <svg v-else class="w-6 h-6 text-cyan-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M14.752 11.168l-3.197-2.132A1 1 0 0010 9.87v4.263a1 1 0 001.555.832l3.197-2.132a1 1 0 000-1.664z" />
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
      </svg>
    </button> 

    <!-- Background Effects -->
    <div class="dynamic-bg"></div>
    <div class="animated-grid"></div>

    <!-- Binary Rain Effect -->
    <div class="binary-rain">
      <span v-for="i in 15" :key="i" class="binary-digit" :style="{
        left: `${Math.random() * 100}%`,
        top: `${Math.random() * 100}%`,
        animationDelay: `${Math.random() * 5}s`,
        animationDuration: `${5 + Math.random() * 10}s`
      }">{{ Math.random() > 0.5 ? '1' : '0' }}</span>
    </div>
    
    <!-- Circuit Board Effect -->
    <svg class="circuit-board" viewBox="0 0 1200 800" preserveAspectRatio="none">
      <path class="circuit-line" d="M0,400 Q300,200 600,400 T1200,400" stroke="rgba(0, 212, 255, 0.3)" stroke-width="1" fill="none" vector-effect="non-scaling-stroke"/>
      <path class="circuit-line" d="M0,600 Q300,400 600,600 T1200,600" stroke="rgba(255, 0, 255, 0.3)" stroke-width="1" fill="none" vector-effect="non-scaling-stroke"/>
      <circle class="circuit-node" cx="300" cy="300" r="4" fill="#00d4ff"/>
      <circle class="circuit-node" cx="600" cy="400" r="4" fill="#00d4ff"/>
      <circle class="circuit-node" cx="900" cy="500" r="4" fill="#ff00ff"/>
    </svg>
    
    <!-- Enhanced Loading Overlay -->
    <div class="loading-overlay" :class="{ 'hidden': !isLoading }">
      <div class="loader-container">
        <div class="loader">
          <div class="loader-circle"></div>
          <div class="loader-circle"></div>
          <div class="loader-circle"></div>
        </div>
        <div class="loader-text">LOADING PORTFOLIO</div>
      </div>
    </div>
    
    <!-- Page Transition -->
    <div class="page-transition" :class="{ 'active': isPageTransitioning }"></div>
    
    <!-- Enhanced Glass Navigation with Lenis -->
    <nav class="fixed w-full glass-nav z-50 transition-all duration-300" :class="{ 'shadow-lg': scrolled }">
      <div class="container mx-auto px-6 py-4">
        <div class="flex justify-between items-center">
          <div class="text-2xl font-bold text-cyan-400 flex items-center cursor-pointer magnetic-btn" @click="scrollToSection('#home')" style="font-family: 'Orbitron', sans-serif; letter-spacing: 2px;">
            <span class="glow-text">JFI</span>
            <span class="ml-2 text-xs px-2 py-1 bg-cyan-900/50 rounded-full text-cyan-300">FULL STACK</span>
          </div>
          
          <div class="hidden md:flex space-x-8">
            <a 
              v-for="(item, index) in navItems" 
              :key="item.name"
              :href="item.href" 
              class="nav-link nav-link-enhanced relative group" 
              :class="{ 'active': activeSection === item.section }"
              @click.prevent="handleNavClick($event, item.section, item.href)"
            >
              <span class="relative z-10">{{ item.name }}</span>
              <div class="absolute inset-0 bg-gradient-to-r from-cyan-500/10 to-purple-500/10 rounded-lg opacity-0 group-hover:opacity-100 transition-opacity duration-300"></div>
              <div class="absolute bottom-0 left-0 w-0 h-0.5 bg-gradient-to-r from-cyan-400 to-purple-500 transition-all duration-300 group-hover:w-full"></div>
            </a>
          </div>
          
          <button @click="toggleMobileMenu" class="md:hidden text-cyan-400 p-2 rounded-lg glass-effect magnetic-btn">
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path v-if="!mobileMenuOpen" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16m-7 6h7"></path>
              <path v-else stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
            </svg>
          </button>
        </div>
        
        <!-- Mobile Menu -->
        <div v-if="mobileMenuOpen" class="md:hidden mt-4 pb-4 glass-menu rounded-xl p-4">
          <a 
            v-for="item in navItems" 
            :key="item.name"
            :href="item.href" 
            class="block py-3 px-4 rounded-lg nav-link nav-link-enhanced relative mb-2" 
            :class="{ 'active': activeSection === item.section }"
            @click.prevent="handleNavClick($event, item.section, item.href)"
          >
            <span class="relative z-10">{{ item.name }}</span>
          </a>
        </div>
      </div>
    </nav>
    
    <!-- Main Content with Lenis Smooth Scroll -->
    <main id="lenis-main">
      <!-- Home Section -->
      <div class="section-container parallax-home" id="home-section">
        <div class="parallax-layer parallax-back">
          <div class="floating-orbs">
            <div v-for="i in 8" :key="`orb-${i}`" class="floating-orb" :style="{
              width: `${20 + Math.random() * 60}px`,
              height: `${20 + Math.random() * 60}px`,
              left: `${Math.random() * 100}%`,
              top: `${Math.random() * 100}%`,
              animationDelay: `${Math.random() * 5}s`,
              animationDuration: `${10 + Math.random() * 20}s`,
              opacity: 0.1 + Math.random() * 0.2
            }"></div>
          </div>
        </div>
        <div class="parallax-layer parallax-mid">
          <div class="geometric-shapes">
            <div v-for="i in 5" :key="`shape-${i}`" class="geometric-shape" :style="{
              left: `${Math.random() * 100}%`,
              top: `${Math.random() * 100}%`,
              animationDelay: `${Math.random() * 3}s`,
              animationDuration: `${15 + Math.random() * 15}s`,
              transform: `scale(${0.5 + Math.random()}) rotate(${Math.random() * 360}deg)`
            }"></div>
          </div>
        </div>
        <div class="parallax-layer parallax-front">
          <HomeSection :isMusicPlaying="isMusicPlaying" />
        </div>
      </div>
      
      <!-- About Section -->
      <div class="section-container parallax-about" id="about-section">
        <div class="parallax-layer parallax-back">
          <div class="skill-icons">
            <div v-for="i in 12" :key="`skill-${i}`" class="skill-icon" :style="{
              left: `${Math.random() * 100}%`,
              top: `${Math.random() * 100}%`,
              animationDelay: `${Math.random() * 5}s`,
              animationDuration: `${20 + Math.random() * 20}s`,
              fontSize: `${1 + Math.random() * 2}rem`
            }">
              <span v-if="i % 3 === 0">{</span>
              <span v-else-if="i % 3 === 1">}</span>
              <span v-else>/</span>
            </div>
          </div>
        </div>
        <div class="parallax-layer parallax-front">
          <AboutSection />
        </div>
      </div>
      
      <!-- Experience Section -->
      <div class="section-container parallax-experience" id="experience-section">
        <div class="parallax-layer parallax-back">
          <div class="timeline-particles">
            <div v-for="i in 15" :key="`particle-${i}`" class="timeline-particle" :style="{
              left: `${Math.random() * 100}%`,
              top: `${Math.random() * 100}%`,
              animationDelay: `${Math.random() * 5}s`,
              animationDuration: `${15 + Math.random() * 15}s`,
              width: `${2 + Math.random() * 4}px`,
              height: `${2 + Math.random() * 4}px`
            }"></div>
          </div>
        </div>
        <div class="parallax-layer parallax-mid">
          <div class="floating-nodes">
            <div v-for="i in 6" :key="`node-${i}`" class="floating-node" :style="{
              left: `${10 + Math.random() * 80}%`,
              top: `${10 + Math.random() * 80}%`,
              animationDelay: `${Math.random() * 5}s`,
              animationDuration: `${10 + Math.random() * 10}s`
            }">
              <div class="node-core"></div>
              <div class="node-pulse"></div>
            </div>
          </div>
        </div>
        <div class="parallax-layer parallax-front">
          <ExperienceSection />
        </div>
      </div>
      
      <!-- Education Section -->
      <div class="section-container parallax-education" id="education-section">
        <div class="parallax-layer parallax-back">
          <div class="academic-symbols">
            <div v-for="i in 10" :key="`symbol-${i}`" class="academic-symbol" :style="{
              left: `${Math.random() * 100}%`,
              top: `${Math.random() * 100}%`,
              animationDelay: `${Math.random() * 5}s`,
              animationDuration: `${20 + Math.random() * 20}s`,
              fontSize: `${1 + Math.random() * 1.5}rem`
            }">
              <span v-if="i % 4 === 0">+</span>
              <span v-else-if="i % 4 === 1">∑</span>
              <span v-else-if="i % 4 === 2">∫</span>
              <span v-else>π</span>
            </div>
          </div>
        </div>
        <div class="parallax-layer parallax-front">
          <EducationSection />
        </div>
      </div>
      
      <!-- Projects Section -->
      <div class="section-container parallax-projects" id="projects-section">
        <div class="parallax-layer parallax-back">
          <div class="tech-grid">
            <div v-for="i in 20" :key="`tech-${i}`" class="tech-grid-item" :style="{
              left: `${Math.random() * 100}%`,
              top: `${Math.random() * 100}%`,
              animationDelay: `${Math.random() * 5}s`,
              animationDuration: `${15 + Math.random() * 15}s`,
              width: `${10 + Math.random() * 30}px`,
              height: `${10 + Math.random() * 30}px`
            }"></div>
          </div>
        </div>
        <div class="parallax-layer parallax-mid">
          <div class="floating-code">
            <div v-for="i in 8" :key="`code-${i}`" class="code-snippet" :style="{
              left: `${Math.random() * 100}%`,
              top: `${Math.random() * 100}%`,
              animationDelay: `${Math.random() * 3}s`,
              animationDuration: `${10 + Math.random() * 10}s`,
              transform: `rotate(${Math.random() * 20 - 10}deg)`
            }">
              <div class="code-line" v-for="j in 3" :key="`line-${j}`"></div>
            </div>
          </div>
        </div>
        <div class="parallax-layer parallax-front">
          <ProjectsSection />
        </div>
      </div>
      
      <!-- Contact Section -->
      <div class="section-container parallax-contact" id="contact-section">
        <div class="parallax-layer parallax-back">
          <div class="connection-lines">
            <svg width="100%" height="100%" class="absolute inset-0">
              <line v-for="i in 10" :key="`line-${i}`" class="connection-line" :x1="`${Math.random() * 100}%`" :y1="`${Math.random() * 100}%`" :x2="`${Math.random() * 100}%`" :y2="`${Math.random() * 100}%`" />
            </svg>
          </div>
        </div>
        <div class="parallax-layer parallax-mid">
          <div class="message-bubbles">
            <div v-for="i in 8" :key="`bubble-${i}`" class="message-bubble" :style="{
              left: `${Math.random() * 100}%`,
              top: `${Math.random() * 100}%`,
              animationDelay: `${Math.random() * 5}s`,
              animationDuration: `${15 + Math.random() * 15}s`,
              width: `${20 + Math.random() * 40}px`,
              height: `${20 + Math.random() * 40}px`
            }"></div>
          </div>
        </div>
        <div class="parallax-layer parallax-front">
          <ContactSection />
        </div>
      </div>
    </main>
    
    <!-- Enhanced Footer -->
    <footer class="glass-footer border-t border-cyan-900/30 py-8">
      <div class="container mx-auto px-6">
        <div class="flex flex-col items-center">
          <!-- Social Media Icons -->
          <div class="flex space-x-6 mb-6">
            <a href="https://t.me" target="_blank" class="social-icon social-icon-enhanced glass-effect p-3 rounded-full magnetic-btn">
              <svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24">
                <path d="M12 0C5.373 0 0 5.373 0 12s5.373 12 12 12 12-5.373 12-12S18.627 0 12 0zm5.568 8.16l-1.696 7.99c-.126.572-.463.712-.935.442l-2.584-1.904-1.247 1.2c-.138.138-.254.254-.52.254l.184-2.65 4.788-4.33c.208-.184-.048-.288-.322-.104l-5.914 3.72-2.55-.796c-.554-.176-.564-.554-.116-.82l9.968-3.84c.464-.176.87.103.72.82z"/>
              </svg>
            </a>
            <a href="https://instagram.com" target="_blank" class="social-icon social-icon-enhanced glass-effect p-3 rounded-full magnetic-btn">
              <svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24">
                <path d="M12 2.163c3.204 0 3.584.012 4.85.07 3.252.148 4.771 1.691 4.919 4.919.058 1.265.069 1.645.069 4.849 0 3.205-.012 3.584-.069 4.849-.149 3.225-1.664 4.771-4.919 4.919-1.266.058-1.644.07-4.85.07-3.204 0-3.584-.012-4.849-.07-4.358-.2-6.78-2.618-6.98-6.98-.059-1.281-.073-1.689-.073-4.849 0-3.204.013-3.583.07-4.849.2-4.358 2.618-6.78 6.98-6.98 1.281-.059 1.69-.073 4.949-.073zm0-2.163c-3.259 0-3.667.014-4.947.072-4.358.2-6.78 2.618-6.98 6.98-.059 1.281-.073 1.689-.073 4.948 0 3.259.014 3.668.072 4.948.2 4.358 2.618 6.78 6.98 6.98 1.281.058 1.689.072 4.948.072 3.259 0 3.668-.014 4.948-.072 4.354-.2 6.782-2.618 6.979-6.98.059-1.28.073-1.689.073-4.948 0-3.259-.014-3.667-.072-4.947-.196-4.354-2.617-6.78-6.979-6.98-1.281-.059-1.69-.073-4.949-.073zM5.838 12a6.162 6.162 0 1112.324 0 6.162 6.162 0 01-12.324 0zM12 16a4 4 0 110-8 4 4 0 010 8zm4.965-10.405a1.44 1.44 0 112.881.001 1.44 1.44 0 01-2.881-.001z"/>
              </svg>
            </a>
            <a href="https://facebook.com" target="_blank" class="social-icon social-icon-enhanced glass-effect p-3 rounded-full magnetic-btn">
              <svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24">
                <path d="M24 12.073c0-6.627-5.373-12-12-12s-12 5.373-12 12c0 5.99 4.388 10.954 10.125 11.854v-8.385H7.078v-3.47h3.047V9.43c0-3.007 1.792-4.669 4.533-4.669 1.312 0 2.686.235 2.686.235v2.953H15.83c-1.491 0-1.956.925-1.956 1.874v2.25h3.328l-.532 3.47h-2.796v8.385C19.612 23.027 24 18.062 24 12.073z"/>
              </svg>
            </a>
            <a href="https://twitter.com" target="_blank" class="social-icon social-icon-enhanced glass-effect p-3 rounded-full magnetic-btn">
              <svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24">
                <path d="M23.953 4.57a10 10 0 01-2.825.775 4.958 4.958 0 002.163-2.723c-.951.555-2.005.959-3.127 1.184a4.92 4.92 0 00-8.384 4.482C7.69 8.095 4.067 6.13 1.64 3.162a4.822 4.822 0 00-.666 2.475c0 1.71.87 3.213 2.188 4.096a4.904 4.904 0 01-2.228-.616v.06a4.923 4.923 0 003.946 4.827 4.996 4.996 0 01-2.212.085 4.936 4.936 0 004.604 3.417 9.867 9.867 0 01-6.102 2.105c-.39 0-.779-.023-1.17-.067a13.995 13.995 0 007.557 2.209c9.053 0 13.998-7.496 13.998-13.985 0-.21 0-.42-.015-.63A9.935 9.935 0 0024 4.59z"/>
              </svg>
            </a>
            <a href="https://whatsapp.me" target="_blank" class="social-icon social-icon-enhanced glass-effect p-3 rounded-full magnetic-btn">
              <svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24">
                <path d="M17.472 14.382c-.297-.149-1.758-.867-2.03-.967-.273-.099-.471-.148-.67.15-.197.297-.767.966-.94 1.164-.173.199-.347.223-.644.075-.297-.15-1.255-.463-2.39-1.475-.883-.788-1.48-1.761-1.653-2.059-.173-.297-.018-.458.13-.606.134-.133.298-.347.446-.52.149-.174.198-.298.347-.497.298-.248-.05-.517-.075-.824-.075-.307 0-.8.115-1.22.573-.42.459-1.6 1.566-1.6 3.816 0 2.25 1.64 4.425 1.869 4.734.229.309 3.219 4.917 7.799 6.89.975.421 1.737.672 2.331.86.98.311 1.872.267 2.576.162.787-.116 2.42-.99 2.76-1.946.34-.956.34-1.775.238-1.946-.102-.171-.372-.27-.782-.472z"/>
              </svg>
            </a>
          </div>
          
          <p class="text-gray-400 text-sm">&copy; {{ new Date().getFullYear() }} John Fritz Izar. All rights reserved</p>
        </div>
      </div>
    </footer>
    
    <!-- Straight Marquee at Bottom -->
    <div class="marquee-container glass-marquee">
      <div class="marquee">
        <div class="marquee-content">
          <div class="marquee-item">
            <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
              <path d="M13 6a3 3 0 11-6 0 3 3 0 016 0zM18 8a2 2 0 11-4 0 2 2 0 014 0zM14 15a4 4 0 00-8 0v3h8v-3zM6 8a2 2 0 11-4 0 2 2 0 014 0zM16 18v-3a5.972 5.972 0 00-.75-2.906A3.005 3.005 0 0119 15v3h-3zM4.75 12.094A5.973 5.973 0 004 15v3H1v-3a3 3 0 013.75-2.906z" />
            </svg>
            FULL STACK DEVELOPER
          </div>
          <div class="marquee-item">
            <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
              <path fill-rule="evenodd" d="M12.316 3.051a1 1 0 10-1.932.518l.259.966a1 1 0 001.932-.518l-.26-.966zM2.429 4.74a1 1 0 10-.517 1.932l.966.259a1 1 0 00.517-1.932l-.966-.26zm8.814-.569a1 1 0 00-1.415-1.414l-.707.707a1 1 0 101.415 1.415l.707-.708zm-7.071 7.072l.707-.707A1 1 0 003.465 9.12l-.708.707a1 1 0 101.415 1.415zm3.2-5.171a1 1 0 00-1.3 1.3l4 10a1 1 0 001.823.075l1.38-2.759 3.018 3.02a1 1 0 001.414-1.415l-3.019-3.02 2.76-1.379a1 1 0 00-.076-1.822l-10-4z" clip-rule="evenodd" />
            </svg>
            VUE.JS | NODE.JS | EXPRESS.JS
          </div>
          <div class="marquee-item">
            <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
              <path fill-rule="evenodd" d="M6.672 1.911a1 1 0 10-1.932.518l.259.966a1 1 0 001.932-.518l-.26-.966zM2.429 4.74a1 1 0 10-.517 1.932l.966.259a1 1 0 00.517-1.932l-.966-.26zm8.814-.569a1 1 0 00-1.415-1.414l-.707.707a1 1 0 101.415 1.415l.707-.708zm-7.071 7.072l.707-.707A1 1 0 003.465 9.12l-.708.707a1 1 0 101.415 1.415zm3.2-5.171a1 1 0 00-1.3 1.3l4 10a1 1 0 001.823.075l1.38-2.759 3.018 3.02a1 1 0 001.414-1.415l-3.019-3.02 2.76-1.379a1 1 0 00-.076-1.822l-10-4z" clip-rule="evenodd" />
            </svg>
            SQL | MONGODB | TAILWIND CSS
          </div>
          <div class="marquee-item">
            <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
              <path fill-rule="evenodd" d="M11.3 1.046A1 1 0 0112 2v5h4a1 1 0 01.82 1.573l-7 10A1 1 0 018 18v-5H4a1 1 0 01-.82-1.573l7-10a1 1 0 011.12-.38z" clip-rule="evenodd" />
            </svg>
            API INTEGRATION | RESTful APIS
          </div>
          <!-- Duplicate for seamless loop -->
          <div class="marquee-item">
            <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
              <path d="M13 6a3 3 0 11-6 0 3 3 0 016 0zM18 8a2 2 0 11-4 0 2 2 0 014 0zM14 15a4 4 0 00-8 0v3h8v-3zM6 8a2 2 0 11-4 0 2 2 0 014 0zM16 18v-3a5.972 5.972 0 00-.75-2.906A3.005 3.005 0 0119 15v3h-3zM4.75 12.094A5.973 5.973 0 004 15v3H1v-3a3 3 0 013.75-2.906z" />
            </svg>
            FULL STACK DEVELOPER
          </div>
          <div class="marquee-item">
            <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
              <path fill-rule="evenodd" d="M12.316 3.051a1 1 0 10-1.932.518l.259.966a1 1 0 001.932-.518l-.26-.966zM2.429 4.74a1 1 0 10-.517 1.932l.966.259a1 1 0 00.517-1.932l-.966-.26zm8.814-.569a1 1 0 00-1.415-1.414l-.707.707a1 1 0 101.415 1.415l.707-.708zm-7.071 7.072l.707-.707A1 1 0 003.465 9.12l-.708.707a1 1 0 101.415 1.415zm3.2-5.171a1 1 0 00-1.3 1.3l4 10a1 1 0 001.823.075l1.38-2.759 3.018 3.02a1 1 0 001.414-1.415l-3.019-3.02 2.76-1.379a1 1 0 00-.076-1.822l-10-4z" clip-rule="evenodd" />
            </svg>
            VUE.JS | NODE.JS | EXPRESS.JS
          </div>
          <div class="marquee-item">
            <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
              <path fill-rule="evenodd" d="M6.672 1.911a1 1 0 10-1.932.518l.259.966a1 1 0 001.932-.518l-.26-.966zM2.429 4.74a1 1 0 10-.517 1.932l.966.259a1 1 0 00.517-1.932l-.966-.26zm8.814-.569a1 1 0 00-1.415-1.414l-.707.707a1 1 0 101.415 1.415l.707-.708zm-7.071 7.072l.707-.707A1 1 0 003.465 9.12l-.708.707a1 1 0 101.415 1.415zm3.2-5.171a1 1 0 00-1.3 1.3l4 10a1 1 0 001.823.075l1.38-2.759 3.018 3.02a1 1 0 001.414-1.415l-3.019-3.02 2.76-1.379a1 1 0 00-.076-1.822l-10-4z" clip-rule="evenodd" />
            </svg>
            SQL | MONGODB | TAILWIND CSS
          </div>
          <div class="marquee-item">
            <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
              <path fill-rule="evenodd" d="M11.3 1.046A1 1 0 0112 2v5h4a1 1 0 01.82 1.573l-7 10A1 1 0 018 18v-5H4a1 1 0 01-.82-1.573l7-10a1 1 0 011.12-.38z" clip-rule="evenodd" />
            </svg>
            API INTEGRATION | RESTful APIS
          </div>
        </div>
      </div>
    </div>
    
    <!-- Back to Top Button -->
    <button 
      v-show="showBackToTop"
      @click="scrollToTop" 
      class="fixed bottom-8 right-8 glass-effect p-3 rounded-full shadow-lg hover:bg-cyan-400/20 transition-all duration-300 z-50 transform hover:scale-110 magnetic-btn"
      aria-label="Back to top"
    >
      <svg class="w-6 h-6 text-cyan-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 15l7-7 7 7"></path>
      </svg>
    </button>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, provide } from 'vue';
import Lenis from 'lenis';

import HomeSection from './components/HomeSection.vue';
import AboutSection from './components/AboutSection.vue';
import ExperienceSection from './components/ExperienceSection.vue';
import EducationSection from './components/EducationSection.vue';
import ProjectsSection from './components/ProjectsSection.vue';
import ContactSection from './components/ContactSection.vue';

const mobileMenuOpen = ref(false);
const scrolled = ref(false);
const showBackToTop = ref(false);
const isLoading = ref(true);
const isPageTransitioning = ref(false);
const activeSection = ref('home');
const backgroundMusic = ref(null);
const isMusicPlaying = ref(false);
const currentTrack = ref('');

// Lenis instance
let lenis = null;

// Provide lenis to child components
provide('lenis', {
  get instance() { return lenis; },
  scrollTo: (target, options) => lenis?.scrollTo(target, options)
});

// Music tracks
const musicTracks = [
  new URL('./music/F1.mp3', import.meta.url).href,
  new URL('./music/F2.mp3', import.meta.url).href,
  new URL('./music/F3.mp3', import.meta.url).href,
  new URL('./music/F4.mp3', import.meta.url).href,
  new URL('./music/F5.mp3', import.meta.url).href
];

const navItems = [
  { name: 'Home', section: 'home', href: '#home' },
  { name: 'About', section: 'about', href: '#about' },
  { name: 'Experience', section: 'experience', href: '#experience' },
  { name: 'Education', section: 'education', href: '#education' },
  { name: 'Projects', section: 'projects', href: '#projects' },
  { name: 'Contact', section: 'contact', href: '#contact' }
];

const toggleMobileMenu = () => {
  mobileMenuOpen.value = !mobileMenuOpen.value;
};

const selectRandomTrack = () => {
  const randomIndex = Math.floor(Math.random() * musicTracks.length);
  return musicTracks[randomIndex];
};

const toggleMusic = () => {
  if (!backgroundMusic.value) return;
  
  if (isMusicPlaying.value) {
    backgroundMusic.value.pause();
    isMusicPlaying.value = false;
  } else {
    currentTrack.value = selectRandomTrack();
    backgroundMusic.value.load();
    
    backgroundMusic.value.play().then(() => {
      isMusicPlaying.value = true;
    }).catch(error => {
      console.error("Playback failed:", error);
      isMusicPlaying.value = false;
    });
  }
};

const handleNavClick = (event, section, href) => {
  mobileMenuOpen.value = false;
  activeSection.value = section;
  isPageTransitioning.value = true;
  
  setTimeout(() => {
    scrollToSection(href);
    
    setTimeout(() => {
      isPageTransitioning.value = false;
    }, 400);
  }, 400);
};

const scrollToSection = (target) => {
  if (!lenis) return;
  
  const element = document.querySelector(target);
  if (element) {
    lenis.scrollTo(element, {
      offset: -80,
      duration: 1.5,
      easing: (t) => Math.min(1, 1.001 - Math.pow(2, -10 * t))
    });
  }
};

const scrollToTop = () => {
  isPageTransitioning.value = true;
  
  setTimeout(() => {
    if (lenis) {
      lenis.scrollTo(0, { duration: 1.5 });
    }
    
    setTimeout(() => {
      isPageTransitioning.value = false;
    }, 400);
  }, 400);
};

// Apply parallax effect using Lenis scroll
const applyParallaxEffect = (scroll) => {
  const parallaxBack = document.querySelectorAll('.parallax-back');
  const parallaxMid = document.querySelectorAll('.parallax-mid');
  
  parallaxBack.forEach(el => {
    el.style.transform = `translateY(${scroll * 0.2}px)`;
  });
  
  parallaxMid.forEach(el => {
    el.style.transform = `translateY(${scroll * 0.5}px)`;
  });
};

// Initialize magnetic buttons
const initMagneticButtons = () => {
  const buttons = document.querySelectorAll('.magnetic-btn');
  
  buttons.forEach(btn => {
    btn.addEventListener('mousemove', (e) => {
      const rect = btn.getBoundingClientRect();
      const x = e.clientX - rect.left - rect.width / 2;
      const y = e.clientY - rect.top - rect.height / 2;
      btn.style.transform = `translate(${x * 0.3}px, ${y * 0.3}px) scale(1.1)`;
    });
    
    btn.addEventListener('mouseleave', () => {
      btn.style.transform = 'translate(0, 0) scale(1)';
    });
  });
};

// Initialize scroll animations
const initScrollAnimations = () => {
  const animatedElements = document.querySelectorAll('.animate-on-scroll');
  
  const observer = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        entry.target.classList.add('visible');
        observer.unobserve(entry.target);
      }
    });
  }, {
    threshold: 0.1,
    rootMargin: '0px 0px -50px 0px'
  });
  
  animatedElements.forEach(el => observer.observe(el));
};

onMounted(() => {
  // Hide loading screen
  setTimeout(() => {
    isLoading.value = false;
  }, 1500);

  // Initialize Lenis smooth scrolling
  lenis = new Lenis({
    duration: 1.2,
    easing: (t) => Math.min(1, 1.001 - Math.pow(2, -10 * t)),
    orientation: 'vertical',
    gestureOrientation: 'vertical',
    smoothWheel: true,
    wheelMultiplier: 1,
    touchMultiplier: 2,
    infinite: false,
  });

  // Lenis scroll event
  lenis.on('scroll', ({ scroll, limit, velocity, direction, progress }) => {
    scrolled.value = scroll > 10;
    showBackToTop.value = scroll > 300;
    
    // Update active section
    const scrollPosition = scroll + 100;
    
    for (const item of navItems) {
      const element = document.getElementById(item.section);
      if (element) {
        const offsetTop = element.offsetTop;
        const offsetHeight = element.offsetHeight;
        
        if (scrollPosition >= offsetTop && scrollPosition < offsetTop + offsetHeight) {
          activeSection.value = item.section;
          break;
        }
      }
    }
    
    // Apply parallax
    applyParallaxEffect(scroll);
  });

  // RAF loop for Lenis
  function raf(time) {
    lenis.raf(time);
    requestAnimationFrame(raf);
  }
  requestAnimationFrame(raf);

  // Add lenis classes
  document.documentElement.classList.add('lenis', 'lenis-smooth');

  // Initialize other features
  initScrollAnimations();
  initMagneticButtons();
  
  // Initialize music
  if (backgroundMusic.value) {
    currentTrack.value = selectRandomTrack();
    backgroundMusic.value.volume = 0.3;
    
    backgroundMusic.value.play().then(() => {
      isMusicPlaying.value = true;
    }).catch(() => {
      // Autoplay prevented
    });
  }
});

onUnmounted(() => {
  if (lenis) {
    lenis.destroy();
    document.documentElement.classList.remove('lenis', 'lenis-smooth');
  }
  
  if (backgroundMusic.value && isMusicPlaying.value) {
    backgroundMusic.value.pause();
  }
});
</script>

<style>
/* Lenis CSS */
html {
  scroll-behavior: auto !important;
}

html.lenis {
  height: auto;
}

.lenis.lenis-smooth {
  scroll-behavior: auto !important;
}

.lenis.lenis-smooth [data-lenis-prevent] {
  overscroll-behavior: contain;
}

.lenis.lenis-stopped {
  overflow: hidden;
}

.lenis.lenis-scrolling iframe {
  pointer-events: none;
}

/* Loading Overlay */
.loading-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(135deg, #0a0a0a 0%, #1a1a1a 100%);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 9999;
  transition: opacity 0.8s, visibility 0.8s;
}

.loading-overlay.hidden {
  opacity: 0;
  visibility: hidden;
}

.loader-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.loader {
  position: relative;
  width: 120px;
  height: 120px;
  margin-bottom: 20px;
}

.loader-circle {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  border-radius: 50%;
  border: 3px solid transparent;
  border-top-color: #00d4ff;
  animation: spin 1.5s cubic-bezier(0.68, -0.55, 0.265, 1.55) infinite;
}

.loader-circle:nth-child(2) {
  width: 80%;
  height: 80%;
  top: 10%;
  left: 10%;
  border-top-color: #ff00ff;
  animation-delay: 0.2s;
}

.loader-circle:nth-child(3) {
  width: 60%;
  height: 60%;
  top: 20%;
  left: 20%;
  border-top-color: #00ff88;
  animation-delay: 0.4s;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

.loader-text {
  font-family: 'Orbitron', sans-serif;
  font-size: 14px;
  letter-spacing: 2px;
  color: #00d4ff;
  animation: pulse 1.5s infinite;
}

@keyframes pulse {
  0%, 100% { opacity: 0.4; }
  50% { opacity: 1; }
}

/* Glass Effects */
.glass-nav {
  background: linear-gradient(135deg, rgba(10, 10, 10, 0.7) 0%, rgba(26, 26, 26, 0.7) 100%);
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  border-bottom: 1px solid rgba(0, 212, 255, 0.1);
  box-shadow: 0 4px 30px rgba(0, 0, 0, 0.1);
}

.glass-menu {
  background: linear-gradient(135deg, rgba(10, 10, 10, 0.8) 0%, rgba(26, 26, 26, 0.8) 100%);
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  border: 1px solid rgba(0, 212, 255, 0.2);
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.2);
}

.glass-footer {
  background: linear-gradient(135deg, rgba(10, 10, 10, 0.7) 0%, rgba(26, 26, 26, 0.7) 100%);
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  border-top: 1px solid rgba(0, 212, 255, 0.2);
}

.glass-marquee {
  background: linear-gradient(135deg, rgba(10, 10, 10, 0.8) 0%, rgba(26, 26, 26, 0.8) 100%);
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  border-top: 1px solid rgba(0, 212, 255, 0.3);
}

.glass-effect {
  background: linear-gradient(135deg, rgba(0, 212, 255, 0.05) 0%, rgba(255, 0, 255, 0.05) 100%);
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  border: 1px solid rgba(0, 212, 255, 0.2);
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
  transition: all 0.3s ease;
  position: relative;
  overflow: hidden;
}

.glass-effect::before {
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

.glass-effect:hover::before {
  transform: translateX(100%);
}

.glass-effect:hover {
  background: linear-gradient(135deg, rgba(0, 212, 255, 0.1) 0%, rgba(255, 0, 255, 0.1) 100%);
  border-color: rgba(0, 212, 255, 0.4);
  box-shadow: 0 8px 30px rgba(0, 212, 255, 0.2);
  transform: translateY(-2px);
}

/* Navigation */
.nav-link {
  position: relative;
  overflow: hidden;
  transition: all 0.3s ease;
  padding: 0.5rem 0;
  font-family: 'Orbitron', sans-serif;
  letter-spacing: 1px;
  color: #a0a0a0;
  text-decoration: none;
}

.nav-link::after {
  content: '';
  position: absolute;
  bottom: 0;
  left: 0;
  width: 0;
  height: 2px;
  background: linear-gradient(90deg, #00d4ff, #ff00ff);
  transition: width 0.3s ease;
}

.nav-link:hover::after,
.nav-link.active::after {
  width: 100%;
}

.nav-link.active {
  color: #00d4ff;
  text-shadow: 0 0 10px rgba(0, 212, 255, 0.5);
}

/* Glow Text */
.glow-text {
  text-shadow: 0 0 10px rgba(0, 212, 255, 0.7);
}

/* Social Icons */
.social-icon {
  color: #a0a0a0;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
}

.social-icon:hover {
  color: #00d4ff;
  transform: scale(1.25);
}

/* Pulse Glow */
.pulse-glow {
  animation: pulse-glow 2s infinite;
}

@keyframes pulse-glow {
  0%, 100% { box-shadow: 0 0 5px rgba(0, 212, 255, 0.5); }
  50% { box-shadow: 0 0 20px rgba(0, 212, 255, 0.8), 0 0 30px rgba(0, 212, 255, 0.6); }
}

/* Magnetic Button */
.magnetic-btn {
  transition: transform 0.3s cubic-bezier(0.23, 1, 0.32, 1);
}

/* Page Transition */
.page-transition {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: #0a0a0a;
  z-index: 9998;
  transform: translateY(100%);
  transition: transform 0.5s cubic-bezier(0.76, 0, 0.24, 1);
}

.page-transition.active {
  transform: translateY(0);
}

/* Marquee */
.marquee-container {
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;
  overflow: hidden;
  z-index: 50;
}

.marquee {
  display: flex;
  animation: marquee 20s linear infinite;
  white-space: nowrap;
}

.marquee-content {
  display: flex;
  padding: 10px 0;
}

.marquee-item {
  display: flex;
  align-items: center;
  margin-right: 50px;
  color: #00d4ff;
  font-family: 'Orbitron', sans-serif;
  font-weight: 600;
  letter-spacing: 1px;
}

.marquee-item svg {
  margin-right: 8px;
}

@keyframes marquee {
  0% { transform: translateX(0); }
  100% { transform: translateX(-50%); }
}

/* Parallax Sections */
.section-container {
  position: relative;
  min-height: 100vh;
  overflow: hidden;
}

.parallax-layer {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  will-change: transform;
}

.parallax-front {
  position: relative;
  z-index: 10;
}

/* Background Effects */
.dynamic-bg {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: -1;
  background: linear-gradient(135deg, #0a0a0a 0%, #1a1a1a 100%);
}

.animated-grid {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-image: 
    linear-gradient(rgba(0, 212, 255, 0.05) 1px, transparent 1px),
    linear-gradient(90deg, rgba(0, 212, 255, 0.05) 1px, transparent 1px);
  background-size: 50px 50px;
  z-index: -1;
  animation: grid-move 40s linear infinite;
}

@keyframes grid-move {
  0% { background-position: 0 0; }
  100% { background-position: 50px 50px; }
}

/* Binary Rain */
.binary-rain {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: -1;
  overflow: hidden;
}

.binary-digit {
  position: absolute;
  color: rgba(0, 212, 255, 0.3);
  font-family: 'Courier New', monospace;
  font-size: 14px;
  animation: binary-fall linear infinite;
  opacity: 0;
}

@keyframes binary-fall {
  0% {
    transform: translateY(-100px);
    opacity: 0;
  }
  10% { opacity: 0.4; }
  90% { opacity: 0.4; }
  100% {
    transform: translateY(100vh);
    opacity: 0;
  }
}

/* Circuit Board */
.circuit-board {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: -1;
  opacity: 0.1;
}

.circuit-line {
  stroke-dasharray: 5, 5;
  animation: circuit-dash 20s linear infinite;
}

@keyframes circuit-dash {
  to { stroke-dashoffset: -100; }
}

.circuit-node {
  animation: circuit-pulse 2s infinite alternate;
}

@keyframes circuit-pulse {
  from { opacity: 0.3; }
  to { opacity: 1; }
}

/* Floating Orbs */
.floating-orb {
  position: absolute;
  border-radius: 50%;
  background: radial-gradient(circle at center, rgba(0, 212, 255, 0.8), rgba(0, 212, 255, 0.2));
  filter: blur(2px);
  animation: floatOrb linear infinite;
}

@keyframes floatOrb {
  0% {
    transform: translateY(0) translateX(0);
  }
  25% {
    transform: translateY(-20px) translateX(10px);
  }
  50% {
    transform: translateY(0) translateX(20px);
  }
  75% {
    transform: translateY(20px) translateX(10px);
  }
  100% {
    transform: translateY(0) translateX(0);
  }
}

/* Geometric Shapes */
.geometric-shape {
  position: absolute;
  width: 40px;
  height: 40px;
  background: linear-gradient(45deg, rgba(255, 0, 255, 0.3), rgba(0, 212, 255, 0.3));
  clip-path: polygon(50% 0%, 0% 100%, 100% 100%);
  animation: rotate linear infinite;
}

@keyframes rotate {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

/* Skill Icons */
.skill-icon {
  position: absolute;
  color: rgba(0, 212, 255, 0.3);
  font-family: 'Courier New', monospace;
  animation: floatSkill linear infinite;
}

@keyframes floatSkill {
  0% {
    transform: translateY(0) rotate(0deg);
    opacity: 0.3;
  }
  50% {
    transform: translateY(-30px) rotate(10deg);
    opacity: 0.7;
  }
  100% {
    transform: translateY(0) rotate(0deg);
    opacity: 0.3;
  }
}

/* Timeline Particles */
.timeline-particle {
  position: absolute;
  background-color: rgba(0, 212, 255, 0.5);
  border-radius: 50%;
  animation: floatParticle linear infinite;
}

@keyframes floatParticle {
  0% {
    transform: translateY(0) scale(1);
    opacity: 0.5;
  }
  50% {
    transform: translateY(-40px) scale(1.5);
    opacity: 0.8;
  }
  100% {
    transform: translateY(0) scale(1);
    opacity: 0.5;
  }
}

/* Floating Nodes */
.floating-node {
  position: absolute;
  width: 60px;
  height: 60px;
  animation: floatNode linear infinite;
}

.node-core {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 10px;
  height: 10px;
  background-color: rgba(0, 212, 255, 0.8);
  border-radius: 50%;
  transform: translate(-50%, -50%);
}

.node-pulse {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 100%;
  height: 100%;
  border: 1px solid rgba(0, 212, 255, 0.5);
  border-radius: 50%;
  transform: translate(-50%, -50%);
  animation: pulseNode 2s infinite;
}

@keyframes floatNode {
  0% { transform: translateY(0) rotate(0deg); }
  25% { transform: translateY(-20px) rotate(5deg); }
  50% { transform: translateY(0) rotate(0deg); }
  75% { transform: translateY(20px) rotate(-5deg); }
  100% { transform: translateY(0) rotate(0deg); }
}

@keyframes pulseNode {
  0% {
    transform: translate(-50%, -50%) scale(0.5);
    opacity: 0.8;
  }
  100% {
    transform: translate(-50%, -50%) scale(1.5);
    opacity: 0;
  }
}

/* Academic Symbols */
.academic-symbol {
  position: absolute;
  color: rgba(0, 212, 255, 0.3);
  font-family: 'Times New Roman', serif;
  animation: floatSymbol linear infinite;
}

@keyframes floatSymbol {
  0% {
    transform: translateY(0) rotate(0deg);
    opacity: 0.3;
  }
  50% {
    transform: translateY(-30px) rotate(5deg);
    opacity: 0.7;
  }
  100% {
    transform: translateY(0) rotate(0deg);
    opacity: 0.3;
  }
}

/* Tech Grid */
.tech-grid-item {
  position: absolute;
  background-color: rgba(0, 212, 255, 0.2);
  border: 1px solid rgba(0, 212, 255, 0.4);
  animation: floatTech linear infinite;
}

@keyframes floatTech {
  0% {
    transform: translateY(0) rotate(0deg);
    opacity: 0.3;
  }
  50% {
    transform: translateY(-40px) rotate(10deg);
    opacity: 0.6;
  }
  100% {
    transform: translateY(0) rotate(0deg);
    opacity: 0.3;
  }
}

/* Code Snippets */
.code-snippet {
  position: absolute;
  width: 80px;
  height: 60px;
  background-color: rgba(10, 10, 10, 0.7);
  border: 1px solid rgba(0, 212, 255, 0.3);
  border-radius: 4px;
  padding: 5px;
  animation: floatCode linear infinite;
}

.code-line {
  height: 4px;
  background-color: rgba(0, 212, 255, 0.3);
  margin-bottom: 4px;
  border-radius: 2px;
}

.code-line:nth-child(1) { width: 100%; }
.code-line:nth-child(2) { width: 80%; }
.code-line:nth-child(3) { width: 60%; }

@keyframes floatCode {
  0% {
    transform: translateY(0) rotate(0deg);
    opacity: 0.4;
  }
  50% {
    transform: translateY(-30px) rotate(5deg);
    opacity: 0.7;
  }
  100% {
    transform: translateY(0) rotate(0deg);
    opacity: 0.4;
  }
}

/* Connection Lines */
.connection-line {
  stroke: rgba(0, 212, 255, 0.3);
  stroke-width: 1;
  stroke-dasharray: 5, 5;
  animation: dash linear infinite;
}

@keyframes dash {
  to { stroke-dashoffset: -10; }
}

/* Message Bubbles */
.message-bubble {
  position: absolute;
  background-color: rgba(0, 212, 255, 0.2);
  border: 1px solid rgba(0, 212, 255, 0.4);
  border-radius: 50%;
  animation: floatBubble linear infinite;
}

@keyframes floatBubble {
  0% {
    transform: translateY(0) scale(1);
    opacity: 0.3;
  }
  50% {
    transform: translateY(-40px) scale(1.2);
    opacity: 0.6;
  }
  100% {
    transform: translateY(0) scale(1);
    opacity: 0.3;
  }
}

/* Scroll Animations */
.animate-on-scroll {
  opacity: 0;
  transform: translateY(30px);
  transition: all 0.8s cubic-bezier(0.175, 0.885, 0.32, 1.275);
}

.animate-on-scroll.visible {
  opacity: 1;
  transform: translateY(0);
}

/* Responsive */
@media (max-width: 768px) {
  .marquee-item {
    font-size: 0.75rem;
    margin-right: 30px;
  }
  
  .loader {
    width: 80px;
    height: 80px;
  }
  
  .loader-text {
    font-size: 12px;
  }
}
</style>