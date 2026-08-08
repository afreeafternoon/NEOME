<!DOCTYPE html>
<html lang="zh-TW">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <title>NEONE - Lumine Insights Movie AI</title>
  <!-- Tailwind CSS CDN -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- FontAwesome 圖標 -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  
  <style>
    /* NEONE 深邃夜空黑背景 */
    body {
      background-color: #04060d;
      color: #f1f5f9;
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", sans-serif;
    }

    /* 藍光拱門弧線特效（支援 RWD 自適應寬度） */
    .hero-glow-container {
      position: relative;
    }
    .hero-glow-arc {
      position: absolute;
      top: -140px;
      left: 50%;
      transform: translateX(-50%);
      width: 130%;
      max-width: 900px;
      height: 280px;
      border-radius: 50%;
      background: radial-gradient(ellipse at center, rgba(56, 189, 248, 0.3) 0%, rgba(14, 165, 233, 0.08) 55%, transparent 75%);
      border-top: 2px solid rgba(125, 211, 252, 0.85);
      box-shadow: 0 -15px 60px rgba(56, 189, 248, 0.45), inset 0 10px 30px rgba(56, 189, 248, 0.2);
      pointer-events: none;
    }

    /* 頂部兩側高光柱背景 */
    .bg-light-pillars {
      background-image: 
        radial-gradient(ellipse 600px 800px at 50% -150px, rgba(56, 189, 248, 0.15), transparent),
        linear-gradient(to bottom, rgba(15, 23, 42, 0.6), transparent 600px);
    }

    /* 微光膠囊徽章 */
    .glow-badge {
      background: rgba(15, 23, 42, 0.6);
      border: 1px solid rgba(56, 189, 248, 0.25);
      box-shadow: 0 0 20px rgba(56, 189, 248, 0.15);
      backdrop-filter: blur(12px);
    }

    /* 白光主按鈕 */
    .btn-lumine-primary {
      background: #ffffff;
      color: #04060d;
      font-weight: 700;
      box-shadow: 0 0 30px rgba(255, 255, 255, 0.35);
      transition: all 0.25s cubic-bezier(0.4, 0, 0.2, 1);
    }
    .btn-lumine-primary:hover {
      box-shadow: 0 0 40px rgba(56, 189, 248, 0.6);
      transform: translateY(-2px);
    }
    .btn-lumine-primary:active {
      transform: scale(0.97);
    }

    /* 暗色次要按鈕 */
    .btn-lumine-secondary {
      background: rgba(15, 23, 42, 0.6);
      color: #e2e8f0;
      border: 1px solid rgba(255, 255, 255, 0.15);
      backdrop-filter: blur(12px);
      transition: all 0.2s ease;
    }
    .btn-lumine-secondary:hover {
      border-color: rgba(56, 189, 248, 0.4);
      background: rgba(30, 41, 59, 0.8);
    }

    /* 玻璃幾何面板 */
    .glass-card {
      background: rgba(10, 15, 29, 0.75);
      border: 1px solid rgba(255, 255, 255, 0.1);
      box-shadow: 0 20px 50px rgba(0, 0, 0, 0.5), inset 0 1px 0 rgba(255, 255, 255, 0.1);
      backdrop-filter: blur(16px);
    }

    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(16px) scale(0.98); }
      to { opacity: 1; transform: translateY(0) scale(1); }
    }
    .animate-fade {
      animation: fadeIn 0.45s cubic-bezier(0.16, 1, 0.3, 1) forwards;
    }
  </style>
