# live-staff
<!DOCTYPE html>
<html lang="ja" class="h-full bg-slate-50">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>ライブスタッフ給料計算プロ</title>
  <!-- Tailwind CSS CDN -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- FontAwesome Icons -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  <!-- Google Fonts: Inter & Noto Sans JP -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=Noto+Sans+JP:wght@400;500;700&display=swap" rel="stylesheet">
  
  <script>
    tailwind.config = {
      theme: {
        extend: {
          fontFamily: {
            sans: ['Inter', 'Noto Sans JP', 'sans-serif'],
          },
          colors: {
            brand: {
              50: '#eef2ff',
              100: '#e0e7ff',
              500: '#6366f1',
              600: '#4f46e5',
              700: '#4338ca',
            }
          }
        }
      }
    }
  </script>
  <style>
    /* Custom scrollbar for better visual polish */
    ::-webkit-scrollbar {
      width: 6px;
      height: 6px;
    }
    ::-webkit-scrollbar-track {
      background: #f1f5f9;
    }
    ::-webkit-scrollbar-thumb {
      background: #cbd5e1;
      border-radius: 9999px;
    }
    ::-webkit-scrollbar-thumb:hover {
      background: #94a3b8;
    }
  </style>
</head>
<body class="h-full text-slate-800 antialiased flex flex-col font-sans">

  <!-- Top Navigation Header -->
  <header class="bg-indigo-600 text-white shadow-md sticky top-0 z-30">
    <div class="max-w-4xl mx-auto px-4 py-3 flex items-center justify-between">
      <div class="flex items-center space-x-3">
        <div class="bg-white/20 p-2 rounded-lg backdrop-blur-sm">
          <i class="fa-solid fa-microphone-lines text-xl text-yellow-300"></i>
        </div>
        <div>
          <h1 class="font-bold text-lg leading-tight">ライブスタッフ給料計算 <span class="text-xs bg-yellow-400 text-slate-900 font-extrabold px-1.5 py-0.5 rounded ml-1">PRO</span></h1>
          <p class="text-xs text-indigo-200">イベント・コンサートスタッフ専用 日給・手当計算機</p>
        </div>
      </div>
      <div class="flex items-center space-x-2">
        <button onclick="exportDataCSV()" title="CSV出力" class="p-2 text-indigo-100 hover:text-white hover:bg-indigo-500 rounded-lg transition text-sm flex items-center space-x-1">
          <i class="fa-solid fa-file-csv text-base"></i>
          <span class="hidden sm:inline">CSV保存</span>
        </button>
      </div>
    </div>

    <!-- Navigation Tabs -->
    <div class="bg-indigo-700/50 backdrop-blur border-t border-indigo-500/30">
      <div class="max-w-4xl mx-auto px-2 flex space-x-1 overflow-x-auto">
        <button onclick="switchTab('calc')" id="tab-calc" class="tab-btn flex-1 py-2.5 px-3 text-center text-sm font-semibold border-b-2 border-yellow-400 text-white flex items-center justify-center space-x-1.5">
          <i class="fa-solid fa-calculator"></i>
          <span>給料計算</span>
        </button>
        <button onclick="switchTab('history')" id="tab-history" class="tab-btn flex-1 py-2.5 px-3 text-center text-sm font-semibold border-b-2 border-transparent text-indigo-200 hover:text-white flex items-center justify-center space-x-1.5">
          <i class="fa-solid fa-calendar-check"></i>
          <span>勤務履歴</span>
          <span id="historyBadge" class="bg-indigo-500 text-white text-xs px-1.5 py-0.2 rounded-full hidden">0</span>
        </button>
        <button onclick="switchTab('venues')" id="tab-venues" class="tab-btn flex-1 py-2.5 px-3 text-center text-sm font-semibold border-b-2 border-transparent text-indigo-200 hover:text-white flex items-center justify-center space-x-1.5">
          <i class="fa-solid fa-map-location-dot"></i>
          <span>会場プリセット</span>
        </button>
        <button onclick="switchTab('companies')" id="tab-companies" class="tab-btn flex-1 py-2.5 px-3 text-center text-sm font-semibold border-b-2 border-transparent text-indigo-200 hover:text-white flex items-center justify-center space-x-1.5">
          <i class="fa-solid fa-building-user"></i>
          <span>派遣・所属会社</span>
        </button>
        <button onclick="switchTab('summary')" id="tab-summary" class="tab-btn flex-1 py-2.5 px-3 text-center text-sm font-semibold border-b-2 border-transparent text-indigo-200 hover:text-white flex items-center justify-center space-x-1.5">
          <i class="fa-solid fa-chart-pie"></i>
          <span>月別集計</span>
        </button>
      </div>
    </div>
  </header>

  <!-- Main Scrollable Content Area -->
  <main class="flex-1 max-w-4xl w-full mx-auto p-4 sm:p-6 space-y-6">

    <!-- TAB 1: CALCULATOR VIEW -->
    <section id="view-calc" class="space-y-6">

      <!-- Shift Date & Input Mode Selector Card -->
      <div class="bg-white rounded-xl shadow-sm border border-slate-200 p-4 sm:p-5 space-y-4">
        <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-3 pb-3 border-b border-slate-100">
          <div class="flex flex-wrap items-center gap-3 flex-1">
            <div class="flex items-center space-x-2">
              <i class="fa-solid fa-calendar-day text-indigo-600"></i>
              <label for="shiftDate" class="font-semibold text-slate-700 text-sm">勤務日</label>
              <input type="date" id="shiftDate" class="px-3 py-1.5 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 focus:outline-none">
            </div>
            <div class="flex items-center space-x-2 flex-1 min-w-[200px]">
              <i class="fa-solid fa-music text-indigo-600"></i>
              <input type="text" id="eventArtist" oninput="calculateSalary()" placeholder="アーティスト名 / イベント名 (任意)" class="w-full px-3 py-1.5 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 focus:outline-none">
            </div>
          </div>

          <!-- Calculation Mode Switcher -->
          <div class="bg-slate-100 p-1 rounded-lg flex text-xs font-semibold shrink-0">
            <button type="button" id="modeTimeBtn" onclick="setInputMode('time')" class="px-3 py-1.5 rounded-md bg-white text-indigo-600 shadow-sm transition">
              <i class="fa-regular fa-clock mr-1"></i>時刻入力 (自動計算)
            </button>
            <button type="button" id="modeHoursBtn" onclick="setInputMode('hours')" class="px-3 py-1.5 rounded-md text-slate-600 hover:text-slate-900 transition">
              <i class="fa-solid fa-hourglass-half mr-1"></i>時間数直接入力
            </button>
          </div>
        </div>

        <!-- Company, Venue Selection & Base Wage Inputs -->
        <div class="grid grid-cols-1 sm:grid-cols-3 gap-4">
          <div>
            <label for="companySelect" class="block text-xs font-semibold text-slate-600 mb-1">
              <i class="fa-solid fa-building-user text-indigo-500 mr-1"></i>派遣・所属会社
            </label>
            <select id="companySelect" onchange="onCompanySelectChange()" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm bg-slate-50/50 focus:bg-white focus:ring-2 focus:ring-indigo-500 focus:outline-none font-medium">
              <option value="custom">-- 指定なし / 直接入力 --</option>
            </select>
          </div>

          <div>
            <label for="venueSelect" class="block text-xs font-semibold text-slate-600 mb-1">
              <i class="fa-solid fa-location-dot text-indigo-500 mr-1"></i>勤務会場
            </label>
            <select id="venueSelect" onchange="onVenueSelectChange()" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm bg-slate-50/50 focus:bg-white focus:ring-2 focus:ring-indigo-500 focus:outline-none">
              <option value="custom">-- 直接入力 / 指定なし --</option>
            </select>
          </div>

          <div>
            <label for="baseWage" class="block text-xs font-semibold text-slate-600 mb-1">
              <i class="fa-solid fa-yen-sign text-indigo-500 mr-1"></i>基本時給 (円)
            </label>
            <input type="number" id="baseWage" value="1200" min="0" step="10" oninput="calculateSalary()" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 focus:outline-none font-medium">
          </div>
        </div>

        <!-- Transportation Fee Input -->
        <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
          <div>
            <label for="transport" class="block text-xs font-semibold text-slate-600 mb-1">
              <i class="fa-solid fa-train text-indigo-500 mr-1"></i>交通費 (円)
            </label>
            <div class="relative">
              <input type="number" id="transport" value="0" min="0" step="10" oninput="calculateSalary()" class="w-full pl-8 pr-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 focus:outline-none font-medium">
              <span class="absolute left-3 top-2 text-slate-400 text-sm">¥</span>
            </div>
          </div>
          <div>
            <label for="allowance" class="block text-xs font-semibold text-slate-600 mb-1">
              <i class="fa-solid fa-utensils text-indigo-500 mr-1"></i>その他手当 (食事代/遠方等)
            </label>
            <div class="relative">
              <input type="number" id="allowance" value="0" min="0" step="100" oninput="calculateSalary()" class="w-full pl-8 pr-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 focus:outline-none font-medium">
              <span class="absolute left-3 top-2 text-slate-400 text-sm">¥</span>
            </div>
          </div>
        </div>
      </div>

      <!-- Hours / Time Details Card -->
      <div class="bg-white rounded-xl shadow-sm border border-slate-200 p-4 sm:p-5 space-y-4">
        <h3 class="text-sm font-bold text-slate-800 flex items-center justify-between border-b border-slate-100 pb-2">
          <span><i class="fa-solid fa-business-time text-indigo-600 mr-1.5"></i>勤務時間の設定</span>
          <span class="text-xs font-normal text-slate-500">※法定労働8時間超えは自動で25%割増</span>
        </h3>

        <!-- Section A: Time Picker Mode -->
        <div id="timeInputSection" class="grid grid-cols-1 sm:grid-cols-3 gap-4">
          <div>
            <label for="startTime" class="block text-xs font-semibold text-slate-600 mb-1">開始時刻</label>
            <input type="time" id="startTime" value="08:00" onchange="calculateSalary()" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 focus:outline-none">
          </div>
          <div>
            <label for="endTime" class="block text-xs font-semibold text-slate-600 mb-1">終了時刻</label>
            <input type="time" id="endTime" value="22:30" onchange="calculateSalary()" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 focus:outline-none">
          </div>
          <div>
            <label for="breakHoursTime" class="block text-xs font-semibold text-slate-600 mb-1">休憩時間 (時間)</label>
            <select id="breakHoursTime" onchange="calculateSalary()" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 focus:outline-none">
              <option value="0">なし (0分)</option>
              <option value="0.5">0.5 時間 (30分)</option>
              <option value="1">1.0 時間 (60分)</option>
              <option value="1.5" selected>1.5 時間 (90分)</option>
              <option value="2">2.0 時間 (120分)</option>
              <option value="2.5">2.5 時間 (150分)</option>
              <option value="3">3.0 時間 (180分)</option>
            </select>
          </div>
        </div>

        <!-- Section B: Direct Hours Mode (Initially Hidden) -->
        <div id="hoursInputSection" class="grid grid-cols-1 sm:grid-cols-3 gap-4 hidden">
          <div>
            <label for="workHours" class="block text-xs font-semibold text-slate-600 mb-1">総拘束時間 (時間)</label>
            <input type="number" id="workHours" value="10" step="0.25" min="0" oninput="calculateSalary()" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 focus:outline-none">
          </div>
          <div>
            <label for="breakHours" class="block text-xs font-semibold text-slate-600 mb-1">休憩時間 (時間)</label>
            <input type="number" id="breakHours" value="1.5" step="0.25" min="0" oninput="calculateSalary()" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 focus:outline-none">
          </div>
          <div>
            <label for="nightHours" class="block text-xs font-semibold text-slate-600 mb-1">深夜労働 [22:00〜05:00] (時間)</label>
            <input type="number" id="nightHours" value="0.5" step="0.25" min="0" oninput="calculateSalary()" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 focus:outline-none">
          </div>
        </div>

        <!-- Additional Legal Options / Withholding Tax -->
        <div class="pt-2 flex flex-wrap items-center gap-4 border-t border-slate-100 text-xs text-slate-700">
          <label class="inline-flex items-center cursor-pointer">
            <input type="checkbox" id="taxDeduction" onchange="calculateSalary()" class="rounded border-slate-300 text-indigo-600 focus:ring-indigo-500 h-4 w-4">
            <span class="ml-2 font-medium">源泉徴収税を引く (10.21%)</span>
          </label>
          <label class="inline-flex items-center cursor-pointer">
            <input type="checkbox" id="overtimeToggle" checked onchange="calculateSalary()" class="rounded border-slate-300 text-indigo-600 focus:ring-indigo-500 h-4 w-4">
            <span class="ml-2 font-medium">法定外残業割増 (8時間超+25%) 適用</span>
          </label>
        </div>
      </div>

      <!-- Live Calculated Result Card -->
      <div class="bg-gradient-to-br from-slate-900 to-indigo-950 text-white rounded-xl shadow-lg p-5 sm:p-6 space-y-5 relative overflow-hidden">
        <div class="absolute -right-10 -bottom-10 opacity-10 text-9xl">
          <i class="fa-solid fa-coins"></i>
        </div>

        <div class="flex flex-wrap items-center justify-between gap-2">
          <span class="text-xs font-bold uppercase tracking-wider text-indigo-300 bg-indigo-900/60 px-2.5 py-1 rounded-full border border-indigo-700/50">
            概算支給額 (日給)
          </span>
          <div class="flex items-center space-x-2 flex-wrap gap-y-1">
            <span id="resCompanyBadge" class="text-xs text-emerald-300 font-semibold bg-emerald-950/80 px-2 py-0.5 rounded border border-emerald-500/30 hidden">
              <i class="fa-solid fa-building-user mr-1"></i><span id="resCompanyText"></span>
            </span>
            <span id="resArtistBadge" class="text-xs text-yellow-300 font-semibold bg-white/10 px-2 py-0.5 rounded border border-white/20 hidden">
              <i class="fa-solid fa-music mr-1"></i><span id="resArtistText"></span>
            </span>
            <span id="resVenueBadge" class="text-xs text-slate-300 font-medium">
              <i class="fa-solid fa-building mr-1"></i>直接入力
            </span>
          </div>
        </div>

        <!-- Big Total Salary Display -->
        <div class="text-center py-2">
          <div id="totalPayDisplay" class="text-4xl sm:text-5xl font-extrabold tracking-tight text-yellow-400 drop-shadow">
            ¥0
          </div>
          <p class="text-xs text-slate-400 mt-1" id="hourlyRateEquivalent">実質時給: ¥0 / 時</p>
        </div>

        <!-- Breakdown Details -->
        <div class="bg-white/5 rounded-lg p-3.5 space-y-2 text-xs border border-white/10 backdrop-blur">
          <div class="flex justify-between items-center text-slate-300">
            <span>実働時間</span>
            <span id="actualHoursDisplay" class="font-bold text-white text-sm">0 時間</span>
          </div>
          <div class="flex justify-between items-center text-slate-300">
            <span>基本給 (<span id="baseHoursSpan">0h</span> × ¥<span id="baseWageSpan">0</span>)</span>
            <span id="basePayDisplay" class="font-semibold text-white">¥0</span>
          </div>
          <div class="flex justify-between items-center text-slate-300">
            <span>残業手当 (25%増 / <span id="overtimeHoursSpan">0h</span>)</span>
            <span id="overtimePayDisplay" class="font-semibold text-yellow-300">¥0</span>
          </div>
          <div class="flex justify-between items-center text-slate-300">
            <span>深夜手当 (25%増 / <span id="nightHoursSpan">0h</span>)</span>
            <span id="nightPayDisplay" class="font-semibold text-indigo-200">¥0</span>
          </div>
          <div class="flex justify-between items-center text-slate-300">
            <span>交通費 + その他手当</span>
            <span id="transportPayDisplay" class="font-semibold text-emerald-300">¥0</span>
          </div>
          <div id="taxRow" class="flex justify-between items-center text-rose-300 hidden pt-1 border-t border-white/10">
            <span>源泉徴収税 (-10.21%)</span>
            <span id="taxPayDisplay" class="font-semibold text-rose-400">-¥0</span>
          </div>
        </div>

        <!-- Action Buttons -->
        <div class="grid grid-cols-1 sm:grid-cols-2 gap-3 pt-1">
          <button type="button" onclick="saveShiftToHistory()" class="w-full bg-indigo-600 hover:bg-indigo-500 text-white font-bold py-3 px-4 rounded-lg shadow-md transition flex items-center justify-center space-x-2 text-sm active:scale-[0.98]">
            <i class="fa-solid fa-bookmark"></i>
            <span>このシフトを履歴に保存</span>
          </button>
          <button type="button" onclick="copyShiftSummary()" class="w-full bg-slate-800 hover:bg-slate-700 text-slate-200 font-bold py-3 px-4 rounded-lg border border-slate-700 transition flex items-center justify-center space-x-2 text-sm active:scale-[0.98]">
            <i class="fa-solid fa-copy"></i>
            <span>結果テキストをコピー (LINE用)</span>
          </button>
        </div>
      </div>

    </section>

    <!-- TAB 2: SHIFT HISTORY VIEW -->
    <section id="view-history" class="space-y-4 hidden">
      <div class="bg-white rounded-xl shadow-sm border border-slate-200 p-4 sm:p-5">
        <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-3 mb-4 pb-3 border-b border-slate-100">
          <div>
            <h2 class="text-base font-bold text-slate-800 flex items-center">
              <i class="fa-solid fa-list-check text-indigo-600 mr-2"></i>保存済み勤務履歴
            </h2>
            <p class="text-xs text-slate-500">過去の給料計算結果を月別に確認できます</p>
          </div>

          <!-- Month Filter & Clear All -->
          <div class="flex flex-wrap items-center gap-2">
            <select id="historyCompanyFilter" onchange="renderHistoryTable()" class="px-3 py-1.5 border border-slate-300 rounded-lg text-xs font-semibold focus:outline-none">
              <!-- JS Populates companies -->
            </select>
            <select id="historyMonthFilter" onchange="renderHistoryTable()" class="px-3 py-1.5 border border-slate-300 rounded-lg text-xs font-semibold focus:outline-none">
              <!-- JS Populates months -->
            </select>
            <button onclick="clearHistoryConfirm()" class="px-2.5 py-1.5 text-xs text-rose-600 hover:bg-rose-50 rounded-lg transition border border-rose-200 font-medium">
              <i class="fa-solid fa-trash-can mr-1"></i>全消去
            </button>
          </div>
        </div>

        <!-- Monthly Summary Banner -->
        <div class="grid grid-cols-3 gap-3 mb-4 bg-slate-50 p-3 rounded-lg border border-slate-200 text-center">
          <div>
            <span class="block text-[11px] font-medium text-slate-500">月間合計支給額</span>
            <span id="histMonthTotalPay" class="text-base font-bold text-indigo-600">¥0</span>
          </div>
          <div>
            <span class="block text-[11px] font-medium text-slate-500">勤務日数</span>
            <span id="histMonthShiftCount" class="text-base font-bold text-slate-800">0 日</span>
          </div>
          <div>
            <span class="block text-[11px] font-medium text-slate-500">支給交通費</span>
            <span id="histMonthTransport" class="text-base font-bold text-emerald-600">¥0</span>
          </div>
        </div>

        <!-- History Item List Container -->
        <div id="historyList" class="space-y-3">
          <!-- JS Populates history cards -->
        </div>
      </div>
    </section>

    <!-- TAB 3: VENUE MANAGEMENT VIEW -->
    <section id="view-venues" class="space-y-4 hidden">
      <div class="bg-white rounded-xl shadow-sm border border-slate-200 p-4 sm:p-5">
        <div class="flex items-center justify-between border-b border-slate-100 pb-3 mb-4">
          <div>
            <h2 class="text-base font-bold text-slate-800 flex items-center">
              <i class="fa-solid fa-map-location-dot text-indigo-600 mr-2"></i>登録済み会場プリセット
            </h2>
            <p class="text-xs text-slate-500">よく行くライブハウス・ドーム等の交通費を登録してすぐ呼び出せます</p>
          </div>
          <button onclick="openVenueModal()" class="px-3 py-2 bg-indigo-600 hover:bg-indigo-500 text-white rounded-lg text-xs font-bold transition flex items-center space-x-1 shadow-sm">
            <i class="fa-solid fa-plus"></i>
            <span>新規登録</span>
          </button>
        </div>

        <div id="venueGrid" class="grid grid-cols-1 sm:grid-cols-2 gap-3">
          <!-- JS Populates Venue Cards -->
        </div>
      </div>
    </section>

    <!-- TAB 3.5: COMPANY MANAGEMENT VIEW -->
    <section id="view-companies" class="space-y-4 hidden">
      <div class="bg-white rounded-xl shadow-sm border border-slate-200 p-4 sm:p-5">
        <div class="flex items-center justify-between border-b border-slate-100 pb-3 mb-4">
          <div>
            <h2 class="text-base font-bold text-slate-800 flex items-center">
              <i class="fa-solid fa-building-user text-indigo-600 mr-2"></i>登録済み 派遣・所属会社
            </h2>
            <p class="text-xs text-slate-500">掛け持ち先の各会社ごとの基本時給や源泉徴収設定を保存できます</p>
          </div>
          <button onclick="openCompanyModal()" class="px-3 py-2 bg-indigo-600 hover:bg-indigo-500 text-white rounded-lg text-xs font-bold transition flex items-center space-x-1 shadow-sm">
            <i class="fa-solid fa-plus"></i>
            <span>会社を追加</span>
          </button>
        </div>

        <div id="companyGrid" class="grid grid-cols-1 sm:grid-cols-2 gap-3">
          <!-- JS Populates Company Cards -->
        </div>
      </div>
    </section>

    <!-- TAB 4: MONTHLY SUMMARY & ANALYTICS VIEW -->
    <section id="view-summary" class="space-y-4 hidden">
      <div class="bg-white rounded-xl shadow-sm border border-slate-200 p-4 sm:p-5 space-y-5">
        <div>
          <h2 class="text-base font-bold text-slate-800 flex items-center">
            <i class="fa-solid fa-chart-pie text-indigo-600 mr-2"></i>年間・月別収入分析
          </h2>
          <p class="text-xs text-slate-500">月ごとの推移や会社別・給料内訳の割合を確認できます</p>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
          <!-- Breakdown Stats -->
          <div class="border border-slate-200 rounded-lg p-4 bg-slate-50 space-y-3">
            <h3 class="text-xs font-bold text-slate-700 uppercase tracking-wider">総支給内訳 (全期間)</h3>
            <div id="analyticsBreakdown" class="space-y-2 text-xs">
              <!-- JS dynamic breakdown -->
            </div>
          </div>

          <!-- Company Breakdown -->
          <div class="border border-slate-200 rounded-lg p-4 bg-slate-50 space-y-3">
            <h3 class="text-xs font-bold text-slate-700 uppercase tracking-wider">派遣・所属会社別 収入</h3>
            <div id="analyticsCompanies" class="space-y-2 text-xs">
              <!-- JS dynamic company earnings -->
            </div>
          </div>

          <!-- Top Venues -->
          <div class="border border-slate-200 rounded-lg p-4 bg-slate-50 space-y-3">
            <h3 class="text-xs font-bold text-slate-700 uppercase tracking-wider">最も勤務が多い会場 TOP 5</h3>
            <div id="analyticsTopVenues" class="space-y-2 text-xs">
              <!-- JS dynamic venue rankings -->
            </div>
          </div>
        </div>
      </div>
    </section>

  </main>

  <!-- MODAL: ADD / EDIT VENUE -->
  <div id="venueModal" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 flex items-center justify-center p-4 hidden">
    <div class="bg-white rounded-xl shadow-2xl max-w-md w-full p-5 space-y-4 transform transition-all">
      <div class="flex justify-between items-center border-b border-slate-100 pb-3">
        <h3 id="venueModalTitle" class="font-bold text-slate-800 text-sm">新規会場の登録</h3>
        <button onclick="closeVenueModal()" class="text-slate-400 hover:text-slate-600 p-1">
          <i class="fa-solid fa-xmark text-lg"></i>
        </button>
      </div>

      <input type="hidden" id="editVenueIndex" value="-1">

      <div class="space-y-3 text-xs">
        <div>
          <label for="modalVenueName" class="block font-semibold text-slate-700 mb-1">会場名</label>
          <input type="text" id="modalVenueName" placeholder="例: 東京ドーム / 幕張メッセ" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 focus:outline-none">
        </div>

        <div>
          <label for="modalVenueCost" class="block font-semibold text-slate-700 mb-1">支給交通費 (円)</label>
          <input type="number" id="modalVenueCost" placeholder="800" min="0" step="10" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 focus:outline-none">
        </div>

        <div>
          <label for="modalVenueWage" class="block font-semibold text-slate-700 mb-1">この会場の基本時給 (任意・円)</label>
          <input type="number" id="modalVenueWage" placeholder="未設定時は会社の時給を優先" min="0" step="10" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 focus:outline-none">
        </div>
      </div>

      <div class="flex justify-end space-x-2 pt-2 border-t border-slate-100">
        <button type="button" onclick="closeVenueModal()" class="px-4 py-2 border border-slate-300 rounded-lg text-slate-600 font-semibold text-xs hover:bg-slate-50 transition">キャンセル</button>
        <button type="button" onclick="saveVenueModal()" class="px-4 py-2 bg-indigo-600 hover:bg-indigo-500 text-white rounded-lg font-bold text-xs transition">保存する</button>
      </div>
    </div>
  </div>

  <!-- MODAL: ADD / EDIT COMPANY -->
  <div id="companyModal" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 flex items-center justify-center p-4 hidden">
    <div class="bg-white rounded-xl shadow-2xl max-w-md w-full p-5 space-y-4 transform transition-all">
      <div class="flex justify-between items-center border-b border-slate-100 pb-3">
        <h3 id="companyModalTitle" class="font-bold text-slate-800 text-sm">新規派遣・所属会社の登録</h3>
        <button onclick="closeCompanyModal()" class="text-slate-400 hover:text-slate-600 p-1">
          <i class="fa-solid fa-xmark text-lg"></i>
        </button>
      </div>

      <input type="hidden" id="editCompanyIndex" value="-1">

      <div class="space-y-3 text-xs">
        <div>
          <label for="modalCompanyName" class="block font-semibold text-slate-700 mb-1">会社名 / 派遣会社名</label>
          <input type="text" id="modalCompanyName" placeholder="例: Aイベントプロモーション / Bスタッフサービス" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 focus:outline-none">
        </div>

        <div>
          <label for="modalCompanyWage" class="block font-semibold text-slate-700 mb-1">基本時給 (円)</label>
          <input type="number" id="modalCompanyWage" placeholder="1300" min="0" step="10" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 focus:outline-none">
        </div>

        <div class="pt-1">
          <label class="inline-flex items-center cursor-pointer">
            <input type="checkbox" id="modalCompanyTax" class="rounded border-slate-300 text-indigo-600 focus:ring-indigo-500 h-4 w-4">
            <span class="ml-2 font-medium text-slate-700">デフォルトで源泉徴収税を引く (10.21%)</span>
          </label>
        </div>

        <div>
          <label for="modalCompanyNote" class="block font-semibold text-slate-700 mb-1">備考 / メモ (支給日や交通費の取り決めなど)</label>
          <input type="text" id="modalCompanyNote" placeholder="例: 交通費全額支給 / 翌月15日振込" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 focus:outline-none">
        </div>
      </div>

      <div class="flex justify-end space-x-2 pt-2 border-t border-slate-100">
        <button type="button" onclick="closeCompanyModal()" class="px-4 py-2 border border-slate-300 rounded-lg text-slate-600 font-semibold text-xs hover:bg-slate-50 transition">キャンセル</button>
        <button type="button" onclick="saveCompanyModal()" class="px-4 py-2 bg-indigo-600 hover:bg-indigo-500 text-white rounded-lg font-bold text-xs transition">保存する</button>
      </div>
    </div>
  </div>

  <!-- MODAL: GENERIC CONFIRMATION (REPLACES ALERT/CONFIRM) -->
  <div id="confirmModal" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 flex items-center justify-center p-4 hidden">
    <div class="bg-white rounded-xl shadow-2xl max-w-sm w-full p-5 space-y-4 text-center">
      <div class="w-12 h-12 bg-amber-100 text-amber-600 rounded-full flex items-center justify-center mx-auto text-xl">
        <i class="fa-solid fa-triangle-exclamation"></i>
      </div>
      <div>
        <h3 id="confirmTitle" class="font-bold text-slate-800 text-base">確認</h3>
        <p id="confirmMessage" class="text-xs text-slate-500 mt-1">本当に実行しますか？</p>
      </div>
      <div class="flex justify-center space-x-2 pt-2">
        <button type="button" onclick="closeConfirmModal(false)" class="px-4 py-2 border border-slate-300 rounded-lg text-slate-600 font-semibold text-xs hover:bg-slate-50 transition">キャンセル</button>
        <button type="button" id="confirmOkBtn" onclick="closeConfirmModal(true)" class="px-4 py-2 bg-rose-600 hover:bg-rose-500 text-white rounded-lg font-bold text-xs transition">実行する</button>
      </div>
    </div>
  </div>

  <!-- TOAST NOTIFICATION POPUP -->
  <div id="toast" class="fixed bottom-5 right-5 bg-slate-900 text-white text-xs font-semibold px-4 py-3 rounded-lg shadow-xl z-50 transform translate-y-10 opacity-0 transition-all duration-300 flex items-center space-x-2">
    <i id="toastIcon" class="fa-solid fa-circle-check text-emerald-400"></i>
    <span id="toastMsg">通知メッセージ</span>
  </div>

  <script>
    // Default Preset Venues
    const defaultVenues = [
      { name: "東京ドーム", cost: 500, defaultWage: 1200 },
      { name: "幕張メッセ", cost: 1200, defaultWage: 1200 },
      { name: "横浜アリーナ", cost: 800, defaultWage: 1200 },
      { name: "さいたまスーパーアリーナ", cost: 950, defaultWage: 1200 },
      { name: "Kアリーナ横浜", cost: 850, defaultWage: 1250 },
      { name: "国立代々木競技場", cost: 400, defaultWage: 1200 }
    ];

    // Default Companies / Agencies
    const defaultCompanies = [
      { name: "Aイベントプロモーション", defaultWage: 1200, taxEnabled: false, note: "交通費実費支給" },
      { name: "Bスタッフサービス", defaultWage: 1300, taxEnabled: true, note: "翌月15日払い / 源泉徴収あり" }
    ];

    let currentInputMode = 'time'; // 'time' or 'hours'
    let currentCalculationResult = null;
    let pendingConfirmAction = null;

    window.onload = function() {
      // Set default date to today
      document.getElementById('shiftDate').value = new Date().toISOString().split('T')[0];
      
      loadCompanies();
      loadVenues();
      renderHistoryTable();
      updateHistoryBadge();
      calculateSalary();
    };

    // Tab Navigation
    function switchTab(tabName) {
      const tabs = ['calc', 'history', 'venues', 'companies', 'summary'];
      tabs.forEach(t => {
        const view = document.getElementById(`view-${t}`);
        const btn = document.getElementById(`tab-${t}`);
        if (t === tabName) {
          view.classList.remove('hidden');
          btn.classList.add('border-yellow-400', 'text-white');
          btn.classList.remove('border-transparent', 'text-indigo-200');
        } else {
          view.classList.add('hidden');
          btn.classList.remove('border-yellow-400', 'text-white');
          btn.classList.add('border-transparent', 'text-indigo-200');
        }
      });

      if (tabName === 'history') renderHistoryTable();
      if (tabName === 'venues') renderVenueGrid();
      if (tabName === 'companies') renderCompanyGrid();
      if (tabName === 'summary') renderAnalytics();
    }

    // --- COMPANY MANAGEMENT FUNCTIONS ---
    function getCompanies() {
      return JSON.parse(localStorage.getItem('liveCompanies')) || defaultCompanies;
    }

    function saveCompanies(companies) {
      localStorage.setItem('liveCompanies', JSON.stringify(companies));
    }

    function loadCompanies() {
      const companies = getCompanies();
      const select = document.getElementById('companySelect');
      select.innerHTML = '<option value="custom">-- 指定なし / 直接入力 --</option>';

      companies.forEach((c, idx) => {
        const option = document.createElement('option');
        option.value = idx;
        option.innerText = `${c.name} (時給¥${c.defaultWage.toLocaleString()})`;
        select.appendChild(option);
      });
    }

    function onCompanySelectChange() {
      const select = document.getElementById('companySelect');
      const baseWageInput = document.getElementById('baseWage');
      const taxCheckbox = document.getElementById('taxDeduction');
      const companies = getCompanies();

      if (select.value !== 'custom') {
        const company = companies[select.value];
        if (company) {
          if (company.defaultWage) {
            baseWageInput.value = company.defaultWage;
          }
          taxCheckbox.checked = !!company.taxEnabled;
        }
      }
      calculateSalary();
    }

    function openCompanyModal(index = -1) {
      document.getElementById('editCompanyIndex').value = index;
      if (index >= 0) {
        const comp = getCompanies()[index];
        document.getElementById('companyModalTitle').innerText = "会社の編集";
        document.getElementById('modalCompanyName').value = comp.name;
        document.getElementById('modalCompanyWage').value = comp.defaultWage || 1200;
        document.getElementById('modalCompanyTax').checked = !!comp.taxEnabled;
        document.getElementById('modalCompanyNote').value = comp.note || '';
      } else {
        document.getElementById('companyModalTitle').innerText = "新規会社・派遣先の登録";
        document.getElementById('modalCompanyName').value = '';
        document.getElementById('modalCompanyWage').value = 1200;
        document.getElementById('modalCompanyTax').checked = false;
        document.getElementById('modalCompanyNote').value = '';
      }
      document.getElementById('companyModal').classList.remove('hidden');
    }

    function closeCompanyModal() {
      document.getElementById('companyModal').classList.add('hidden');
    }

    function saveCompanyModal() {
      const name = document.getElementById('modalCompanyName').value.trim();
      const defaultWage = parseInt(document.getElementById('modalCompanyWage').value) || 0;
      const taxEnabled = document.getElementById('modalCompanyTax').checked;
      const note = document.getElementById('modalCompanyNote').value.trim();

      if (!name) {
        showToast('会社名を入力してください', 'error');
        return;
      }

      const companies = getCompanies();
      const index = parseInt(document.getElementById('editCompanyIndex').value);

      if (index >= 0) {
        companies[index] = { name, defaultWage, taxEnabled, note };
        showToast('会社情報を更新しました');
      } else {
        companies.push({ name, defaultWage, taxEnabled, note });
        showToast('新しい会社を追加しました');
      }

      saveCompanies(companies);
      loadCompanies();
      renderCompanyGrid();
      closeCompanyModal();
    }

    function renderCompanyGrid() {
      const companies = getCompanies();
      const grid = document.getElementById('companyGrid');
      grid.innerHTML = '';

      companies.forEach((c, idx) => {
        const card = document.createElement('div');
        card.className = "bg-slate-50 border border-slate-200 rounded-lg p-3.5 flex justify-between items-center hover:border-indigo-300 transition shadow-sm";
        card.innerHTML = `
          <div>
            <h4 class="font-bold text-slate-800 text-sm flex items-center">
              <i class="fa-solid fa-building-user text-indigo-500 mr-1.5"></i>${c.name}
            </h4>
            <p class="text-xs text-slate-500 mt-0.5">
              基本時給: <span class="font-semibold text-slate-700">¥${c.defaultWage.toLocaleString()}</span>
              | 源泉徴収: <span class="font-semibold ${c.taxEnabled ? 'text-rose-600' : 'text-slate-600'}">${c.taxEnabled ? 'あり(10.21%)' : 'なし'}</span>
            </p>
            ${c.note ? `<p class="text-[11px] text-slate-400 mt-1"><i class="fa-regular fa-note-sticky mr-1"></i>${c.note}</p>` : ''}
          </div>
          <div class="flex items-center space-x-1 shrink-0">
            <button onclick="openCompanyModal(${idx})" class="p-1.5 text-slate-500 hover:text-indigo-600 rounded transition" title="編集">
              <i class="fa-solid fa-pen-to-square"></i>
            </button>
            <button onclick="deleteCompanyConfirm(${idx})" class="p-1.5 text-slate-500 hover:text-rose-600 rounded transition" title="削除">
              <i class="fa-solid fa-trash-can"></i>
            </button>
          </div>
        `;
        grid.appendChild(card);
      });
    }

    function deleteCompanyConfirm(index) {
      const comp = getCompanies()[index];
      showConfirmModal("会社の削除", `「${comp.name}」をプリセットから削除しますか？`, () => {
        const companies = getCompanies();
        companies.splice(index, 1);
        saveCompanies(companies);
        loadCompanies();
        renderCompanyGrid();
        showToast("会社を削除しました");
      });
    }

    // Toggle Input Mode (Time picker vs Direct Hours)
    function setInputMode(mode) {
      currentInputMode = mode;
      const timeBtn = document.getElementById('modeTimeBtn');
      const hoursBtn = document.getElementById('modeHoursBtn');
      const timeSec = document.getElementById('timeInputSection');
      const hoursSec = document.getElementById('hoursInputSection');

      if (mode === 'time') {
        timeBtn.className = "px-3 py-1.5 rounded-md bg-white text-indigo-600 shadow-sm transition";
        hoursBtn.className = "px-3 py-1.5 rounded-md text-slate-600 hover:text-slate-900 transition";
        timeSec.classList.remove('hidden');
        hoursSec.classList.add('hidden');
      } else {
        hoursBtn.className = "px-3 py-1.5 rounded-md bg-white text-indigo-600 shadow-sm transition";
        timeBtn.className = "px-3 py-1.5 rounded-md text-slate-600 hover:text-slate-900 transition";
        hoursSec.classList.remove('hidden');
        timeSec.classList.add('hidden');
      }
      calculateSalary();
    }

    function calculateSalary() {
      const baseWage = parseFloat(document.getElementById('baseWage').value) || 0;
      const transport = parseFloat(document.getElementById('transport').value) || 0;
      const allowance = parseFloat(document.getElementById('allowance').value) || 0;
      const taxEnabled = document.getElementById('taxDeduction').checked;
      const overtimeEnabled = document.getElementById('overtimeToggle').checked;
      const eventArtist = document.getElementById('eventArtist').value.trim();

      // Get current company name
      const companySelect = document.getElementById('companySelect');
      let companyName = "指定なし";
      if (companySelect.value !== 'custom') {
        const savedCompanies = getCompanies();
        companyName = savedCompanies[companySelect.value]?.name || "選択会社";
      }

      let actualHours = 0;
      let overtimeHours = 0;
      let nightHours = 0;

      if (currentInputMode === 'time') {
        const startVal = document.getElementById('startTime').value;
        const endVal = document.getElementById('endTime').value;
        const breakVal = parseFloat(document.getElementById('breakHoursTime').value) || 0;

        if (startVal && endVal) {
          const [startH, startM] = startVal.split(':').map(Number);
          const [endH, endM] = endVal.split(':').map(Number);

          let startTotal = startH * 60 + startM;
          let endTotal = endH * 60 + endM;

          if (endTotal <= startTotal) {
            endTotal += 24 * 60; // Next day shift
          }

          const totalMinutes = endTotal - startTotal;
          const totalWorkMins = Math.max(0, totalMinutes - (breakVal * 60));
          actualHours = totalWorkMins / 60;

          // Legal Overtime (> 8 Hours)
          if (overtimeEnabled && actualHours > 8) {
            overtimeHours = actualHours - 8;
          }

          // Midnight Hours Calculation (22:00 to 29:00 / 05:00)
          let nightMins = 0;
          for (let m = startTotal; m < endTotal; m++) {
            const timeOfDay = m % (24 * 60);
            if (timeOfDay >= 22 * 60 || timeOfDay < 5 * 60) {
              nightMins++;
            }
          }
          nightHours = Math.min(actualHours, nightMins / 60);
        }
      } else {
        const workHours = parseFloat(document.getElementById('workHours').value) || 0;
        const breakHours = parseFloat(document.getElementById('breakHours').value) || 0;
        actualHours = Math.max(0, workHours - breakHours);
        
        if (overtimeEnabled && actualHours > 8) {
          overtimeHours = actualHours - 8;
        }
        nightHours = parseFloat(document.getElementById('nightHours').value) || 0;
      }

      // Base hours without extra overtime multiplier
      const regularHours = actualHours - overtimeHours;

      // Wage Calculation
      const basePay = Math.floor(actualHours * baseWage);
      const overtimePay = Math.floor(overtimeHours * baseWage * 0.25);
      const nightPay = Math.floor(nightHours * baseWage * 0.25);
      const subtotalWage = basePay + overtimePay + nightPay + allowance;

      // Withholding Tax Calculation (10.21%)
      let taxAmount = 0;
      if (taxEnabled) {
        taxAmount = Math.floor(subtotalWage * 0.1021);
      }

      const totalPay = subtotalWage - taxAmount + transport;
      const equivalentHourly = actualHours > 0 ? Math.round(totalPay / actualHours) : 0;

      // Get current venue name
      const venueSelect = document.getElementById('venueSelect');
      let venueName = "直接入力";
      if (venueSelect.value !== 'custom') {
        const savedVenues = getVenues();
        venueName = savedVenues[venueSelect.value]?.name || "選択会場";
      }

      // Store in memory
      currentCalculationResult = {
        date: document.getElementById('shiftDate').value,
        companyName: companyName,
        eventArtist: eventArtist,
        venueName: venueName,
        baseWage: baseWage,
        actualHours: actualHours,
        regularHours: regularHours,
        overtimeHours: overtimeHours,
        nightHours: nightHours,
        basePay: basePay,
        overtimePay: overtimePay,
        nightPay: nightPay,
        transport: transport,
        allowance: allowance,
        taxAmount: taxAmount,
        totalPay: totalPay
      };

      // Update UI
      document.getElementById('totalPayDisplay').innerText = `¥${totalPay.toLocaleString()}`;
      document.getElementById('hourlyRateEquivalent').innerText = `実質時給: ¥${equivalentHourly.toLocaleString()} / 時`;
      document.getElementById('resVenueBadge').innerHTML = `<i class="fa-solid fa-building mr-1"></i>${venueName}`;

      const companyBadge = document.getElementById('resCompanyBadge');
      if (companyName && companyName !== "指定なし") {
        document.getElementById('resCompanyText').innerText = companyName;
        companyBadge.classList.remove('hidden');
      } else {
        companyBadge.classList.add('hidden');
      }

      const artistBadge = document.getElementById('resArtistBadge');
      if (eventArtist) {
        document.getElementById('resArtistText').innerText = eventArtist;
        artistBadge.classList.remove('hidden');
      } else {
        artistBadge.classList.add('hidden');
      }

      document.getElementById('actualHoursDisplay').innerText = `${actualHours.toFixed(2)} 時間`;
      document.getElementById('baseHoursSpan').innerText = `${actualHours.toFixed(1)}h`;
      document.getElementById('baseWageSpan').innerText = baseWage.toLocaleString();
      document.getElementById('basePayDisplay').innerText = `¥${basePay.toLocaleString()}`;

      document.getElementById('overtimeHoursSpan').innerText = `${overtimeHours.toFixed(1)}h`;
      document.getElementById('overtimePayDisplay').innerText = `¥${overtimePay.toLocaleString()}`;

      document.getElementById('nightHoursSpan').innerText = `${nightHours.toFixed(1)}h`;
      document.getElementById('nightPayDisplay').innerText = `¥${nightPay.toLocaleString()}`;

      document.getElementById('transportPayDisplay').innerText = `¥${(transport + allowance).toLocaleString()}`;

      const taxRow = document.getElementById('taxRow');
      if (taxEnabled) {
        taxRow.classList.remove('hidden');
        document.getElementById('taxPayDisplay').innerText = `-¥${taxAmount.toLocaleString()}`;
      } else {
        taxRow.classList.add('hidden');
      }
    }

    function getVenues() {
      return JSON.parse(localStorage.getItem('liveVenues')) || defaultVenues;
    }

    function saveVenues(venues) {
      localStorage.setItem('liveVenues', JSON.stringify(venues));
    }

    function loadVenues() {
      const venues = getVenues();
      const select = document.getElementById('venueSelect');
      select.innerHTML = '<option value="custom">-- 直接入力 / 指定なし --</option>';

      venues.forEach((v, idx) => {
        const option = document.createElement('option');
        option.value = idx;
        option.innerText = `${v.name} (交通費: ¥${v.cost.toLocaleString()})`;
        select.appendChild(option);
      });
    }

    function onVenueSelectChange() {
      const select = document.getElementById('venueSelect');
      const transportInput = document.getElementById('transport');
      const baseWageInput = document.getElementById('baseWage');
      const venues = getVenues();

      if (select.value !== 'custom') {
        const venue = venues[select.value];
        if (venue) {
          transportInput.value = venue.cost;
          if (venue.defaultWage) {
            baseWageInput.value = venue.defaultWage;
          }
        }
      }
      calculateSalary();
    }

    function openVenueModal(index = -1) {
      document.getElementById('editVenueIndex').value = index;
      if (index >= 0) {
        const venue = getVenues()[index];
        document.getElementById('venueModalTitle').innerText = "会場情報の編集";
        document.getElementById('modalVenueName').value = venue.name;
        document.getElementById('modalVenueCost').value = venue.cost;
        document.getElementById('modalVenueWage').value = venue.defaultWage || '';
      } else {
        document.getElementById('venueModalTitle').innerText = "新規会場の登録";
        document.getElementById('modalVenueName').value = '';
        document.getElementById('modalVenueCost').value = '';
        document.getElementById('modalVenueWage').value = '';
      }
      document.getElementById('venueModal').classList.remove('hidden');
    }

    function closeVenueModal() {
      document.getElementById('venueModal').classList.add('hidden');
    }

    function saveVenueModal() {
      const name = document.getElementById('modalVenueName').value.trim();
      const cost = parseInt(document.getElementById('modalVenueCost').value) || 0;
      const defaultWage = parseInt(document.getElementById('modalVenueWage').value) || 0;

      if (!name) {
        showToast('会場名を入力してください', 'error');
        return;
      }

      const venues = getVenues();
      const index = parseInt(document.getElementById('editVenueIndex').value);

      if (index >= 0) {
        venues[index] = { name, cost, defaultWage };
        showToast('会場情報を更新しました');
      } else {
        venues.push({ name, cost, defaultWage });
        showToast('新しい会場を追加しました');
      }

      saveVenues(venues);
      loadVenues();
      renderVenueGrid();
      closeVenueModal();
    }

    function renderVenueGrid() {
      const venues = getVenues();
      const grid = document.getElementById('venueGrid');
      grid.innerHTML = '';

      venues.forEach((v, idx) => {
        const card = document.createElement('div');
        card.className = "bg-slate-50 border border-slate-200 rounded-lg p-3.5 flex justify-between items-center hover:border-indigo-300 transition shadow-sm";
        card.innerHTML = `
          <div>
            <h4 class="font-bold text-slate-800 text-sm flex items-center">
              <i class="fa-solid fa-building text-indigo-500 mr-1.5"></i>${v.name}
            </h4>
            <p class="text-xs text-slate-500 mt-0.5">
              交通費: <span class="font-semibold text-slate-700">¥${v.cost.toLocaleString()}</span>
              ${v.defaultWage ? ` | 基本時給: ¥${v.defaultWage.toLocaleString()}` : ''}
            </p>
          </div>
          <div class="flex items-center space-x-1">
            <button onclick="openVenueModal(${idx})" class="p-1.5 text-slate-500 hover:text-indigo-600 rounded transition" title="編集">
              <i class="fa-solid fa-pen-to-square"></i>
            </button>
            <button onclick="deleteVenueConfirm(${idx})" class="p-1.5 text-slate-500 hover:text-rose-600 rounded transition" title="削除">
              <i class="fa-solid fa-trash-can"></i>
            </button>
          </div>
        `;
        grid.appendChild(card);
      });
    }

    function deleteVenueConfirm(index) {
      const venue = getVenues()[index];
      showConfirmModal("会場の削除", `「${venue.name}」をプリセットから削除しますか？`, () => {
        const venues = getVenues();
        venues.splice(index, 1);
        saveVenues(venues);
        loadVenues();
        renderVenueGrid();
        showToast("会場を削除しました");
      });
    }

    function getHistory() {
      return JSON.parse(localStorage.getItem('liveShiftHistory')) || [];
    }

    function saveHistory(history) {
      localStorage.setItem('liveShiftHistory', JSON.stringify(history));
      updateHistoryBadge();
    }

    function updateHistoryBadge() {
      const history = getHistory();
      const badge = document.getElementById('historyBadge');
      if (history.length > 0) {
        badge.innerText = history.length;
        badge.classList.remove('hidden');
      } else {
        badge.classList.add('hidden');
      }
    }

    function saveShiftToHistory() {
      if (!currentCalculationResult) return;
      const history = getHistory();
      history.unshift({ ...currentCalculationResult, id: Date.now() });
      saveHistory(history);
      showToast("勤務履歴に保存しました");
    }

    function renderHistoryTable() {
      const history = getHistory();
      const listContainer = document.getElementById('historyList');
      const monthFilter = document.getElementById('historyMonthFilter');
      const companyFilter = document.getElementById('historyCompanyFilter');

      if (!listContainer || !monthFilter || !companyFilter) return;

      // Populate Company Filter Dropdown
      const companies = [...new Set(history.map(item => item.companyName || '指定なし'))].sort();
      const selectedCompany = companyFilter.value || 'ALL';
      companyFilter.innerHTML = '<option value="ALL">すべての会社</option>' + companies.map(c => `<option value="${c}" ${c === selectedCompany ? 'selected' : ''}>${c}</option>`).join('');

      // Populate Month Filter Dropdown
      const months = [...new Set(history.map(item => item.date ? item.date.slice(0, 7) : ''))].filter(Boolean).sort().reverse();
      const selectedMonth = monthFilter.value || (months.length > 0 ? months[0] : '');
      monthFilter.innerHTML = months.map(m => `<option value="${m}" ${m === selectedMonth ? 'selected' : ''}>${m}</option>`).join('') || '<option value="">データなし</option>';

      // Filter by Month & Company
      let filteredHistory = history;
      if (selectedMonth) {
        filteredHistory = filteredHistory.filter(item => item.date && item.date.startsWith(selectedMonth));
      }
      if (selectedCompany !== 'ALL') {
        filteredHistory = filteredHistory.filter(item => (item.companyName || '指定なし') === selectedCompany);
      }

      // Stats
      const totalMonthPay = filteredHistory.reduce((sum, item) => sum + item.totalPay, 0);
      const totalTransport = filteredHistory.reduce((sum, item) => sum + item.transport, 0);
      
      const totalPayEl = document.getElementById('histMonthTotalPay');
      const shiftCountEl = document.getElementById('histMonthShiftCount');
      const transportEl = document.getElementById('histMonthTransport');

      if (totalPayEl) totalPayEl.innerText = `¥${totalMonthPay.toLocaleString()}`;
      if (shiftCountEl) shiftCountEl.innerText = `${filteredHistory.length} 日`;
      if (transportEl) transportEl.innerText = `¥${totalTransport.toLocaleString()}`;

      if (filteredHistory.length === 0) {
        listContainer.innerHTML = `
          <div class="text-center py-8 text-slate-400 text-xs">
            <i class="fa-solid fa-calendar-xmark text-3xl mb-2 block"></i>
            表示できる勤務履歴がありません
          </div>
        `;
        return;
      }

      listContainer.innerHTML = filteredHistory.map(item => `
        <div class="border border-slate-200 rounded-lg p-3 sm:p-4 bg-slate-50 hover:bg-white hover:border-indigo-200 transition space-y-2">
          <div class="flex justify-between items-start">
            <div>
              <div class="flex flex-wrap items-center gap-2">
                <span class="font-bold text-slate-800 text-sm">${item.date || '日付未設定'}</span>
                ${item.companyName && item.companyName !== '指定なし' ? `<span class="bg-emerald-100 text-emerald-800 text-xs px-2 py-0.5 rounded font-bold"><i class="fa-solid fa-building-user mr-1"></i>${item.companyName}</span>` : ''}
                ${item.eventArtist ? `<span class="bg-amber-100 text-amber-800 text-xs px-2 py-0.5 rounded font-bold"><i class="fa-solid fa-music mr-1"></i>${item.eventArtist}</span>` : ''}
                <span class="bg-indigo-100 text-indigo-700 text-xs px-2 py-0.5 rounded font-semibold">${item.venueName}</span>
              </div>
              <p class="text-xs text-slate-500 mt-1">
                実働: <span class="font-medium text-slate-700">${item.actualHours.toFixed(1)}h</span>
                (基本: ¥${item.baseWage}/h ${item.overtimeHours > 0 ? `| 残業: ${item.overtimeHours.toFixed(1)}h` : ''} ${item.nightHours > 0 ? `| 深夜: ${item.nightHours.toFixed(1)}h` : ''})
              </p>
            </div>
            <div class="text-right">
              <span class="text-base font-extrabold text-indigo-700">¥${item.totalPay.toLocaleString()}</span>
              <button onclick="deleteHistoryItem(${item.id})" class="block ml-auto text-xs text-slate-400 hover:text-rose-500 mt-1" title="削除">
                <i class="fa-solid fa-trash"></i>
              </button>
            </div>
          </div>
        </div>
      `).join('');
    }

    function deleteHistoryItem(id) {
      showConfirmModal("履歴の削除", "この勤務記録を削除しますか？", () => {
        let history = getHistory();
        history = history.filter(item => item.id !== id);
        saveHistory(history);
        renderHistoryTable();
        showToast("記録を削除しました");
      });
    }

    function clearHistoryConfirm() {
      showConfirmModal("履歴の全消去", "保存されたすべての勤務履歴を消去しますか？この操作は取り消せません。", () => {
        localStorage.removeItem('liveShiftHistory');
        updateHistoryBadge();
        renderHistoryTable();
        showToast("すべての履歴をクリアしました");
      });
    }

    function renderAnalytics() {
      const history = getHistory();
      const breakdownEl = document.getElementById('analyticsBreakdown');
      const companiesEl = document.getElementById('analyticsCompanies');
      const topVenuesEl = document.getElementById('analyticsTopVenues');

      if (history.length === 0) {
        breakdownEl.innerHTML = '<p class="text-slate-400 text-center py-4">データがまだ登録されていません</p>';
        companiesEl.innerHTML = '<p class="text-slate-400 text-center py-4">データがまだ登録されていません</p>';
        topVenuesEl.innerHTML = '<p class="text-slate-400 text-center py-4">データがまだ登録されていません</p>';
        return;
      }

      const totalBase = history.reduce((sum, item) => sum + item.basePay, 0);
      const totalOvertime = history.reduce((sum, item) => sum + item.overtimePay, 0);
      const totalNight = history.reduce((sum, item) => sum + item.nightPay, 0);
      const totalTransport = history.reduce((sum, item) => sum + item.transport + item.allowance, 0);
      const grandTotal = totalBase + totalOvertime + totalNight + totalTransport;

      breakdownEl.innerHTML = `
        <div class="space-y-2">
          <div class="flex justify-between"><span>基本給合計</span><span class="font-bold">¥${totalBase.toLocaleString()} (${Math.round(totalBase/grandTotal*100||0)}%)</span></div>
          <div class="w-full bg-slate-200 rounded-full h-1.5"><div class="bg-indigo-600 h-1.5 rounded-full" style="width: ${Math.round(totalBase/grandTotal*100||0)}%"></div></div>
          
          <div class="flex justify-between"><span>残業・深夜手当</span><span class="font-bold">¥${(totalOvertime + totalNight).toLocaleString()} (${Math.round((totalOvertime+totalNight)/grandTotal*100||0)}%)</span></div>
          <div class="w-full bg-slate-200 rounded-full h-1.5"><div class="bg-yellow-500 h-1.5 rounded-full" style="width: ${Math.round((totalOvertime+totalNight)/grandTotal*100||0)}%"></div></div>

          <div class="flex justify-between"><span>交通費・その他</span><span class="font-bold">¥${totalTransport.toLocaleString()} (${Math.round(totalTransport/grandTotal*100||0)}%)</span></div>
          <div class="w-full bg-slate-200 rounded-full h-1.5"><div class="bg-emerald-500 h-1.5 rounded-full" style="width: ${Math.round(totalTransport/grandTotal*100||0)}%"></div></div>
        </div>
      `;

      // Company Breakdown Calculation
      const companyPay = {};
      history.forEach(item => {
        const c = item.companyName || "指定なし";
        companyPay[c] = (companyPay[c] || 0) + item.totalPay;
      });

      const sortedCompanies = Object.entries(companyPay).sort((a,b) => b[1] - a[1]);

      companiesEl.innerHTML = sortedCompanies.map(([name, pay]) => `
        <div class="flex justify-between items-center py-1.5 border-b border-slate-200/60 last:border-0">
          <span class="font-medium text-slate-700 flex items-center">
            <i class="fa-solid fa-building-user text-emerald-600 mr-1.5"></i>${name}
          </span>
          <span class="font-bold text-slate-800">¥${pay.toLocaleString()}</span>
        </div>
      `).join('');

      // Top Venues Calculation
      const venueCounts = {};
      history.forEach(item => {
        const v = item.venueName || "未設定";
        venueCounts[v] = (venueCounts[v] || 0) + 1;
      });

      const sortedVenues = Object.entries(venueCounts).sort((a,b) => b[1] - a[1]).slice(0, 5);

      topVenuesEl.innerHTML = sortedVenues.map(([name, count]) => `
        <div class="flex justify-between items-center py-1.5 border-b border-slate-200/60 last:border-0">
          <span class="font-medium text-slate-700"><i class="fa-solid fa-location-dot text-indigo-500 mr-1.5"></i>${name}</span>
          <span class="font-bold bg-indigo-100 text-indigo-800 text-xs px-2 py-0.5 rounded-full">${count} 回</span>
        </div>
      `).join('');
    }

    function copyShiftSummary() {
      if (!currentCalculationResult) return;
      const res = currentCalculationResult;
      const text = `【ライブスタッフ給料概算】
■勤務日: ${res.date}${res.companyName && res.companyName !== '指定なし' ? `\n■派遣/所属会社: ${res.companyName}` : ''}${res.eventArtist ? `\n■イベント/アーティスト: ${res.eventArtist}` : ''}
■会場名: ${res.venueName}
■実働時間: ${res.actualHours.toFixed(1)} 時間 (基本時給: ¥${res.baseWage.toLocaleString()})
------------------
基本給: ¥${res.basePay.toLocaleString()}
残業手当: ¥${res.overtimePay.toLocaleString()}
深夜手当: ¥${res.nightPay.toLocaleString()}
交通費等: ¥${(res.transport + res.allowance).toLocaleString()}
${res.taxAmount > 0 ? `源泉徴収: -¥${res.taxAmount.toLocaleString()}\n` : ''}------------------
■合計支給額: ¥${res.totalPay.toLocaleString()}`;

      // Fallback copy logic for iframe safety
      const dummy = document.createElement("textarea");
      document.body.appendChild(dummy);
      dummy.value = text;
      dummy.select();
      document.execCommand("copy");
      document.body.removeChild(dummy);

      showToast("LINE送信用テキストをコピーしました！");
    }

    function exportDataCSV() {
      const history = getHistory();
      if (history.length === 0) {
        showToast("エクスポートする履歴データがありません", "error");
        return;
      }

      let csv = "\uFEFF日付,派遣/所属会社,アーティスト/イベント名,会場名,実働時間,基本時給,基本給,残業手当,深夜手当,交通費,手当,源泉徴収,合計支給額\n";
      history.forEach(item => {
        csv += `"${item.date}","${item.companyName || '指定なし'}","${item.eventArtist || ''}","${item.venueName}",${item.actualHours},${item.baseWage},${item.basePay},${item.overtimePay},${item.nightPay},${item.transport},${item.allowance},${item.taxAmount},${item.totalPay}\n`;
      });

      const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' });
      const link = document.createElement("a");
      const url = URL.createObjectURL(blob);
      link.setAttribute("href", url);
      link.setAttribute("download", `live_staff_salary_${new Date().toISOString().slice(0,10)}.csv`);
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);

      showToast("CSVファイルをダウンロードしました");
    }

    function showToast(msg, type = 'success') {
      const toast = document.getElementById('toast');
      const toastMsg = document.getElementById('toastMsg');
      const toastIcon = document.getElementById('toastIcon');

      toastMsg.innerText = msg;
      if (type === 'error') {
        toastIcon.className = "fa-solid fa-circle-exclamation text-rose-400";
      } else {
        toastIcon.className = "fa-solid fa-circle-check text-emerald-400";
      }

      toast.classList.remove('translate-y-10', 'opacity-0');
      toast.classList.add('translate-y-0', 'opacity-100');

      setTimeout(() => {
        toast.classList.remove('translate-y-0', 'opacity-100');
        toast.classList.add('translate-y-10', 'opacity-0');
      }, 3000);
    }

    function showConfirmModal(title, msg, onOk) {
      document.getElementById('confirmTitle').innerText = title;
      document.getElementById('confirmMessage').innerText = msg;
      pendingConfirmAction = onOk;
      document.getElementById('confirmModal').classList.remove('hidden');
    }

    function closeConfirmModal(execute) {
      document.getElementById('confirmModal').classList.add('hidden');
      if (execute && typeof pendingConfirmAction === 'function') {
        pendingConfirmAction();
      }
      pendingConfirmAction = null;
    }
  </script>
</body>
</html>
