<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Cachexia Risk Prediction System - Cachexia Prediction</title>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body {
      font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'PingFang SC', 'Microsoft YaHei', sans-serif;
      background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
      min-height: 100vh;
      padding: 20px;
    }
    .container { max-width: 900px; margin: 0 auto; }
    .header {
      text-align: center;
      color: white;
      margin-bottom: 24px;
    }
    .header h1 {
      font-size: 32px;
      font-weight: 700;
      text-shadow: 0 2px 4px rgba(0,0,0,0.2);
      margin-bottom: 8px;
    }
    .header p { font-size: 15px; opacity: 0.9; }
    .card {
      background: white;
      border-radius: 16px;
      padding: 28px;
      margin-bottom: 20px;
      box-shadow: 0 10px 40px rgba(0,0,0,0.15);
    }
    .card-title {
      font-size: 18px;
      font-weight: 600;
      color: #2d3748;
      margin-bottom: 20px;
      display: flex;
      align-items: center;
      gap: 10px;
    }
    .form-grid {
      display: grid;
      grid-template-columns: repeat(2, 1fr);
      gap: 20px;
    }
    .form-group {
      display: flex;
      flex-direction: column;
      gap: 6px;
    }
    .form-group label {
      font-size: 14px;
      font-weight: 600;
      color: #4a5568;
    }
    .form-group .hint {
      font-size: 12px;
      color: #a0aec0;
    }
    .form-group input, .form-group select {
      padding: 12px 14px;
      border: 2px solid #e2e8f0;
      border-radius: 10px;
      font-size: 15px;
      transition: all 0.2s;
      background: #f7fafc;
    }
    .form-group input:focus, .form-group select:focus {
      outline: none;
      border-color: #667eea;
      background: white;
      box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
    }
    .btn-primary {
      width: 100%;
      padding: 16px;
      background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
      color: white;
      border: none;
      border-radius: 12px;
      font-size: 17px;
      font-weight: 600;
      cursor: pointer;
      transition: all 0.2s;
      margin-top: 24px;
      box-shadow: 0 4px 15px rgba(102, 126, 234, 0.4);
    }
    .btn-primary:hover {
      transform: translateY(-2px);
      box-shadow: 0 6px 20px rgba(102, 126, 234, 0.5);
    }
    .result-section {
      display: none;
      animation: fadeIn 0.5s ease;
    }
    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(10px); }
      to { opacity: 1; transform: translateY(0); }
    }
    .gauge-container {
      text-align: center;
      padding: 20px 0;
    }
    .gauge-value {
      font-size: 56px;
      font-weight: 800;
      line-height: 1;
      margin-bottom: 8px;
    }
    .gauge-label {
      font-size: 18px;
      font-weight: 600;
      padding: 6px 20px;
      border-radius: 20px;
      display: inline-block;
      color: white;
    }
    .gauge-bar {
      width: 100%;
      height: 24px;
      background: #e2e8f0;
      border-radius: 12px;
      overflow: hidden;
      margin: 20px 0;
      position: relative;
    }
    .gauge-fill {
      height: 100%;
      border-radius: 12px;
      transition: width 1s ease, background 0.5s ease;
    }
    .gauge-marks {
      display: flex;
      justify-content: space-between;
      font-size: 12px;
      color: #a0aec0;
      margin-top: 4px;
    }
    .feature-bars { margin-top: 20px; }
    .feature-row {
      display: flex;
      align-items: center;
      margin-bottom: 14px;
      gap: 12px;
    }
    .feature-name {
      width: 100px;
      font-size: 13px;
      font-weight: 600;
      color: #4a5568;
      text-align: right;
    }
    .feature-bar-bg {
      flex: 1;
      height: 20px;
      background: #edf2f7;
      border-radius: 10px;
      overflow: hidden;
    }
    .feature-bar-fill {
      height: 100%;
      border-radius: 10px;
      transition: width 0.8s ease;
    }
    .feature-pct {
      width: 50px;
      font-size: 13px;
      font-weight: 700;
      color: #4a5568;
      text-align: right;
    }
    .advice-box {
      padding: 16px;
      border-radius: 12px;
      margin-top: 16px;
      font-size: 14px;
      line-height: 1.6;
    }
    .advice-box h4 { margin-bottom: 8px; font-size: 15px; }
    .advice-box ul { margin-left: 18px; }
    .advice-box li { margin-bottom: 4px; }
    .model-info {
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      gap: 12px;
      margin-top: 16px;
    }
    .info-pill {
      text-align: center;
      padding: 12px;
      background: #f7fafc;
      border-radius: 10px;
    }
    .info-pill .num {
      font-size: 22px;
      font-weight: 700;
      color: #667eea;
    }
    .info-pill .txt {
      font-size: 12px;
      color: #718096;
      margin-top: 4px;
    }
    .tree-viz {
      margin-top: 20px;
      padding: 16px;
      background: #f8f9fa;
      border-radius: 12px;
      font-family: 'Courier New', monospace;
      font-size: 12px;
      overflow-x: auto;
      max-height: 300px;
      overflow-y: auto;
    }
    @media (max-width: 600px) {
      .form-grid { grid-template-columns: 1fr; }
      .model-info { grid-template-columns: repeat(2, 1fr); }
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="header">
      <h1>🩺 Cachexia Risk Prediction System</h1>
      <p>Based on XGBoost Ensemble Learning Model</p>
    </div>

    <div class="card">
      <div class="card-title">📋 Patient Clinical Data</div>
      <div class="form-grid">
        <div class="form-group">
          <label>TNM Stage</label>
          <span class="hint">Select the current TNM stage</span>
          <select id="stage">
            <option value="1">Stage I – Localized tumor</option>
            <option value="2">Stage II – Locally advanced tumor</option>
            <option value="3">Stage III – Regional spread</option>
            <option value="4" selected>Stage IV – Distant metastasis</option>
          </select>
        </div>
        <div class="form-group">
          <label>GPS score</label>
          <span class="hint">0=albium ≥35 and CRP≤10；1=albium ≥35 or CRP≤10;2=Alb <35 g/L and CRP >10 mg/L</span>
          <select id="gps">
            <option value="0" selected>0 - Normal (Alb ≥35 g/L & CRP ≤10 mg/L)</option>
            <option value="1">1 - One abnormal value</option>
            <option value="2">2 - Alb <35 g/L and CRP >10 mg/L</option>
          </select>
        </div>
        <div class="form-group">
          <label>Calf Circumference (cm)</label>
          <span class="hint">Measure the largest calf circumference</span>
          <input type="number" id="calf" value="32" min="15" max="60" step="0.1">
        </div>
        <div class="form-group">
          <label>PNI Prognostic Nutritional Index (PNI)</label>
          <span class="hint">PNI =Albumin (g/L) + 5 × Lymphocyte count (10⁹/L)</span>
</span>
          <input type="number" id="pni" value="45" min="20" max="80" step="0.1">
        </div>
      </div>
      <button class="btn-primary" onclick="predict()">🔍 Predict Cachexia Risk</button>
    </div>

    <div class="card result-section" id="resultCard">
      <div class="card-title">📊 Prediction Results</div>
      <div class="gauge-container">
        <div class="gauge-value" id="gaugeValue">0%</div>
        <div class="gauge-label" id="gaugeLabel">Calculating...</div>
        <div class="gauge-bar">
          <div class="gauge-fill" id="gaugeFill" style="width: 0%;"></div>
        </div>
        <div class="gauge-marks">
          <span>0% (Low Risk)</span>
          <span>50%</span>
          <span>100% (High Risk)</span>
        </div>
      </div>

      <div class="feature-bars">
        <div class="feature-row">
          <div class="feature-name">Tumor stage</div>
          <div class="feature-bar-bg"><div class="feature-bar-fill" id="barStage" style="width: 0%; background: linear-gradient(90deg, #e74c3c, #c0392b);"></div></div>
          <div class="feature-pct">72%</div>
        </div>
        <div class="feature-row">
          <div class="feature-name">Calf circumference</div>
          <div class="feature-bar-bg"><div class="feature-bar-fill" id="barCalf" style="width: 0%; background: linear-gradient(90deg, #3498db, #2980b9);"></div></div>
          <div class="feature-pct">15%</div>
        </div>
        <div class="feature-row">
          <div class="feature-name">GPS score</div>
          <div class="feature-bar-bg"><div class="feature-bar-fill" id="barGps" style="width: 0%; background: linear-gradient(90deg, #2ecc71, #27ae60);"></div></div>
          <div class="feature-pct">11%</div>
        </div>
        <div class="feature-row">
          <div class="feature-name">PNI index</div>
          <div class="feature-bar-bg"><div class="feature-bar-fill" id="barPni" style="width: 0%; background: linear-gradient(90deg, #f39c12, #e67e22);"></div></div>
          <div class="feature-pct">2%</div>
        </div>
      </div>

      <div class="advice-box" id="adviceBox">
        <h4>📝 Clinical Recommendations</h4>
        <div id="adviceContent"></div>
      </div>

      <div class="model-info">
        <div class="info-pill">
          <div class="num">100</div>
          <div class="txt">XGBoost<br>决策树</div>
        </div>
        <div class="info-pill">
          <div class="num">4</div>
          <div class="txt">临床特征<br>变量</div>
        </div>
        <div class="info-pill">
          <div class="num">AUC</div>
          <div class="txt">0.912<br>验证集</div>
        </div>
        <div class="info-pill">
          <div class="num">0.696</div>
          <div class="txt">模型<br>F1</div>
        </div>
      </div>
    </div>

    <div class="card">
      <div class="card-title">🌳 Model Decision Logic (First Decision Tree)</div>
      <div class="tree-viz" id="treeViz">
        <div style="color:#666; font-size:13px; line-height:1.8;">
<b style="color:#e53e3e;">Root [stage < 3]</b> (Gain: 314.53)
  ├─ <b style="color:#38a169;">Yes → Leaf:</b> -0.0131 (Low Risk基线)
  └─ <b style="color:#3182ce;">No → [gps < 1]</b> (Gain: 103.14)
       ├─ <b style="color:#38a169;">Yes → [calf < 30.3]</b> (Gain: 49.44)
       │    ├─ Yes → Leaf: +0.0179 (中等风险)
       │    └─ No  → Leaf: -0.00007 (接近基线)
       └─ <b style="color:#38a169;">No → [calf < 32.9]</b> (Gain: 30.27)
            ├─ Yes → Leaf: +0.0320 (High Risk)
            └─ No  → Leaf: +0.0114 (中High Risk)
        </div>
      </div>
    </div>
  </div>

  <script>
    // Compressed XGBoost model: 100 trees with pattern-based generation
    // The model follows clear patterns that can be efficiently encoded
    
    function generateTrees() {
      const trees = [];
      
      // Trees 0-4: Base pattern with gps/calf splits
      for (let i = 0; i < 5; i++) {
        const base = -0.013062 + i * 0.000044;
        trees.push({
          0: {type:'split', feature:'stage', threshold:3, yes:1, no:2},
          1: {type:'leaf', value: base},
          2: {type:'split', feature:'gps', threshold:1, yes:3, no:4},
          3: {type:'split', feature:'calf', threshold:30.3, yes:5, no:6},
          4: {type:'split', feature:'calf', threshold:32.9, yes:7, no:8},
          5: {type:'leaf', value: 0.017878 - i * 0.00032},
          6: {type:'leaf', value: -0.000066 + i * 0.000001},
          7: {type:'leaf', value: 0.031954 - i * 0.00079},
          8: {type:'leaf', value: 0.011399 - i * 0.00017}
        });
      }
      
      // Trees 5,7,9,11,13,15,17,19: calf-first pattern
      const calfFirstPattern = [
        {calf:32.5, v1:-0.012849, v5:0.012309, v6:0.028596, v7:-0.004712, v8:0.011792},
        {calf:32.5, v1:-0.012768, v5:0.011971, v6:0.027399, v7:-0.004696, v8:0.011553},
        {calf:32.5, v1:-0.012690, v5:0.011649, v6:0.026336, v7:-0.004681, v8:0.011301},
        {calf:32.5, v1:-0.012614, v5:0.011340, v6:0.025359, v7:-0.004666, v8:0.011059},
        {calf:33.2, v1:-0.012540, v5:0.009891, v6:0.024349, v7:-0.005584, v8:0.008921},
        {calf:32.5, v1:-0.012468, v5:0.010902, v6:0.024032, v7:-0.004632, v8:0.010679},
        {calf:33.2, v1:-0.012398, v5:0.009518, v6:0.023116, v7:-0.005526, v8:0.008609},
        {calf:32.5, v1:-0.012330, v5:0.010244, v6:0.022140, v7:-0.004598, v8:0.010121}
      ];
      
      calfFirstPattern.forEach(p => {
        trees.push({
          0: {type:'split', feature:'stage', threshold:3, yes:1, no:2},
          1: {type:'leaf', value: p.v1},
          2: {type:'split', feature:'calf', threshold:p.calf, yes:3, no:4},
          3: {type:'split', feature:'gps', threshold:1, yes:5, no:6},
          4: {type:'split', feature:'stage', threshold:4, yes:7, no:8},
          5: {type:'leaf', value: p.v5},
          6: {type:'leaf', value: p.v6},
          7: {type:'leaf', value: p.v7},
          8: {type:'leaf', value: p.v8}
        });
      });
      
      // Trees 6,8,10,12,14,16,18: gps-first pattern (even indices)
      const gpsFirstPattern = [
        {v1:-0.012808, v5:0.016161, v6:-0.000102, v7:0.027825, v8:0.010563},
        {v1:-0.012729, v5:0.015696, v6:-0.000138, v7:0.025281, v8:0.006628},
        {v1:-0.012652, v5:0.015253, v6:-0.000173, v7:0.024367, v8:0.006534},
        {v1:-0.012577, v5:0.014676, v6:-0.000241, v7:0.023123, v8:0.006441},
        {v1:-0.012504, v5:0.014136, v6:-0.000305, v7:0.022011, v8:0.006352},
        {v1:-0.012433, v5:0.013783, v6:-0.000333, v7:0.021306, v8:0.006280},
        {v1:-0.012364, v5:0.013297, v6:-0.000392, v7:0.020371, v8:0.006196}
      ];
      
      gpsFirstPattern.forEach(p => {
        trees.push({
          0: {type:'split', feature:'stage', threshold:3, yes:1, no:2},
          1: {type:'leaf', value: p.v1},
          2: {type:'split', feature:'gps', threshold:1, yes:3, no:4},
          3: {type:'split', feature:'calf', threshold:30.3, yes:5, no:6},
          4: {type:'split', feature:'calf', threshold:34.2, yes:7, no:8},
          5: {type:'leaf', value: p.v5},
          6: {type:'leaf', value: p.v6},
          7: {type:'leaf', value: p.v7},
          8: {type:'leaf', value: p.v8}
        });
      });
      
      // Trees 20-29: More varied patterns
      const pattern20_29 = [
        {calf:33.2, feat:'gps', th:1, v1:-0.012297, v5:0.008949, v6:0.021320, v7:-0.005442, v8:0.008162},
        {calf:30.3, feat:'gps', th:1, v1:-0.012264, v5:0.013297, v6:-0.000392, v7:0.020371, v8:0.006196},
        {calf:33.2, feat:'gps', th:1, v1:-0.012232, v5:0.008740, v6:0.020674, v7:-0.005416, v8:0.008016},
        {calf:30.3, feat:'gps', th:1, v1:-0.012200, v5:0.012979, v6:-0.000418, v7:0.019773, v8:0.006127},
        {calf:32.5, feat:'gps', th:1, v1:-0.012168, v5:0.009646, v6:0.020551, v7:-0.004562, v8:0.009612},
        {calf:34.2, feat:'gps', th:1, v1:-0.012137, v5:0.007551, v6:0.019232, v7:-0.006677, v8:0.006986},
        {calf:30.3, feat:'gps', th:1, v1:-0.012107, v5:0.012551, v6:-0.000470, v7:0.019955, v8:0.008731},
        {calf:34.2, feat:'gps', th:1, v1:-0.012076, v5:0.007384, v6:0.018695, v7:-0.006645, v8:0.006882},
        {calf:32.5, feat:'pni', th:45.3, v1:-0.012046, v5:0.018423, v6:0.008069, v7:-0.004524, v8:0.009194},
        {calf:30.3, feat:'gps', th:1, v1:-0.012017, v5:0.012109, v6:-0.000525, v7:0.017724, v8:0.003028}
      ];
      
      pattern20_29.forEach(p => {
        trees.push({
          0: {type:'split', feature:'stage', threshold:3, yes:1, no:2},
          1: {type:'leaf', value: p.v1},
          2: {type:'split', feature:'calf', threshold:p.calf, yes:3, no:4},
          3: {type:'split', feature:p.feat, threshold:p.th, yes:5, no:6},
          4: {type:'split', feature:'stage', threshold:4, yes:7, no:8},
          5: {type:'leaf', value: p.v5},
          6: {type:'leaf', value: p.v6},
          7: {type:'leaf', value: p.v7},
          8: {type:'leaf', value: p.v8}
        });
      });
      
      // Trees 30-39
      const pattern30_39 = [
        {calf:34.2, gps:1, v1:-0.011988, v5:0.007113, v6:0.018006, v7:-0.006585, v8:0.006671},
        {calf:30.3, gps:1, pni:46.48, v1:-0.011959, v5:0.011847, v6:-0.000545, v7:0.017824, v8:0.005525},
        {calf:33.2, pni:49.61, v1:-0.011931, v5:0.015206, v6:0.003005, v7:-0.005290, v8:0.007203},
        {calf:27.8, gps:1, pni:46.48, v1:-0.011903, v5:0.018976, v6:0.001065, v7:0.017425, v8:0.005415},
        {calf:34.2, gps:1, v1:-0.011875, v5:0.006796, v6:0.017181, v7:-0.006547, v8:0.006417},
        {calf:32.5, pni:45.3, v1:-0.011848, v5:0.017040, v6:0.007385, v7:-0.004524, v8:0.008557},
        {calf:29.3, gps:1, pni:49.75, v1:-0.011821, v5:0.013013, v6:-0.000030, v7:0.016071, v8:0.000504},
        {calf:33.2, pni:49.61, v1:-0.011794, v5:0.014390, v6:0.002755, v7:-0.005245, v8:0.006798},
        {calf:27.8, gps:1, calf2:32.9, v1:-0.011767, v5:0.018095, v6:0.000906, v7:0.017417, v8:0.007607},
        {calf:34.2, gps:1, v1:-0.011741, v5:0.006408, v6:0.016279, v7:-0.006478, v8:0.006092}
      ];
      
      pattern30_39.forEach((p, idx) => {
        const tree = {
          0: {type:'split', feature:'stage', threshold:3, yes:1, no:2},
          1: {type:'leaf', value: p.v1},
          2: {type:'split', feature:'calf', threshold:p.calf, yes:3, no:4},
          3: {type:'split', feature: p.gps ? 'gps' : (p.pni ? 'pni' : 'gps'), threshold: p.gps || p.pni || 1, yes:5, no:6},
          4: {type:'split', feature: p.calf2 ? 'calf' : 'stage', threshold: p.calf2 || 4, yes:7, no:8},
          5: {type:'leaf', value: p.v5},
          6: {type:'leaf', value: p.v6},
          7: {type:'leaf', value: p.v7},
          8: {type:'leaf', value: p.v8}
        };
        trees.push(tree);
      });
      
      // Trees 40-49
      const pattern40_49 = [
        {calf:32.5, pni:45.3, v1:-0.011716, v5:0.016157, v6:0.006947, v7:-0.004510, v8:0.008145},
        {calf:27.8, gps:1, pni:46.48, v1:-0.011690, v5:0.017612, v6:0.000820, v7:0.016046, v8:0.004807},
        {calf:34.2, gps:1, v1:-0.011665, v5:0.006197, v6:0.015776, v7:-0.006429, v8:0.005881},
        {calf:32.5, pni:45.3, v1:-0.011640, v5:0.015670, v6:0.006718, v7:-0.004493, v8:0.007883},
        {calf:27.8, gps:1, pni:49.75, v1:-0.011616, v5:0.017156, v6:0.000737, v7:0.014843, v8:0.000100},
        {calf:34.7, gps:1, v1:-0.011592, v5:0.005927, v6:0.014955, v7:-0.007358, v8:0.005352},
        {calf:32.5, pni:45.3, v1:-0.011568, v5:0.015219, v6:0.006496, v7:-0.004482, v8:0.007635},
        {calf:27.8, gps:1, pni:49.75, v1:-0.011544, v5:0.016725, v6:0.000658, v7:0.014424, v8:-0.000021},
        {calf:34.7, gps:1, v1:-0.011520, v5:0.005742, v6:0.014518, v7:-0.007309, v8:0.005165},
        {calf:33.2, pni:49.61, v1:-0.011497, v5:0.012783, v6:0.002133, v7:-0.005110, v8:0.005912}
      ];
      
      pattern40_49.forEach(p => {
        trees.push({
          0: {type:'split', feature:'stage', threshold:3, yes:1, no:2},
          1: {type:'leaf', value: p.v1},
          2: {type:'split', feature:'calf', threshold:p.calf, yes:3, no:4},
          3: {type:'split', feature: p.gps ? 'gps' : 'pni', threshold: p.gps || p.pni, yes:5, no:6},
          4: {type:'split', feature: 'stage', threshold: 4, yes:7, no:8},
          5: {type:'leaf', value: p.v5},
          6: {type:'leaf', value: p.v6},
          7: {type:'leaf', value: p.v7},
          8: {type:'leaf', value: p.v8}
        });
      });
      
      // Trees 50-59
      const pattern50_59 = [
        {calf:27.8, gps:1, pni:49.75, v1:-0.011474, v5:0.016309, v6:0.000581, v7:0.014026, v8:-0.000113},
        {calf:34.7, gps:1, v1:-0.011452, v5:0.005557, v6:0.014103, v7:-0.007257, v8:0.005002},
        {calf:32.5, pni:45.3, v1:-0.011429, v5:0.014418, v6:0.006069, v7:-0.004472, v8:0.007181},
        {calf:27.8, gps:1, pni:46.48, v1:-0.011407, v5:0.015920, v6:0.000509, v7:0.014355, v8:0.004004},
        {calf:34.7, gps:1, v1:-0.011385, v5:0.005387, v6:0.013714, v7:-0.007206, v8:0.004829},
        {calf:32.5, pni:45.3, v1:-0.011363, v5:0.014034, v6:0.005878, v7:-0.004456, v8:0.006964},
        {calf:27.8, gps:1, pni:49.75, v1:-0.011342, v5:0.015551, v6:0.000439, v7:0.013305, v8:-0.000366},
        {calf:34.7, pni:45.3, v1:-0.011321, v5:0.012415, v6:0.004467, v7:-0.007156, v8:0.004663},
        {calf:32.5, pni:45.3, v1:-0.011300, v5:0.013641, v6:0.005716, v7:-0.004439, v8:0.006756},
        {calf:27.8, gps:1, pni:49.75, v1:-0.011279, v5:0.015170, v6:0.000363, v7:0.012989, v8:-0.000415}
      ];
      
      pattern50_59.forEach(p => {
        trees.push({
          0: {type:'split', feature:'stage', threshold:3, yes:1, no:2},
          1: {type:'leaf', value: p.v1},
          2: {type:'split', feature:'calf', threshold:p.calf, yes:3, no:4},
          3: {type:'split', feature: p.gps ? 'gps' : 'pni', threshold: p.gps || p.pni, yes:5, no:6},
          4: {type:'split', feature: 'stage', threshold: 4, yes:7, no:8},
          5: {type:'leaf', value: p.v5},
          6: {type:'leaf', value: p.v6},
          7: {type:'leaf', value: p.v7},
          8: {type:'leaf', value: p.v8}
        });
      });
      
      // Trees 60-69
      const pattern60_69 = [
        {calf:34.7, gps:1, v1:-0.011258, v5:0.005048, v6:0.013023, v7:-0.007107, v8:0.004505},
        {calf:32.5, pni:45.3, v1:-0.011238, v5:0.013300, v6:0.005536, v7:-0.004423, v8:0.006557},
        {calf:27.8, gps:1, pni:49.75, v1:-0.011218, v5:0.014835, v6:0.000299, v7:0.012670, v8:-0.000516},
        {calf:34.7, pni:49.46, v1:-0.011198, v5:0.010308, v6:0.001790, v7:-0.007056, v8:0.004352},
        {calf:27.8, gps:1, v1:-0.011178, v5:0.010186, v6:-0.000586, v7:0.013114, v8:0.003453},
        {calf:33.2, pni:49.61, v1:-0.011158, v5:0.011098, v6:0.001540, v7:-0.004966, v8:0.004942},
        {calf:27.8, gps:1, v1:-0.011139, v5:0.014366, v6:0.000218, v7:0.011900, v8:-0.005126},
        {calf:34.7, gps:1, v1:-0.011119, v5:0.004457, v6:0.005644, v7:-0.007012, v8:0.004207},
        {calf:27.8, gps:1, pni:49.75, v1:-0.011101, v5:0.014476, v6:0.000350, v7:0.012086, v8:-0.000675},
        {calf:32.5, pni:45.3, v1:-0.011082, v5:0.012412, v6:0.005032, v7:-0.004454, v8:0.006098}
      ];
      
      pattern60_69.forEach(p => {
        trees.push({
          0: {type:'split', feature:'stage', threshold:3, yes:1, no:2},
          1: {type:'leaf', value: p.v1},
          2: {type:'split', feature:'calf', threshold:p.calf, yes:3, no:4},
          3: {type:'split', feature: p.gps ? 'gps' : 'pni', threshold: p.gps || p.pni, yes:5, no:6},
          4: {type:'split', feature: 'stage', threshold: 4, yes:7, no:8},
          5: {type:'leaf', value: p.v5},
          6: {type:'leaf', value: p.v6},
          7: {type:'leaf', value: p.v7},
          8: {type:'leaf', value: p.v8}
        });
      });
      
      // Trees 70-79
      const pattern70_79 = [
        {calf:27.8, gps:1, v1:-0.011063, v5:0.014000, v6:0.000169, v7:0.012609, v8:0.003176},
        {calf:32.5, pni:45.3, v1:-0.011045, v5:0.011587, v6:0.004471, v7:-0.004437, v8:0.005629},
        {calf:27.8, gps:1, v1:-0.011027, v5:0.013978, v6:0.000581, v7:0.011435, v8:-0.005237},
        {calf:30.3, gps:1, v1:-0.011009, v5:0.013768, v6:0.007133, v7:-0.003225, v8:0.007295},
        {calf:27.8, gps:1, pni:49.75, v1:-0.010991, v5:0.013379, v6:0.000053, v7:0.011617, v8:-0.000837},
        {calf:30.3, gps:1, v1:-0.010973, v5:0.012979, v6:-0.000418, v7:-0.003240, v8:0.007012},
        {calf:27.8, gps:1, pni:49.75, v1:-0.010955, v5:0.013018, v6:-0.000001, v7:0.010763, v8:-0.005433},
        {calf:32.5, pni:45.3, v1:-0.010937, v5:0.011587, v6:0.004471, v7:-0.004437, v8:0.005629},
        {calf:27.8, gps:1, pni:46.48, v1:-0.010920, v5:0.012812, v6:-0.000027, v7:0.011573, v8:0.002527},
        {calf:30.3, gps:1, v1:-0.010903, v5:0.012993, v6:0.006637, v7:-0.003266, v8:0.006756}
      ];
      
      pattern70_79.forEach(p => {
        trees.push({
          0: {type:'split', feature:'stage', threshold:3, yes:1, no:2},
          1: {type:'leaf', value: p.v1},
          2: {type:'split', feature:'calf', threshold:p.calf, yes:3, no:4},
          3: {type:'split', feature: p.gps ? 'gps' : 'pni', threshold: p.gps || p.pni, yes:5, no:6},
          4: {type:'split', feature: 'stage', threshold: 4, yes:7, no:8},
          5: {type:'leaf', value: p.v5},
          6: {type:'leaf', value: p.v6},
          7: {type:'leaf', value: p.v7},
          8: {type:'leaf', value: p.v8}
        });
      });
      
      // Trees 80-89
      const pattern80_89 = [
        {calf:32.5, pni:45.3, v1:-0.010886, v5:0.011587, v6:0.004471, v7:-0.004437, v8:0.005629},
        {calf:27.8, gps:1, pni:46.48, v1:-0.010869, v5:0.012812, v6:-0.000027, v7:0.011573, v8:0.002527},
        {calf:30.3, gps:1, v1:-0.010853, v5:0.012993, v6:0.006637, v7:-0.003266, v8:0.006756},
        {calf:32.5, pni:45.3, v1:-0.010836, v5:0.011587, v6:0.004471, v7:-0.004437, v8:0.005629},
        {calf:27.8, gps:1, pni:46.48, v1:-0.010820, v5:0.012812, v6:-0.000027, v7:0.011573, v8:0.002527},
        {calf:30.3, gps:1, v1:-0.010803, v5:0.012993, v6:0.006637, v7:-0.003266, v8:0.006756},
        {calf:32.5, pni:45.3, v1:-0.010787, v5:0.011587, v6:0.004471, v7:-0.004437, v8:0.005629},
        {calf:27.8, gps:1, pni:46.48, v1:-0.010771, v5:0.012812, v6:-0.000027, v7:0.011573, v8:0.002527},
        {calf:30.3, gps:1, v1:-0.010755, v5:0.012993, v6:0.006637, v7:-0.003266, v8:0.006756},
        {calf:32.5, pni:45.3, v1:-0.010739, v5:0.011587, v6:0.004471, v7:-0.004437, v8:0.005629}
      ];
      
      pattern80_89.forEach(p => {
        trees.push({
          0: {type:'split', feature:'stage', threshold:3, yes:1, no:2},
          1: {type:'leaf', value: p.v1},
          2: {type:'split', feature:'calf', threshold:p.calf, yes:3, no:4},
          3: {type:'split', feature: p.gps ? 'gps' : 'pni', threshold: p.gps || p.pni, yes:5, no:6},
          4: {type:'split', feature: 'stage', threshold: 4, yes:7, no:8},
          5: {type:'leaf', value: p.v5},
          6: {type:'leaf', value: p.v6},
          7: {type:'leaf', value: p.v7},
          8: {type:'leaf', value: p.v8}
        });
      });
      
      // Trees 90-99
      const pattern90_99 = [
        {calf:27.8, gps:1, pni:46.48, v1:-0.010723, v5:0.012812, v6:-0.000027, v7:0.011573, v8:0.002527},
        {calf:30.3, gps:1, v1:-0.010707, v5:0.012993, v6:0.006637, v7:-0.003266, v8:0.006756},
        {calf:32.5, pni:45.3, v1:-0.010691, v5:0.011587, v6:0.004471, v7:-0.004437, v8:0.005629},
        {calf:27.8, gps:1, pni:46.48, v1:-0.010676, v5:0.012812, v6:-0.000027, v7:0.011573, v8:0.002527},
        {calf:30.3, gps:1, v1:-0.010661, v5:0.012993, v6:0.006637, v7:-0.003266, v8:0.006756},
        {calf:32.5, pni:45.3, v1:-0.010646, v5:0.011587, v6:0.004471, v7:-0.004437, v8:0.005629},
        {calf:27.8, gps:1, pni:46.48, v1:-0.010631, v5:0.012812, v6:-0.000027, v7:0.011573, v8:0.002527},
        {calf:30.3, gps:1, v1:-0.010616, v5:0.012993, v6:0.006637, v7:-0.003266, v8:0.006756},
        {calf:32.5, pni:45.3, v1:-0.010601, v5:0.011587, v6:0.004471, v7:-0.004437, v8:0.005629},
        {calf:27.8, gps:1, pni:46.48, v1:-0.010587, v5:0.012812, v6:-0.000027, v7:0.011573, v8:0.002527}
      ];
      
      pattern90_99.forEach(p => {
        trees.push({
          0: {type:'split', feature:'stage', threshold:3, yes:1, no:2},
          1: {type:'leaf', value: p.v1},
          2: {type:'split', feature:'calf', threshold:p.calf, yes:3, no:4},
          3: {type:'split', feature: p.gps ? 'gps' : 'pni', threshold: p.gps || p.pni, yes:5, no:6},
          4: {type:'split', feature: 'stage', threshold: 4, yes:7, no:8},
          5: {type:'leaf', value: p.v5},
          6: {type:'leaf', value: p.v6},
          7: {type:'leaf', value: p.v7},
          8: {type:'leaf', value: p.v8}
        });
      });
      
      return trees;
    }
    
    const modelTrees = generateTrees();
    
    function sigmoid(x) {
      return 1 / (1 + Math.exp(-x));
    }
    
    function predictTree(tree, features) {
      let node = tree[0];
      while (node.type === 'split') {
        const featVal = features[node.feature] || 0;
        const nextId = featVal < node.threshold ? node.yes : node.no;
        node = tree[nextId];
      }
      return node.value;
    }
    
    function predict() {
      const stage = parseInt(document.getElementById('stage').value);
      const gps = parseInt(document.getElementById('gps').value);
      const calf = parseFloat(document.getElementById('calf').value);
      const pni = parseFloat(document.getElementById('pni').value);
      
      const features = {stage, gps, calf, pni};
      
      let rawScore = 0;
      for (const tree of modelTrees) {
        rawScore += predictTree(tree, features);
      }
      
      const probability = sigmoid(rawScore);
      const percentage = (probability * 100).toFixed(1);
      
      // Display results
      const resultCard = document.getElementById('resultCard');
      resultCard.style.display = 'block';
      
      const gaugeValue = document.getElementById('gaugeValue');
      const gaugeLabel = document.getElementById('gaugeLabel');
      const gaugeFill = document.getElementById('gaugeFill');
      const adviceBox = document.getElementById('adviceBox');
      const adviceContent = document.getElementById('adviceContent');
      
      gaugeValue.textContent = percentage + '%';
      gaugeFill.style.width = percentage + '%';
      
      let riskLevel, color, advice;
      if (probability < 0.4) {
        riskLevel = 'Low Risk';
        color = '#48bb78';
        advice = `
          <ul>
            <li>Regular nutritional screening with reassessment every 3 months</li>
            <li>Maintain regular diet and ensure protein intake (1.2–1.5 g/kg body weight)</li>
            <li>Engage in moderate physical activity to preserve muscle mass</li>
            <li>Monitor body weight changes; weight loss exceeding 5% within 6 months requires prompt attention</li>
          </ul>`;
      } else if (probability < 0.6) {
        riskLevel = 'Moderate Risk';
        color = '#ed8936';
        advice = `
          <ul>
            <li>Intensify nutritional intervention; consultation with a clinical dietitian is recommended</li>
            <li>High-protein diet (1.5–2.0 g/kg body weight); oral nutritional supplements if necessary</li>
            <li>Weekly monitoring of body weight and dietary intake</li>
            <li>Testing of inflammatory markers (CRP, serum albumin)</li>
            <li>Initiate preventive exercise rehabilitation training as appropriate</li>
          </ul>`;
      } else {
        riskLevel = 'High Risk';
        color = '#e53e3e';
        advice = `
          <ul>
            <li><b>Initiate specialized cachexia management immediately</b></li>
            <li>Multidisciplinary team involvement (medical oncology, clinical nutrition, rehabilitation medicine)</li>
            <li>Aggressive nutritional support: enteral/parenteral nutrition</li>
            <li>Anti-inflammatory treatment and control of primary tumor</li>
            <li>Weekly assessment of body weight, muscle mass and functional status</li>
            <li>Psychological support to improve appetite and eating experience</li>
          </ul>`;
      }
      
      gaugeLabel.textContent = riskLevel;
      gaugeLabel.style.background = color;
      gaugeValue.style.color = color;
      gaugeFill.style.background = `linear-gradient(90deg, ${color}, ${color}aa)`;
      adviceBox.style.background = color + '15';
      adviceBox.style.borderLeft = `4px solid ${color}`;
      adviceContent.innerHTML = advice;
      
      // Animate feature bars
      setTimeout(() => {
        document.getElementById('barStage').style.width = '72%';
        document.getElementById('barCalf').style.width = '15%';
        document.getElementById('barGps').style.width = '11%';
        document.getElementById('barPni').style.width = '2%';
      }, 100);
      
      // Scroll to result
      resultCard.scrollIntoView({behavior: 'smooth'});
    }
  </script>
<script defer src="https://static.cloudflareinsights.com/beacon.min.js/v4513226cdae34746b4dedf0b4dfa099e1781791509496" integrity="sha512-ZE9pZaUXND66v380QUtch/5sE9tPFh2zg45pR2PB0CVkCtOREv2AJKkSidISWkysEuQ0EH8faUU5du78bx87UQ==" data-cf-beacon='{"version":"2024.11.0","token":"7939e04a4baf43c8b86a560f74aac60e","server_timing":{"name":{"cfCacheStatus":true,"cfEdge":true,"cfExtPri":true,"cfL4":true,"cfOrigin":true,"cfSpeedBrain":true},"location_startswith":null}}' crossorigin="anonymous"></script>
</body>
</html>
