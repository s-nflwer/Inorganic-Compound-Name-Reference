# Inorganic-Compound-Name-Reference
A reference tool where you can input the inorganic compound formula to show its corresponding common name from the card game "Periodicard."
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Inorganic Compound Namer - Card Game Reference</title>
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
    </style>
</head>
<body>
    <h1>Inorganic Compound Namer - Card Game Reference</h1>
    <p>Enter a chemical formula (e.g., NaCl, H2SO4, NH42SO4 for (NH4)2SO4) to get its name. This covers <strong>all possible valid ionic compounds and acids</strong> formable from the card game's elements and ions (considering valences for neutrality). Formulas are generated from the provided card list: Na, K, Mg, Ca, Al, Zn, Cu(I), Cu(II), Fe(II), Fe(III), O, H, Cl, C (for CO₃²⁻), N (for NO₃⁻), F, S, P (for PO₄³⁻), OH⁻, NH₄⁺, PO₄³⁻, SO₄²⁻.</p>
    <p><strong>Notes:</strong> Enter polyatomic multiples without parentheses (e.g., NH42SO4). Includes ~100 salts, oxides, and acids. Transition metals use Roman numerals. For reference, use the "Show All Compounds" button below.</p>
    
    <input type="text" id="formulaInput" placeholder="Enter formula, e.g., NaCl or NH42SO4" maxlength="50">
    <button onclick="nameCompound()">Name Compound</button>
    
    <div id="result"></div>
    
    <button class="toggle-btn" onclick="toggleAllCompounds()">Show All Compounds (Reference List)</button>
    <div id="allCompounds">
        <h2>All Possible Compounds</h2>
        <ul id="compoundsList"></ul>
    </div>
    
    <div class="examples">
        <strong>Examples:</strong><br>
        NaCl → Sodium chloride<br>
        H2SO4 → Sulfuric acid<br>
        NH42SO4 → Ammonium sulfate (standard: (NH₄)₂SO₄)<br>
        AlPO4 → Aluminum phosphate<br>
        Cu2O → Copper(I) oxide<br>
        FeCl3 → Iron(III) chloride<br>
        CaCO3 → Calcium carbonate
    </div>

    <script>
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

        // Build the compound map
        const compoundMap = {};
        const allCompounds = []; // For reference list

        for (let cat of cations) {
            for (let an of anions) {
                let standardFormula = generateFormula(cat, an);
                let flattened = standardFormula.replace(/\(([^)]+)\)(\d+)/g, (match, inner, num) => inner + num);
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
                    standard: standardFormula
                };

                allCompounds.push(`${standardFormula} - ${displayName}`);
            }
        }

        // Add special neutral compounds if relevant to cards (e.g., NH3 from N + H)
        compoundMap['NH3'] = {name: 'Ammonia', standard: 'NH3'};
        allCompounds.push('NH3 - Ammonia');

        // Sort all compounds alphabetically by standard formula
        allCompounds.sort();

        function normalizeFormula(input) {
            if (!input) return '';
            input = input.trim().replace(/\s/g, '');
            // Flatten parentheses: (NH4)2 -> NH42
            input = input.replace(/\(([^)]+)\)(\d*)/g, (match, inner, num) => inner + (num || ''));
            return input;
        }

        function nameCompound() {
            const input = document.getElementById('formulaInput').value;
            const normalized = normalizeFormula(input);
            const resultDiv = document.getElementById('result');
            
            if (!normalized) {
                resultDiv.innerHTML = '<span class="error">Please enter a formula.</span>';
                resultDiv.className = 'error';
                return;
            }
            
            const entry = compoundMap[normalized];
            if (entry) {
                const text = `${entry.standard}: ${entry.name}`;
                resultDiv.innerHTML = `<strong>Result:</strong> ${text}`;
                resultDiv.className = '';
            } else {
                resultDiv.innerHTML = `<strong>Result:</strong> <span class="error">Unable to name "${input}". This may not be a valid combination from the card deck. Check spelling or try an example.</span>`;
                resultDiv.className = 'error';
            }
        }

        function toggleAllCompounds() {
            const div = document.getElementById('allCompounds');
            const btn = document.querySelector('.toggle-btn');
            if (div.style.display === 'none') {
                div.style.display = 'block';
                document.getElementById('compoundsList').innerHTML = allCompounds.map(comp => `<li>${comp}</li>`).join('');
                btn.textContent = 'Hide All Compounds';
            } else {
                div.style.display = 'none';
                btn.textContent = 'Show All Compounds (Reference List)';
            }
        }
        
        // Allow Enter key to submit
        document.getElementById('formulaInput').addEventListener('keypress', function(e) {
            if (e.key === 'Enter') {
                nameCompound();
            }
        });
    </script>
</body>
</html>
