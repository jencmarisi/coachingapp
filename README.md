<!DOCTYPE html>
<html lang="en" class="h-full bg-slate-50">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ChallengeSync - Challenge-Based Coaching Platform</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Chart.js for Admin Analytics -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        brand: {
                            50: '#f0f7ff',
                            100: '#e0effe',
                            500: '#2563eb',
                            600: '#1d4ed8',
                            700: '#1e40af',
                            900: '#1e3a8a',
                        }
                    }
                }
            }
        }
    </script>
    <style>
        .cbc-step-active { border-color: #2563eb; background-color: #eff6ff; }
        .tab-active { border-bottom: 2px solid #2563eb; color: #2563eb; font-weight: 600; }
        /* Custom scrollbar */
        ::-webkit-scrollbar { width: 6px; height: 6px; }
        ::-webkit-scrollbar-track { background: #f1f5f9; }
        ::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 4px; }
    </style>
</head>
<body class="h-full text-slate-800 flex flex-col font-sans">

    <!-- Top Navigation Header -->
    <header class="bg-white border-b border-slate-200 sticky top-0 z-30 shadow-sm">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="flex flex-col sm:flex-row justify-between h-auto sm:h-16 py-3 sm:py-0 items-center gap-3 sm:gap-0">
                <!-- Logo & Title -->
                <div class="flex items-center space-x-3">
                    <div class="w-10 h-10 rounded-xl bg-gradient-to-tr from-blue-600 to-indigo-600 flex items-center justify-center text-white shadow-md">
                        <i class="fa-solid fa-bullseye text-lg"></i>
                    </div>
                    <div>
                        <h1 class="text-xl font-bold bg-gradient-to-r from-slate-900 to-slate-700 bg-clip-text text-transparent">ChallengeSync</h1>
                        <p class="text-xs text-slate-500 font-medium">Challenge-Based Instructional Coaching</p>
                    </div>
                </div>

                <!-- Active Role Selector Switcher -->
                <div class="flex flex-wrap items-center justify-center gap-3">
                    <div class="bg-slate-100 p-1 rounded-lg flex items-center border border-slate-200 text-xs font-medium">
                        <span class="hidden md:inline px-2 py-1 text-slate-500 font-semibold uppercase tracking-wider text-[10px]">Role:</span>
                        <button id="role-coach" onclick="setRole('coach')" class="role-btn px-3 py-1.5 rounded-md transition-all shadow-sm bg-white text-blue-600 font-bold">
                            <i class="fa-solid fa-user-ninja md:mr-1"></i> <span class="hidden md:inline">Coach</span>
                        </button>
                        <button id="role-teacher" onclick="setRole('teacher')" class="role-btn px-3 py-1.5 rounded-md transition-all text-slate-600 hover:text-slate-900">
                            <i class="fa-solid fa-chalkboard-user md:mr-1"></i> <span class="hidden md:inline">Teacher</span>
                        </button>
                        <button id="role-admin" onclick="setRole('admin')" class="role-btn px-3 py-1.5 rounded-md transition-all text-slate-600 hover:text-slate-900">
                            <i class="fa-solid fa-school-flag md:mr-1"></i> <span class="hidden md:inline">Admin</span>
                        </button>
                    </div>

                    <!-- Sync Indicator Button -->
                    <button onclick="switchTab('sheets')" class="flex items-center space-x-2 px-3 py-1.5 rounded-lg border border-emerald-200 bg-emerald-50 text-emerald-700 hover:bg-emerald-100 transition-colors text-xs font-semibold">
                        <i class="fa-solid fa-file-csv text-emerald-600"></i>
                        <span id="sync-status-text" class="hidden md:inline">Sheets: Ready</span>
                    </button>
                </div>
            </div>
        </div>

        <!-- Main Workspace Tabs -->
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 flex space-x-4 md:space-x-8 text-sm border-t border-slate-100 overflow-x-auto pb-1">
            <button id="tab-cycles-btn" onclick="switchTab('cycles')" class="tab-btn tab-active py-3 px-1 flex items-center space-x-2 whitespace-nowrap">
                <i class="fa-solid fa-diagram-project text-xs"></i>
                <span>Coaching Cycles</span>
                <span id="cycle-count-badge" class="ml-1 bg-blue-100 text-blue-700 text-xs px-2 py-0.5 rounded-full font-bold">0</span>
            </button>
            <button id="tab-observe-btn" onclick="switchTab('observe')" class="tab-btn text-slate-500 hover:text-slate-800 py-3 px-1 flex items-center space-x-2 whitespace-nowrap">
                <i class="fa-solid fa-stopwatch-20 text-xs"></i>
                <span>Observe Tool</span>
            </button>
            <button id="tab-admin-btn" onclick="switchTab('admin')" class="tab-btn text-slate-500 hover:text-slate-800 py-3 px-1 flex items-center space-x-2 whitespace-nowrap">
                <i class="fa-solid fa-chart-line text-xs"></i>
                <span>Insights</span>
            </button>
            <button id="tab-sheets-btn" onclick="switchTab('sheets')" class="tab-btn text-slate-500 hover:text-slate-800 py-3 px-1 flex items-center space-x-2 whitespace-nowrap">
                <i class="fa-solid fa-table-cells text-xs"></i>
                <span>Sync Hub</span>
            </button>
        </div>
    </header>

    <!-- App Main Body Content -->
    <main class="flex-1 max-w-7xl w-full mx-auto px-4 sm:px-6 lg:px-8 py-6">

        <!-- Banner Alert based on Role -->
        <div id="role-banner" class="mb-6 p-4 rounded-xl bg-gradient-to-r from-blue-50 to-indigo-50 border border-blue-100 text-blue-900 flex flex-col md:flex-row justify-between items-start md:items-center gap-4 shadow-sm">
            <div class="flex items-center space-x-3">
                <div class="p-2 bg-blue-600 text-white rounded-lg">
                    <i id="role-banner-icon" class="fa-solid fa-user-ninja"></i>
                </div>
                <div>
                    <h2 id="role-banner-title" class="font-bold text-sm">Instructional Coach View</h2>
                    <p id="role-banner-desc" class="text-xs text-slate-600">Guide teachers through Challenge-Based Learning stages, conduct live observations, and record strategy impacts.</p>
                </div>
            </div>
            <button onclick="openNewCycleModal()" class="w-full md:w-auto px-4 py-2 bg-blue-600 hover:bg-blue-700 text-white text-xs font-bold rounded-lg shadow transition-all flex items-center justify-center space-x-2 whitespace-nowrap">
                <i class="fa-solid fa-plus"></i>
                <span>Start New Challenge Cycle</span>
            </button>
        </div>

        <!-- ================= SECTION 1: COACHING CYCLES WORKSPACE ================= -->
        <section id="section-cycles" class="space-y-6">
            <!-- Filter & Search Controls -->
            <div class="flex flex-col md:flex-row justify-between items-center gap-4 bg-white p-4 rounded-xl border border-slate-200 shadow-sm">
                <div class="flex flex-col md:flex-row items-center space-y-3 md:space-y-0 md:space-x-3 w-full md:w-auto">
                    <div class="relative w-full md:w-72">
                        <i class="fa-solid fa-magnifying-glass absolute left-3 top-3 text-slate-400 text-xs"></i>
                        <input type="text" id="search-input" oninput="renderCycles()" placeholder="Search teacher, coach, challenge..." class="w-full pl-9 pr-3 py-1.5 bg-slate-50 border border-slate-200 rounded-lg text-xs focus:ring-2 focus:ring-blue-500 focus:outline-none">
                    </div>
                    <select id="stage-filter" onchange="renderCycles()" class="w-full md:w-auto bg-slate-50 border border-slate-200 rounded-lg text-xs py-1.5 px-3 text-slate-700 font-medium focus:outline-none">
                        <option value="ALL">All CBC Stages</option>
                        <option value="ENGAGE">1. Engage (Big Idea & EQ)</option>
                        <option value="INVESTIGATE">2. Investigate (Strategy & Plan)</option>
                        <option value="ACT">3. Act (Implementation & Data)</option>
                        <option value="REFLECT">4. Reflect & Aggregate</option>
                    </select>
                </div>

                <div class="flex items-center space-x-2 text-xs text-slate-500 w-full md:w-auto justify-end">
                    <span>Sorting:</span>
                    <button onclick="toggleSort()" class="font-semibold text-slate-700 hover:text-blue-600">
                        Date Created <i class="fa-solid fa-arrow-down-wide-short ml-1"></i>
                    </button>
                </div>
            </div>

            <!-- Cycles Cards Grid -->
            <div id="cycles-grid" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                <!-- Dynamically populated via JS -->
            </div>
        </section>

        <!-- ================= SECTION 2: LIVE OBSERVE TOOL ================= -->
        <section id="section-observe" class="hidden space-y-6">
            <div class="bg-white rounded-xl border border-slate-200 p-4 md:p-6 shadow-sm">
                <div class="border-b border-slate-100 pb-4 mb-6 flex flex-col md:flex-row justify-between items-start md:items-center gap-4">
                    <div>
                        <h2 class="text-lg font-bold text-slate-900">Active Challenge Observation Mode</h2>
                        <p class="text-xs text-slate-500">Conduct structured classroom evidence collection tied to the teacher's active challenge.</p>
                    </div>
                    <div id="active-obs-cycle-select" class="w-full md:w-72">
                        <label class="block text-xs font-semibold text-slate-600 mb-1">Select Active Coaching Cycle:</label>
                        <select id="obs-cycle-dropdown" onchange="loadCycleForObservation()" class="w-full bg-slate-50 border border-slate-200 rounded-lg text-xs p-2 focus:ring-2 focus:ring-blue-500 focus:outline-none">
                            <!-- Populated dynamically -->
                        </select>
                    </div>
                </div>

                <!-- Selected Cycle Quick Reference Card -->
                <div id="obs-context-card" class="bg-gradient-to-r from-slate-900 to-slate-800 text-white rounded-xl p-4 mb-6 shadow-md">
                    <div class="flex flex-col sm:flex-row justify-between items-start gap-4">
                        <div>
                            <span id="obs-tag-subject" class="px-2 py-0.5 rounded text-[10px] font-bold bg-blue-500 uppercase tracking-wider">Math - 8th Grade</span>
                            <h3 id="obs-teacher-name" class="text-base font-bold mt-1">Teacher: Sarah Jenkins</h3>
                            <p id="obs-challenge-title" class="text-xs text-slate-300 mt-1"><strong class="text-amber-400">Challenge:</strong> Increasing active participation in partner problem solving.</p>
                        </div>
                        <div class="sm:text-right w-full sm:w-auto bg-slate-800/50 p-2 rounded sm:bg-transparent sm:p-0">
                            <p class="text-[10px] text-slate-400 uppercase tracking-wider">Target Metric</p>
                            <p id="obs-target-metric" class="text-xs font-semibold text-emerald-400">80%+ student engagement in groups</p>
                        </div>
                    </div>
                </div>

                <!-- Observation Time & Evidence Log Grid -->
                <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
                    <!-- Left: Real-time Notes Input -->
                    <div class="lg:col-span-1 space-y-4 bg-slate-50 p-4 rounded-xl border border-slate-200">
                        <h4 class="text-xs font-bold text-slate-700 uppercase tracking-wider">Capture Evidence Note</h4>
                        
                        <div>
                            <label class="block text-xs text-slate-600 mb-1 font-semibold">Evidence Category / Tag</label>
                            <select id="obs-tag-input" class="w-full bg-white border border-slate-200 rounded-lg text-xs p-2">
                                <option value="Teacher Strategy">🎯 Instructional Strategy Used</option>
                                <option value="Student Action">💡 Student Behavior / Response</option>
                                <option value="Challenge Proof">📈 Direct Challenge Metric Evidence</option>
                                <option value="Coaching Question">❓ Prompt for Reflection</option>
                            </select>
                        </div>

                        <div>
                            <label class="block text-xs text-slate-600 mb-1 font-semibold">Time-Stamped Observation Note</label>
                            <textarea id="obs-note-input" rows="4" placeholder="e.g. Teacher used cold-call equity sticks at 10:14 AM. 5 out of 5 students were prepared with written answers..." class="w-full bg-white border border-slate-200 rounded-lg text-xs p-2 focus:ring-2 focus:ring-blue-500 focus:outline-none"></textarea>
                        </div>

                        <button onclick="addObservationNote()" class="w-full py-2 bg-blue-600 hover:bg-blue-700 text-white font-bold text-xs rounded-lg shadow transition-all flex items-center justify-center space-x-2">
                            <i class="fa-solid fa-plus-circle"></i>
                            <span>Log Timestamped Evidence Note</span>
                        </button>
                    </div>

                    <!-- Right: Live Feed of Evidence -->
                    <div class="lg:col-span-2 space-y-3">
                        <div class="flex justify-between items-center">
                            <h4 class="text-xs font-bold text-slate-700 uppercase tracking-wider">Observation Evidence Stream</h4>
                            <span id="obs-count-badge" class="text-xs text-slate-500">0 notes captured</span>
                        </div>

                        <div id="obs-timeline" class="space-y-3 max-h-96 overflow-y-auto pr-2">
                            <!-- Observation logs rendered here -->
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <!-- ================= SECTION 3: ADMIN AGGREGATE DASHBOARD ================= -->
        <section id="section-admin" class="hidden space-y-6">
            <!-- Summary Stat Cards -->
            <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-4 gap-4">
                <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm flex items-center space-x-4">
                    <div class="w-12 h-12 flex-shrink-0 rounded-lg bg-blue-50 text-blue-600 flex items-center justify-center text-xl font-bold">
                        <i class="fa-solid fa-diagram-next"></i>
                    </div>
                    <div>
                        <p class="text-xs text-slate-500 font-semibold uppercase">Total Coaching Cycles</p>
                        <p id="stat-total-cycles" class="text-2xl font-black text-slate-900">0</p>
                    </div>
                </div>

                <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm flex items-center space-x-4">
                    <div class="w-12 h-12 flex-shrink-0 rounded-lg bg-amber-50 text-amber-600 flex items-center justify-center text-xl font-bold">
                        <i class="fa-solid fa-fire"></i>
                    </div>
                    <div>
                        <p class="text-xs text-slate-500 font-semibold uppercase">Active Challenges</p>
                        <p id="stat-active-cycles" class="text-2xl font-black text-slate-900">0</p>
                    </div>
                </div>

                <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm flex items-center space-x-4">
                    <div class="w-12 h-12 flex-shrink-0 rounded-lg bg-emerald-50 text-emerald-600 flex items-center justify-center text-xl font-bold">
                        <i class="fa-solid fa-circle-check"></i>
                    </div>
                    <div>
                        <p class="text-xs text-slate-500 font-semibold uppercase">Completed & Reflected</p>
                        <p id="stat-completed-cycles" class="text-2xl font-black text-slate-900">0</p>
                    </div>
                </div>

                <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm flex items-center space-x-4">
                    <div class="w-12 h-12 flex-shrink-0 rounded-lg bg-indigo-50 text-indigo-600 flex items-center justify-center text-xl font-bold">
                        <i class="fa-solid fa-chart-line-up"></i>
                    </div>
                    <div>
                        <p class="text-xs text-slate-500 font-semibold uppercase">Avg Strategy Impact</p>
                        <p id="stat-avg-impact" class="text-2xl font-black text-slate-900">0 / 5</p>
                    </div>
                </div>
            </div>

            <!-- Charts Section -->
            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                <!-- Chart 1: Challenges by Big Idea / Topic -->
                <div class="bg-white p-5 rounded-xl border border-slate-200 shadow-sm">
                    <h3 class="text-sm font-bold text-slate-800 mb-4 flex items-center">
                        <i class="fa-solid fa-chart-pie text-blue-500 mr-2"></i> Challenges by Educational Big Idea
                    </h3>
                    <div class="h-64 relative">
                        <canvas id="chart-big-ideas"></canvas>
                    </div>
                </div>

                <!-- Chart 2: Cycles Stage Distribution -->
                <div class="bg-white p-5 rounded-xl border border-slate-200 shadow-sm">
                    <h3 class="text-sm font-bold text-slate-800 mb-4 flex items-center">
                        <i class="fa-solid fa-chart-bar text-indigo-500 mr-2"></i> Challenge-Based Coaching Stage Progress
                    </h3>
                    <div class="h-64 relative">
                        <canvas id="chart-stages"></canvas>
                    </div>
                </div>
            </div>

            <!-- Admin Aggregated Summary Table -->
            <div class="bg-white rounded-xl border border-slate-200 shadow-sm overflow-hidden">
                <div class="p-4 border-b border-slate-200 flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4 bg-slate-50">
                    <div>
                        <h3 class="text-sm font-bold text-slate-800">School-Wide Challenge Summary Log</h3>
                        <p class="text-xs text-slate-500">Macro overview of teacher challenges, high-impact strategies, and coach assignments</p>
                    </div>
                    <button onclick="exportToGoogleSheetCSV()" class="w-full sm:w-auto px-3 py-1.5 bg-emerald-600 hover:bg-emerald-700 text-white text-xs font-bold rounded-lg transition-colors flex items-center justify-center space-x-1 whitespace-nowrap">
                        <i class="fa-solid fa-file-excel"></i>
                        <span>Export Summary CSV</span>
                    </button>
                </div>
                <div class="overflow-x-auto w-full">
                    <table class="w-full text-left border-collapse text-xs min-w-[800px]">
                        <thead>
                            <tr class="bg-slate-100 border-b border-slate-200 text-slate-600 font-semibold uppercase tracking-wider">
                                <th class="py-3 px-4">Teacher</th>
                                <th class="py-3 px-4">Coach</th>
                                <th class="py-3 px-4">Big Idea</th>
                                <th class="py-3 px-4">Challenge Focus</th>
                                <th class="py-3 px-4">Stage</th>
                                <th class="py-3 px-4">Strategy Tested</th>
                                <th class="py-3 px-4 text-center">Impact Score</th>
                            </tr>
                        </thead>
                        <tbody id="admin-summary-tbody" class="divide-y divide-slate-100 text-slate-700">
                            <!-- Populated dynamically -->
                        </tbody>
                    </table>
                </div>
            </div>
        </section>

        <!-- ================= SECTION 4: GOOGLE SHEETS SYNC HUB ================= -->
        <section id="section-sheets" class="hidden space-y-6">
            <div class="bg-white rounded-xl border border-slate-200 p-4 md:p-6 shadow-sm space-y-6">
                <div>
                    <h2 class="text-lg font-bold text-slate-900 flex items-center">
                        <i class="fa-brands fa-google text-emerald-600 mr-2"></i> Google Sheets Integration & Data Aggregator
                    </h2>
                    <p class="text-xs text-slate-500 mt-1">Connect ChallengeSync directly to a district Google Sheet via live Webhook or export on-demand.</p>
                </div>

                <!-- Webhook Setup Container -->
                <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
                    <!-- Setup Form -->
                    <div class="bg-slate-50 p-5 rounded-xl border border-slate-200 space-y-4">
                        <h3 class="text-sm font-bold text-slate-800 flex items-center">
                            <i class="fa-solid fa-plug text-blue-600 mr-2"></i> Option 1: Live Webhook URL (Real-time Sync)
                        </h3>
                        <p class="text-xs text-slate-600 leading-relaxed">
                            Paste your Google Apps Script Web App URL below. Whenever a coach updates a challenge cycle or adds evidence, data is automatically appended to your Google Sheet!
                        </p>

                        <div>
                            <label class="block text-xs font-semibold text-slate-700 mb-1">Google Apps Script Web App Webhook URL</label>
                            <input type="url" id="webhook-url-input" placeholder="https://script.google.com/macros/s/AKfycbx.../exec" class="w-full p-2.5 bg-white border border-slate-300 rounded-lg text-xs font-mono focus:ring-2 focus:ring-blue-500 focus:outline-none">
                        </div>

                        <div class="flex flex-col sm:flex-row space-y-3 sm:space-y-0 sm:space-x-3">
                            <button onclick="saveWebhookUrl()" class="w-full sm:w-auto px-4 py-2 bg-blue-600 hover:bg-blue-700 text-white font-bold text-xs rounded-lg shadow transition-colors flex items-center justify-center space-x-2">
                                <i class="fa-solid fa-floppy-disk"></i>
                                <span>Save Webhook</span>
                            </button>
                            <button onclick="testWebhook()" class="w-full sm:w-auto px-4 py-2 bg-emerald-600 hover:bg-emerald-700 text-white font-bold text-xs rounded-lg shadow transition-colors flex items-center justify-center space-x-2">
                                <i class="fa-solid fa-paper-plane"></i>
                                <span>Send Test Row</span>
                            </button>
                        </div>
                    </div>

                    <!-- Instructions / Script Generator -->
                    <div class="bg-slate-900 text-slate-200 p-5 rounded-xl border border-slate-800 space-y-3 overflow-hidden">
                        <div class="flex justify-between items-center">
                            <h3 class="text-xs font-bold uppercase tracking-wider text-amber-400">Google Apps Script Code Snippet</h3>
                            <button onclick="copyScriptCode()" class="text-xs text-slate-300 hover:text-white flex items-center space-x-1 bg-slate-800 px-2 py-1 rounded">
                                <i class="fa-solid fa-copy"></i>
                                <span>Copy Code</span>
                            </button>
                        </div>
                        <p class="text-[11px] text-slate-400">Steps: 1. Open Google Sheet $\rightarrow$ Extensions $\rightarrow$ Apps Script. 2. Paste code below. 3. Click Deploy $\rightarrow$ New Deployment $\rightarrow$ Web App (Set access to "Anyone").</p>
                        <pre class="bg-slate-950 p-3 rounded-lg text-[10px] font-mono text-emerald-400 overflow-x-auto leading-relaxed border border-slate-800">
function doPost(e) {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  var data = JSON.parse(e.postData.contents);
  
  if (sheet.getLastRow() === 0) {
    sheet.appendRow(["Timestamp", "Teacher", "Coach", "Big Idea", "Challenge Focus", "Stage", "Strategy", "Impact Score"]);
  }
  
  sheet.appendRow([
    new Date(),
    data.teacherName,
    data.coachName,
    data.bigIdea,
    data.challenge,
    data.stage,
    data.strategy || "N/A",
    data.impactScore || "N/A"
  ]);
  
  return ContentService.createTextOutput(JSON.stringify({"result": "success"}))
    .setMimeType(ContentService.MimeType.JSON);
}</pre>
                    </div>
                </div>

                <!-- Manual Export Options -->
                <div class="pt-6 border-t border-slate-200">
                    <h3 class="text-sm font-bold text-slate-800 mb-3 flex items-center">
                        <i class="fa-solid fa-file-export text-indigo-600 mr-2"></i> Option 2: Instant Manual Export & Clipboard Copy
                    </h3>
                    <div class="flex flex-col sm:flex-row flex-wrap gap-4">
                        <button onclick="exportToGoogleSheetCSV()" class="w-full sm:w-auto px-4 py-2.5 bg-emerald-600 hover:bg-emerald-700 text-white font-bold text-xs rounded-xl shadow transition-all flex items-center justify-center space-x-2">
                            <i class="fa-solid fa-file-csv text-base"></i>
                            <span>Download Full CSV File</span>
                        </button>

                        <button onclick="copyTableToClipboard()" class="w-full sm:w-auto px-4 py-2.5 bg-slate-800 hover:bg-slate-900 text-white font-bold text-xs rounded-xl shadow transition-all flex items-center justify-center space-x-2">
                            <i class="fa-solid fa-paste text-base"></i>
                            <span>Copy Table Data to Paste</span>
                        </button>
                    </div>
                </div>
            </div>
        </section>

    </main>

    <!-- ================= MODAL: CREATE / EDIT COACHING CYCLE ================= -->
    <div id="modal-cycle" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 flex items-center justify-center hidden p-4 sm:p-6 overflow-y-auto">
        <div class="bg-white rounded-2xl w-full max-w-2xl max-h-[90vh] overflow-y-auto shadow-2xl border border-slate-200 flex flex-col my-auto relative">
            <!-- Modal Header -->
            <div class="sticky top-0 p-4 sm:p-5 border-b border-slate-100 flex justify-between items-center bg-slate-50 z-10 rounded-t-2xl">
                <div class="flex items-center space-x-2">
                    <div class="w-8 h-8 rounded-lg bg-blue-600 text-white flex items-center justify-center font-bold text-sm shadow-sm">
                        <i class="fa-solid fa-arrows-spin"></i>
                    </div>
                    <h3 id="modal-title" class="font-bold text-base text-slate-900 truncate pr-4">New Challenge-Based Coaching Cycle</h3>
                </div>
                <button type="button" onclick="closeNewCycleModal()" class="text-slate-400 hover:text-slate-600 p-2 bg-slate-200/50 hover:bg-slate-200 rounded-full transition-colors">
                    <i class="fa-solid fa-xmark text-lg"></i>
                </button>
            </div>

            <!-- Modal Form Body -->
            <form id="cycle-form" onsubmit="saveCycle(event)" class="p-4 sm:p-6 space-y-5">
                <input type="hidden" id="form-cycle-id">

                <!-- Basic Info Grid -->
                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <div>
                        <label class="block text-xs font-semibold text-slate-700 mb-1">Teacher Name *</label>
                        <input type="text" id="form-teacher" required placeholder="e.g. Maria Rodriguez" class="w-full p-2 bg-slate-50 border border-slate-200 rounded-lg text-xs focus:ring-2 focus:ring-blue-500 focus:outline-none">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-700 mb-1">Instructional Coach *</label>
                        <input type="text" id="form-coach" required placeholder="e.g. David Miller" class="w-full p-2 bg-slate-50 border border-slate-200 rounded-lg text-xs focus:ring-2 focus:ring-blue-500 focus:outline-none">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-700 mb-1">Subject & Grade Level</label>
                        <input type="text" id="form-subject" placeholder="e.g. 7th Grade Science" class="w-full p-2 bg-slate-50 border border-slate-200 rounded-lg text-xs focus:ring-2 focus:ring-blue-500 focus:outline-none">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-700 mb-1">Current Coaching Stage</label>
                        <select id="form-stage" class="w-full p-2 bg-slate-50 border border-slate-200 rounded-lg text-xs focus:ring-2 focus:ring-blue-500 focus:outline-none">
                            <option value="ENGAGE">1. Engage (Identify Challenge)</option>
                            <option value="INVESTIGATE">2. Investigate (Select Strategy)</option>
                            <option value="ACT">3. Act (Implement & Observe)</option>
                            <option value="REFLECT">4. Reflect (Analyze Impact)</option>
                        </select>
                    </div>
                </div>

                <!-- CBC Framework Core Sections -->
                <div class="border-t border-slate-100 pt-4 space-y-4">
                    <h4 class="text-xs font-bold text-blue-600 uppercase tracking-wider flex items-center">
                        <i class="fa-solid fa-lightbulb mr-1"></i> Stage 1: Engage Phase
                    </h4>
                    <div>
                        <label class="block text-xs font-semibold text-slate-700 mb-1">Big Idea / Educational Domain *</label>
                        <select id="form-big-idea" required class="w-full p-2 bg-slate-50 border border-slate-200 rounded-lg text-xs focus:ring-2 focus:ring-blue-500 focus:outline-none">
                            <option value="Student Engagement">Student Engagement & Active Learning</option>
                            <option value="Formative Assessment">Formative Assessment & Feedback</option>
                            <option value="Classroom Management">Classroom Routines & Management</option>
                            <option value="Differentiated Instruction">Differentiated Instruction & Tiered Support</option>
                            <option value="Educational Technology">EdTech Integration</option>
                            <option value="DOK & Higher Order Thinking">DOK & Rigorous Questioning</option>
                        </select>
                    </div>

                    <div>
                        <label class="block text-xs font-semibold text-slate-700 mb-1">Essential Question *</label>
                        <input type="text" id="form-eq" required placeholder="e.g., How can we increase student verbal participation during checks for understanding?" class="w-full p-2 bg-slate-50 border border-slate-200 rounded-lg text-xs focus:ring-2 focus:ring-blue-500 focus:outline-none">
                    </div>

                    <div>
                        <label class="block text-xs font-semibold text-slate-700 mb-1">Actionable Challenge Statement *</label>
                        <textarea id="form-challenge" required rows="2" placeholder="e.g., Transition 80% of quiet students to active group discussion leads by week 3." class="w-full p-2 bg-slate-50 border border-slate-200 rounded-lg text-xs focus:ring-2 focus:ring-blue-500 focus:outline-none"></textarea>
                    </div>
                </div>

                <div class="border-t border-slate-100 pt-4 space-y-4">
                    <h4 class="text-xs font-bold text-indigo-600 uppercase tracking-wider flex items-center">
                        <i class="fa-solid fa-magnifying-glass-chart mr-1"></i> Stage 2: Investigate Phase
                    </h4>
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                        <div>
                            <label class="block text-xs font-semibold text-slate-700 mb-1">Instructional Strategy Chosen</label>
                            <input type="text" id="form-strategy" placeholder="e.g. Think-Pair-Share with Sentence Frames" class="w-full p-2 bg-slate-50 border border-slate-200 rounded-lg text-xs focus:ring-2 focus:ring-blue-500 focus:outline-none">
                        </div>
                        <div>
                            <label class="block text-xs font-semibold text-slate-700 mb-1">Target Metric / Goal</label>
                            <input type="text" id="form-target-metric" placeholder="e.g. 85% participation rate in period 3" class="w-full p-2 bg-slate-50 border border-slate-200 rounded-lg text-xs focus:ring-2 focus:ring-blue-500 focus:outline-none">
                        </div>
                    </div>
                </div>

                <div class="border-t border-slate-100 pt-4 space-y-4">
                    <h4 class="text-xs font-bold text-amber-600 uppercase tracking-wider flex items-center">
                        <i class="fa-solid fa-person-running mr-1"></i> Stage 3: Act Phase
                    </h4>
                    <div>
                        <label class="block text-xs font-semibold text-slate-700 mb-1">Action Plan & Implementation Notes</label>
                        <textarea id="form-action-plan" rows="2" placeholder="e.g. Teacher will introduce the strategy on Tuesday; Coach will observe on Thursday morning." class="w-full p-2 bg-slate-50 border border-slate-200 rounded-lg text-xs focus:ring-2 focus:ring-blue-500 focus:outline-none"></textarea>
                    </div>
                </div>

                <div class="border-t border-slate-100 pt-4 space-y-4">
                    <h4 class="text-xs font-bold text-emerald-600 uppercase tracking-wider flex items-center">
                        <i class="fa-solid fa-star mr-1"></i> Stage 4: Reflect & Impact
                    </h4>
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                        <div>
                            <label class="block text-xs font-semibold text-slate-700 mb-1">Strategy Impact Rating (1 to 5)</label>
                            <select id="form-impact-score" class="w-full p-2 bg-slate-50 border border-slate-200 rounded-lg text-xs font-bold">
                                <option value="0">Not Assessed Yet</option>
                                <option value="1">1 - Low Impact / Needs Revision</option>
                                <option value="2">2 - Minimal Impact</option>
                                <option value="3">3 - Moderate Impact</option>
                                <option value="4">4 - High Impact</option>
                                <option value="5">5 - Exceptional Transformative Impact</option>
                            </select>
                        </div>
                        <div>
                            <label class="block text-xs font-semibold text-slate-700 mb-1">Post-Reflection Notes</label>
                            <input type="text" id="form-reflection" placeholder="Key takeaways and next steps..." class="w-full p-2 bg-slate-50 border border-slate-200 rounded-lg text-xs">
                        </div>
                    </div>
                </div>

                <!-- Form Submit Buttons -->
                <div class="border-t border-slate-200 pt-4 pb-2 flex justify-end space-x-3 sticky bottom-0 bg-white">
                    <button type="button" onclick="closeNewCycleModal()" class="px-4 py-2 border border-slate-200 text-slate-600 rounded-lg text-xs font-bold hover:bg-slate-50">Cancel</button>
                    <button type="submit" class="px-5 py-2 bg-blue-600 hover:bg-blue-700 text-white rounded-lg text-xs font-bold shadow transition-colors flex items-center space-x-1">
                        <i class="fa-solid fa-check"></i>
                        <span>Save Challenge Cycle</span>
                    </button>
                </div>
            </form>
        </div>
    </div>

    <!-- Notification Toast Element -->
    <div id="toast" class="fixed bottom-5 right-5 bg-slate-900 text-white px-4 py-3 rounded-xl shadow-xl text-xs flex items-center space-x-3 transition-all duration-300 transform translate-y-20 opacity-0 z-50 pointer-events-none">
        <i id="toast-icon" class="fa-solid fa-circle-check text-emerald-400 text-sm"></i>
        <span id="toast-message" class="font-medium">Action completed successfully.</span>
    </div>

    <!-- MAIN JAVASCRIPT APPLICATION LOGIC -->
    <script>
        // ================= GLOBAL STATE =================
        let currentRole = 'coach'; // 'coach', 'teacher', 'admin'
        let webhookUrl = localStorage.getItem('cbc_webhook_url') || '';
        let bigIdeasChart = null;
        let stagesChart = null;

        // Sample Seed Data for immediate engagement
        let cycles = [
            {
                id: 'c1',
                teacherName: 'Sarah Jenkins',
                coachName: 'David Miller',
                subject: '8th Grade Mathematics',
                stage: 'ACT', // ENGAGE, INVESTIGATE, ACT, REFLECT
                bigIdea: 'Student Engagement',
                essentialQuestion: 'How can we increase active student problem-solving in small groups?',
                challenge: 'Empower 80%+ of students to lead discussions without direct coach intervention.',
                strategy: 'Structured Think-Pair-Share with Sentence Stems',
                targetMetric: '80% active participation rate',
                actionPlan: 'Introduce stems on Monday, observe on Wednesday.',
                impactScore: 4,
                reflection: 'Students responded well to sentence frames during group math discussions.',
                createdAt: '2026-07-10',
                observations: [
                    { time: '10:15 AM', tag: 'Teacher Strategy', note: 'Teacher displayed sentence stems on board and set 3-minute timer.' },
                    { time: '10:19 AM', tag: 'Student Action', note: 'Group 3 student used frame: "I agree with your solution because..."' },
                    { time: '10:24 AM', tag: 'Challenge Proof', note: '7 out of 8 student pairs were actively conversing.' }
                ]
            },
            {
                id: 'c2',
                teacherName: 'Marcus Vance',
                coachName: 'Elena Rostova',
                subject: '10th Grade English',
                stage: 'REFLECT',
                bigIdea: 'Formative Assessment',
                essentialQuestion: 'How can digital quick-checks drive real-time lesson pivots?',
                challenge: 'Use exit tickets to immediately group students for next-day tier 2 intervention.',
                strategy: 'Formative Exit Ticket Polling App',
                targetMetric: '100% exit check submission & targeted grouping',
                actionPlan: 'Setup polling app accounts for all students. Run test poll Tuesday.',
                impactScore: 5,
                reflection: 'Transformed morning warm-ups; targeted intervention groups are now data-driven.',
                createdAt: '2026-07-05',
                observations: [
                    { time: '01:40 PM', tag: 'Teacher Strategy', note: 'Launched 3-question digital check at end of literary analysis.' }
                ]
            },
            {
                id: 'c3',
                teacherName: 'Jessica Chen',
                coachName: 'David Miller',
                subject: 'Biology High School',
                stage: 'ENGAGE',
                bigIdea: 'DOK & Higher Order Thinking',
                essentialQuestion: 'How can we raise questioning depth from DOK 1 to DOK 3 during lab time?',
                challenge: 'Incorporate student-generated hypotheses before every lab investigation.',
                strategy: 'Q-Matrix Question Prompts',
                targetMetric: 'Students generate at least 2 DOK-3 questions per lab',
                actionPlan: '',
                impactScore: 0,
                reflection: '',
                createdAt: '2026-07-18',
                observations: []
            }
        ];

        // ================= INITIALIZATION =================
        window.onload = function() {
            // Load saved local storage if available
            const stored = localStorage.getItem('cbc_cycles_data');
            if (stored) {
                try { cycles = JSON.parse(stored); } catch(e){}
            }

            if (webhookUrl) {
                document.getElementById('webhook-url-input').value = webhookUrl;
                updateSyncBadge(true);
            }

            setRole('coach');
            renderCycles();
            populateObsDropdown();
            initCharts();
            updateAdminStats();
        };

        function saveState() {
            localStorage.setItem('cbc_cycles_data', JSON.stringify(cycles));
            updateAdminStats();
            populateObsDropdown();
        }

        // ================= ROLE MANAGEMENT =================
        function setRole(role) {
            currentRole = role;
            
            // Update Tab/Button styling
            document.querySelectorAll('.role-btn').forEach(btn => {
                btn.className = 'role-btn px-3 py-1.5 rounded-md transition-all text-slate-600 hover:text-slate-900';
            });
            const activeBtn = document.getElementById(`role-${role}`);
            activeBtn.className = 'role-btn px-3 py-1.5 rounded-md transition-all shadow-sm bg-white text-blue-600 font-bold';

            // Update Banner messaging
            const bannerTitle = document.getElementById('role-banner-title');
            const bannerDesc = document.getElementById('role-banner-desc');
            const bannerIcon = document.getElementById('role-banner-icon');

            if (role === 'coach') {
                bannerTitle.innerText = "Instructional Coach Mode";
                bannerDesc.innerText = "Facilitate challenge cycles, conduct live timestamped observations, and log strategy impacts.";
                bannerIcon.className = "fa-solid fa-user-ninja";
            } else if (role === 'teacher') {
                bannerTitle.innerText = "Teacher Reflection & Challenge Mode";
                bannerDesc.innerText = "Define your classroom challenge, review coach observations, and assess instructional strategy growth.";
                bannerIcon.className = "fa-solid fa-chalkboard-user";
            } else if (role === 'admin') {
                bannerTitle.innerText = "School Administrator Analytics View";
                bannerDesc.innerText = "Monitor macro-level challenge trends across departments and aggregate data into Google Sheets.";
                bannerIcon.className = "fa-solid fa-school-flag";
            }

            renderCycles();
        }

        // ================= TAB NAVIGATION =================
        function switchTab(tabId) {
            document.querySelectorAll('.tab-btn').forEach(btn => {
                btn.classList.remove('tab-active');
                btn.classList.add('text-slate-500');
            });

            document.getElementById(`tab-${tabId}-btn`).classList.add('tab-active');
            document.getElementById(`tab-${tabId}-btn`).classList.remove('text-slate-500');

            // Hide all sections
            document.getElementById('section-cycles').classList.add('hidden');
            document.getElementById('section-observe').classList.add('hidden');
            document.getElementById('section-admin').classList.add('hidden');
            document.getElementById('section-sheets').classList.add('hidden');

            // Show target
            document.getElementById(`section-${tabId}`).classList.remove('hidden');

            if (tabId === 'admin') {
                updateAdminStats();
                refreshCharts();
            }
        }

        // ================= RENDER CYCLES (STAGE CARDS) =================
        function renderCycles() {
            const grid = document.getElementById('cycles-grid');
            const search = document.getElementById('search-input').value.toLowerCase();
            const stageFilter = document.getElementById('stage-filter').value;

            const filtered = cycles.filter(c => {
                const matchSearch = c.teacherName.toLowerCase().includes(search) || 
                                    c.coachName.toLowerCase().includes(search) ||
                                    c.challenge.toLowerCase().includes(search) ||
                                    c.bigIdea.toLowerCase().includes(search);
                const matchStage = stageFilter === 'ALL' || c.stage === stageFilter;
                return matchSearch && matchStage;
            });

            document.getElementById('cycle-count-badge').innerText = filtered.length;

            if (filtered.length === 0) {
                grid.innerHTML = `
                    <div class="col-span-full py-12 text-center bg-white rounded-xl border border-dashed border-slate-300">
                        <i class="fa-solid fa-folder-open text-3xl text-slate-300 mb-2"></i>
                        <p class="text-xs text-slate-500 font-semibold">No coaching cycles found matching criteria.</p>
                        <button onclick="openNewCycleModal()" class="mt-3 px-3 py-1.5 bg-blue-600 text-white rounded-lg text-xs font-bold">Start New Cycle</button>
                    </div>
                `;
                return;
            }

            grid.innerHTML = filtered.map(c => {
                const stageBadge = getStageBadge(c.stage);
                return `
                    <div class="bg-white rounded-2xl border border-slate-200 shadow-sm hover:shadow-md transition-shadow p-4 sm:p-5 flex flex-col justify-between space-y-4">
                        <!-- Card Top Header -->
                        <div class="flex justify-between items-start">
                            <div class="pr-2">
                                <span class="px-2 py-0.5 rounded text-[10px] font-bold bg-slate-100 text-slate-700 border border-slate-200 uppercase tracking-wider">${escapeHtml(c.bigIdea)}</span>
                                <h3 class="text-base font-bold text-slate-900 mt-1">${escapeHtml(c.teacherName)}</h3>
                                <p class="text-xs text-slate-500"><i class="fa-solid fa-user-shield text-blue-500 mr-1"></i>Coach: ${escapeHtml(c.coachName)} | ${escapeHtml(c.subject || 'General')}</p>
                            </div>
                            <div class="flex-shrink-0">${stageBadge}</div>
                        </div>

                        <!-- Card Core Challenge Content -->
                        <div class="space-y-2 bg-slate-50 p-3 rounded-xl border border-slate-100 flex-1">
                            <div>
                                <p class="text-[10px] font-bold uppercase text-slate-400">Essential Question</p>
                                <p class="text-xs font-semibold text-slate-800 line-clamp-2" title="${escapeHtml(c.essentialQuestion)}">${escapeHtml(c.essentialQuestion)}</p>
                            </div>
                            <div>
                                <p class="text-[10px] font-bold uppercase text-slate-400">Action Challenge</p>
                                <p class="text-xs text-blue-900 font-medium line-clamp-2" title="${escapeHtml(c.challenge)}">${escapeHtml(c.challenge)}</p>
                            </div>
                        </div>

                        <!-- Strategy & Metric Info -->
                        <div class="grid grid-cols-2 gap-2 text-xs">
                            <div class="bg-white p-2 rounded-lg border border-slate-100">
                                <p class="text-[10px] text-slate-400 font-bold uppercase">Strategy Tested</p>
                                <p class="font-bold text-slate-700 truncate" title="${escapeHtml(c.strategy || 'Not set')}">${escapeHtml(c.strategy || 'Not set yet')}</p>
                            </div>
                            <div class="bg-white p-2 rounded-lg border border-slate-100">
                                <p class="text-[10px] text-slate-400 font-bold uppercase">Impact Rating</p>
                                <p class="font-bold ${c.impactScore > 3 ? 'text-emerald-600' : 'text-amber-600'}">
                                    ${c.impactScore > 0 ? '★ ' + c.impactScore + ' / 5' : 'Pending'}
                                </p>
                            </div>
                        </div>

                        <!-- Card Footer Action Controls -->
                        <div class="pt-2 border-t border-slate-100 flex justify-between items-center text-xs">
                            <button onclick="quickSelectObservation('${c.id}')" class="text-blue-600 hover:text-blue-800 font-bold flex items-center space-x-1 p-1 -ml-1">
                                <i class="fa-solid fa-stopwatch"></i>
                                <span>Observe (${c.observations ? c.observations.length : 0})</span>
                            </button>

                            <div class="flex space-x-1 sm:space-x-2">
                                <button onclick="editCycle('${c.id}')" class="text-slate-400 hover:text-slate-700 p-2 sm:p-1" title="Edit Cycle">
                                    <i class="fa-solid fa-pen-to-square text-base sm:text-sm"></i>
                                </button>
                                <button onclick="triggerRowSheetsSync('${c.id}')" class="text-emerald-600 hover:text-emerald-800 p-2 sm:p-1" title="Sync to Sheets">
                                    <i class="fa-solid fa-file-export text-base sm:text-sm"></i>
                                </button>
                                <button onclick="deleteCycle('${c.id}')" class="text-slate-300 hover:text-rose-600 p-2 sm:p-1" title="Delete Cycle">
                                    <i class="fa-solid fa-trash-can text-base sm:text-sm"></i>
                                </button>
                            </div>
                        </div>
                    </div>
                `;
            }).join('');
        }

        function getStageBadge(stage) {
            switch(stage) {
                case 'ENGAGE': 
                    return `<span class="px-2 py-1 rounded-full text-[10px] font-bold bg-blue-100 text-blue-700 whitespace-nowrap">1. ENGAGE</span>`;
                case 'INVESTIGATE': 
                    return `<span class="px-2 py-1 rounded-full text-[10px] font-bold bg-indigo-100 text-indigo-700 whitespace-nowrap">2. INVESTIGATE</span>`;
                case 'ACT': 
                    return `<span class="px-2 py-1 rounded-full text-[10px] font-bold bg-amber-100 text-amber-700 whitespace-nowrap">3. ACT</span>`;
                case 'REFLECT': 
                    return `<span class="px-2 py-1 rounded-full text-[10px] font-bold bg-emerald-100 text-emerald-700 whitespace-nowrap">4. REFLECT</span>`;
                default: 
                    return `<span class="px-2 py-1 rounded-full text-[10px] font-bold bg-slate-100 text-slate-600 whitespace-nowrap">${stage}</span>`;
            }
        }

        // ================= MODAL & CYCLE CRUD =================
        function openNewCycleModal() {
            document.getElementById('cycle-form').reset();
            document.getElementById('form-cycle-id').value = '';
            document.getElementById('modal-title').innerText = "New Challenge-Based Coaching Cycle";
            document.getElementById('modal-cycle').classList.remove('hidden');
        }

        function closeNewCycleModal() {
            document.getElementById('modal-cycle').classList.add('hidden');
        }

        function saveCycle(e) {
            e.preventDefault();
            const id = document.getElementById('form-cycle-id').value || 'c_' + Date.now();
            const existingIdx = cycles.findIndex(c => c.id === id);

            const cycleObj = {
                id: id,
                teacherName: document.getElementById('form-teacher').value,
                coachName: document.getElementById('form-coach').value,
                subject: document.getElementById('form-subject').value,
                stage: document.getElementById('form-stage').value,
                bigIdea: document.getElementById('form-big-idea').value,
                essentialQuestion: document.getElementById('form-eq').value,
                challenge: document.getElementById('form-challenge').value,
                strategy: document.getElementById('form-strategy').value,
                targetMetric: document.getElementById('form-target-metric').value,
                actionPlan: document.getElementById('form-action-plan').value,
                impactScore: parseInt(document.getElementById('form-impact-score').value) || 0,
                reflection: document.getElementById('form-reflection').value,
                createdAt: existingIdx >= 0 ? cycles[existingIdx].createdAt : new Date().toISOString().split('T')[0],
                observations: existingIdx >= 0 ? cycles[existingIdx].observations : []
            };

            if (existingIdx >= 0) {
                cycles[existingIdx] = cycleObj;
                showToast("Coaching cycle updated!");
            } else {
                cycles.unshift(cycleObj);
                showToast("New coaching cycle created!");
                // Trigger Webhook if present
                if (webhookUrl) sendToWebhook(cycleObj);
            }

            saveState();
            renderCycles();
            closeNewCycleModal();
        }

        function editCycle(id) {
            const c = cycles.find(item => item.id === id);
            if (!c) return;

            document.getElementById('form-cycle-id').value = c.id;
            document.getElementById('form-teacher').value = c.teacherName;
            document.getElementById('form-coach').value = c.coachName;
            document.getElementById('form-subject').value = c.subject || '';
            document.getElementById('form-stage').value = c.stage;
            document.getElementById('form-big-idea').value = c.bigIdea;
            document.getElementById('form-eq').value = c.essentialQuestion;
            document.getElementById('form-challenge').value = c.challenge;
            document.getElementById('form-strategy').value = c.strategy || '';
            document.getElementById('form-target-metric').value = c.targetMetric || '';
            document.getElementById('form-action-plan').value = c.actionPlan || '';
            document.getElementById('form-impact-score').value = c.impactScore || 0;
            document.getElementById('form-reflection').value = c.reflection || '';

            document.getElementById('modal-title').innerText = "Edit Challenge Cycle: " + c.teacherName;
            document.getElementById('modal-cycle').classList.remove('hidden');
        }

        function deleteCycle(id) {
            if (!confirm("Are you sure you want to delete this coaching cycle?")) return;
            cycles = cycles.filter(c => c.id !== id);
            saveState();
            renderCycles();
            showToast("Coaching cycle removed.");
        }

        // ================= OBSERVATION TOOL =================
        function populateObsDropdown() {
            const select = document.getElementById('obs-cycle-dropdown');
            select.innerHTML = cycles.map(c => `
                <option value="${c.id}">${escapeHtml(c.teacherName)} - ${escapeHtml(c.bigIdea)} (${c.stage})</option>
            `).join('');

            if (cycles.length > 0) loadCycleForObservation();
        }

        function quickSelectObservation(cycleId) {
            switchTab('observe');
            document.getElementById('obs-cycle-dropdown').value = cycleId;
            loadCycleForObservation();
        }

        function loadCycleForObservation() {
            const id = document.getElementById('obs-cycle-dropdown').value;
            const c = cycles.find(item => item.id === id);
            if (!c) return;

            document.getElementById('obs-teacher-name').innerText = "Teacher: " + c.teacherName;
            document.getElementById('obs-challenge-title').innerHTML = `<strong class="text-amber-400">Challenge:</strong> ${escapeHtml(c.challenge)}`;
            document.getElementById('obs-tag-subject').innerText = c.subject || 'General';
            document.getElementById('obs-target-metric').innerText = c.targetMetric || 'Standard Goal';

            renderObservationTimeline(c);
        }

        function renderObservationTimeline(c) {
            const timeline = document.getElementById('obs-timeline');
            document.getElementById('obs-count-badge').innerText = (c.observations ? c.observations.length : 0) + " notes logged";

            if (!c.observations || c.observations.length === 0) {
                timeline.innerHTML = `
                    <div class="p-8 text-center bg-slate-50 rounded-xl border border-dashed border-slate-200 text-slate-400 text-xs">
                        No timestamped notes logged yet for this observation session.
                    </div>
                `;
                return;
            }

            timeline.innerHTML = c.observations.map(obs => `
                <div class="bg-white p-3 rounded-xl border border-slate-200 shadow-sm flex items-start space-x-3">
                    <span class="px-2 py-1 bg-slate-100 text-slate-700 text-[10px] font-mono rounded font-bold">${obs.time}</span>
                    <div class="flex-1">
                        <span class="inline-block px-1.5 py-0.5 rounded text-[9px] font-bold uppercase mb-1 ${getTagStyle(obs.tag)}">${obs.tag}</span>
                        <p class="text-xs text-slate-800 leading-relaxed">${escapeHtml(obs.note)}</p>
                    </div>
                </div>
            `).join('');
        }

        function getTagStyle(tag) {
            switch(tag) {
                case 'Teacher Strategy': return 'bg-blue-100 text-blue-700';
                case 'Student Action': return 'bg-amber-100 text-amber-800';
                case 'Challenge Proof': return 'bg-emerald-100 text-emerald-800';
                default: return 'bg-purple-100 text-purple-700';
            }
        }

        function addObservationNote() {
            const id = document.getElementById('obs-cycle-dropdown').value;
            const c = cycles.find(item => item.id === id);
            const noteInput = document.getElementById('obs-note-input');
            const tagInput = document.getElementById('obs-tag-input');

            if (!c || !noteInput.value.trim()) return;

            const now = new Date();
            const timeStr = now.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });

            if (!c.observations) c.observations = [];
            c.observations.push({
                time: timeStr,
                tag: tagInput.value,
                note: noteInput.value.trim()
            });

            saveState();
            renderObservationTimeline(c);
            noteInput.value = '';
            showToast("Timestamped observation note recorded!");
        }

        // ================= ADMIN ANALYTICS & CHARTS =================
        function updateAdminStats() {
            const total = cycles.length;
            const active = cycles.filter(c => c.stage !== 'REFLECT').length;
            const completed = cycles.filter(c => c.stage === 'REFLECT').length;
            
            const ratedCycles = cycles.filter(c => c.impactScore > 0);
            const avgImpact = ratedCycles.length > 0 
                ? (ratedCycles.reduce((acc, curr) => acc + curr.impactScore, 0) / ratedCycles.length).toFixed(1)
                : '0.0';

            document.getElementById('stat-total-cycles').innerText = total;
            document.getElementById('stat-active-cycles').innerText = active;
            document.getElementById('stat-completed-cycles').innerText = completed;
            document.getElementById('stat-avg-impact').innerText = `${avgImpact} / 5`;

            // Render Table
            const tbody = document.getElementById('admin-summary-tbody');
            tbody.innerHTML = cycles.map(c => `
                <tr class="hover:bg-slate-50 transition-colors">
                    <td class="py-3 px-4 font-bold text-slate-900">${escapeHtml(c.teacherName)}</td>
                    <td class="py-3 px-4">${escapeHtml(c.coachName)}</td>
                    <td class="py-3 px-4">${escapeHtml(c.bigIdea)}</td>
                    <td class="py-3 px-4 max-w-xs truncate" title="${escapeHtml(c.challenge)}">${escapeHtml(c.challenge)}</td>
                    <td class="py-3 px-4">${getStageBadge(c.stage)}</td>
                    <td class="py-3 px-4 font-semibold" title="${escapeHtml(c.strategy || 'N/A')}">${escapeHtml(c.strategy || 'N/A')}</td>
                    <td class="py-3 px-4 text-center font-bold ${c.impactScore >= 4 ? 'text-emerald-600' : 'text-slate-600'}">
                        ${c.impactScore > 0 ? c.impactScore + ' ★' : '-'}
                    </td>
                </tr>
            `).join('');
        }

        function initCharts() {
            const ctx1 = document.getElementById('chart-big-ideas').getContext('2d');
            bigIdeasChart = new Chart(ctx1, {
                type: 'doughnut',
                data: { labels: [], datasets: [{ data: [], backgroundColor: ['#2563eb', '#6366f1', '#10b981', '#f59e0b', '#ec4899', '#8b5cf6'] }] },
                options: { responsive: true, maintainAspectRatio: false }
            });

            const ctx2 = document.getElementById('chart-stages').getContext('2d');
            stagesChart = new Chart(ctx2, {
                type: 'bar',
                data: {
                    labels: ['Engage', 'Investigate', 'Act', 'Reflect'],
                    datasets: [{ label: 'Number of Cycles', data: [0, 0, 0, 0], backgroundColor: '#3b82f6' }]
                },
                options: { responsive: true, maintainAspectRatio: false, scales: { y: { beginAtZero: true, ticks: { stepSize: 1 } } } }
            });

            refreshCharts();
        }

        function refreshCharts() {
            if (!bigIdeasChart || !stagesChart) return;

            // Compute Big Ideas
            const ideaCounts = {};
            cycles.forEach(c => {
                ideaCounts[c.bigIdea] = (ideaCounts[c.bigIdea] || 0) + 1;
            });
            bigIdeasChart.data.labels = Object.keys(ideaCounts);
            bigIdeasChart.data.datasets[0].data = Object.values(ideaCounts);
            bigIdeasChart.update();

            // Compute Stages
            const stageCounts = { 'ENGAGE': 0, 'INVESTIGATE': 0, 'ACT': 0, 'REFLECT': 0 };
            cycles.forEach(c => {
                if (stageCounts[c.stage] !== undefined) stageCounts[c.stage]++;
            });
            stagesChart.data.datasets[0].data = Object.values(stageCounts);
            stagesChart.update();
        }

        // ================= GOOGLE SHEETS INTEGRATION & WEBHOOKS =================
        function saveWebhookUrl() {
            const url = document.getElementById('webhook-url-input').value.trim();
            webhookUrl = url;
            localStorage.setItem('cbc_webhook_url', url);
            updateSyncBadge(!!url);
            showToast(url ? "Google Sheets Webhook URL saved!" : "Webhook URL cleared.");
        }

        function updateSyncBadge(active) {
            const statusText = document.getElementById('sync-status-text');
            if (active) {
                statusText.innerText = "Sheets: Live Webhook";
                statusText.parentElement.className = "flex items-center space-x-2 px-3 py-1.5 rounded-lg border border-emerald-300 bg-emerald-100 text-emerald-800 text-xs font-semibold";
            } else {
                statusText.innerText = "Sheets: Manual CSV";
                statusText.parentElement.className = "flex items-center space-x-2 px-3 py-1.5 rounded-lg border border-slate-200 bg-slate-100 text-slate-700 text-xs font-semibold";
            }
        }

        function triggerRowSheetsSync(cycleId) {
            const c = cycles.find(item => item.id === cycleId);
            if (!c) return;
            
            if (webhookUrl) {
                sendToWebhook(c);
            } else {
                switchTab('sheets');
                showToast("Enter a Webhook URL or use CSV Export!");
            }
        }

        function sendToWebhook(data) {
            if (!webhookUrl) return;

            fetch(webhookUrl, {
                method: 'POST',
                mode: 'no-cors',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify(data)
            }).then(() => {
                showToast("Synced row directly to Google Sheet!");
            }).catch(err => {
                showToast("Error sending webhook sync.");
            });
        }

        function testWebhook() {
            if (!webhookUrl) {
                showToast("Please enter a valid Google Apps Script Webhook URL first.");
                return;
            }
            sendToWebhook({
                teacherName: "Test Teacher",
                coachName: "Test Coach",
                bigIdea: "Test Domain",
                challenge: "Test Challenge Payload",
                stage: "ENGAGE",
                strategy: "Test Strategy",
                impactScore: 5
            });
        }

        function copyScriptCode() {
            const scriptText = `function doPost(e) {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  var data = JSON.parse(e.postData.contents);
  if (sheet.getLastRow() === 0) {
    sheet.appendRow(["Timestamp", "Teacher", "Coach", "Big Idea", "Challenge Focus", "Stage", "Strategy", "Impact Score"]);
  }
  sheet.appendRow([new Date(), data.teacherName, data.coachName, data.bigIdea, data.challenge, data.stage, data.strategy || "N/A", data.impactScore || "N/A"]);
  return ContentService.createTextOutput(JSON.stringify({"result": "success"})).setMimeType(ContentService.MimeType.JSON);
}`;
            copyToClipboard(scriptText);
            showToast("Google Apps Script copied to clipboard!");
        }

        function exportToGoogleSheetCSV() {
            let csv = "ID,Teacher Name,Coach Name,Subject,Big Idea,Essential Question,Action Challenge,Stage,Strategy,Target Metric,Action Plan,Impact Score,Reflection,Observations Count,Created Date\n";

            cycles.forEach(c => {
                const row = [
                    c.id,
                    `"${(c.teacherName||'').replace(/"/g, '""')}"`,
                    `"${(c.coachName||'').replace(/"/g, '""')}"`,
                    `"${(c.subject||'').replace(/"/g, '""')}"`,
                    `"${(c.bigIdea||'').replace(/"/g, '""')}"`,
                    `"${(c.essentialQuestion||'').replace(/"/g, '""')}"`,
                    `"${(c.challenge||'').replace(/"/g, '""')}"`,
                    c.stage,
                    `"${(c.strategy||'').replace(/"/g, '""')}"`,
                    `"${(c.targetMetric||'').replace(/"/g, '""')}"`,
                    `"${(c.actionPlan||'').replace(/"/g, '""')}"`,
                    c.impactScore,
                    `"${(c.reflection||'').replace(/"/g, '""')}"`,
                    c.observations ? c.observations.length : 0,
                    c.createdAt
                ];
                csv += row.join(",") + "\n";
            });

            const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' });
            const link = document.createElement("a");
            const url = URL.createObjectURL(blob);
            link.setAttribute("href", url);
            link.setAttribute("download", `Challenge_Coaching_Aggregated_${new Date().toISOString().split('T')[0]}.csv`);
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
            showToast("Google Sheet CSV exported successfully!");
        }

        function copyTableToClipboard() {
            let tsv = "Teacher\tCoach\tBig Idea\tChallenge Focus\tStage\tStrategy\tImpact Score\n";
            cycles.forEach(c => {
                tsv += `${c.teacherName}\t${c.coachName}\t${c.bigIdea}\t${c.challenge}\t${c.stage}\t${c.strategy||'N/A'}\t${c.impactScore||'0'}\n`;
            });
            copyToClipboard(tsv);
            showToast("Table data copied! Paste directly into Google Sheets.");
        }

        // ================= UTILITIES =================
        function copyToClipboard(text) {
            const textarea = document.createElement('textarea');
            textarea.value = text;
            document.body.appendChild(textarea);
            textarea.select();
            document.execCommand('copy');
            document.body.removeChild(textarea);
        }

        function escapeHtml(str) {
            if (!str) return '';
            return str.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;").replace(/"/g, "&quot;");
        }

        function showToast(msg) {
            const toast = document.getElementById('toast');
            document.getElementById('toast-message').innerText = msg;
            toast.classList.remove('translate-y-20', 'opacity-0', 'pointer-events-none');
            setTimeout(() => {
                toast.classList.add('translate-y-20', 'opacity-0', 'pointer-events-none');
            }, 3000);
        }
    </script>
</body>
</html>
