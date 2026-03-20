# Ex06 BMI Calculator
## Date: 

## AIM
To develop a responsive and interactive Body Mass Index (BMI) Calculator using React that allows users to input their height and weight, and calculates their BMI to categorize their health status (e.g., Underweight, Normal, Overweight, Obese).

## DESIGN STEPS

### STEP 1: Initialize React Project

<li>Create a new React app using create-react-app.</li>
<li>Install React Router using:</li>
npm install react-router-dom

### STEP 2: Set Up Routing

Create routing structure with react-router-dom:

<li>Home route (/) – Intro or Navigation</li>

<li>BMI Calculator route (/bmi)</li>

<li>Result route (/result)</li>

### STEP 3: Design the BMI Form Page

<li>Create a form to accept Height (in cm or m) and Weight (in kg).</li>

<li>On form submit, navigate to the result page with entered values via URL query params or context/state.</li>

## STEP 4: Handle Input Validation

<li>Check if height and weight are valid numbers.</li>

<li>Optionally, show error messages for invalid inputs.</li>

### STEP 5: Perform BMI Calculation

<li>In the result component:

<li>Extract height and weight from the route (URL or passed state).</li>

<li>Apply the BMI formula:</li>

![image](https://github.com/user-attachments/assets/ec785506-c96b-489e-8783-fb1a5d36101a)
​
 
<li>Convert height from cm to m if needed.</li></li>

### STEP 6: Display Result

<li>Show calculated BMI.</li>

<li>Show category based on BMI range:

<li>Underweight, Normal, Overweight, Obese, etc.</li></li>

### STEP 7: Navigation Options

<li>Provide a button to go back to the BMI form to calculate again.</li>

### STEP 8: Enhancements

<li>Add styling using CSS or Tailwind.</li>

## PROGRAM
home.jsx
```
import { useNavigate } from "react-router-dom";

// Step 2 – Home route (/)
export default function Home() {
  const navigate = useNavigate();

  return (
    <div className="page home-page">
      <div className="home-content">
        <span className="home-eyebrow">Health Tool</span>
        <h1 className="home-title">BMI<br />Calculator</h1>
        <p className="home-desc">
          Enter your height and weight to instantly calculate your
          Body Mass Index and discover your health category.
        </p>
        <button className="btn-primary" onClick={() => navigate("/bmi")}>
          Get Started →
        </button>
      </div>

      {/* Decorative rings */}
      <div className="ring ring-1" />
      <div className="ring ring-2" />
      <div className="ring ring-3" />
    </div>
  );
}
```

BMIResult.jsx
```
import { useNavigate, useSearchParams } from "react-router-dom";

// BMI scale segments
const CATEGORIES = [
  { label: "Underweight",      min: 0,    max: 18.5, color: "#60a5fa" },
  { label: "Normal",           min: 18.5, max: 25,   color: "#34d399" },
  { label: "Overweight",       min: 25,   max: 30,   color: "#fbbf24" },
  { label: "Obese Class I",    min: 30,   max: 35,   color: "#f97316" },
  { label: "Obese Class II",   min: 35,   max: 40,   color: "#ef4444" },
  { label: "Obese Class III",  min: 40,   max: Infinity, color: "#b91c1c" },
];

function getCategory(bmi) {
  return CATEGORIES.find((c) => bmi >= c.min && bmi < c.max);
}

// Map BMI (10–45) to a 0–100% needle position
function bmiToPercent(bmi) {
  const clamped = Math.min(Math.max(bmi, 10), 45);
  return ((clamped - 10) / 35) * 100;
}

// Step 5 – BMI Calculation
function calcBMI(height, unit, weight) {
  const w = parseFloat(weight);
  let hm = parseFloat(height);
  if (unit === "cm") hm = hm / 100; // convert cm → m
  if (!hm || !w || hm <= 0 || w <= 0) return null;
  return w / (hm * hm);
}

// Step 6 – Result Page (/result)
export default function BMIResult() {
  const navigate = useNavigate();
  const [params] = useSearchParams();

  const height = params.get("height");
  const unit   = params.get("unit") || "cm";
  const weight = params.get("weight");

  const bmi = calcBMI(height, unit, weight);

  // Guard: if invalid params, redirect back
  if (!bmi) {
    return (
      <div className="page">
        <div className="form-card" style={{ textAlign: "center" }}>
          <p style={{ color: "#ef4444", marginBottom: "1.5rem" }}>
            Invalid data. Please go back and try again.
          </p>
          <button className="btn-primary" onClick={() => navigate("/bmi")}>
            ← Recalculate
          </button>
        </div>
      </div>
    );
  }

  const bmiFixed   = bmi.toFixed(1);
  const category   = getCategory(bmi);
  const needlePct  = bmiToPercent(bmi);
  const displayH   = unit === "cm" ? `${height} cm` : `${height} m`;

  return (
    <div className="page result-page">
      <div className="result-card">
        <button className="back-link" onClick={() => navigate("/bmi")}>
          ← Recalculate
        </button>

        <h2 className="form-title">Your Result</h2>

        {/* Input summary */}
        <div className="summary-row">
          <div className="summary-pill">Height: {displayH}</div>
          <div className="summary-pill">Weight: {weight} kg</div>
        </div>

        {/* BMI score */}
        <div className="bmi-score-block" style={{ "--cat-color": category.color }}>
          <span className="bmi-number">{bmiFixed}</span>
          <span className="bmi-label" style={{ color: category.color }}>
            {category.label}
          </span>
        </div>

        {/* Gauge bar */}
        <div className="gauge-wrap">
          <div className="gauge-track">
            {CATEGORIES.map((c, i) => (
              <div
                key={i}
                className="gauge-seg"
                style={{ background: c.color, flex: c.max === Infinity ? 1.5 : c.max - c.min }}
              />
            ))}
            {/* Needle */}
            <div className="gauge-needle" style={{ left: `${needlePct}%` }} />
          </div>
          <div className="gauge-labels">
            <span>10</span>
            <span>18.5</span>
            <span>25</span>
            <span>30</span>
            <span>35</span>
            <span>40+</span>
          </div>
        </div>

        {/* Category table */}
        <div className="cat-table">
          {CATEGORIES.map((c) => (
            <div
              key={c.label}
              className={`cat-row ${c.label === category.label ? "cat-active" : ""}`}
            >
              <span className="cat-dot" style={{ background: c.color }} />
              <span className="cat-name">{c.label}</span>
              <span className="cat-range">
                {c.max === Infinity ? `≥ ${c.min}` : `${c.min} – ${c.max}`}
              </span>
            </div>
          ))}
        </div>

        {/* Step 7 – Navigation back */}
        <button className="btn-primary full-width" onClick={() => navigate("/bmi")}>
          Calculate Again
        </button>
      </div>
    </div>
  );
}
```
App.jsx
```
import { BrowserRouter, Routes, Route } from "react-router-dom";
import Home from "./pages/Home";
import BMIForm from "./pages/BMIForm";
import BMIResult from "./pages/BMIResult";
import "./BMI.css";
 
export default function App() {
  return (
    <BrowserRouter>
      <Routes>
        {/* Step 2 – Routing structure */}
        <Route path="/" element={<Home />} />
        <Route path="/bmi" element={<BMIForm />} />
        <Route path="/result" element={<BMIResult />} />
      </Routes>
    </BrowserRouter>
  );
}
```



## OUTPUT

<img width="1918" height="1145" alt="image" src="https://github.com/user-attachments/assets/37a9f4ed-65b9-4a99-9114-7181ea949cda" />

<img width="1909" height="1147" alt="image" src="https://github.com/user-attachments/assets/4bb43ec4-dd3d-4ede-9b3a-b95372e996df" />

<img width="1919" height="1140" alt="image" src="https://github.com/user-attachments/assets/0216a2bf-9e71-4552-b597-1a60b3a065c3" />



## RESULT
The BMI Calculator successfully takes user input for height and weight, performs the BMI calculation in real-time using React state and event handling, and displays the BMI value along with the corresponding health category.
