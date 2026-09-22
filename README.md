# my-city-rankings

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
    
   ```
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

```

```
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
            
            <!-- View Tabs (Browse, Ranked List, Interactive Map) -->
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
            <div id="load-more-trigger" class="py-4 text-center text-xs text-slate-400">
                Scroll down to load more cities...
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
<div id="toast" class="fixed bottom-5 right-5 bg-slate-900 text-white px-4 py-3 rounded-xl shadow-xl text-xs font-medium translate-y-20 opacity-0 transition-all duration-300 flex items-center gap-2.5 z-50">
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
        { name: "Amsterdam", state: "North Holland", country: "Netherlands", continent: "Europe", pop: 1150000, lat: 52.3676, lng: 4.9041 },
        { name: "Vienna", state: "Vienna", country: "Austria", continent: "Europe", pop: 1900000, lat: 48.2082, lng: 16.3738 },
        { name: "Prague", state: "Prague", country: "Czech Republic", continent: "Europe", pop: 1300000, lat: 50.0755, lng: 14.4378 },
        { name: "Lisbon", state: "Lisbon", country: "Portugal", continent: "Europe", pop: 2900000, lat: 38.7223, lng: -9.1393 },
        { name: "Porto", state: "Porto", country: "Portugal", continent: "Europe", pop: 1300000, lat: 41.1579, lng: -8.6291 },
        { name: "Athens", state: "Attica", country: "Greece", continent: "Europe", pop: 3150000, lat: 37.9838, lng: 23.7275 },
        { name: "Dublin", state: "Leinster", country: "Ireland", continent: "Europe", pop: 1250000, lat: 53.3498, lng: -6.2603 },
        { name: "Stockholm", state: "Stockholm", country: "Sweden", continent: "Europe", pop: 1600000, lat: 59.3293, lng: 18.0686 },
        { name: "Copenhagen", state: "Capital Region", country: "Denmark", continent: "Europe", pop: 1330000, lat: 55.6761, lng: 12.5683 },
        { name: "Oslo", state: "Oslo", country: "Norway", continent: "Europe", pop: 1060000, lat: 59.9139, lng: 10.7522 },
        { name: "Zurich", state: "Zurich", country: "Switzerland", continent: "Europe", pop: 1400000, lat: 47.3769, lng: 8.5417 },
        { name: "Geneva", state: "Geneva", country: "Switzerland", continent: "Europe", pop: 600000, lat: 46.2044, lng: 6.1432 },
        { name: "Istanbul", state: "Istanbul", country: "Turkey", continent: "Europe", pop: 15800000, lat: 41.0082, lng: 28.9784 },

        // South America
        { name: "São Paulo", state: "São Paulo", country: "Brazil", continent: "South America", pop: 22400000, lat: -23.5505, lng: -46.6333 },
        { name: "Rio de Janeiro", state: "Rio de Janeiro", country: "Brazil", continent: "South America", pop: 13600000, lat: -22.9068, lng: -43.1729 },
        { name: "Buenos Aires", state: "Buenos Aires", country: "Argentina", continent: "South America", pop: 15500000, lat: -34.6037, lng: -58.3816 },
        { name: "Bogotá", state: "Cundinamarca", country: "Colombia", continent: "South America", pop: 11400000, lat: 4.7110, lng: -74.0721 },
        { name: "Medellín", state: "Antioquia", country: "Colombia", continent: "South America", pop: 4000000, lat: 6.2442, lng: -75.5812 },
        { name: "Lima", state: "Lima", country: "Peru", continent: "South America", pop: 11200000, lat: -12.0463, lng: -77.0428 },
        { name: "Santiago", state: "Santiago Metropolitan", country: "Chile", continent: "South America", pop: 6800000, lat: -33.4489, lng: -70.6693 },

        // Africa
        { name: "Cairo", state: "Cairo", country: "Egypt", continent: "Africa", pop: 22100000, lat: 30.0444, lng: 31.2357 },
        { name: "Lagos", state: "Lagos", country: "Nigeria", continent: "Africa", pop: 15900000, lat: 6.5244, lng: 3.3792 },
        { name: "Johannesburg", state: "Gauteng", country: "South Africa", continent: "Africa", pop: 6100000, lat: -26.2041, lng: 28.0473 },
        { name: "Cape Town", state: "Western Cape", country: "South Africa", continent: "Africa", pop: 4800000, lat: -33.9249, lng: 18.4241 },
        { name: "Nairobi", state: "Nairobi", country: "Kenya", continent: "Africa", pop: 5100000, lat: -1.2921, lng: 36.8219 },
        { name: "Casablanca", state: "Casablanca-Settat", country: "Morocco", continent: "Africa", pop: 3800000, lat: 33.5731, lng: -7.5898 },

        // Oceania
        { name: "Sydney", state: "New South Wales", country: "Australia", continent: "Oceania", pop: 5300000, lat: -33.8688, lng: 151.2093 },
        { name: "Melbourne", state: "Victoria", country: "Australia", continent: "Oceania", pop: 5000000, lat: -37.8136, lng: 144.9631 },
        { name: "Brisbane", state: "Queensland", country: "Australia", continent: "Oceania", pop: 2600000, lat: -27.4705, lng: 153.0260 },
        { name: "Perth", state: "Western Australia", country: "Australia", continent: "Oceania", pop: 2100000, lat: -31.9505, lng: 115.8605 },
        { name: "Auckland", state: "Auckland", country: "New Zealand", continent: "Oceania", pop: 1650000, lat: -36.8485, lng: 174.7633 }
    ];

    // Unique Identifier Helper
    function getCityKey(city) {
        return `${city.name}|${city.state}|${city.country}`;
    }

    // Global State Management
    let appState = {
        activeProfile: 'Default Profile',
        profiles: {
            'Default Profile': {
                visitedKeys: [], // Keys of checked cities
                rankedKeys: []   // Explicit ordered array of visited city keys
            }
        },
        showPopulation: true
    };

    // UI State
    let currentTab = 'browse';
    let renderedBrowseCount = 30;
    let mapInstance = null;
    let mapMarkers = [];
    let draggedItemIndex = null;

    // Initialize App
    document.addEventListener('DOMContentLoaded', () => {
        loadStateFromLocalStorage();
        sortCitiesDataset();
        updateUIStats();
        renderBrowseList();
        setupInfiniteScroll();
    });

    // Ensure Dataset Sorting Strategy: Country (A-Z) -> State/Province (A-Z) -> Metro Pop (Desc)
    function sortCitiesDataset() {
        CITIES_DATA.sort((a, b) => {
            if (a.country < b.country) return -1;
            if (a.country > b.country) return 1;
            
            if (a.state < b.state) return -1;
            if (a.state > b.state) return 1;
            
            return b.pop - a.pop;
        });
    }

    // Local Storage Persistence
    function loadStateFromLocalStorage() {
        const saved = localStorage.getItem('global_city_ranker_data');
        if (saved) {
            try {
                const parsed = JSON.parse(saved);
                appState = { ...appState, ...parsed };
            } catch (e) {
                console.error("Failed to parse saved state", e);
            }
        }
        if (!appState.profiles[appState.activeProfile]) {
            appState.profiles[appState.activeProfile] = { visitedKeys: [], rankedKeys: [] };
        }
        document.getElementById('active-profile-badge').innerText = appState.activeProfile;
        document.getElementById('toggle-pop-visibility').checked = appState.showPopulation;
    }

    function saveStateToLocalStorage() {
        localStorage.setItem('global_city_ranker_data', JSON.stringify(appState));
    }

    // Getters for Current Profile Data
    function getCurrentProfile() {
        return appState.profiles[appState.activeProfile] || { visitedKeys: [], rankedKeys: [] };
    }

    function isVisited(city) {
        const key = getCityKey(city);
        return getCurrentProfile().visitedKeys.includes(key);
    }

    // Toggle Visited Status
    function toggleVisited(cityKey) {
        const profile = getCurrentProfile();
        const index = profile.visitedKeys.indexOf(cityKey);

        if (index > -1) {
            // Remove from visited and ranked
            profile.visitedKeys.splice(index, 1);
            const rankIdx = profile.rankedKeys.indexOf(cityKey);
            if (rankIdx > -1) profile.rankedKeys.splice(rankIdx, 1);
            showToast("City removed from visited list", "info");
        } else {
            // Add to visited and append to bottom of rankings
            profile.visitedKeys.push(cityKey);
            profile.rankedKeys.push(cityKey);
            showToast("City marked as visited!", "success");
        }

        saveStateToLocalStorage();
        updateUIStats();
        
        // Re-render based on current view
        if (currentTab === 'browse') renderBrowseList();
        else if (currentTab === 'ranked') renderRankedList();
        else if (currentTab === 'map') updateMapMarkers();
    }

    // Stats Updates
    function updateUIStats() {
        const profile = getCurrentProfile();
        const visitedCount = profile.visitedKeys.length;
        const totalCount = CITIES_DATA.length;

        document.getElementById('visited-count').innerText = visitedCount;
        document.getElementById('total-count').innerText = totalCount;
        document.getElementById('tab-visited-badge').innerText = visitedCount;
    }

    // Tab Switching Logic
    function switchTab(tab) {
        currentTab = tab;

        const browseTabBtn = document.getElementById('tab-browse');
        const rankedTabBtn = document.getElementById('tab-ranked');
        const mapTabBtn = document.getElementById('tab-map');

        const viewBrowse = document.getElementById('view-browse');
        const viewRanked = document.getElementById('view-ranked');
        const viewMap = document.getElementById('view-map');

        const browseFilters = document.getElementById('browse-filters');
        const browseSortingInfo = document.getElementById('browse-sorting-info');
        const rankedActions = document.getElementById('ranked-actions');
        const mapActions = document.getElementById('map-actions');

        // Reset Tab Styling
        [browseTabBtn, rankedTabBtn, mapTabBtn].forEach(btn => {
            btn.className = "flex-1 sm:flex-initial px-4 py-2 rounded-lg font-medium text-xs sm:text-sm transition-all flex items-center justify-center gap-2 text-slate-600 hover:text-slate-900";
        });

        // Hide All Views & Actions
        viewBrowse.classList.add('hidden');
        viewRanked.classList.add('hidden');
        viewMap.classList.add('hidden');
        browseFilters.classList.add('hidden');
        browseSortingInfo.classList.add('hidden');
        rankedActions.classList.add('hidden');
        mapActions.classList.add('hidden');

        if (tab === 'browse') {
            browseTabBtn.className = "flex-1 sm:flex-initial px-4 py-2 rounded-lg font-medium text-xs sm:text-sm transition-all flex items-center justify-center gap-2 bg-white text-indigo-600 shadow-sm";
            viewBrowse.classList.remove('hidden');
            browseFilters.classList.remove('hidden');
            browseSortingInfo.classList.remove('hidden');
            renderBrowseList();
        } else if (tab === 'ranked') {
            rankedTabBtn.className = "flex-1 sm:flex-initial px-4 py-2 rounded-lg font-medium text-xs sm:text-sm transition-all flex items-center justify-center gap-2 bg-white text-indigo-600 shadow-sm";
            viewRanked.classList.remove('hidden');
            rankedActions.classList.remove('hidden');
            renderRankedList();
        } else if (tab === 'map') {
            mapTabBtn.className = "flex-1 sm:flex-initial px-4 py-2 rounded-lg font-medium text-xs sm:text-sm transition-all flex items-center justify-center gap-2 bg-white text-indigo-600 shadow-sm";
            viewMap.classList.remove('hidden');
            mapActions.classList.remove('hidden');
            initOrUpdateMap();
        }
    }

    // Filter and Search Logic
    function getFilteredCities() {
        const query = document.getElementById('search-input').value.toLowerCase().trim();
        const region = document.getElementById('region-filter').value;
        const status = document.getElementById('status-filter').value;

        return CITIES_DATA.filter(city => {
            const key = getCityKey(city);
            const visited = isVisited(city);

            // Text search match across City, State/Province, and Country
            const matchQuery = !query || 
                city.name.toLowerCase().includes(query) || 
                city.state.toLowerCase().includes(query) || 
                city.country.toLowerCase().includes(query);

            // Region filter match
            const matchRegion = region === 'All' || city.continent === region;

            // Status filter match
            let matchStatus = true;
            if (status === 'visited') matchStatus = visited;
            if (status === 'unvisited') matchStatus = !visited;

            return matchQuery && matchRegion && matchStatus;
        });
    }

    function handleSearch() {
        const query = document.getElementById('search-input').value;
        const clearBtn = document.getElementById('clear-search');
        if (query.length > 0) clearBtn.classList.remove('hidden');
        else clearBtn.classList.add('hidden');

        renderedBrowseCount = 30;
        renderBrowseList();
    }

    function clearSearch() {
        document.getElementById('search-input').value = '';
        document.getElementById('clear-search').classList.add('hidden');
        renderedBrowseCount = 30;
        renderBrowseList();
    }

    function handleFilter() {
        renderedBrowseCount = 30;
        renderBrowseList();
    }

    // Toggle Population Column/Badge Visibility
    function togglePopulationVisibility() {
        appState.showPopulation = document.getElementById('toggle-pop-visibility').checked;
        saveStateToLocalStorage();
        renderBrowseList();
        renderRankedList();
    }

    // Render Browse List with Lazy Loading
    function renderBrowseList() {
        const container = document.getElementById('city-list-container');
        const filtered = getFilteredCities();

        document.getElementById('results-count').innerText = `Showing ${Math.min(renderedBrowseCount, filtered.length)} of ${filtered.length} cities`;

        if (filtered.length === 0) {
            container.innerHTML = `
                <div class="py-12 text-center text-slate-400">
                    <i class="fa-solid fa-magnifying-glass-location text-3xl mb-2"></i>
                    <p class="text-sm font-medium">No cities match your current search and filters.</p>
                </div>
            `;
            document.getElementById('load-more-trigger').classList.add('hidden');
            return;
        }

        const visibleCities = filtered.slice(0, renderedBrowseCount);
        let html = '';

        visibleCities.forEach((city) => {
            const key = getCityKey(city);
            const visited = isVisited(city);
            const formattedPop = (city.pop / 1000000).toFixed(2) + 'M';

            html += `
                <div class="p-3 bg-white rounded-xl border ${visited ? 'border-indigo-200 bg-indigo-50/30' : 'border-slate-200'} hover:border-indigo-300 transition-all flex items-center justify-between gap-3 shadow-sm">
                    <div class="flex items-center gap-3 min-w-0">
                        <button onclick="toggleVisited('${key}')" class="w-6 h-6 rounded-lg flex items-center justify-center border transition-all ${visited ? 'bg-indigo-600 border-indigo-600 text-white' : 'bg-slate-100 border-slate-300 text-transparent hover:border-slate-400'}">
                            <i class="fa-solid fa-check text-xs"></i>
                        </button>

                        <div class="min-w-0">
                            <div class="flex items-center gap-2">
                                <h4 class="font-bold text-slate-800 text-sm truncate">${city.name}</h4>
                                <span class="text-[10px] bg-slate-100 text-slate-600 px-2 py-0.5 rounded-md font-medium border border-slate-200 flex-none">${city.country}</span>
                            </div>
                            <p class="text-xs text-slate-500 truncate">${city.state} &bull; <span class="text-slate-400">${city.continent}</span></p>
                        </div>
                    </div>

                    <div class="flex items-center gap-3 flex-none">
                        ${appState.showPopulation ? `
                            <div class="text-right hidden sm:block">
                                <span class="text-[10px] uppercase font-bold text-slate-400 block leading-tight">Metro Pop</span>
                                <span class="text-xs font-semibold text-slate-700">${formattedPop}</span>
                            </div>
                        ` : ''}

                        <button onclick="toggleVisited('${key}')" class="px-3 py-1.5 rounded-lg text-xs font-semibold transition-all ${visited ? 'bg-indigo-100 text-indigo-700 hover:bg-indigo-200' : 'bg-slate-100 text-slate-700 hover:bg-slate-200'}">
                            ${visited ? '<i class="fa-solid fa-check text-indigo-600 mr-1"></i> Visited' : '+ Mark Visited'}
                        </button>
                    </div>
                </div>
            `;
        });

        container.innerHTML = html;

        const trigger = document.getElementById('load-more-trigger');
        if (renderedBrowseCount < filtered.length) {
            trigger.classList.remove('hidden');
        } else {
            trigger.classList.add('hidden');
        }
    }

    // Infinite Scroll Setup
    function setupInfiniteScroll() {
        const browseView = document.getElementById('view-browse');
        browseView.addEventListener('scroll', () => {
            if (currentTab !== 'browse') return;
            const { scrollTop, scrollHeight, clientHeight } = browseView;
            if (scrollTop + clientHeight >= scrollHeight - 120) {
                const filtered = getFilteredCities();
                if (renderedBrowseCount < filtered.length) {
                    renderedBrowseCount += 25;
                    renderBrowseList();
                }
            }
        });
    }

    // Render Visited & Drag-and-Drop Ranked List
    function renderRankedList() {
        const container = document.getElementById('ranked-list-container');
        const emptyState = document.getElementById('ranked-empty-state');
        const profile = getCurrentProfile();

        // Filter out any stale keys
        const validRankedKeys = profile.rankedKeys.filter(key => profile.visitedKeys.includes(key));
        profile.rankedKeys = validRankedKeys;

        if (validRankedKeys.length === 0) {
            container.innerHTML = '';
            emptyState.classList.remove('hidden');
            document.getElementById('ranked-instructions').classList.add('hidden');
            return;
        }

        emptyState.classList.add('hidden');
        document.getElementById('ranked-instructions').classList.remove('hidden');

        let html = '';

        validRankedKeys.forEach((key, index) => {
            const city = CITIES_DATA.find(c => getCityKey(c) === key);
            if (!city) return;

            const rank = index + 1;
            const formattedPop = (city.pop / 1000000).toFixed(2) + 'M';

            // Trophy Badge styling for top 3
            let rankBadge = `<span class="w-7 h-7 rounded-lg bg-slate-100 text-slate-700 font-bold text-xs flex items-center justify-center flex-none">#${rank}</span>`;
            if (rank === 1) {
                rankBadge = `<span class="w-7 h-7 rounded-lg bg-gradient-to-tr from-amber-400 to-amber-500 text-white font-bold text-xs flex items-center justify-center shadow-md flex-none"><i class="fa-solid fa-trophy"></i></span>`;
            } else if (rank === 2) {
                rankBadge = `<span class="w-7 h-7 rounded-lg bg-gradient-to-tr from-slate-300 to-slate-400 text-white font-bold text-xs flex items-center justify-center shadow-sm flex-none"><i class="fa-solid fa-trophy"></i></span>`;
            } else if (rank === 3) {
                rankBadge = `<span class="w-7 h-7 rounded-lg bg-gradient-to-tr from-amber-700 to-amber-800 text-white font-bold text-xs flex items-center justify-center shadow-sm flex-none"><i class="fa-solid fa-trophy"></i></span>`;
            }

            html += `
                <div draggable="true" 
                     ondragstart="handleDragStart(event, ${index})" 
                     ondragover="handleDragOver(event)" 
                     ondragleave="handleDragLeave(event)"
                     ondrop="handleDrop(event, ${index})" 
                     ondragend="handleDragEnd(event)"
                     class="p-3 bg-white rounded-xl border border-slate-200 hover:border-indigo-300 transition-all flex items-center justify-between gap-2 shadow-sm cursor-grab active:cursor-grabbing">
                    
                    <div class="flex items-center gap-2 sm:gap-3 min-w-0">
                        <!-- Drag Handle -->
                        <div class="text-slate-300 hover:text-slate-500 cursor-grab px-1">
                            <i class="fa-solid fa-grip-vertical"></i>
                        </div>

                        ${rankBadge}

                        <div class="min-w-0">
                            <div class="flex items-center gap-2">
                                <h4 class="font-bold text-slate-800 text-sm truncate">${city.name}</h4>
                                <span class="text-[10px] bg-slate-100 text-slate-600 px-2 py-0.5 rounded-md font-medium border border-slate-200 flex-none">${city.country}</span>
                            </div>
                            <p class="text-xs text-slate-500 truncate">${city.state} &bull; <span class="text-slate-400">${city.continent}</span></p>
                        </div>
                    </div>

                    <div class="flex items-center gap-2 flex-none">
                        ${appState.showPopulation ? `
                            <div class="text-right hidden md:block mr-2">
                                <span class="text-[10px] uppercase font-bold text-slate-400 block leading-tight">Metro Pop</span>
                                <span class="text-xs font-semibold text-slate-700">${formattedPop}</span>
                            </div>
                        ` : ''}

                        <!-- Up / Down Reorder Buttons -->
                        <div class="flex items-center bg-slate-100 rounded-lg p-0.5 border border-slate-200">
                            <button onclick="moveRank(${index}, -1)" ${index === 0 ? 'disabled class="opacity-30 p-1 text-slate-400"' : 'class="p-1 text-slate-600 hover:text-indigo-600"'}>
                                <i class="fa-solid fa-chevron-up text-xs"></i>
                            </button>
                            <button onclick="moveRank(${index}, 1)" ${index === validRankedKeys.length - 1 ? 'disabled class="opacity-30 p-1 text-slate-400"' : 'class="p-1 text-slate-600 hover:text-indigo-600"'}>
                                <i class="fa-solid fa-chevron-down text-xs"></i>
                            </button>
                        </div>

                        <!-- Remove from Visited -->
                        <button onclick="toggleVisited('${key}')" class="p-1.5 text-slate-400 hover:text-rose-600 rounded-lg transition-colors" title="Remove from list">
                            <i class="fa-solid fa-xmark text-sm"></i>
                        </button>
                    </div>
                </div>
            `;
        });

        container.innerHTML = html;
    }

    // Reordering Logic
    function moveRank(index, direction) {
        const profile = getCurrentProfile();
        const targetIndex = index + direction;

        if (targetIndex < 0 || targetIndex >= profile.rankedKeys.length) return;

        // Swap positions
        const temp = profile.rankedKeys[index];
        profile.rankedKeys[index] = profile.rankedKeys[targetIndex];
        profile.rankedKeys[targetIndex] = temp;

        saveStateToLocalStorage();
        renderRankedList();
    }

    // Drag-and-Drop Event Handlers
    function handleDragStart(e, index) {
        draggedItemIndex = index;
        e.currentTarget.classList.add('dragging');
        e.dataTransfer.effectAllowed = 'move';
    }

    function handleDragOver(e) {
        e.preventDefault();
        e.dataTransfer.dropEffect = 'move';
        e.currentTarget.classList.add('drag-over');
    }

    function handleDragLeave(e) {
        e.currentTarget.classList.remove('drag-over');
    }

    function handleDrop(e, dropIndex) {
        e.preventDefault();
        e.currentTarget.classList.remove('drag-over');

        if (draggedItemIndex === null || draggedItemIndex === dropIndex) return;

        const profile = getCurrentProfile();
        const draggedKey = profile.rankedKeys[draggedItemIndex];

        profile.rankedKeys.splice(draggedItemIndex, 1);
        profile.rankedKeys.splice(dropIndex, 0, draggedKey);

        saveStateToLocalStorage();
        renderRankedList();
    }

    function handleDragEnd(e) {
        e.currentTarget.classList.remove('dragging');
        draggedItemIndex = null;
    }

    // Interactive Leaflet Map Initialization
    function initOrUpdateMap() {
        if (!mapInstance) {
            mapInstance = L.map('map-container', {
                center: [20, 0],
                zoom: 2,
                zoomControl: false
            });

            // Add zoom control to top-right
            L.control.zoom({ position: 'topright' }).addTo(mapInstance);

            // Tile Layer (OpenStreetMap CartoDB Positron / Clean)
            L.tileLayer('https://{s}[.basemaps.cartocdn.com/rastertiles/voyager/](https://.basemaps.cartocdn.com/rastertiles/voyager/){z}/{x}/{y}{r}.png', {
                attribution: '&copy; <a href="[https://www.openstreetmap.org/copyright](https://www.openstreetmap.org/copyright)">OpenStreetMap</a> contributors &copy; <a href="[https://carto.com/attributions](https://carto.com/attributions)">CARTO</a>',
                subdomains: 'abcd',
                maxZoom: 19
            }).addTo(mapInstance);
        }

        // Invalidate size in case tab switching caused container miscalculation
        setTimeout(() => {
            mapInstance.invalidateSize();
            updateMapMarkers();
        }, 100);
    }

    function updateMapMarkers() {
        if (!mapInstance) return;

        // Clear existing markers
        mapMarkers.forEach(marker => mapInstance.removeLayer(marker));
        mapMarkers = [];

        const profile = getCurrentProfile();
        const rankedKeys = profile.rankedKeys;

        rankedKeys.forEach((key, index) => {
            const city = CITIES_DATA.find(c => getCityKey(c) === key);
            if (!city) return;

            const rank = index + 1;
            let iconHtml = '';
            let className = '';
            let size = [28, 28];

            if (rank === 1) {
                className = 'trophy-marker trophy-gold';
                iconHtml = '<i class="fa-solid fa-trophy text-xs"></i>';
                size = [34, 34];
            } else if (rank === 2) {
                className = 'trophy-marker trophy-silver';
                iconHtml = '<i class="fa-solid fa-trophy text-xs"></i>';
                size = [32, 32];
            } else if (rank === 3) {
                className = 'trophy-marker trophy-bronze';
                iconHtml = '<i class="fa-solid fa-trophy text-xs"></i>';
                size = [30, 30];
            } else {
                className = 'standard-pin';
                iconHtml = `${rank}`;
            }

            const customIcon = L.divIcon({
                className: className,
                html: iconHtml,
                iconSize: size,
                iconAnchor: [size[0]/2, size[1]/2]
            });

            const formattedPop = (city.pop / 1000000).toFixed(2) + 'M';

            const popupContent = `
                <div class="p-2 text-center min-w-[160px]">
                    <span class="inline-block px-2 py-0.5 rounded-full text-[10px] font-bold ${rank <= 3 ? 'bg-amber-100 text-amber-800' : 'bg-indigo-100 text-indigo-700'} mb-1">
                        ${rank <= 3 ? `Rank #${rank} Trophy` : `Rank #${rank}`}
                    </span>
                    <h4 class="font-bold text-slate-800 text-sm leading-tight">${city.name}</h4>
                    <p class="text-xs text-slate-500 mb-1">${city.state}, ${city.country}</p>
                    ${appState.showPopulation ? `<p class="text-[11px] font-semibold text-slate-600 bg-slate-100 rounded py-0.5">Metro Pop: ${formattedPop}</p>` : ''}
                </div>
            `;

            const marker = L.marker([city.lat, city.lng], { icon: customIcon })
                .bindPopup(popupContent)
                .addTo(mapInstance);

            mapMarkers.push(marker);
        });
    }

    function fitMapToBounds() {
        if (!mapInstance || mapMarkers.length === 0) {
            showToast("No visited markers on map to focus", "info");
            return;
        }

        const group = new L.featureGroup(mapMarkers);
        mapInstance.fitBounds(group.getBounds().pad(0.15));
    }

    // Export and Reset Functions
    function exportList(format) {
        const profile = getCurrentProfile();
        if (profile.rankedKeys.length === 0) {
            showToast("No visited cities to export!", "info");
            return;
        }

        const rankedCities = profile.rankedKeys.map((key, i) => {
            const city = CITIES_DATA.find(c => getCityKey(c) === key);
            return {
                rank: i + 1,
                name: city ? city.name : '',
                state: city ? city.state : '',
                country: city ? city.country : '',
                continent: city ? city.continent : '',
                metro_pop: city ? city.pop : 0
            };
        });

        if (format === 'text') {
            let text = `My Top Ranked Cities (${appState.activeProfile}):\n\n`;
            rankedCities.forEach(c => {
                text += `${c.rank}. ${c.name}, ${c.state}, ${c.country}\n`;
            });
            navigator.clipboard.writeText(text);
            showToast("Ranked list copied to clipboard!", "success");
        } else if (format === 'json') {
            const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(rankedCities, null, 2));
            const downloadAnchor = document.createElement('a');
            downloadAnchor.setAttribute("href", dataStr);
            downloadAnchor.setAttribute("download", `city_rankings_${appState.activeProfile.toLowerCase().replace(/\s+/g, '_')}.json`);
            document.body.appendChild(downloadAnchor);
            downloadAnchor.click();
            downloadAnchor.remove();
            showToast("JSON file downloaded!", "success");
        }
    }

    function clearAllVisited() {
        if (confirm(`Are you sure you want to clear all visited cities for profile "${appState.activeProfile}"?`)) {
            const profile = getCurrentProfile();
            profile.visitedKeys = [];
            profile.rankedKeys = [];
            saveStateToLocalStorage();
            updateUIStats();
            renderRankedList();
            showToast("Profile rankings cleared", "info");
        }
    }

    // Profile Modal Logic
    function openProfileModal() {
        const modal = document.getElementById('modal-profile');
        const select = document.getElementById('profile-select');

        select.innerHTML = '';
        Object.keys(appState.profiles).forEach(pName => {
            const opt = document.createElement('option');
            opt.value = pName;
            opt.innerText = pName;
            if (pName === appState.activeProfile) opt.selected = true;
            select.appendChild(opt);
        });

        modal.classList.remove('hidden');
    }

    function closeProfileModal() {
        document.getElementById('modal-profile').classList.add('hidden');
    }

    function switchProfile(profileName) {
        if (!appState.profiles[profileName]) return;
        appState.activeProfile = profileName;
        document.getElementById('active-profile-badge').innerText = profileName;
        saveStateToLocalStorage();
        updateUIStats();
        
        if (currentTab === 'browse') renderBrowseList();
        else if (currentTab === 'ranked') renderRankedList();
        else if (currentTab === 'map') updateMapMarkers();

        showToast(`Switched to profile: ${profileName}`, "success");
    }

    function createNewProfile() {
        const input = document.getElementById('new-profile-input');
        const name = input.value.trim();

        if (!name) return;
        if (appState.profiles[name]) {
            showToast("Profile name already exists", "info");
            return;
        }

        appState.profiles[name] = { visitedKeys: [], rankedKeys: [] };
        input.value = '';
        switchProfile(name);
        openProfileModal();
    }

    function importJSONState(event) {
        const file = event.target.files[0];
        if (!file) return;

        const reader = new FileReader();
        reader.onload = function(e) {
            try {
                const imported = JSON.parse(e.target.result);
                if (Array.isArray(imported)) {
                    // Assuming imported array of ranked cities
                    const profile = getCurrentProfile();
                    imported.forEach(item => {
                        const match = CITIES_DATA.find(c => c.name.toLowerCase() === item.name.toLowerCase() && c.country.toLowerCase() === item.country.toLowerCase());
                        if (match) {
                            const key = getCityKey(match);
                            if (!profile.visitedKeys.includes(key)) profile.visitedKeys.push(key);
                            if (!profile.rankedKeys.includes(key)) profile.rankedKeys.push(key);
                        }
                    });
                    saveStateToLocalStorage();
                    updateUIStats();
                    showToast("Imported rankings into active profile!", "success");
                    closeProfileModal();
                }
            } catch (err) {
                showToast("Invalid JSON file structure", "info");
            }
        };
        reader.readAsText(file);
    }

    // Share & Email Modal Logic
    function openShareModal() {
        const modal = document.getElementById('modal-share');
        const preview = document.getElementById('share-text-preview');
        const profile = getCurrentProfile();

        let text = `Global City Ranker Profile: ${appState.activeProfile}\nVisited: ${profile.visitedKeys.length} / ${CITIES_DATA.length} cities\n\nTop Visited Cities:\n`;

        profile.rankedKeys.forEach((key, index) => {
            const city = CITIES_DATA.find(c => getCityKey(c) === key);
            if (city) {
                text += `${index + 1}. ${city.name}, ${city.country}\n`;
            }
        });

        preview.value = text;
        modal.classList.remove('hidden');
    }

    function closeShareModal() {
        document.getElementById('modal-share').classList.add('hidden');
    }

    function copyShareText() {
        const preview = document.getElementById('share-text-preview');
        navigator.clipboard.writeText(preview.value);
        showToast("Share text copied to clipboard!", "success");
    }

    function sendEmailCopy() {
        const email = document.getElementById('share-email-input').value.trim();
        const body = encodeURIComponent(document.getElementById('share-text-preview').value);
        const subject = encodeURIComponent(`My Global City Rankings - ${appState.activeProfile}`);

        window.location.href = `mailto:${email}?subject=${subject}&body=${body}`;
    }

    // Toast Notification System
    function showToast(message, type = "success") {
        const toast = document.getElementById('toast');
        const msgSpan = document.getElementById('toast-message');
        const icon = document.getElementById('toast-icon');

        msgSpan.innerText = message;

        if (type === "success") {
            icon.className = "fa-solid fa-circle-check text-emerald-400 text-sm";
        } else if (type === "info") {
            icon.className = "fa-solid fa-circle-info text-indigo-400 text-sm";
        }

        toast.classList.remove('translate-y-20', 'opacity-0');

        setTimeout(() => {
            toast.classList.add('translate-y-20', 'opacity-0');
        }, 3000);
    }
</script>
