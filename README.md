# NEOME
電影推薦
<!DOCTYPE html>
<html lang="zh-TW">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <title>NEONE.FM - TMDb 電影盲盒</title>
  <!-- Tailwind CSS CDN -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- FontAwesome 圖標 -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  
  <style>
    /* 附圖一 黑魂純黑背景與字體風格 */
    body {
      background-color: #000000;
      color: #e2e8f0;
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
    }

    /* 空心字體樣式 (匹配附圖一 D.FM 風格) */
    .logo-outline-text {
      font-weight: 900;
      letter-spacing: -1px;
      color: transparent;
      -webkit-text-stroke: 1.8px #ffffff;
    }

    /* 玻璃幾何面板 */
    .glass-card {
      background: rgba(18, 18, 20, 0.75);
      border: 1px solid rgba(255, 255, 255, 0.12);
      backdrop-filter: blur(20px);
    }

    /* 光學漩渦摩爾紋轉動動畫 */
    @keyframes vortexRotate {
      0% { transform: rotate(0deg) scale(0.95); opacity: 0.8; }
      50% { transform: rotate(180deg) scale(1.05); opacity: 1; }
      100% { transform: rotate(360deg) scale(0.95); opacity: 0.8; }
    }
    .animate-vortex-spin {
      animation: vortexRotate 1.4s cubic-bezier(0.4, 0, 0.2, 1) infinite;
    }

    /* 按鈕高光 */
    .btn-white {
      background: #ffffff;
      color: #000000;
      box-shadow: 0 0 25px rgba(255, 255, 255, 0.25);
      transition: all 0.2s ease;
    }
    .btn-white:hover {
      box-shadow: 0 0 35px rgba(255, 255, 255, 0.45);
      transform: translateY(-2px);
    }
    .btn-white:active {
      transform: scale(0.97);
    }

    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(12px); }
      to { opacity: 1; transform: translateY(0); }
    }
    .animate-fade {
      animation: fadeIn 0.4s cubic-bezier(0.16, 1, 0.3, 1) forwards;
    }
  </style>
