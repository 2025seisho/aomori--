<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title data-i18n="appTitle">青森県イベントカレンダー</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #F8F8F8; /* 全体の背景色を少し明るく */
            color: #333;
        }
        /* ヘッダーの色を深い青緑に変更 */
        .bg-blue-500 {
            background-color: #2C5282; /* 青森の海や深い森のイメージ */
        }
        /* 言語切り替えボタンのアクティブ/非アクティブ時の色を調整 */
        .bg-blue-700 {
            background-color: #3182CE; /* 少し明るい青 */
        }
        .hover\:bg-blue-800:hover {
            background-color: #2B6CB0; /* ホバー時の青 */
        }
        .bg-gray-600 {
            background-color: #718096; /* 落ち着いたグレー */
        }
        .hover\:bg-gray-700:hover {
            background-color: #4A5568; /* ホバー時の濃いグレー */
        }

        /* カレンダーナビゲーションボタンのスタイル */
        .nav-button {
            background-color: #4CAF50; /* 緑色 */
            color: white;
            padding: 8px 16px;
            border-radius: 8px;
            font-weight: 600;
            transition: background-color 0.3s ease;
            cursor: pointer;
            display: inline-flex;
            align-items: center;
            justify-content: center;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }
        .nav-button:hover {
            background-color: #45a049;
        }
        .nav-button:disabled {
            background-color: #cccccc;
            cursor: not-allowed;
        }

        /* カレンダーグリッドのスタイル */
        .calendar-grid {
            display: grid;
            grid-template-columns: repeat(7, 1fr); /* 7列 */
            gap: 4px; /* セル間の隙間 */
            text-align: center;
        }
        .calendar-day-header {
            font-weight: 700;
            padding: 8px 0;
            background-color: #E2E8F0; /* 曜日ヘッダーの背景色 */
            border-radius: 4px;
        }
        .calendar-cell {
            padding: 10px 0;
            border-radius: 8px;
            background-color: #F7FAFC; /* 通常の日の背景色 */
            cursor: pointer;
            transition: background-color 0.2s ease;
            position: relative;
            min-height: 70px; /* セルの最小高さを確保（複数行対応のため） */
            display: flex;
            flex-direction: column;
            justify-content: flex-start;
            align-items: center;
            font-size: 0.9rem;
        }
        .calendar-cell:hover {
            background-color: #EBF8FF; /* ホバー時の背景色 */
        }
        .calendar-cell.other-month {
            color: #A0AEC0; /* 他の月の日は薄く表示 */
            background-color: #EDF2F7;
        }

        /* 土日祝日のスタイル */
        .calendar-cell.weekend-holiday {
            background-color: #FFEBEE; /* 薄い赤 */
            border: 1px solid #FFCDD2;
        }
        .calendar-cell.weekend-holiday:hover {
            background-color: #FFCDD2;
        }

        /* イベントがある日の一般的なスタイル（ボーダーのみ残す） */
        .calendar-cell.has-event {
            font-weight: 600;
            border: 2px solid; /* 各イベントタイプで色を指定 */
        }
        /* イベントタイプ別の背景色とボーダー色 */
        /* 祭り系（デフォルト） */
        .calendar-cell.has-event.event-type-festival {
            background-color: #FFDDC1; /* 薄いオレンジ */
            border-color: #FF8C00; /* 濃いオレンジ */
        }
        .calendar-cell.has-event.event-type-festival:hover {
            background-color: #FFC09F;
        }
        /* 自然・季節イベント */
        .calendar-cell.has-event.event-type-nature {
            background-color: #D4EDDA; /* 薄い緑 */
            border-color: #28A745; /* 濃い緑 */
        }
        .calendar-cell.has-event.event-type-nature:hover {
            background-color: #C3E6CB;
        }
        /* 体験系イベント */
        .calendar-cell.has-event.event-type-experience {
            background-color: #FFF3CD; /* 薄い黄色 */
            border-color: #FFC107; /* 濃い黄色 */
        }
        .calendar-cell.has-event.event-type-experience:hover {
            background-color: #FFE79B;
        }

        /* 特定のイベントの色を上書き */
        .calendar-cell.has-event.event-hirosakiSakura {
            background-color: #FCE4EC; /* 薄いピンク */
            border-color: #E91E63; /* 濃いピンク */
        }
        .calendar-cell.has-event.event-hirosakiSakura:hover {
            background-color: #F8BBD0;
        }

        .calendar-cell.has-event.event-hachinoheSansha {
            background-color: #E3F2FD; /* 薄い青 */
            border-color: #2196F3; /* 濃い青 */
        }
        .calendar-cell.has-event.event-hachinoheSansha:hover {
            background-color: #BBDEFB;
        }

        .calendar-cell.has-event.event-nebuta {
            background-color: #FFEBEE; /* 薄い赤 */
            border-color: #F44336; /* 濃い赤 */
        }
        .calendar-cell.has-event.event-nebuta:hover {
            background-color: #FFCDD2;
        }

        /* 新しいねぷた祭りの色 */
        .calendar-cell.has-event.event-kuroishiNeputa {
            background-color: #E6E6FA; /* 薄いラベンダー */
            border-color: #8A2BE2; /* 紫 */
        }
        .calendar-cell.has-event.event-kuroishiNeputa:hover {
            background-color: #D8BFD8;
        }

        .calendar-cell.has-event.event-hirosakiNeputa {
            background-color: #FFE0B2; /* 薄いオレンジ */
            border-color: #FF9800; /* オレンジ */
        }
        .calendar-cell.has-event.event-hirosakiNeputa:hover {
            background-color: #FFCC80;
        }

        .calendar-cell.has-event.event-hirakawaNeputa {
            background-color: #B2EBF2; /* 薄いシアン */
            border-color: #00BCD4; /* シアン */
        }
        .calendar-cell.has-event.event-hirakawaNeputa:hover {
            background-color: #80DEEA;
        }

        /* 複数のイベントがある日のスタイル（明るい水色に変更） */
        .calendar-cell.has-multiple-events {
            background-color: #ADD8E6; /* LightBlue */
            border-color: #4682B4; /* SteelBlue */
        }
        .calendar-cell.has-multiple-events:hover {
            background-color: #87CEEB; /* SkyBlue */
        }
        .calendar-cell.has-multiple-events .event-indicator {
            color: #424242; /* 複数のイベントのテキスト色 */
        }

        .calendar-cell .day-number {
            font-size: 1.1rem;
            font-weight: 700;
            margin-bottom: 4px;
        }
        .calendar-cell .event-indicator {
            font-size: 0.75rem;
            color: #424242; /* イベント名のテキスト色を濃い灰色に */
            margin-top: 2px;
            line-height: 1.2;
        }

        /* ポップアップ（モーダル）のスタイル */
        .modal {
            display: none; /* 初期状態では非表示 */
            position: fixed;
            z-index: 100; /* 最前面に表示 */
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            overflow: auto;
            background-color: rgba(0,0,0,0.6); /* 半透明の背景 */
            justify-content: center;
            align-items: center;
        }
        .modal-content {
            background-color: #fefefe;
            margin: auto;
            padding: 20px;
            border-radius: 12px;
            width: 90%;
            max-width: 600px;
            box-shadow: 0 5px 15px rgba(0,0,0,0.3);
            position: relative;
            animation: fadeIn 0.3s;
        }
        .close-button {
            color: #aaa;
            float: right;
            font-size: 28px;
            font-weight: bold;
            cursor: pointer;
            position: absolute;
            top: 10px;
            right: 20px;
        }
        .close-button:hover,
        .close-button:focus {
            color: black;
            text-decoration: none;
            cursor: pointer;
        }
        .modal-event-card {
            background-color: #EBF8FF; /* モーダル内のイベントカード背景 */
            border-radius: 8px;
            padding: 15px;
            margin-bottom: 10px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }
        .modal-event-card img {
            width: 100%;
            height: 150px;
            object-fit: cover;
            border-radius: 6px;
            margin-bottom: 10px;
        }
        .modal-event-card h4 {
            font-size: 1.1rem;
            font-weight: 700;
            color: #2C5282;
            margin-bottom: 5px;
        }
        .modal-event-card p {
            font-size: 0.85rem;
            color: #555;
        }
        .modal-event-card .date-range {
            font-weight: 600;
            color: #4CAF50;
            margin-bottom: 5px;
            display: block;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(-20px); }
            to { opacity: 1; transform: translateY(0); }
        }
    </style>
