<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>績效獎金戰情室 - 專業版</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        .card { @apply bg-white p-6 rounded-xl shadow-md border border-gray-100; }
        .input-group { @apply mb-4; }
        .input-label { @apply block text-sm font-medium text-gray-700 mb-1; }
        .input-field { @apply w-full p-2 border border-gray-300 rounded-md focus:ring-2 focus:ring-blue-500 focus:border-blue-500 outline-none transition-all; }
        .table-input { @apply w-full p-1 border border-transparent focus:border-blue-400 bg-transparent rounded transition-all hover:bg-gray-50; }
        .table-header { @apply p-3 text-sm font-semibold text-gray-600 bg-gray-50; }
        .bg-level { @apply bg-indigo-50 text-indigo-700; }
    </style>
</head>
<body class="bg-slate-50 min-h-screen pb-20">

    <!-- Header -->
    <header class="bg-blue-900 text-white py-8 shadow-xl mb-10 text-center relative overflow-hidden">
        <div class="relative z-10">
            <h1 class="text-4xl font-extrabold tracking-tight">績效獎金戰情室</h1>
            <p class="text-blue-200 mt-2 text-lg">數據驅動分配 · 權重連動管理</p>
        </div>
        <div class="absolute top-0 left-0 w-full h-full opacity-10 pointer-events-none">
            <svg viewBox="0 0 100 100" preserveAspectRatio="none" class="w-full h-full"><path d="M0 100 C 20 0 50 0 100 100 Z" fill="white"></path></svg>
        </div>
    </header>

    <main class="max-w-7xl mx-auto px-4 space-y-8">
        
        <!-- Top Summary Cards -->
        <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
            <div class="card bg-indigo-600 text-white">
                <p class="text-indigo-100 text-sm mb-1 uppercase tracking-wider font-semibold">總獎金水庫預算</p>
                <h3 id="totalPool" class="text-4xl font-black">$ 0</h3>
            </div>
            <div class="card border-l-4 border-blue-500">
                <p class="text-gray-500 text-sm mb-1">績效績勵水庫 (由 kW 產生)</p>
                <h3 id="performancePool" class="text-3xl font-bold text-blue-700">$ 0</h3>
            </div>
            <div class="card border-l-4 border-green-500">
                <p class="text-gray-500 text-sm mb-1">人均總獎金估計</p>
                <h3 id="avgBonusPerPerson" class="text-3xl font-bold text-green-600">$ 0</h3>
            </div>
        </div>

        <div class="grid grid-cols-1 lg:grid-cols-3 gap-8">
            
            <!-- Left: Base Parameters & kW -->
            <div class="space-y-6">
                <section class="card">
                    <h2 class="text-xl font-bold mb-5 flex items-center text-gray-800">
                        <span class="mr-2 text-blue-600">⚙️</span> 基礎參數
                    </h2>
                    <div class="space-y-4">
                        <div class="input-group">
                            <label class="input-label">總公司員工人數</label>
                            <input type="number" id="staffCount" class="input-field" value="48" oninput="calculate()">
                        </div>
                        <div class="input-group">
                            <label class="input-label">平均月薪成本 (TWD)</label>
                            <input type="number" id="avgSalary" class="input-field" value="40000" oninput="calculate()">
                        </div>
                        <div class="input-group">
                            <label class="input-label">平均出勤率 (%)</label>
                            <input type="number" id="attendanceRate" class="input-field" value="95" min="0" max="100" oninput="calculate()">
                        </div>
                    </div>
                </section>

                <section class="card bg-blue-50 border-blue-100">
                    <h2 class="text-xl font-bold mb-5 flex items-center text-blue-800">
                        <span class="mr-2">⚡</span> 業務換算 (kW)
                    </h2>
                    <div class="space-y-4">
                        <div>
                            <label class="input-label">新場建置 (kW)</label>
                            <input type="number" id="newBuildKw" class="input-field" value="5000" oninput="calculate()">
                            <div class="text-right text-xs text-blue-600 mt-1" id="newBuildBonusDisp">$0</div>
                        </div>
                        <div>
                            <label class="input-label">掛表完成 (kW)</label>
                            <input type="number" id="meterKw" class="input-field" value="3000" oninput="calculate()">
                            <div class="text-right text-xs text-blue-600 mt-1" id="meterBonusDisp">$0</div>
                        </div>
                        <div>
                            <label class="input-label">電費收入 (kW)</label>
                            <input type="number" id="incomeKw" class="input-field" value="10000" oninput="calculate()">
                            <div class="text-right text-xs text-blue-600 mt-1" id="incomeBonusDisp">$0</div>
                        </div>
                    </div>
                </section>
            </div>

            <!-- Right: Tables (9 Levels & Depts) -->
            <div class="lg:col-span-2 space-y-8">
                
                <!-- 9-Level Weight Setup -->
                <section class="card overflow-hidden">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-xl font-bold flex items-center text-gray-800">
                            <span class="mr-2 text-indigo-600">🎯</span> 績效等級權重 (9級)
                        </h2>
                        <span class="text-xs text-gray-400">連動績效水庫分配</span>
                    </div>
                    <div class="overflow-x-auto">
                        <table class="w-full text-left">
                            <thead>
                                <tr>
                                    <th class="table-header">等級</th>
                                    <th class="table-header">名稱</th>
                                    <th class="table-header w-24">權重倍數</th>
                                    <th class="table-header w-24">建議人數%</th>
                                    <th class="table-header text-right">估計個人獎金</th>
                                </tr>
                            </thead>
                            <tbody id="levelTableBody"></tbody>
                            <tfoot>
                                <tr class="bg-gray-50 font-bold border-t text-sm">
                                    <td class="p-3" colspan="3">合計占比</td>
                                    <td class="p-3 text-center" id="totalLevelPct">100%</td>
                                    <td class="p-3 text-right text-gray-400">*根據全公司預算推算</td>
                                </tr>
                            </tfoot>
                        </table>
                    </div>
                </section>

                <!-- Department Distribution -->
                <section class="card">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-xl font-bold flex items-center text-gray-800">
                            <span class="mr-2 text-orange-600">🏢</span> 各部門貢獻分配
                        </h2>
                    </div>
                    <div class="overflow-x-auto">
                        <table class="w-full text-left border-collapse">
                            <thead>
                                <tr>
                                    <th class="table-header">部門名稱 (可編輯)</th>
                                    <th class="table-header w-24 text-center">人數</th>
                                    <th class="table-header w-32 text-center">貢獻比例%</th>
                                    <th class="table-header text-right">分配金額</th>
                                    <th class="table-header text-right">人均績效額</th>
                                </tr>
                            </thead>
                            <tbody id="deptTableBody"></tbody>
                            <tfoot>
                                <tr class="border-t bg-slate-50 font-bold">
                                    <td class="p-3">合計</td>
                                    <td class="p-3 text-center" id="totalDeptCount">0</td>
                                    <td class="p-3 text-center" id="totalDeptWeight">100%</td>
                                    <td class="p-3 text-right" id="totalDistributedAmt">$ 0</td>
                                    <td></td>
                                </tr>
                            </tfoot>
                        </table>
                    </div>
                    <div id="warningMsg" class="mt-4 p-3 bg-red-100 text-red-700 text-sm rounded-lg flex items-center hidden">
                        <span class="mr-2">⚠️</span> 數據總和不等於 100%，請確認各項比例設定。
                    </div>
                </section>

            </div>
        </div>
    </main>

    <script>
        // 初始績效 9 等級資料
        const performanceLevels = [
            { id: 1, name: 'S+', weight: 2.0, pct: 5 },
            { id: 2, name: 'S', weight: 1.7, pct: 10 },
            { id: 3, name: 'S-', weight: 1.5, pct: 10 },
            { id: 4, name: 'A+', weight: 1.3, pct: 15 },
            { id: 5, name: 'A', weight: 1.1, pct: 20 },
            { id: 6, name: 'A-', weight: 0.9, pct: 20 },
            { id: 7, name: 'B', weight: 0.7, pct: 10 },
            { id: 8, name: 'C', weight: 0.4, pct: 5 },
            { id: 9, name: 'D', weight: 0.0, pct: 5 }
        ];

        // 初始部門資料
        const departments = [
            { id: 'exec', name: '處級主管', count: 2, weight: 5 },
            { id: 'a1', name: 'A1 營運部', count: 10, weight: 25 },
            { id: 'a2', name: 'A2 工程部', count: 20, weight: 60 },
            { id: 'others', name: '後勤支援部', count: 16, weight: 10 }
        ];

        function formatCurrency(val) {
            return new Intl.NumberFormat('zh-TW', { style: 'currency', currency: 'TWD', maximumFractionDigits: 0 }).format(val);
        }

        function initTables() {
            // 初始化績效等級表格
            const levelBody = document.getElementById('levelTableBody');
            levelBody.innerHTML = '';
            performanceLevels.forEach(level => {
                const row = document.createElement('tr');
                row.className = 'border-b hover:bg-slate-50 transition-colors';
                row.innerHTML = `
                    <td class="p-3 text-sm font-bold text-indigo-600">${level.id}</td>
                    <td class="p-3"><input type="text" id="levelName_${level.id}" class="table-input" value="${level.name}"></td>
                    <td class="p-3"><input type="number" id="levelWeight_${level.id}" step="0.1" class="table-input font-bold" value="${level.weight}" oninput="calculate()"></td>
                    <td class="p-3"><input type="number" id="levelPct_${level.id}" class="table-input" value="${level.pct}" oninput="calculate()"></td>
                    <td class="p-3 text-right font-medium text-gray-700" id="levelEstBonus_${level.id}">$ 0</td>
                `;
                levelBody.appendChild(row);
            });

            // 初始化部門表格
            const deptBody = document.getElementById('deptTableBody');
            deptBody.innerHTML = '';
            departments.forEach(dept => {
                const row = document.createElement('tr');
                row.className = 'border-b hover:bg-slate-50 transition-colors';
                row.innerHTML = `
                    <td class="p-3 font-medium text-gray-800">
                        <input type="text" id="deptName_${dept.id}" class="table-input font-bold" value="${dept.name}">
                    </td>
                    <td class="p-3">
                        <input type="number" id="deptCount_${dept.id}" class="table-input text-center" value="${dept.count}" oninput="calculate()">
                    </td>
                    <td class="p-3">
                        <input type="number" id="deptWeight_${dept.id}" class="table-input text-center text-blue-600 font-bold" value="${dept.weight}" oninput="calculate()">
                    </td>
                    <td class="p-3 text-right font-semibold" id="deptAmt_${dept.id}">$ 0</td>
                    <td class="p-3 text-right text-xs text-gray-500" id="deptAvg_${dept.id}">$ 0</td>
                `;
                deptBody.appendChild(row);
            });
        }

        function calculate() {
            const totalStaff = parseFloat(document.getElementById('staffCount').value) || 0;
            const avgSalary = parseFloat(document.getElementById('avgSalary').value) || 0;
            const attRate = parseFloat(document.getElementById('attendanceRate').value) || 0;

            // 1. kW 獎金計算
            const kw1 = (parseFloat(document.getElementById('newBuildKw').value) || 0) * 10;
            const kw2 = (parseFloat(document.getElementById('meterKw').value) || 0) * 20;
            const kw3 = (parseFloat(document.getElementById('incomeKw').value) || 0) * 40;
            
            document.getElementById('newBuildBonusDisp').innerText = formatCurrency(kw1);
            document.getElementById('meterBonusDisp').innerText = formatCurrency(kw2);
            document.getElementById('incomeBonusDisp').innerText = formatCurrency(kw3);

            // 2. 總體水庫預算
            const baseAnnual = totalStaff * avgSalary; // 基本 1 個月
            const attBonus = attRate >= 70 ? (totalStaff * avgSalary * (attRate / 100)) : 0;
            const perfPool = kw1 + kw2 + kw3;
            const totalBudget = baseAnnual + attBonus + perfPool;

            document.getElementById('totalPool').innerText = formatCurrency(totalBudget);
            document.getElementById('performancePool').innerText = formatCurrency(perfPool);
            document.getElementById('avgBonusPerPerson').innerText = totalStaff > 0 ? formatCurrency(totalBudget / totalStaff) : "$ 0";

            // 3. 績效等級估算 (公司整體連動)
            // 計算權重加總 = 人數比例 * 權重
            let weightedSum = 0;
            let totalLevelPct = 0;
            performanceLevels.forEach(level => {
                const w = parseFloat(document.getElementById(`levelWeight_${level.id}`).value) || 0;
                const p = parseFloat(document.getElementById(`levelPct_${level.id}`).value) || 0;
                weightedSum += (w * (p / 100));
                totalLevelPct += p;
            });
            document.getElementById('totalLevelPct').innerText = totalLevelPct + "%";
            
            // 計算個人獎金基數
            const individualBase = weightedSum > 0 ? (perfPool / totalStaff / weightedSum) : 0;

            performanceLevels.forEach(level => {
                const w = parseFloat(document.getElementById(`levelWeight_${level.id}`).value) || 0;
                document.getElementById(`levelEstBonus_${level.id}`).innerText = formatCurrency(individualBase * w);
            });

            // 4. 部門分配計算
            let totalDeptWeight = 0;
            let currentTotalStaff = 0;
            let totalDistributed = 0;

            departments.forEach(dept => {
                const w = parseFloat(document.getElementById(`deptWeight_${dept.id}`).value) || 0;
                const c = parseFloat(document.getElementById(`deptCount_${dept.id}`).value) || 0;
                totalDeptWeight += w;
                currentTotalStaff += c;

                const deptAmt = perfPool * (w / 100);
                totalDistributed += deptAmt;
                
                document.getElementById(`deptAmt_${dept.id}`).innerText = formatCurrency(deptAmt);
                document.getElementById(`deptAvg_${dept.id}`).innerText = c > 0 ? "人均 " + formatCurrency(deptAmt / c) : "$ 0";
            });

            document.getElementById('totalDeptCount').innerText = currentTotalStaff;
            document.getElementById('totalDeptWeight').innerText = totalDeptWeight + "%";
            document.getElementById('totalDistributedAmt').innerText = formatCurrency(totalDistributed);

            // 5. 異常提示
            const warning = document.getElementById('warningMsg');
            if (Math.abs(totalDeptWeight - 100) > 0.1 || Math.abs(totalLevelPct - 100) > 0.1) {
                warning.classList.remove('hidden');
            } else {
                warning.classList.add('hidden');
            }
        }

        window.onload = () => {
            initTables();
            calculate();
        };
    </script>
</body>
</html>
