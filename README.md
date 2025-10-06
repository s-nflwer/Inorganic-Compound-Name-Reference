<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Inorganic Compound Namer - Card Game Reference (Ionic & Covalent)</title>
    <!-- PWA Manifest -->
    <link rel="manifest" href="manifest.json">
    <!-- Theme color for PWA (optional) -->
    <meta name="theme-color" content="#007bff">
    <!-- Apple/iOS PWA support -->
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="default">
    <link rel="apple-touch-icon" href="icon-192.png"> <!-- Add an icon image if desired -->
    <style>
        body {
            font-family: Arial, sans-serif;
            max-width: 800px;
            margin: 50px auto;
            padding: 20px;
            background-color: #f4f4f4;
        }
        h1 {
            text-align: center;
            color: #333;
        }
        input[type="text"] {
            width: 100%;
            padding: 10px;
            font-size: 16px;
            border: 1px solid #ccc;
            border-radius: 4px;
            box-sizing: border-box;
        }
        button {
            width: 100%;
            padding: 10px;
            background-color: #007bff;
            color: white;
            border: none;
            border-radius: 4px;
            font-size: 16px;
            cursor: pointer;
            margin-top: 10px;
        }
        button:hover {
            background-color: #0056b3;
        }
        #result {
            margin-top: 20px;
            padding: 15px;
            background-color: white;
            border-radius: 4px;
            border-left: 4px solid #007bff;
            min-height: 20px;
        }
        .error {
            color: #dc3545;
            border-left-color: #dc3545;
        }
        .examples {
            margin-top: 20px;
            font-size: 14px;
            color: #666;
        }
        #allCompounds {
            margin-top: 30px;
            padding: 15px;
            background-color: white;
            border-radius: 4px;
            display: none;
        }
        #allCompounds h2 {
            color: #333;
        }
        #allCompounds ul {
            column-count: 2;
            list-style-type: none;
            padding: 0;
        }
        #allCompounds li {
            margin-bottom: 5px;
            font-size: 14px;
        }
        .toggle-btn {
            width: 100%;
            padding: 10px;
            background-color: #28a745;
            color: white;
            border: none;
            border-radius: 4px;
            font-size: 16px;
            cursor: pointer;
            margin-top: 10px;
        }
        .toggle-btn:hover {
            background-color: #218838;
        }
        .covalent-note {
            background-color: #fff3cd;
            border: 1px solid #ffeaa7;
            padding: 10px;
            border-radius: 4px;
            margin-top: 10px;
        }
        /* PWA Install Prompt Styling (optional) */
        #install-banner {
            position: fixed;
            bottom: 0;
            left: 0;
            right: 0;
            background: #007bff;
            color: white;
            padding: 10px;
            text-align: center;
            display: none;
            z-index: 1000;
        }
    </style>
</head>
<body>
    <h1>Inorganic Compound Namer - Card Game Reference (Ionic & Covalent)</h1>
    <p>Enter a chemical formula (e.g., NaCl, H2SO4, NH42SO4 for (NH4)2SO4, CCl4) to get its name. This covers <strong>all possible valid ionic compounds and common inorganic covalent compounds</strong> formable from the card game's elements and ions (considering valences for neutrality). Formulas are generated from: Na, K, Mg, Ca, Al, Zn, Cu(I), Cu(II), Fe(II), Fe(III), O, H, Cl, C (for CO₃²⁻ or covalent like CO2/CCl4), N (for NO₃⁻ or covalent like NH3/NO2), F, S, P (for PO₄³⁻ or covalent like SO2/PCl3), OH⁻, NH₄⁺, PO₄³⁻, SO₄²⁻.</p>
    <p><strong>Notes:</strong> Enter polyatomic multiples without parentheses (e.g., NH42SO4). Includes ~150 salts, oxides, acids, and covalent molecules (e.g., CCl4, CO2, SO2). Transition metals use Roman numerals. For reference, use the "Show All Compounds" button below. Covalent compounds are neutral molecules from non-metals (no charges). </p>
    
<input type="text" id="formulaInput" placeholder="Enter formula, e.g., NaCl, CCl4, or NH42SO4" maxlength="50">
    <button onclick="nameCompound()">Name Compound</button>
    
 <div id="result"></div>
    
<button class="toggle-btn" onclick="toggleAllCompounds()">Show All Compounds (Reference List)</button>
    <div id="allCompounds">
        <h2>All Possible Compounds</h2>
        <p><strong>Ionic:</strong> Salts/acids from metals + anions. <strong>Covalent:</strong> Neutral non-metal molecules (e.g., CCl4 from C + 4Cl).</p>
        <ul id="compoundsList"></ul>
    </div>
    
