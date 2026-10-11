<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Портал Приложений</title>
    <style>
        :root {
            --bg: #0f172a;
            --card: #1e293b;
            --accent: #3b82f6;
            --accent-hover: #2563eb;
            --text: #f8fafc;
            --muted: #94a3b8;
            --danger: #ef4444;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
        }

        body {
            background-color: var(--bg);
            color: var(--text);
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 20px;
        }

        /* Выпадающая панель администратора сверху */
        #admin-dropdown {
            width: 100%;
            max-width: 450px;
            background: #111827;
            border: 1px solid #374151;
            border-radius: 16px;
            padding: 20px;
            margin-bottom: 20px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.5);
            display: none;
            text-align: left;
        }

        #admin-dropdown h3 {
            color: #60a5fa;
            font-size: 1.1rem;
            margin-bottom: 12px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .card {
            background-color: var(--card);
            width: 100%;
            max-width: 400px;
            padding: 30px;
            border-radius: 20px;
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.4);
            text-align: center;
        }

        .app-icon {
            width: 80px;
            height: 80px;
            background: linear-gradient(135deg, #3b82f6, #1d4ed8);
            border-radius: 20px;
            margin: 0 auto 16px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 36px;
        }

        h1 {
            font-size: 1.4rem;
            margin-bottom: 6px;
        }

        p.subtitle {
            color: var(--muted);
            font-size: 0.85rem;
            margin-bottom: 20px;
        }

        .form-group {
            margin-bottom: 14px;
            text-align: left;
        }

        label {
            display: block;
            font-size: 0.8rem;
            color: var(--muted);
            margin-bottom: 6px;
        }

        input[type="text"],
        input[type="password"] {
            width: 100%;
            padding: 12px;
            border-radius: 10px;
            border: 1px solid #334155;
            background: #0f172a;
            color: var(--text);
            font-size: 0.95rem;
            outline: none;
        }

        input:focus {
            border-color: var(--accent);
        }

        .btn {
            width: 100%;
            padding: 12px;
            border: none;
            border-radius: 10px;
            background: var(--accent);
            color: white;
            font-size: 0.95rem;
            font-weight: 600;
            cursor: pointer;
            margin-top: 8px;
            text-decoration: none;
            display: inline-block;
            transition: background 0.2s;
            text-align: center;
        }

        .btn:hover {
            background: var(--accent-hover);
        }

        .btn-outline {
            background: transparent;
            border: 1px solid #334155;
            color: var(--muted);
            margin-top: 10px;
        }

        .btn-outline:hover {
            background: #334155;
            color: var(--text);
        }

        .error-msg {
            color: var(--danger);
            font-size: 0.85rem;
            margin-top: 10px;
            display: none;
        }

        .hidden {
            display: none !important;
        }

        .upload-box {
            border: 2px dashed #334155;
            padding: 14px;
            border-radius: 10px;
            margin-bottom: 12px;
            background: #0f172a;
        }

        input[type="file"] {
            width: 100%;
            font-size: 0.85rem;
            color: var(--muted);
        }

        .file-list {
            max-height: 160px;
            overflow-y: auto;
            margin-top: 10px;
            border-top: 1px solid #374151;
            padding-top: 10px;
        }

        .file-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            background: #1f2937;
            padding: 8px 12px;
            border-radius: 8px;
            margin-bottom: 6px;
            font-size: 0.85rem;
        }

        .file-item a {
            color: #60a5fa;
            text-decoration: none;
            margin-left: 10px;
        }
        .file-item a:hover {
            text-decoration: underline;
        }
    </style>