</head>
<body class="min-h-screen bg-light-pillars relative overflow-x-hidden flex flex-col justify-between items-center px-4 sm:px-8 py-6">

  <!-- 頂部 Header（RWD 自適應寬度，支援 PC / 平板 / 手機） -->
  <header class="w-full max-w-5xl flex justify-between items-center z-20 py-3 border-b border-slate-800/40">
    <!-- 左側 LOGO -->
    <div class="flex items-center gap-2">
      <span class="text-cyan-400 text-xl leading-none">✦✦</span>
      <span class="font-extrabold tracking-wider text-xl text-white font-sans">NEONE</span>
    </div>
    
    <!-- 右側控制鈕 -->
    <div class="flex items-center gap-3 text-xs sm:text-sm font-medium">
      <button id="toggle-filter-btn" class="btn-lumine-secondary px-4 py-2 rounded-full flex items-center gap-2 text-slate-300 hover:text-white">
        <i class="fa-solid fa-sliders text-cyan-400 text-xs"></i>
        <span>偏好篩選</span>
      </button>
    </div>
  </header>

  <!-- 可摺疊篩選器（PC 欄位自動擴展為多欄排版） -->
  <div id="filter-panel" class="hidden w-full max-w-5xl glass-card rounded-2xl p-5 my-4 z-30 space-y-4 border-cyan-500/20">
    <div class="flex justify-between items-center pb-2 border-b border-slate-800/80">
      <span class="text-xs sm:text-sm font-semibold text-slate-300 flex items-center gap-2">
        <i class="fa-solid fa-filter text-cyan-400"></i> 設定 TMDb 篩選條件
      </span>
      <button id="reset-filter" class="text-xs text-cyan-400 hover:underline">重置條件</button>
    </div>
    <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-4 gap-4 text-xs sm:text-sm">
      <div>
        <label class="block text-slate-400 mb-1.5 text-xs">電影類型</label>
        <select id="filter-genre" class="w-full bg-slate-900/90 text-slate-200 border border-slate-700/80 rounded-xl p-2.5 outline-none focus:border-cyan-400">
          <option value="">全部類型</option>
          <option value="18">劇情片</option>
          <option value="53">驚悚片</option>
          <option value="878">科幻片</option>
          <option value="16">動畫片</option>
          <option value="27">恐怖片</option>
          <option value="35">喜劇片</option>
          <option value="28">動作片</option>
        </select>
      </div>
      <div>
        <label class="block text-slate-400 mb-1.5 text-xs">發音語系</label>
        <select id="filter-lang" class="w-full bg-slate-900/90 text-slate-200 border border-slate-700/80 rounded-xl p-2.5 outline-none focus:border-cyan-400">
          <option value="">全部語系</option>
          <option value="ko">韓語</option>
          <option value="ja">日語</option>
          <option value="en">英語</option>
          <option value="zh">華語</option>
        </select>
      </div>
    </div>
  </div>

  <!-- 主內容區塊 -->
  <main class="w-full max-w-5xl my-auto py-8 sm:py-12 flex flex-col items-center justify-center z-10 space-y-8">

    <!-- 頂部經典藍光拱門 -->
    <div class="hero-glow-container w-full flex flex-col items-center text-center">
      <div class="hero-glow-arc"></div>

      <!-- 膠囊徽章 -->
      <div class="glow-badge inline-flex items-center gap-2 px-4 py-1.5 rounded-full text-xs font-medium text-cyan-200 mb-6">
        <span class="text-cyan-400 text-xs">✦</span>
        <span>Live TMDb API Connected</span>
      </div>

      <!-- 未抽取狀態 Hero Section（PC端級別字體與佈局） -->
      <div id="hero-state" class="space-y-4 px-2 max-w-2xl">
        <h1 class="text-3xl sm:text-5xl md:text-6xl font-extrabold tracking-tight leading-tight text-white">
          Build Faster With<br>
          <span class="bg-clip-text text-transparent bg-gradient-to-r from-sky-200 via-cyan-300 to-indigo-300">
            NEONE Movie Insights
          </span>
        </h1>

        <p class="text-xs sm:text-base text-slate-400 max-w-lg mx-auto leading-relaxed pt-2">
          擺脫選擇困難症。點擊下方按鈕，即刻從全球 TMDb 資料庫中為你隨機抽選一部優質電影。
        </p>
      </div>

      <!-- 載入中狀態 (預設隱藏) -->
      <div id="loading-state" class="hidden flex-col items-center justify-center space-y-3 py-10">
        <div class="w-12 h-12 border-2 border-cyan-400 border-t-transparent rounded-full animate-spin"></div>
        <p class="text-xs sm:text-sm font-medium text-cyan-300 animate-pulse tracking-widest">SEARCHING TMDB DATABASE...</p>
      </div>

      <!-- 推薦電影結果卡片（PC 雙欄 / 手機單欄 RWD 自動切換） -->
      <div id="result-card" class="hidden w-full glass-card rounded-2xl p-5 sm:p-8 text-left border-cyan-500/30 mt-4 animate-fade">
        <div class="grid grid-cols-1 md:grid-cols-12 gap-6 md:gap-8 items-center">
          
          <!-- 左側海報（PC 端佔 4 欄，手機端滿版） -->
          <div class="md:col-span-4 lg:col-span-4 flex justify-center">
            <div class="relative w-full max-w-sm md:max-w-none h-80 sm:h-96 md:h-[400px] rounded-xl overflow-hidden bg-slate-950 shadow-2xl group border border-slate-800">
              <img id="movie-poster" src="" alt="電影海報" 
                   onerror="this.onerror=null; this.src='https://placehold.co/400x600/0f172a/38bdf8?text=Poster+Unavailable';" 
                   class="w-full h-full object-cover">
              
              <div class="absolute top-3 left-3 bg-slate-950/80 backdrop-blur-md px-2.5 py-1 rounded-md text-[10px] font-bold text-cyan-300 border border-cyan-500/30">
                <span id="movie-lang-tag"></span>
              </div>

              <div class="absolute top-3 right-3 bg-slate-950/80 backdrop-blur-md px-2.5 py-1 rounded-md text-xs font-extrabold text-amber-400 border border-amber-400/30 flex items-center gap-1">
                <i class="fa-solid fa-star text-[10px]"></i>
                <span id="movie-rating">0.0</span>
              </div>
            </div>
          </div>

          <!-- 右側電影詳細資訊（PC 端佔 8 欄，手機端置下） -->
          <div class="md:col-span-8 lg:col-span-8 space-y-4">
            <div>
              <div class="flex items-baseline justify-between flex-wrap gap-2">
                <h2 id="movie-title" class="text-2xl sm:text-3xl font-extrabold text-white tracking-wide"></h2>
                <span id="movie-year" class="text-sm sm:text-base font-semibold text-cyan-400/80"></span>
              </div>
              <p id="movie-original-title" class="text-xs sm:text-sm text-slate-400 italic pt-1"></p>
            </div>

            <div class="flex items-center gap-2 pt-1">
              <span id="movie-genre-tag" class="px-3 py-1 rounded-md bg-cyan-950/80 border border-cyan-500/30 text-cyan-300 text-xs font-medium"></span>
            </div>

            <div class="pt-2 border-t border-slate-800/80">
              <h3 class="text-xs font-semibold text-slate-400 uppercase tracking-wider mb-1.5">劇情簡介</h3>
              <p id="movie-overview" class="text-xs sm:text-sm text-slate-300 leading-relaxed line-clamp-6"></p>
            </div>
          </div>

        </div>
      </div>

      <!-- 置中雙按鈕區塊 -->
      <div class="flex items-center justify-center gap-4 pt-8 w-full max-w-xs sm:max-w-sm">
        <button id="recommend-btn" class="btn-lumine-primary flex-1 py-4 px-8 rounded-full text-xs sm:text-sm tracking-wide flex items-center justify-center gap-2.5">
          <i class="fa-solid fa-wand-magic-sparkles text-cyan-600" id="btn-icon"></i>
          <span id="btn-text">請推薦我電影</span>
        </button>
      </div>

    </div>

  </main>

  <!-- 頁尾 -->
  <footer class="w-full max-w-5xl text-center z-10 py-4 border-t border-slate-800/40">
    <p class="text-xs text-slate-500 font-sans">© NEONE Cinema AI • Powered by TMDb API</p>
  </footer>

  <script>
    const TMDB_API_KEY = 'f1baea1a64768c2ace8585a66ad8fbdc';

    const genreDict = {
      18: "劇情", 53: "驚悚", 878: "科幻", 16: "動畫", 
      27: "恐怖", 35: "喜劇", 28: "動作"
    };

    const langDict = {
      ko: "韓語", ja: "日語", en: "英語", zh: "華語", cn: "華語"
    };

    const recommendBtn = document.getElementById('recommend-btn');
    const btnText = document.getElementById('btn-text');
    const btnIcon = document.getElementById('btn-icon');
    const heroState = document.getElementById('hero-state');
    const loadingState = document.getElementById('loading-state');
    const resultCard = document.getElementById('result-card');
    
    const toggleFilterBtn = document.getElementById('toggle-filter-btn');
    const filterPanel = document.getElementById('filter-panel');
    const filterGenre = document.getElementById('filter-genre');
    const filterLang = document.getElementById('filter-lang');
    const resetFilter = document.getElementById('reset-filter');

    const moviePoster = document.getElementById('movie-poster');
    const movieTitle = document.getElementById('movie-title');
    const movieOriginalTitle = document.getElementById('movie-original-title');
    const movieYear = document.getElementById('movie-year');
    const movieRating = document.getElementById('movie-rating');
    const movieGenreTag = document.getElementById('movie-genre-tag');
    const movieLangTag = document.getElementById('movie-lang-tag');
    const movieOverview = document.getElementById('movie-overview');

    toggleFilterBtn.addEventListener('click', () => filterPanel.classList.toggle('hidden'));
    resetFilter.addEventListener('click', () => { filterGenre.value = ""; filterLang.value = ""; });

    async function fetchRandomMovieFromTMDb() {
      const genre = filterGenre.value;
      const lang = filterLang.value;

      let baseUrl = `https://api.themoviedb.org/3/discover/movie?api_key=${TMDB_API_KEY}&language=zh-TW&sort_by=popularity.desc&vote_count.gte=50&include_adult=false`;
      
      if (genre) baseUrl += `&with_genres=${genre}`;
      if (lang) baseUrl += `&with_original_language=${lang}`;

      try {
        const res1 = await fetch(`${baseUrl}&page=1`);
        const data1 = await res1.json();

        if (!data1.results || data1.results.length === 0) {
          alert("沒有找到符合該條件的電影，請放寬篩選條件！");
          return null;
        }

        const totalPages = Math.min(data1.total_pages || 1, 30);
        const randomPage = Math.floor(Math.random() * totalPages) + 1;

        const res2 = await fetch(`${baseUrl}&page=${randomPage}`);
        const data2 = await res2.json();

        const validMovies = (data2.results || []).filter(m => m.poster_path && m.overview);
        if (validMovies.length === 0) return data1.results[0];

        const randomIndex = Math.floor(Math.random() * validMovies.length);
        return validMovies[randomIndex];
      } catch (error) {
        console.error("TMDb API Error:", error);
        alert("無法連線至 TMDb API，請檢查網路連線！");
        return null;
      }
    }

    recommendBtn.addEventListener('click', async () => {
      heroState.classList.add('hidden');
      resultCard.classList.add('hidden');
      loadingState.classList.remove('hidden');
      loadingState.classList.add('flex');

      btnText.textContent = "抽選中...";
      btnIcon.className = "fa-solid fa-spinner fa-spin text-cyan-600";
      recommendBtn.disabled = true;

      const [movie] = await Promise.all([
        fetchRandomMovieFromTMDb(),
        new Promise(resolve => setTimeout(resolve, 800))
      ]);

      loadingState.classList.add('hidden');
      loadingState.classList.remove('flex');

      if (movie) {
        moviePoster.src = `https://image.tmdb.org/t/p/w500${movie.poster_path}`;
        movieTitle.textContent = movie.title || movie.original_title;
        movieOriginalTitle.textContent = movie.original_title !== movie.title ? movie.original_title : '';
        movieYear.textContent = movie.release_date ? `(${movie.release_date.split('-')[0]})` : '';
        movieRating.textContent = movie.vote_average ? movie.vote_average.toFixed(1) : 'N/A';
        
        movieGenreTag.textContent = genreDict[movie.genre_ids?.[0]] || "電影";
        movieLangTag.textContent = langDict[movie.original_language] || movie.original_language.toUpperCase();
        movieOverview.textContent = movie.overview || "此電影暫無中文劇情摘要說明。";

        resultCard.classList.remove('hidden');

        btnText.textContent = "再換一部 🎲";
        btnIcon.className = "fa-solid fa-rotate-right text-cyan-600";
      } else {
        heroState.classList.remove('hidden');
        btnText.textContent = "請推薦我電影";
        btnIcon.className = "fa-solid fa-wand-magic-sparkles text-cyan-600";
      }

      recommendBtn.disabled = false;
    });
  </script>
</body>
</html>
