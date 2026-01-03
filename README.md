<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Periodicards Compound List Reference</title>
    <link rel="manifest" href="manifest.json">  <!-- Link to the manifest for PWA -->
    <style>
        body {
            font-family: 'Courier New', monospace;  /* Chemistry-themed monospace font */
            max-width: 600px;
            margin: 0 auto;
            padding: 20px;
            background-color: #001122;  /* Dark blue background */
            color: #ffffff;  /* White text for contrast */
            position: relative;
            overflow: hidden;  /* Hide overflow for scattered symbols */
        }
        /* Scattered periodic element symbols */
        body::before {
            content: "H O Na Fe C N P S Cl";
            position: absolute;
            top: 10%;
            left: 5%;
            font-size: 24px;
            color: rgba(0, 255, 0, 0.1);  /* Semi-transparent green for chemistry feel */
            z-index: -1;
            white-space: pre-wrap;
            line-height: 2;
        }
        body::after {
            content: "K Mg Ca Al Zn Cu";
            position: absolute;
            bottom: 10%;
            right: 5%;
            font-size: 20px;
            color: rgba(0, 255, 0, 0.1);
            z-index: -1;
            white-space: pre-wrap;
            line-height: 2;
        }
        h1 {
            text-align: center;
            color: #00ff00;  /* Green for chemistry theme */
            text-shadow: 2px 2px 4px #000;  /* Shadow for depth */
        }
        p {
            text-align: center;
            color: #cccccc;  /* Light gray for readability */
        }
        input {
            width: 100%;
            padding: 10px;
            font-size: 16px;
            margin-bottom: 10px;
            border: 2px solid #00ff00;  /* Green border for theme */
            border-radius: 10px;  /* Rounded for lab equipment feel */
            background-color: #002244;  /* Darker blue */
            color: #ffffff;
        }
        button {
            width: 100%;
            padding: 10px;
            font-size: 16px;
            background-color: #ADD8E6;  /* Light blue button */
            color: #001122;  /* Dark blue text */
            border: none;
            border-radius: 10px;
            cursor: pointer;
            font-family: 'Courier New', monospace;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);  /* Shadow for 3D effect */
        }
        button:hover {
            background-color: #87CEEB;  /* Slightly darker light blue on hover */
            transform: scale(1.05);  /* Slight zoom for interactivity */
        }
        #result {
            margin-top: 20px;
            font-size: 18px;
            color: #00ff00;  /* Green for results */
            text-align: center;
            background-color: #002244;  /* Darker background for result box */
            padding: 10px;
            border-radius: 10px;
            border: 1px solid #00ff00;
        }
    </style>
