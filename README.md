[Upl<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
  <title>Magic Hands: The Missing Number Challenge</title>

  <!-- Google Fonts: Fredoka (headings & numbers) + Nunito (friendly body text) -->
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=Fredoka:wght@500;600;700&family=Nunito:wght@600;700;800&display=swap" rel="stylesheet" />

  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>
  <script>
    tailwind.config = {
      theme: {
        extend: {
          fontFamily: {
            display: ['Fredoka', 'cursive', 'sans-serif'],
            body: ['Nunito', 'sans-serif'],
          },
          colors: {
            forest: {
              light: '#A8D5BA',
              mid: '#6BA368',
              sage: '#8FA998',
              deep: '#4F6D5A',
              dark: '#2F4838',
            },
            cream: {
              light: '#FDF8EE',
              warm: '#F4E8D1',
              border: '#DFC9A6',
            },
            wood: {
              light: '#8D6346',
              mid: '#6D4C3D',
              dark: '#4A3222',
            },
            starlight: {
              gold: '#FFD166',
              amber: '#F4A261',
            }
          }
        }
      }
    };
  </script>

  <!-- MediaPipe Hands & Camera Utils -->
  <script src="https://cdn.jsdelivr.net/npm/@mediapipe/hands/hands.js" crossorigin="anonymous"></script>
  <script src="https://cdn.jsdelivr.net/npm/@mediapipe/camera_utils/camera_utils.js" crossorigin="anonymous"></script>

  <style>
    * {
      box-sizing: border-box;
      user-select: none;
      -webkit-user-select: none;
    }

    html, body {
      margin: 0;
      padding: 0;
      width: 100%;
      height: 100%;
      overflow: hidden;
      font-family: 'Nunito', sans-serif;
      background-color: #2F4838;
      color: #4A3222;
    }

    .font-display {
      font-family: 'Fredoka', sans-serif;
    }

    /* Storybook Wooden Plaque Styling */
    .wood-plaque {
      background: linear-gradient(180deg, #8D6346 0%, #6D4C3D 55%, #5A3D30 100%);
      border: 4px solid #4A3222;
      box-shadow:
        0 8px 0 #3A2619,
        0 14px 24px rgba(26, 40, 29, 0.45),
        inset 0 3px 0 rgba(255, 224, 185, 0.28);
    }

    .parchment-card {
      background: linear-gradient(180deg, #FDF8EE 0%, #F4E8D1 100%);
      border: 4px solid #8D6346;
      box-shadow:
        0 8px 0 #5A3D30,
        0 16px 32px rgba(25, 42, 30, 0.35),
        inset 0 2px 0 #FFFFFF;
    }

    /* Chunky Storybook Buttons */
    .btn-storybook {
      transition: transform 0.14s cubic-bezier(0.34, 1.56, 0.64, 1), box-shadow 0.14s ease, filter 0.14s ease;
      cursor: pointer;
    }

    .btn-storybook:hover {
      transform: translateY(-2px) scale(1.02);
      filter: brightness(1.04);
    }

    .btn-storybook:active {
      transform: translateY(3px) scale(0.98);
    }

    .btn-green {
      background: linear-gradient(180deg, #7CC479 0%, #579654 100%);
      border: 3px solid #386636;
      box-shadow: 0 5px 0 #386636, 0 10px 18px rgba(0, 0, 0, 0.25), inset 0 2px 0 rgba(255, 255, 255, 0.45);
      color: #FFFFFF;
      text-shadow: 0 2px 0 #386636;
    }

    .btn-green:active {
      box-shadow: 0 2px 0 #386636, 0 4px 8px rgba(0, 0, 0, 0.25);
    }

    .btn-gold {
      background: linear-gradient(180deg, #FFE082 0%, #F4A261 100%);
      border: 3px solid #8D5524;
      box-shadow: 0 5px 0 #8D5524, 0 10px 18px rgba(0, 0, 0, 0.25), inset 0 2px 0 rgba(255, 255, 255, 0.6);
      color: #4A3222;
    }

    .btn-gold:active {
      box-shadow: 0 2px 0 #8D5524, 0 4px 8px rgba(0, 0, 0, 0.25);
    }

    .btn-cream {
      background: linear-gradient(180deg, #FDF8EE 0%, #EAD9BC 100%);
      border: 3px solid #6D4C3D;
      box-shadow: 0 5px 0 #4A3222, 0 8px 14px rgba(0, 0, 0, 0.2), inset 0 2px 0 #FFFFFF;
      color: #4A3222;
    }

    .btn-cream:active {
      box-shadow: 0 2px 0 #4A3222, 0 4px 8px rgba(0, 0, 0, 0.2);
    }

    /* Gentle Floating Animation for UI Elements */
    @keyframes floatSlow {
      0%, 100% { transform: translateY(0px); }
      50% { transform: translateY(-6px); }
    }

    .animate-float {
      animation: floatSlow 3.4s ease-in-out infinite;
    }

    @keyframes popIn {
      0% { opacity: 0; transform: scale(0.82) translateY(14px); }
      70% { opacity: 1; transform: scale(1.03) translateY(-2px); }
      100% { opacity: 1; transform: scale(1) translateY(0); }
    }

    .animate-pop-in {
      animation: popIn 0.36s cubic-bezier(0.34, 1.56, 0.64, 1) forwards;
    }

    @keyframes starPulse {
      0% { transform: scale(1); }
      40% { transform: scale(1.42) rotate(12deg); }
      70% { transform: scale(0.92) rotate(-6deg); }
      100% { transform: scale(1) rotate(0deg); }
    }

    .star-bump {
      animation: starPulse 0.55s cubic-bezier(0.34, 1.56, 0.64, 1);
    }

    /* Custom Scrollbar for Results Review */
    .storybook-scroll::-webkit-scrollbar {
      width: 8px;
    }
    .storybook-scroll::-webkit-scrollbar-track {
      background: #E9DAC1;
      border-radius: 999px;
    }
    .storybook-scroll::-webkit-scrollbar-thumb {
      background: #8D6346;
      border-radius: 999px;
    }
  </style>
</head>
<body class="relative w-screen h-screen overflow-hidden select-none">

  <!-- Hidden Webcam Source Video -->
  <video id="webcamVideo" playsinline autoplay muted class="hidden"></video>

  <!-- Main Interactive Storybook Canvas (Background + Portal + Orbs + Magic Hand + Particles) -->
  <canvas id="gameCanvas" class="fixed inset-0 w-full h-full block z-0 touch-none"></canvas>

  <!-- ==================== SCREEN 1: WELCOME SCREEN ==================== -->
  <div id="welcomeScreen" class="fixed inset-0 z-20 flex items-center justify-center p-4 bg-forest-dark/35 backdrop-blur-[2px]">
    <div class="parchment-card max-w-2xl w-full rounded-[32px] p-6 sm:p-8 text-center relative animate-pop-in">
      <!-- Decorative Corner Leaves & Stars -->
      <div class="absolute -top-6 left-1/2 -translate-x-1/2 flex items-center gap-2 bg-wood-mid border-4 border-wood-dark px-5 py-1.5 rounded-full shadow-md">
        <span class="text-starlight-gold text-lg">★</span>
        <span class="font-display text-cream-light text-xs sm:text-sm tracking-wide uppercase">Year 4 Mathematics Adventure</span>
        <span class="text-starlight-gold text-lg">★</span>
      </div>

      <!-- Storybook Emblem -->
      <div class="mt-3 mb-2 inline-flex items-center justify-center w-20 h-20 sm:w-24 sm:h-24 rounded-full bg-gradient-to-b from-forest-light to-forest-mid border-4 border-wood-mid shadow-lg animate-float">
        <svg viewBox="0 0 80 80" class="w-14 h-14 sm:w-16 sm:h-16">
          <defs>
            <radialGradient id="orbEmblem" cx="35%" cy="30%" r="65%">
              <stop offset="0%" stop-color="#FFF5CC" />
              <stop offset="45%" stop-color="#FFD166" />
              <stop offset="100%" stop-color="#F4A261" />
            </radialGradient>
          </defs>
          <circle cx="40" cy="40" r="28" fill="url(#orbEmblem)" stroke="#6D4C3D" stroke-width="3.5" />
          <text x="40" y="49" text-anchor="middle" font-family="Fredoka, sans-serif" font-weight="700" font-size="28" fill="#4A3222">?</text>
          <path d="M14 22 L17 28 L23 31 L17 34 L14 40 L11 34 L5 31 L11 28 Z" fill="#FFF8DC" />
          <path d="M64 16 L66 20 L70 22 L66 24 L64 28 L62 24 L58 22 L62 20 Z" fill="#FFF8DC" />
        </svg>
      </div>

      <h1 class="font-display text-3xl sm:text-5xl font-bold text-wood-dark leading-tight tracking-tight">
        Magic Hands:<br />
        <span class="text-forest-deep">The Missing Number Challenge</span>
      </h1>

      <p class="mt-3 text-base sm:text-lg font-bold text-wood-mid max-w-xl mx-auto leading-relaxed">
        Step into the Enchanted Forest! Use your hands to grab the missing number orbs and toss them into the <span class="text-forest-deep underline decoration-starlight-amber decoration-2">Enchanted Tree Hollow</span>.
      </p>

      <!-- Learning Standards Pill -->
      <div class="mt-4 inline-block bg-forest-light/45 border-2 border-forest-mid/60 rounded-2xl px-4 py-2 text-xs sm:text-sm font-extrabold text-forest-dark">
        Topic: Usage of Unknowns in Addition &amp; Subtraction (Standards 1.8.1 &amp; 1.8.2)
      </div>

      <!-- Primary Action Buttons -->
      <div class="mt-7 flex flex-col sm:flex-row items-center justify-center gap-4">
        <button id="btnGoToSetup" class="btn-storybook btn-green w-full sm:w-auto px-8 py-4 rounded-2xl font-display text-xl sm:text-2xl font-bold flex items-center justify-center gap-3">
          <span>🪄</span>
          <span>Start Adventure</span>
        </button>

        <button id="btnWelcomeMouse" class="btn-storybook btn-cream w-full sm:w-auto px-6 py-4 rounded-2xl font-display text-lg sm:text-xl font-bold flex items-center justify-center gap-2">
          <span>🖱️</span>
          <span>Play with Mouse / Touch</span>
        </button>
      </div>
    </div>
  </div>

  <!-- ==================== SCREEN 2: INSTRUCTION & WEBCAM SETUP ==================== -->
  <div id="setupScreen" class="fixed inset-0 z-20 hidden flex items-center justify-center p-3 sm:p-5 bg-forest-dark/45 backdrop-blur-[2px] overflow-y-auto">
    <div class="parchment-card max-w-4xl w-full rounded-[32px] p-5 sm:p-7 relative animate-pop-in my-auto">
      <div class="flex items-center justify-between gap-2 mb-3">
        <button id="btnBackToWelcome" class="btn-storybook btn-cream px-3.5 py-1.5 rounded-xl font-display text-sm font-bold">
          ← Back
        </button>
        <h2 class="font-display text-2xl sm:text-3xl font-bold text-wood-dark text-center flex-1">
          How to Cast Number Magic
        </h2>
        <div class="w-16"></div>
      </div>

      <!-- 3-Step Visual Hand Gesture Guide -->
      <div class="grid grid-cols-1 sm:grid-cols-3 gap-3 sm:gap-4 mt-3">
        <!-- Step 1 -->
        <div class="bg-cream-light border-3 border-cream-border rounded-2xl p-3.5 text-center shadow-sm">
          <div class="w-12 h-12 mx-auto rounded-full bg-forest-light/60 border-2 border-forest-mid flex items-center justify-center text-2xl mb-2">
            ✋
          </div>
          <div class="font-display text-sm uppercase tracking-wider text-forest-deep font-bold">Step 1</div>
          <h3 class="font-display text-lg font-bold text-wood-dark">Show Your Hand</h3>
          <p class="text-xs sm:text-sm font-bold text-wood-mid mt-1">
            Wave one hand in front of the webcam to guide your glowing fairy cursor.
          </p>
        </div>

        <!-- Step 2 -->
        <div class="bg-cream-light border-3 border-cream-border rounded-2xl p-3.5 text-center shadow-sm">
          <div class="w-12 h-12 mx-auto rounded-full bg-starlight-gold/50 border-2 border-starlight-amber flex items-center justify-center text-2xl mb-2">
            🤏
          </div>
          <div class="font-display text-sm uppercase tracking-wider text-wood-light font-bold">Step 2</div>
          <h3 class="font-display text-lg font-bold text-wood-dark">Pinch to Grab</h3>
          <p class="text-xs sm:text-sm font-bold text-wood-mid mt-1">
            Touch your <strong>thumb and index finger</strong> together over an answer orb to grab it!
          </p>
        </div>

        <!-- Step 3 -->
        <div class="bg-cream-light border-3 border-cream-border rounded-2xl p-3.5 text-center shadow-sm">
          <div class="w-12 h-12 mx-auto rounded-full bg-forest-mid/30 border-2 border-forest-deep flex items-center justify-center text-2xl mb-2">
            ✨
          </div>
          <div class="font-display text-sm uppercase tracking-wider text-forest-deep font-bold">Step 3</div>
          <h3 class="font-display text-lg font-bold text-wood-dark">Open to Throw!</h3>
          <p class="text-xs sm:text-sm font-bold text-wood-mid mt-1">
            Flick your hand toward the <strong>Enchanted Tree Hollow</strong> and open your fingers!
          </p>
        </div>
      </div>

      <!-- Webcam Activation & Live Gesture Test Area -->
      <div class="mt-4 bg-forest-light/30 border-3 border-forest-mid/50 rounded-2xl p-4 flex flex-col md:flex-row items-center gap-4">
        <!-- Mini Live Mirror Preview -->
        <div class="relative w-full md:w-64 h-40 rounded-2xl overflow-hidden bg-wood-dark border-4 border-wood-mid shadow-inner flex items-center justify-center shrink-0">
          <canvas id="setupPreviewCanvas" class="w-full h-full block"></canvas>
          <div id="setupPreviewPlaceholder" class="absolute inset-0 flex flex-col items-center justify-center text-center p-3 text-cream-light">
            <span class="text-3xl mb-1">📷</span>
            <span class="font-display text-sm font-semibold">Camera Preview</span>
            <span class="text-xs text-cream-warm/80 mt-0.5">Click "Activate Webcam" to test your hand!</span>
          </div>
          <div id="setupGestureBadge" class="hidden absolute bottom-2 left-2 right-2 bg-wood-dark/85 text-cream-light text-xs font-bold px-2.5 py-1 rounded-lg text-center border border-starlight-gold/50">
            Show hand to test pinch!
          </div>
        </div>

        <!-- Controls & Status Message -->
        <div class="flex-1 text-center md:text-left">
          <h4 class="font-display text-xl font-bold text-wood-dark">Webcam Hand-Tracking Setup</h4>
          <p id="webcamStatusText" class="text-sm font-bold text-wood-mid mt-1">
            Enable your webcam to play with magical hand gestures, or choose Mouse / Touch mode anytime.
          </p>

          <div class="mt-3 flex flex-wrap items-center justify-center md:justify-start gap-3">
            <button id="btnActivateWebcam" class="btn-storybook btn-gold px-5 py-2.5 rounded-xl font-display text-base sm:text-lg font-bold flex items-center gap-2">
              <span>📷</span>
              <span>Activate Webcam</span>
            </button>

            <button id="btnUseMouseSetup" class="btn-storybook btn-cream px-4 py-2.5 rounded-xl font-display text-sm sm:text-base font-bold flex items-center gap-2">
              <span>🖱️</span>
              <span>Use Mouse / Touch Instead</span>
            </button>
          </div>
        </div>
      </div>

      <!-- Start Quest Button -->
      <div class="mt-5 text-center">
        <button id="btnStartQuest" class="btn-storybook btn-green px-10 py-3.5 rounded-2xl font-display text-xl sm:text-2xl font-bold inline-flex items-center gap-3">
          <span>🌲</span>
          <span>Start Quest! (10 Questions)</span>
        </button>
      </div>
    </div>
  </div>

  <!-- ==================== SCREEN 3: MAIN GAMEPLAY HUD ==================== -->
  <div id="gameHud" class="fixed inset-0 z-10 hidden pointer-events-none flex flex-col justify-between p-2.5 sm:p-4">
    <!-- Top HUD Bar -->
    <div class="w-full max-w-6xl mx-auto flex flex-wrap items-center justify-between gap-2 pointer-events-auto">
      <!-- Question Progress Counter & 10 Pips -->
      <div class="wood-plaque rounded-2xl px-3.5 py-2 flex items-center gap-3">
        <div>
          <div id="questionCounterText" class="font-display text-base sm:text-lg font-bold text-cream-light leading-none">
            Question 1 / 10
          </div>
          <div id="progressPips" class="flex items-center gap-1 mt-1.5" aria-label="Question Progress">
            <!-- 10 pips populated by JS -->
          </div>
        </div>
      </div>

      <!-- Hand-Tracking Indicator + Utility Controls -->
      <div class="flex items-center gap-2 order-3 sm:order-2">
        <div id="trackingBadge" class="bg-cream-light/95 border-3 border-wood-mid rounded-full px-3.5 py-1.5 shadow-md flex items-center gap-2">
          <span id="trackingDot" class="w-3 h-3 rounded-full bg-amber-400 inline-block"></span>
          <span id="trackingLabel" class="font-display text-xs sm:text-sm font-bold text-wood-dark">Show Hand to Camera ✋</span>
        </div>

        <button id="btnToggleModeHud" title="Switch between Webcam and Mouse/Touch" class="btn-storybook btn-cream px-3 py-1.5 rounded-xl font-display text-xs sm:text-sm font-bold">
          🖱️ Mouse Mode
        </button>

        <button id="btnToggleSound" title="Mute or Unmute Sound Effects" class="btn-storybook btn-cream w-9 h-9 sm:w-10 sm:h-10 rounded-xl flex items-center justify-center text-base sm:text-lg">
          🔊
        </button>
      </div>

      <!-- Star Score Counter -->
      <div id="starCounterBox" class="wood-plaque rounded-2xl px-4 py-2 flex items-center gap-2 order-2 sm:order-3">
        <span id="hudStarIcon" class="text-2xl sm:text-3xl leading-none drop-shadow">⭐</span>
        <span id="starScoreText" class="font-display text-xl sm:text-2xl font-bold text-starlight-gold leading-none">
          0 / 10
        </span>
      </div>
    </div>

    <!-- Storybook Question Plaque (Positioned neatly below Top HUD) -->
    <div class="w-full max-w-xl mx-auto mt-1 sm:mt-2 pointer-events-none">
      <div class="parchment-card rounded-3xl px-5 py-2.5 sm:py-3.5 text-center relative">
        <div class="flex items-center justify-center gap-2 text-xs sm:text-sm font-extrabold uppercase tracking-wider text-forest-deep">
          <span id="questionTopicTag">Addition • Find the Unknown</span>
        </div>
        <div id="equationDisplay" class="font-display text-3xl sm:text-5xl font-bold text-wood-dark tracking-wide my-0.5">
          a + 24 = 61
        </div>
        <div id="questionPromptText" class="text-sm sm:text-base font-extrabold text-wood-mid">
          Find the value of <span class="text-forest-deep font-display text-lg">a</span> and throw it into the portal!
        </div>
      </div>
    </div>

    <!-- Spacer so middle canvas area stays open for Portal & Orbs -->
    <div class="flex-1"></div>

    <!-- Bottom Hint Bar & Picture-in-Picture Webcam Mirror -->
    <div class="w-full max-w-6xl mx-auto flex items-end justify-between gap-3">
      <!-- Helpful Student Tip Pill -->
      <div id="gameplayHintPill" class="bg-wood-dark/80 backdrop-blur-sm border-2 border-wood-light/70 text-cream-light rounded-2xl px-3.5 py-2 text-xs sm:text-sm font-bold shadow-lg max-w-xs sm:max-w-md pointer-events-none">
        🤏 Pinch thumb &amp; index finger on an orb, then open your hand to throw it into the tree hollow!
      </div>

      <!-- Compact Picture-in-Picture Webcam Mirror -->
      <div id="pipWebcamContainer" class="hidden pointer-events-auto relative w-36 h-28 sm:w-48 sm:h-36 rounded-2xl overflow-hidden wood-plaque p-1 shrink-0">
        <canvas id="pipCanvas" class="w-full h-full rounded-xl block bg-wood-dark"></canvas>
        <div id="pipGestureLabel" class="absolute bottom-2 left-2 right-2 bg-wood-dark/80 text-cream-light text-[10px] sm:text-xs font-bold py-0.5 px-2 rounded-md text-center truncate">
          Open Hand ✋
        </div>
      </div>
    </div>
  </div>

  <!-- ==================== FEEDBACK MODAL OVERLAY (AFTER EACH THROW) ==================== -->
  <div id="feedbackOverlay" class="fixed inset-0 z-20 hidden flex items-center justify-center p-4 bg-forest-dark/35 backdrop-blur-[2px]">
    <div id="feedbackCard" class="parchment-card max-w-lg w-full rounded-[32px] p-6 sm:p-7 text-center animate-pop-in">
      <div id="feedbackBadge" class="inline-flex items-center gap-2 px-4 py-1.5 rounded-full font-display text-base sm:text-lg font-bold mb-3 bg-starlight-gold/40 border-2 border-starlight-amber text-wood-dark">
        ⭐ +1 Star! Amazing!
      </div>

      <h3 id="feedbackHeading" class="font-display text-2xl sm:text-3xl font-bold text-wood-dark">
        Spot On!
      </h3>

      <p id="feedbackMessage" class="mt-2 text-base sm:text-lg font-bold text-wood-mid leading-relaxed">
        a = 37 because 37 + 24 = 61!
      </p>

      <!-- Auto-advance progress bar -->
      <div class="mt-5 w-full bg-cream-border/70 h-2.5 rounded-full overflow-hidden">
        <div id="feedbackTimerBar" class="bg-forest-mid h-full w-full transition-all duration-100 ease-linear"></div>
      </div>

      <div class="mt-4">
        <button id="btnNextQuestion" class="btn-storybook btn-green px-7 py-3 rounded-2xl font-display text-lg sm:text-xl font-bold inline-flex items-center gap-2">
          <span id="btnNextQuestionLabel">Next Question</span>
          <span>→</span>
        </button>
      </div>
    </div>
  </div>

  <!-- ==================== SCREEN 4: FINAL RESULTS SCREEN ==================== -->
  <div id="resultsScreen" class="fixed inset-0 z-30 hidden flex items-center justify-center p-3 sm:p-5 bg-forest-dark/50 backdrop-blur-[3px]">
    <div class="parchment-card max-w-2xl w-full max-h-[92vh] flex flex-col rounded-[32px] p-5 sm:p-7 text-center animate-pop-in">
      <div class="inline-block mx-auto bg-wood-mid text-starlight-gold font-display text-xs sm:text-sm uppercase tracking-wider px-4 py-1 rounded-full border-2 border-wood-dark">
        Quest Complete!
      </div>

      <h2 id="resultsTitle" class="font-display text-3xl sm:text-4xl font-bold text-wood-dark mt-2">
        Forest Math Wizard!
      </h2>

      <!-- Total Stars Earned Display -->
      <div class="mt-3 bg-wood-dark rounded-3xl p-4 border-4 border-wood-light shadow-inner">
        <div id="resultsStarsRow" class="flex flex-wrap items-center justify-center gap-1.5 text-2xl sm:text-3xl mb-1">
          <!-- 10 star icons generated by JS -->
        </div>
        <div id="resultsScoreText" class="font-display text-2xl sm:text-3xl font-bold text-starlight-gold">
          You earned 10 / 10 Stars!
        </div>
      </div>

      <p id="resultsEncouragement" class="mt-3 text-sm sm:text-base font-bold text-wood-mid">
        Incredible job solving all the missing numbers in the Enchanted Forest!
      </p>

      <!-- 10-Question Answer Review List -->
      <div class="mt-3 text-left flex-1 min-h-0 flex flex-col">
        <div class="font-display text-sm font-bold text-wood-dark mb-1.5 px-1">
          Your Adventure Scroll (10 Questions):
        </div>
        <div id="resultsReviewList" class="storybook-scroll overflow-y-auto flex-1 space-y-2 pr-1 max-h-48 sm:max-h-56">
          <!-- Populated dynamically -->
        </div>
      </div>

      <div class="mt-5">
        <button id="btnPlayAgain" class="btn-storybook btn-green px-9 py-3.5 rounded-2xl font-display text-xl sm:text-2xl font-bold inline-flex items-center gap-3">
          <span>🔄</span>
          <span>Play Again</span>
        </button>
      </div>
    </div>
  </div>

  <script>
    /**
     * ============================================================================
     * MAGIC HANDS: THE MISSING NUMBER CHALLENGE
     * Year 4 Mathematics (Learning Standards 1.8.1 & 1.8.2)
     * Single-file HTML5 Canvas + MediaPipe Hands + Web Audio API Educational Game
     * ============================================================================
     */

    // ----------------------------------------------------------------------------
    // 1. VERIFIED YEAR 4 MATHEMATICS QUESTION BANK (EXACT 10 QUESTIONS)
    // ----------------------------------------------------------------------------
    const QUESTIONS_BANK = [
      {
        id: 1,
        type: 'Addition',
        unknown: 'a',
        equation: 'a + 24 = 61',
        correct: 37,
        distractors: [47, 85],
        explanation: '37 + 24 = 61, so a = 37!'
      },
      {
        id: 2,
        type: 'Addition',
        unknown: 'b',
        equation: '45 + b = 83',
        correct: 38,
        distractors: [42, 28],
        explanation: '45 + 38 = 83, so b = 38!'
      },
      {
        id: 3,
        type: 'Addition',
        unknown: 'c',
        equation: 'c + 36 = 90',
        correct: 54,
        distractors: [64, 44],
        explanation: '54 + 36 = 90, so c = 54!'
      },
      {
        id: 4,
        type: 'Addition',
        unknown: 'd',
        equation: '29 + d = 75',
        correct: 46,
        distractors: [56, 36],
        explanation: '29 + 46 = 75, so d = 46!'
      },
      {
        id: 5,
        type: 'Addition',
        unknown: 'e',
        equation: 'e + 48 = 95',
        correct: 47,
        distractors: [53, 37],
        explanation: '47 + 48 = 95, so e = 47!'
      },
      {
        id: 6,
        type: 'Subtraction',
        unknown: 'a',
        equation: 'a - 25 = 43',
        correct: 68,
        distractors: [18, 58],
        explanation: '68 - 25 = 43, so a = 68!'
      },
      {
        id: 7,
        type: 'Subtraction',
        unknown: 'b',
        equation: '84 - b = 39',
        correct: 45,
        distractors: [55, 35],
        explanation: '84 - 45 = 39, so b = 45!'
      },
      {
        id: 8,
        type: 'Subtraction',
        unknown: 'c',
        equation: 'c - 34 = 57',
        correct: 91,
        distractors: [23, 81],
        explanation: '91 - 34 = 57, so c = 91!'
      },
      {
        id: 9,
        type: 'Subtraction',
        unknown: 'd',
        equation: '72 - d = 28',
        correct: 44,
        distractors: [54, 46],
        explanation: '72 - 44 = 28, so d = 44!'
      },
      {
        id: 10,
        type: 'Subtraction',
        unknown: 'e',
        equation: '95 - e = 38',
        correct: 57,
        distractors: [67, 47],
        explanation: '95 - 57 = 38, so e = 57!'
      }
    ];

    // Orb colour themes matching the soft green, sage, cream & golden forest palette
    const ORB_PALETTES = [
      { top: '#FFF3B0', mid: '#FFD166', bottom: '#E09F3E', rim: '#6D4C3D', glow: 'rgba(255, 209, 102, 0.55)' },
      { top: '#D8F3DC', mid: '#74C69D', bottom: '#40916C', rim: '#2D5A43', glow: 'rgba(116, 198, 157, 0.55)' },
      { top: '#FDE4CF', mid: '#F4A261', bottom: '#D97736', rim: '#6D4C3D', glow: 'rgba(244, 162, 97, 0.55)' }
    ];

    // ----------------------------------------------------------------------------
    // 2. SYNTHESIZED WEB AUDIO API SOUND ENGINE (NO EXTERNAL AUDIO FILES)
    // ----------------------------------------------------------------------------
    class SoundEngine {
      constructor() {
        this.ctx = null;
        this.muted = false;
      }

      unlock() {
        try {
          if (!this.ctx) {
            const AudioCtx = window.AudioContext || window.webkitAudioContext;
            if (AudioCtx) this.ctx = new AudioCtx();
          }
          if (this.ctx && this.ctx.state === 'suspended') {
            this.ctx.resume();
          }
        } catch (err) {
          // AudioContext blocked or unavailable; fail gracefully
        }
      }

      toggleMute() {
        this.muted = !this.muted;
        return this.muted;
      }

      playClick() {
        if (this.muted) return;
        this.unlock();
        if (!this.ctx) return;
        try {
          const now = this.ctx.currentTime;
          const osc = this.ctx.createOscillator();
          const gain = this.ctx.createGain();
          osc.type = 'triangle';
          osc.frequency.setValueAtTime(520, now);
          osc.frequency.exponentialRampToValueAtTime(680, now + 0.06);
          gain.gain.setValueAtTime(0.12, now);
          gain.gain.exponentialRampToValueAtTime(0.001, now + 0.07);
          osc.connect(gain);
          gain.connect(this.ctx.destination);
          osc.start(now);
          osc.stop(now + 0.075);
        } catch (e) {}
      }

      playGrab() {
        if (this.muted) return;
        this.unlock();
        if (!this.ctx) return;
        try {
          const now = this.ctx.currentTime;
          [587.33, 880].forEach((freq, i) => {
            const osc = this.ctx.createOscillator();
            const gain = this.ctx.createGain();
            osc.type = 'sine';
            osc.frequency.setValueAtTime(freq, now + i * 0.045);
            gain.gain.setValueAtTime(0.13, now + i * 0.045);
            gain.gain.exponentialRampToValueAtTime(0.001, now + i * 0.045 + 0.18);
            osc.connect(gain);
            gain.connect(this.ctx.destination);
            osc.start(now + i * 0.045);
            osc.stop(now + i * 0.045 + 0.19);
          });
        } catch (e) {}
      }

      playThrow() {
        if (this.muted) return;
        this.unlock();
        if (!this.ctx) return;
        try {
          const now = this.ctx.currentTime;
          const osc = this.ctx.createOscillator();
          const gain = this.ctx.createGain();
          osc.type = 'sine';
          osc.frequency.setValueAtTime(320, now);
          osc.frequency.exponentialRampToValueAtTime(760, now + 0.22);
          gain.gain.setValueAtTime(0.11, now);
          gain.gain.exponentialRampToValueAtTime(0.001, now + 0.24);
          osc.connect(gain);
          gain.connect(this.ctx.destination);
          osc.start(now);
          osc.stop(now + 0.25);
        } catch (e) {}
      }

      playSuccess() {
        if (this.muted) return;
        this.unlock();
        if (!this.ctx) return;
        try {
          const now = this.ctx.currentTime;
          const notes = [523.25, 659.25, 783.99, 987.77, 1046.50]; // C5 E5 G5 B5 C6
          notes.forEach((freq, idx) => {
            const osc = this.ctx.createOscillator();
            const gain = this.ctx.createGain();
            osc.type = idx === notes.length - 1 ? 'triangle' : 'sine';
            const t = now + idx * 0.075;
            osc.frequency.setValueAtTime(freq, t);
            gain.gain.setValueAtTime(0.16, t);
            gain.gain.exponentialRampToValueAtTime(0.001, t + 0.42);
            osc.connect(gain);
            gain.connect(this.ctx.destination);
            osc.start(t);
            osc.stop(t + 0.44);
          });
        } catch (e) {}
      }

      playMiss() {
        if (this.muted) return;
        this.unlock();
        if (!this.ctx) return;
        try {
          const now = this.ctx.currentTime;
          const notes = [392.00, 329.63]; // Gentle G4 -> E4 wooden marimba dip
          notes.forEach((freq, idx) => {
            const osc = this.ctx.createOscillator();
            const gain = this.ctx.createGain();
            osc.type = 'triangle';
            const t = now + idx * 0.13;
            osc.frequency.setValueAtTime(freq, t);
            gain.gain.setValueAtTime(0.14, t);
            gain.gain.exponentialRampToValueAtTime(0.001, t + 0.26);
            osc.connect(gain);
            gain.connect(this.ctx.destination);
            osc.start(t);
            osc.stop(t + 0.28);
          });
        } catch (e) {}
      }
    }

    const sound = new SoundEngine();

    // ----------------------------------------------------------------------------
    // 3. GAME STATE & CONFIGURATION
    // ----------------------------------------------------------------------------
    const state = {
      screen: 'welcome', // 'welcome' | 'setup' | 'playing' | 'feedback' | 'results'
      inputMode: 'camera', // 'camera' | 'mouse'
      webcamActive: false,
      webcamError: null,
      handDetected: false,
      isPinching: false,

      // Hand / Pointer Cursor in Canvas Logical Coordinates
      cursor: {
        x: 0,
        y: 0,
        targetX: 0,
        targetY: 0,
        visible: false,
        history: [] // { x, y, t } for throw velocity calculation
      },

      // Latest raw MediaPipe landmarks for PiP skeleton drawing
      latestLandmarks: null,

      // Quiz Progression
      currentIndex: 0,
      score: 0,
      resultsLog: [], // { question, chosen, isCorrect }
      questionLocked: false,

      // Interactive Entities on Main Canvas
      portal: {
        x: 0,
        y: 0,
        radius: 68,
        pulse: 0
      },
      orbs: [], // { value, isCorrect, palette, homeX, homeY, x, y, vx, vy, radius, bobPhase, state: 'idle'|'grabbed'|'thrown'|'returning', hovered }

      // Ambient Forest Decorations & Particles
      leaves: [],
      fireflies: [],
      particles: [],
      cursorTrail: [],

      // Feedback auto-advance timer
      feedbackTimer: null,
      feedbackStart: 0,
      feedbackDuration: 3400
    };

    // DOM Element References
    const dom = {
      video: document.getElementById('webcamVideo'),
      canvas: document.getElementById('gameCanvas'),
      welcomeScreen: document.getElementById('welcomeScreen'),
      setupScreen: document.getElementById('setupScreen'),
      gameHud: document.getElementById('gameHud'),
      feedbackOverlay: document.getElementById('feedbackOverlay'),
      resultsScreen: document.getElementById('resultsScreen'),

      // Setup Screen
      setupPreviewCanvas: document.getElementById('setupPreviewCanvas'),
      setupPreviewPlaceholder: document.getElementById('setupPreviewPlaceholder'),
      setupGestureBadge: document.getElementById('setupGestureBadge'),
      webcamStatusText: document.getElementById('webcamStatusText'),
      btnActivateWebcam: document.getElementById('btnActivateWebcam'),

      // HUD
      questionCounterText: document.getElementById('questionCounterText'),
      progressPips: document.getElementById('progressPips'),
      trackingDot: document.getElementById('trackingDot'),
      trackingLabel: document.getElementById('trackingLabel'),
      btnToggleModeHud: document.getElementById('btnToggleModeHud'),
      btnToggleSound: document.getElementById('btnToggleSound'),
      hudStarIcon: document.getElementById('hudStarIcon'),
      starScoreText: document.getElementById('starScoreText'),
      questionTopicTag: document.getElementById('questionTopicTag'),
      equationDisplay: document.getElementById('equationDisplay'),
      questionPromptText: document.getElementById('questionPromptText'),
      gameplayHintPill: document.getElementById('gameplayHintPill'),
      pipWebcamContainer: document.getElementById('pipWebcamContainer'),
      pipCanvas: document.getElementById('pipCanvas'),
      pipGestureLabel: document.getElementById('pipGestureLabel'),

      // Feedback Modal
      feedbackBadge: document.getElementById('feedbackBadge'),
      feedbackHeading: document.getElementById('feedbackHeading'),
      feedbackMessage: document.getElementById('feedbackMessage'),
      feedbackTimerBar: document.getElementById('feedbackTimerBar'),
      btnNextQuestionLabel: document.getElementById('btnNextQuestionLabel'),

      // Results Screen
      resultsTitle: document.getElementById('resultsTitle'),
      resultsStarsRow: document.getElementById('resultsStarsRow'),
      resultsScoreText: document.getElementById('resultsScoreText'),
      resultsEncouragement: document.getElementById('resultsEncouragement'),
      resultsReviewList: document.getElementById('resultsReviewList')
    };

    const ctx = dom.canvas.getContext('2d');
    let canvasWidth = window.innerWidth;
    let canvasHeight = window.innerHeight;

    // ----------------------------------------------------------------------------
    // 4. RESPONSIVE CANVAS & LAYOUT GEOMETRY
    // ----------------------------------------------------------------------------
    function resizeCanvas() {
      const dpr = Math.min(window.devicePixelRatio || 1, 2);
      canvasWidth = window.innerWidth;
      canvasHeight = window.innerHeight;
      dom.canvas.width = Math.floor(canvasWidth * dpr);
      dom.canvas.height = Math.floor(canvasHeight * dpr);
      ctx.setTransform(dpr, 0, 0, dpr, 0, 0);

      updateArenaLayout();
    }

    function updateArenaLayout() {
      // Position Enchanted Tree Hollow Portal in upper-center below Question Plaque
      const isShort = canvasHeight < 640;
      state.portal.x = canvasWidth * 0.5;
      state.portal.y = isShort ? canvasHeight * 0.43 : Math.min(canvasHeight * 0.43, 330);
      state.portal.radius = Math.max(52, Math.min(76, Math.min(canvasWidth, canvasHeight) * 0.095));

      // Position 3 Floating Answer Orbs symmetrically in the lower half
      if (state.orbs.length === 3) {
        const spread = Math.min(canvasWidth * 0.28, 250);
        const baseOrbY = isShort ? canvasHeight * 0.77 : Math.min(canvasHeight * 0.75, state.portal.y + 235);
        const orbRadius = Math.max(40, Math.min(54, Math.min(canvasWidth, canvasHeight) * 0.068));

        const offsets = [-spread, 0, spread];
        const yArc = [12, -10, 12]; // Gentle storybook arc

        state.orbs.forEach((orb, idx) => {
          orb.homeX = canvasWidth * 0.5 + offsets[idx];
          orb.homeY = baseOrbY + yArc[idx];
          orb.radius = orbRadius;
          if (orb.state === 'idle') {
            orb.x = orb.homeX;
            orb.y = orb.homeY;
          }
        });
      }
    }

    function initAmbientForest() {
      state.leaves = [];
      state.fireflies = [];

      for (let i = 0; i < 16; i++) {
        state.leaves.push({
          x: Math.random() * canvasWidth,
          y: Math.random() * canvasHeight,
          size: 7 + Math.random() * 7,
          speedY: 16 + Math.random() * 20,
          swaySpeed: 1.2 + Math.random() * 1.5,
          swayAmp: 22 + Math.random() * 25,
          phase: Math.random() * Math.PI * 2,
          rot: Math.random() * Math.PI * 2,
          rotSpeed: (Math.random() - 0.5) * 1.8,
          color: ['#74C69D', '#95D5B2', '#F4A261', '#FFD166'][i % 4]
        });
      }

      for (let i = 0; i < 24; i++) {
        state.fireflies.push({
          x: Math.random() * canvasWidth,
          y: Math.random() * canvasHeight,
          r: 2 + Math.random() * 2.5,
          vx: (Math.random() - 0.5) * 18,
          vy: (Math.random() - 0.5) * 14,
          pulsePhase: Math.random() * Math.PI * 2
        });
      }
    }

    // ----------------------------------------------------------------------------
    // 5. MEDIAPIPE HANDS INTEGRATION & GESTURE RECOGNITION
    // ----------------------------------------------------------------------------
    let mpHands = null;
    let mpCamera = null;
    let webcamInitializing = false;

    async function activateWebcam() {
      if (state.webcamActive || webcamInitializing) return;
      webcamInitializing = true;
      dom.webcamStatusText.textContent = 'Requesting webcam access... Please allow camera permission.';
      dom.btnActivateWebcam.disabled = true;

      try {
        if (typeof Hands === 'undefined') {
          throw new Error('Hand-tracking library is still loading. Please check your internet connection or use Mouse / Touch.');
        }

        if (!navigator.mediaDevices || !navigator.mediaDevices.getUserMedia) {
          throw new Error('Webcam is not supported in this browser.');
        }

        // Request stream first to verify hardware access clearly
        const stream = await navigator.mediaDevices.getUserMedia({
          video: { width: { ideal: 640 }, height: { ideal: 480 }, facingMode: 'user' },
          audio: false
        });
        dom.video.srcObject = stream;
        await dom.video.play();

        mpHands = new Hands({
          locateFile: (file) => `https://cdn.jsdelivr.net/npm/@mediapipe/hands/${file}`
        });

        mpHands.setOptions({
          maxNumHands: 1,
          modelComplexity: 1,
          minDetectionConfidence: 0.62,
          minTrackingConfidence: 0.58
        });

        mpHands.onResults(onMediaPipeResults);

        // Drive frame processing smoothly via requestAnimationFrame loop
        state.webcamActive = true;
        state.inputMode = 'camera';
        webcamInitializing = false;

        dom.setupPreviewPlaceholder.classList.add('hidden');
        dom.setupGestureBadge.classList.remove('hidden');
        dom.webcamStatusText.innerHTML = '<span class="text-forest-deep font-extrabold">Webcam Active!</span> Wave your hand and pinch your thumb &amp; index finger to test!';
        dom.btnActivateWebcam.innerHTML = '<span>✅</span><span>Webcam Connected</span>';

        updateInputModeUI();
        processWebcamLoop();
      } catch (err) {
        webcamInitializing = false;
        state.webcamActive = false;
        state.webcamError = err.message || 'Camera unavailable';
        dom.btnActivateWebcam.disabled = false;
        dom.webcamStatusText.innerHTML = `<span class="text-amber-800 font-extrabold">Camera unavailable:</span> ${state.webcamError}. No problem—you can play right away with Mouse or Touch!`;
        switchToMouseMode(false);
      }
    }

    async function processWebcamLoop() {
      if (!state.webcamActive) return;
      try {
        if (dom.video.readyState >= 2 && mpHands) {
          await mpHands.send({ image: dom.video });
        }
      } catch (e) {
        // Ignore transient frame errors
      }
      if (state.webcamActive) {
        requestAnimationFrame(processWebcamLoop);
      }
    }

    function onMediaPipeResults(results) {
      if (state.inputMode !== 'camera') return;

      const multiLandmarks = results.multiHandLandmarks;
      if (multiLandmarks && multiLandmarks.length > 0) {
        const landmarks = multiLandmarks[0];
        state.latestLandmarks = landmarks;
        state.handDetected = true;

        // Landmark 4 = Thumb tip, Landmark 8 = Index fingertip, Landmark 0 = Wrist, Landmark 9 = Middle MCP
        const thumb = landmarks[4];
        const index = landmarks[8];
        const wrist = landmarks[0];
        const midMcp = landmarks[9];

        // Mirror horizontally so moving hand left moves cursor left naturally
        const rawMidX = 1 - ((thumb.x + index.x) * 0.5);
        const rawMidY = (thumb.y + index.y) * 0.5;

        // Slightly pad edge mapping so corners of the screen are effortless to reach
        const normX = Math.max(0, Math.min(1, (rawMidX - 0.08) / 0.84));
        const normY = Math.max(0, Math.min(1, (rawMidY - 0.08) / 0.84));

        state.cursor.targetX = normX * canvasWidth;
        state.cursor.targetY = normY * canvasHeight;
        if (!state.cursor.visible) {
          state.cursor.x = state.cursor.targetX;
          state.cursor.y = state.cursor.targetY;
        }
        state.cursor.visible = true;

        // Normalize pinch distance by palm size so distance from camera doesn't skew pinch sensitivity
        const pinchDist = Math.hypot(thumb.x - index.x, thumb.y - index.y);
        const palmSize = Math.max(0.06, Math.hypot(wrist.x - midMcp.x, wrist.y - midMcp.y));
        const pinchRatio = pinchDist / palmSize;

        // Hysteresis thresholds: pinch < 0.43 to grab, > 0.62 to release
        const wasPinching = state.isPinching;
        if (!wasPinching && pinchRatio < 0.43) {
          state.isPinching = true;
          handleGesturePinchStart();
        } else if (wasPinching && pinchRatio > 0.62) {
          state.isPinching = false;
          handleGesturePinchRelease();
        }
      } else {
        state.latestLandmarks = null;
        state.handDetected = false;
        // If hand briefly leaves frame while holding an orb, release safely
        if (state.isPinching) {
          state.isPinching = false;
          handleGesturePinchRelease();
        }
      }

      updateTrackingIndicator();
      renderWebcamMirror(dom.setupPreviewCanvas, false);
      renderWebcamMirror(dom.pipCanvas, true);
    }

    function renderWebcamMirror(targetCanvas, isPip) {
      if (!targetCanvas || !state.webcamActive || dom.video.readyState < 2) return;
      const rect = targetCanvas.getBoundingClientRect();
      if (rect.width === 0 || rect.height === 0) return;

      if (targetCanvas.width !== Math.floor(rect.width) || targetCanvas.height !== Math.floor(rect.height)) {
        targetCanvas.width = Math.floor(rect.width);
        targetCanvas.height = Math.floor(rect.height);
      }

      const mCtx = targetCanvas.getContext('2d');
      const w = targetCanvas.width;
      const h = targetCanvas.height;

      mCtx.save();
      // Mirror horizontally
      mCtx.translate(w, 0);
      mCtx.scale(-1, 1);
      mCtx.drawImage(dom.video, 0, 0, w, h);

      // Draw hand landmarks if detected
      if (state.latestLandmarks) {
        const lm = state.latestLandmarks;
        const connections = [
          [0,1],[1,2],[2,3],[3,4],
          [0,5],[5,6],[6,7],[7,8],
          [5,9],[9,10],[10,11],[11,12],
          [9,13],[13,14],[14,15],[15,16],
          [13,17],[17,18],[18,19],[19,20],[0,17]
        ];

        mCtx.strokeStyle = state.isPinching ? '#FFD166' : '#A8D5BA';
        mCtx.lineWidth = isPip ? 2 : 2.5;
        connections.forEach(([a, b]) => {
          mCtx.beginPath();
          mCtx.moveTo(lm[a].x * w, lm[a].y * h);
          mCtx.lineTo(lm[b].x * w, lm[b].y * h);
          mCtx.stroke();
        });

        // Pinch line between thumb (4) and index (8)
        mCtx.strokeStyle = state.isPinching ? '#FF6B6B' : '#FFFFFF';
        mCtx.lineWidth = 3;
        mCtx.beginPath();
        mCtx.moveTo(lm[4].x * w, lm[4].y * h);
        mCtx.lineTo(lm[8].x * w, lm[8].y * h);
        mCtx.stroke();

        // Highlight fingertips
        [4, 8].forEach((idx) => {
          mCtx.fillStyle = state.isPinching ? '#FFD166' : '#6BA368';
          mCtx.beginPath();
          mCtx.arc(lm[idx].x * w, lm[idx].y * h, isPip ? 4.5 : 6, 0, Math.PI * 2);
          mCtx.fill();
        });
      }
      mCtx.restore();

      // Update small status labels
      if (!isPip && dom.setupGestureBadge) {
        if (!state.handDetected) {
          dom.setupGestureBadge.textContent = 'Show hand to camera ✋';
        } else if (state.isPinching) {
          dom.setupGestureBadge.textContent = '🤏 Pinch Detected! Ready to Grab!';
        } else {
          dom.setupGestureBadge.textContent = '✋ Hand Tracked! Try pinching fingers!';
        }
      }
      if (isPip && dom.pipGestureLabel) {
        if (!state.handDetected) {
          dom.pipGestureLabel.textContent = 'Show Hand ✋';
        } else if (state.isPinching) {
          dom.pipGestureLabel.textContent = 'Pinching! 🤏';
        } else {
          dom.pipGestureLabel.textContent = 'Open Hand ✋';
        }
      }
    }

    // ----------------------------------------------------------------------------
    // 6. MOUSE & TOUCH POINTER FALLBACK (SEAMLESS DRAG & FLICK)
    // ----------------------------------------------------------------------------
    let pointerDownActive = false;

    dom.canvas.addEventListener('pointerdown', (e) => {
      sound.unlock();
      if (state.screen !== 'playing' || state.questionLocked) return;

      const rect = dom.canvas.getBoundingClientRect();
      const px = e.clientX - rect.left;
      const py = e.clientY - rect.top;

      // Switch to mouse mode automatically if user interacts directly via mouse/touch while no hand is tracked
      if (state.inputMode === 'camera' && !state.handDetected) {
        switchToMouseMode(false);
      }

      if (state.inputMode === 'mouse') {
        pointerDownActive = true;
        state.cursor.x = px;
        state.cursor.y = py;
        state.cursor.targetX = px;
        state.cursor.targetY = py;
        state.cursor.visible = true;
        state.cursor.history = [{ x: px, y: py, t: performance.now() }];
        state.isPinching = true;
        handleGesturePinchStart();
      }
    });

    window.addEventListener('pointermove', (e) => {
      if (state.inputMode !== 'mouse') return;
      const rect = dom.canvas.getBoundingClientRect();
      const px = e.clientX - rect.left;
      const py = e.clientY - rect.top;

      state.cursor.targetX = px;
      state.cursor.targetY = py;
      state.cursor.x = px;
      state.cursor.y = py;
      state.cursor.visible = true;
      recordCursorHistory(px, py);
    });

    window.addEventListener('pointerup', () => {
      if (state.inputMode !== 'mouse' || !pointerDownActive) return;
      pointerDownActive = false;
      state.isPinching = false;
      handleGesturePinchRelease();
    });

    window.addEventListener('pointercancel', () => {
      if (state.inputMode !== 'mouse' || !pointerDownActive) return;
      pointerDownActive = false;
      state.isPinching = false;
      handleGesturePinchRelease();
    });

    function switchToMouseMode(playClick = true) {
      if (playClick) sound.playClick();
      state.inputMode = 'mouse';
      state.isPinching = false;
      updateInputModeUI();
      updateTrackingIndicator();
    }

    function toggleInputModeHud() {
      sound.playClick();
      if (state.inputMode === 'mouse') {
        if (state.webcamActive) {
          state.inputMode = 'camera';
          updateInputModeUI();
          updateTrackingIndicator();
        } else {
          // Open setup screen so student can activate webcam
          showScreen('setup');
          activateWebcam();
        }
      } else {
        switchToMouseMode(false);
      }
    }

    function updateInputModeUI() {
      if (state.inputMode === 'camera' && state.webcamActive) {
        dom.pipWebcamContainer.classList.remove('hidden');
        dom.btnToggleModeHud.textContent = '🖱️ Mouse Mode';
        dom.gameplayHintPill.innerHTML = '🤏 Pinch thumb &amp; index finger on an orb, then flick &amp; open your hand toward the portal!';
      } else {
        dom.pipWebcamContainer.classList.add('hidden');
        dom.btnToggleModeHud.textContent = '📷 Webcam Mode';
        dom.gameplayHintPill.innerHTML = '🖱️ Click &amp; hold an answer orb, then drag or flick it into the Enchanted Tree Hollow!';
      }
    }

    function updateTrackingIndicator() {
      if (state.inputMode === 'mouse') {
        dom.trackingDot.className = 'w-3 h-3 rounded-full bg-sky-500 inline-block';
        dom.trackingLabel.textContent = 'Mouse / Touch Mode 🖱️';
      } else if (state.handDetected) {
        dom.trackingDot.className = 'w-3 h-3 rounded-full bg-emerald-500 animate-pulse inline-block';
        dom.trackingLabel.textContent = state.isPinching ? 'Pinching Orb! 🤏' : 'Hand Tracked ✨';
      } else {
        dom.trackingDot.className = 'w-3 h-3 rounded-full bg-amber-400 animate-ping inline-block';
        dom.trackingLabel.textContent = 'Show Hand to Camera ✋';
      }
    }

    // ----------------------------------------------------------------------------
    // 7. GRAB & THROW GAMEPLAY MECHANICS (WITH CHILD-FRIENDLY THROW ASSIST)
    // ----------------------------------------------------------------------------
    function recordCursorHistory(x, y) {
      const now = performance.now();
      state.cursor.history.push({ x, y, t: now });
      // Keep last 180ms of movement for crisp release velocity
      while (state.cursor.history.length > 2 && now - state.cursor.history[0].t > 180) {
        state.cursor.history.shift();
      }
    }

    function computeReleaseVelocity() {
      const h = state.cursor.history;
      if (h.length < 2) return { vx: 0, vy: 0 };
      const first = h[0];
      const last = h[h.length - 1];
      const dt = Math.max(0.016, (last.t - first.t) / 1000);
      return {
        vx: (last.x - first.x) / dt,
        vy: (last.y - first.y) / dt
      };
    }

    function handleGesturePinchStart() {
      if (state.screen !== 'playing' || state.questionLocked) return;

      // Check if an orb is already grabbed
      if (state.orbs.some((o) => o.state === 'grabbed')) return;

      // Find nearest idle/returning orb within generous grab radius (1.45x orb radius for Year 4 ease)
      let closestOrb = null;
      let bestDist = Infinity;

      state.orbs.forEach((orb) => {
        if (orb.state === 'thrown') return;
        const d = Math.hypot(state.cursor.x - orb.x, state.cursor.y - orb.y);
        if (d <= orb.radius * 1.45 && d < bestDist) {
          bestDist = d;
          closestOrb = orb;
        }
      });

      if (closestOrb) {
        closestOrb.state = 'grabbed';
        closestOrb.vx = 0;
        closestOrb.vy = 0;
        state.cursor.history = [{ x: state.cursor.x, y: state.cursor.y, t: performance.now() }];
        sound.playGrab();
        spawnBurst(closestOrb.x, closestOrb.y, '#FFD166', 14);
      }
    }

    function handleGesturePinchRelease() {
      const grabbedOrb = state.orbs.find((o) => o.state === 'grabbed');
      if (!grabbedOrb) return;

      if (state.screen !== 'playing' || state.questionLocked) {
        grabbedOrb.state = 'returning';
        return;
      }

      const { vx, vy } = computeReleaseVelocity();
      const speed = Math.hypot(vx, vy);

      const dxPortal = state.portal.x - grabbedOrb.x;
      const dyPortal = state.portal.y - grabbedOrb.y;
      const distToPortal = Math.hypot(dxPortal, dyPortal);

      // Case A: Released directly inside or right near the Enchanted Portal
      if (distToPortal <= state.portal.radius * 1.35) {
        launchOrbIntoPortal(grabbedOrb);
        return;
      }

      // Case B: Released with an upward or portal-directed flick!
      // Smart child-friendly throw assist: guides upward flicks along a smooth arc into the portal
      const isUpwardFlick = vy < -160 || (speed > 240 && dyPortal < 0 && Math.abs(dxPortal) < canvasWidth * 0.45);
      if (isUpwardFlick) {
        launchOrbIntoPortal(grabbedOrb);
        return;
      }

      // Case C: Weak drop or sideways toss — orb gracefully floats back to its pedestal
      // (Does NOT penalize the student's attempt; encourages a proper toss!)
      grabbedOrb.state = 'returning';
      grabbedOrb.vx = vx * 0.25;
      grabbedOrb.vy = vy * 0.25;
    }

    function launchOrbIntoPortal(orb) {
      orb.state = 'thrown';
      sound.playThrow();

      // Calculate smooth flight velocity toward portal center
      const dx = state.portal.x - orb.x;
      const dy = state.portal.y - orb.y;
      const dist = Math.max(1, Math.hypot(dx, dy));
      const flightSpeed = Math.max(760, dist * 3.1);

      orb.vx = (dx / dist) * flightSpeed;
      orb.vy = (dy / dist) * flightSpeed;
    }

    function evaluateThrownAnswer(orb) {
      if (state.questionLocked) return;
      state.questionLocked = true;

      const currentQ = QUESTIONS_BANK[state.currentIndex];
      const isCorrect = orb.isCorrect;

      state.resultsLog.push({
        question: currentQ,
        chosen: orb.value,
        isCorrect
      });

      if (isCorrect) {
        state.score += 1;
        sound.playSuccess();
        spawnBurst(state.portal.x, state.portal.y, '#FFD166', 36);
        spawnBurst(state.portal.x, state.portal.y, '#74C69D', 24);
      } else {
        sound.playMiss();
        spawnBurst(state.portal.x, state.portal.y, '#F4A261', 22);
      }

      updateHudStats();
      showQuestionFeedback(currentQ, orb.value, isCorrect);
    }

    // ----------------------------------------------------------------------------
    // 8. QUESTION PROGRESSION & UI SCREENS
    // ----------------------------------------------------------------------------
    function showScreen(screenName) {
      state.screen = screenName;
      dom.welcomeScreen.classList.toggle('hidden', screenName !== 'welcome');
      dom.setupScreen.classList.toggle('hidden', screenName !== 'setup');
      dom.gameHud.classList.toggle('hidden', screenName !== 'playing' && screenName !== 'feedback');
      dom.feedbackOverlay.classList.toggle('hidden', screenName !== 'feedback');
      dom.resultsScreen.classList.toggle('hidden', screenName !== 'results');
    }

    function startNewGame() {
      sound.playClick();
      state.currentIndex = 0;
      state.score = 0;
      state.resultsLog = [];
      state.questionLocked = false;
      updateInputModeUI();
      updateTrackingIndicator();
      loadQuestion(0);
      showScreen('playing');
    }

    function shuffleArray(arr) {
      const copy = [...arr];
      for (let i = copy.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [copy[i], copy[j]] = [copy[j], copy[i]];
      }
      return copy;
    }

    function loadQuestion(index) {
      if (state.feedbackTimer) {
        clearInterval(state.feedbackTimer);
        state.feedbackTimer = null;
      }

      state.currentIndex = index;
      state.questionLocked = false;
      state.isPinching = false;

      const q = QUESTIONS_BANK[index];

      // Update Question Plaque
      dom.questionTopicTag.textContent = `${q.type} • Learning Standard ${q.type === 'Addition' ? '1.8.1' : '1.8.2'}`;
      dom.equationDisplay.innerHTML = q.equation.replace(
        q.unknown,
        `<span class="text-forest-deep bg-starlight-gold/45 px-2.5 py-0.5 rounded-xl border-2 border-starlight-amber">${q.unknown}</span>`
      );
      dom.questionPromptText.innerHTML = `Find the value of <span class="font-display text-lg text-forest-deep font-bold">${q.unknown}</span> and throw it into the portal!`;

      // Build 3 shuffled answer orbs
      const choices = shuffleArray([
        { value: q.correct, isCorrect: true },
        { value: q.distractors[0], isCorrect: false },
        { value: q.distractors[1], isCorrect: false }
      ]);

      const palettes = shuffleArray(ORB_PALETTES);

      state.orbs = choices.map((choice, idx) => ({
        value: choice.value,
        isCorrect: choice.isCorrect,
        palette: palettes[idx],
        homeX: canvasWidth * 0.5,
        homeY: canvasHeight * 0.75,
        x: canvasWidth * 0.5,
        y: canvasHeight * 0.75,
        vx: 0,
        vy: 0,
        radius: 48,
        bobPhase: idx * 2.1,
        state: 'idle',
        hovered: false
      }));

      updateArenaLayout();
      updateHudStats();
    }

    function updateHudStats() {
      dom.questionCounterText.textContent = `Question ${state.currentIndex + 1} / ${QUESTIONS_BANK.length}`;
      dom.starScoreText.textContent = `${state.score} / ${QUESTIONS_BANK.length}`;

      // Bump star icon when score increases
      if (state.score > 0) {
        dom.hudStarIcon.classList.remove('star-bump');
        void dom.hudStarIcon.offsetWidth;
        dom.hudStarIcon.classList.add('star-bump');
      }

      // Render 10 progress pips
      dom.progressPips.innerHTML = '';
      for (let i = 0; i < QUESTIONS_BANK.length; i++) {
        const pip = document.createElement('span');
        const logged = state.resultsLog[i];
        if (logged) {
          pip.className = logged.isCorrect
            ? 'w-2.5 h-2.5 sm:w-3 sm:h-3 rounded-full bg-starlight-gold border border-wood-dark shadow-sm'
            : 'w-2.5 h-2.5 sm:w-3 sm:h-3 rounded-full bg-starlight-amber/70 border border-wood-dark';
        } else if (i === state.currentIndex) {
          pip.className = 'w-3 h-3 sm:w-3.5 sm:h-3.5 rounded-full bg-forest-light border-2 border-starlight-gold scale-110';
        } else {
          pip.className = 'w-2 h-2 sm:w-2.5 sm:h-2.5 rounded-full bg-wood-dark/70 border border-wood-light/50';
        }
        dom.progressPips.appendChild(pip);
      }
    }

    function showQuestionFeedback(question, chosenVal, isCorrect) {
      if (isCorrect) {
        dom.feedbackBadge.className = 'inline-flex items-center gap-2 px-4 py-1.5 rounded-full font-display text-base sm:text-lg font-bold mb-3 bg-starlight-gold/55 border-2 border-starlight-amber text-wood-dark';
        dom.feedbackBadge.textContent = '⭐ +1 Star! Magical!';
        dom.feedbackHeading.textContent = ['Brilliant Job!', 'Super Math Magic!', 'Awesome Throw!', 'Spot On!'][state.currentIndex % 4];
        dom.feedbackMessage.innerHTML = `You threw <span class="text-forest-deep font-display text-xl">${chosenVal}</span>!<br/><span class="text-wood-dark">${question.explanation}</span>`;
      } else {
        dom.feedbackBadge.className = 'inline-flex items-center gap-2 px-4 py-1.5 rounded-full font-display text-base sm:text-lg font-bold mb-3 bg-forest-light/60 border-2 border-forest-mid text-forest-dark';
        dom.feedbackBadge.textContent = '🌿 Good Try! Keep Going!';
        dom.feedbackHeading.textContent = 'Almost Had It!';
        dom.feedbackMessage.innerHTML = `You threw <span class="text-amber-800 font-display text-xl">${chosenVal}</span>.<br/><span class="text-wood-dark">Since ${question.explanation}</span>`;
      }

      const isLast = state.currentIndex >= QUESTIONS_BANK.length - 1;
      dom.btnNextQuestionLabel.textContent = isLast ? 'See Final Results 🏆' : 'Next Question';

      showScreen('feedback');

      // Smooth auto-advance timer bar
      state.feedbackStart = performance.now();
      dom.feedbackTimerBar.style.width = '100%';

      state.feedbackTimer = setInterval(() => {
        const elapsed = performance.now() - state.feedbackStart;
        const pct = Math.max(0, 100 - (elapsed / state.feedbackDuration) * 100);
        dom.feedbackTimerBar.style.width = `${pct}%`;
        if (elapsed >= state.feedbackDuration) {
          advanceAfterFeedback();
        }
      }, 50);
    }

    function advanceAfterFeedback() {
      if (state.feedbackTimer) {
        clearInterval(state.feedbackTimer);
        state.feedbackTimer = null;
      }
      if (state.currentIndex + 1 < QUESTIONS_BANK.length) {
        loadQuestion(state.currentIndex + 1);
        showScreen('playing');
      } else {
        showFinalResults();
      }
    }

    function showFinalResults() {
      sound.playSuccess();
      showScreen('results');

      dom.resultsScoreText.textContent = `You earned ${state.score} / ${QUESTIONS_BANK.length} Stars!`;

      if (state.score >= 8) {
        dom.resultsTitle.textContent = 'Grand Forest Math Wizard!';
        dom.resultsEncouragement.textContent = 'Sensational! You mastered missing numbers in both addition and subtraction!';
      } else if (state.score >= 5) {
        dom.resultsTitle.textContent = 'Enchanted Forest Explorer!';
        dom.resultsEncouragement.textContent = 'Great effort! You solved lots of missing numbers—play again to collect all 10 stars!';
      } else {
        dom.resultsTitle.textContent = 'Brave Forest Apprentice!';
        dom.resultsEncouragement.textContent = 'Well done finishing the quest! Check the scroll below and try again to win even more stars!';
      }

      // Render 10 stars row
      dom.resultsStarsRow.innerHTML = '';
      for (let i = 0; i < QUESTIONS_BANK.length; i++) {
        const span = document.createElement('span');
        const earned = i < state.score;
        span.textContent = earned ? '⭐' : '☆';
        span.className = earned ? 'drop-shadow scale-110 transition-transform' : 'text-wood-light/50';
        dom.resultsStarsRow.appendChild(span);
      }

      // Render 10-question review list
      dom.resultsReviewList.innerHTML = '';
      state.resultsLog.forEach((entry, idx) => {
        const row = document.createElement('div');
        row.className = `flex items-center justify-between gap-2 px-3.5 py-2 rounded-xl border-2 text-xs sm:text-sm font-bold ${
          entry.isCorrect
            ? 'bg-forest-light/35 border-forest-mid/60 text-forest-dark'
            : 'bg-cream-light border-cream-border text-wood-dark'
        }`;

        row.innerHTML = `
          <div class="flex items-center gap-2">
            <span class="font-display text-wood-mid">#${idx + 1}</span>
            <span class="font-display text-base text-wood-dark">${entry.question.equation}</span>
          </div>
          <div class="flex items-center gap-2 text-right">
            <span>${entry.question.unknown} = <strong>${entry.question.correct}</strong></span>
            <span>${entry.isCorrect ? '⭐' : `(Threw ${entry.chosen})`}</span>
          </div>
        `;
        dom.resultsReviewList.appendChild(row);
      });
    }

    // ----------------------------------------------------------------------------
    // 9. PARTICLE EFFECTS & ANIMATED STORYBOOK FOREST RENDERER
    // ----------------------------------------------------------------------------
    function spawnBurst(x, y, color, count) {
      for (let i = 0; i < count; i++) {
        const angle = (Math.PI * 2 * i) / count + Math.random() * 0.4;
        const speed = 70 + Math.random() * 190;
        state.particles.push({
          x,
          y,
          vx: Math.cos(angle) * speed,
          vy: Math.sin(angle) * speed - 30,
          size: 4 + Math.random() * 5,
          alpha: 1,
          decay: 1.1 + Math.random() * 0.8,
          color,
          isStar: i % 2 === 0
        });
      }
    }

    function drawStarShape(c, cx, cy, spikes, outerR, innerR) {
      let rot = (Math.PI / 2) * 3;
      const step = Math.PI / spikes;
      c.beginPath();
      c.moveTo(cx, cy - outerR);
      for (let i = 0; i < spikes; i++) {
        c.lineTo(cx + Math.cos(rot) * outerR, cy + Math.sin(rot) * outerR);
        rot += step;
        c.lineTo(cx + Math.cos(rot) * innerR, cy + Math.sin(rot) * innerR);
        rot += step;
      }
      c.closePath();
      c.fill();
    }

    function drawStorybookForestBackground(t) {
      const w = canvasWidth;
      const h = canvasHeight;

      // 1. Sunlit Forest Canopy Sky Gradient
      const skyGrad = ctx.createLinearGradient(0, 0, 0, h);
      skyGrad.addColorStop(0, '#BCE3C8');
      skyGrad.addColorStop(0.42, '#8FBF9F');
      skyGrad.addColorStop(0.82, '#5F8D6A');
      skyGrad.addColorStop(1, '#3D6147');
      ctx.fillStyle = skyGrad;
      ctx.fillRect(0, 0, w, h);

      // 2. Soft Warm Sunbeams Filtering Through Trees
      ctx.save();
      const beamGrad = ctx.createLinearGradient(w * 0.5, 0, w * 0.5, h * 0.78);
      beamGrad.addColorStop(0, 'rgba(253, 248, 238, 0.34)');
      beamGrad.addColorStop(1, 'rgba(253, 248, 238, 0)');
      ctx.fillStyle = beamGrad;
      for (let i = -1; i <= 1; i++) {
        ctx.beginPath();
        ctx.moveTo(w * 0.5 + i * 90 - 40, 0);
        ctx.lineTo(w * 0.5 + i * 90 + 40, 0);
        ctx.lineTo(w * 0.5 + i * 240 + 140, h * 0.8);
        ctx.lineTo(w * 0.5 + i * 240 - 140, h * 0.8);
        ctx.closePath();
        ctx.fill();
      }
      ctx.restore();

      // 3. Distant Rolling Sage Hills
      ctx.fillStyle = '#6B9B78';
      ctx.beginPath();
      ctx.moveTo(0, h * 0.68);
      ctx.quadraticCurveTo(w * 0.28, h * 0.56, w * 0.55, h * 0.66);
      ctx.quadraticCurveTo(w * 0.82, h * 0.75, w, h * 0.62);
      ctx.lineTo(w, h);
      ctx.lineTo(0, h);
      ctx.closePath();
      ctx.fill();

      // 4. Framing Storybook Tree Trunks on Left & Right Edges
      const trunkWidth = Math.max(48, Math.min(95, w * 0.08));
      // Left Tree Trunk
      ctx.fillStyle = '#5C3D2E';
      ctx.beginPath();
      ctx.moveTo(0, 0);
      ctx.lineTo(trunkWidth * 1.15, 0);
      ctx.quadraticCurveTo(trunkWidth * 0.85, h * 0.5, trunkWidth * 1.35, h);
      ctx.lineTo(0, h);
      ctx.closePath();
      ctx.fill();

      // Right Tree Trunk
      ctx.beginPath();
      ctx.moveTo(w, 0);
      ctx.lineTo(w - trunkWidth * 1.15, 0);
      ctx.quadraticCurveTo(w - trunkWidth * 0.85, h * 0.5, w - trunkWidth * 1.35, h);
      ctx.lineTo(w, h);
      ctx.closePath();
      ctx.fill();

      // Top Leafy Canopy Scallops
      ctx.fillStyle = '#4A7856';
      const puffs = Math.ceil(w / 110) + 1;
      for (let i = 0; i < puffs; i++) {
        const px = i * 110;
        const py = 12 + Math.sin(t * 1.1 + i) * 4;
        ctx.beginPath();
        ctx.arc(px, py, 68, 0, Math.PI * 2);
        ctx.fill();
      }

      // 5. Lush Forest Floor Meadow + Mossy Stones & Cute Toadstools
      const floorGrad = ctx.createLinearGradient(0, h * 0.78, 0, h);
      floorGrad.addColorStop(0, '#588B60');
      floorGrad.addColorStop(1, '#36593C');
      ctx.fillStyle = floorGrad;
      ctx.beginPath();
      ctx.moveTo(0, h * 0.83);
      ctx.quadraticCurveTo(w * 0.5, h * 0.76, w, h * 0.83);
      ctx.lineTo(w, h);
      ctx.lineTo(0, h);
      ctx.closePath();
      ctx.fill();

      // Cute Toadstools on Left & Right Foreground
      drawToadstool(trunkWidth * 1.35, h * 0.88, 1.0);
      drawToadstool(w - trunkWidth * 1.45, h * 0.89, 0.85);

      // 6. Drifting Leaves & Glowing Fireflies
      state.leaves.forEach((leaf) => {
        const lx = leaf.x + Math.sin(t * leaf.swaySpeed + leaf.phase) * leaf.swayAmp;
        ctx.save();
        ctx.translate(lx, leaf.y);
        ctx.rotate(leaf.rot);
        ctx.fillStyle = leaf.color;
        ctx.globalAlpha = 0.75;
        ctx.beginPath();
        ctx.ellipse(0, 0, leaf.size, leaf.size * 0.52, 0, 0, Math.PI * 2);
        ctx.fill();
        ctx.restore();
      });

      state.fireflies.forEach((ff) => {
        const glowAlpha = 0.35 + 0.55 * (0.5 + 0.5 * Math.sin(t * 2.6 + ff.pulsePhase));
        ctx.save();
        ctx.fillStyle = `rgba(255, 232, 133, ${glowAlpha})`;
        ctx.beginPath();
        ctx.arc(ff.x, ff.y, ff.r * 2.4, 0, Math.PI * 2);
        ctx.fill();
        ctx.fillStyle = `rgba(255, 252, 230, ${Math.min(1, glowAlpha + 0.25)})`;
        ctx.beginPath();
        ctx.arc(ff.x, ff.y, ff.r, 0, Math.PI * 2);
        ctx.fill();
        ctx.restore();
      });
    }

    function drawToadstool(x, y, scale) {
      ctx.save();
      ctx.translate(x, y);
      ctx.scale(scale, scale);

      // Stem
      ctx.fillStyle = '#FDF8EE';
      ctx.strokeStyle = '#6D4C3D';
      ctx.lineWidth = 2.5;
      ctx.beginPath();
      ctx.roundRect(-9, -6, 18, 26, 8);
      ctx.fill();
      ctx.stroke();

      // Cap
      ctx.fillStyle = '#E06D53';
      ctx.beginPath();
      ctx.arc(0, -6, 22, Math.PI, 0);
      ctx.closePath();
      ctx.fill();
      ctx.stroke();

      // Cream Spots
      ctx.fillStyle = '#FDF8EE';
      ctx.beginPath();
      ctx.arc(-8, -14, 4, 0, Math.PI * 2);
      ctx.arc(7, -15, 3.5, 0, Math.PI * 2);
      ctx.arc(0, -21, 3, 0, Math.PI * 2);
      ctx.fill();

      ctx.restore();
    }

    function drawEnchantedPortal(t) {
      if (state.screen !== 'playing' && state.screen !== 'feedback') return;

      const { x, y, radius } = state.portal;
      const hasGrabbedOrb = state.orbs.some((o) => o.state === 'grabbed');
      const pulseScale = 1 + Math.sin(t * 3.5) * 0.04 + (hasGrabbedOrb ? 0.07 : 0);

      ctx.save();
      ctx.translate(x, y);

      // Outer Magical Aura
      const auraR = radius * 1.55 * pulseScale;
      const auraGrad = ctx.createRadialGradient(0, 0, radius * 0.4, 0, 0, auraR);
      auraGrad.addColorStop(0, hasGrabbedOrb ? 'rgba(255, 209, 102, 0.55)' : 'rgba(168, 213, 186, 0.45)');
      auraGrad.addColorStop(1, 'rgba(168, 213, 186, 0)');
      ctx.fillStyle = auraGrad;
      ctx.beginPath();
      ctx.arc(0, 0, auraR, 0, Math.PI * 2);
      ctx.fill();

      // Carved Storybook Wood Ring Frame
      ctx.fillStyle = '#6D4C3D';
      ctx.strokeStyle = '#4A3222';
      ctx.lineWidth = 4;
      ctx.beginPath();
      ctx.arc(0, 0, radius + 12, 0, Math.PI * 2);
      ctx.fill();
      ctx.stroke();

      // Mossy Leaf Accents Around Ring
      ctx.fillStyle = '#6BA368';
      for (let i = 0; i < 8; i++) {
        const ang = (Math.PI * 2 * i) / 8 + Math.sin(t + i) * 0.06;
        const lx = Math.cos(ang) * (radius + 10);
        const ly = Math.sin(ang) * (radius + 10);
        ctx.save();
        ctx.translate(lx, ly);
        ctx.rotate(ang + Math.PI / 2);
        ctx.beginPath();
        ctx.ellipse(0, 0, 11, 6, 0, 0, Math.PI * 2);
        ctx.fill();
        ctx.restore();
      }

      // Swirling Inner Portal Vortex
      const vortexGrad = ctx.createRadialGradient(0, 0, 6, 0, 0, radius);
      vortexGrad.addColorStop(0, '#FFF8D6');
      vortexGrad.addColorStop(0.45, '#74C69D');
      vortexGrad.addColorStop(0.85, '#2D6A4F');
      vortexGrad.addColorStop(1, '#1B4332');
      ctx.fillStyle = vortexGrad;
      ctx.beginPath();
      ctx.arc(0, 0, radius, 0, Math.PI * 2);
      ctx.fill();

      // Orbiting Golden Runes / Sparkles inside Portal
      for (let i = 0; i < 6; i++) {
        const a = t * 1.8 + (Math.PI * 2 * i) / 6;
        const rr = radius * 0.62;
        ctx.fillStyle = 'rgba(255, 232, 133, 0.85)';
        drawStarShape(ctx, Math.cos(a) * rr, Math.sin(a) * rr, 4, 6, 2.5);
      }

      // Clear Label Banner at Bottom of Portal
      const labelW = Math.max(156, radius * 2.3);
      const labelH = 26;
      ctx.fillStyle = '#FDF8EE';
      ctx.strokeStyle = '#6D4C3D';
      ctx.lineWidth = 2.5;
      ctx.beginPath();
      ctx.roundRect(-labelW / 2, radius + 2, labelW, labelH, 13);
      ctx.fill();
      ctx.stroke();

      ctx.fillStyle = '#4A3222';
      ctx.font = '700 13px Fredoka, sans-serif';
      ctx.textAlign = 'center';
      ctx.textBaseline = 'middle';
      ctx.fillText(hasGrabbedOrb ? '✨ Release to Throw! ✨' : 'Throw Answer Here!', 0, radius + 15);

      ctx.restore();
    }

    function drawAnswerOrbs(t) {
      if (state.screen !== 'playing' && state.screen !== 'feedback') return;

      // Draw idle/returning orbs first, then grabbed/thrown orbs on top
      const sortedOrbs = [...state.orbs].sort((a, b) => {
        const rank = { idle: 0, returning: 1, thrown: 2, grabbed: 3 };
        return rank[a.state] - rank[b.state];
      });

      sortedOrbs.forEach((orb) => {
        const bobOffset = orb.state === 'idle' ? Math.sin(t * 2.8 + orb.bobPhase) * 7 : 0;
        const drawX = orb.x;
        const drawY = orb.y + bobOffset;
        const scale = orb.state === 'grabbed' ? 1.15 : orb.hovered ? 1.08 : 1.0;
        const r = orb.radius * scale;

        ctx.save();
        ctx.translate(drawX, drawY);

        // Outer Glow when Hovered or Grabbed
        if (orb.hovered || orb.state === 'grabbed' || orb.state === 'thrown') {
          ctx.fillStyle = orb.palette.glow;
          ctx.beginPath();
          ctx.arc(0, 0, r * 1.38, 0, Math.PI * 2);
          ctx.fill();
        }

        // Ground Shadow when idle
        if (orb.state === 'idle') {
          ctx.fillStyle = 'rgba(26, 44, 31, 0.25)';
          ctx.beginPath();
          ctx.ellipse(0, r + 14 - bobOffset * 0.5, r * 0.68, 9, 0, 0, Math.PI * 2);
          ctx.fill();
        }

        // 3D Cartoon Sphere Shading
        const grad = ctx.createRadialGradient(-r * 0.3, -r * 0.32, r * 0.12, 0, 0, r);
        grad.addColorStop(0, orb.palette.top);
        grad.addColorStop(0.55, orb.palette.mid);
        grad.addColorStop(1, orb.palette.bottom);

        ctx.fillStyle = grad;
        ctx.strokeStyle = orb.state === 'grabbed' ? '#FFF8DC' : orb.palette.rim;
        ctx.lineWidth = orb.state === 'grabbed' ? 4.5 : 3.5;
        ctx.beginPath();
        ctx.arc(0, 0, r, 0, Math.PI * 2);
        ctx.fill();
        ctx.stroke();

        // Glossy Specular Highlight
        ctx.fillStyle = 'rgba(255, 255, 255, 0.45)';
        ctx.beginPath();
        ctx.ellipse(-r * 0.32, -r * 0.35, r * 0.26, r * 0.15, -Math.PI / 5, 0, Math.PI * 2);
        ctx.fill();

        // High-Contrast Number Label for Year 4 Readability
        const fontSize = Math.round(r * 0.76);
        ctx.font = `700 ${fontSize}px Fredoka, sans-serif`;
        ctx.textAlign = 'center';
        ctx.textBaseline = 'middle';

        // Crisp cream halo behind dark wood number
        ctx.lineWidth = 5;
        ctx.strokeStyle = '#FDF8EE';
        ctx.strokeText(String(orb.value), 0, 2);

        ctx.fillStyle = '#3B2516';
        ctx.fillText(String(orb.value), 0, 2);

        ctx.restore();
      });
    }

    function drawParticlesAndCursor(t) {
      // 1. Burst Particles
      state.particles.forEach((p) => {
        ctx.save();
        ctx.globalAlpha = Math.max(0, p.alpha);
        ctx.fillStyle = p.color;
        if (p.isStar) {
          drawStarShape(ctx, p.x, p.y, 5, p.size * 1.3, p.size * 0.55);
        } else {
          ctx.beginPath();
          ctx.arc(p.x, p.y, p.size, 0, Math.PI * 2);
          ctx.fill();
        }
        ctx.restore();
      });

      // 2. Magical Hand Cursor & Sparkle Trail (during gameplay)
      if ((state.screen === 'playing' || state.screen === 'feedback') && state.cursor.visible) {
        // Trail
        state.cursorTrail.forEach((tr) => {
          ctx.save();
          ctx.globalAlpha = tr.alpha * 0.7;
          ctx.fillStyle = tr.pinching ? '#FFD166' : '#A8D5BA';
          ctx.beginPath();
          ctx.arc(tr.x, tr.y, tr.r, 0, Math.PI * 2);
          ctx.fill();
          ctx.restore();
        });

        const cx = state.cursor.x;
        const cy = state.cursor.y;
        const pinching = state.isPinching;
        const ringR = pinching ? 20 : 28 + Math.sin(t * 5) * 2.5;

        ctx.save();
        ctx.translate(cx, cy);

        // Glowing Fairy Aura
        const cGrad = ctx.createRadialGradient(0, 0, 3, 0, 0, ringR * 1.4);
        cGrad.addColorStop(0, pinching ? 'rgba(255, 209, 102, 0.9)' : 'rgba(253, 248, 238, 0.85)');
        cGrad.addColorStop(1, 'rgba(255, 209, 102, 0)');
        ctx.fillStyle = cGrad;
        ctx.beginPath();
        ctx.arc(0, 0, ringR * 1.4, 0, Math.PI * 2);
        ctx.fill();

        // Outer Ring
        ctx.strokeStyle = pinching ? '#F4A261' : '#FDF8EE';
        ctx.lineWidth = 3.5;
        ctx.beginPath();
        ctx.arc(0, 0, ringR, 0, Math.PI * 2);
        ctx.stroke();

        // Center Star Sparkle
        ctx.fillStyle = pinching ? '#FFD166' : '#6BA368';
        drawStarShape(ctx, 0, 0, 4, pinching ? 11 : 9, 4);

        ctx.restore();
      }
    }

    // ----------------------------------------------------------------------------
    // 10. MAIN SIMULATION & ANIMATION LOOP
    // ----------------------------------------------------------------------------
    let lastFrameTime = performance.now();

    function updateSimulation(dt, t) {
      // Ambient leaves & fireflies
      state.leaves.forEach((leaf) => {
        leaf.y += leaf.speedY * dt;
        leaf.rot += leaf.rotSpeed * dt;
        if (leaf.y > canvasHeight + 20) {
          leaf.y = -20;
          leaf.x = Math.random() * canvasWidth;
        }
      });

      state.fireflies.forEach((ff) => {
        ff.x += ff.vx * dt;
        ff.y += ff.vy * dt;
        if (ff.x < 0) ff.x = canvasWidth;
        if (ff.x > canvasWidth) ff.x = 0;
        if (ff.y < 0) ff.y = canvasHeight;
        if (ff.y > canvasHeight) ff.y = 0;
      });

      // Smooth cursor interpolation for camera tracking
      if (state.inputMode === 'camera' && state.cursor.visible) {
        const lerp = 1 - Math.pow(0.0008, dt);
        state.cursor.x += (state.cursor.targetX - state.cursor.x) * lerp;
        state.cursor.y += (state.cursor.targetY - state.cursor.y) * lerp;
        recordCursorHistory(state.cursor.x, state.cursor.y);
      }

      // Add cursor trail particles
      if (state.cursor.visible && (state.screen === 'playing')) {
        state.cursorTrail.push({
          x: state.cursor.x,
          y: state.cursor.y,
          r: state.isPinching ? 6 : 4,
          alpha: 0.85,
          pinching: state.isPinching
        });
      }
      state.cursorTrail.forEach((tr) => {
        tr.alpha -= dt * 2.8;
        tr.r *= 0.96;
      });
      state.cursorTrail = state.cursorTrail.filter((tr) => tr.alpha > 0.05);

      // Update particles
      state.particles.forEach((p) => {
        p.x += p.vx * dt;
        p.y += p.vy * dt;
        p.vy += 180 * dt;
        p.alpha -= p.decay * dt;
      });
      state.particles = state.particles.filter((p) => p.alpha > 0);

      // Update Answer Orbs
      state.orbs.forEach((orb) => {
        // Hover check
        const distToCursor = Math.hypot(state.cursor.x - orb.x, state.cursor.y - orb.y);
        orb.hovered = state.cursor.visible && distToCursor <= orb.radius * 1.3;

        if (orb.state === 'grabbed') {
          // Follow hand/pointer cursor smoothly
          const followLerp = 1 - Math.pow(0.0001, dt);
          orb.x += (state.cursor.x - orb.x) * followLerp;
          orb.y += (state.cursor.y - orb.y) * followLerp;
        } else if (orb.state === 'thrown') {
          orb.x += orb.vx * dt;
          orb.y += orb.vy * dt;

          // Emit golden sparkle trail
          if (Math.random() < 0.75) {
            state.particles.push({
              x: orb.x + (Math.random() - 0.5) * 16,
              y: orb.y + (Math.random() - 0.5) * 16,
              vx: (Math.random() - 0.5) * 40,
              vy: (Math.random() - 0.5) * 40,
              size: 4 + Math.random() * 3,
              alpha: 0.9,
              decay: 2.2,
              color: '#FFD166',
              isStar: true
            });
          }

          // Check collision with Enchanted Portal
          const distPortal = Math.hypot(orb.x - state.portal.x, orb.y - state.portal.y);
          if (distPortal <= state.portal.radius * 0.72) {
            orb.x = state.portal.x;
            orb.y = state.portal.y;
            orb.state = 'idle';
            evaluateThrownAnswer(orb);
          }
        } else if (orb.state === 'returning') {
          const springLerp = 1 - Math.pow(0.002, dt);
          orb.x += (orb.homeX - orb.x) * springLerp;
          orb.y += (orb.homeY - orb.y) * springLerp;
          if (Math.hypot(orb.homeX - orb.x, orb.homeY - orb.y) < 2) {
            orb.x = orb.homeX;
            orb.y = orb.homeY;
            orb.state = 'idle';
          }
        }
      });
    }

    function animateLoop(now) {
      const dt = Math.min(0.05, (now - lastFrameTime) / 1000);
      lastFrameTime = now;
      const t = now / 1000;

      updateSimulation(dt, t);

      ctx.clearRect(0, 0, canvasWidth, canvasHeight);
      drawStorybookForestBackground(t);
      drawEnchantedPortal(t);
      drawAnswerOrbs(t);
      drawParticlesAndCursor(t);

      requestAnimationFrame(animateLoop);
    }

    // ----------------------------------------------------------------------------
    // 11. BUTTON EVENT LISTENERS & WINDOW INITIALIZATION
    // ----------------------------------------------------------------------------
    document.getElementById('btnGoToSetup').addEventListener('click', () => {
      sound.playClick();
      showScreen('setup');
    });

    document.getElementById('btnWelcomeMouse').addEventListener('click', () => {
      switchToMouseMode(false);
      startNewGame();
    });

    document.getElementById('btnBackToWelcome').addEventListener('click', () => {
      sound.playClick();
      showScreen('welcome');
    });

    dom.btnActivateWebcam.addEventListener('click', () => {
      sound.playClick();
      activateWebcam();
    });

    document.getElementById('btnUseMouseSetup').addEventListener('click', () => {
      switchToMouseMode(true);
      dom.webcamStatusText.innerHTML = '<span class="text-forest-deep font-extrabold">Mouse / Touch Mode Selected!</span> Click &amp; drag orbs into the Enchanted Tree Hollow.';
    });

    document.getElementById('btnStartQuest').addEventListener('click', () => {
      if (!state.webcamActive) {
        switchToMouseMode(false);
      }
      startNewGame();
    });

    dom.btnToggleModeHud.addEventListener('click', toggleInputModeHud);

    dom.btnToggleSound.addEventListener('click', () => {
      const isMuted = sound.toggleMute();
      dom.btnToggleSound.textContent = isMuted ? '🔇' : '🔊';
      if (!isMuted) sound.playClick();
    });

    document.getElementById('btnNextQuestion').addEventListener('click', () => {
      sound.playClick();
      advanceAfterFeedback();
    });

    document.getElementById('btnPlayAgain').addEventListener('click', () => {
      startNewGame();
    });

    window.addEventListener('resize', resizeCanvas);

    window.onload = function () {
      resizeCanvas();
      initAmbientForest();
      lastFrameTime = performance.now();
      requestAnimationFrame(animateLoop);
    };
  </script>
</body>
</html>oading magic_hands_the_missing_number_challenge (2).html…]()