</head>
<body>

    <!-- ВЫПАДАЮЩАЯ ПАНЕЛЬ АДМИНА СВЕРХУ -->
    <div id="admin-dropdown">
        <h3>
            <span>⚙️ Админ-панель (Ivan_Grin)</span>
            <button class="btn btn-outline" style="width: auto; padding: 4px 10px; margin: 0; font-size: 0.8rem;" onclick="logout()">Выйти</button>
        </h3>
        <div class="upload-box">
            <input type="file" id="apk-file-input" accept=".apk">
        </div>
        <button class="btn" onclick="uploadApk()">Загрузить APK (от 1 МБ до 1 ГБ)</button>
        <p id="upload-status" style="font-size: 0.8rem; color: var(--muted); margin-top: 6px;"></p>

        <div style="margin-top: 15px; font-size: 0.85rem; color: var(--muted);">Все ваши APK файлы (сохраняются на устройстве):</div>
        <div class="file-list" id="admin-file-list">
            <div style="color:var(--muted); font-size:0.8rem; padding:4px;">Загрузка списка...</div>
        </div>
    </div>

    <!-- ОСНОВНАЯ КАРТОЧКА -->
    <div class="card">
        <div class="app-icon">📦</div>
        <h1>Мое Приложение</h1>
        <p class="subtitle" id="subtitle-text">Авторизация</p>

        <!-- ФОРМА ВХОДА И РЕГИСТРАЦИИ -->
        <div id="auth-section">
            <div class="form-group">
                <label>Никнейм</label>
                <input type="text" id="username" placeholder="Ваш ник" autocomplete="off">
            </div>
            <div class="form-group">
                <label>Пароль</label>
                <input type="password" id="password" placeholder="Пароль">
            </div>
            <button class="btn" onclick="registerUser()">Зарегистрироваться</button>
            <button class="btn btn-outline" onclick="loginUser()">Войти</button>
            <div class="error-msg" id="error-msg">Ошибка</div>
        </div>

        <!-- КАБИНЕТ ПОЛЬЗОВАТЕЛЯ -->
        <div id="user-screen" class="hidden">
            <p style="color: var(--muted); font-size: 0.9rem; margin-bottom: 16px;" id="user-greeting">Добро пожаловать!</p>
            <div id="no-file-msg" style="color: var(--muted); font-size: 0.85rem; margin-bottom: 16px;">
                Приложение пока не загружено разработчиком.
            </div>
            <a id="download-link" class="btn hidden" href="#" download>Скачать APK</a>
            <button class="btn btn-outline" onclick="logout()">Выйти из профиля</button>
        </div>
    </div>

    <script>
        const MIN_SIZE = 1 * 1024 * 1024;     // 1 МБ
        const MAX_SIZE = 1024 * 1024 * 1024; // 1 ГБ

        // Настройка базы данных IndexedDB для тяжелых APK файлов
        let db;
        const request = indexedDB.open("ApkPortalDB", 1);

        request.onerror = function(event) {
            console.error("Ошибка открытия базы данных IndexedDB");
        };

        request.onupgradeneeded = function(event) {
            db = event.target.result;
            if (!db.objectStoreNames.contains("files")) {
                db.createObjectStore("files", { keyPath: "id", autoIncrement: true });
            }
        };

        request.onsuccess = function(event) {
            db = event.target.result;
            checkSavedData(); // Проверяем данные сразу после загрузки БД
        };

        function getUsers() {
            return JSON.parse(localStorage.getItem('site_users')) || [];
        }

        function saveUsers(users) {
            localStorage.setItem('site_users', JSON.stringify(users));
        }

        function registerUser() {
            const nick = document.getElementById('username').value.trim();
            const pass = document.getElementById('password').value.trim();

            if (!nick || !pass) {
                showError("Заполните ник и пароль!");
                return;
            }

            if (nick === "Ivan_Grin" && pass !== "Gmail1992") {
                showError("Неверный пароль администратора!");
                return;
            }

            let users = getUsers();
            if (users.find(u => u.username === nick)) {
                showError("Ник уже занят. Нажмите «Войти».");
                return;
            }

            users.push({ username: nick, password: pass });
            saveUsers(users);
            completeLogin(nick);
        }

        function loginUser() {
            const nick = document.getElementById('username').value.trim();
            const pass = document.getElementById('password').value.trim();

            if (!nick || !pass) {
                showError("Введите ник и пароль!");
                return;
            }

            let users = getUsers();
            let user = users.find(u => u.username === nick && u.password === pass);

            if (!user && nick === "Ivan_Grin" && pass === "Gmail1992") {
                user = { username: "Ivan_Grin", password: "Gmail1992" };
                users.push(user);
                saveUsers(users);
            }

            if (!user) {
                showError("Неверный ник или пароль!");
                return;
            }

            completeLogin(nick);
        }

        function completeLogin(nick) {
            document.getElementById('auth-section').classList.add('hidden');
            localStorage.setItem('current_user', nick);

            if (nick === "Ivan_Grin") {
                document.getElementById('admin-dropdown').style.display = 'block';
                document.getElementById('subtitle-text').innerText = "Режим администратора активен";
                document.getElementById('user-screen').classList.add('hidden');
                loadFilesFromDB();
            } else {
                document.getElementById('admin-dropdown').style.display = 'none';
                document.getElementById('user-screen').classList.remove('hidden');
                document.getElementById('subtitle-text').innerText = "Личный кабинет";
                document.getElementById('user-greeting').innerText = "Привет, " + nick + "!";
                checkSavedData();
            }
        }

        function logout() {
            localStorage.removeItem('current_user');
            document.getElementById('auth-section').classList.remove('hidden');
            document.getElementById('admin-dropdown').style.display = 'none';
            document.getElementById('user-screen').classList.add('hidden');
            document.getElementById('subtitle-text').innerText = "Авторизация";
            document.getElementById('username').value = '';
            document.getElementById('password').value = '';
            hideError();
        }

        function showError(text) {
            const err = document.getElementById('error-msg');
            err.innerText = text;
            err.style.display = 'block';
        }

        function hideError() {
            document.getElementById('error-msg').style.display = 'none';
        }

        // Загрузка APK с сохранением в IndexedDB
        function uploadApk() {
            const fileInput = document.getElementById('apk-file-input');
            const file = fileInput.files[0];
            const status = document.getElementById('upload-status');

            if (!file) {
                alert("Выберите .apk файл!");
                return;
            }

            if (file.size < MIN_SIZE) {
                alert("Ошибка: файл слишком маленький (меньше 1 МБ).");
                return;
            }

            if (file.size > MAX_SIZE) {
                alert("Ошибка: файл слишком большой (больше 1 ГБ).");
                return;
            }

            status.innerText = "Чтение файла...";

            const reader = new FileReader();
            reader.onload = function(e) {
                const base64Data = e.target.result;
                const fileRecord = {
                    name: file.name,
                    size: (file.size / (1024 * 1024)).toFixed(1) + " МБ",
                    data: base64Data,
                    date: new Date().toLocaleTimeString() + " " + new Date().toLocaleDateString()
                };

                // Сохраняем в IndexedDB (база данных браузера, не удаляется при перезагрузках)
                const transaction = db.transaction(["files"], "readwrite");
                const store = transaction.objectStore("files");
                const request = store.add(fileRecord);

                request.onsuccess = function() {
                    status.innerText = "Успешно загружено: " + file.name;
                    fileInput.value = "";
                    loadFilesFromDB();
                    alert("APK успешно сохранен в память!");
                };

                request.onerror = function() {
                    alert("Ошибка сохранения в базу данных.");
                    status.innerText = "Ошибка сохранения";
                };
            };
            reader.readAsDataURL(file);
        }

        // Получение списка файлов из IndexedDB
        function loadFilesFromDB() {
            if (!db) return;
            const transaction = db.transaction(["files"], "readonly");
            const store = transaction.objectStore("files");
            const request = store.getAll();

            request.onsuccess = function(event) {
                const files = event.target.result;
                renderAdminFiles(files);

                if (files.length > 0) {
                    // Самый последний добавленный файл делаем доступным для обычных пользователей
                    const latest = files[files.length - 1];
                    updateDownloadLink(latest.data, latest.name);
                }
            };
        }

        function renderAdminFiles(files) {
            const listContainer = document.getElementById('admin-file-list');
            if (files.length === 0) {
                listContainer.innerHTML = '<div style="color:var(--muted); font-size:0.8rem; padding:4px;">Нет загруженных файлов</div>';
                return;
            }

            listContainer.innerHTML = '';
            // Перебираем в обратном порядке, чтобы свежие были сверху
            [...files].reverse().forEach((file) => {
                let item = document.createElement('div');
                item.className = 'file-item';
                item.innerHTML = `
                    <span style="overflow:hidden; text-overflow:ellipsis; white-space:nowrap; max-width:180px;" title="${file.name}">${file.name} (${file.size})</span>
                    <div>
                        <a href="${file.data}" download="${file.name}">Скачать</a>
                    </div>
                `;
                listContainer.appendChild(item);
            });
        }

        function updateDownloadLink(dataUrl, fileName) {
            const dlBtn = document.getElementById('download-link');
            const noFileMsg = document.getElementById('no-file-msg');

            if (dataUrl && fileName) {
                dlBtn.href = dataUrl;
                dlBtn.download = fileName;
                dlBtn.classList.remove('hidden');
                dlBtn.innerText = "Скачать " + fileName;
                if (noFileMsg) noFileMsg.style.display = 'none';
            }
        }

        function checkSavedData() {
            if (!db) return;
            const transaction = db.transaction(["files"], "readonly");
            const store = transaction.objectStore("files");
            const request = store.getAll();

            request.onsuccess = function(event) {
                const files = event.target.result;
                if (files.length > 0) {
                    const latest = files[files.length - 1];
                    updateDownloadLink(latest.data, latest.name);
                }

                // Автоматический вход, если сессия сохранена
                const savedUser = localStorage.getItem('current_user');
                if (savedUser) {
                    completeLogin(savedUser);
                }
            };
        }
    </script>
</body>
</html>