</head>
<body>
    <header class="bg-blue-500 text-white py-4 text-center rounded-b-md shadow-md">
        <h1 class="text-2xl font-semibold" data-i18n="headerTitle">青森県イベントカレンダー</h1>
        <p class="text-lg" data-i18n="headerSubtitle">青森の年間イベント</p>
        <div class="mt-2">
            <button id="lang-ja" class="bg-blue-700 hover:bg-blue-800 text-white font-bold py-1 px-3 rounded-md mr-2">日本語</button>
            <button id="lang-en" class="bg-gray-600 hover:bg-gray-700 text-white font-bold py-1 px-3 rounded-md">English</button>
        </div>
    </header>

    <main class="container mx-auto mt-6 p-4">
        <section class="mb-8 bg-white p-6 rounded-lg shadow-md">
            <h2 class="text-xl font-semibold text-gray-800 mb-4 border-l-4 border-green-500 pl-4 py-2 rounded-md" data-i18n="calendarSectionTitle">イベントカレンダー</h2>
            <div class="flex justify-between items-center mb-6">
                <button id="prev-month" class="nav-button">
                    <span class="mr-2">❮</span> <span data-i18n="prevMonth">前月</span>
                </button>
                <h3 id="current-month-year" class="text-2xl font-bold text-gray-800"></h3>
                <button id="next-month" class="nav-button">
                    <span data-i18n="nextMonth">次月</span> <span class="ml-2">❯</span>
                </button>
            </div>
            <div class="text-center mb-4">
                <button id="today-button" class="nav-button" data-i18n="todayButton">今日に戻る</button>
            </div>

            <div class="calendar-grid">
                <div class="calendar-day-header" data-i18n="sunday">日</div>
                <div class="calendar-day-header" data-i18n="monday">月</div>
                <div class="calendar-day-header" data-i18n="tuesday">火</div>
                <div class="calendar-day-header" data-i18n="wednesday">水</div>
                <div class="calendar-day-header" data-i18n="thursday">木</div>
                <div class="calendar-day-header" data-i18n="friday">金</div>
                <div class="calendar-day-header" data-i18n="saturday">土</div>
                <!-- 日付セルがここに動的に挿入されます -->
            </div>
            <p id="no-events-message" class="text-center text-gray-500 mt-8 hidden" data-i18n="noEvents">この月にはイベントがありません。</p>
        </section>
    </main>

    <footer class="bg-gray-200 text-gray-600 py-4 text-center mt-8 rounded-t-md shadow-md">
        <p data-i18n="footerText">© 2024 青森県イベント情報</p>
    </footer>

    <!-- イベント詳細ポップアップ -->
    <div id="event-modal" class="modal">
        <div class="modal-content">
            <span class="close-button" id="close-modal-button">&times;</span>
            <h3 class="text-xl font-bold mb-4" data-i18n="eventsOnDate">日付のイベント</h3>
            <div id="modal-events-list">
                <!-- 選択された日のイベントがここに動的に挿入されます -->
            </div>
            <p id="modal-no-events" class="text-center text-gray-500 hidden" data-i18n="noEventsForDay">この日にはイベントがありません。</p>
        </div>
    </div>

    <script>
        // 多言語対応のための翻訳データ
        const translations = {
            ja: {
                appTitle: '青森県イベントカレンダー',
                headerTitle: '青森県イベントカレンダー',
                headerSubtitle: '青森の年間イベント',
                calendarSectionTitle: 'イベントカレンダー',
                prevMonth: '前月',
                nextMonth: '次月',
                todayButton: '今日に戻る',
                sunday: '日', monday: '月', tuesday: '火', wednesday: '水', thursday: '木', friday: '金', saturday: '土',
                noEvents: 'この月にはイベントがありません。',
                eventsOnDate: '{{date}}のイベント',
                noEventsForDay: 'この日にはイベントがありません。',
                multipleEventsIndicator: '複数のイベント 🎉', // このキーは直接表示には使われなくなりましたが、残しておきます
                footerText: '© 2024 青森県イベント情報',
                months: [
                    '1月', '2月', '3月', '4月', '5月', '6月',
                    '7月', '8月', '9月', '10月', '11月', '12月'
                ],
                eventDetails: {
                    nebuta: {
                        name: '青森ねぶた祭',
                        description: '青森の夏の風物詩、勇壮なねぶたの山車が特徴です。',
                        imageUrl: 'image_9d213a.jpg'
                    },
                    hirosakiSakura: {
                        name: '弘前さくらまつり',
                        description: '弘前公園で開催される、日本有数の桜まつりです。',
                        imageUrl: 'photo0000-8670 弘前城　仮.jpg'
                    },
                    goshogawaraTachineputa: {
                        name: '五所川原立佞武多',
                        description: '巨大な立佞武多が街を圧倒する、迫力満点の祭りです。',
                        imageUrl: 'https://placehold.co/300x200/EEE/31343C?text=Goshogawara+Tachineputa'
                    },
                    hachinoheSansha: {
                        name: '八戸三社大祭',
                        description: '豪華絢爛な山車が街を練り歩く、八戸の伝統的な祭りです。',
                        imageUrl: 'https://placehold.co/300x200/EEE/31343C?text=Hachinohe+Sansha'
                    },
                    towadaWinter: {
                        name: '十和田湖冬物語',
                        description: '冬の十和田湖を彩るイルミネーションとイベント。',
                        imageUrl: 'https://placehold.co/300x200/EEE/31343C?text=Lake+Towada+Winter'
                    },
                    tsugaruStove: {
                        name: '津軽鉄道 ストーブ列車',
                        description: 'レトロな列車で津軽の冬景色とストーブ料理を楽しめます。',
                        imageUrl: 'https://placehold.co/300x200/EEE/31343C?text=Tsugaru+Stove+Train'
                    },
                    kuroishiNeputa: {
                        name: '黒石ねぷた祭り',
                        description: '扇ねぷたが中心の、情緒あるねぷた祭りです。',
                        imageUrl: 'https://placehold.co/300x200/EEE/31343C?text=Kuroishi+Neputa'
                    },
                    hirosakiNeputa: {
                        name: '弘前ねぷたまつり',
                        description: '勇壮な扇ねぷたと組ねぷたが特徴の祭りです。',
                        imageUrl: 'https://placehold.co/300x200/EEE/31343C?text=Hirosaki+Neputa'
                    },
                    hirakawaNeputa: {
                        name: '平川ねぷたまつり',
                        description: '世界一の扇ねぷたが運行される祭りです。',
                        imageUrl: 'https://placehold.co/300x200/EEE/31343C?text=Hirakawa+Neputa'
                    },
                    aomoriWinterIllumination: {
                        name: '青森冬のイルミネーション',
                        description: '冬の青森市を彩る美しい光のイベントです。',
                        imageUrl: 'https://placehold.co/300x200/EEE/31343C?text=Aomori+Illumination'
                    },
                    ajisaiMatsuri: {
                        name: 'あじさい祭り',
                        description: '色とりどりのあじさいが咲き誇る美しい祭りです。',
                        imageUrl: 'https://placehold.co/300x200/EEE/31343C?text=Ajisai+Festival'
                    },
                    aomoriAppleHarvest: {
                        name: '青森りんご収穫祭',
                        description: '青森の旬のりんごを収穫し、味わえるイベントです。',
                        imageUrl: 'https://placehold.co/300x200/EEE/31343C?text=Apple+Harvest'
                    },
                    tsugaruMaguroMatsuri: {
                        name: '津軽海峡マグロ祭り',
                        description: '津軽海峡の新鮮なマグロを堪能できる食のイベントです。',
                        imageUrl: 'https://placehold.co/300x200/EEE/31343C?text=Maguro+Festival'
                    }
                }
            },
            en: {
                appTitle: 'Aomori Event Calendar',
                headerTitle: 'Aomori Event Calendar',
                headerSubtitle: 'Annual Events in Aomori',
                calendarSectionTitle: 'Event Calendar',
                prevMonth: 'Prev Month',
                nextMonth: 'Next Month',
                todayButton: 'Back to Today',
                sunday: 'Sun', monday: 'Mon', tuesday: 'Tue', wednesday: 'Wed', thursday: 'Thu', friday: 'Fri', saturday: 'Sat',
                noEvents: 'No events for this month.',
                eventsOnDate: 'Events on {{date}}',
                noEventsForDay: 'No events for this day.',
                multipleEventsIndicator: 'Multiple Events 🎉',
                footerText: '© 2024 Aomori Event Information',
                months: [
                    'January', 'February', 'March', 'April', 'May', 'June',
                    'July', 'August', 'September', 'October', 'November', 'December'
                ],
                eventDetails: {
                    nebuta: {
                        name: 'Aomori Nebuta Festival',
                        description: 'Aomori\'s summer tradition, featuring magnificent Nebuta floats.',
                        imageUrl: 'image_9d213a.jpg'
                    },
                    hirosakiSakura: {
                        name: 'Hirosaki Cherry Blossom Festival',
                        description: 'One of Japan\'s most famous cherry blossom festivals held at Hirosaki Park.',
                        imageUrl: 'photo0000-8670 弘前城　仮.jpg'
                    },
                    goshogawaraTachineputa: {
                        name: 'Goshogawara Tachineputa',
                        description: 'A powerful festival where giant Tachineputa floats dominate the streets.',
                        imageUrl: 'https://placehold.co/300x200/EEE/31343C?text=Goshogawara+Tachineputa'
                    },
                    hachinoheSansha: {
                        name: 'Hachinohe Sansha Taisai',
                        description: 'Hachinohe\'s traditional festival featuring gorgeous floats parading through the city.',
                        imageUrl: 'https://placehold.co/300x200/EEE/31343C?text=Hachinohe+Sansha'
                    },
                    towadaWinter: {
                        name: 'Lake Towada Winter Story',
                        description: 'Illuminations and events coloring Lake Towada in winter.',
                        imageUrl: 'https://placehold.co/300x200/EEE/31343C?text=Lake+Towada+Winter'
                    },
                    tsugaruStove: {
                        name: 'Tsugaru Railway Stove Train',
                        description: 'Enjoy Tsugaru\'s winter scenery and stove-cooked meals on a retro train.',
                        imageUrl: 'https://placehold.co/300x200/EEE/31343C?text=Tsugaru+Stove+Train'
                    },
                    kuroishiNeputa: {
                        name: 'Kuroishi Neputa Festival',
                        description: 'A charming Neputa festival centered around fan-shaped floats.',
                        imageUrl: 'https://placehold.co/300x200/EEE/31343C?text=Kuroishi+Neputa'
                    },
                    hirosakiNeputa: {
                        name: 'Hirosaki Neputa Festival',
                        description: 'A powerful festival featuring majestic fan-shaped and group Neputa floats.',
                        imageUrl: 'https://placehold.co/300x200/EEE/31343C?text=Hirosaki+Neputa'
                    },
                    hirakawaNeputa: {
                        name: 'Hirakawa Neputa Festival',
                        description: 'A festival where the world\'s largest fan-shaped Neputa floats are paraded.',
                        imageUrl: 'https://placehold.co/300x200/EEE/31343C?text=Hirakawa+Neputa'
                    },
                    aomoriWinterIllumination: {
                        name: 'Aomori Winter Illumination',
                        description: 'A beautiful light event that illuminates Aomori City in winter.',
                        imageUrl: 'https://placehold.co/300x200/EEE/31343C?text=Aomori+Illumination'
                    },
                    ajisaiMatsuri: {
                        name: 'Hydrangea Festival',
                        description: 'A beautiful festival where colorful hydrangeas bloom.',
                        imageUrl: 'https://placehold.co/300x200/EEE/31343C?text=Ajisai+Festival'
                    },
                    aomoriAppleHarvest: {
                        name: 'Aomori Apple Harvest Festival',
                        description: 'An event where you can harvest and taste seasonal Aomori apples.',
                        imageUrl: 'https://placehold.co/300x200/EEE/31343C?text=Apple+Harvest'
                    },
                    tsugaruMaguroMatsuri: {
                        name: 'Tsugaru Strait Tuna Festival',
                        description: 'A food event where you can enjoy fresh tuna from the Tsugaru Strait.',
                        imageUrl: 'https://placehold.co/300x200/EEE/31343C?text=Maguro+Festival'
                    }
                }
            }
        };

        let currentLang = 'ja'; // 現在の言語を保持
        let displayDate = new Date(); // 表示中の月と年を管理するDateオブジェクト

        // イベントデータ
        const events = [
            // 2025年のイベント
            { id: 'hachinoheSansha', startDate: new Date(2025, 6, 31), endDate: new Date(2025, 7, 4), name: '八戸三社大祭', type: 'festival' }, // 月は0-11
            { id: 'nebuta', startDate: new Date(2025, 7, 2), endDate: new Date(2025, 7, 7), name: '青森ねぶた祭', type: 'festival' },
            { id: 'goshogawaraTachineputa', startDate: new Date(2025, 7, 4), endDate: new Date(2025, 7, 8), name: '五所川原立佞武多', type: 'festival' },
            { id: 'hirosakiSakura', startDate: new Date(2025, 3, 20), endDate: new Date(2025, 4, 5), name: '弘前さくらまつり', type: 'nature' }, // 4月下旬-5月上旬を仮設定
            { id: 'towadaWinter', startDate: new Date(2025, 1, 1), endDate: new Date(2025, 1, 28), name: '十和田湖冬物語', type: 'nature' }, // 2月上旬-下旬を仮設定
            { id: 'tsugaruStove', startDate: new Date(2025, 11, 1), endDate: new Date(2026, 2, 31), name: '津軽鉄道 ストーブ列車', type: 'experience' }, // 12月-3月を仮設定
            { id: 'kuroishiNeputa', startDate: new Date(2025, 6, 30), endDate: new Date(2025, 7, 5), name: '黒石ねぷた祭り', type: 'festival' }, // 新規追加
            { id: 'hirosakiNeputa', startDate: new Date(2025, 7, 1), endDate: new Date(2025, 7, 7), name: '弘前ねぷたまつり', type: 'festival' }, // 新規追加
            { id: 'hirakawaNeputa', startDate: new Date(2025, 7, 2), endDate: new Date(2025, 7, 3), name: '平川ねぷたまつり', type: 'festival' }, // 新規追加
            { id: 'aomoriWinterIllumination', startDate: new Date(2025, 0, 15), endDate: new Date(2025, 0, 25), name: '青森冬のイルミネーション', type: 'nature' }, // 新規追加 (イベントがない月用)
            { id: 'ajisaiMatsuri', startDate: new Date(2025, 5, 15), endDate: new Date(2025, 5, 30), name: 'あじさい祭り', type: 'nature' }, // 6月追加
            { id: 'aomoriAppleHarvest', startDate: new Date(2025, 8, 15), endDate: new Date(2025, 8, 30), name: '青森りんご収穫祭', type: 'experience' }, // 9月追加
            { id: 'tsugaruMaguroMatsuri', startDate: new Date(2025, 9, 20), endDate: new Date(2025, 9, 25), name: '津軽海峡マグロ祭り', type: 'festival' }, // 10月追加


            // 2026年のイベント（おおまかな予定）
            { id: 'hirosakiSakura', startDate: new Date(2026, 3, 20), endDate: new Date(2026, 4, 5), name: '弘前さくらまつり', type: 'nature' },
            { id: 'hachinoheSansha', startDate: new Date(2026, 6, 31), endDate: new Date(2026, 7, 4), name: '八戸三社大祭', type: 'festival' },
            { id: 'nebuta', startDate: new Date(2026, 7, 2), endDate: new Date(2026, 7, 7), name: '青森ねぶた祭', type: 'festival' },
            { id: 'goshogawaraTachineputa', startDate: new Date(2026, 7, 4), endDate: new Date(2026, 7, 8), name: '五所川原立佞武多', type: 'festival' },
            { id: 'towadaWinter', startDate: new Date(2026, 1, 1), endDate: new Date(2026, 1, 28), name: '十和田湖冬物語', type: 'nature' },
            { id: 'tsugaruStove', startDate: new Date(2026, 11, 1), endDate: new Date(2027, 2, 31), name: '津軽鉄道 ストーブ列車', type: 'experience' },
            { id: 'kuroishiNeputa', startDate: new Date(2026, 6, 30), endDate: new Date(2026, 7, 5), name: '黒石ねぷた祭り', type: 'festival' }, // 新規追加
            { id: 'hirosakiNeputa', startDate: new Date(2026, 7, 1), endDate: new Date(2026, 7, 7), name: '弘前ねぷたまつり', type: 'festival' }, // 新規追加
            { id: 'hirakawaNeputa', startDate: new Date(2026, 7, 2), endDate: new Date(2026, 7, 3), name: '平川ねぷたまつり', type: 'festival' }, // 新規追加
            { id: 'aomoriWinterIllumination', startDate: new Date(2026, 0, 15), endDate: new Date(2026, 0, 25), name: '青森冬のイルミネーション', type: 'nature' }, // 新規追加 (イベントがない月用)
            { id: 'ajisaiMatsuri', startDate: new Date(2026, 5, 15), endDate: new Date(2026, 5, 30), name: 'あじさい祭り', type: 'nature' }, // 6月追加
            { id: 'aomoriAppleHarvest', startDate: new Date(2026, 8, 15), endDate: new Date(2026, 8, 30), name: '青森りんご収穫祭', type: 'experience' }, // 9月追加
            { id: 'tsugaruMaguroMatsuri', startDate: new Date(2026, 9, 20), endDate: new Date(2026, 9, 25), name: '津軽海峡マグロ祭り', type: 'festival' }, // 10月追加
        ];

        // 言語を切り替える関数
        function setLanguage(lang) {
            currentLang = lang;
            document.querySelectorAll('[data-i18n]').forEach(element => {
                const key = element.getAttribute('data-i18n');
                // currentMonthYear はrenderCalendarで更新するためスキップ
                if (key === 'currentMonthYear') return;

                // テキストコンテンツを更新
                if (translations[lang][key]) {
                    element.textContent = translations[lang][key];
                } else if (translations[lang].eventDetails && translations[lang].eventDetails[key]) {
                    // イベント詳細の翻訳
                    // ここは直接要素に設定しないため、スキップ
                }
            });
            // タイトルタグの更新
            document.title = translations[lang].appTitle;

            // ボタンのアクティブ状態を更新
            document.getElementById('lang-ja').classList.remove('bg-blue-700', 'hover:bg-blue-800');
            document.getElementById('lang-ja').classList.add('bg-gray-600', 'hover:bg-gray-700');
            document.getElementById('lang-en').classList.remove('bg-blue-700', 'hover:bg-blue-800');
            document.getElementById('lang-en').classList.add('bg-gray-600', 'hover:bg-gray-700');

            if (lang === 'ja') {
                document.getElementById('lang-ja').classList.remove('bg-gray-600', 'hover:bg-gray-700');
                document.getElementById('lang-ja').classList.add('bg-blue-700', 'hover:bg-blue-800');
            } else {
                document.getElementById('lang-en').classList.remove('bg-gray-600', 'hover:bg-gray-700');
                document.getElementById('lang-en').classList.add('bg-blue-700', 'hover:bg-blue-800');
            }

            // カレンダーを再描画して、イベント詳細の翻訳を適用
            renderCalendar();
        }

        // カレンダーをレンダリングする関数
        function renderCalendar() {
            const year = displayDate.getFullYear();
            const month = displayDate.getMonth(); // 0-11
            const currentMonthYearElement = document.getElementById('current-month-year');
            currentMonthYearElement.textContent = `${year}年 ${translations[currentLang].months[month]}`;

            const calendarGrid = document.querySelector('.calendar-grid');
            // 曜日ヘッダー以外の既存のセルをクリア
            calendarGrid.querySelectorAll('.calendar-cell').forEach(cell => cell.remove());

            // その月の最初の日
            const firstDayOfMonth = new Date(year, month, 1);
            // その月の最終日
            const lastDayOfMonth = new Date(year, month + 1, 0);
            // カレンダーの最初のセルとなる日付 (日曜日から開始)
            const startDay = new Date(firstDayOfMonth);
            startDay.setDate(firstDayOfMonth.getDate() - firstDayOfMonth.getDay());

            // カレンダーの最終セルとなる日付 (土曜日で終了)
            const endDay = new Date(lastDayOfMonth);
            endDay.setDate(lastDayOfMonth.getDate() + (6 - lastDayOfMonth.getDay()));

            let currentDate = new Date(startDay);

            while (currentDate <= endDay) {
                const cell = document.createElement('div');
                cell.className = 'calendar-cell';
                const dayNumber = currentDate.getDate();
                cell.innerHTML = `<span class="day-number">${dayNumber}</span>`;

                // 他の月の日のスタイル
                if (currentDate.getMonth() !== month) {
                    cell.classList.add('other-month');
                }

                // 土日祝日のスタイル（イベントがある場合は適用しない）
                const dayOfWeek = currentDate.getDay(); // 0: 日曜日, 6: 土曜日
                if ((dayOfWeek === 0 || dayOfWeek === 6) && currentDate.getMonth() === month) {
                    // その日にイベントがない場合のみ、週末/祝日スタイルを適用
                    const hasEventToday = events.some(event => {
                        const eventStartDate = new Date(event.startDate.getFullYear(), event.startDate.getMonth(), event.startDate.getDate());
                        const eventEndDate = new Date(event.endDate.getFullYear(), event.endDate.getMonth(), event.endDate.getDate());
                        const currentDayOnly = new Date(currentDate.getFullYear(), currentDate.getMonth(), currentDate.getDate());
                        return currentDayOnly >= eventStartDate && currentDayOnly <= eventEndDate;
                    });
                    if (!hasEventToday) {
                        cell.classList.add('weekend-holiday');
                    }
                }


                // イベントがある日の強調
                const eventsOnThisDay = events.filter(event => {
                    const eventStartDate = new Date(event.startDate.getFullYear(), event.startDate.getMonth(), event.startDate.getDate());
                    const eventEndDate = new Date(event.endDate.getFullYear(), event.endDate.getMonth(), event.endDate.getDate());
                    const currentDayOnly = new Date(currentDate.getFullYear(), currentDate.getMonth(), currentDate.getDate());
                    return currentDayOnly >= eventStartDate && currentDayOnly <= eventEndDate;
                });

                if (eventsOnThisDay.length > 0) {
                    cell.classList.add('has-event');

                    const eventIndicator = document.createElement('div');
                    eventIndicator.className = 'event-indicator';

                    if (eventsOnThisDay.length === 1) {
                        // イベントが1つの場合
                        const event = eventsOnThisDay[0];
                        const eventId = event.id;
                        const eventType = event.type;

                        if (eventType) {
                            cell.classList.add(`event-type-${eventType}`);
                        }
                        // 特定のイベントIDに基づいてクラスを追加（上書き用）
                        if (eventId === 'hirosakiSakura') {
                            cell.classList.add('event-hirosakiSakura');
                        } else if (eventId === 'hachinoheSansha') {
                            cell.classList.add('event-hachinoheSansha');
                        } else if (eventId === 'nebuta') {
                            cell.classList.add('event-nebuta');
                        } else if (eventId === 'kuroishiNeputa') {
                            cell.classList.add('event-kuroishiNeputa');
                        } else if (eventId === 'hirosakiNeputa') {
                            cell.classList.add('event-hirosakiNeputa');
                        } else if (eventId === 'hirakawaNeputa') {
                            cell.classList.add('event-hirakawaNeputa');
                        }
                        eventIndicator.textContent = translations[currentLang].eventDetails[eventId].name;

                    } else if (eventsOnThisDay.length === 2) {
                        // イベントが2つの場合
                        cell.classList.add('has-multiple-events'); // 複数のイベントがある日のスタイルを適用
                        eventIndicator.innerHTML = `
                            ${translations[currentLang].eventDetails[eventsOnThisDay[0].id].name}<br>
                            ${translations[currentLang].eventDetails[eventsOnThisDay[1].id].name}
                        `;
                    } else { // eventsOnThisDay.length >= 3
                        // イベントが3つ以上の場合
                        cell.classList.add('has-multiple-events'); // 複数のイベントがある日のスタイルを適用
                        eventIndicator.innerHTML = `
                            ${translations[currentLang].eventDetails[eventsOnThisDay[0].id].name}<br>
                            ${translations[currentLang].eventDetails[eventsOnThisDay[1].id].name}<br>
                            <span style="font-size: 1.5em; line-height: 0.5;">.</span><br>
                            <span style="font-size: 1.5em; line-height: 0.5;">.</span><br>
                            <span style="font-size: 1.5em; line-height: 0.5;">.</span>
                        `;
                    }
                    cell.appendChild(eventIndicator);

                    // イベント詳細ポップアップのトリガー
                    cell.addEventListener('click', () => {
                        showEventModal(currentDate, eventsOnThisDay);
                    });
                } else {
                    // イベントがない日もクリック可能にする（モーダルは空）
                    cell.addEventListener('click', () => {
                        showEventModal(currentDate, []);
                    });
                }

                calendarGrid.appendChild(cell);
                currentDate.setDate(currentDate.getDate() + 1);
            }
        }

        // イベント詳細モーダルを表示する関数
        function showEventModal(date, eventsForDay) {
            const modal = document.getElementById('event-modal');
            const modalTitle = modal.querySelector('h3');
            const modalEventsList = document.getElementById('modal-events-list');
            const modalNoEvents = document.getElementById('modal-no-events');

            const formattedDate = `${date.getFullYear()}年${translations[currentLang].months[date.getMonth()]}${date.getDate()}日`;
            modalTitle.textContent = translations[currentLang].eventsOnDate.replace('{{date}}', formattedDate);
            modalEventsList.innerHTML = ''; // リストをクリア

            if (eventsForDay.length > 0) {
                modalNoEvents.classList.add('hidden');
                eventsForDay.forEach(event => {
                    const eventDetail = translations[currentLang].eventDetails[event.id];
                    const eventCard = document.createElement('div');
                    eventCard.className = 'modal-event-card';
                    eventCard.innerHTML = `
                        <img src="${eventDetail.imageUrl}" alt="${eventDetail.name}" onerror="this.onerror=null;this.src='https://placehold.co/300x200/EEE/31343C?text=No+Image';">
                        <span class="date-range">${event.startDate.getMonth()+1}/${event.startDate.getDate()} - ${event.endDate.getMonth()+1}/${event.endDate.getDate()}</span>
                        <h4>${eventDetail.name}</h4>
                        <p>${eventDetail.description}</p>
                    `;
                    modalEventsList.appendChild(eventCard);
                });
            } else {
                modalNoEvents.classList.remove('hidden');
            }

            modal.style.display = 'flex'; // モーダルを表示
        }

        // イベント詳細モーダルを閉じる関数
        function closeEventModal() {
            document.getElementById('event-modal').style.display = 'none';
        }

        // DOMContentLoadedイベントリスナー
        document.addEventListener('DOMContentLoaded', () => {
            // デフォルト言語を設定
            setLanguage(currentLang);

            // 言語切り替えボタンのイベントリスナー
            document.getElementById('lang-ja').addEventListener('click', () => setLanguage('ja'));
            document.getElementById('lang-en').addEventListener('click', () => setLanguage('en'));

            // 前月へボタン
            document.getElementById('prev-month').addEventListener('click', () => {
                displayDate.setMonth(displayDate.getMonth() - 1);
                renderCalendar();
            });

            // 次月へボタン
            document.getElementById('next-month').addEventListener('click', () => {
                displayDate.setMonth(displayDate.getMonth() + 1);
                renderCalendar();
            });

            // 今日に戻るボタン
            document.getElementById('today-button').addEventListener('click', () => {
                displayDate = new Date(); // 現在の月にリセット
                renderCalendar();
            });

            // モーダルを閉じるボタン
            document.getElementById('close-modal-button').addEventListener('click', closeEventModal);

            // モーダルの外側をクリックして閉じる
            window.addEventListener('click', (event) => {
                const modal = document.getElementById('event-modal');
                if (event.target === modal) {
                    closeEventModal();
                }
            });

            // 初期表示
            renderCalendar();
        });
    </script>
</body>
</html>
