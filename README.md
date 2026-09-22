# my-city-rankings

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Global City Tracker & Ranker (>200k Metro)</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Google Font Inter -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&display=swap" rel="stylesheet">
    <!-- Leaflet JS Map CSS -->
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY=" crossorigin=""/>
    <!-- Leaflet JS Library -->
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js" integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo=" crossorigin=""></script>
    
    <style>
        body {
            font-family: 'Inter', sans-serif;
        }
        .custom-scrollbar::-webkit-scrollbar {
            width: 8px;
        }
        .custom-scrollbar::-webkit-scrollbar-track {
            background: #f1f5f9;
            border-radius: 4px;
        }
        .custom-scrollbar::-webkit-scrollbar-thumb {
            background: #cbd5e1;
            border-radius: 4px;
        }
        .custom-scrollbar::-webkit-scrollbar-thumb:hover {
            background: #94a3b8;
        }
        .dragging {
            opacity: 0.5;
            background-color: #f1f5f9;
            border: 2px dashed #6366f1;
        }
        .drag-over {
            border-top: 3px solid #4f46e5;
        }
        /* Custom Leaflet Map Pins */
        .trophy-marker {
            display: flex;
            align-items: center;
            justify-content: center;
            border-radius: 50%;
            color: white;
            box-shadow: 0 4px 10px rgba(0,0,0,0.35);
            border: 2px solid white;
            transition: transform 0.2s ease;
        }
        .trophy-marker:hover {
            transform: scale(1.18);
            z-index: 1000 !important;
        }
        .trophy-gold {
            background: linear-gradient(135deg, #f59e0b, #d97706);
            border-color: #fef3c7;
        }
        .trophy-silver {
            background: linear-gradient(135deg, #94a3b8, #64748b);
            border-color: #f1f5f9;
        }
        .trophy-bronze {
            background: linear-gradient(135deg, #b45309, #78350f);
            border-color: #fde68a;
        }
        .standard-pin {
            background: linear-gradient(135deg, #4f46e5, #3730a3);
            border: 2px solid white;
            border-radius: 50%;
            color: white;
            font-size: 11px;
            font-weight: 700;
            display: flex;
            align-items: center;
            justify-content: center;
            box-shadow: 0 3px 8px rgba(0,0,0,0.3);
            transition: transform 0.2s ease;
        }
        .standard-pin:hover {
            transform: scale(1.15);
            z-index: 1000 !important;
        }
        .leaflet-popup-content-wrapper {
            border-radius: 12px;
            padding: 4px;
            box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.2);
        }
    </style>
</head>
<body class="bg-slate-100 min-h-screen flex flex-col h-screen overflow-hidden">

<header class="bg-slate-900 text-white shadow-md z-20 flex-none">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 h-16 flex items-center justify-between gap-2">
        
        <!-- Brand Logo -->
        <div class="flex items-center gap-3">
            <div class="bg-indigo-600 p-2 rounded-xl text-white shadow-lg shadow-indigo-600/30">
                <i class="fa-solid fa-earth-americas text-xl"></i>
            </div>
            <div>
                <h1 class="font-bold text-base sm:text-lg leading-tight flex items-center gap-2">
                    Global City Ranker
                    <span id="active-profile-badge" class="bg-indigo-900 text-indigo-300 text-[10px] font-semibold px-2 py-0.5 rounded-full border border-indigo-700">Default Profile</span>
                </h1>
                <p class="text-xs text-slate-400 hidden sm:block">Cities with &gt;200,000 Metro Population</p>
            </div>
        </div>

        <!-- Global Action Controls -->
        <div class="flex items-center gap-2 sm:gap-3">
            
            <!-- Population Toggle Switch -->
            <div class="flex items-center gap-2 bg-slate-800/80 hover:bg-slate-800 px-3 py-1.5 rounded-xl border border-slate-700/80 transition-colors">
                <span class="text-xs text-slate-300 font-medium hidden md:inline">Metro Pop</span>
                <label class="relative inline-flex items-center cursor-pointer">
                    <input type="checkbox" id="toggle-pop-visibility" onchange="togglePopulationVisibility()" class="sr-only peer" checked>
                    <div class="w-8 h-4 bg-slate-600 peer-focus:outline-none rounded-full peer peer-checked:after:translate-x-full peer-checked:after:border-white after:content-[''] after:absolute after:top-[2px] after:left-[2px] after:bg-white after:border-slate-300 after:border after:rounded-full after:h-3 after:w-3 after:transition-all peer-checked:bg-indigo-600"></div>
                </label>
            </div>

            <!-- Profile Switcher Button -->
            <button onclick="openProfileModal()" class="px-2.5 sm:px-3 py-1.5 bg-slate-800 hover:bg-slate-700 text-slate-200 rounded-xl text-xs font-semibold flex items-center gap-1.5 border border-slate-700 transition-all shadow-sm">
                <i class="fa-solid fa-user-gear text-indigo-400"></i>
                <span class="hidden sm:inline">Profile</span>
            </button>

            <!-- Share / Email Button -->
            <button onclick="openShareModal()" class="px-2.5 sm:px-3 py-1.5 bg-indigo-600 hover:bg-indigo-500 text-white rounded-xl text-xs font-semibold flex items-center gap-1.5 shadow-md shadow-indigo-600/20 transition-all">
                <i class="fa-solid fa-paper-plane"></i>
                <span class="hidden sm:inline">Share / Email</span>
            </button>

            <!-- Stats Counter Badge -->
            <div class="flex items-center gap-1.5 bg-slate-800 px-3 py-1.5 rounded-xl border border-slate-700">
                <i class="fa-solid fa-passport text-indigo-400 text-xs"></i>
                <span class="text-xs font-medium">
                    <strong id="visited-count" class="text-indigo-400 font-bold">0</strong> / <span id="total-count">0</span>
                </span>
            </div>
        </div>
    </div>
</header>

<main class="flex-1 flex flex-col max-w-7xl w-full mx-auto px-4 sm:px-6 lg:px-8 py-3 overflow-hidden">
    
    <!-- Tab Navigation Header -->
    <div class="bg-white rounded-t-xl shadow-sm border border-slate-200 border-b-0 p-3.5 flex-none">
        <div class="flex flex-col sm:flex-row items-stretch sm:items-center justify-between gap-3">
            
            <!-- View Tabs -->
            <div class="flex bg-slate-100 p-1 rounded-xl border border-slate-200">
                <button id="tab-browse" onclick="switchTab('browse')" class="flex-1 sm:flex-initial px-4 py-2 rounded-lg font-medium text-xs sm:text-sm transition-all flex items-center justify-center gap-2 bg-white text-indigo-600 shadow-sm">
                    <i class="fa-solid fa-list-ul"></i>
                    <span>Browse Cities</span>
                </button>

                <button id="tab-ranked" onclick="switchTab('ranked')" class="flex-1 sm:flex-initial px-4 py-2 rounded-lg font-medium text-xs sm:text-sm transition-all flex items-center justify-center gap-2 text-slate-600 hover:text-slate-900">
                    <i class="fa-solid fa-trophy text-amber-500"></i>
                    <span>My Ranked List</span>
                    <span id="tab-visited-badge" class="ml-0.5 bg-indigo-100 text-indigo-700 text-[10px] px-2 py-0.5 rounded-full font-bold">0</span>
                </button>

                <button id="tab-map" onclick="switchTab('map')" class="flex-1 sm:flex-initial px-4 py-2 rounded-lg font-medium text-xs sm:text-sm transition-all flex items-center justify-center gap-2 text-slate-600 hover:text-slate-900">
                    <i class="fa-solid fa-map-location-dot text-emerald-600"></i>
                    <span>Interactive Map</span>
                </button>
            </div>

            <!-- Export & Action Buttons for Ranked List -->
            <div id="ranked-actions" class="hidden flex items-center gap-2">
                <button onclick="exportList('text')" class="px-3 py-1.5 bg-slate-100 hover:bg-slate-200 text-slate-700 rounded-lg text-xs font-semibold flex items-center gap-1.5 border border-slate-300 transition-colors">
                    <i class="fa-solid fa-copy"></i> Copy Text
                </button>
                <button onclick="exportList('json')" class="px-3 py-1.5 bg-indigo-50 hover:bg-indigo-100 text-indigo-700 rounded-lg text-xs font-semibold flex items-center gap-1.5 border border-indigo-200 transition-colors">
                    <i class="fa-solid fa-download"></i> Export JSON
                </button>
                <button onclick="clearAllVisited()" class="px-3 py-1.5 bg-rose-50 hover:bg-rose-100 text-rose-700 rounded-lg text-xs font-semibold flex items-center gap-1.5 border border-rose-200 transition-colors">
                    <i class="fa-solid fa-trash-can"></i> Reset
                </button>
            </div>

            <!-- Map Action Controls -->
            <div id="map-actions" class="hidden flex items-center gap-2">
                <button onclick="fitMapToBounds()" class="px-3 py-1.5 bg-emerald-50 hover:bg-emerald-100 text-emerald-700 rounded-lg text-xs font-semibold flex items-center gap-1.5 border border-emerald-200 transition-colors">
                    <i class="fa-solid fa-expand"></i> Fit Visited Bounds
                </button>
            </div>
        </div>

        <!-- Browse Search & Filter Bar -->
        <div id="browse-filters" class="mt-3 pt-3 border-t border-slate-100 grid grid-cols-1 md:grid-cols-12 gap-2.5">
            <!-- Search Box -->
            <div class="relative md:col-span-6">
                <i class="fa-solid fa-magnifying-glass absolute left-3 top-1/2 transform -translate-y-1/2 text-slate-400 text-xs"></i>
                <input type="text" id="search-input" oninput="handleSearch()" placeholder="Search city, state/province, or country..." 
                    class="w-full pl-9 pr-8 py-2 bg-slate-50 border border-slate-300 rounded-lg text-xs sm:text-sm focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 transition-all">
                <button id="clear-search" onclick="clearSearch()" class="hidden absolute right-2.5 top-1/2 transform -translate-y-1/2 text-slate-400 hover:text-slate-600">
                    <i class="fa-solid fa-circle-xmark text-xs"></i>
                </button>
            </div>

            <!-- Continent Filter -->
            <div class="md:col-span-3">
                <select id="region-filter" onchange="handleFilter()" class="w-full py-2 px-3 bg-slate-50 border border-slate-300 rounded-lg text-xs sm:text-sm focus:outline-none focus:ring-2 focus:ring-indigo-500">
                    <option value="All">All Continents</option>
                    <option value="Asia">Asia</option>
                    <option value="Europe">Europe</option>
                    <option value="North America">North America</option>
                    <option value="South America">South America</option>
                    <option value="Africa">Africa</option>
                    <option value="Oceania">Oceania</option>
                </select>
            </div>

            <!-- Visited Status Filter -->
            <div class="md:col-span-3">
                <select id="status-filter" onchange="handleFilter()" class="w-full py-2 px-3 bg-slate-50 border border-slate-300 rounded-lg text-xs sm:text-sm focus:outline-none focus:ring-2 focus:ring-indigo-500">
                    <option value="all">Show All Statuses</option>
                    <option value="unvisited">Unvisited Only</option>
                    <option value="visited">Visited Only</option>
                </select>
            </div>
        </div>
        
        <div id="browse-sorting-info" class="mt-2 text-[11px] text-slate-500 flex flex-wrap items-center justify-between gap-1">
            <span>Sorting: <strong class="text-slate-700">Country (A-Z) &rarr; State/Province (A-Z) &rarr; Metro Pop (Desc)</strong></span>
            <span id="results-count" class="font-medium text-slate-600">Showing 0 cities</span>
        </div>
    </div>

    <div class="flex-1 bg-white border border-slate-200 border-t-0 rounded-b-xl shadow-sm overflow-hidden flex flex-col relative">
        
        <!-- VIEW 1: Browse All Cities Grid/List -->
        <div id="view-browse" class="flex-1 overflow-y-auto custom-scrollbar p-3.5 space-y-2">
            <div id="city-list-container" class="space-y-2">
                <!-- Dynamic city cards -->
            </div>
        </div>

        <!-- VIEW 2: Visited & Drag-and-Drop Ranking List -->
        <div id="view-ranked" class="hidden flex-1 overflow-y-auto custom-scrollbar p-3.5">
            <div id="ranked-instructions" class="mb-3 p-3 bg-indigo-50/80 border border-indigo-100 rounded-xl text-xs text-indigo-900 flex items-center justify-between gap-2">
                <div class="flex items-center gap-2">
                    <i class="fa-solid fa-circle-info text-indigo-500 text-sm flex-none"></i>
                    <span>Drag items using <i class="fa-solid fa-grip-vertical text-slate-400 mx-0.5"></i> or use Up/Down arrows to reorder your top visited cities!</span>
                </div>
                <span class="hidden md:inline font-bold text-[11px] text-indigo-700 bg-indigo-100 px-2.5 py-1 rounded-lg">Top 3 earn Map Trophies</span>
            </div>

            <div id="ranked-list-container" class="space-y-2">
                <!-- Ranked items rendered here -->
            </div>

            <div id="ranked-empty-state" class="hidden flex flex-col items-center justify-center py-16 text-center">
                <div class="w-16 h-16 bg-slate-100 rounded-full flex items-center justify-center text-slate-400 text-2xl mb-3">
                    <i class="fa-solid fa-city"></i>
                </div>
                <h3 class="font-bold text-slate-700 text-base mb-1">No Cities Marked as Visited</h3>
                <p class="text-xs text-slate-500 max-w-sm mb-4">Check off cities you've visited in the 'Browse Cities' tab to construct your custom map and top rankings!</p>
                <button onclick="switchTab('browse')" class="px-4 py-2 bg-indigo-600 hover:bg-indigo-700 text-white rounded-lg text-xs font-semibold shadow-sm transition-colors">
                    Browse Cities
                </button>
            </div>
        </div>

        <!-- VIEW 3: Interactive Leaflet Map -->
        <div id="view-map" class="hidden flex-1 relative flex flex-col">
            <div id="map-container" class="w-full h-full z-10"></div>
            
            <!-- Map Legend Overlay -->
            <div class="absolute bottom-4 left-4 z-20 bg-white/95 backdrop-blur-md p-3 rounded-xl shadow-lg border border-slate-200 text-xs text-slate-700 flex flex-col gap-1.5 pointer-events-auto max-w-xs">
                <span class="font-bold text-slate-900 text-[11px] uppercase tracking-wider mb-0.5">Map Legend</span>
                <div class="flex items-center gap-2">
                    <span class="w-5 h-5 rounded-full bg-gradient-to-br from-amber-400 to-amber-600 text-white flex items-center justify-center text-[10px] shadow-sm"><i class="fa-solid fa-trophy"></i></span>
                    <span class="font-semibold text-slate-800">#1 Top Rated (Gold)</span>
                </div>
                <div class="flex items-center gap-2">
                    <span class="w-5 h-5 rounded-full bg-gradient-to-br from-slate-300 to-slate-500 text-white flex items-center justify-center text-[10px] shadow-sm"><i class="fa-solid fa-trophy"></i></span>
                    <span class="font-semibold text-slate-800">#2 Rank (Silver)</span>
                </div>
                <div class="flex items-center gap-2">
                    <span class="w-5 h-5 rounded-full bg-gradient-to-br from-amber-700 to-amber-900 text-white flex items-center justify-center text-[10px] shadow-sm"><i class="fa-solid fa-trophy"></i></span>
                    <span class="font-semibold text-slate-800">#3 Rank (Bronze)</span>
                </div>
                <div class="flex items-center gap-2">
                    <span class="w-5 h-5 rounded-full bg-indigo-700 text-white flex items-center justify-center text-[10px] font-bold shadow-sm">4+</span>
                    <span class="text-slate-600">Other Visited Cities</span>
                </div>
            </div>
        </div>

    </div>
</main>

<!-- Profile Modal -->
<div id="modal-profile" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 flex items-center justify-center p-4 hidden">
    <div class="bg-white rounded-2xl shadow-2xl max-w-md w-full p-6 border border-slate-100 flex flex-col gap-4">
        <div class="flex items-center justify-between border-b border-slate-100 pb-3">
            <h3 class="font-bold text-slate-800 text-base flex items-center gap-2">
                <i class="fa-solid fa-user-circle text-indigo-600 text-lg"></i>
                Profile & User Selections
            </h3>
            <button onclick="closeProfileModal()" class="text-slate-400 hover:text-slate-600 p-1">
                <i class="fa-solid fa-xmark text-lg"></i>
            </button>
        </div>

        <div class="space-y-3">
            <div>
                <label class="block text-xs font-semibold text-slate-700 mb-1">Current User Profile</label>
                <select id="profile-select" onchange="switchProfile(this.value)" class="w-full py-2 px-3 bg-slate-50 border border-slate-300 rounded-xl text-xs sm:text-sm font-medium focus:ring-2 focus:ring-indigo-500">
                    <!-- Profiles dynamically rendered -->
                </select>
            </div>

            <div class="pt-2 border-t border-slate-100">
                <label class="block text-xs font-semibold text-slate-700 mb-1">Create New Profile</label>
                <div class="flex gap-2">
                    <input type="text" id="new-profile-input" placeholder="e.g. Vacation Wishlist, Alex's Travels" class="flex-1 py-2 px-3 bg-slate-50 border border-slate-300 rounded-xl text-xs sm:text-sm focus:ring-2 focus:ring-indigo-500">
                    <button onclick="createNewProfile()" class="px-4 py-2 bg-indigo-600 hover:bg-indigo-700 text-white rounded-xl text-xs font-semibold shadow-sm transition-colors">
                        Add
                    </button>
                </div>
            </div>

            <div class="pt-2 border-t border-slate-100 flex justify-between items-center text-xs">
                <span class="text-slate-500">Import/Export Profile Data</span>
                <label class="cursor-pointer px-3 py-1.5 bg-slate-100 hover:bg-slate-200 text-slate-700 font-semibold rounded-lg border border-slate-300 transition-colors">
                    <i class="fa-solid fa-upload mr-1"></i> Import JSON
                    <input type="file" id="import-json-file" accept=".json" onchange="importJSONState(event)" class="hidden">
                </label>
            </div>
        </div>

        <div class="pt-3 border-t border-slate-100 flex justify-end">
            <button onclick="closeProfileModal()" class="px-4 py-2 bg-slate-800 text-white rounded-xl text-xs font-semibold">Done</button>
        </div>
    </div>
</div>

<!-- Share / Email Modal -->
<div id="modal-share" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 flex items-center justify-center p-4 hidden">
    <div class="bg-white rounded-2xl shadow-2xl max-w-lg w-full p-6 border border-slate-100 flex flex-col gap-4">
        <div class="flex items-center justify-between border-b border-slate-100 pb-3">
            <h3 class="font-bold text-slate-800 text-base flex items-center gap-2">
                <i class="fa-solid fa-paper-plane text-indigo-600 text-lg"></i>
                Email or Share Your Rankings
            </h3>
            <button onclick="closeShareModal()" class="text-slate-400 hover:text-slate-600 p-1">
                <i class="fa-solid fa-xmark text-lg"></i>
            </button>
        </div>

        <div class="space-y-3">
            <div>
                <label class="block text-xs font-semibold text-slate-700 mb-1">Your Email (Optional)</label>
                <input type="email" id="share-email-input" placeholder="traveler@example.com" class="w-full py-2 px-3 bg-slate-50 border border-slate-300 rounded-xl text-xs sm:text-sm focus:ring-2 focus:ring-indigo-500">
            </div>

            <div>
                <label class="block text-xs font-semibold text-slate-700 mb-1">Formatted Ranking Preview</label>
                <textarea id="share-text-preview" readonly class="w-full h-36 p-3 bg-slate-50 border border-slate-200 rounded-xl text-xs font-mono text-slate-700 resize-none custom-scrollbar"></textarea>
            </div>
        </div>

        <div class="pt-3 border-t border-slate-100 flex items-center justify-between gap-2">
            <button onclick="copyShareText()" class="px-4 py-2 bg-slate-100 hover:bg-slate-200 text-slate-700 rounded-xl text-xs font-semibold flex items-center gap-1.5 border border-slate-300">
                <i class="fa-solid fa-copy"></i> Copy Text
            </button>
            
            <div class="flex items-center gap-2">
                <button onclick="closeShareModal()" class="px-4 py-2 bg-slate-100 hover:bg-slate-200 text-slate-600 rounded-xl text-xs font-semibold">Cancel</button>
                <button onclick="sendEmailCopy()" class="px-4 py-2 bg-indigo-600 hover:bg-indigo-700 text-white rounded-xl text-xs font-semibold shadow-sm flex items-center gap-1.5">
                    <i class="fa-solid fa-envelope"></i> Open Email App
                </button>
            </div>
        </div>
    </div>
</div>

<!-- Toast Notification Popup -->
<div id="toast" class="fixed bottom-5 right-5 bg-slate-900 text-white px-4 py-3 rounded-xl shadow-xl text-xs font-medium translate-y-20 opacity-0 transition-all duration-300 flex items-center gap-2.5 z-50 pointer-events-none">
    <i id="toast-icon" class="fa-solid fa-circle-check text-emerald-400 text-sm"></i>
    <span id="toast-message">Notification</span>
</div>

<script>
    // Comprehensive Dataset of Global Cities (>200,000 Metro Population)
    const CITIES_DATA = [
        // Asia
        { name: "Tokyo", state: "Tokyo", country: "Japan", continent: "Asia", pop: 37400000, lat: 35.6762, lng: 139.6503 },
        { name: "Yokohama", state: "Kanagawa", country: "Japan", continent: "Asia", pop: 3770000, lat: 35.4437, lng: 139.6380 },
        { name: "Osaka", state: "Osaka", country: "Japan", continent: "Asia", pop: 19200000, lat: 34.6937, lng: 135.5023 },
        { name: "Nagoya", state: "Aichi", country: "Japan", continent: "Asia", pop: 9500000, lat: 35.1815, lng: 136.9066 },
        { name: "Kyoto", state: "Kyoto", country: "Japan", continent: "Asia", pop: 1470000, lat: 35.0116, lng: 135.7681 },
        { name: "Sapporo", state: "Hokkaido", country: "Japan", continent: "Asia", pop: 1950000, lat: 43.0618, lng: 141.3545 },
        { name: "Fukuoka", state: "Fukuoka", country: "Japan", continent: "Asia", pop: 1580000, lat: 33.5904, lng: 130.4017 },
        { name: "Kobe", state: "Hyogo", country: "Japan", continent: "Asia", pop: 1530000, lat: 34.6901, lng: 135.1955 },
        { name: "Delhi", state: "Delhi", country: "India", continent: "Asia", pop: 32900000, lat: 28.6139, lng: 77.2090 },
        { name: "Mumbai", state: "Maharashtra", country: "India", continent: "Asia", pop: 21200000, lat: 19.0760, lng: 72.8777 },
        { name: "Kolkata", state: "West Bengal", country: "India", continent: "Asia", pop: 15300000, lat: 22.5726, lng: 88.3639 },
        { name: "Bengaluru", state: "Karnataka", country: "India", continent: "Asia", pop: 13600000, lat: 12.9716, lng: 77.5946 },
        { name: "Chennai", state: "Tamil Nadu", country: "India", continent: "Asia", pop: 11900000, lat: 13.0827, lng: 80.2707 },
        { name: "Hyderabad", state: "Telangana", country: "India", continent: "Asia", pop: 10800000, lat: 17.3850, lng: 78.4867 },
        { name: "Ahmedabad", state: "Gujarat", country: "India", continent: "Asia", pop: 8600000, lat: 23.0225, lng: 72.5714 },
        { name: "Pune", state: "Maharashtra", country: "India", continent: "Asia", pop: 7000000, lat: 18.5204, lng: 73.8567 },
        { name: "Jaipur", state: "Rajasthan", country: "India", continent: "Asia", pop: 4100000, lat: 26.9124, lng: 75.7873 },
        { name: "Shanghai", state: "Shanghai", country: "China", continent: "Asia", pop: 28500000, lat: 31.2304, lng: 121.4737 },
        { name: "Beijing", state: "Beijing", country: "China", continent: "Asia", pop: 21800000, lat: 39.9042, lng: 116.4074 },
        { name: "Guangzhou", state: "Guangdong", country: "China", continent: "Asia", pop: 18700000, lat: 23.1291, lng: 113.2644 },
        { name: "Shenzhen", state: "Guangdong", country: "China", continent: "Asia", pop: 17500000, lat: 22.5431, lng: 114.0579 },
        { name: "Chengdu", state: "Sichuan", country: "China", continent: "Asia", pop: 20900000, lat: 30.5728, lng: 104.0668 },
        { name: "Chongqing", state: "Chongqing", country: "China", continent: "Asia", pop: 16800000, lat: 29.5630, lng: 106.5516 },
        { name: "Wuhan", state: "Hubei", country: "China", continent: "Asia", pop: 12300000, lat: 30.5928, lng: 114.3055 },
        { name: "Xi'an", state: "Shaanxi", country: "China", continent: "Asia", pop: 12900000, lat: 34.3416, lng: 108.9398 },
        { name: "Hangzhou", state: "Zhejiang", country: "China", continent: "Asia", pop: 11900000, lat: 30.2741, lng: 120.1551 },
        { name: "Jakarta", state: "Jakarta", country: "Indonesia", continent: "Asia", pop: 10500000, lat: -6.2088, lng: 106.8456 },
        { name: "Surabaya", state: "East Java", country: "Indonesia", continent: "Asia", pop: 3000000, lat: -7.2575, lng: 112.7521 },
        { name: "Seoul", state: "Seoul", country: "South Korea", continent: "Asia", pop: 9900000, lat: 37.5665, lng: 126.9780 },
        { name: "Busan", state: "Busan", country: "South Korea", continent: "Asia", pop: 3400000, lat: 35.1796, lng: 129.0756 },
        { name: "Manila", state: "Metro Manila", country: "Philippines", continent: "Asia", pop: 14400000, lat: 14.5995, lng: 120.9842 },
        { name: "Bangkok", state: "Bangkok", country: "Thailand", continent: "Asia", pop: 10700000, lat: 13.7563, lng: 100.5018 },
        { name: "Chiang Mai", state: "Chiang Mai", country: "Thailand", continent: "Asia", pop: 1200000, lat: 18.7883, lng: 98.9853 },
        { name: "Ho Chi Minh City", state: "Ho Chi Minh", country: "Vietnam", continent: "Asia", pop: 9000000, lat: 10.8231, lng: 106.6297 },
        { name: "Hanoi", state: "Hanoi", country: "Vietnam", continent: "Asia", pop: 8400000, lat: 21.0285, lng: 105.8542 },
        { name: "Kuala Lumpur", state: "Federal Territory", country: "Malaysia", continent: "Asia", pop: 8200000, lat: 3.1390, lng: 101.6869 },
        { name: "Singapore", state: "Singapore", country: "Singapore", continent: "Asia", pop: 5900000, lat: 1.3521, lng: 103.8198 },
        { name: "Riyadh", state: "Riyadh", country: "Saudi Arabia", continent: "Asia", pop: 7600000, lat: 24.7136, lng: 46.6753 },
        { name: "Dubai", state: "Dubai", country: "United Arab Emirates", continent: "Asia", pop: 3500000, lat: 25.2048, lng: 55.2708 },

        // North America
        { name: "New York City", state: "New York", country: "United States", continent: "North America", pop: 19800000, lat: 40.7128, lng: -74.0060 },
        { name: "Los Angeles", state: "California", country: "United States", continent: "North America", pop: 12800000, lat: 34.0522, lng: -118.2437 },
        { name: "San Francisco", state: "California", country: "United States", continent: "North America", pop: 4600000, lat: 37.7749, lng: -122.4194 },
        { name: "Chicago", state: "Illinois", country: "United States", continent: "North America", pop: 9400000, lat: 41.8781, lng: -87.6298 },
        { name: "Houston", state: "Texas", country: "United States", continent: "North America", pop: 7100000, lat: 29.7604, lng: -95.3698 },
        { name: "Austin", state: "Texas", country: "United States", continent: "North America", pop: 2300000, lat: 30.2672, lng: -97.7431 },
        { name: "Miami", state: "Florida", country: "United States", continent: "North America", pop: 6100000, lat: 25.7617, lng: -80.1918 },
        { name: "Seattle", state: "Washington", country: "United States", continent: "North America", pop: 4000000, lat: 47.6062, lng: -122.3321 },
        { name: "Boston", state: "Massachusetts", country: "United States", continent: "North America", pop: 4900000, lat: 42.3601, lng: -71.0589 },
        { name: "Washington", state: "District of Columbia", country: "United States", continent: "North America", pop: 6300000, lat: 38.9072, lng: -77.0369 },
        { name: "Toronto", state: "Ontario", country: "Canada", continent: "North America", pop: 6300000, lat: 43.6532, lng: -79.3832 },
        { name: "Montreal", state: "Quebec", country: "Canada", continent: "North America", pop: 4250000, lat: 45.5017, lng: -73.5673 },
        { name: "Vancouver", state: "British Columbia", country: "Canada", continent: "North America", pop: 2600000, lat: 49.2827, lng: -123.1207 },
        { name: "Mexico City", state: "CDMX", country: "Mexico", continent: "North America", pop: 21800000, lat: 19.4326, lng: -99.1332 },
        { name: "Guadalajara", state: "Jalisco", country: "Mexico", continent: "North America", pop: 5200000, lat: 20.6597, lng: -103.3496 },

        // Europe
        { name: "London", state: "Greater London", country: "United Kingdom", continent: "Europe", pop: 9500000, lat: 51.5074, lng: -0.1278 },
        { name: "Manchester", state: "Greater Manchester", country: "United Kingdom", continent: "Europe", pop: 2700000, lat: 53.4808, lng: -2.2426 },
        { name: "Edinburgh", state: "Scotland", country: "United Kingdom", continent: "Europe", pop: 540000, lat: 55.9533, lng: -3.1883 },
        { name: "Paris", state: "Île-de-France", country: "France", continent: "Europe", pop: 11100000, lat: 48.8566, lng: 2.3522 },
        { name: "Lyon", state: "Auvergne-Rhône-Alpes", country: "France", continent: "Europe", pop: 1700000, lat: 45.7640, lng: 4.8357 },
        { name: "Berlin", state: "Berlin", country: "Germany", continent: "Europe", pop: 3800000, lat: 52.5200, lng: 13.4050 },
        { name: "Munich", state: "Bavaria", country: "Germany", continent: "Europe", pop: 1500000, lat: 48.1351, lng: 11.5820 },
        { name: "Hamburg", state: "Hamburg", country: "Germany", continent: "Europe", pop: 1900000, lat: 53.5511, lng: 9.9937 },
        { name: "Rome", state: "Lazio", country: "Italy", continent: "Europe", pop: 4300000, lat: 41.9028, lng: 12.4964 },
        { name: "Milan", state: "Lombardy", country: "Italy", continent: "Europe", pop: 3100000, lat: 45.4642, lng: 9.1900 },
        { name: "Florence", state: "Tuscany", country: "Italy", continent: "Europe", pop: 700000, lat: 43.7696, lng: 11.2558 },
        { name: "Venice", state: "Veneto", country: "Italy", continent: "Europe", pop: 260000, lat: 45.4408, lng: 12.3155 },
        { name: "Madrid", state: "Madrid", country: "Spain", continent: "Europe", pop: 6700000, lat: 40.4168, lng: -3.7038 },
        { name: "Barcelona", state: "Catalonia", country: "Spain", continent: "Europe", pop: 5600000, lat: 41.3851, lng: 2.1734 },
        { name: "Amsterdam", state: "North Holland", country: "Netherlands", continent: "Europe", pop: 2400000, lat: 52.3676, lng: 4.9041 },
        { name: "Vienna", state: "Vienna", country: "Austria", continent: "Europe", pop: 1900000, lat: 48.2082, lng: 16.3738 },
        { name: "Prague", state: "Prague", country: "Czech Republic", continent: "Europe", pop: 1300000, lat: 50.0755, lng: 14.4378 },

        // South America
        { name: "São Paulo", state: "São Paulo", country: "Brazil", continent: "South America", pop: 22400000, lat: -23.5505, lng: -46.6333 },
        { name: "Rio de Janeiro", state: "Rio de Janeiro", country: "Brazil", continent: "South America", pop: 13300000, lat: -22.9068, lng: -43.1729 },
        { name: "Buenos Aires", state: "Capital Federal", country: "Argentina", continent: "South America", pop: 15300000, lat: -34.6037, lng: -58.3816 },
        { name: "Bogotá", state: "Cundinamarca", country: "Colombia", continent: "South America", pop: 11300000, lat: 4.7110, lng: -74.0721 },
        { name: "Lima", state: "Lima", country: "Peru", continent: "South America", pop: 11000000, lat: -12.0464, lng: -77.0428 },
        { name: "Santiago", state: "Santiago Metropolitan", country: "Chile", continent: "South America", pop: 6800000, lat: -33.4489, lng: -70.6693 },

        // Africa
        { name: "Cairo", state: "Cairo", country: "Egypt", continent: "Africa", pop: 21700000, lat: 30.0444, lng: 31.2357 },
        { name: "Lagos", state: "Lagos", country: "Nigeria", continent: "Africa", pop: 15400000, lat: 6.5244, lng: 3.3792 },
        { name: "Johannesburg", state: "Gauteng", country: "South Africa", continent: "Africa", pop: 6000000, lat: -26.2041, lng: 28.0473 },
        { name: "Cape Town", state: "Western Cape", country: "South Africa", continent: "Africa", pop: 4700000, lat: -33.9249, lng: 18.4241 },
        { name: "Nairobi", state: "Nairobi", country: "Kenya", continent: "Africa", pop: 5100000, lat: -1.2921, lng: 36.8219 },

        // Oceania
        { name: "Sydney", state: "New South Wales", country: "Australia", continent: "Oceania", pop: 5300000, lat: -33.8688, lng: 151.2093 },
        { name: "Melbourne", state: "Victoria", country: "Australia", continent: "Oceania", pop: 5000000, lat: -37.8136, lng: 144.9631 },
        { name: "Brisbane", state: "Queensland", country: "Australia", continent: "Oceania", pop: 2500000, lat: -27.4705, lng: 153.0260 },
        { name: "Auckland", state: "Auckland", country: "New Zealand", continent: "Oceania", pop: 1650000, lat: -36.8485, lng: 174.7633 }
    ];

    // Helper: Generate unique ID for a city
    function getCityId(city) {
        return `${city.name}-${city.state}-${city.country}`.replace(/\s+/g, '_').toLowerCase();
    }

    // Assign IDs to master dataset
    CITIES_DATA.forEach(c => c.id = getCityId(c));

    // Sort Master Data Primary: Country (A-Z) -> State (A-Z) -> Metro Pop (Desc)
    CITIES_DATA.sort((a, b) => {
        if (a.country !== b.country) return a.country.localeCompare(b.country);
        if (a.state !== b.state) return a.state.localeCompare(b.state);
        return b.pop - a.pop;
    });

    // App State Variables
    let appState = {
        activeProfile: "Default Profile",
        profiles: {
            "Default Profile": [] // Array of city IDs in ranked order
        },
        showPopulation: true,
        currentTab: "browse"
    };

    let leafletMap = null;
    let mapMarkers = [];
    let draggedIndex = null;

    // Local Storage Sync
    function loadState() {
        const saved = localStorage.getItem('global_city_ranker_state');
        if (saved) {
            try {
                const parsed = JSON.parse(saved);
                appState = { ...appState, ...parsed };
            } catch(e) {
                console.error("Failed to parse local storage:", e);
            }
        }
        document.getElementById('toggle-pop-visibility').checked = appState.showPopulation;
        updateProfileBadge();
    }

    function saveState() {
        localStorage.setItem('global_city_ranker_state', JSON.stringify(appState));
        updateCounters();
    }

    function getActiveRankedIds() {
        if (!appState.profiles[appState.activeProfile]) {
            appState.profiles[appState.activeProfile] = [];
        }
        return appState.profiles[appState.activeProfile];
    }

    // UI Formatting Helpers
    function formatPop(num) {
        if (!appState.showPopulation) return '';
        if (num >= 1000000) {
            return `(${(num / 1000000).toFixed(1)}M)`;
        }
        return `(${(num / 1000).toFixed(0)}k)`;
    }

    function showToast(msg, iconClass = "fa-circle-check text-emerald-400") {
        const toast = document.getElementById('toast');
        const icon = document.getElementById('toast-icon');
        const text = document.getElementById('toast-message');
        
        icon.className = `fa-solid ${iconClass} text-sm`;
        text.textContent = msg;
        
        toast.classList.remove('translate-y-20', 'opacity-0');
        toast.classList.add('translate-y-0', 'opacity-100');
        
        setTimeout(() => {
            toast.classList.remove('translate-y-0', 'opacity-100');
            toast.classList.add('translate-y-20', 'opacity-0');
        }, 2500);
    }

    // UI Render Controllers
    function updateCounters() {
        const activeIds = getActiveRankedIds();
        document.getElementById('visited-count').textContent = activeIds.length;
        document.getElementById('total-count').textContent = CITIES_DATA.length;
        document.getElementById('tab-visited-badge').textContent = activeIds.length;
    }

    function renderBrowseView() {
        const container = document.getElementById('city-list-container');
        const search = document.getElementById('search-input').value.toLowerCase().trim();
        const region = document.getElementById('region-filter').value;
        const status = document.getElementById('status-filter').value;
        const activeIds = getActiveRankedIds();

        const filtered = CITIES_DATA.filter(city => {
            const matchesSearch = city.name.toLowerCase().includes(search) || 
                                  city.state.toLowerCase().includes(search) || 
                                  city.country.toLowerCase().includes(search);
            const matchesRegion = region === "All" || city.continent === region;
            const isVisited = activeIds.includes(city.id);
            const matchesStatus = status === "all" || 
                                 (status === "visited" && isVisited) || 
                                 (status === "unvisited" && !isVisited);
            return matchesSearch && matchesRegion && matchesStatus;
        });

        document.getElementById('results-count').textContent = `Showing ${filtered.length} cities`;

        if (filtered.length === 0) {
            container.innerHTML = `
                <div class="py-12 text-center text-slate-400 text-xs">
                    <i class="fa-solid fa-filter-circle-xmark text-2xl mb-2 text-slate-300"></i>
                    <p>No cities matching current filter criteria.</p>
                </div>
            `;
            return;
        }

        container.innerHTML = filtered.map(city => {
            const isVisited = activeIds.includes(city.id);
            const rankIndex = activeIds.indexOf(city.id);
            const popBadge = appState.showPopulation ? `<span class="text-slate-400 font-normal text-xs ml-1">${formatPop(city.pop)}</span>` : '';

            return `
                <div class="p-3 bg-slate-50 hover:bg-slate-100/80 border ${isVisited ? 'border-indigo-200 bg-indigo-50/30' : 'border-slate-200'} rounded-xl flex items-center justify-between gap-3 transition-all">
                    <div class="flex items-center gap-3 min-w-0">
                        <input type="checkbox" onchange="toggleVisited('${city.id}')" ${isVisited ? 'checked' : ''} 
                            class="w-4 h-4 text-indigo-600 rounded border-slate-300 focus:ring-indigo-500 cursor-pointer">
                        <div class="min-w-0">
                            <h4 class="font-bold text-slate-800 text-xs sm:text-sm truncate flex items-center gap-1">
                                <span>${city.name}</span>
                                ${popBadge}
                            </h4>
                            <p class="text-[11px] text-slate-500 truncate">${city.state}, <strong class="text-slate-600">${city.country}</strong></p>
                        </div>
                    </div>
                    
                    <div class="flex items-center gap-2 flex-none">
                        ${isVisited ? `<span class="bg-indigo-100 text-indigo-700 font-bold text-[10px] px-2 py-0.5 rounded-full">Rank #${rankIndex + 1}</span>` : ''}
                        <span class="text-[10px] font-semibold text-slate-400 bg-slate-200/60 px-2 py-0.5 rounded-md">${city.continent}</span>
                    </div>
                </div>
            `;
        }).join('');
    }

    function renderRankedView() {
        const container = document.getElementById('ranked-list-container');
        const emptyState = document.getElementById('ranked-empty-state');
        const instructions = document.getElementById('ranked-instructions');
        const activeIds = getActiveRankedIds();

        if (activeIds.length === 0) {
            container.innerHTML = '';
            emptyState.classList.remove('hidden');
            instructions.classList.add('hidden');
            return;
        }

        emptyState.classList.add('hidden');
        instructions.classList.remove('hidden');

        const rankedCities = activeIds.map(id => CITIES_DATA.find(c => c.id === id)).filter(Boolean);

        container.innerHTML = rankedCities.map((city, index) => {
            let trophyClass = "bg-slate-100 text-slate-600 border-slate-200";
            let trophyIcon = `${index + 1}`;
            
            if (index === 0) {
                trophyClass = "bg-gradient-to-br from-amber-400 to-amber-600 text-white border-amber-300 shadow-md shadow-amber-500/20";
                trophyIcon = '<i class="fa-solid fa-trophy text-xs"></i>';
            } else if (index === 1) {
                trophyClass = "bg-gradient-to-br from-slate-300 to-slate-500 text-white border-slate-200 shadow-md";
                trophyIcon = '<i class="fa-solid fa-trophy text-xs"></i>';
            } else if (index === 2) {
                trophyClass = "bg-gradient-to-br from-amber-700 to-amber-900 text-white border-amber-600 shadow-md";
                trophyIcon = '<i class="fa-solid fa-trophy text-xs"></i>';
            }

            const popBadge = appState.showPopulation ? `<span class="text-slate-400 font-normal text-xs ml-1">${formatPop(city.pop)}</span>` : '';

            return `
                <div draggable="true" 
                     ondragstart="handleDragStart(event, ${index})" 
                     ondragover="handleDragOver(event, ${index})" 
                     ondrop="handleDrop(event, ${index})" 
                     ondragend="handleDragEnd(event)"
                     class="p-3 bg-white hover:bg-slate-50 border border-slate-200 rounded-xl flex items-center justify-between gap-3 shadow-sm transition-all group cursor-grab active:cursor-grabbing">
                    
                    <div class="flex items-center gap-3 min-w-0">
                        <i class="fa-solid fa-grip-vertical text-slate-300 group-hover:text-slate-400 text-xs flex-none"></i>
                        
                        <div class="w-7 h-7 rounded-full border flex items-center justify-center font-bold text-xs flex-none ${trophyClass}">
                            ${trophyIcon}
                        </div>

                        <div class="min-w-0">
                            <h4 class="font-bold text-slate-800 text-xs sm:text-sm truncate">
                                ${city.name} ${popBadge}
                            </h4>
                            <p class="text-[11px] text-slate-500 truncate">${city.state}, <strong class="text-slate-600">${city.country}</strong></p>
                        </div>
                    </div>

                    <div class="flex items-center gap-1.5 flex-none">
                        <button onclick="moveRank(${index}, -1)" ${index === 0 ? 'disabled class="opacity-20 cursor-not-allowed"' : 'class="p-1.5 hover:bg-slate-100 rounded-lg text-slate-500 hover:text-indigo-600 text-xs"'}>
                            <i class="fa-solid fa-chevron-up"></i>
                        </button>
                        <button onclick="moveRank(${index}, 1)" ${index === activeIds.length - 1 ? 'disabled class="opacity-20 cursor-not-allowed"' : 'class="p-1.5 hover:bg-slate-100 rounded-lg text-slate-500 hover:text-indigo-600 text-xs"'}>
                            <i class="fa-solid fa-chevron-down"></i>
                        </button>
                        <button onclick="toggleVisited('${city.id}')" title="Remove from visited" class="p-1.5 hover:bg-rose-50 rounded-lg text-slate-400 hover:text-rose-600 text-xs ml-1 transition-colors">
                            <i class="fa-solid fa-xmark"></i>
                        </button>
                    </div>
                </div>
            `;
        }).join('');
    }

    function initMap() {
        if (leafletMap) return;
        
        // Initialize Leaflet Map centered on world view
        leafletMap = L.map('map-container', {
            center: [20, 0],
            zoom: 2,
            zoomControl: false
        });

        L.control.zoom({ position: 'topright' }).addTo(leafletMap);

        // Standard OpenStreetMap tiles
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            maxZoom: 18,
            attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
        }).addTo(leafletMap);
    }

    function renderMapView() {
        initMap();
        
        // Clear previous markers
        mapMarkers.forEach(m => leafletMap.removeLayer(m));
        mapMarkers = [];

        const activeIds = getActiveRankedIds();
        if (activeIds.length === 0) return;

        const bounds = [];

        activeIds.forEach((id, index) => {
            const city = CITIES_DATA.find(c => c.id === id);
            if (!city) return;

            bounds.push([city.lat, city.lng]);

            let markerHtml = '';
            let iconSize = [28, 28];

            if (index === 0) {
                markerHtml = `<div class="trophy-marker trophy-gold w-8 h-8"><i class="fa-solid fa-trophy text-xs"></i></div>`;
                iconSize = [32, 32];
            } else if (index === 1) {
                markerHtml = `<div class="trophy-marker trophy-silver w-7 h-7"><i class="fa-solid fa-trophy text-[11px]"></i></div>`;
                iconSize = [28, 28];
            } else if (index === 2) {
                markerHtml = `<div class="trophy-marker trophy-bronze w-7 h-7"><i class="fa-solid fa-trophy text-[11px]"></i></div>`;
                iconSize = [28, 28];
            } else {
                markerHtml = `<div class="standard-pin w-6 h-6">${index + 1}</div>`;
                iconSize = [24, 24];
            }

            const customIcon = L.divIcon({
                html: markerHtml,
                className: '',
                iconSize: iconSize,
                iconAnchor: [iconSize[0]/2, iconSize[1]/2]
            });

            const popText = appState.showPopulation ? `<div class="text-xs text-slate-500 mt-0.5">Metro Pop: <strong>${city.pop.toLocaleString()}</strong></div>` : '';

            const popupHtml = `
                <div class="p-1 text-center">
                    <span class="text-[10px] uppercase tracking-wider font-bold text-indigo-600 bg-indigo-50 px-2 py-0.5 rounded-full">Rank #${index + 1}</span>
                    <h4 class="font-bold text-slate-900 text-sm mt-1">${city.name}</h4>
                    <p class="text-xs text-slate-600">${city.state}, ${city.country}</p>
                    ${popText}
                </div>
            `;

            const marker = L.marker([city.lat, city.lng], { icon: customIcon })
                .bindPopup(popupHtml)
                .addTo(leafletMap);

            mapMarkers.push(marker);
        });

        // Fit map bounds if markers exist
        if (bounds.length > 0) {
            setTimeout(() => {
                leafletMap.invalidateSize();
                leafletMap.fitBounds(bounds, { padding: [50, 50], maxZoom: 8 });
            }, 200);
        }
    }

    function fitMapToBounds() {
        if (!leafletMap || mapMarkers.length === 0) return;
        const activeIds = getActiveRankedIds();
        const bounds = activeIds.map(id => {
            const c = CITIES_DATA.find(x => x.id === id);
            return c ? [c.lat, c.lng] : null;
        }).filter(Boolean);

        if (bounds.length > 0) {
            leafletMap.fitBounds(bounds, { padding: [50, 50], maxZoom: 8 });
        }
    }

    // Interactive Actions & Tab Switchers
    function switchTab(tabName) {
        appState.currentTab = tabName;
        
        // Tab UI button styling
        ['browse', 'ranked', 'map'].forEach(t => {
            const btn = document.getElementById(`tab-${t}`);
            const view = document.getElementById(`view-${t}`);
            
            if (t === tabName) {
                btn.className = "flex-1 sm:flex-initial px-4 py-2 rounded-lg font-medium text-xs sm:text-sm transition-all flex items-center justify-center gap-2 bg-white text-indigo-600 shadow-sm";
                view.classList.remove('hidden');
            } else {
                btn.className = "flex-1 sm:flex-initial px-4 py-2 rounded-lg font-medium text-xs sm:text-sm transition-all flex items-center justify-center gap-2 text-slate-600 hover:text-slate-900";
                view.classList.add('hidden');
            }
        });

        // Toggle action bar visibility
        document.getElementById('browse-filters').style.display = (tabName === 'browse') ? 'grid' : 'none';
        document.getElementById('browse-sorting-info').style.display = (tabName === 'browse') ? 'flex' : 'none';
        document.getElementById('ranked-actions').classList.toggle('hidden', tabName !== 'ranked');
        document.getElementById('map-actions').classList.toggle('hidden', tabName !== 'map');

        // Render appropriate tab
        if (tabName === 'browse') renderBrowseView();
        if (tabName === 'ranked') renderRankedView();
        if (tabName === 'map') renderMapView();
    }

    function toggleVisited(cityId) {
        const activeIds = getActiveRankedIds();
        const index = activeIds.indexOf(cityId);

        if (index > -1) {
            activeIds.splice(index, 1);
            showToast("Removed from visited list", "fa-circle-xmark text-rose-400");
        } else {
            activeIds.push(cityId);
            showToast("Added to visited list!");
        }

        saveState();
        if (appState.currentTab === 'browse') renderBrowseView();
        if (appState.currentTab === 'ranked') renderRankedView();
        if (appState.currentTab === 'map') renderMapView();
    }

    function moveRank(currentIndex, direction) {
        const activeIds = getActiveRankedIds();
        const targetIndex = currentIndex + direction;

        if (targetIndex < 0 || targetIndex >= activeIds.length) return;

        const temp = activeIds[currentIndex];
        activeIds[currentIndex] = activeIds[targetIndex];
        activeIds[targetIndex] = temp;

        saveState();
        renderRankedView();
    }

    // Drag and Drop Logic
    function handleDragStart(e, index) {
        draggedIndex = index;
        e.currentTarget.classList.add('dragging');
        e.dataTransfer.effectAllowed = 'move';
    }

    function handleDragOver(e, index) {
        e.preventDefault();
        e.dataTransfer.dropEffect = 'move';
    }

    function handleDrop(e, targetIndex) {
        e.preventDefault();
        if (draggedIndex === null || draggedIndex === targetIndex) return;

        const activeIds = getActiveRankedIds();
        const draggedItem = activeIds.splice(draggedIndex, 1)[0];
        activeIds.splice(targetIndex, 0, draggedItem);

        saveState();
        renderRankedView();
    }

    function handleDragEnd(e) {
        e.currentTarget.classList.remove('dragging');
        draggedIndex = null;
    }

    function togglePopulationVisibility() {
        appState.showPopulation = document.getElementById('toggle-pop-visibility').checked;
        saveState();
        if (appState.currentTab === 'browse') renderBrowseView();
        if (appState.currentTab === 'ranked') renderRankedView();
        if (appState.currentTab === 'map') renderMapView();
    }

    function handleSearch() {
        const val = document.getElementById('search-input').value;
        document.getElementById('clear-search').classList.toggle('hidden', val.length === 0);
        renderBrowseView();
    }

    function clearSearch() {
        document.getElementById('search-input').value = '';
        document.getElementById('clear-search').classList.add('hidden');
        renderBrowseView();
    }

    function handleFilter() {
        renderBrowseView();
    }

    function clearAllVisited() {
        if (confirm("Are you sure you want to reset all visited cities for this profile?")) {
            appState.profiles[appState.activeProfile] = [];
            saveState();
            renderRankedView();
            showToast("Visited list cleared", "fa-trash-can text-rose-400");
        }
    }

    // Profile Management
    function updateProfileBadge() {
        document.getElementById('active-profile-badge').textContent = appState.activeProfile;
    }

    function openProfileModal() {
        const select = document.getElementById('profile-select');
        select.innerHTML = Object.keys(appState.profiles).map(p => `
            <option value="${p}" ${p === appState.activeProfile ? 'selected' : ''}>${p} (${appState.profiles[p].length} visited)</option>
        `).join('');
        document.getElementById('modal-profile').classList.remove('hidden');
    }

    function closeProfileModal() {
        document.getElementById('modal-profile').classList.add('hidden');
    }

    function switchProfile(profileName) {
        appState.activeProfile = profileName;
        updateProfileBadge();
        saveState();
        switchTab(appState.currentTab);
        showToast(`Switched to profile: ${profileName}`);
    }

    function createNewProfile() {
        const input = document.getElementById('new-profile-input');
        const name = input.value.trim();
        if (!name) return;

        if (appState.profiles[name]) {
            alert("A profile with this name already exists.");
            return;
        }

        appState.profiles[name] = [];
        appState.activeProfile = name;
        input.value = '';
        
        updateProfileBadge();
        saveState();
        openProfileModal();
        switchTab(appState.currentTab);
        showToast(`Created profile: ${name}`);
    }

    // Share & Email Exporting
    function openShareModal() {
        const activeIds = getActiveRankedIds();
        const textPreview = document.getElementById('share-text-preview');
        
        if (activeIds.length === 0) {
            textPreview.value = "No cities marked as visited yet!";
        } else {
            let output = `My Top Ranked Visited Cities (${appState.activeProfile}):\n\n`;
            activeIds.forEach((id, idx) => {
                const c = CITIES_DATA.find(x => x.id === id);
                if (c) {
                    const popStr = appState.showPopulation ? ` ${formatPop(c.pop)}` : '';
                    output += `${idx + 1}. ${c.name}${popStr} - ${c.state}, ${c.country}\n`;
                }
            });
            textPreview.value = output;
        }
        
        document.getElementById('modal-share').classList.remove('hidden');
    }

    function closeShareModal() {
        document.getElementById('modal-share').classList.add('hidden');
    }

    function copyShareText() {
        const text = document.getElementById('share-text-preview').value;
        navigator.clipboard.writeText(text);
        showToast("Rankings copied to clipboard!");
    }

    function sendEmailCopy() {
        const email = document.getElementById('share-email-input').value.trim();
        const text = document.getElementById('share-text-preview').value;
        const subject = encodeURIComponent(`My Top Ranked Cities - ${appState.activeProfile}`);
        const body = encodeURIComponent(text);
        
        window.location.href = `mailto:${email}?subject=${subject}&body=${body}`;
    }

    function exportList(type) {
        const activeIds = getActiveRankedIds();
        if (type === 'text') {
            let output = `My Top Cities (${appState.activeProfile}):\n`;
            activeIds.forEach((id, idx) => {
                const c = CITIES_DATA.find(x => x.id === id);
                if (c) output += `${idx + 1}. ${c.name}, ${c.state}, ${c.country}\n`;
            });
            navigator.clipboard.writeText(output);
            showToast("Copied text to clipboard!");
        } else if (type === 'json') {
            const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(appState, null, 2));
            const downloadAnchor = document.createElement('a');
            downloadAnchor.setAttribute("href", dataStr);
            downloadAnchor.setAttribute("download", `city_rankings_${appState.activeProfile.replace(/\s+/g, '_')}.json`);
            document.body.appendChild(downloadAnchor);
            downloadAnchor.click();
            downloadAnchor.remove();
        }
    }

    function importJSONState(event) {
        const file = event.target.files[0];
        if (!file) return;

        const reader = new FileReader();
        reader.onload = function(e) {
            try {
                const imported = JSON.parse(e.target.result);
                if (imported.profiles) {
                    appState = { ...appState, ...imported };
                    saveState();
                    closeProfileModal();
                    switchTab(appState.currentTab);
                    showToast("Profile data imported successfully!");
                } else {
                    alert("Invalid JSON format.");
                }
            } catch(err) {
                alert("Error reading JSON file.");
            }
        };
        reader.readAsText(file);
    }

    // App Initialization
    window.addEventListener('DOMContentLoaded', () => {
        loadState();
        updateCounters();
        renderBrowseView();
    });
</script>

</body>
</html>
