<!DOCTYPE html>
<html lang="ru">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Дневник с Будильником</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            background-image: url('background.jpg'); /* Замените на путь к вашему изображению */
            background-size: cover;
            background-position: center;
            margin: 0;
            padding: 20px;
        }

        .container {
            max-width: 600px;
            margin: auto;
            background: rgba(255, 255, 255, 0.9);
            padding: 30px;
            border-radius: 10px;
            box-shadow: 0 4px 30px rgba(0, 0, 0, 0.3);
            animation: fadeIn 0.5s;
        }

        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }

        h1 {
            text-align: center;
            color: #333;
            font-size: 2em;
            margin-bottom: 20px;
            text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.2);
        }

        .current-time {
            font-size: 1.5em;
            text-align: center;
            margin: 10px 0;
            color: #28a745;
        }

        textarea {
            width: 100%;
            height: 100px;
            margin-bottom: 10px;
            border: 2px solid #ccc;
            border-radius: 5px;
            padding: 10px;
            font-size: 1em;
            transition: border-color 0.3s, box-shadow 0.3s;
        }

        textarea:focus {
            border-color: #28a745;
            box-shadow: 0 0 5px rgba(40, 167, 69, 0.5);
        }

        button {
            width: 100%;
            padding: 10px;
            background-color: #28a745;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-size: 1.2em;
            transition: background-color 0.3s, transform 0.2s;
        }

        button:hover {
            background-color: #218838;
            transform: scale(1.05);
        }

        ul {
            list-style-type: none;
            padding: 0;
            margin-top: 20px;
        }

        li {
            padding: 10px;
            border: 1px solid #eee;
            border-radius: 5px;
            margin-bottom: 10px;
            transition: background-color 0.3s, transform 0.2s;
        }

        li:hover {
            background-color: rgba(40, 167, 69, 0.1);
            transform: scale(1.02);
        }

        .alarm-container {
            margin-top: 20px;
            padding: 20px;
            border: 2px solid #28a745;
            border-radius: 10px;
            text-align: center;
            background-color: rgba(40, 167, 69, 0.1);
        }

        .alarm-input {
            width: 100%;
            margin-bottom: 10px;
        }

        .modal {
            display: none;
            position: fixed;
            z-index: 1;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.4);
        }

        .modal-content {
            background-color: #fefefe;
            margin: 15% auto;
            padding: 20px;
            border: 1px solid #888;
            width: 80%;
            border-radius: 10px;
            text-align: center;
        }

        .close {
            color: #aaa;
            float: right;
            font-size: 28px;
            font-weight: bold;
        }

        .close:hover,
        .close:focus {
            color: black;
            text-decoration: none;
            cursor: pointer;
        }

        /* Медиа-запросы для адаптивности */
        @media (max-width: 768px) {
            h1 {
                font-size: 1.5em;
            }

            .current-time {
                font-size: 1.2em;
            }

            textarea {
                height: 80px;
            }

            button {
                font-size: 1em;
            }

            li {
                padding: 8px;
            }

            .modal-content {
                width: 90%;
            }
        }

        @media (max-width: 480px) {
            .container {
                padding: 15px;
            }

            h1 {
                font-size: 1.2em;
            }

            .current-time {
                font-size: 1em;
            }
        }
    </style>
</head>

<body>
    <div class="container">
        <h1>Мой Дневник</h1>

        <div id="currentTime" class="current-time"></div> <!-- Текущее время -->

        <textarea id="entryInput" placeholder="Введите вашу запись здесь..."></textarea>
        <button id="addEntryButton">Добавить запись</button>

        <div class="alarm-container">
            <h2>Установить будильник</h2>
            <input type="time" id="alarmTime" class="alarm-input" />
            <button id="setAlarmButton">Установить будильник</button>
        </div>

        <ul id="entriesList"></ul>

        <audio id="alarmSound" src="будильник.mp3" preload="auto"></audio>

        <!-- Модальное окно -->
        <div id="alarmModal" class="modal">
            <div class="modal-content">
                <span class="close">&times;</span>
                <p>Будильник сработал!</p>
            </div>
        </div>
    </div>

    <script>
        // Функция для обновления текущего времени
        function updateTime() {
            const now = new Date();
            const options = { hour: '2-digit', minute: '2-digit', second: '2-digit', hour12: false };
            document.getElementById('currentTime').textContent = now.toLocaleTimeString('ru-RU', options);
        }

        // Запускаем обновление времени каждую секунду
        setInterval(updateTime, 1000);
        updateTime(); // Начальное обновление

        document.getElementById('addEntryButton').addEventListener('click', function () {
            const entryInput = document.getElementById('entryInput');
            const entryContent = entryInput.value.trim();

            if (entryContent) {
                const entriesList = document.getElementById('entriesList');
                const now = new Date();
                const timestamp = now.toLocaleString('ru-RU', {
                    hour: '2-digit',
                    minute: '2-digit',
                    second: '2-digit',
                    day: '2-digit',
                    month: '2-digit',
                    year: 'numeric'
                });

                const listItem = document.createElement('li');
                listItem.textContent = `${timestamp}: ${entryContent}`; // Добавляем временную метку
                entriesList.appendChild(listItem);

                entryInput.value = ''; // Очистить поле ввода
            } else {
                alert('Пожалуйста, введите запись!');
            }
        });

        // Функция для установки будильника
        document.getElementById('setAlarmButton').addEventListener('click', function () {
            const alarmTimeInput = document.getElementById('alarmTime');
            const alarmTime = alarmTimeInput.value;

            if (!alarmTime) {
                alert('Пожалуйста, установите время будильника!');
                return;
            }

            const now = new Date();
            const alarmDate = new Date(now.getFullYear(), now.getMonth(), now.getDate(), alarmTime.split(':')[0], alarmTime.split(':')[1]);

            if (alarmDate <= now) {
                alert('Выберите время в будущем!');
                return;
            }

            const timeToAlarm = alarmDate - now;
            const alarmSound = document.getElementById('alarmSound');

            setTimeout(() => {
                alarmSound.play();
                showModal(); // Показываем модальное окно
            }, timeToAlarm);

            alert('Будильник установлен на ' + alarmTime);
        });

        // Функция для показа модального окна
        function showModal() {
            const modal = document.getElementById('alarmModal');
            modal.style.display = "block";

            // Закрытие модального окна при нажатии на "X"
            const closeBtn = document.getElementsByClassName("close")[0];
            closeBtn.onclick = function () {
                modal.style.display = "none";
            };

            // Закрытие модального окна при клике вне его
            window.onclick = function (event) {
