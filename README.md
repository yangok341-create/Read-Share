[index.html](https://github.com/user-attachments/files/26454081/index.html)
<!DOCTYPE html>
<html lang="zh-Hant">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>閱讀共享紀錄系統 - 雙頁切換版</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/wordcloud2.js/1.2.2/wordcloud2.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@400;700&display=swap');
        body { font-family: 'Noto Sans TC', sans-serif; background-color: #f1f5f9; }
        .glass-card { background: white; border-radius: 2rem; box-shadow: 0 10px 30px rgba(0,0,0,0.05); }
        .page-node { display: none; }
        .page-node.active { display: block; animation: fadeIn 0.3s ease; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        
        /* 標籤式勾選框 */
        .check-tag { cursor: pointer; transition: all 0.2s; border: 2px solid #f1f5f9; border-radius: 999px; padding: 6px 16px; font-size: 13px; font-weight: bold; color: #94a3b8; display: inline-block; background: #f8fafc; }
        input[type="checkbox"]:checked + .check-tag { background-color: #2d5a27; color: white; border-color: #2d5a27; }
        
        /* 分類標籤 */
        .cat-tag { cursor: pointer; padding: 8px 16px; border-radius: 12px; font-size: 14px; font-weight: bold; background: white; color: #64748b; border: 1px solid #e2e8f0; }
        .cat-tag.active { background: #2d5a27; color: white; border-color: #2d5a27; }
        
        #cloud-container { width: 100%; height: 600px; background: white; border-radius: 2rem; border: 1px solid #f1f5f9; }
        #modal { display: none; position: fixed; top:0; left:0; width:100%; height:100%; background: rgba(15, 23, 42, 0.95); z-index: 1000; justify-content: center; align-items: center; padding: 20px; }
        textarea, input[type="text"] { border: 1px solid #e2e8f0; border-radius: 1rem; padding: 12px; width: 100%; }
    </style>
</head>
<body class="p-4 md:p-8">

    <nav class="fixed top-6 right-6 z-50 flex gap-2">
        <button onclick="switchPage('form')" class="bg-white border shadow-md px-6 py-2 rounded-full font-bold text-slate-600 hover:bg-slate-50 active:scale-95 transition">✍️ 填寫/列表</button>
        <button onclick="switchPage('cloud')" class="bg-white border shadow-md px-6 py-2 rounded-full font-bold text-slate-600 hover:bg-slate-50 active:scale-95 transition">🌈 視覺文字雲</button>
    </nav>

    <main class="max-w-6xl mx-auto">
        
        <section id="page-form" class="page-node active space-y-8">
            <div class="glass-card p-6 md:p-10">
                <h2 class="text-2xl font-bold text-slate-800 mb-6 flex items-center gap-2">📗 閱讀共享紀錄撰寫</h2>
                
                <div class="space-y-4 mb-8 bg-slate-50 p-6 rounded-[2rem] border border-slate-100">
                    <div class="flex flex-col md:flex-row md:items-center gap-4">
                        <span class="text-xs font-black text-slate-400 w-20">社區議題</span>
                        <div id="cat-group-1" class="flex flex-wrap gap-2"></div>
                    </div>
                    <div class="flex flex-col md:flex-row md:items-center gap-4 border-t border-slate-200 pt-4">
                        <span class="text-xs font-black text-slate-400 w-20">其他議題</span>
                        <div id="cat-group-2" class="flex flex-wrap gap-2"></div>
                    </div>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-2 gap-8">
                    <div class="space-y-5">
                        <div>
                            <label class="block text-sm font-bold text-slate-600 mb-1">繪本名稱</label>
                            <input type="text" id="title" placeholder="請輸入繪本或書籍名稱">
                        </div>
                        <div>
                            <label class="block text-sm font-bold text-slate-600 mb-1 font-bold text-green-700">📌 文字雲關鍵字 (筆記核心)</label>
                            <input type="text" id="kw" placeholder="如：踏查 種植 貢獻 (空格隔開)">
                        </div>
                        <div>
                            <label class="block text-sm font-bold text-slate-600 mb-2">使用方式 (可多選)</label>
                            <div class="flex flex-wrap gap-2">
                                <label><input type="checkbox" name="useType" value="團體共讀" class="hidden"><span class="check-tag">團體共讀</span></label>
                                <label><input type="checkbox" name="useType" value="學習區鷹架" class="hidden"><span class="check-tag">學習區鷹架</span></label>
                                <label><input type="checkbox" name="useType" value="自由閱讀" class="hidden"><span class="check-tag">自由閱讀</span></label>
                                <label><input type="checkbox" name="useType" value="延伸活動" class="hidden"><span class="check-tag">延伸活動</span></label>
                            </div>
                        </div>
                    </div>
                    <div class="space-y-4">
                        <div class="grid grid-cols-2 gap-4">
                            <div class="bg-amber-50 p-3 rounded-2xl border border-amber-100">
                                <label class="block text-xs font-bold text-amber-700 mb-1">📙 繪本討論關鍵字</label>
                                <input type="text" id="discuss" class="bg-white border-amber-200" placeholder="角色 情節 衝突 結局">
                            </div>
                            <div class="bg-sky-50 p-3 rounded-2xl border border-sky-100">
                                <label class="block text-xs font-bold text-sky-700 mb-1">🏗️ 鷹架引導關鍵字</label>
                                <input type="text" id="scaffold" class="bg-white border-sky-200" placeholder="提問 示範">
                            </div>
                        </div>
                        <div>
                            <label class="block text-sm font-bold text-slate-600 mb-1">詳細內容說明 (活動小紀實)</label>
                            <textarea id="others" rows="3" placeholder="在此輸入完整的觀察過程..."></textarea>
                        </div>
                        <div>
                            <label class="block text-sm font-bold text-slate-600 mb-1">📸 上傳照片</label>
                            <input type="file" id="photo" accept="image/*" class="text-xs w-full">
                        </div>
                    </div>
                </div>
                <button id="save-btn" onclick="uploadData()" class="w-full bg-[#2d5a27] text-white font-bold py-4 rounded-2xl shadow-xl mt-8 text-lg hover:opacity-90 active:scale-95 transition">確認儲存並同步至雲端</button>
            </div>

            <div class="glass-card p-6 md:p-10">
                <h3 class="text-xl font-bold text-slate-700 mb-6">📂 歷史紀錄列表 (<span id="list-cat-label">公園</span>)</h3>
                <div id="record-list" class="space-y-4">
                    </div>
            </div>
        </section>

        <section id="page-cloud" class="page-node space-y-8">
            <div class="glass-card p-6 md:p-10">
                <div class="flex flex-col md:flex-row justify-between items-center mb-8 gap-4">
                    <div>
                        <h2 class="text-3xl font-bold text-slate-800">🌈 共享視覺雲：<span id="cloud-cat-label" class="text-[#2d5a27]">公園</span></h2>
                    </div>
                    <div class="flex items-center gap-3 bg-slate-100 p-2 rounded-2xl">
                        <label class="text-xs font-bold text-slate-500 px-2">切換觀點</label>
                        <select id="queryMode" onchange="refreshUI()" class="bg-white rounded-xl px-4 py-2 font-bold text-slate-700 text-sm outline-none">
                            <option value="kw">核心關鍵字</option>
                            <option value="discuss">繪本討論</option>
                            <option value="scaffold">鷹架引導</option>
                        </select>
                    </div>
                </div>
                <div id="cloud-container"><canvas id="cloud-canvas" class="cursor-pointer"></canvas></div>
            </div>
        </section>
    </main>

    <div id="modal" onclick="closeModal()">
        <div class="bg-white w-full max-w-2xl p-8 rounded-[2.5rem] max-h-[85vh] overflow-y-auto relative shadow-2xl" onclick="event.stopPropagation()">
            <button onclick="closeModal()" class="absolute top-6 right-8 text-slate-300 hover:text-slate-600 text-4xl font-bold">&times;</button>
            <div id="m-body" class="space-y-6"></div>
        </div>
    </div>

    <script>
        const API_URL = "https://script.google.com/macros/s/AKfycbzkbWySR4X0vf25J7ZWNvgEdpLtUzpMQHlnRHRy2zvHOt4opftZmsTqADPOxkQkFLysVw/exec";
        const group1 = ["公園", "大樹花草", "鳥類", "好朋友", "社區", "爺爺奶奶", "其他"];
        const group2 = ["生活自理", "性別平等", "其他"];
        let currentCat = "公園", db = [];

        function init() {
            const b1 = document.getElementById('cat-group-1'), b2 = document.getElementById('cat-group-2');
            [...group1, ...group2].forEach(cat => {
                const b = document.createElement('div');
                b.className = `cat-tag ${cat === currentCat ? 'active' : ''}`;
                b.innerText = cat;
                b.onclick = (e) => {
                    currentCat = cat;
                    document.querySelectorAll('.cat-tag').forEach(tag => tag.classList.remove('active'));
                    e.target.classList.add('active');
                    document.getElementById('cloud-cat-label').innerText = cat;
                    document.getElementById('list-cat-label').innerText = cat;
                    refreshUI();
                    renderList();
                };
                (group1.includes(cat) ? b1 : b2).appendChild(b);
            });
            fetchData();
        }

        async function fetchData() {
            try { const res = await fetch(API_URL); db = await res.json(); refreshUI(); renderList(); } catch(e) { console.error(e); }
        }

        function switchPage(page) {
            document.querySelectorAll('.page-node').forEach(p => p.classList.remove('active'));
            document.getElementById('page-' + page).classList.add('active');
            if(page === 'cloud') setTimeout(refreshUI, 100); // 延遲讓畫布正確計算寬度
        }

        function refreshUI() {
            const mode = document.getElementById('queryMode').value;
            const filtered = db.filter(r => String(r[0]).trim() === currentCat);
            const freq = {};
            let col = mode === "kw" ? 2 : (mode === "discuss" ? 6 : 7);
            let color = mode === "discuss" ? '#d97706' : (mode === "scaffold" ? '#0284c7' : '#2d5a27');

            filtered.forEach(r => {
                const words = String(r[col] || "").split(/[\s,，、]+/).filter(w => w.length > 0);
                words.forEach(w => { freq[w] = (freq[w] || 0) + 1; });
            });

            const cvs = document.getElementById('cloud-canvas'), cnt = document.getElementById('cloud-container');
            cvs.width = cnt.offsetWidth; cvs.height = 600;
            WordCloud(cvs, {
                list: Object.entries(freq).map(([w, c]) => [w, Math.min(c * 15 + 30, 100)]),
                click: (item) => showDetails(item[0]),
                fontFamily: 'Noto Sans TC', color: color, rotateRatio: 0, backgroundColor: '#ffffff', gridSize: 10, weightFactor: 1
            });
        }

        function renderList() {
            const list = document.getElementById('record-list');
            const filtered = db.filter(r => String(r[0]).trim() === currentCat);
            list.innerHTML = filtered.length ? "" : "<p class='text-slate-400 italic'>此類別尚無紀錄</p>";
            filtered.reverse().forEach(r => {
                const item = document.createElement('div');
                item.className = "bg-white p-5 rounded-2xl border border-slate-100 shadow-sm flex flex-col md:flex-row gap-4 items-start";
                item.innerHTML = `
                    <div class="flex-grow">
                        <div class="flex justify-between items-center mb-2">
                            <h4 class="font-bold text-[#2d5a27]">📖 ${r[1]}</h4>
                            <span class="text-[10px] bg-slate-50 px-2 py-1 rounded text-slate-400 font-bold">${r[5]}</span>
                        </div>
                        <p class="text-sm text-slate-600 mb-1">🧒 孩子說：${r[3] || '無'}</p>
                        <p class="text-sm text-slate-500 line-clamp-2 italic">${r[8] || '無詳細說明'}</p>
                    </div>
                    ${r[9] && r[9].startsWith('data:image') ? `<img src="${r[9]}" class="w-24 h-24 object-cover rounded-xl shadow-inner border">` : ''}
                `;
                item.onclick = () => showDetailsForOne(r);
                list.appendChild(item);
            });
        }

        function showDetailsForOne(r) {
            const body = document.getElementById('m-body');
            body.innerHTML = `
                <div class="py-4">
                    <div class="flex justify-between items-center mb-6 border-b pb-4">
                        <h2 class="text-2xl font-bold text-[#2d5a27]">📖 ${r[1]}</h2>
                        <span class="bg-slate-100 p-2 rounded-xl text-xs font-black">${r[5]}</span>
                    </div>
                    <div class="grid grid-cols-1 gap-6 text-sm">
                        <div class="bg-slate-50 p-4 rounded-2xl"><b>🧒 孩子的話：</b><br>${r[3] || '未記錄'}</div>
                        <div class="bg-slate-50 p-4 rounded-2xl"><b>✍️ 我想說：</b><br>${r[4] || '未記錄'}</div>
                        <div class="bg-green-50 p-4 rounded-2xl border border-green-100"><b>📝 詳細內容說明：</b><br>${r[8] || '無'}</div>
                    </div>
                    ${r[9] && r[9].startsWith('data:image') ? `<img src="${r[9]}" class="w-full rounded-[2rem] shadow-xl border-4 border-white mt-6">` : ''}
                </div>`;
            document.getElementById('modal').style.display = 'flex';
        }

        function showDetails(kw) {
            const mode = document.getElementById('queryMode').value;
            let col = mode === "kw" ? 2 : (mode === "discuss" ? 6 : 7);
            const matched = db.filter(r => String(r[0]).trim() === currentCat && String(r[col]).includes(kw));
            const body = document.getElementById('m-body');
            body.innerHTML = `<h2 class="text-3xl font-bold text-slate-800 border-b pb-4">關鍵字：${kw}</h2>`;
            matched.forEach(r => {
                body.innerHTML += `
                    <div class="py-8 border-b last:border-none">
                        <div class="flex justify-between items-center mb-6"><h4 class="font-bold text-xl text-[#2d5a27]">📖 ${r[1]}</h4><span class="text-xs bg-slate-100 p-2 rounded-xl font-black">${r[5]}</span></div>
                        <div class="grid grid-cols-1 gap-4 text-sm">
                            <div class="bg-slate-50 p-4 rounded-2xl"><b>🧒 孩子的話：</b>${r[3]}</div>
                            <div class="bg-green-50 p-4 rounded-2xl border border-green-100"><b>📝 詳細說明：</b><br>${r[8] || '無'}</div>
                        </div>
                        ${r[9] && r[9].startsWith('data:image') ? `<img src="${r[9]}" class="w-full rounded-[2rem] shadow-xl border-4 border-white mt-4">` : ''}
                    </div>`;
            });
            document.getElementById('modal').style.display = 'flex';
        }

        async function uploadData() {
            const btn = document.getElementById('save-btn'), file = document.getElementById('photo').files[0];
            if(!document.getElementById('title').value || !document.getElementById('kw').value) return alert("必填喔！");
            btn.disabled = true; btn.innerText = "同步中...";
            const data = {
                cat: currentCat, title: document.getElementById('title').value, kw: document.getElementById('kw').value,
                child: document.getElementById('child').value, feel: document.getElementById('feel').value,
                useType: Array.from(document.querySelectorAll('input[name="useType"]:checked')).map(el => el.value).join(", "),
                discuss: document.getElementById('discuss').value, scaffold: document.getElementById('scaffold').value,
                others: document.getElementById('others').value, img: file ? await toBase64(file) : ""
            };
            try { await fetch(API_URL, { method: 'POST', mode: 'no-cors', body: JSON.stringify(data) }); alert("成功！"); location.reload(); } catch(e) { btn.disabled = false; }
        }

        const toBase64 = f => new Promise(res => { const rd = new FileReader(); rd.readAsDataURL(f); rd.onload = () => res(rd.result); });
        function closeModal() { document.getElementById('modal').style.display = 'none'; }
        window.onload = init;
    </script>
</body>
</html>