</head>
<body class="min-h-screen relative overflow-x-hidden flex flex-col justify-between items-center px-4 py-6">

  <!-- 頁頭 Logo 區塊 (完全對標附圖一左上角風格) -->
  <header class="w-full max-w-md flex justify-between items-center z-10 py-2">
    <div class="flex items-center gap-3">
      <!-- 空心 D.FM 風格 LOGO -->
      <span class="logo-outline-text text-2xl tracking-tighter">N.FM</span>
      <div class="h-4 w-[1px] bg-slate-800"></div>
      <div class="flex flex-col text-[10px] leading-tight text-slate-400 font-mono">
        <span class="text-slate-200 font-semibold tracking-wider">NEONE.FM</span>
        <span class="text-slate-500">Built on TMDb</span>
      </div>
    </div>
    
    <!-- 右側圓形按鈕 (匹配附圖一右上角圓形按鈕風格) -->
    <button id="toggle-filter-btn" class="w-9 h-9 rounded-full bg-neutral-900 border border-neutral-700 flex items-center justify-center text-slate-300 hover:text-white hover:border-slate-400 transition-all">
      <i class="fa-solid fa-sliders text-xs"></i>
    </button>
  </header>

  <!-- 篩選器面板 -->
  <div id="filter-panel" class="hidden w-full max-w-md glass-card rounded-2xl p-4 my-2 z-20 space-y-3">
    <div class="flex justify-between items-center">
      <span class="text-xs font-semibold text-slate-300 font-mono">// FILTER PREFERENCES</span>
      <button id="reset-filter" class="text-[11px] text-slate-400 hover:text-white">RESET</button>
    </div>
    <div class="grid grid-cols-2 gap-2 text-xs">
      <div>
        <label class="block text-slate-500 mb-1 text-[10px] font-mono">GENRE</label>
        <select id="filter-genre" class="w-full bg-black text-slate-200 border border-neutral-800 rounded-lg p-2 outline-none focus:border-slate-500">
          <option value="">全部類型</option>
          <option value="18">劇情片</option>
          <option value="53">驚悚片</option>
          <option value="878">科幻片</option>
          <option value="16">動畫片</option>
          <option value="27">恐怖片</option>
          <option value="35">喜劇片</option>
        </select>
      </div>
      <div>
        <label class="block text-slate-500 mb-1 text-[10px] font-mono">LANGUAGE</label>
        <select id="filter-lang" class="w-full bg-black text-slate-200 border border-neutral-800 rounded-lg p-2 outline-none focus:border-slate-500">
          <option value="">全部語系</option>
          <option value="ko">韓語</option>
          <option value="ja">日語</option>
          <option value="en">英語</option>
          <option value="zh">華語</option>
        </select>
      </div>
    </div>
  </div>

  <!-- 核心主頁面 -->
  <main class="w-full max-w-md my-auto py-4 flex flex-col items-center justify-center z-10 space-y-6">

    <!-- 未抽取狀態 Hero -->
    <div id="hero-state" class="text-center space-y-4 px-2">
      <div class="w-28 h-28 mx-auto flex items-center justify-center opacity-60">
        <!-- 靜止時的同心圓圖騰 -->
        <svg viewBox="0 0 100 100" class="w-full h-full stroke-slate-500 fill-none stroke-[0.8]">
          <circle cx="50" cy="50" r="45" stroke-dasharray="2 2" />
          <circle cx="50" cy="50" r="35" />
          <circle cx="50" cy="50" r="25" stroke-dasharray="4 2" />
          <circle cx="50" cy="50" r="15" />
          <circle cx="50" cy="50" r="5" class="fill-slate-400" />
        </svg>
      </div>
      <h1 class="text-2xl font-bold tracking-tight text-white">
        今晚不知道看什麼？
      </h1>
      <p class="text-xs text-slate-500 font-mono">
        PRESS BUTTON BELOW TO SPIN THE WHEEL
      </p>
    </div>

    <!-- 抽選進行中：附圖一同款旋轉摩爾紋/光學漩渦動畫 (預設隱藏) -->
    <div id="loading-state" class="hidden flex-col items-center justify-center space-y-5 py-8">
      <div class="relative w-48 h-48 flex items-center justify-center">
        <!-- SVG 摩爾紋光學漩渦 -->
        <svg viewBox="0 0 200 200" class="w-full h-full animate-vortex-spin stroke-white fill-none stroke-[1]">
          <ellipse cx="100" cy="100" rx="90" ry="40" transform="rotate(0 100 100)" opacity="0.9"/>
          <ellipse cx="100" cy="100" rx="90" ry="40" transform="rotate(30 100 100)" opacity="0.8"/>
          <ellipse cx="100" cy="100" rx="90" ry="40" transform="rotate(60 100 100)" opacity="0.7"/>
          <ellipse cx="100" cy="100" rx="90" ry="40" transform="rotate(90 100 100)" opacity="0.9"/>
          <ellipse cx="100" cy="100" rx="90" ry="40" transform="rotate(120 100 100)" opacity="0.8"/>
          <ellipse cx="100" cy="100" rx="90" ry="40" transform="rotate(150 100 100)" opacity="0.7"/>
          <circle cx="100" cy="100" r="20" class="fill-white stroke-none" />
        </svg>
        <div class="absolute text-[10px] font-mono tracking-widest text-white/80 uppercase">Selecting</div>
      </div>
      <span class="text-xs font-mono text-slate-400 tracking-wider animate-pulse">SEARCHING TMDB ARCHIVE...</span>
    </div>

    <!-- 推薦結果卡片 (預設隱藏) -->
    <div id="result-card" class="hidden w-full glass-card rounded-2xl p-4 space-y-4 animate-fade">
      <div class="relative w-full h-80 sm:h-96 rounded-xl overflow-hidden bg-neutral-950 border border-neutral-800">
        <img id="movie-poster" src="" alt="電影海報" 
             onerror="this.onerror=null; this.src='https://placehold.co/400x600/000/fff?text=No+Poster';" 
             class="w-full h-full object-cover">
        
        <div class="absolute top-3 left-3 bg-black/80 backdrop-blur-md px-2.5 py-1 rounded text-[10px] font-mono text-slate-300 border border-neutral-700">
          <span id="movie-lang-tag"></span>
        </div>

        <div class="absolute top-3 right-3 bg-black/80 backdrop-blur-md px-2.5 py-1 rounded text-xs font-mono font-bold text-amber-400 border border-neutral-700 flex items-center gap-1">
          ★ <span id="movie-rating">0.0</span>
        </div>
      </div>

      <div class="space-y-2">
        <div class="flex items-baseline justify-between gap-2">
          <h2 id="movie-title" class="text-lg font-bold text-white tracking-wide"></h2>
          <span id="movie-year" class="text-xs font-mono text-slate-500"></span>
        </div>

        <div class="flex items-center gap-2">
          <span id="movie-genre-tag" class="px-2 py-0.5 rounded bg-neutral-800 text-slate-300 text-[10px] font-mono"></span>
          <span id="movie-original-title" class="text-xs text-slate-500 truncate italic"></span>
        </div>

        <p id="movie-overview" class="text-xs text-slate-400 leading-relaxed pt-1 line-clamp-3"></p>
      </div>
    </div>

    <!-- 置中推薦按鈕 -->
    <div class="w-full flex justify-center pt-2">
      <button id="recommend-btn" class="btn-white w-full sm:w-auto px-8 py-3.5 rounded-full font-bold text-xs tracking-wider uppercase font-mono flex items-center justify-center gap-2">
        <i class="fa-solid fa-play text-xs" id="btn-icon"></i>
        <span id="btn-text">請推薦我電影</span>
      </button>
    </div>

  </main>

  <footer class="w-full max-w-md text-center z-10 py-2">
    <p class="text-[10px] font-mono text-neutral-600">NEONE.FM ARCHIVE • RANDOM SELECTOR</p>
  </footer>

  <script>
    const TMDB_API_KEY = 'f1baea1a64768c2ace8585a66ad8fbdc';

    const genreDict = { 18: "劇情", 53: "驚悚", 878: "科幻", 16: "動畫", 27: "恐怖", 35: "喜劇" };
    const langDict = { ko: "韓語", ja: "日語", en: "英語", zh: "華語", cn: "華語" };

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
        if (!data1.results || data1.results.length === 0) return null;

        const totalPages = Math.min(data1.total_pages || 1, 25);
        const randomPage = Math.floor(Math.random() * totalPages) + 1;

        const res2 = await fetch(`${baseUrl}&page=${randomPage}`);
        const data2 = await res2.json();

        const validMovies = (data2.results || []).filter(m => m.poster_path && m.overview);
        return validMovies.length > 0 ? validMovies[Math.floor(Math.random() * validMovies.length)] : data1.results[0];
      } catch (e) {
        console.error(e);
        return null;
      }
    }

    recommendBtn.addEventListener('click', async () => {
      // 切換至旋轉動畫狀態
      heroState.classList.add('hidden');
      resultCard.classList.add('hidden');
      loadingState.classList.remove('hidden');
      loadingState.classList.add('flex');

      btnText.textContent = "SELECTING...";
      btnIcon.className = "fa-solid fa-spinner fa-spin";
      recommendBtn.disabled = true;

      // 同時執行 API 抓取與 1.2 秒最小轉動動畫展示
      const [movie] = await Promise.all([
        fetchRandomMovieFromTMDb(),
        new Promise(resolve => setTimeout(resolve, 1200))
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
        btnText.textContent = "AGAIN 🎲";
        btnIcon.className = "fa-solid fa-rotate-right";
      } else {
        alert("未找到符合條件的電影！");
        heroState.classList.remove('hidden');
        btnText.textContent = "請推薦我電影";
        btnIcon.className = "fa-solid fa-play";
      }

      recommendBtn.disabled = false;
    });
  </script>
</body>
</html>