</head>
<body>
    <p>Enter a chemical formula (e.g., Na2O, Mg(OH)2, (NH4)2SO4) to get its name. This app works offline once installed.</p>
    <input type="text" id="formula" placeholder="Enter chemical formula">
    <button onclick="getName()">Get Name</button>
    <p id="result"></p>

    <script>
        // Register the service worker for offline functionality
        if ('serviceWorker' in navigator) {
            navigator.serviceWorker.register('sw.js')
                .then(function(registration) {
                    console.log('Service Worker registered with scope:', registration.scope);
                })
                .catch(function(error) {
                    console.log('Service Worker registration failed:', error);
                });
        }

        const compounds = {
            "HCl": "Hydrogen chloride/Hydrochloric acid",
            "HF": "Hydrogen fluoride/Hydrofluoric acid",
            "H2S": "Hydrogen sulfide",
            "H2SO4": "Sulfuric acid",
            "H3PO4": "Phosphoric acid",
            "NaOH": "Sodium hydroxide",
            "KOH": "Potassium hydroxide",
            "Mg(OH)2": "Magnesium hydroxide",
            "Ca(OH)2": "Calcium hydroxide",
            "Al(OH)3": "Aluminum hydroxide",
            "Zn(OH)2": "Zinc hydroxide",
            "Cu(OH)2": "Copper(II)/Cupric hydroxide",
            "Fe(OH)2": "Iron(II)/Ferrous hydroxide",
            "Fe(OH)3": "Iron(III)/Ferric hydroxide",
            "NH4OH": "Ammonium hydroxide",
            "NaCl": "Sodium chloride",
            "NaF": "Sodium fluoride",
            "Na2S": "Sodium sulfide",
            "Na2SO4": "Sodium sulfate",
            "Na3PO4": "Sodium phosphate",
            "KCl": "Potassium chloride",
            "KF": "Potassium fluoride",
            "K2S": "Potassium sulfide",
            "K2SO4": "Potassium sulfate",
            "K3PO4": "Potassium phosphate",
            "MgCl2": "Magnesium chloride",
            "MgF2": "Magnesium fluoride",
            "MgS": "Magnesium sulfide",
            "MgSO4": "Magnesium sulfate",
            "Mg3(PO4)2": "Magnesium phosphate",
            "CaCl2": "Calcium chloride",
            "CaF2": "Calcium fluoride",
            "CaS": "Calcium sulfide",
            "CaSO4": "Calcium sulfate",
            "Ca3(PO4)2": "Calcium phosphate",
            "AlCl3": "Aluminum chloride",
            "AlF3": "Aluminum fluoride",
            "Al2S3": "Aluminum sulfide",
            "Al2(SO4)3": "Aluminum sulfate",
            "AlPO4": "Aluminum phosphate",
            "ZnCl2": "Zinc chloride",
            "ZnF2": "Zinc fluoride",
            "ZnS": "Zinc sulfide",
            "ZnSO4": "Zinc sulfate",
            "Zn3(PO4)2": "Zinc phosphate",
            "CuCl": "Copper(I)/Cuprous chloride",
            "Cu2S": "Copper(I)/Cuprous sulfide",
            "CuCl2": "Copper(II)/Cupric chloride",
            "CuF2": "Copper(II)/Cupric fluoride",
            "CuS": "Copper(II)/Cupric sulfide",
            "CuSO4": "Copper(II)/Cupric sulfate",
            "Cu3(PO4)2": "Copper(II)/Cupric phosphate",
            "FeCl2": "Iron(II)/Ferrous chloride",
            "FeF2": "Iron(II)/Ferrous fluoride",
            "FeS": "Iron(II)/Ferrous sulfide",
            "FeSO4": "Iron(II)/Ferrous sulfate",
            "Fe3(PO4)2": "Iron(II)/Ferrous phosphate",
            "FeCl3": "Iron(III)/Ferric chloride",
            "FeF3": "Iron(III)/Ferric fluoride",
            "Fe2S3": "Iron(III)/Ferric sulfide",
            "Fe2(SO4)3": "Iron(III)/Ferric sulfate",
            "FePO4": "Iron(III)/Ferric phosphate",
            "NH4Cl": "Ammonium chloride",
            "NH4F": "Ammonium fluoride",
            "(NH4)2S": "Ammonium sulfide",
            "(NH4)2SO4": "Ammonium sulfate",
            "(NH4)3PO4": "Ammonium phosphate",
            "NaH": "Sodium hydride",
            "KH": "Potassium hydride",
            "MgH2": "Magnesium hydride",
            "CaH2": "Calcium hydride",
            "AlH3": "Aluminum hydride",
            "ZnH2": "Zinc hydride",
            "CuH": "Copper(I)/Cuprous hydride",
            "Mg3N2": "Magnesium nitride",
            "Ca3N2": "Calcium nitride",
            "AlN": "Aluminum nitride",
            "Zn3N2": "Zinc nitride",
            "Na2O": "Sodium oxide",
            "K2O": "Potassium oxide",
            "MgO": "Magnesium oxide",
            "CaO": "Calcium oxide",
            "Al2O3": "Aluminum oxide",
            "ZnO": "Zinc oxide",
            "Cu2O": "Copper(I)/Cuprous oxide",
            "CuO": "Copper(II)/Cupric oxide",
            "FeO": "Iron(II)/Ferrous oxide",
            "Fe2O3": "Iron(III)/Ferric oxide",
            "Mg3P2": "Magnesium phosphide",
            "Ca3P2": "Calcium phosphide",
            "Zn3P2": "Zinc phosphide",
            "H2O": "Water/Dihydrogen oxide",
            "NH3": "Ammonia",
            "PH3": "Phosphine",
            "SO2": "Sulfur dioxide",
            "SO3": "Sulfur trioxide",
            "NO2": "Nitrogen dioxide",
            "N2O5": "Dinitrogen pentoxide",
            "P2O5": "Diphosphorus pentoxide",
            "CO": "Carbon monoxide",
            "CO2": "Carbon dioxide",
            "N2O3": "Dinitrogen trioxide",
            "P2O3": "Diphosphorus trioxide",
            "Cu(OH)": "Copper(I) hydroxide",
            "CuF": "Copper(I) fluoride",
            "Cu3PO4": "Copper(I) phosphate",
            "Na3N": "Sodium nitride",
            "K3N": "Potassium nitride",
            "Cu3N": "Copper(I) nitride",
            "Cu3N2": "Copper(II) nitride",
            "Fe3N2": "Iron(II) nitride",
            "FeN": "Iron(III) nitride",
            "Na3P": "Sodium phosphide",
            "K3P": "Potassium phosphide",
            "AlP": "Aluminum phosphide",
            "Cu3P": "Copper(I) phosphide",
            "Cu3P2": "Copper(II) phosphide",
            "Fe3P2": "Iron(II) phosphide",
            "FeP": "Iron(III) phosphide",
            "CuH2": "Copper(II) hydride",
            "FeH2": "Iron(II) hydride",
            "FeH3": "Iron(III) hydride",
            "Na2CO3": "Sodium carbonate",
            "K2CO3": "Potassium carbonate",
            "MgCO3": "Magnesium carbonate",
            "CaCO3": "Calcium carbonate",
            "Al2(CO3)3": "Aluminum carbonate",
            "ZnCO3": "Zinc carbonate",
            "Cu2CO3": "Copper(I) carbonate",
            "CuCO3": "Copper(II) carbonate",
            "FeCO3": "Iron(II) carbonate",
            "Fe2(CO3)3": "Iron(III) carbonate",
            "(NH4)2CO3": "Ammonium carbonate",
            "Na4C": "Sodium carbide",
            "K4C": "Potassium carbide",
            "Mg2C": "Magnesium carbide",
            "Ca2C": "Calcium carbide (CaC2)",
            "Al4C3": "Aluminum carbide",
            "ZnC": "Zinc carbide",
            "Cu4C": "Copper(I) carbide",
            "CuC": "Copper(II) carbide",
            "Fe2C": "Iron(II) carbide (Cementite - Fe3C)",
            "CH4": "Methane (Carbon hydride)",
            "(NH4)2O": "Ammonium oxide"
        };

        function getName() {
            const formula = document.getElementById('formula').value.trim();
            const name = compounds[formula];
            if (name) {
                document.getElementById('result').innerText = `The name is: ${name}`;
            } else {
                document.getElementById('result').innerText = "Compound not found. Please check the formula and ensure it matches the list (e.g., use parentheses for complex ions).";
            }
        }
    </script>
</body>
</html>