<div class="examples">
        <strong>Examples:</strong><br>
        NaCl → Sodium chloride (ionic)<br>
        H2SO4 → Sulfuric acid (ionic acid)<br>
        NH42SO4 → Ammonium sulfate (standard: (NH₄)₂SO₄, ionic)<br>
        AlPO4 → Aluminum phosphate (ionic)<br>
        Cu2O → Copper(I) oxide (ionic)<br>
        FeCl3 → Iron(III) chloride (ionic)<br>
        CaCO3 → Calcium carbonate (ionic)<br>
        CCl4 → Carbon tetrachloride (covalent)<br>
        CO2 → Carbon dioxide (covalent)<br>
        SO2 → Sulfur dioxide (covalent)<br>
        PCl3 → Phosphorus trichloride (covalent)
    </div>

    <!-- PWA Install Banner (optional, shows prompt to install) -->
    <div id="install-banner">
        <p>Install this app for offline use? <button onclick="installPWA()">Install</button> <button onclick="dismissInstall()">Dismiss</button></p>
    </div>

<script>
        // Service Worker Registration (for offline caching)
        if ('serviceWorker' in navigator) {
            window.addEventListener('load', () => {
                navigator.serviceWorker.register('sw.js')
                    .then(reg => console.log('SW registered!'))
                    .catch(err => console.log('SW registration failed'));
            });
        }

        // PWA Install Prompt (optional)
        let deferredPrompt;
        window.addEventListener('beforeinstallprompt', (e) => {
            e.preventDefault();
            deferredPrompt = e;
            document.getElementById('install-banner').style.display = 'block';
        });

        function installPWA() {
            deferredPrompt.prompt();
            deferredPrompt.userChoice.then(() => deferredPrompt = null);
            document.getElementById('install-banner').style.display = 'none';
        }

        function dismissInstall() {
            document.getElementById('install-banner').style.display = 'none';
        }

        // Original app script (unchanged)
        // Cations (including H for acids)
        const cations = [
            {symbol: 'H', charge: 1, baseName: 'hydrogen', roman: '', isAcid: true},
            {symbol: 'Na', charge: 1, baseName: 'sodium', roman: ''},
            {symbol: 'K', charge: 1, baseName: 'potassium', roman: ''},
            {symbol: 'NH4', charge: 1, baseName: 'ammonium', roman: ''},
            {symbol: 'Cu', charge: 1, baseName: 'copper', roman: '(I)'},
            {symbol: 'Mg', charge: 2, baseName: 'magnesium', roman: ''},
            {symbol: 'Ca', charge: 2, baseName: 'calcium', roman: ''},
            {symbol: 'Zn', charge: 2, baseName: 'zinc', roman: ''},
            {symbol: 'Fe', charge: 2, baseName: 'iron', roman: '(II)'},
            {symbol: 'Cu', charge: 2, baseName: 'copper', roman: '(II)'},
            {symbol: 'Al', charge: 3, baseName: 'aluminum', roman: ''},
            {symbol: 'Fe', charge: 3, baseName: 'iron', roman: '(III)'}
        ];

        // Anions
        const anions = [
            {symbol: 'F', charge: -1, name: 'fluoride'},
            {symbol: 'Cl', charge: -1, name: 'chloride'},
            {symbol: 'OH', charge: -1, name: 'hydroxide'},
            {symbol: 'NO3', charge: -1, name: 'nitrate'},
            {symbol: 'O', charge: -2, name: 'oxide'},
            {symbol: 'S', charge: -2, name: 'sulfide'},
            {symbol: 'CO3', charge: -2, name: 'carbonate'},
            {symbol: 'SO4', charge: -2, name: 'sulfate'},
            {symbol: 'PO4', charge: -3, name: 'phosphate'}
        ];

        // GCD function for balancing
        function gcd(a, b) {
            return b === 0 ? a : gcd(b, a % b);
        }

        // Generate standard formula with parentheses where needed
        function generateFormula(cat, an) {
            const catCharge = Math.abs(cat.charge);
            const anCharge = Math.abs(an.charge);
            const g = gcd(catCharge, anCharge);
            const numCat = anCharge / g;
            const numAn = catCharge / g;

            let formula = '';

            // Cation part
            let catPart = cat.symbol;
            if (numCat > 1) {
                if (cat.symbol.match(/^[A-Z][a-z]?\d+$/)) { // Polyatomic like NH4
                    catPart = `(${cat.symbol})${numCat}`;
                } else {
                    catPart = cat.symbol + numCat;
                }
            }
            formula += catPart;

            // Anion part
            let anPart = an.symbol;
            if (numAn > 1) {
                if (an.symbol.match(/^[A-Z][a-z]?\d+$/)) { // Polyatomic like SO4, PO4
                    anPart = `(${an.symbol})${numAn}`;
                } else {
                    anPart = an.symbol + numAn;
                }
            }
            formula += anPart;

            return formula;
        }

        // Get acid name
        function getAcidName(an) {
            switch (an.symbol) {
                case 'O': return 'Water';
                case 'OH': return 'Water';
                case 'S': return 'Hydrogen sulfide';
                case 'F': return 'Hydrofluoric acid';
                case 'Cl': return 'Hydrochloric acid';
                case 'NO3': return 'Nitric acid';
                case 'CO3': return 'Carbonic acid';
                case 'SO4': return 'Sulfuric acid';
                case 'PO4': return 'Phosphoric acid';
                default: return `Hydrogen ${an.name}`;
            }
        }

        // Predefined covalent inorganic compounds (neutral molecules from non-metals in deck: C, O, H, Cl, N, F, S, P)
        // Generated based on common valences: C(4), O(2), H(1), Cl/F(1), N(3/5), S(2/4/6), P(3/5)
        // Only simple, stable inorganics (no organics like CH4; focus on halides, oxides, etc.)
        const covalentCompounds = {
            // Diatomics/Elements (neutral)
            'Cl2': 'Chlorine',
            'F2': 'Fluorine',
            'O2': 'Oxygen',
            'N2': 'Nitrogen',
            'H2': 'Hydrogen',
            'P4': 'White phosphorus (P₄)', // Tetramer, but common
            'S8': 'Sulfur (S₈)', // Octamer, but common

            // Hydrogen compounds
            'H2O': 'Water',
            'HCl': 'Hydrogen chloride',
            'HF': 'Hydrogen fluoride',
            'H2S': 'Hydrogen sulfide',
            'NH3': 'Ammonia',
            'PH3': 'Phosphine',

            // Carbon compounds (inorganic: oxides, halides)
            'CO': 'Carbon monoxide',
            'CO2': 'Carbon dioxide',
            'CCl4': 'Carbon tetrachloride',
            'CF4': 'Carbon tetrafluoride',
            'CS2': 'Carbon disulfide', // S from deck

            // Nitrogen compounds
            'NO': 'Nitrogen monoxide',
            'N2O': 'Dinitrogen monoxide (Nitrous oxide)',
            'NO2': 'Nitrogen dioxide',
            'N2O4': 'Dinitrogen tetroxide',
            'NF3': 'Nitrogen trifluoride',
            'NCl3': 'Nitrogen trichloride',

            // Phosphorus compounds
            'PCl3': 'Phosphorus trichloride',
            'PCl5': 'Phosphorus pentachloride',
            'PF3': 'Phosphorus trifluoride',
            'PF5': 'Phosphorus pentafluoride',
            'POCl3': 'Phosphoryl chloride', // P + O + 3Cl

            // Sulfur compounds
            'SO2': 'Sulfur dioxide',
            'SO3': 'Sulfur trioxide',
            'SF4': 'Sulfur tetrafluoride',
            'SF6': 'Sulfur hexafluoride', // Uses 6F, but deck has 5; still include as possible
            'SCl2': 'Sulfur dichloride',

            // Mixed/Others
            'ClF': 'Chlorine monofluoride',
            'ClF3': 'Chlorine trifluoride',
            'N2H4': 'Dihydrazine (Hydrazine)', // N + H, inorganic
            'HNO2': 'Nitrous acid', // But covalent form
            'HNO3': 'Nitric acid' // Already in ionic, but covalent molecule
        };

        // Build the compound map
        const compoundMap = {};
        const allCompounds = []; // For reference list

        // Ionic compounds
        for (let cat of cations) {
            for (let an of anions) {
                let standardFormula = generateFormula(cat, an);
                let flattened = standardFormula.replace(/\$([^)]+)\$(\d+)/g, (match, inner, num) => inner + num);
                let displayName;

                // Special handling for H + OH
                if (cat.symbol === 'H' && an.symbol === 'OH') {
                    standardFormula = 'H2O';
                    flattened = 'H2O';
                    displayName = 'Water';
                } else if (cat.isAcid) {
                    displayName = getAcidName(an);
                } else {
                    const catFullName = cat.baseName.charAt(0).toUpperCase() + cat.baseName.slice(1) + cat.roman;
                    displayName = `${catFullName} ${an.name}`;
                }

                compoundMap[flattened] = {
                    name: displayName,
                    standard: standardFormula,
                    type: 'Ionic'
                };

                allCompounds.push(`${standardFormula} - ${displayName} (Ionic)`);
            }
        }

        // Add covalent compounds to map and list
        for (let formula in covalentCompounds) {
            compoundMap[formula] = {
                name: covalentCompounds[formula],
                standard: formula,
                type: 'Covalent'
            };
            allCompounds.push(`${formula} - ${covalentCompounds[formula]} (Covalent)`);
        }

        // Sort all compounds alphabetically by standard formula
        allCompounds.sort();

        function normalizeFormula(input) {
            if (!input) return '';
            input = input.trim().replace(/\s/g, '').toUpperCase(); // Normalize case for matching
            // Flatten parentheses: (NH4)2 -> NH42
            input = input.replace(/\$([^)]+)\$(\d*)/g, (match, inner, num) => inner + (num || ''));
            // Handle common variations (e.g., co2 -> CO2, but since uppercased, ok)
            return input;
        }

        function
