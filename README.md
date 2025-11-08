<!DOCTYPE html>
<html lang="uk">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Список Покупок</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            -webkit-tap-highlight-color: transparent;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            background-color: var(--tg-theme-bg-color, #ffffff);
            color: var(--tg-theme-text-color, #000000);
            padding: 12px;
            padding-bottom: 80px;
            overflow-x: hidden;
        }

        .header {
            position: sticky;
            top: 0;
            background-color: var(--tg-theme-bg-color, #ffffff);
            padding: 16px 0;
            margin-bottom: 16px;
            border-bottom: 1px solid var(--tg-theme-hint-color, #e0e0e0);
            z-index: 100;
        }

        .header-content {
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .header h1 {
            font-size: 24px;
            font-weight: 600;
            color: var(--tg-theme-text-color, #000000);
        }

        .header-info {
            font-size: 14px;
            color: var(--tg-theme-hint-color, #999999);
            margin-top: 4px;
        }

        .progress {
            background-color: var(--tg-theme-secondary-bg-color, #f0f0f0);
            height: 6px;
            border-radius: 3px;
            overflow: hidden;
            margin-top: 12px;
        }

        .progress-bar {
            height: 100%;
            background-color: var(--tg-theme-button-color, #3390ec);
            transition: width 0.3s ease;
            border-radius: 3px;
        }

        .stats {
            display: flex;
            justify-content: space-between;
            font-size: 12px;
            margin-top: 8px;
            color: var(--tg-theme-hint-color, #999999);
        }

        .category {
            margin-bottom: 24px;
            background-color: var(--tg-theme-secondary-bg-color, #f8f9fa);
            border-radius: 12px;
            padding: 16px;
        }

        .category-header {
            display: flex;
            align-items: center;
            margin-bottom: 12px;
            cursor: pointer;
            user-select: none;
        }

        .category-icon {
            font-size: 24px;
            margin-right: 12px;
        }

        .category-title {
            font-size: 18px;
            font-weight: 600;
            color: var(--tg-theme-text-color, #000000);
            flex-grow: 1;
        }

        .category-count {
            font-size: 14px;
            color: var(--tg-theme-hint-color, #999999);
            background-color: var(--tg-theme-bg-color, #ffffff);
            padding: 4px 10px;
            border-radius: 12px;
            margin-right: 8px;
        }

        .category-toggle {
            font-size: 18px;
            color: var(--tg-theme-hint-color, #999999);
            transition: transform 0.3s ease;
        }

        .category.collapsed .category-toggle {
            transform: rotate(-90deg);
        }

        .items {
            display: grid;
            gap: 8px;
        }

        .category.collapsed .items {
            display: none;
        }

        .item {
            background-color: var(--tg-theme-bg-color, #ffffff);
            padding: 14px;
            border-radius: 10px;
            display: flex;
            align-items: center;
            cursor: pointer;
            user-select: none;
            transition: all 0.2s ease;
            border: 2px solid transparent;
        }

        .item:active {
            transform: scale(0.98);
            background-color: var(--tg-theme-secondary-bg-color, #f0f0f0);
        }

        .item.checked {
            opacity: 0.5;
            border-color: var(--tg-theme-button-color, #3390ec);
        }

        .checkbox {
            width: 24px;
            height: 24px;
            border: 2px solid var(--tg-theme-hint-color, #999999);
            border-radius: 6px;
            margin-right: 12px;
            display: flex;
            align-items: center;
            justify-content: center;
            flex-shrink: 0;
            transition: all 0.2s ease;
        }

        .item.checked .checkbox {
            background-color: var(--tg-theme-button-color, #3390ec);
            border-color: var(--tg-theme-button-color, #3390ec);
        }

        .checkbox::after {
            content: '✓';
            color: white;
            font-size: 16px;
            font-weight: bold;
            opacity: 0;
            transform: scale(0);
            transition: all 0.2s ease;
        }

        .item.checked .checkbox::after {
            opacity: 1;
            transform: scale(1);
        }

        .item-content {
            flex-grow: 1;
        }

        .item-name {
            font-size: 16px;
            font-weight: 500;
            color: var(--tg-theme-text-color, #000000);
            margin-bottom: 2px;
        }

        .item.checked .item-name {
            text-decoration: line-through;
        }

        .item-amount {
            font-size: 13px;
            color: var(--tg-theme-hint-color, #999999);
        }

        .bottom-buttons {
            position: fixed;
            bottom: 0;
            left: 0;
            right: 0;
            background-color: var(--tg-theme-bg-color, #ffffff);
            padding: 12px;
            border-top: 1px solid var(--tg-theme-hint-color, #e0e0e0);
            display: flex;
            gap: 8px;
        }

        .btn {
            flex: 1;
            padding: 14px;
            border-radius: 10px;
            border: none;
            font-size: 15px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.2s ease;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 6px;
        }

        .btn:active {
            transform: scale(0.96);
        }

        .btn-primary {
            background-color: var(--tg-theme-button-color, #3390ec);
            color: var(--tg-theme-button-text-color, #ffffff);
        }

        .btn-secondary {
            background-color: var(--tg-theme-secondary-bg-color, #f0f0f0);
            color: var(--tg-theme-text-color, #000000);
        }

        .filter-buttons {
            display: flex;
            gap: 8px;
            margin-bottom: 16px;
            overflow-x: auto;
            padding-bottom: 4px;
        }

        .filter-btn {
            padding: 8px 16px;
            border-radius: 20px;
            border: 2px solid var(--tg-theme-hint-color, #e0e0e0);
            background-color: var(--tg-theme-bg-color, #ffffff);
            color: var(--tg-theme-text-color, #000000);
            font-size: 14px;
            cursor: pointer;
            white-space: nowrap;
            transition: all 0.2s ease;
        }

        .filter-btn.active {
            background-color: var(--tg-theme-button-color, #3390ec);
            color: var(--tg-theme-button-text-color, #ffffff);
            border-color: var(--tg-theme-button-color, #3390ec);
        }

        .filter-btn:active {
            transform: scale(0.95);
        }

        .empty-state {
            text-align: center;
            padding: 40px 20px;
            color: var(--tg-theme-hint-color, #999999);
        }

        .empty-state-icon {
            font-size: 64px;
            margin-bottom: 16px;
        }

        .empty-state-text {
            font-size: 16px;
        }
    </style>
</head>
<body>
    <div class="header">
        <div class="header-content">
            <div>
                <h1>🛒 Список Покупок</h1>
                <div class="header-info">Меню для Олі</div>
            </div>
        </div>
        <div class="progress">
            <div class="progress-bar" id="progressBar"></div>
        </div>
        <div class="stats">
            <span id="checkedCount">0 з 0</span>
            <span id="percentage">0%</span>
        </div>
    </div>

    <div class="filter-buttons">
        <button class="filter-btn active" data-filter="all">Усі</button>
        <button class="filter-btn" data-filter="unchecked">Не куплені</button>
        <button class="filter-btn" data-filter="checked">Куплені</button>
    </div>

    <div id="categories">
        <!-- Categories will be generated here -->
    </div>

    <div class="bottom-buttons">
        <button class="btn btn-secondary" id="clearBtn">🗑️ Очистити</button>
        <button class="btn btn-primary" id="shareBtn">📤 Поділитися</button>
    </div>

    <script>
        // Telegram WebApp initialization
        const tg = window.Telegram.WebApp;
        tg.expand();
        tg.enableClosingConfirmation();

        // Shopping list data
        const shoppingData = [
            {
                icon: '🥩',
                title: 'М\'ясо, Птиця та Субпродукти',
                items: [
                    { name: 'Телятина', amount: '200 г' },
                    { name: 'Куряче філе / Індичка', amount: '390 г' },
                    { name: 'Курячі серця', amount: '300 г' },
                    { name: 'Куряча печінка', amount: '120 г' },
                    { name: 'М\'ясо для борщу', amount: '400 г' }
                ]
            },
            {
                icon: '🐟',
                title: 'Риба та Морепродукти',
                items: [
                    { name: 'Лосось малосолений', amount: '250 г' },
                    { name: 'Сібас', amount: '300 г' },
                    { name: 'Хек', amount: '200 г' },
                    { name: 'Креветки очищені', amount: '120 г' },
                    { name: 'Тунець консервований', amount: '1 банка' },
                    { name: 'Дорадо', amount: '1 філе' }
                ]
            },
            {
                icon: '🥛',
                title: 'Молочні продукти, Сири та Яйця',
                items: [
                    { name: 'Грецький йогурт 3%', amount: '800 г' },
                    { name: 'Кисломолочний сир 1,5%', amount: '450 г' },
                    { name: 'Зернистий творог 4%', amount: '150 г' },
                    { name: 'Вершки 20%', amount: '25 мл' },
                    { name: 'Вершкове масло', amount: '15 г' },
                    { name: 'Крем-сир', amount: '30 г' },
                    { name: 'Камамбер / Пармезан', amount: '50/30 г' },
                    { name: 'Моцарела', amount: '75 г' },
                    { name: 'Твердий сир', amount: '40 г' },
                    { name: 'Яйця', amount: '3 шт.' }
                ]
            },
            {
                icon: '🌾',
                title: 'Крупи та Хлібобулочні вироби',
                items: [
                    { name: 'Цільнозерновий хліб', amount: '300 г' },
                    { name: 'Булгур', amount: '100 г' },
                    { name: 'Гречка', amount: '450 г' },
                    { name: 'Нешліфований рис', amount: '150 г' },
                    { name: 'Кіноа', amount: '100 г' },
                    { name: 'Цільнозернова тортилья', amount: '60 г' },
                    { name: 'Паста з твердих сортів', amount: '60 г' },
                    { name: 'Цільнозернові макарони', amount: '' },
                    { name: 'Рисові хлібці', amount: '5 шт.' },
                    { name: 'Круасан', amount: '80 г' }
                ]
            },
            {
                icon: '🥬',
                title: 'Овочі, Салати та Бобові',
                items: [
                    { name: 'Картопля', amount: '350 г' },
                    { name: 'Морква', amount: 'кілька штук' },
                    { name: 'Томати', amount: '' },
                    { name: 'Томати чері', amount: '' },
                    { name: 'Солодкий перець', amount: 'кілька штук' },
                    { name: 'Огірки', amount: 'кілька штук' },
                    { name: 'Капуста молода', amount: '100 г' },
                    { name: 'Шпинат', amount: '150-200 г' },
                    { name: 'Рукола', amount: '150-200 г' },
                    { name: 'Кабачок / Цукіні', amount: 'кілька штук' },
                    { name: 'Баклажан', amount: '' },
                    { name: 'Броколі', amount: '100 г' },
                    { name: 'Заморожений горошок', amount: '100 г' },
                    { name: 'Оливки', amount: '20 г' },
                    { name: 'Консервована кукурудза', amount: '50 г' },
                    { name: 'Авокадо', amount: '' },
                    { name: 'Ріпчаста цибуля', amount: '' }
                ]
            },
            {
                icon: '🍎',
                title: 'Фрукти, Ягоди та Горіхи',
                items: [
                    { name: 'Банани', amount: '3-4 шт.' },
                    { name: 'Мандарини / Ківі', amount: '2-3 шт.' },
                    { name: 'Ягоди (свіжі/заморожені)', amount: '250 г' },
                    { name: 'Яблуко', amount: '80 г' },
                    { name: 'Апельсин', amount: '1 шт.' },
                    { name: 'Груша', amount: '1 шт.' },
                    { name: 'Мигдаль', amount: '30 г' },
                    { name: 'Гарбузове насіння', amount: '30 г' },
                    { name: 'Кеш\'ю', amount: '20 г' },
                    { name: 'Горіхи', amount: '10 г' }
                ]
            },
            {
                icon: '🧂',
                title: 'Приправи, Олії та Соуси',
                items: [
                    { name: 'Оливкова олія', amount: '' },
                    { name: 'Бальзамічний оцет', amount: '' },
                    { name: 'Лимон', amount: '' },
                    { name: 'Мед', amount: '3 ч.л' },
                    { name: 'Зерниста гірчиця', amount: '2 ч.л' },
                    { name: 'Хумус', amount: '' },
                    { name: 'Соєвий соус', amount: '1 ч.л' },
                    { name: 'Соус песто', amount: '' },
                    { name: 'Свіжа зелень', amount: 'петрушка, кріп, базилік' },
                    { name: 'Спеції', amount: 'сіль, перець, лавровий лист' }
                ]
            },
            {
                icon: '🍫',
                title: 'Снеки',
                items: [
                    { name: 'Протеїновий батончик', amount: '3 шт. × 50 г' },
                    { name: 'Шоколад 80%', amount: '2 шт. × 30 г' },
                    { name: 'Батончик 200 ккал', amount: '50 г' },
                    { name: 'Снікерс маленький', amount: '40 г' }
                ]
            }
        ];

        let currentFilter = 'all';
        let checkedItems = new Set();

        // Load saved state
        function loadState() {
            const saved = localStorage.getItem('shoppingList');
            if (saved) {
                checkedItems = new Set(JSON.parse(saved));
            }
        }

        // Save state
        function saveState() {
            localStorage.setItem('shoppingList', JSON.stringify([...checkedItems]));
        }

        // Render categories
        function renderCategories() {
            const container = document.getElementById('categories');
            container.innerHTML = '';

            shoppingData.forEach((category, catIndex) => {
                const categoryDiv = document.createElement('div');
                categoryDiv.className = 'category';
                
                let checkedInCategory = 0;
                const totalInCategory = category.items.length;
                
                category.items.forEach((item, itemIndex) => {
                    if (checkedItems.has(`${catIndex}-${itemIndex}`)) {
                        checkedInCategory++;
                    }
                });

                categoryDiv.innerHTML = `
                    <div class="category-header" onclick="toggleCategory(${catIndex})">
                        <span class="category-icon">${category.icon}</span>
                        <span class="category-title">${category.title}</span>
                        <span class="category-count">${checkedInCategory}/${totalInCategory}</span>
                        <span class="category-toggle">▼</span>
                    </div>
                    <div class="items" id="items-${catIndex}"></div>
                `;

                container.appendChild(categoryDiv);

                // Render items
                const itemsContainer = categoryDiv.querySelector('.items');
                category.items.forEach((item, itemIndex) => {
                    const itemId = `${catIndex}-${itemIndex}`;
                    const isChecked = checkedItems.has(itemId);

                    // Filter logic
                    if (currentFilter === 'checked' && !isChecked) return;
                    if (currentFilter === 'unchecked' && isChecked) return;

                    const itemDiv = document.createElement('div');
                    itemDiv.className = `item ${isChecked ? 'checked' : ''}`;
                    itemDiv.onclick = () => toggleItem(catIndex, itemIndex);

                    itemDiv.innerHTML = `
                        <div class="checkbox"></div>
                        <div class="item-content">
                            <div class="item-name">${item.name}</div>
                            ${item.amount ? `<div class="item-amount">${item.amount}</div>` : ''}
                        </div>
                    `;

                    itemsContainer.appendChild(itemDiv);
                });

                // Hide category if all items filtered out
                if (itemsContainer.children.length === 0) {
                    categoryDiv.style.display = 'none';
                }
            });

            updateProgress();
        }

        // Toggle item
        function toggleItem(catIndex, itemIndex) {
            const itemId = `${catIndex}-${itemIndex}`;
            
            if (checkedItems.has(itemId)) {
                checkedItems.delete(itemId);
                tg.HapticFeedback.impactOccurred('light');
            } else {
                checkedItems.add(itemId);
                tg.HapticFeedback.impactOccurred('medium');
            }

            saveState();
            renderCategories();
        }

        // Toggle category collapse
        function toggleCategory(catIndex) {
            const category = document.querySelectorAll('.category')[catIndex];
            category.classList.toggle('collapsed');
            tg.HapticFeedback.impactOccurred('soft');
        }

        // Update progress
        function updateProgress() {
            const total = shoppingData.reduce((sum, cat) => sum + cat.items.length, 0);
            const checked = checkedItems.size;
            const percentage = total > 0 ? Math.round((checked / total) * 100) : 0;

            document.getElementById('progressBar').style.width = `${percentage}%`;
            document.getElementById('checkedCount').textContent = `${checked} з ${total}`;
            document.getElementById('percentage').textContent = `${percentage}%`;

            // Update Telegram main button
            if (checked === total && total > 0) {
                tg.MainButton.setText('🎉 Усе куплено!');
                tg.MainButton.show();
            } else {
                tg.MainButton.hide();
            }
        }

        // Filter buttons
        document.querySelectorAll('.filter-btn').forEach(btn => {
            btn.addEventListener('click', function() {
                document.querySelectorAll('.filter-btn').forEach(b => b.classList.remove('active'));
                this.classList.add('active');
                currentFilter = this.dataset.filter;
                tg.HapticFeedback.impactOccurred('soft');
                renderCategories();
            });
        });

        // Clear button
        document.getElementById('clearBtn').addEventListener('click', () => {
            tg.showConfirm('Очистити всі відмітки?', (confirmed) => {
                if (confirmed) {
                    checkedItems.clear();
                    saveState();
                    renderCategories();
                    tg.HapticFeedback.notificationOccurred('success');
                }
            });
        });

        // Share button
        document.getElementById('shareBtn').addEventListener('click', () => {
            const unchecked = [];
            shoppingData.forEach((category, catIndex) => {
                category.items.forEach((item, itemIndex) => {
                    if (!checkedItems.has(`${catIndex}-${itemIndex}`)) {
                        unchecked.push(`${category.icon} ${item.name}${item.amount ? ' (' + item.amount + ')' : ''}`);
                    }
                });
            });

            const message = unchecked.length > 0 
                ? `🛒 Список покупок:\n\n${unchecked.join('\n')}`
                : '✅ Усі продукти куплені!';

            if (tg.initDataUnsafe.user) {
                tg.showAlert('Функція "Поділитися" буде доступна після публікації боту');
            } else {
                navigator.clipboard.writeText(message).then(() => {
                    tg.showAlert('Список скопійовано!');
                });
            }
        });

        // Initialize
        loadState();
        renderCategories();
    </script>
</body>
</html>
