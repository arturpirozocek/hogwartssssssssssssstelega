
<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no">
<title>Хогвартс</title>
<script src="https://telegram.org/js/telegram-web-app.js"></script>
<link href="https://fonts.googleapis.com/css2?family=Cinzel:wght@400;600;700;900&family=Crimson+Text:ital,wght@0,400;0,600;0,700;1,400&family=UnifrakturMaguntia&display=swap" rel="stylesheet">
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<link href="https://fonts.googleapis.com/css2?family=Dancing+Script&display=swap" rel="stylesheet">
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js"></script>
<style>
/* =========================================
   1. ПЕРЕМЕННЫЕ И БАЗОВЫЕ НАСТРОЙКИ
========================================= */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  -webkit-tap-highlight-color: transparent;
}

:root {
  /* Темная тема / Фон */
  --bg: #080810;
  --bg2: #10101e;
  --card: #161628;
  --card2: #1e1e38;
  
  /* Золото */
  --gold: #c9a84c;
  --gold2: #9a7830;
  --gold3: #e8d5a3;
  
  /* Текст */
  --txt: #e0d8c8;
  --txt2: #887e6e;
  
  /* Цвета факультетов (Gryffindor, Slytherin, Ravenclaw, Hufflepuff) */
  --g: #c0392b;
  --s: #1a7a3c;
  --r: #1a5a86;
  --h: #d4a017;
  
  /* Пергамент и чернила */
  --parch: #f0e4c8;
  --parch2: #e0d0a8;
  --parch3: #d4c090;
  --ink: #2c1810;
  --ink2: #5a4030;
}

body {
  font-family: 'Crimson Text', serif;
  background: var(--bg);
  color: var(--txt);
  min-height: 100vh;
  overflow-x: hidden;
}

/* Скроллбар */
::-webkit-scrollbar {
  width: 3px;
}
::-webkit-scrollbar-thumb {
  background: var(--gold2);
  border-radius: 3px;
}

/* =========================================
   2. АНИМАЦИИ
========================================= */
@keyframes fi {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: none; }
}

@keyframes fl {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-5px); }
}

@keyframes shimmer {
  0% { background-position: -200% 0; }
  100% { background-position: 200% 0; }
}

/* =========================================
   3. ГЛАВНАЯ СТРУКТУРА И ЭКРАНЫ (Улучшено)
========================================= */
canvas#stars {
  position: fixed;
  inset: 0;
  z-index: 0;
  pointer-events: none;
  /* Мягкое виньетирование поверх звезд */
  background: radial-gradient(circle at center, transparent 0%, rgba(5,5,10,0.4) 100%);
}

#app {
  position: relative;
  z-index: 1;
  max-width: 460px;
  margin: 0 auto;
  min-height: 100vh;
  padding-bottom: 90px; /* Увеличено для отступа от меню */
}

.scr {
  display: none;
  padding: 20px 16px;
  /* Плавный вылет снизу с размытием */
  animation: screenIn 0.5s cubic-bezier(0.2, 0.8, 0.2, 1) forwards;
}

@keyframes screenIn {
  from { opacity: 0; transform: translateY(15px); filter: blur(5px); }
  to { opacity: 1; transform: translateY(0); filter: blur(0); }
}

.scr.on {
  display: block;
}

/* Шапка (Королевский стиль) */
.hdr {
  text-align: center;
  padding: 30px 0 10px;
}

.hdr h1 {
  font-family: 'Cinzel', serif;
  font-size: 30px;
  font-weight: 900;
  /* Золотой градиент на тексте */
  background: linear-gradient(to bottom, var(--gold3), var(--gold), var(--gold2));
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  letter-spacing: 5px;
  filter: drop-shadow(0 0 15px rgba(201, 168, 76, 0.3));
  text-transform: uppercase;
}

.hdr .sub {
  font-size: 11px;
  color: var(--txt2);
  font-style: italic;
  margin-top: 5px;
  letter-spacing: 2px;
  text-transform: uppercase;
  opacity: 0.7;
}

/* Разделитель (Магическая нить) */
.dv {
  height: 1px;
  background: radial-gradient(circle, var(--gold) 0%, transparent 85%);
  opacity: 0.4;
  margin: 20px 40px;
  position: relative;
}
.dv::after {
  content: '✻';
  position: absolute;
  top: 50%; left: 50%;
  transform: translate(-50%, -50%);
  background: var(--bg);
  padding: 0 10px;
  color: var(--gold2);
  font-size: 10px;
}

/* =========================================
   4. БАЗОВЫЕ КОМПОНЕНТЫ (Карточки, Кнопки)
========================================= */
/* Карточки (Эффект темного стекла) */
.cd {
  background: rgba(22, 22, 40, 0.5);
  backdrop-filter: blur(8px);
  border: 1px solid rgba(201, 168, 76, 0.15);
  border-radius: 14px;
  padding: 18px;
  margin-bottom: 15px;
  box-shadow: 0 10px 30px rgba(0,0,0,0.4), inset 0 0 15px rgba(255,255,255,0.02);
  transition: transform 0.3s ease;
}

.cd-t {
  font-family: 'Cinzel', serif;
  font-size: 13px;
  font-weight: 700;
  color: var(--gold);
  margin-bottom: 10px;
  display: flex;
  align-items: center;
  gap: 10px;
  letter-spacing: 1.5px;
}
.cd-t i {
  color: var(--gold2);
  font-size: 14px;
  filter: drop-shadow(0 0 5px var(--gold2));
}

/* Кнопки (Кованый металл) */
.btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  padding: 11px 22px;
  border: 1px solid var(--gold2);
  background: linear-gradient(145deg, rgba(201,168,76,0.05), transparent);
  color: var(--gold);
  font-family: 'Cinzel', serif;
  font-size: 11px;
  font-weight: 600;
  letter-spacing: 1.5px;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  text-transform: uppercase;
  position: relative;
  overflow: hidden;
}

/* Блик на кнопке при наведении/нажатии */
.btn::before {
  content: '';
  position: absolute;
  top: 0; left: -100%;
  width: 100%; height: 100%;
  background: linear-gradient(90deg, transparent, rgba(255,255,255,0.1), transparent);
  transition: 0.5s;
}

.btn:active {
  transform: scale(0.96);
  background: var(--gold);
  color: var(--bg);
  box-shadow: 0 0 20px var(--gold);
}
.btn:active::before { left: 100%; }

.btn-f { 
  background: var(--gold); 
  color: var(--bg); 
  border-color: var(--gold3);
  box-shadow: 0 4px 15px rgba(201, 168, 76, 0.2);
}
.btn-f:active { background: var(--gold3); }

.btn-d { 
  border-color: rgba(192, 57, 43, 0.5); 
  color: #e74c3c; 
}
.btn-d:active { 
  background: #c0392b; 
  color: #fff; 
  box-shadow: 0 0 20px #c0392b;
}

.btn-sm {
  padding: 6px 14px;
  font-size: 9px;
  border-radius: 5px;
}
/* =========================================
   5. ГАЛЕРЕЯ ГЕРБОВ ФАКУЛЬТЕТОВ
========================================= */
.hgrid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
  margin: 25px 0;
  perspective: 1000px; /* Добавляет объем при взаимодействии */
}

/* Карточка-Медальон */
.hc {
  background: radial-gradient(circle at center, #1a1a2e, #080810) !important;
  border: 1px solid rgba(201, 168, 76, 0.15);
  border-radius: 15px;
  padding: 30px 10px;
  text-align: center;
  position: relative;
  overflow: hidden;
  transition: all 0.5s cubic-bezier(0.23, 1, 0.32, 1);
  box-shadow: 
    0 15px 35px rgba(0,0,0,0.6),
    inset 0 0 20px rgba(0,0,0,0.5);
}

/* Эффект "живого" герба (Пульсация магии) */
.hc::after {
  content: '';
  position: absolute;
  inset: 0;
  background: radial-gradient(circle at center, var(--h-aura), transparent 70%);
  opacity: 0.3;
  pointer-events: none;
}

/* Магическое Животное (Герб) */
.hc .em {
  font-size: 56px; /* Большой, величественный герб */
  display: block;
  margin-bottom: 15px;
  color: var(--h-color);
  filter: 
    drop-shadow(0 0 10px var(--h-color))
    contrast(1.2);
  transition: transform 0.5s ease;
  animation: fl 5s ease-in-out infinite; /* Твоя плавающая анимация */
  z-index: 2;
  position: relative;
}

/* Название Факультета */
.hc .hn {
  font-family: 'Cinzel', serif;
  font-size: 11px;
  letter-spacing: 3px;
  text-transform: uppercase;
  color: var(--gold); /* Все названия в золоте для статусности */
  opacity: 0.9;
  position: relative;
  z-index: 2;
  text-shadow: 0 2px 4px rgba(0,0,0,0.8);
}

/* Скрываем очки и полоски, если они остались в HTML */
.hc .hp, .hc .h-bar {
  display: none !important;
}

/* Декоративная печать под гербом */
.hc::before {
  content: 'ϟ';
  position: absolute;
  bottom: -10px;
  right: -5px;
  font-size: 40px;
  color: var(--h-color);
  opacity: 0.05;
  transform: rotate(-20deg);
}

/* Цвета и ауры */
.hc[data-h="g"] { --h-color: var(--g); --h-aura: rgba(192, 57, 43, 0.4); }
.hc[data-h="s"] { --h-color: var(--s); --h-aura: rgba(26, 122, 60, 0.3); }
.hc[data-h="r"] { --h-color: var(--r); --h-aura: rgba(26, 90, 134, 0.4); }
.hc[data-h="h"] { --h-color: var(--h); --h-aura: rgba(212, 160, 23, 0.4); }

/* Анимация при касании (как будто нажимаешь на реальный медальон) */
.hc:active {
  transform: scale(0.95) rotateY(10deg);
  border-color: var(--gold);
  filter: brightness(1.3);
}

/* =========================================
   6. НАВИГАЦИЯ (Artifact Ultra Version)
========================================= */

:root {
  --nav-bezier: cubic-bezier(0.4, 0, 0.2, 1);
  --nav-bounce: cubic-bezier(0.175, 0.885, 0.32, 1.275);
  --gold-glow: 0 0 15px rgba(201, 168, 76, 0.4);
  --glass-bg: rgba(8, 8, 16, 0.82);
}

/* Режим чтения: раздельное скрытие */
body.reading-mode .hdr {
  transform: translateY(-100%);
  opacity: 0;
}

body.reading-mode #bnav {
  transform: translate3d(-50%, 120%, 0); /* Уходит полностью за экран */
  opacity: 0;
  pointer-events: none;
  visibility: hidden;
}

/* Основной контейнер навигации */
#bnav {
  position: fixed;
  bottom: 25px;
  left: 50%;
  /* Использование translate3d для GPU ускорения */
  transform: translate3d(-50%, 0, 0);
  width: calc(100% - 32px);
  max-width: 410px;
  height: 68px;
  display: flex;
  z-index: 1000;
  
  background: var(--glass-bg);
  backdrop-filter: blur(20px) saturate(180%);
  -webkit-backdrop-filter: blur(20px) saturate(180%);
  
  border: 1px solid rgba(201, 168, 76, 0.2);
  border-radius: 24px;
  
  /* Глубокая многослойная тень */
  box-shadow: 
    0 4px 6px rgba(0,0,0,0.3),
    0 15px 35px rgba(0,0,0,0.5),
    inset 0 1px 1px rgba(255,255,255,0.1);
    
  padding: 0 12px;
  /* Учитываем безопасную зону iPhone без потери высоты */
  padding-bottom: env(safe-area-inset-bottom);
  transition: transform 0.6s var(--nav-bezier), opacity 0.6s var(--nav-bezier);
  will-change: transform, opacity;
}

/* Декоративный блик на стекле */
#bnav::before {
  content: '';
  position: absolute;
  inset: 0;
  border-radius: 24px;
  background: linear-gradient(110deg, transparent 40%, rgba(201, 168, 76, 0.05) 50%, transparent 60%);
  pointer-events: none;
}

/* Элементы меню */
.ni {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 3px;
  cursor: pointer;
  color: var(--txt2);
  position: relative;
  transition: color 0.3s var(--nav-bezier);
  -webkit-tap-highlight-color: transparent;
}

/* Увеличенная зона клика */
.ni::before {
  content: '';
  position: absolute;
  inset: 0;
  z-index: 1;
}

.ni i {
  font-size: 22px;
  z-index: 2;
  transition: transform 0.4s var(--nav-bounce), color 0.3s;
}

.ni span {
  font-family: 'Cinzel', serif;
  font-size: 8px;
  letter-spacing: 1.5px;
  text-transform: uppercase;
  opacity: 0.5;
  font-weight: 500;
  z-index: 2;
  transition: all 0.3s var(--nav-bezier);
}

/* Активное состояние */
.ni.on {
  color: var(--gold);
}

.ni.on i {
  transform: translateY(-10px) scale(1.2);
  filter: drop-shadow(0 0 10px rgba(201, 168, 76, 0.6));
}

.ni.on span { 
  opacity: 1; 
  transform: translateY(-2px);
  text-shadow: 0 0 8px rgba(201, 168, 76, 0.3);
}

/* Улучшенный индикатор-артефакт */
.ni.on::after {
  content: '';
  position: absolute;
  bottom: 8px;
  width: 16px;
  height: 2px;
  background: var(--gold);
  border-radius: 4px;
  box-shadow: 0 0 15px var(--gold);
  animation: artifact-pulse 2.5s infinite ease-in-out;
}

/* Плавный эффект "всплытия" при клике (ripple) */
.ni:active i {
  transform: translateY(-12px) scale(0.9);
  opacity: 0.8;
}

@keyframes artifact-pulse {
  0%, 100% { 
    opacity: 0.5; 
    width: 12px;
    filter: blur(1px);
  }
  50% { 
    opacity: 1; 
    width: 20px;
    filter: blur(0px);
  }
}

/* Адаптив для широких экранов */
@media (min-width: 500px) {
  #bnav {
    width: 400px;
    bottom: 30px;
  }
}

/* -----------------------------------------
   СПИСОК ВНУТРИ СТРАНИЦ (.nvl) — Artifact Edition
----------------------------------------- */
.nv {
  display: flex;
  flex-direction: column;
  gap: 12px; /* Чуть увеличим зазор для "воздуха" */
  margin-top: 24px;
  padding: 0 8px;
}

.nvl {
  display: flex;
  align-items: center;
  gap: 18px;
  padding: 18px 22px;
  
  /* Многослойный фон: базовый цвет + градиент + шум (опционально) */
  background: linear-gradient(145deg, rgba(255, 255, 255, 0.05) 0%, rgba(0, 0, 0, 0.1) 100%);
  background-color: rgba(255, 255, 255, 0.02);
  
  border: 1px solid rgba(201, 168, 76, 0.15);
  border-left: 2px solid rgba(201, 168, 76, 0.3); /* Акцент на левую сторону */
  border-radius: 16px;
  
  cursor: pointer;
  position: relative;
  overflow: hidden;
  transition: all 0.4s cubic-bezier(0.23, 1, 0.32, 1);
  
  /* Внешняя и внутренняя тени для объема */
  box-shadow: 
    0 4px 15px rgba(0, 0, 0, 0.2),
    inset 0 1px 1px rgba(255, 255, 255, 0.05);
}

/* Эффект при наведении (Hover) */
@media (hover: hover) {
  .nvl:hover {
    background: rgba(201, 168, 76, 0.08);
    border-color: rgba(201, 168, 76, 0.5);
    border-left-color: var(--gold);
    transform: translateX(6px);
    box-shadow: 0 6px 20px rgba(0, 0, 0, 0.3);
  }
}

/* Эффект при нажатии (Active) */
.nvl:active {
  transform: scale(0.97) translateX(2px);
  background: rgba(0, 0, 0, 0.2);
  border-color: rgba(201, 168, 76, 0.2);
  box-shadow: inset 0 2px 10px rgba(0, 0, 0, 0.5); /* Эффект вдавленности */
}

/* Иконка слева */
.nvl i {
  font-size: 20px;
  color: var(--gold);
  filter: drop-shadow(0 0 5px rgba(201, 168, 76, 0.3));
  transition: transform 0.4s ease;
}

.nvl:hover i {
  transform: scale(1.1) rotate(-5deg);
  filter: drop-shadow(0 0 8px var(--gold));
}

/* Текст */
.nvl span {
  font-family: 'Cinzel', serif;
  font-size: 15px;
  color: var(--txt);
  flex: 1;
  letter-spacing: 0.5px;
  transition: color 0.3s ease;
}

.nvl:hover span {
  color: #fff;
  text-shadow: 0 0 10px rgba(255, 255, 255, 0.2);
}

/* Стрелочка справа */
.nvl .ar {
  color: var(--gold);
  opacity: 0.2;
  font-size: 12px;
  transition: all 0.4s ease;
}

.nvl:hover .ar { 
  opacity: 1; 
  transform: translateX(4px); 
}

/* Улучшенный блик "Металлическое сияние" */
.nvl::before {
  content: '';
  position: absolute;
  top: 0; 
  left: -150%;
  width: 100%; 
  height: 100%;
  /* Блик под углом для динамики */
  background: linear-gradient(
    120deg, 
    transparent, 
    rgba(255, 255, 255, 0.05), 
    rgba(201, 168, 76, 0.15), 
    rgba(255, 255, 255, 0.05), 
    transparent
  );
  transition: 0.8s ease;
}

.nvl:hover::before {
  left: 150%;
}

/* Состояние "Выбрано" (если нужно выделить текущий пункт) */
.nvl.selected {
  background: rgba(201, 168, 76, 0.12);
  border-color: var(--gold);
  border-left-width: 4px;
}

/* =========================================
   7. ВТОРОСТЕПЕННАЯ НАВИГАЦИЯ (Artifact Style)
========================================= */

.sh {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 16px;
  padding: 0 4px;
}

.sh button {
  background: rgba(201, 168, 76, 0.05);
  border: 1px solid transparent;
  color: var(--gold);
  font-size: 18px;
  cursor: pointer;
  padding: 8px;
  border-radius: 50%;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
}

.sh button:active {
  background: rgba(201, 168, 76, 0.15);
  transform: scale(0.9);
}

.sh h2 {
  font-family: 'Cinzel', serif;
  font-size: 19px;
  color: var(--gold);
  flex: 1;
  letter-spacing: 0.5px;
  text-shadow: 0 2px 4px rgba(0,0,0,0.3);
}

/* Вкладки с эффектом затухания по краям */
.tabs {
  display: flex;
  gap: 8px;
  margin-bottom: 16px;
  overflow-x: auto;
  padding: 4px 2px 10px;
  scrollbar-width: none;
  -webkit-overflow-scrolling: touch;
  /* Маска, создающая эффект исчезновения вкладок по краям */
  mask-image: linear-gradient(to right, transparent, black 5%, black 95%, transparent);
  -webkit-mask-image: linear-gradient(to right, transparent, black 5%, black 95%, transparent);
}

.tabs::-webkit-scrollbar { display: none; }

.tb {
  padding: 8px 18px;
  border: 1px solid rgba(201, 168, 76, 0.2);
  border-radius: 20px;
  font-family: 'Cinzel', serif;
  font-size: 11px;
  color: var(--txt2);
  cursor: pointer;
  white-space: nowrap;
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
  background: rgba(255, 255, 255, 0.02);
  flex-shrink: 0;
  text-transform: uppercase;
  letter-spacing: 1px;
}

/* Эффект при наведении на десктопе */
@media (hover: hover) {
  .tb:hover {
    border-color: rgba(201, 168, 76, 0.5);
    color: var(--txt);
  }
}

.tb.on {
  background: var(--gold);
  color: var(--bg);
  border-color: var(--gold);
  box-shadow: 0 4px 15px rgba(201, 168, 76, 0.3);
  font-weight: 700;
  transform: translateY(-1px);
}

/* Строка поиска — углубленная панель */
.sb {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 11px 15px;
  background: rgba(0, 0, 0, 0.2); /* Темнее основной карточки */
  border: 1px solid rgba(201, 168, 76, 0.15);
  border-radius: 12px;
  margin-bottom: 16px;
  transition: all 0.3s ease;
  box-shadow: inset 0 2px 4px rgba(0,0,0,0.4);
}

/* Состояние фокуса при поиске */
.sb:focus-within {
  border-color: var(--gold);
  background: rgba(201, 168, 76, 0.03);
  box-shadow: 0 0 15px rgba(201, 168, 76, 0.1), inset 0 1px 2px rgba(0,0,0,0.5);
}

.sb i {
  color: var(--gold);
  opacity: 0.5;
  font-size: 14px;
  transition: 0.3s;
}

.sb:focus-within i {
  opacity: 1;
  filter: drop-shadow(0 0 5px var(--gold));
}

.sb input {
  flex: 1;
  background: none;
  border: none;
  color: var(--txt);
  font-family: 'Crimson Text', serif;
  font-size: 15px; /* Увеличено для предотвращения автозума в iOS */
  outline: none;
  padding: 2px 0;
}

.sb input::placeholder {
  color: var(--txt2);
  opacity: 0.6;
  font-style: italic;
}

/* =========================================
   8. СЕТКИ И МОДАЛЬНЫЕ ОКНА
========================================= */
.bgrid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 9px;
}
.bc {
  background: var(--card);
  border: 1px solid rgba(201, 168, 76, 0.08);
  border-radius: 7px;
  padding: 8px;
  text-align: center;
  cursor: pointer;
  transition: 0.2s;
  overflow: hidden;
}
.bc:active { transform: scale(0.96); border-color: var(--gold2); }
.bcv {
  width: 100%;
  aspect-ratio: 2/2.8;
  border-radius: 4px;
  margin-bottom: 5px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 22px;
  position: relative;
}
.bcv::after { content: ''; position: absolute; top: 0; left: 6px; width: 1px; height: 100%; background: rgba(255, 255, 255, 0.08); }
.bcv::before { content: ''; position: absolute; inset: 0; background: linear-gradient(135deg, rgba(255, 255, 255, 0.08), transparent); }
.bc .bt { font-family: 'Cinzel', serif; font-size: 8px; color: var(--txt); line-height: 1.2; display: -webkit-box; -webkit-line-clamp: 2; -webkit-box-orient: vertical; overflow: hidden; }
.bc .ba { font-size: 7px; color: var(--txt2); margin-top: 1px; }

/* Окно чтения/деталей (#br) */
#br {
  position: fixed;
  inset: 0;
  z-index: 200;
  background: var(--bg);
  display: none;
  flex-direction: column;
}
#br.open { display: flex; }
.brh {
  display: flex;
  align-items: center;
  gap: 9px;
  padding: 11px 14px;
  background: rgba(8, 8, 16, 0.96);
  backdrop-filter: blur(8px);
  border-bottom: 1px solid rgba(201, 168, 76, 0.12);
  z-index: 10;
}
.brh button { background: none; border: none; color: var(--gold); font-size: 17px; cursor: pointer; }
.brh h3 { font-family: 'Cinzel', serif; font-size: 13px; color: var(--gold); flex: 1; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
.brb { flex: 1; overflow-y: auto; padding: 16px; }

/* Тосты и Модалки */
.toast {
  position: fixed;
  top: 16px;
  left: 50%;
  transform: translateX(-50%);
  background: var(--gold);
  color: var(--bg);
  padding: 9px 18px;
  border-radius: 7px;
  font-family: 'Cinzel', serif;
  font-size: 12px;
  z-index: 999;
  animation: fi 0.25s;
  pointer-events: none;
}
.modal-bg { position: fixed; inset: 0; background: rgba(0, 0, 0, 0.65); z-index: 150; display: none; align-items: center; justify-content: center; padding: 18px; }
.modal-bg.open { display: flex; }
.mdl { background: var(--bg2); border: 1px solid rgba(201, 168, 76, 0.18); border-radius: 10px; padding: 18px; width: 100%; max-width: 380px; max-height: 80vh; overflow-y: auto; }
.mdl h3 { font-family: 'Cinzel', serif; font-size: 14px; color: var(--gold); margin-bottom: 10px; }

/* Переключатели (Toggle) */
.tgl { width: 40px; height: 22px; background: var(--card2); border-radius: 11px; cursor: pointer; position: relative; transition: 0.2s; border: 1px solid rgba(201, 168, 76, 0.12); flex-shrink: 0; }
.tgl.on { background: var(--gold); border-color: var(--gold); }
.tgl::after { content: ''; position: absolute; top: 2px; left: 2px; width: 16px; height: 16px; background: #fff; border-radius: 50%; transition: 0.2s; }
.tgl.on::after { left: 20px; }
.ntf { display: flex; align-items: center; justify-content: space-between; padding: 9px 0; border-bottom: 1px solid rgba(201, 168, 76, 0.06); }
.ntf span { font-size: 12px; }

/* =========================================
   9. ФОРМЫ, ПРОФИЛЬ, НАСТРОЙКИ
========================================= */
.ph { text-align: center; padding: 20px 0; }
.av { width: 70px; height: 70px; border-radius: 50%; border: 2px solid var(--gold); margin: 0 auto 8px; display: flex; align-items: center; justify-content: center; font-size: 28px; background: var(--card); }
.pn { font-family: 'Cinzel', serif; font-size: 18px; color: var(--gold); }
.pr { font-size: 12px; color: var(--txt2); margin-top: 3px; }

.pst { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 7px; margin: 14px 0; }
.stb { background: var(--card); border: 1px solid rgba(201, 168, 76, 0.08); border-radius: 7px; padding: 10px; text-align: center; }
.stb .sv { font-family: 'Cinzel', serif; font-size: 18px; font-weight: 700; color: var(--gold); }
.stb .sl { font-size: 10px; color: var(--txt2); margin-top: 1px; }

.as { border: 1px solid rgba(201, 168, 76, 0.18); border-radius: 9px; padding: 13px; margin-bottom: 10px; background: rgba(201, 168, 76, 0.02); }
.as h4 { font-family: 'Cinzel', serif; font-size: 12px; color: var(--gold); margin-bottom: 8px; display: flex; align-items: center; gap: 5px; }
.as h4 i { font-size: 11px; }

.pr-row { display: flex; align-items: center; gap: 6px; margin-bottom: 7px; flex-wrap: wrap; }
.pr-row select, .pr-row input { background: var(--card2); border: 1px solid rgba(201, 168, 76, 0.12); color: var(--txt); padding: 5px 9px; border-radius: 5px; font-family: 'Crimson Text', serif; font-size: 12px; outline: none; }
.pr-row input { width: 55px; text-align: center; }
textarea { width: 100%; background: var(--card2); border: 1px solid rgba(201, 168, 76, 0.12); color: var(--txt); padding: 9px; border-radius: 7px; font-family: 'Crimson Text', serif; font-size: 13px; resize: vertical; outline: none; min-height: 70px; }

.art { background: var(--card); border: 1px solid rgba(201, 168, 76, 0.08); border-radius: 9px; padding: 14px; margin-bottom: 10px; }
.art .ad { font-size: 10px; color: var(--txt2); margin-bottom: 3px; }
.art .at { font-family: 'Cinzel', serif; font-size: 15px; color: var(--gold); margin-bottom: 5px; }
.art .ab { font-size: 13px; line-height: 1.65; }

.sc { display: flex; align-items: center; gap: 10px; padding: 11px; background: var(--card); border: 1px solid rgba(201, 168, 76, 0.08); border-radius: 7px; margin-bottom: 7px; }
.sa { width: 36px; height: 36px; border-radius: 50%; background: var(--card2); display: flex; align-items: center; justify-content: center; font-size: 16px; flex-shrink: 0; }
.si { flex: 1; }
.si .sn { font-family: 'Cinzel', serif; font-size: 12px; }
.si .sr { font-size: 10px; color: var(--txt2); margin-top: 1px; }

/* Аккордеон (Раскрывающиеся списки) */
.us { margin-bottom: 12px; }
.us h3 { font-family: 'Cinzel', serif; font-size: 13px; color: var(--gold); padding: 9px 13px; background: var(--card); border-radius: 7px; cursor: pointer; display: flex; align-items: center; justify-content: space-between; }
.us h3 i { font-size: 9px; transition: 0.3s; }
.us.op h3 i { transform: rotate(180deg); }
.uc { max-height: 0; overflow: hidden; transition: 0.35s; background: var(--card); border-radius: 0 0 7px 7px; }
.us.op .uc { max-height: 3000px; padding: 2px 0; }
.uc p, .uc li { padding: 6px 13px; font-size: 13px; line-height: 1.65; }
.uc ul { list-style: none; padding: 0; }
.uc li::before { content: '✦ '; color: var(--gold); font-size: 9px; }

.li { display: flex; align-items: center; gap: 10px; padding: 11px; background: var(--card); border: 1px solid rgba(201, 168, 76, 0.08); border-radius: 7px; margin-bottom: 7px; }
.lt { font-family: 'Cinzel', serif; font-size: 11px; color: var(--gold); min-width: 45px; }
.lnfo { flex: 1; }
.lnfo .ln { font-size: 12px; }
.lnfo .ld { font-size: 10px; color: var(--txt2); margin-top: 1px; }

.es { text-align: center; padding: 35px 18px; color: var(--txt2); }
.es i { font-size: 36px; margin-bottom: 10px; opacity: 0.25; display: block; }
.es p { font-size: 13px; font-style: italic; }

/* =========================================
   10. ПЕРГАМЕНТНЫЕ СТРАНИЦЫ
========================================= */
.bp {
  background: linear-gradient(145deg, #f4e8c1, #e8d8a8, #f0e0b0);
  color: var(--ink);
  border-radius: 3px;
  padding: 22px 18px;
  margin-bottom: 14px;
  position: relative;
  box-shadow: 2px 2px 8px rgba(0, 0, 0, 0.3), inset 0 0 25px rgba(139, 109, 56, 0.08);
  font-size: 14px;
  line-height: 1.75;
}
.bp::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 3px;
  height: 100%;
  background: linear-gradient(to bottom, rgba(139, 109, 56, 0.35), rgba(139, 109, 56, 0.08));
}
.bp::after {
  content: '';
  position: absolute;
  top: 8px;
  right: 8px;
  bottom: 8px;
  left: 26px;
  border: 1px solid rgba(139, 109, 56, 0.12);
  pointer-events: none;
}
.bp .cht { font-family: 'Cinzel', serif; font-size: 16px; color: #4a2c0a; text-align: center; margin-bottom: 14px; padding-bottom: 8px; border-bottom: 1px solid rgba(139, 109, 56, 0.25); }
.bp p { margin-bottom: 10px; text-indent: 1.4em; text-align: justify; }
.bp p:first-of-type { text-indent: 0; }
.bp .orn { text-align: center; color: rgba(139, 109, 56, 0.4); font-size: 18px; margin: 14px 0; letter-spacing: 6px; }

/* =========================================
   11. ОБЛОЖКИ КНИГ-СБОРНИКОВ (ГРИМУАРЫ)
========================================= */
.grimoire-wrap { text-align: center; padding: 10px 0 20px; }
.grimoire-cover {
  width: 100%;
  max-width: 260px;
  margin: 0 auto;
  border-radius: 3px 10px 10px 3px;
  padding: 32px 22px 26px;
  position: relative;
  cursor: pointer;
  transition: transform 0.3s, box-shadow 0.3s;
  box-shadow: 6px 6px 24px rgba(0, 0, 0, 0.55), -2px 0 6px rgba(0, 0, 0, 0.25), inset 0 0 40px rgba(0, 0, 0, 0.15);
}
.grimoire-cover:active { transform: scale(0.97); }

.grimoire-cover .spine { position: absolute; left: 0; top: 0; bottom: 0; width: 14px; border-radius: 3px 0 0 3px; }
.grimoire-cover .inner-border { position: absolute; right: 10px; top: 10px; bottom: 10px; left: 24px; border: 1px solid rgba(255, 255, 255, 0.06); pointer-events: none; border-radius: 2px; }

.grimoire-cover .corner { position: absolute; font-size: 16px; opacity: 0.3; }
.grimoire-cover .corner.tl { top: 14px; left: 22px; }
.grimoire-cover .corner.tr { top: 14px; right: 14px; }
.grimoire-cover .corner.bl { bottom: 14px; left: 22px; }
.grimoire-cover .corner.br { bottom: 14px; right: 14px; }

.grimoire-cover .cover-icon { font-size: 44px; margin-bottom: 14px; filter: drop-shadow(0 3px 6px rgba(0, 0, 0, 0.4)); position: relative; z-index: 1; }
.grimoire-cover .cover-title { font-family: 'UnifrakturMaguntia', cursive; font-size: 22px; text-align: center; margin-bottom: 6px; position: relative; z-index: 1; text-shadow: 0 2px 6px rgba(0, 0, 0, 0.5); }
.grimoire-cover .cover-sub { font-family: 'Cinzel', serif; font-size: 9px; text-align: center; letter-spacing: 2.5px; text-transform: uppercase; opacity: 0.45; margin-bottom: 16px; }
.grimoire-cover .cover-meta { text-align: center; font-size: 10px; opacity: 0.3; }
.grimoire-cover .cover-line { width: 60%; height: 1px; margin: 0 auto 10px; opacity: 0.2; }
.grimoire-cover .cover-orn-row { display: flex; justify-content: center; gap: 12px; margin: 8px 0 0; opacity: 0.2; font-size: 10px; }
.grimoire-cover .cover-seal { position: absolute; bottom: 18px; right: 18px; width: 36px; height: 36px; border-radius: 50%; border: 1px solid rgba(255, 255, 255, 0.1); display: flex; align-items: center; justify-content: center; font-size: 14px; opacity: 0.25; }

/* Специфические обложки */
.gcover-spells { background: linear-gradient(155deg, #3a0c0c, #200606, #2a0808, #1a0404); color: #e8d5a3; }
.gcover-spells .spine { background: linear-gradient(to bottom, #5a1a1a, #2a0808); }
.gcover-spells .cover-line, .gcover-spells .corner { background: #c9a84c; }

.gcover-potions { background: linear-gradient(155deg, #0c2a14, #061a0c, #082a10, #041a08); color: #a3e8c0; }
.gcover-potions .spine { background: linear-gradient(to bottom, #1a4a2a, #082a10); }
.gcover-potions .cover-line, .gcover-potions .corner { background: #4cc98a; }

.gcover-beasts { background: linear-gradient(155deg, #2a1a0c, #1a0e06, #2a1e0c, #1a1004); color: #e8d0a3; }
.gcover-beasts .spine { background: linear-gradient(to bottom, #4a3a1a, #2a1a0c); }
.gcover-beasts .cover-line, .gcover-beasts .corner { background: #c9a44c; }

/* =========================================
   12. ВНУТРЕННИЕ СТРАНИЦЫ КНИГ
========================================= */
.ib-sb { display: flex; align-items: center; gap: 7px; padding: 9px 13px; background: rgba(139, 109, 56, 0.08); border: 1px solid rgba(139, 109, 56, 0.18); border-radius: 8px; margin-bottom: 12px; }
.ib-sb i { color: var(--ink2); font-size: 13px; }
.ib-sb input { flex: 1; background: none; border: none; color: var(--ink); font-family: 'Crimson Text', serif; font-size: 13px; outline: none; }
.ib-sb input::placeholder { color: var(--ink2); opacity: 0.6; }

.ib-tabs { display: flex; gap: 4px; margin-bottom: 14px; overflow-x: auto; padding-bottom: 3px; flex-wrap: wrap; }
.ib-tabs::-webkit-scrollbar { display: none; }
.ib-tb { padding: 5px 11px; border: 1px solid rgba(139, 109, 56, 0.2); border-radius: 12px; font-family: 'Cinzel', serif; font-size: 9px; color: var(--ink2); cursor: pointer; white-space: nowrap; transition: 0.2s; background: rgba(139, 109, 56, 0.04); }
.ib-tb.on { background: rgba(139, 109, 56, 0.2); color: var(--ink); border-color: rgba(139, 109, 56, 0.35); }
.ib-count { text-align: center; font-family: 'Cinzel', serif; font-size: 10px; color: var(--ink2); margin-bottom: 14px; letter-spacing: 1px; }

/* --- СТРАНИЦЫ ЗАКЛИНАНИЙ --- */
.sp { background: linear-gradient(145deg, var(--parch), var(--parch2), var(--parch3)); color: var(--ink); border-radius: 4px; padding: 18px 16px; margin-bottom: 14px; position: relative; box-shadow: 2px 2px 10px rgba(0, 0, 0, 0.3), inset 0 0 30px rgba(139, 109, 56, 0.08); overflow: hidden; }
.sp::before { content: ''; position: absolute; top: 0; left: 0; width: 3px; height: 100%; background: linear-gradient(to bottom, rgba(139, 109, 56, 0.4), rgba(139, 109, 56, 0.08)); }
.sp::after { content: ''; position: absolute; top: 8px; right: 8px; bottom: 8px; left: 22px; border: 1px solid rgba(139, 109, 56, 0.1); pointer-events: none; }

.sp-head { display: flex; align-items: flex-start; gap: 14px; margin-bottom: 10px; position: relative; z-index: 1; }
.sp-circle { width: 52px; height: 52px; border-radius: 50%; border: 1.5px solid rgba(139, 109, 56, 0.25); display: flex; align-items: center; justify-content: center; font-size: 20px; flex-shrink: 0; position: relative; background: rgba(139, 109, 56, 0.06); }
.sp-circle .ring { position: absolute; inset: -4px; border: 1px dashed rgba(139, 109, 56, 0.12); border-radius: 50%; }
.sp-info { flex: 1; }
.sp-name { font-family: 'UnifrakturMaguntia', cursive; font-size: 18px; color: #3a1a0a; line-height: 1.2; }
.sp-incant { font-family: 'Cinzel', serif; font-size: 10px; color: var(--ink2); font-style: italic; margin-top: 2px; }

.sp-badges { display: flex; gap: 4px; margin: 8px 0; flex-wrap: wrap; position: relative; z-index: 1; }
.sp-badge { display: inline-flex; align-items: center; gap: 3px; padding: 2px 8px; border-radius: 10px; font-family: 'Cinzel', serif; font-size: 8px; }
.sp-badge.daily { background: rgba(100, 80, 40, 0.1); color: #5a4020; border: 1px solid rgba(100, 80, 40, 0.2); }
.sp-badge.combat { background: rgba(180, 20, 20, 0.1); color: #8b0000; border: 1px solid rgba(180, 20, 20, 0.2); }
.sp-badge.light { background: rgba(26, 122, 60, 0.1); color: #1a5c3c; border: 1px solid rgba(26, 122, 60, 0.2); }
.sp-badge.dark { background: rgba(30, 10, 10, 0.12); color: #3a0a0a; border: 1px solid rgba(30, 10, 10, 0.25); }
.sp-badge.mental { background: rgba(80, 40, 120, 0.1); color: #3a1a5c; border: 1px solid rgba(80, 40, 120, 0.2); }
.sp-badge.heal { background: rgba(20, 100, 80, 0.1); color: #0a4a3a; border: 1px solid rgba(20, 100, 80, 0.2); }

.sp-desc { font-size: 12px; line-height: 1.65; color: var(--ink); text-align: justify; position: relative; z-index: 1; }
.sp-orn { text-align: center; color: rgba(139, 109, 56, 0.3); font-size: 12px; margin: 6px 0; letter-spacing: 8px; position: relative; z-index: 1; }

/* --- СТРАНИЦЫ ЗЕЛИЙ --- */
.pp { background: linear-gradient(145deg, var(--parch), var(--parch2)); color: var(--ink); border-radius: 4px; padding: 18px 16px; margin-bottom: 14px; position: relative; box-shadow: 2px 2px 10px rgba(0, 0, 0, 0.3), inset 0 0 30px rgba(139, 109, 56, 0.08); overflow: hidden; }
.pp::before { content: ''; position: absolute; top: 0; left: 0; width: 3px; height: 100%; background: linear-gradient(to bottom, rgba(139, 109, 56, 0.4), rgba(139, 109, 56, 0.08)); }
.pp::after { content: ''; position: absolute; top: 8px; right: 8px; bottom: 8px; left: 22px; border: 1px solid rgba(139, 109, 56, 0.1); pointer-events: none; }

.pp-head { display: flex; align-items: flex-start; gap: 14px; margin-bottom: 10px; position: relative; z-index: 1; }
.pp-bottle { width: 48px; height: 72px; flex-shrink: 0; position: relative; }
.pp-bottle .bk-cork { width: 16px; height: 6px; margin: 0 auto; background: linear-gradient(90deg, #a08060, #c0a070, #a08060); border-radius: 2px 2px 0 0; }
.pp-bottle .bk-neck { width: 14px; height: 14px; margin: 0 auto; background: linear-gradient(90deg, rgba(200, 200, 200, 0.3), rgba(255, 255, 255, 0.2), rgba(200, 200, 200, 0.3)); border: 1px solid rgba(139, 109, 56, 0.15); border-bottom: none; border-radius: 2px 2px 0 0; }
.pp-bottle .bk-body { width: 40px; height: 48px; margin: 0 auto; background: linear-gradient(180deg, rgba(200, 200, 200, 0.12), rgba(200, 200, 200, 0.04)); border: 1px solid rgba(139, 109, 56, 0.2); border-radius: 3px 3px 10px 10px; position: relative; overflow: hidden; top: -1px; }
.pp-bottle .bk-liq { position: absolute; bottom: 0; left: 0; right: 0; border-radius: 0 0 9px 9px; }
.pp-bottle .bk-shine { position: absolute; top: 4px; left: 4px; width: 6px; height: 35%; background: linear-gradient(180deg, rgba(255, 255, 255, 0.3), transparent); border-radius: 3px; }

.pp-info { flex: 1; }
.pp-name { font-family: 'UnifrakturMaguntia', cursive; font-size: 18px; color: #3a1a0a; line-height: 1.2; }
.pp-cat { font-family: 'Cinzel', serif; font-size: 9px; color: var(--ink2); text-transform: uppercase; letter-spacing: 1.5px; margin-top: 2px; }
.pp-diff { display: flex; gap: 3px; margin-top: 5px; }
.pp-dot { width: 7px; height: 7px; border-radius: 50%; border: 1px solid rgba(139, 109, 56, 0.25); }
.pp-dot.f { background: var(--ink2); }

.pp-section { margin: 8px 0; position: relative; z-index: 1; }
.pp-stitle { font-family: 'Cinzel', serif; font-size: 9px; color: var(--ink2); text-transform: uppercase; letter-spacing: 1px; margin-bottom: 4px; display: flex; align-items: center; gap: 4px; }
.pp-stitle i { font-size: 8px; }
.pp-stext { font-size: 12px; line-height: 1.55; color: var(--ink); }

.pp-ings { display: flex; flex-wrap: wrap; gap: 3px; margin-top: 3px; }
.pp-ing { background: rgba(139, 109, 56, 0.08); border: 1px solid rgba(139, 109, 56, 0.12); border-radius: 10px; padding: 2px 8px; font-size: 10px; color: var(--ink); }
.pp-orn { text-align: center; color: rgba(139, 109, 56, 0.3); font-size: 12px; margin: 6px 0; letter-spacing: 8px; position: relative; z-index: 1; }

/* --- СТРАНИЦЫ СУЩЕСТВ --- */
.cp { background: linear-gradient(145deg, var(--parch), var(--parch2), var(--parch)); color: var(--ink); border-radius: 4px; padding: 18px 16px; margin-bottom: 14px; position: relative; box-shadow: 2px 2px 10px rgba(0, 0, 0, 0.3), inset 0 0 30px rgba(139, 109, 56, 0.08); overflow: hidden; }
.cp::before { content: ''; position: absolute; top: 0; left: 0; width: 3px; height: 100%; background: linear-gradient(to bottom, rgba(139, 109, 56, 0.4), rgba(139, 109, 56, 0.08)); }
.cp::after { content: ''; position: absolute; top: 8px; right: 8px; bottom: 8px; left: 22px; border: 1px solid rgba(139, 109, 56, 0.1); pointer-events: none; }

.cp-icon-area { width: 100%; height: 100px; border-radius: 4px; margin-bottom: 10px; display: flex; align-items: center; justify-content: center; position: relative; overflow: hidden; background: rgba(139, 109, 56, 0.04); }
.cp-icon-area .bg-tex { position: absolute; inset: 0; opacity: 0.06; background: radial-gradient(circle at 30% 40%, rgba(139, 109, 56, 0.5), transparent 60%); }
.cp-icon-area .c-icon { font-size: 64px; filter: drop-shadow(2px 4px 6px rgba(0, 0, 0, 0.2)); position: relative; z-index: 1; }

.cp-name { font-family: 'UnifrakturMaguntia', cursive; font-size: 20px; color: #3a1a0a; text-align: center; margin-bottom: 3px; position: relative; z-index: 1; }
.cp-sub { font-family: 'Cinzel', serif; font-size: 9px; color: var(--ink2); text-align: center; letter-spacing: 2px; text-transform: uppercase; margin-bottom: 8px; }

.cp-badge { display: inline-flex; align-items: center; gap: 3px; padding: 2px 9px; border-radius: 10px; font-family: 'Cinzel', serif; font-size: 8px; font-weight: 700; }
.cp-badge.x5 { background: rgba(180, 20, 20, 0.12); color: #8b0000; border: 1px solid rgba(180, 20, 20, 0.25); }
.cp-badge.x4 { background: rgba(180, 100, 20, 0.12); color: #8b4513; border: 1px solid rgba(180, 100, 20, 0.25); }
.cp-badge.x3 { background: rgba(20, 120, 60, 0.12); color: #1a5c3c; border: 1px solid rgba(20, 120, 60, 0.25); }
.cp-badge.x2 { background: rgba(80, 80, 120, 0.1); color: #3a3a5c; border: 1px solid rgba(80, 80, 120, 0.2); }
.cp-badge.x1 { background: rgba(100, 100, 100, 0.08); color: #555; border: 1px solid rgba(100, 100, 100, 0.15); }

.cp-row { display: flex; gap: 6px; margin: 8px 0; flex-wrap: wrap; }
.cp-field { flex: 1; min-width: 80px; background: rgba(139, 109, 56, 0.06); border: 1px solid rgba(139, 109, 56, 0.1); border-radius: 3px; padding: 5px 7px; }
.cp-field .cf-l { font-family: 'Cinzel', serif; font-size: 7px; color: var(--ink2); text-transform: uppercase; letter-spacing: 1px; }
.cp-field .cf-v { font-size: 11px; color: var(--ink); margin-top: 1px; line-height: 1.35; }

.cp-orn { display: flex; align-items: center; gap: 6px; margin: 8px 0; color: rgba(139, 109, 56, 0.3); font-size: 12px; position: relative; z-index: 1; }
.cp-orn::before, .cp-orn::after { content: ''; flex: 1; height: 1px; background: rgba(139, 109, 56, 0.15); }

.cp-desc { font-size: 12px; line-height: 1.6; color: var(--ink); text-align: justify; text-indent: 1.2em; position: relative; z-index: 1; }
.cp-desc:first-of-type { text-indent: 0; }
.cp-feat { background: rgba(139, 109, 56, 0.05); border: 1px solid rgba(139, 109, 56, 0.1); border-radius: 3px; padding: 6px 8px; margin-top: 6px; position: relative; z-index: 1; }
.cp-feat .cf-l { font-family: 'Cinzel', serif; font-size: 7px; color: var(--ink2); text-transform: uppercase; letter-spacing: 1px; margin-bottom: 2px; }
.cp-feat .cf-v { font-size: 11px; color: var(--ink); line-height: 1.4; }

.cp-empty { text-align: center; padding: 30px 18px; color: var(--ink2); font-style: italic; font-size: 13px; }
/* ============================================================
   МАГИЧЕСКИЙ ГРИМУАР: ГЛУБОКАЯ НОЧЬ (ВЕРСИЯ 2026)
   ============================================================ */

:root {
  /* Цветовая палитра */
  --bg: #050301;          /* Цвет бездны */
  --bg-card: #0a0805;     /* Цвет обложки гримуара */
  --gold: #c9a84c;        /* Старое золото */
  --gold-br: #f5e2b8;     /* Сияющее золото */
  --gold-dark: #7a5f1d;   /* Бронза */
  --txt: #e0d8c8;         /* Цвет кости */
  --txt-dim: #887e6e;     /* Пыльный текст */
  --border: rgba(201, 168, 76, 0.15);
  --glow: 0 0 15px rgba(201, 168, 76, 0.3);
  --shadow: 0 15px 35px rgba(0, 0, 0, 0.9);
}

/* 1. ГЛОБАЛЬНЫЕ СБРОСЫ */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  -webkit-tap-highlight-color: transparent;
}

/* Тело документа */
body {
  font-family: 'Crimson Text', serif;
  background-color: var(--bg);
  background-image:
    radial-gradient(circle at 50% 0%, #1e140a 0%, transparent 60%),
    url("https://www.transparenttextures.com/patterns/dark-leather.png");
  color: var(--txt);
  min-height: 100vh;
  overflow-x: hidden;
}

/* Скроллбар */
::-webkit-scrollbar {
  width: 3px;
}
::-webkit-scrollbar-thumb {
  background: var(--gold-dark);
  border-radius: 3px;
}

/* Контейнер приложения */
#app {
  max-width: 460px;
  margin: 0 auto;
  padding: 20px;
  padding-bottom: 80px; /* пространство под навигацией */
  position: relative;
  z-index: 1;
}

/* 2. Заголовок */
.hdr h1 {
  font-family: 'Cinzel', serif;
  font-size: 28px;
  color: var(--gold);
  text-align: center;
  letter-spacing: 4px;
  text-shadow: 0 0 20px rgba(201, 168, 76, 0.5);
  margin-bottom: 20px;
}

/* Навигационная панель */
#bnav {
  position: fixed;
  bottom: 0;
  inset-x: 0;
  max-width: 460px;
  margin: 0 auto;
  display: flex;
  background: rgba(5, 3, 1, 0.96);
  backdrop-filter: blur(10px);
  border-top: 1px solid var(--border);
  z-index: 1000;
}
.ni {
  flex: 1;
  padding: 12px 0;
  text-align: center;
  font-size: 10px;
  color: var(--txt-dim);
  cursor: pointer;
  transition: 0.3s;
}
.ni i {
  font-size: 20px;
  display: block;
  margin-bottom: 4px;
}
.ni.on {
  color: var(--gold);
  text-shadow: 0 0 10px var(--gold);
}

/* 3. Стиль карточек и элементов формы (Glass Morphism) */
.ib-sb, .sb {
  background: rgba(20, 15, 10, 0.7) !important;
  border: 1px solid var(--border) !important;
  border-radius: 12px !important;
  padding: 12px 16px !important;
  margin-bottom: 15px;
  display: flex;
  align-items: center;
  gap: 10px;
}
.ib-sb input, .sb input {
  background: none;
  border: none;
  outline: none;
  color: var(--gold-br);
  font-family: 'Cinzel', serif;
  width: 100%;
  font-size: 13px;
}

/* 4. Заклинания (Темные страницы) */
.sp {
  background: linear-gradient(145deg, #120d07, #050301) !important;
  border: 1px solid var(--border) !important;
  border-radius: 10px !important;
  padding: 20px !important;
  margin-bottom: 15px;
  box-shadow: var(--shadow), inset 0 0 20px rgba(201, 168, 76, 0.05) !important;
  position: relative;
  overflow: hidden;
}
.sp::before {
  content: '';
  position: absolute;
  left: 0; top: 0; bottom: 0;
  width: 3px;
  background: var(--gold-dark);
}
.sp-name {
  font-family: 'UnifrakturMaguntia', cursive !important;
  font-size: 24px !important;
  color: var(--gold-br) !important;
  text-shadow: 0 0 10px var(--gold);
  margin-bottom: 10px;
}
.sp-incant {
  color: var(--gold);
  font-family: 'Cinzel', serif;
  font-size: 11px;
  font-style: italic;
  margin-bottom: 10px;
}
.sp-desc {
  color: var(--txt-dim);
  font-size: 13px;
  margin-top: 10px;
  line-height: 1.6;
  text-align: justify;
}

.pp {
    position: relative;
    background: linear-gradient(145deg, #0a150f, #020805); /* Глубокий лесной оттенок */
    border: 1px solid rgba(76, 201, 138, 0.3);
    border-radius: 14px;
    padding: 22px;
    margin-bottom: 20px;
    box-shadow: 
        0 15px 35px rgba(0,0,0,0.7),
        inset 0 0 20px rgba(76, 201, 138, 0.15);
    overflow: hidden;
    transition: transform 0.3s ease;
}

/* Эффект испарения зелья */
.pp::after {
    content: '';
    position: absolute;
    top: -50%; left: -50%;
    width: 200%; height: 200%;
    background: radial-gradient(circle, rgba(76, 201, 138, 0.05) 0%, transparent 70%);
    animation: potionMist 6s infinite linear;
    pointer-events: none;
}

@keyframes potionMist {
    0% { transform: translate(0, 0) rotate(0deg); }
    100% { transform: translate(10%, 10%) rotate(360deg); }
}

.pp-name {
    font-family: 'UnifrakturMaguntia', cursive;
    font-size: 26px;
    color: #a3e8c0;
    text-shadow: 0 0 15px rgba(76, 201, 138, 0.6);
    margin-bottom: 8px;
}

.pp-ing {
    display: inline-block;
    background: rgba(76, 201, 138, 0.15);
    border: 1px solid rgba(76, 201, 138, 0.3);
    color: #d1f2e1;
    border-radius: 6px;
    padding: 3px 10px;
    font-size: 11px;
    letter-spacing: 0.5px;
    text-transform: uppercase;
}

/* 6. СУЩЕСТВА (BEASTS) — СЕТКА И КАРТОЧКИ */
.beast-grid { 
    display: grid; 
    grid-template-columns: repeat(2, 1fr); 
    gap: 16px; 
}

.beast-card-mini {
    background: var(--bg-card);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 12px;
    text-align: center;
    transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
    position: relative;
}

.beast-card-mini:hover {
    border-color: var(--gold);
    transform: translateY(-8px) scale(1.02);
    box-shadow: 0 15px 30px rgba(0,0,0,0.5), 0 0 15px rgba(201,168,76,0.15);
}

.mini-img-wrap {
    width: 100%; height: 120px;
    border-radius: 8px;
    overflow: hidden;
    margin-bottom: 10px;
    border: 1px solid rgba(255,255,255,0.05);
    background: #000; /* Черная подложка для глубины */
}

.mini-img-wrap img {
    width: 100%; height: 100%;
    object-fit: cover;
    filter: sepia(0.8) brightness(0.6) contrast(1.2);
    transition: 0.6s ease;
}

.beast-card-mini:hover img {
    filter: sepia(0) brightness(1) contrast(1);
    transform: scale(1.1);
}

.mini-name {
    font-family: 'Cinzel', serif;
    color: var(--gold-br);
    font-size: 13px;
    letter-spacing: 1px;
}
/* ============================================================
   МАГИЧЕСКИЙ УСТАВ: АЛЬФА-РЕДАКЦИЯ (ARTIFACT STYLING)
   ============================================================ */

/* 1. ПЕРЕМЕННЫЕ (Обновлено для лучшего контраста) */
:root {
  --bg: #080810;
  --bg2: #10101e;
  --card: rgba(22, 22, 40, 0.7);
  --gold: #c9a84c;
  --gold2: #9a7830;
  --gold3: #e8d5a3;
  --txt: #e0d8c8;
  --txt2: #887e6e;
  --shadow: 0 10px 30px rgba(0, 0, 0, 0.6);
  --plus: #4caf50;
  --minus: #f44336;
}

/* 2. ГЛОБАЛЬНЫЕ ТЕКСТОВЫЕ ТЕГИ (Внутри Устава) */
.magic-ustav {
  font-family: 'Crimson Text', serif;
  max-width: 460px;
  margin: 0 auto;
}

/* Стилизация жирного текста как золотых акцентов */
.uc b, .us b {
  color: var(--gold3);
  font-weight: 600;
  text-shadow: 0 0 8px rgba(201, 168, 76, 0.2);
}

/* Стилизация наград и штрафов по ключевым символам */
.uc b:contains("+"), .plus { color: var(--plus); }
.uc b:contains("-"), .minus { color: var(--minus); }

/* 3. АККОРДЕОН (Разделы Устава) */
.us {
  margin-bottom: 16px;
  perspective: 1000px;
}

/* Заголовок раздела */
.us h3 {
  font-family: 'Cinzel', serif;
  font-size: 13px;
  color: var(--gold);
  padding: 16px 20px;
  background: linear-gradient(145deg, var(--card), var(--bg2));
  border: 1px solid rgba(201, 168, 76, 0.2);
  border-radius: 12px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: space-between;
  transition: all 0.4s cubic-bezier(0.23, 1, 0.32, 1);
  box-shadow: var(--shadow);
  text-transform: uppercase;
  letter-spacing: 1px;
}

/* Эффект при наведении */
.us h3:hover {
  border-color: var(--gold);
  transform: translateY(-2px);
  background: linear-gradient(145deg, var(--card2), var(--card));
}

/* Иконка стрелочки в заголовке */
.us h3 i {
  font-size: 10px;
  transition: transform 0.4s var(--nav-bounce);
  color: var(--gold2);
}

/* Когда раздел открыт (класс .op) */
.us.op h3 {
  background: var(--gold);
  color: var(--bg);
  border-bottom-left-radius: 0;
  border-bottom-right-radius: 0;
  box-shadow: 0 5px 15px rgba(201, 168, 76, 0.3);
}

.us.op h3 i {
  transform: rotate(180deg);
  color: var(--bg);
}

/* 4. КОНТЕНТ РАЗДЕЛА (Выпадающее меню) */
.uc {
  max-height: 0;
  overflow: hidden;
  background: rgba(16, 16, 30, 0.5);
  backdrop-filter: blur(10px);
  border: 1px solid rgba(201, 168, 76, 0.1);
  border-top: none;
  border-radius: 0 0 12px 12px;
  transition: max-height 0.6s cubic-bezier(0.4, 0, 0.2, 1);
}

.us.op .uc {
  max-height: 2500px; /* Достаточно для любого текста */
  padding: 10px 0;
}

/* Текстовые блоки внутри */
.uc p {
  padding: 10px 20px;
  font-size: 14px;
  line-height: 1.6;
  color: var(--txt);
  margin: 0;
}

/* Списки внутри Устава */
.uc ul {
  list-style: none;
  padding: 5px 0;
}

.uc li {
  padding: 8px 20px 8px 40px;
  font-size: 14px;
  position: relative;
  color: var(--txt);
  border-bottom: 1px solid rgba(255, 255, 255, 0.03);
}

.uc li:last-child { border-bottom: none; }

/* Магический маркер списка */
.uc li::before {
  content: '✦';
  position: absolute;
  left: 20px;
  color: var(--gold2);
  font-size: 10px;
  top: 12px;
}

/* 5. СПЕЦИАЛЬНЫЕ БЛОКИ (Инструкции и Важное) */
/* Цитаты или блоки "Важно" */
.uc blockquote {
  margin: 15px 20px;
  padding: 15px;
  background: rgba(201, 168, 76, 0.05);
  border-left: 3px solid var(--gold);
  border-radius: 4px;
  font-style: italic;
  font-size: 13px;
  color: var(--gold3);
}

/* Имена персонажей или технические требования */
.uc code {
  background: rgba(0, 0, 0, 0.3);
  padding: 2px 6px;
  border-radius: 4px;
  color: var(--gold);
  font-family: monospace;
  border: 1px solid rgba(201, 168, 76, 0.1);
}

/* Анимация пульсации для критических предупреждений */
.pulse-warn {
  animation: warning-glow 2s infinite ease-in-out;
}

@keyframes warning-glow {
  0%, 100% { text-shadow: 0 0 5px rgba(244, 67, 54, 0); }
  50% { text-shadow: 0 0 10px rgba(244, 67, 54, 0.5); color: #ff8a80; }
}

/* 6. СКРОЛЛБАР ВНУТРИ УСТАВА */
.uc::-webkit-scrollbar {
  width: 2px;
}
.uc::-webkit-scrollbar-thumb {
  background: var(--gold2);
}

/* 7. ШАПКА ХОГВАРТСА (Золотое сияние) */
.hdr h1 {
  font-family: 'Cinzel', serif;
  background: linear-gradient(90deg, var(--gold2), var(--gold3), var(--gold), var(--gold2));
  background-size: 200% auto;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  animation: shimmer 5s linear infinite;
}

@keyframes shimmer {
  to { background-position: 200% center; }
}
/* 7. ПОЛНОЭКРАННЫЙ ГРИМУАР (СТРАНИЦА СУЩЕСТВА) */
.cp {
    background: #0d0a07; /* Чуть светлее бездны для контраста */
    border: 1px solid var(--border);
    border-radius: 20px;
    padding: 30px;
    box-shadow: var(--shadow);
    perspective: 1000px; /* Для 3D-эффекта */
    animation: bookOpen 0.8s cubic-bezier(0.23, 1, 0.32, 1);
}

@keyframes bookOpen {
    from { 
        opacity: 0; 
        transform: rotateY(-30deg) scale(0.9);
        filter: blur(5px);
    }
    to { 
        opacity: 1; 
        transform: rotateY(0) scale(1);
        filter: blur(0);
    }
}

.cp-creature-img {
    width: 100%;
    max-height: 300px;
    object-fit: cover;
    border-radius: 12px;
    border: 1px solid var(--border);
    margin: 20px 0;
    box-shadow: 0 0 25px rgba(0,0,0,0.5);
    /* Виньетка поверх изображения */
    mask-image: radial-gradient(circle, black 60%, transparent 100%);
}

.tb, .ib-tb {
    background: rgba(201, 168, 76, 0.08);
    border: 1px solid var(--border);
    color: var(--txt-dim);
    padding: 8px 16px;
    border-radius: 25px;
    font-family: 'Cinzel', serif;
    font-size: 11px;
    text-transform: uppercase;
    letter-spacing: 1px;
    cursor: pointer;
    transition: all 0.3s ease;
    outline: none;
}

.tb.on, .ib-tb.on {
    background: var(--gold);
    color: #050301;
    border-color: var(--gold-br);
    box-shadow: 0 0 15px rgba(201, 168, 76, 0.4);
    transform: translateY(-2px);
}

/* Эффект нажатия */
.tb:active {
    transform: scale(0.95);
}

/* Общие стили для элементов с светлыми эффектами */
.bp,
.sp,
.pp,
.cp {
  color: var(--txt) !important;
}
.sp::after,
.pp::after,
.cp::after {
  display: none !important; /* Убираем белые рамки-окантовки */
}

/* Эффект свечения жидкости (анимация пульсации) */
@keyframes liquidPulse {
  0%, 100% {
    filter: brightness(1) drop-shadow(0 0 5px var(--p-col));
  }
  50% {
    filter: brightness(1.3) drop-shadow(0 0 15px var(--p-col));
  }
}

/* Стиль карточки зелия */
.pp {
  position: relative;
  overflow: hidden;
  border-left: 4px solid var(--p-col);
  background: linear-gradient(145deg, #0a0805, #050301) !important;
  border: 1px solid rgba(201, 168, 76, 0.15) !important;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5), inset 0 0 15px var(--p-glow) !important;
  padding: 25px 20px !important;
  margin-bottom: 25px;
  border-radius: 12px;
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
}

/* Визуальный элемент флакона */
.pp-bottle-art {
  position: absolute;
  right: 15px;
  top: 15px;
  width: 40px;
  height: 50px;
  opacity: 0.6;
  background: var(--p-col);
  clip-path: polygon(30% 0%, 70% 0%, 70% 20%, 100% 20%, 100% 100%, 0% 100%, 0% 20%, 30% 20%);
  animation: liquidPulse 3s infinite ease-in-out;
}

/* Маркировка редкости (стикеры) */
.rare-tag {
  font-family: 'Cinzel', serif;
  font-size: 9px;
  text-transform: uppercase;
  letter-spacing: 1px;
  padding: 2px 8px;
  border-radius: 4px;
  margin-bottom: 8px;
  display: inline-block;
}
.rare-Legendary {
  background: rgba(212, 160, 23, 0.2);
  color: #d4a017;
  border: 1px solid #d4a017;
  animation: legendaryPulse 4s infinite ease-in-out;
}
.rare-Epic {
  background: rgba(160, 32, 240, 0.2);
  color: #bf5fff;
  border: 1px solid #bf5fff;
}
.rare-Common {
  background: rgba(255, 255, 255, 0.05);
  color: #888;
  border: 1px solid #444;
}

/* Список ингредиентов как магические теги */
.pp-ings {
  display: flex;
  flex-wrap: wrap;
  gap: 5px;
  margin-top: 10px;
}
.pp-ing {
  font-size: 10px !important;
  background: rgba(0, 0, 0, 0.3) !important;
  border: 1px dashed var(--border) !important;
  color: var(--txt-dim) !important;
  padding: 2px 7px !important;
}

/* Название зелья */
.pp-name {
  font-family: 'UnifrakturMaguntia', cursive, serif !important;
  font-size: 26px !important;
  color: var(--gold-br) !important;
  text-shadow: 0 0 10px var(--p-col) !important;
}

/* Пузырьки внутри флакона */
.bk-bubbles {
  position: absolute;
  top: 0; left: 0; right: 0; bottom: 0;
  width: 100%; height: 100%;
  background-image: radial-gradient(circle, #fff 10%, transparent 10%);
  background-size: 8px 8px;
  opacity: 0.2;
  animation: potionBubbles 2s infinite linear;
}
@keyframes potionBubbles {
  from { background-position: 0 0; }
  to { background-position: 0 -16px; }
}

/* Бейдж редкости в углу карточки */
.pp-rare-badge {
  position: absolute;
  top: 0;
  right: 0;
  padding: 4px 12px;
  background: var(--p-col);
  color: #000;
  font-family: 'Cinzel', serif;
  font-size: 9px;
  font-weight: bold;
  border-radius: 0 0 0 10px;
  text-transform: uppercase;
}

/* Сетка для состава и эффектов */
.pp-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 15px;
  margin: 15px 0;
}

/* Ингредиенты */
.pp-ing {
  display: inline-block;
  background: rgba(255, 255, 255, 0.03);
  border: 1px dashed rgba(201, 168, 76, 0.1);
  padding: 3px 7px;
  border-radius: 4px;
  margin: 2px;
  font-size: 11px;
  color: var(--txt-dim);
}

/* Выделение эффектом */
.highlight {
  color: var(--p-col) !important;
  font-style: italic;
}
/* Декоративный элемент */
.pp-orn {
  text-align: center;
  color: var(--gold-dark);
  font-size: 18px;
  margin: 10px 0;
  opacity: 0.5;
}

/* Развернутая карточка */
.pp {
  cursor: pointer;
  transition: all 0.4s cubic-bezier(0.2, 1, 0.3, 1);
}
.pp.open {
  border-color: var(--p-col) !important;
  box-shadow: 0 0 25px var(--p-glow) !important;
  background: #0d0b08 !important;
}

/* Иконка стрелки для раскрытия */
.pp-arrow {
  position: absolute;
  right: 20px;
  top: 40px;
  color: var(--gold-dark);
  font-size: 14px;
  transition: 0.3s;
}
.pp-arrow.rotated {
  transform: rotate(180deg) scale(1.2);
}
/* Точки сложности */
.pp-dot {
  width: 8px;
  height: 8px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 50%;
  display: inline-block;
  margin-right: 4px;
}
.pp-dot.active {
  background: var(--p-col);
  box-shadow: 0 0 8px var(--p-col);
}

/* Свиток рецепта */
.recipe-box {
  background: rgba(0, 0, 0, 0.3);
  border: 1px solid rgba(201, 168, 76, 0.1);
  padding: 10px;
  border-radius: 6px;
  font-size: 0.85rem;
  line-height: 1.4;
  color: #b0a89e;
  font-style: italic;
}

/* Теги ингредиентов */
.ing-tag {
  display: inline-block;
  padding: 2px 8px;
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid var(--border);
  border-radius: 4px;
  font-size: 11px;
  margin: 2px;
}

/* Анимация раскрытия содержимого */
.pp-expanded-content {
  animation: magicUnfold 0.5s ease-out;
}
@keyframes magicUnfold {
  from {
    opacity: 0;
    max-height: 0;
    transform: scaleY(0);
  }
  to {
    opacity: 1;
    max-height: 500px;
    transform: scaleY(1);
  }
}

/* Эффекты для форм бутылочек */
.bk-body {
  transition: all 0.3s ease;
  margin: 0 auto;
}
.pp[data-form="round"] .bk-body {
  border-radius: 50% !important;
  width: 45px !important;
  height: 45px !important;
}
.pp[data-form="triangle"] .bk-body {
  clip-path: polygon(50% 0%, 0% 100%, 100% 100%);
  border-radius: 0 !important;
  width: 50px !important;
}
.pp[data-form="flask"] .bk-body {
  width: 35px !important;
  border-radius: 5px 5px 15px 15px !important;
}
.pp[data-form="poison"] .bk-body {
  background: rgba(50, 0, 0, 0.3) !important;
  border: 2px dashed var(--p-col) !important;
  position: relative;
}
.pp[data-form="poison"] .bk-liq::after {
  content: '☠';
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 12px;
  opacity: 0.5;
  color: white;
}
/* Оформление текста книги */
.book-content p {
  font-family: 'Crimson Text', serif;
  font-size: 1.1em;
  line-height: 1.6;
  margin-bottom: 15px;
  color: #e0d8c8;
  text-indent: 20px;
}
.full-book-text {
  padding: 15px;
  font-family: 'Crimson Text', serif;
  line-height: 1.7;
  font-size: 18px;
  color: var(--txt);
  text-align: justify;
}
.full-book-text p {
  margin-bottom: 1.2em;
  text-indent: 20px;
}
.book-author-sig {
  font-family: 'UnifrakturMaguntia', cursive;
  font-size: 20px;
  color: var(--gold);
  text-align: center;
  margin-bottom: 30px;
  border-bottom: 1px double var(--gold2);
  padding-bottom: 10px;
}
/* Лоадер книги */
.book-loading {
  text-align: center;
  padding: 50px;
  color: var(--gold);
}
.book-loading i {
  font-size: 40px;
  margin-bottom: 15px;
}

/* Общие стили для контейнера текста книги */
.book-content {
    font-family: 'Georgia', serif; /* Классический книжный шрифт */
    line-height: 1.6;
    color: #2c1e11; /* Цвет чернил */
    background: #fdf5e6; /* Цвет старого пергамента */
    padding: 40px;
    border-radius: 5px;
    box-shadow: inset 0 0 50px rgba(0, 0, 0, 0.1);
}

/* Заголовки глав */
.book-content h2 {
    font-family: 'Garamond', serif;
    text-align: center;
    text-transform: uppercase;
    border-bottom: 1px solid #8b4513;
    margin-bottom: 25px;
    color: #5d3a1a;
}

/* Абзацы внутри книги */
.book-content p {
    margin-bottom: 15px;
    text-indent: 25px;
    text-align: justify; /* Выравнивание по ширине */
}

/* Выделение заклинаний */
.book-content span.spell {
    font-weight: bold;
    font-style: italic;
    color: #1a3a5c;
}

/* Общие стили для страницы книги (пергамент) */
.book-page {
    background-color: #f2e8c9;
    background-image: 
        radial-gradient(circle at 50% 50%, rgba(0,0,0,0) 0%, rgba(0,0,0,0.05) 100%),
        url('https://www.transparenttextures.com/patterns/old-paper.png');
    padding: 40px;
    box-shadow: inset 0 0 40px rgba(139, 69, 19, 0.3);
    border-left: 2px solid rgba(0, 0, 0, 0.1);
    color: #2c1e11;
    font-family: 'EB Garamond', serif;
    line-height: 1.6;
    position: absolute;
    width: 100%;
    height: 100%;
    transition: transform 0.8s cubic-bezier(0.645, 0.045, 0.355, 1);
    transform-origin: left;
}

/* Первая буква (буквица) */
.book-page::first-letter {
    float: left;
    font-size: 3em;
    line-height: 1;
    margin-right: 10px;
    color: #8b4513;
    font-family: 'MedievalSharp', cursive;
}

/* Анимация перелистывания страниц */
.page-flipped {
    transform: rotateY(-180deg);
}

/* Декоративные края страниц */
.book-page::after {
    content: "";
    position: absolute;
    top: 0; bottom: 0; left: 0; right: 0;
    border: 1px solid rgba(139, 69, 19, 0.1);
    pointer-events: none;
}

/* Контейнер всей книги с эффектом 3D */
.magic-book {
    width: 900px;
    height: 600px;
    background: #2c1e11; /* Цвет старого дерева */
    display: flex;
    justify-content: center;
    align-items: center;
    box-shadow: 20px 20px 50px rgba(0, 0, 0, 0.7);
    perspective: 1500px;
    border-radius: 10px;
}

/* Корешок книги (Центральная линия) */
.book-spine {
    width: 20px;
    height: 100%;
    background: #1a0f08;
    box-shadow: inset 0 0 10px rgba(0, 0, 0, 0.5);
    position: absolute;
    left: 50%;
    transform: translateX(-50%);
}

/* Страницы книги (левая и правая) */
.book-page {
    background-color: #f4ecd8;
    background-image: 
        radial-gradient(circle at 50% 50%, rgba(0,0,0,0) 0%, rgba(0,0,0,0.05) 100%),
        url('https://www.transparenttextures.com/patterns/old-paper.png');
    padding: 40px;
    box-shadow: inset 0 0 40px rgba(139, 69, 19, 0.3);
    border-left: 1px solid rgba(0,0,0,0.1);
    color: #2c1e11;
    font-family: 'EB Garamond', serif;
    line-height: 1.8;
    position: absolute;
    width: 50%;
    height: 100%;
    transition: transform 0.8s cubic-bezier(0.645, 0.045, 0.355, 1);
    transform-origin: left;
}

/* Левая страница */
.book-page-L {
    left: 0;
}

/* Правая страница */
.book-page-R {
    right: 0;
}

/* Буквица (первая буква главы) */
.book-page::first-letter {
    float: left;
    font-size: 3em;
    line-height: 1;
    margin-right: 10px;
    color: #8b4513;
    font-family: 'MedievalSharp', cursive;
}

/* Анимация перелистывания страниц */
.page-flipped {
    transform: rotateY(-180deg);
}

/* Обожженные края (декор) */
.book-page::after {
    content: "";
    position: absolute;
    top: 0; bottom: 0; left: 0; right: 0;
    border: 1px solid rgba(139, 69, 19, 0.1);
    pointer-events: none;
}

/* Контейнер для всей книги (центральное позиционирование) */
.library-shelf {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
    gap: 50px;
    padding: 100px;
    justify-items: center;
    align-items: start;
}

/* Обложка книги (3D эффект) */
.book-item {
    width: 180px;
    height: 260px;
    position: relative;
    cursor: pointer;
    transition: transform 0.4s ease;
    transform-style: preserve-3d;
    box-shadow: 5px 5px 20px rgba(0, 0, 0, 0.5);
}

/* Эффект при наведении */
.book-item:hover {
    transform: rotateY(-25deg) scale(1.1);
}

/* Корешок книги (спина) */
.book-item::before {
    content: "";
    position: absolute;
    left: 0;
    top: 0;
    width: 30px;
    height: 100%;
    background: rgba(0,0,0,0.3);
    transform: translateX(-15px) rotateY(-90deg);
    border-radius: 5px 0 0 5px;
}

/* Оформление обложки */
.book-cover-design {
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: space-around;
    align-items: center;
    padding: 15px;
    border: 2px solid rgba(255, 215, 0, 0.3);
    border-radius: 0 10px 10px 0;
    text-align: center;
    color: #ffd700;
    font-family: 'Cinzel', serif;
}

.book-cover-design i {
    font-size: 40px;
    margin-bottom: 10px;
}
.book-cover-design h3 {
    font-size: 16px;
    text-transform: uppercase;
}
/* Переменные для золотого тиснения (Гримуар) */
:root {
  --g-gold: #f1e4bc;      /* Золотой текст */
  --g-border: #9c7b4f;    /* Золотая рамка и узоры */
  --g-gold-dim: #9c8e76;  /* Приглушенное золото */
}

/* 1. Сетка для книг в стиле Гримуаров */
.bgrid-grimoire {
  display: grid;
  grid-template-columns: repeat(2, 1fr); /* Две колонки, как на скрине */
  gap: 20px;
  padding: 15px;
  perspective: 1000px;
}

/* 2. Обложка Книги (Кожаная База) */
.bc-grimoire {
  width: 100%;             /* Занимает всю ширину колонки */
  height: 240px;           /* Оптимальная высота */
  border-radius: 5px;      /* Скругление */
  position: relative;
  overflow: hidden;
  box-shadow: 10px 10px 25px rgba(0,0,0,0.8); /* Тяжелая тень */
  transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
  cursor: pointer;
  color: var(--g-gold);
  font-family: 'Cinzel', serif; /* Шрифт как на скрине */
  padding: 15px;
}

/* Текстура кожи */
.bc-grimoire::before {
  content: '';
  position: absolute; inset: 0;
  opacity: 0.2;
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='4' height='4' viewBox='0 0 4 4'%3E%3Cpath fill='%239C92AC' fill-opacity='0.4' d='M1 3h1v1H1V3zm2-2h1v1H3V1z'%3E%3C/path%3E%3C/svg%3E");
}

/* 3. Корешок и Внутренняя золотая рамка */
.bc-grimoire .spine {
  position: absolute;
  left: 0; top: 0; bottom: 0;
  width: 12px;
  background: rgba(0,0,0,0.3);
  border-right: 1px solid rgba(255,255,255,0.05);
  z-index: 2;
}

.bc-grimoire .inner-border {
  position: absolute;
  top: 12px; left: 25px; right: 12px; bottom: 12px;
  border: 1px double var(--g-border);
  opacity: 0.8;
  z-index: 1;
}

/* 4. Угловые Узоры (Рыбки) */
.bc-grimoire .corner {
  position: absolute;
  font-size: 11px;
  color: var(--g-border);
  z-index: 2;
}
.bc-grimoire .tl { top: 15px; left: 28px; }
.bc-grimoire .tr { top: 15px; right: 15px; }
.bc-grimoire .bl { bottom: 15px; left: 28px; }
.bc-grimoire .br { bottom: 15px; right: 15px; }

/* 5. Наполнение Обложки (Золотое тиснение) */

.bc-grimoire .cover-icon {
  position: absolute;
  top: 25%; left: 50%;
  transform: translateX(-50%);
  font-size: 20px;
  color: var(--g-border);
  z-index: 3;
}

.bc-grimoire .cover-title {
  position: absolute;
  top: 35%; left: 25px; right: 12px;
  font-size: 14px;
  text-align: center;
  z-index: 3;
  text-transform: uppercase;
  letter-spacing: 1px;
  line-height: 1.1;
  text-shadow: 1px 1px 2px #000;
}

.bc-grimoire .cover-sub {
  font-family: 'Crimson Text', serif; /* Простой шрифт автора */
  position: absolute;
  top: 60%; left: 25px; right: 12px;
  font-size: 10px;
  text-align: center;
  z-index: 3;
  color: var(--g-gold-dim);
  letter-spacing: 2px;
}

.bc-grimoire .cover-meta {
  font-family: 'Crimson Text', serif;
  position: absolute;
  bottom: 25px; left: 25px; right: 12px;
  font-size: 9px;
  text-align: center;
  z-index: 3;
  color: var(--g-gold-dim);
  opacity: 0.7;
  letter-spacing: 1px;
}

/* Декоративная линия */
.bc-grimoire .cover-line {
    width: 40px;
    height: 1px;
    background: var(--g-border);
    position: absolute;
    top: 55%; left: 50%;
    transform: translateX(-50%);
    z-index: 2;
}

/* Ромбики ◆ ◇ ◆ */
.bc-grimoire .cover-orn-row {
  position: absolute;
  bottom: 45px; left: 50%;
  transform: translateX(-50%);
  font-size: 8px;
  color: var(--g-border);
  letter-spacing: 3px;
  z-index: 3;
}

/* 6. Эффекты при наведении */
.book-item-wrap:hover .bc-grimoire {
  transform: translateY(-8px) scale(1.02);
  box-shadow: 15px 15px 35px rgba(0,0,0,0.9), 0 0 20px rgba(201,168,76,0.15);
}
/* Модальное окно на весь экран */
.book-modal {
    position: fixed;
    inset: 0;
    background: rgba(0,0,0,0.9);
    z-index: 1000;
    flex-direction: column;
    animation: fadeIn 0.3s;
}

/* Контейнер для страниц */
.book-content {
    flex: 1;
    overflow-y: auto;
    padding: 20px;
    display: flex;
    justify-content: center;
}

/* Разворот книги */
.page-spread {
    background: #f4e4bc; /* Цвет старой бумаги */
    background-image: url('https://www.transparenttextures.com/patterns/paper-fibers.png');
    color: #2c1e14;
    width: 95%;
    max-width: 800px;
    min-height: 500px;
    margin-bottom: 50px;
    display: flex;
    flex-direction: column;
    box-shadow: 0 20px 50px rgba(0,0,0,0.5);
    border-radius: 5px;
    padding: 30px;
    position: relative;
}

/* Эффект разделения страниц посередине */
.page-spread::after {
    content: '';
    position: absolute;
    left: 50%; top: 0; bottom: 0;
    width: 2px;
    background: linear-gradient(to right, rgba(0,0,0,0.1), rgba(0,0,0,0.05), rgba(0,0,0,0.1));
}

.page h2 { font-family: 'Cinzel', serif; color: #8b0000; margin-bottom: 20px; text-align: center; }
.page-text p { line-height: 1.6; margin-bottom: 15px; font-size: 16px; text-align: justify; }

/* Буквица (первая буква главы большая) */
.page-text p:first-child::first-letter {
    float: left;
    font-size: 50px;
    line-height: 40px;
    padding-top: 4px;
    padding-right: 8px;
    font-family: 'Cinzel', serif;
    color: #8b0000;
}

.page-header { font-size: 10px; text-transform: uppercase; opacity: 0.5; text-align: center; margin-bottom: 20px; }
.page-footer { text-align: center; font-style: italic; margin-top: 20px; opacity: 0.5; }

@keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
#br {
    position: fixed !important;
    top: 0; left: 0; width: 100%; height: 100%;
    background: #0a0a0a !important; /* Глубокий черный фон */
    z-index: 99999 !important; /* Максимальный приоритет */
    display: none; 
    flex-direction: column;
}

#brB {
    flex: 1;
    overflow-y: auto;
    padding: 30px 20px;
    background: radial-gradient(circle, #1a1a1a 0%, #050505 100%);
}

.book-header {
    padding: 15px;
    background: #111;
    border-bottom: 1px solid var(--gold);
    display: flex;
    justify-content: space-between;
    align-items: center;
}
/* Стиль контейнера открытой книги */
.book-reader-wrap {
    width: 100%;
    max-width: 800px;
    margin: 0 auto;
    padding: 20px;
    perspective: 1000px;
}

/* Эффект старой бумаги */
.parchment-page {
    background: #f4e4bc;
    background-image: url('https://www.transparenttextures.com/patterns/paper-fibers.png');
    color: #2c1e14;
    padding: 40px;
    box-shadow: 0 20px 40px rgba(0,0,0,0.6), inset 0 0 100px rgba(139, 69, 19, 0.15);
    border-radius: 2px;
    position: relative;
    min-height: 80vh;
    border: 1px solid #d2b48c;
}

/* Декоративная рамка внутри страницы */
.parchment-page::after {
    content: '';
    position: absolute;
    top: 15px; left: 15px; right: 15px; bottom: 15px;
    border: 1px solid rgba(139, 69, 19, 0.2);
    pointer-events: none;
}

/* Заголовки глав */
.book-reader-wrap h2 {
    font-family: 'Cinzel', serif;
    text-align: center;
    font-size: 24px;
    margin-bottom: 20px;
    color: #5d4037;
    border-bottom: 1px double #5d4037;
    display: block;
    padding-bottom: 10px;
}

/* Текст книги */
.book-reader-wrap p {
    font-family: 'Crimson Text', serif;
    font-size: 19px;
    line-height: 1.6;
    margin-bottom: 15px;
    text-indent: 30px;
    text-align: justify;
}

/* Буквица (первая буква первой главы) */
.book-reader-wrap .bp:first-of-type p:first-of-type::first-letter {
    float: left;
    font-size: 60px;
    line-height: 50px;
    padding-top: 4px;
    padding-right: 10px;
    font-family: 'UnifrakturMaguntia', cursive;
    color: var(--book-color, #8b0000);
}

/* Нижний колонтитул */
.book-footer-decor {
    text-align: center;
    font-family: 'Cinzel';
    font-size: 12px;
    margin-top: 30px;
    opacity: 0.6;
}
/* Обновленный контейнер читалки */
.book-reader-wrap {
    width: 100%;
    max-width: 500px;
    margin: 0 auto;
    padding: 15px;
    /* Переменная по умолчанию, если что-то пойдет не так */
    --active-book-color: #c9a84c; 
}

/* Эффект магического пергамента */
.parchment-page {
    background: #fdf5e6; /* Более светлый оттенок для читаемости */
    background-image: 
        url('https://www.transparenttextures.com/patterns/paper-fibers.png'),
        radial-gradient(circle at center, rgba(255,255,255,0.5) 0%, rgba(210,180,140,0.3) 100%);
    color: #1a1a1a; /* Темный текст на светлом фоне — залог читаемости */
    padding: 30px 20px;
    border-radius: 5px;
    position: relative;
    box-shadow: 
        0 0 20px rgba(0,0,0,0.5),
        inset 0 0 50px rgba(0,0,0,0.1);
    border: 8px solid transparent;
    /* Магия: граница страницы теперь светится цветом обложки */
    border-image: linear-gradient(to bottom, var(--active-book-color), #000) 1;
    min-height: 85vh;
}

/* Заголовок книги внутри */
.inner-book-title {
    font-family: 'Cinzel', serif;
    font-size: 22px;
    color: var(--active-book-color); /* Цвет заголовка как у обложки */
    text-align: center;
    text-shadow: 1px 1px 0px rgba(0,0,0,0.1);
    margin-bottom: 5px;
}

/* Буквица (первая буква) */
.book-text-body p:first-of-type::first-letter {
    float: left;
    font-family: 'UnifrakturMaguntia', cursive;
    font-size: 55px;
    line-height: 45px;
    padding-right: 8px;
    color: var(--active-book-color); /* Буквица красится в цвет книги */
    text-shadow: 1px 1px 2px rgba(0,0,0,0.2);
}

/* Текст */
.book-text-body p {
    font-family: 'Crimson Text', serif;
    font-size: 18px;
    line-height: 1.5;
    margin-bottom: 15px;
    color: #2c2c2c;
    text-align: justify;
}

/* Декоративный разделитель */
.magic-sep {
    text-align: center;
    color: var(--active-book-color);
    font-size: 20px;
    margin: 20px 0;
    opacity: 0.7;
}
/* Контейнер магии */
.book-reader-wrap {
    --active-book-color: #8b0000; /* Меняется через JS */
    padding: 20px 10px;
    background: transparent;
}

/* Страница как в фильме */
.parchment-page {
    background: #fdf5e6; /* Светлый пергамент */
    background-image: url('https://www.transparenttextures.com/patterns/paper-fibers.png');
    color: #1a1a1a; /* Глубокий темный для чтения */
    padding: 35px 25px;
    border-radius: 4px;
    box-shadow: 0 0 30px rgba(0,0,0,0.7), inset 0 0 60px rgba(0,0,0,0.1);
    position: relative;
    border: 1px solid #d2b48c;
    min-height: 80vh;
}

/* Светящаяся рамка под цвет книги */
.parchment-page::before {
    content: '';
    position: absolute;
    top: 10px; left: 10px; right: 10px; bottom: 10px;
    border: 2px solid var(--active-book-color);
    opacity: 0.2;
    pointer-events: none;
}

.book-internal-header { text-align: center; margin-bottom: 25px; }
.book-internal-author { font-family: 'Cinzel'; font-size: 13px; margin-top: 8px; letter-spacing: 2px; }
.magic-divider { color: var(--active-book-color); margin-top: 10px; font-size: 18px; }

/* Заголовок главы */
.chapter-title {
    font-family: 'Cinzel', serif;
    color: var(--active-book-color);
    text-align: center;
    font-size: 20px;
    margin: 20px 0;
}

/* Основной текст */
.book-text-body p {
    font-family: 'Crimson Text', serif;
    font-size: 18px;
    line-height: 1.6;
    margin-bottom: 15px;
    text-align: justify;
    color: #2c2c2c;
}

/* Магическая буквица */
.book-chapter-section:first-of-type p:first-of-type::first-letter {
    float: left;
    font-family: 'UnifrakturMaguntia', cursive;
    font-size: 55px;
    line-height: 45px;
    padding-right: 8px;
    color: var(--active-book-color);
}

.chapter-end-ornament { 
    text-align: center; 
    color: var(--active-book-color); 
    opacity: 0.4; 
    font-size: 20px; 
    margin-bottom: 30px; 
}

.book-internal-footer {
    text-align: center;
    font-family: 'Cinzel';
    font-size: 10px;
    opacity: 0.5;
    margin-top: 40px;
    border-top: 1px solid rgba(0,0,0,0.1);
    padding-top: 10px;
}
/* АЛХИМИЧЕСКИЙ МАНУСКРИПТ: ВЫСШИЙ ПОРЯДОК */
.custom-dossier {
    margin: 40px 10px;
    padding: 45px 25px;
    position: relative;
    
    /* Эффект старого пергамента с пятнами */
    background: #f4ecd8;
    background-image: 
        radial-gradient(at 20% 30%, rgba(139, 69, 19, 0.05) 0%, transparent 50%),
        radial-gradient(at 80% 70%, rgba(201, 168, 76, 0.05) 0%, transparent 50%),
        url('https://www.transparenttextures.com/patterns/paper-fibers.png');
    
    /* Рваные края пергамента */
    clip-path: polygon(
        0% 2%, 5% 0%, 10% 3%, 25% 1%, 40% 4%, 60% 1%, 80% 3%, 95% 0%, 100% 4%, 
        98% 25%, 100% 45%, 97% 65%, 100% 85%, 98% 100%, 
        80% 97%, 65% 100%, 45% 98%, 25% 100%, 10% 97%, 0% 100%, 
        2% 80%, 0% 60%, 3% 40%, 1% 20%
    );

    border: 1px solid rgba(139, 69, 19, 0.2);
    box-shadow: 2px 10px 20px rgba(0,0,0,0.15);
    transition: transform 0.4s ease;
}

/* Алхимическая печать (водяной знак на фоне) */
.custom-dossier::before {
    content: "🜁 🜃 🜂 🜄"; /* Символы стихий */
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    font-size: 80px;
    color: rgba(139, 69, 19, 0.03);
    font-family: serif;
    pointer-events: none;
    white-space: nowrap;
}

.dossier-title {
    text-align: center;
    font-family: 'Cinzel', serif;
    font-size: 22px;
    font-weight: 900;
    text-transform: uppercase;
    letter-spacing: 5px;
    margin-bottom: 30px;
    
    /* Эффект вдавленных золотых чернил */
    color: #4a3728;
    text-shadow: 0px 1px 1px rgba(255,255,255,0.8);
    background: linear-gradient(135deg, #4a3728 0%, var(--active-book-color) 50%, #4a3728 100%);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    
    border-bottom: 1px solid var(--active-book-color);
    padding-bottom: 15px;
}

.dossier-row {
    margin-bottom: 22px;
    padding-left: 20px;
    border-left: 1px solid var(--active-book-color);
    position: relative;
}

/* Магические точки связи */
.dossier-row::after {
    content: "";
    position: absolute;
    left: -4px;
    top: 0;
    width: 7px;
    height: 7px;
    background: var(--active-book-color);
    border-radius: 50%;
    box-shadow: 0 0 5px var(--active-book-color);
}

.d-label {
    color: var(--active-book-color);
    font-family: 'Cinzel', serif;
    font-size: 11px;
    font-weight: bold;
    text-transform: uppercase;
    letter-spacing: 2px;
    margin-bottom: 5px;
    display: block;
    opacity: 0.8;
}

.d-value {
    color: #1a1510; /* Глубокий чернильный цвет */
    font-family: 'Crimson Text', serif;
    font-size: 19px;
    line-height: 1.3;
    font-weight: 600;
    font-style: italic;
    display: block;
}

/* Разделитель с символом философского камня */
.dossier-line {
    display: flex;
    align-items: center;
    justify-content: center;
    margin: 25px 0;
}

.dossier-line::before {
    content: "🜔"; /* Символ алхимии */
    font-size: 24px;
    color: var(--active-book-color);
    opacity: 0.6;
    filter: drop-shadow(0 0 2px var(--active-book-color));
}

/* Алхимическая сургучная печать */
.custom-dossier::after {
    content: "⚗";
    position: absolute;
    bottom: 15px;
    right: 15px;
    width: 50px;
    height: 50px;
    background: rgba(139, 69, 19, 0.1);
    border: 2px double var(--active-book-color);
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 25px;
    color: var(--active-book-color);
    transform: rotate(-20deg);
    opacity: 0.6;
}
/* ВЕЛИКОЕ ДЕЛАНИЕ: МАГИЧЕСКИЙ МАНУСКРИПТ */
.alchemy-stages {
    margin: 40px 10px;
    padding: 30px 20px;
    background: #fdfaf1; /* Цвет старого пергамента */
    border: 1px solid #d2b48c;
    border-radius: 4px;
    box-shadow: 
        0 10px 30px rgba(0,0,0,0.1),
        inset 0 0 60px rgba(139, 69, 19, 0.05);
    position: relative;
    overflow: hidden;
}

/* Эффект водяного знака (алхимический круг) на фоне */
.alchemy-stages::before {
    content: "";
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 250px;
    height: 250px;
    background-image: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100"><circle cx="50" cy="50" r="45" fill="none" stroke="rgba(139,69,19,0.03)" stroke-width="0.5"/><path d="M50,5 L50,95 M5,50 L95,50" stroke="rgba(139,69,19,0.03)" stroke-width="0.5"/></svg>');
    opacity: 0.6;
    pointer-events: none;
}

.dossier-header-stages {
    text-align: center;
    color: var(--active-book-color);
    font-family: 'Cinzel', serif;
    font-size: 18px;
    font-weight: 900;
    text-transform: uppercase;
    letter-spacing: 4px;
    margin-bottom: 30px;
    text-shadow: 0 0 10px var(--active-book-color);
    border-bottom: 1px solid rgba(201, 168, 76, 0.3);
    padding-bottom: 15px;
}

/* Строка этапа (вместо <tr>) */
.stage-entry {
    display: flex;
    margin-bottom: 25px;
    padding-bottom: 15px;
    border-bottom: 1px solid rgba(139, 69, 19, 0.1);
    position: relative;
    z-index: 1;
}

.stage-entry:last-child {
    border-bottom: none;
    margin-bottom: 0;
}

/* Левая колонка: Имя и Символ */
.stage-meta {
    width: 35%;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding-right: 15px;
    border-right: 1px solid rgba(139, 69, 19, 0.1);
}

.stage-title {
    font-family: 'Cinzel', serif;
    font-size: 16px;
    font-weight: 900;
    text-transform: uppercase;
    letter-spacing: 2px;
    margin-bottom: 8px;
    /* Эффект каллиграфии с тиснением */
    background: linear-gradient(135deg, #4a3728 0%, var(--active-book-color) 50%, #4a3728 100%);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    filter: drop-shadow(0 0 2px rgba(255,140,0,0.2));
}

.stage-icon {
    font-size: 30px;
    margin-bottom: 10px;
    color: var(--active-book-color);
    text-shadow: 0 0 15px var(--active-book-color);
    opacity: 0.8;
}

.stage-symbols {
    font-family: 'Crimson Text', serif;
    font-style: italic;
    font-size: 12px;
    color: #5d4037;
    text-align: center;
    line-height: 1.2;
}

/* Правая колонка: Описание и Металл */
.stage-info {
    flex: 1;
    padding-left: 15px;
}

.stage-metal {
    color: var(--active-book-color);
    font-family: 'Cinzel', serif;
    font-size: 10px;
    font-weight: bold;
    text-transform: uppercase;
    letter-spacing: 1px;
    display: flex;
    align-items: center;
    gap: 5px;
    margin-bottom: 8px;
    opacity: 0.8;
}

.stage-psy {
    color: #2b1e16;
    font-family: 'Crimson Text', serif;
    font-size: 16px;
    line-height: 1.3;
    font-weight: 600;
}

/* Декоративный символ в конце */
.alchemy-stages::after {
    content: "⚗";
    position: absolute;
    bottom: -15px;
    right: 15px;
    font-size: 40px;
    color: var(--active-book-color);
    opacity: 0.1;
    transform: rotate(-15deg);
}
/* ============================================================
   ВЕЛИКОЕ ДЕЛАНИЕ: ПОЛНЫЙ ЦСС МАГИЧЕСКОГО МАНУСКРИПТА
   ============================================================ */

.alchemy-scroll {
    margin: 50px 10px;
    padding: 60px 30px;
    background: #f2e6d0; /* Базовый цвет старой бумаги */
    position: relative;
    border: 1px solid #d2b48c;
    
    /* Эффект рваного края */
    clip-path: polygon(0% 2%, 2% 0%, 98% 1%, 100% 3%, 99% 97%, 98% 100%, 2% 99%, 0% 97%);
    
    /* Глубокие тени и эффект засаленного пергамента */
    box-shadow: 
        10px 15px 40px rgba(0,0,0,0.3),
        inset 0 0 120px rgba(139, 69, 19, 0.15);
    
    /* Наслоение текстур: волокна бумаги + акварельные пятна */
    background-image: 
        url('https://www.transparenttextures.com/patterns/paper-fibers.png'),
        radial-gradient(circle at 20% 30%, rgba(139, 69, 19, 0.05) 0%, transparent 50%),
        radial-gradient(circle at 80% 70%, rgba(139, 69, 19, 0.05) 0%, transparent 50%);
}

/* Эффект магической пыли и потертости */
.alchemy-scroll::before {
    content: "";
    position: absolute;
    inset: 0;
    background: url('https://www.transparenttextures.com/patterns/stardust.png');
    opacity: 0.15;
    pointer-events: none;
    z-index: 1;
}

/* Заголовок: эффект вдавленного клейма */
.scroll-header {
    font-family: 'Cinzel', serif;
    font-size: 28px;
    text-align: center;
    color: #3d2b1f;
    letter-spacing: 12px;
    margin-bottom: 50px;
    text-transform: uppercase;
    text-shadow: 1px 1px 1px rgba(255,255,255,0.5), -1px -1px 1px rgba(0,0,0,0.3);
    border-bottom: 1px double #d2b48c;
    display: block;
    position: relative;
    z-index: 2;
}

.alchemy-step {
    display: flex;
    margin-bottom: 50px;
    position: relative;
    align-items: flex-start;
    z-index: 2;
}

/* Колонка с магической печатью */
.step-seal {
    width: 80px;
    display: flex;
    flex-direction: column;
    align-items: center;
    margin-right: 25px;
}

/* Крупные глифы как на гравюрах */
.seal-glyph {
    font-size: 55px;
    line-height: 1;
    color: #2b1e16;
    opacity: 0.85;
    filter: sepia(0.5) drop-shadow(0 0 2px rgba(0,0,0,0.3));
    transition: transform 0.5s cubic-bezier(0.4, 0, 0.2, 1);
}

.alchemy-step:hover .seal-glyph {
    transform: rotate(8deg) scale(1.1);
    color: var(--active-book-color);
}

.seal-label {
    font-family: 'Cinzel', serif;
    font-size: 10px;
    text-transform: uppercase;
    color: #8b4513;
    margin-top: 8px;
    letter-spacing: 1px;
    opacity: 0.7;
}

/* Контент этапа */
.step-content {
    flex: 1;
    position: relative;
}

.step-name {
    font-family: 'Cinzel', serif;
    font-size: 24px;
    margin: 0;
    letter-spacing: 3px;
    text-transform: uppercase;
}

/* Цвета чернил */
.nigredo-ink { color: #1a1a1a; }
.albedo-ink { color: #4a5a6a; }
.citrinitas-ink { color: #8a751b; }
.rubedo-ink { color: #7b1a1a; }

.step-symbols {
    font-family: 'Crimson Text', serif;
    font-style: italic;
    font-size: 15px;
    color: #5d4037;
    margin: 5px 0 12px 0;
    display: block;
    opacity: 0.9;
}

/* Описание: Исправляем проблему с красными буквами */
.step-psy {
    font-family: 'Crimson Text', serif;
    font-size: 19px;
    color: #2b1e16;
    line-height: 1.4;
    margin: 0;
    position: relative;
    padding-left: 20px;
}

/* Полная отмена буквицы (Drop Cap) для связности текста */
.step-psy::first-letter {
    all: unset !important;
    initial-letter: normal !important;
    float: none !important;
    font-size: inherit !important;
    font-family: inherit !important;
    color: inherit !important;
    padding: 0 !important;
    margin: 0 !important;
    text-transform: none !important;
}

/* Декоративный указатель перед текстом вместо буквицы */
.step-psy::before {
    content: "◈";
    position: absolute;
    left: 0;
    top: 2px;
    font-size: 12px;
    color: var(--active-book-color);
    opacity: 0.5;
}

/* Пометка на полях (металл) */
.step-metal-tag {
    position: absolute;
    right: 0;
    top: -22px;
    font-family: 'Dancing Script', cursive;
    font-size: 18px;
    color: #8b4513;
    transform: rotate(-3deg);
    opacity: 0.6;
    border-bottom: 1px solid rgba(139, 69, 19, 0.2);
    pointer-events: none;
}

/* Финальная подпись мастера */
.alchemy-signature {
    text-align: right;
    font-family: 'Dancing Script', cursive;
    font-size: 18px;
    color: #3d2b1f;
    opacity: 0.5;
    margin-top: 40px;
    padding-right: 20px;
    font-style: italic;
}
.alchemy-analogies {
    margin: 30px 0;
    border-top: 1px double #d2b48c;
    padding-top: 20px;
}

.analogy-item {
    margin-bottom: 25px;
}

.analogy-func {
    font-family: 'Cinzel', serif;
    color: var(--active-book-color);
    text-align: center;
    font-size: 16px;
    letter-spacing: 2px;
    margin-bottom: 10px;
    text-transform: uppercase;
}

.analogy-row {
    display: flex;
    gap: 20px;
    justify-content: space-between;
}

.analogy-magic, .analogy-alchemy {
    flex: 1;
    font-family: 'Crimson Text', serif;
    font-size: 15px;
    line-height: 1.3;
}

.analogy-magic span, .analogy-alchemy span {
    display: block;
    font-size: 10px;
    text-transform: uppercase;
    opacity: 0.5;
    margin-bottom: 4px;
    font-family: 'Cinzel', serif;
}

.analogy-alchemy {
    font-style: italic;
    color: #5d4037;
    border-left: 1px solid rgba(139, 69, 19, 0.1);
    padding-left: 15px;
}
/* 1. ГРИМУАР: Единое полотно страницы */
[style*="#1c1c1c"], .shadow-rites-book {
    background-color: #070707 !important;
    /* Текстура грубой кожи с эффектом естественного старения */
    background-image: 
        radial-gradient(circle at 50% 50%, rgba(20, 10, 30, 0.2), transparent),
        url('https://www.transparenttextures.com/patterns/dark-leather.png') !important;
    
    /* Переплет: глубокая тень слева имитирует изгиб к корешку */
    border-left: 15px solid #030303 !important;
    box-shadow: 
        inset 0 0 100px #000, 
        30px 30px 80px rgba(0,0,0,0.9) !important;
    
    padding: 80px 50px !important;
    position: relative;
    overflow: hidden;
}

/* 2. ЗАГОЛОВОК: "Вдавленное золото" (Никакого неона!) */
[style*="#1c1c1c"] h1, 
[style*="#1c1c1c"] h2 {
    font-family: "Cinzel", "Georgia", serif !important;
    color: #7d6b4a !important; /* Глухое, старое золото */
    text-transform: uppercase;
    letter-spacing: 0.5em;
    text-align: center;
    /* Эффект рельефа: светлая кайма снизу, темная сверху */
    text-shadow: 
        -1px -1px 1px rgba(0,0,0,0.8), 
        1px 1px 1px rgba(255,255,255,0.05) !important;
    margin-bottom: 70px !important;
    position: relative;
}

/* Магический разделитель, встроенный в заголовок */
[style*="#1c1c1c"] h1::after {
    content: " ϟ ⛧ ϟ ";
    display: block;
    font-size: 14px;
    letter-spacing: 2.5em;
    margin-top: 25px;
    color: #4b0082;
    opacity: 0.2;
    filter: blur(0.5px);
}

/* 3. ТЕКСТ: Чернила, ушедшие вглубь материала */
[style*="#1c1c1c"] p {
    color: #857f76 !important; /* Цвет пепла и пыли */
    font-size: 1.15em !important;
    line-height: 2 !important;
    text-align: justify;
    margin-bottom: 40px !important;
    /* Эффект "Letterpress": текст кажется вбитым в кожу */
    text-shadow: 1px 1px 1px rgba(0,0,0,1) !important;
    position: relative;
    z-index: 2;
}

/* 4. СИМВОЛЫ КАК ЧАСТЬ ВЕРСТКИ (Интеграция) */

/* Буквица (Ψ) — теперь это начало пути, вплетенное в абзац */
[style*="#1c1c1c"] p::first-letter {
    float: left;
    font-size: 4em;
    line-height: 0.7;
    margin: 12px 18px 0 0;
    color: #2e004f !important; /* Очень темный фиолетовый */
    /* Мягкое "тление" вместо яркого света */
    text-shadow: 
        0 0 15px rgba(75, 0, 130, 0.3),
        1px 1px 0px rgba(255,255,255,0.03) !important;
}

/* Фоновая Дельта (Δ) — теперь это не картинка, а РЕЛЬЕФ страницы */
[style*="#1c1c1c"]::before {
    content: "Δ";
    position: absolute;
    top: 50%; left: 50%;
    transform: translate(-50%, -50%) rotate(-5deg);
    font-size: 650px;
    /* Цвет почти сливается с кожей, виден только контур за счет теней */
    color: transparent !important;
    -webkit-text-stroke: 1px rgba(255,255,255,0.02);
    text-shadow: 10px 10px 30px rgba(0,0,0,0.5) !important;
    pointer-events: none;
    z-index: 1;
}

/* 5. ФИНАЛЬНАЯ "ШЛИФОВКА" (Убираем эффект сайта) */

/* Наложение зернистости ПОВЕРХ всего */
[style*="#1c1c1c"]::after {
    content: "";
    position: absolute;
    inset: 0;
    background: url('https://www.transparenttextures.com/patterns/stardust.png');
    opacity: 0.15;
    mix-blend-mode: overlay; /* Текст и фон становятся единым целым */
    pointer-events: none;
    z-index: 5;
}

/* Убираем лишний контраст, добавляем атмосферу старины */
[style*="#1c1c1c"] {
    filter: sepia(0.1) contrast(1.05) brightness(0.95);
}
.faculty-card {
    --f-color: #222; /* Значение по умолчанию */
    width: 300px;
    height: 400px;
    background: #0a0a0a;
    border: 2px solid var(--f-color);
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    position: relative;
    overflow: hidden;
    transition: 0.8s cubic-bezier(0.2, 1, 0.2, 1);
}

/* Эффект "Живого зверя" из эмодзи */
.animal-icon {
    font-size: 100px;
    filter: drop-shadow(0 0 15px var(--f-color));
    transition: 0.5s;
    z-index: 2;
    position: relative;
}

/* Наложение текстуры и объема на эмодзи */
.animal-icon::after {
    content: '';
    position: absolute;
    inset: 0;
    background: linear-gradient(45deg, rgba(255,255,255,0.4), transparent);
    mix-blend-mode: overlay;
    pointer-events: none;
}

/* Динамический фон-сияние */
.card-glow {
    position: absolute;
    width: 200%;
    height: 200%;
    background: radial-gradient(circle, var(--f-color) 0%, transparent 70%);
    opacity: 0.15;
    animation: rotate 10s linear infinite;
}

@keyframes rotate {
    from { transform: rotate(0deg); }
    to { transform: rotate(360deg); }
}
/* 1. ГЛАВНЫЙ СТИЛЬ ТЕКСТА (ЧИТАЕМОСТЬ И КЛАСС) */
.brb:has(.fa-heart), 
.uc:has(.fa-heart) {
    /* Основные цвета */
    --gold-leaf: #dfbb85; /* Золотое тиснение */
    --love-pink: #f06292; /* Тот самый розовый */
    --soft-text: #333333; /* Темно-серый для легкого чтения на светлом */
    
    line-height: 1.8 !important;
    font-family: 'Georgia', serif !important;
    position: relative !important;
}

/* 2. ЗАГЛАВНЫЕ БУКВИЦЫ (БОЛЬШИЕ И РОЗОВЫЕ) */
/* Первая буква каждого абзаца или только первой главы */
.brb:has(.fa-heart) p:first-of-type::first-letter {
    float: left !important;
    font-size: 60px !important;
    line-height: 50px !important;
    color: var(--love-pink) !important;
    font-family: 'Cinzel', serif !important;
    margin-right: 12px !important;
    padding-top: 8px !important;
    font-weight: bold !important;
    text-shadow: 1px 1px 0px rgba(0,0,0,0.1) !important;
}

/* 3. ЗАГОЛОВКИ (ЭФФЕКТ ГРАВИРОВКИ) */
.brb:has(.fa-heart) h3, 
.brb:has(.fa-heart) b:first-child,
.brb:has(.fa-heart) .cd-t {
    color: var(--love-pink) !important; /* Розовый для акцента */
    text-transform: uppercase !important;
    letter-spacing: 4px !important;
    text-align: center !important;
    display: block !important;
    border-bottom: 1px double var(--gold-leaf) !important;
    padding-bottom: 10px !important;
    margin-bottom: 25px !important;
}

/* 4. МАГИЧЕСКИЕ СИМВОЛЫ (СИГИЛЫ ЛЮБВИ) */
/* Добавляем символ "Сердце" в правый нижний угол страницы */
.brb:has(.fa-heart)::after {
    content: '❦'; /* Изящный символ флорона/сердца */
    position: absolute;
    bottom: 20px;
    right: 30px;
    font-size: 40px;
    color: var(--love-pink);
    opacity: 0.15;
    pointer-events: none;
}

/* Добавляем "Пар Амортенции" сверху */
.brb:has(.fa-heart)::before {
    content: '♨';
    position: absolute;
    top: 10px;
    left: 20px;
    font-size: 30px;
    color: var(--gold-leaf);
    opacity: 0.2;
    filter: blur(1px);
}

/* 5. ИКОНКА СЕРДЦА (ПУЛЬСАЦИЯ) */
.fa-heart {
    color: var(--love-pink) !important;
    font-size: 22px !important;
    filter: drop-shadow(0 0 5px var(--love-pink)) !important;
    animation: simple-pulse 2.5s infinite ease-in-out !important;
}

@keyframes simple-pulse {
    0%, 100% { opacity: 0.6; transform: scale(1); }
    50% { opacity: 1; transform: scale(1.1); }
}

/* 6. АКЦЕНТЫ В ТЕКСТЕ (ЖИРНЫЙ ТЕКСТ) */
.brb:has(.fa-heart) b {
    color: var(--love-pink) !important;
    border-bottom: 1px solid rgba(240, 98, 146, 0.2);
}

/* 7. ДЕКОРАТИВНЫЕ РАЗДЕЛИТЕЛИ */
.brb:has(.fa-heart) hr {
    border: 0 !important;
    height: 1px !important;
    background-image: linear-gradient(to right, transparent, var(--gold-leaf), transparent) !important;
    margin: 20px 0 !important;
}
/* 1. ГЛАВНЫЙ КОНТЕЙНЕР (ТОЛЬКО ДЛЯ ID 60) */
[data-id="60"] .dossier {
    background: #0d0d0f !important; /* Глубокий графит */
    border: 1px solid rgba(240, 98, 146, 0.15) !important;
    border-left: 4px solid #f06292 !important; /* Яркий розовый торец */
    padding: 24px !important;
    margin: 35px 0 !important;
    position: relative !important;
    box-shadow: 0 15px 35px rgba(0, 0, 0, 0.4) !important;
    font-family: 'Inter', 'Segoe UI', sans-serif !important;
}

/* 2. ЗАГОЛОВОК ДОСЬЕ (Лабораторный паспорт) */
[data-id="60"] .dossier-title {
    color: #f06292 !important;
    text-transform: uppercase !important;
    font-size: 11px !important;
    letter-spacing: 4px !important;
    font-weight: 900 !important;
    margin-bottom: 25px !important;
    display: block !important;
    border-bottom: 1px solid rgba(240, 98, 146, 0.1) !important;
    padding-bottom: 12px !important;
    text-shadow: 0 0 10px rgba(240, 98, 146, 0.2) !important;
}

/* 3. СТРОКИ С ДАННЫМИ */
[data-id="60"] .dossier-row {
    display: flex !important;
    justify-content: space-between !important;
    align-items: flex-start !important;
    padding: 14px 0 !important;
    border-bottom: 1px solid rgba(255, 255, 255, 0.03) !important;
}

[data-id="60"] .dossier-row:last-child {
    border-bottom: none !important;
}

/* 4. ПАРАМЕТР (ЛЕВАЯ КОЛОНКА) */
[data-id="60"] .dossier-key {
    color: #ad1457 !important; /* Темный винный пурпур */
    font-size: 11px !important;
    font-weight: 800 !important;
    text-transform: uppercase !important;
    flex: 0 0 35% !important;
    letter-spacing: 1px !important;
}

/* 5. ЗНАЧЕНИЕ (ПРАВАЯ КОЛОНКА) */
[data-id="60"] .dossier-value {
    color: #dcd0d4 !important; /* Мягкий цвет пыльной розы */
    font-size: 13px !important;
    line-height: 1.5 !important;
    text-align: right !important;
    flex: 1 !important;
    font-style: italic !important;
}

/* 6. ЗАГЛАВНЫЕ БУКВЫ И ЗАГОЛОВКИ КНИГИ */
/* Делаем все "заглавные" элементы книги розовыми */
[data-id="60"] h1, 
[data-id="60"] h3, 
[data-id="60"] .ch-t, 
[data-id="60"] b:first-child {
    color: #f06292 !important;
    text-transform: uppercase !important;
    letter-spacing: 3px !important;
    text-shadow: 0 0 8px rgba(240, 98, 146, 0.3) !important;
}

/* 7. ДЕКОРАТИВНЫЙ "ШТАМП" В УГЛУ */
[data-id="60"] .dossier::after {
    content: "MINISTRY APPROVED";
    position: absolute;
    bottom: 8px;
    right: 12px;
    font-size: 7px;
    color: #f06292;
    opacity: 0.2;
    border: 1px solid #f06292;
    padding: 2px 4px;
    transform: rotate(-3deg);
}

</style>
</head>
<body>
  <div id="app">
    <!-- ===== ПРОФИЛЬ ===== -->
    <div class="pn"></div>

    <!-- ===== ОЧКИ ===== -->
    <div id="houses"></div>

    <!-- ===== УРОКИ ===== -->
    <div id="lessonsList"></div>

  
	

    <!-- ===== КНИГИ ===== -->
    <div id="books"></div>
    <div id="br"></div>
  </div>

  <!-- Звездное небо на канвасе -->
  <canvas id="stars"></canvas>

  <!-- Контейнер для всплывающих элементов или модалок -->
  <div id="app"></div>

  <!-- Панель для книги (с кнопкой назад и заголовком) -->
  <div id="br" class="book-controls">
    <div class="brh">
      <button onclick="closeBook()"><i class="fas fa-arrow-left"></i></button>
      <h3 id="brT"></h3>
    </div>
    <div class="brb" id="brB"></div>
  </div>

  <!-- Нижняя навигационная панель -->
  <div id="bnav" class="bottom-nav">
    <div class="ni on" data-n="home" onclick="go('home')">
      <i class="fas fa-hat-wizard"></i><span>Главная</span>
    </div>
 
    <div class="ni" data-n="lessons" onclick="go('lessons')">
      <i class="fas fa-book-open"></i><span>Уроки</span>
    </div>
    <div class="ni" data-n="library" onclick="go('library')">
      <i class="fas fa-book"></i><span>Библиотека</span>
    </div>
  </div>
  <div id="br" class="book-modal" style="display:none;">
    <div class="book-header">
        <span id="brT">Название книги</span>
        <button onclick="closeBook()" class="close-btn">✕</button>
    </div>
    
    <div id="brB" class="book-content"></div> 
    
    <div id="bnav" class="book-footer">
        <span>Нажмите на поля, чтобы листать</span>
    </div>
</div>

</body>
<script>

let tg = null;
const user = {
  id: 0,
  name: 'Гость',
  username: 'guest',
  role: 'student',
  house: ''
};

try {
  if (window.Telegram && window.Telegram.WebApp) {
    tg = window.Telegram.WebApp;
    tg.ready();
    tg.expand();
  }
} catch (e) {
  console.error('Ошибка инициализации Telegram:', e);
}

function initUser() {
  if (tg?.initDataUnsafe?.user) {
    const u = tg.initDataUnsafe.user;
    user.id = u.id || 0;
    user.name = u.first_name || u.last_name || 'Гость';
    user.username = u.username || '';
  }

  const admins = ['Demianovich', 'Ananasik_ya'];
  const deans = {
    'sof_russo': 'g',
    'cirquel': 'r',
    'kbaylife': 'h',
    'nikatinka_me': 's'
  };
  const teachers = [
    'Pandadrag', 'Buna72', 'Olesionokc', 'snepulik', 'emiliaairapetyan',
    'Lerusik_lala', 'lenka_dm', 'Severus9969', 'Lune474', 'KristinaTAA',
    'StSatanDay', 'notommi', 'moonzam'
  ];

  if (admins.includes(user.username)) {
    user.role = 'admin';
  } else if (deans[user.username]) {
    user.role = 'dean';
    user.house = deans[user.username];
  } else if (teachers.includes(user.username)) {
    user.role = 'teacher';
  }

  loadState();
}

function isAdmin() {
  return ['admin', 'dean', 'teacher'].includes(user.role);
}

const FC = {
  g: { n: 'Гриффиндор', e: '🦁', c: '#c0392b' },
  s: { n: 'Слизерин', e: '🐍', c: '#1a7a3c' },
  r: { n: 'Когтевран', e: '🦅', c: '#1a5a86' },
  h: { n: 'Пуффендуй', e: '🦡', c: '#d4a017' }
};

function applyStyle(key) {
    const faculty = FC[key];
    const card = document.querySelector('.faculty-card');
    const icon = document.querySelector('.animal-icon');
    const title = document.querySelector('.faculty-name');

    // 1. Меняем цвет через CSS-переменную
    card.style.setProperty('--f-color', faculty.c);
    
    // 2. Обновляем контент
    icon.textContent = faculty.e;
    title.textContent = faculty.n;
    
    // 3. Добавляем эффект "пробуждения"
    icon.style.transform = 'scale(1.2)';
    setTimeout(() => icon.style.transform = 'scale(1)', 300);
}
const KEYS = ['g', 's', 'r', 'h'];
let pts = { g: 47, s: 52, r: 38, h: 44 };

// STAFF
const STAFF = [
  { tg: '@Demianovich', n: 'Демьян', r: 'Админ (технические вопросы)', i: 'fa-gear', c: 'Администрация' },
  { tg: '@Ananasik_ya', n: 'Анастасия', r: 'Админ (вопросы обучения)', i: 'fa-gear', c: 'Администрация' },
  { tg: '@sof_russo', n: 'Софа', r: 'Декан Гриффиндора', i: 'fa-shield-halved', c: 'Деканат' },
  { tg: '@cirquel', n: 'Апепя', r: 'Декан Когтеврана', i: 'fa-shield-halved', c: 'Деканат' },
  { tg: '@kbaylife', n: 'Карина', r: 'Декан Пуффендуя', i: 'fa-shield-halved', c: 'Деканат' },
  { tg: '@nikatinka_me', n: 'Ника', r: 'Декан Слизерина / Магистр викторин', i: 'fa-shield-halved', c: 'Деканат' },
  { tg: '@StSatanDay', n: 'Мистер Филч', r: 'Завхоз', i: 'fa-broom', c: 'Администрация' },
  { tg: '@Pandadrag', n: 'Эларион', r: 'Староста Гриффиндор / УЗМС, История магии', i: 'fa-star', c: 'Старосты' },
  { tg: '@Lerusik_lala', n: 'Лера', r: 'Староста Гриффиндор / Заклинания 1', i: 'fa-star', c: 'Старосты' },
  { tg: '@Midonhe', n: 'Marlen', r: 'Староста Когтеврана', i: 'fa-star', c: 'Старосты' },
  { tg: '@Fir_Ali', n: 'Дарья', r: 'Староста Пуффендуя', i: 'fa-star', c: 'Старосты' },
  { tg: '@emiliaairapetyan', n: 'Эмилия', r: 'Староста Пуффендуй / Зелья 3, Заклинания 2, Руны 2', i: 'fa-star', c: 'Старосты' },
  { tg: '@mamamarishki', n: 'Лиза', r: 'Староста Слизерина', i: 'fa-star', c: 'Старосты' },
  { tg: '@Adrian_LoR', n: 'Элизабет', r: 'Староста Слизерина / Мракоборец', i: 'fa-star', c: 'Старосты' },
  { tg: '@Buna72', n: 'Проф. Таун', r: 'Травология 1,2 / Астрономия', i: 'fa-leaf', c: 'Преподаватели' },
  { tg: '@Olesionokc', n: 'Пр. Морвей', r: 'Прорицания 1 / Полёты 1', i: 'fa-eye', c: 'Преподаватели' },
  { tg: '@snepulik', n: 'Проф. Снейп', r: 'Зельеварение 1,2', i: 'fa-flask', c: 'Преподаватели' },
  { tg: '@lenka_dm', n: 'Проф. Флитвик', r: 'Заклинания 3', i: 'fa-wand-sparkles', c: 'Преподаватели' },
  { tg: '@Sofikoo_o', n: 'Пр. Браун', r: 'Полёты 2 / Ксенозоология', i: 'fa-broom-ball', c: 'Преподаватели' },
  { tg: '@Severus9969', n: 'Проф. Снейп', r: 'ЗОТИ 1,2,3', i: 'fa-shield', c: 'Преподаватели' },
  { tg: '@Lune474', n: 'Пр. Свитч', r: 'Трансфигурация 1,2,3 / Руны и судьба', i: 'fa-arrows-rotate', c: 'Преподаватели' },
  { tg: '@KristinaTAA', n: 'Проф. Нокстер', r: 'Древние руны 1', i: 'fa-language', c: 'Преподаватели' },
  { tg: '@notommi', n: '', r: 'Боевая трансфигурация', i: 'fa-fire', c: 'Преподаватели' },
  { tg: '@moonzam', n: '', r: 'Числовая астрономия', i: 'fa-calculator', c: 'Преподаватели' },
  { tg: '@vticov', n: 'Матвей', r: 'Мракоборец', i: 'fa-shield', c: 'Мракоборцы' },
  { tg: '@Lune474', n: 'Ульяна', r: 'Мракоборец', i: 'fa-shield', c: 'Мракоборцы' },
  { tg: '@moonzam', n: 'Глэм', r: 'Мракоборец', i: 'fa-shield', c: 'Мракоборцы' }
];

// Устав
const USTAV = [
  {
    t: 'Часто задаваемые вопросы',
    c: [
      'В: Где посмотреть расписание занятий?\nО: Каждое воскресенье администрация формирует расписание на неделю и отправляет его вам в боте — отдельно по вашему курсу. Также расписание публикуется и закрепляется в группе «Большой зал» и в вашей гостиной.\nОбратите внимание: расписание может меняться в течение дня, поэтому важно следить за обновлениями в гостиной.',
      'В: Как заработать алмазы и галлеоны?\nО: Выполнять задания от Гильдий, составлять вопросы для Викторины, пригласить друга, помочь проекту, устроиться преподавателем и вести уроки, побеждать в турнирах, участвовать в разных конкурсах от администрации.',
      'В: Я нажал кнопку «Начать», но ничего не происходит.\nО: Убедитесь, что вы взаимодействуете с ботом в личной переписке и что у вас включены уведомления. Если регистрация была прервана, просто нажмите кнопку «Начать» ещё раз — процесс начнётся с нуля.',
      'В: Можно ли поменять палочку/питомца?\nО: Первая палочка и питомец выдаются бесплатно, но позже вы можете купить новые в Косом переулке. При покупке система предупредит, что питомец заменит текущий.\nСтарый питомец уйдёт навсегда, а палочка переместится в инвентарь (сохранятся все свойства).',
      'В: Как вернуть прочность предметам?\nО: В гильдии работает мастерская. Отнесите туда предметы с нулевой прочностью — за небольшую плату кузнецы восстановят их. Вам понадобится время, поэтому заранее следите за состоянием своей экипировки.',
      'В: Что происходит, если я проигрываю дуэль?\nО: Проигравший получает кулдаун на вызовы и принятие дуэлей. Подождите, пока кулдаун закончится — обычно это несколько минут. Кроме того, вы можете потерять очки факультета.',
      'В: Как получить опыт и галлеоны без дуэлей?\nО: Посещайте занятия, выполняйте задания гильдий, участвуйте в событиях и праздниках. В будущем появятся дополнительные мини‑игры, за которые начисляются награды.',
      'В: Что даёт гильдия?\nО: Каждая гильдия имеет собственный уровень и шкалу очков. Повышая уровень, вы получаете постоянные бонусы к характеристикам и дополнительные галеоны.',
      'В: Как покормить питомца?\nО: Зайдите в раздел «Мой Питомец», нажмите «Покормить» и выберите корм из списка. Корм можно купить в магазине или получить как подарок. Помните: сытый питомец — залог победы!',
      'В: Как сменить факультет?\nО: Возможность смены факультета существует, но требует большого количества алмазов. Это сделано для того, чтобы выбор дома был осознанным. Инструкция по смене факультета - тут.',
      'В: Как пригласить друга?\nО: Откройте Банк, выберите «Пригласить друга» и нажмите «Поделиться ссылкой». Скопируйте получившийся текст и отправьте другу. После его регистрации награды начислятся автоматически.',
      'В: Что делать, если я нашёл баг?\nО: Сообщите об этом администрации в чате вашего факультета. Проект находится в активной разработке, и ваш отзыв поможет сделать игру лучше.',
      'В: Как отправить подарок?\nО: Откройте свой инвентарь, выберите предмет и нажмите кнопку «Подарить». Бот попросит ввести ID получателя (его можно узнать в профиле) и предложит оставить комментарий. После ввода данных вы увидите итоговую информацию и сможете подтвердить отправку. Предмет исчезнет из вашего инвентаря, а получатель получит уведомление о подарке.',
      'В: Как перевести галлеоны?\nО: В своём профиле нажмите «Перевести галлеоны». Отправитель должен быть не ниже 3‑го уровня, а получатель — не ниже 2‑го. Введите ID адресата, сумму (не менее 50 G) и комментарий; игра покажет комиссию 5 % и итоговую сумму. После подтверждения деньги спишутся с вашего баланса и будут доставлены адресату, а оба получите уведомления.',
      'В: Что такое дневник студента?\nО: Дневник — это раздел вашего профиля, в котором отображаются курсы, выбранные предметы, посещения и оценки. Чтобы открыть дневник, нажмите «Мой дневник» в профиле. В нём вы увидите даты уроков, оценки или отметки о посещении, а также сможете перейти в архив прошлых курсов.'
    ]
  },
  {
    t: 'Введение',
    c: [
      'Дорогой волшебник, руководство поможет разобраться в школе. После распределения ты в одной из четырёх гостиных.',
      'Палочка используется в дуэлях. Питомец влияет на бой. Факультет определяет команду и соревнование Песочных часов.'
    ]
  },
  {
    t: 'Правила поведения',
    c: [
      'Запрещено: оскорбления, буллинг, хамство, спам, реклама, политика, махинации.',
      'Разрешено: дружеское общение, ролевой формат, мемы, соревнования, поддержка.',
      'Наказания: предупреждения, снятие баллов, отстранение, Азкабан.'
    ]
  },
  {
    t: 'Профиль студента',
    c: [
      
    'При нажатии на кнопку <b>«Профиль студента»</b> открывается полная информация о вашем персонаже:',
    
    '🆔 <b>Паспорт</b> — уникальный ID игрока (необходим для дуэлей или при обращении в поддержку).\n' +
    '👤 <b>Роль</b> — ваша текущая позиция в мире игры.\n' +
    '💍 <b>Браки</b> — ваш семейный статус (доступно до 2-х активных браков).\n' +
    '👪 <b>Семья</b> — информация о родителях и ваших детях (лимит до 10 детей).',
    
    '🏰 <b>Академические данные:</b>\n' +
    '• <b>Факультет:</b> ваш текущий дом (можно сменить в настройках).\n' +
    '• <b>Курс:</b> этап вашего обучения в Хогвартсе.\n' +
    '• <b>Уровень:</b> текущий прогресс и количество XP до следующей ступени.',
    
    '💰 <b>Экономика и достижения:</b>\n' +
    '• <b>Галлеоны и Алмазы:</b> средства для покупки инвентаря, питомцев и переводов.\n' +
    '• <b>Приглашено:</b> количество ваших рефералов и бонусы за них.\n' +
    '• <b>Донат:</b> количество присланных «Звезд» и активные бонусы.',
    
    '🛡️ <b>Снаряжение и работа:</b>\n' +
    '• <b>Палочка и Предметы:</b> выбранная экипировка (можно сменить в инвентаре).\n' +
    '• <b>Питомец:</b> вид вашего спутника и процент его сытости.\n' +
    '• <b>Гильдии:</b> ваш уровень мастерства и доступные вакансии для работы.',
    
    '<i>* При нажатии на кнопку «Профиль студента 🎓» открывается дополнительное подменю управления.</i>'
  
    ]
  },
  {
  t: 'Мои заклинания ',
  c: [
    '<b>Книга заклинаний</b> — это ваш личный магический арсенал. Здесь хранятся все изученные чары и настраиваются стратегии для боёв.',

    '📖 <b>Как получить доступ:</b>\n' +
    'Кнопка «Мои заклинания» появится в профиле только после покупки Книги в <b>Косом Переулке</b>. Без книги изучение магии невозможно.',

    '📜 <b>Изучение магии:</b>\n' +
    '• После покупки книги в лавке открываются доступные заклинания.\n' +
    '• Купленные чары автоматически попадают в раздел «Мои заклинания».\n' +
    '• В профиле появится счетчик: <i>«Изучено заклинаний: N»</i>.\n' +
    '• Повторно покупать выученное заклинание не нужно.',

    '⚔️ <b>Где использовать:</b>\n' +
    'Сейчас заклинания доступны в 3-х режимах:\n' +
    '1. <b>Дуэли</b>\n' +
    '2. <b>Бои в Гильдии Защитников</b>\n' +
    '3. <b>Рейды</b>\n' +
    '<i>* В турнирах и других режимах поддержка появится позже.</i>',

    '⚙️ <b>Как работает выбор:</b>\n' +
    'В меню можно назначить конкретное заклинание под каждый тип боя.\n' +
    '• <b>Лимит:</b> 1 заклинание на 1 тип боя.\n' +
    '• <b>Использование:</b> применить можно только 1 раз за весь бой.\n' +
    '<i>* Лимиты будут увеличиваться через достижения и манускрипты.</i>',

    '✨ <b>Виды и механика:</b>\n' +
    '• 🟢 <b>Баффы</b> — усиливают ваши характеристики.\n' +
    '• 🔴 <b>Дебаффы</b> — ослабляют противника.\n' +
    '• 🎲 <b>Шансовые</b> — эффект срабатывает с определенной вероятностью.\n\n' +
    '<b>Типы расчёта:</b>\n' +
    '• <b>Процентные (%)</b> — меняют статы в процентах.\n' +
    '• <b>Единичные (int)</b> — меняют статы на конкретное число.',

    '🆙 <b>Уровни и усиление:</b>\n' +
    'Все заклинания стартуют с 1 уровня. Чтобы получить реальное преимущество в бою, улучшайте их прямо в меню <b>Профиль → Мои заклинания</b>.'
  ]
},
  {
  t: 'Мой питомец 🐾',
  c: [
    'Информация о вашем верном спутнике доступна в меню: <b>Профиль студента 🎓 → Мой Питомец</b>.',

    '📋 <b>Возможности раздела:</b>\n' +
    '• Просмотр боевых характеристик и бонусов питомца.\n' +
    '• <b>Кормление:</b> поддержание сытости для эффективности в боях.\n' +
    '• <i>* Для покупки корма или смены питомца необходимо посетить Косой Переулок.</i>',

    '✨ <b>Первый питомец:</b>\n' +
    'При регистрации каждый студент получает одного питомца на выбор бесплатно:\n' +
    '🦉 <b>Сова</b> | 🐈 <b>Кошка</b> | 🐸 <b>Жаба</b>\n' +
    '<i>Начальные питомцы не обладают боевыми характеристиками и служат верными друзьями.</i>',

    '🏪 <b>Волшебный Зверинец (Косой Переулок):</b>\n' +
    'По мере повышения вашего уровня в лавке открываются более редкие и сильные существа с уникальными статами.',

    '⚠️ <b>Важное правило выбора:</b>\n' +
    'При покупке нового питомца в Зверинце <b>прежний удаляется навсегда</b>! Тщательно выбирайте своего будущего фамильяра.'
  ]
},
{
  t: 'Мой дневник 📖',
  c: [
    'Ваша зачетная книжка и история обучения находятся в меню: <b>Профиль студента 🎓 → Мой Дневник</b>.',

    '📝 <b>Текущий курс и дисциплины:</b>\n' +
    '• В шапке дневника отображается ваш текущий год обучения и список выбранных предметов.\n' +
    '• <b>Важно:</b> Если дисциплины на этот курс еще не выбраны, игра предложит сделать это в первую очередь.',

    '📊 <b>Записи и успеваемость:</b>\n' +
    'По каждому предмету ведется подробный протокол:\n' +
    '• <b>Дата и номер урока:</b> когда вы присутствовали на занятии.\n' +
    '• <b>Оценки:</b> ваш балл за урок или отметка о посещении.\n' +
    '• <i>* Если записей еще нет, вы увидите пометку: «Посещений и оценок пока нет».</i>',

    '🗄️ <b>Архив дневников:</b>\n' +
    'Начиная со <b>2-го курса</b>, в меню открывается доступ к <b>Архиву</b>.\n' +
    '• Просматривайте журналы прошлых лет.\n' +
    '• Отслеживайте свой магический прогресс и список изученных ранее наук.',

    '💡 <b>Зачем это нужно:</b>\n' +
    'Дневник — это ваш главный навигатор по учебе. Он помогает понять, какие лекции вы пропустили и по каким предметам стоит подтянуть знания для успешной сдачи экзаменов.'
  ]
},
{
  t: 'Подарки 🎁',
  c: [
    'В мире магов обмен подарками — это важная традиция и способ выразить признание.',

    '📦 <b>Ваша коллекция:</b>\n' +
    'В разделе <b>«Подарки»</b> хранятся все подношения, которые вы когда-либо получали от других студентов или профессоров.',

    '✨ <b>Что можно сделать с подарком:</b>\n' +
    '• <b>Открыть</b> — узнать, что скрывается внутри.\n' +
    '• <b>Узнать отправителя</b> — посмотреть, кто прислал вам сову.\n' +
    '• <b>Переслать в гостиную</b> — поделиться радостью с сокурсниками.\n' +
    '• <b>Показать в Большом зале</b> — продемонстрировать редкий дар всей школе.\n' +
    '• <b>Удалить</b> — если хотите освободить место в коллекции.',

    '🛍️ <b>Как самому стать дарителем:</b>\n' +
    'Чтобы отправить подарок другому игроку, загляните в <b>«Косой переулок»</b>. Там вы найдете лавки с самыми изысканными магическими вещами.'
  ]
},
{
  t: 'Перевод галлеонов 💰',
  c: [
    'Поддержать друга или оплатить магическую услугу можно через кнопку <b>«Перевести галлеоны»</b> в вашем профиле.',

    '📜 <b>Золотые правила переводов:</b>\n' +
    '• 🎓 <b>Уровни:</b> Отправитель — от <b>3-го уровня</b>, Получатель — от <b>2-го уровня</b>.\n' +
    '• 🔢 <b>Лимиты:</b> Минимальная сумма перевода — <b>50 G</b>.\n' +
    '• ⚖️ <b>Налог:</b> Министерство магии взимает комиссию <b>5%</b> с каждого перевода.',

    '📲 <b>Как проходит транзакция:</b>\n' +
    '1. Введите <b>ID получателя</b> и подтвердите его личность.\n' +
    '2. Укажите сумму и (по желанию) короткий комментарий.\n' +
    '3. Проверьте итоговый расчет (сумма + комиссия) и нажмите «Подтвердить».\n' +
    '<i>* Вы можете отменить операцию на любом этапе до финального подтверждения.</i>',

    '🔔 <b>Уведомления:</b>\n' +
    'Как только золото сменит владельца, оба участника сделки мгновенно получат сообщение от магической почты.',

    '🚫 <b>Важное ограничение:</b>\n' +
    'Переводу подлежат только <b>Галлеоны</b>. Передавать Алмазы между игроками невозможно.'
  ]
},
{
  t: 'Мои достижения 🏆',
  c: [
    'В этом разделе хранится история ваших великих дел и знаковых моментов в Хогвартсе. За каждое достижение полагается <b>награда</b>.',

    '📜 <b>Академические успехи:</b>\n' +
    '• <b>Новичок 2 курса</b> — Переход на 2 курс.\n' +
    '• <b>Ученик 3 курса</b> — Переход на 3 курс.\n' +
    '• <b>Середина пути</b> — Переход на 4 курс.\n' +
    '• <b>Старшекурсник</b> — Переход на 5 курс.\n' +
    '• <b>Продвинутый ученик</b> — Переход на 6 курс.\n' +
    '• <b>Выпускник</b> — Переход на 7 курс.\n' +
    '• <b>Отличник</b> — Первая оценка «Превосходно».\n' +
    '• <b>Прилежный ученик</b> — Посещение 10 уроков.\n' +
    '• <b>Гордость факультета</b> — Заработал 10 очков своему дому.',

    '⚔️ <b>Боевая слава:</b>\n' +
    '• <b>Первые дуэльные шаги</b> — Первая победа в дуэли.\n' +
    '• <b>Боевая закалка</b> — Победил в 10 дуэлях.',

    '⚒️ <b>Гильдия Волшебного Ремесла:</b>\n' +
    '• <b>Первый удачный рецепт</b> — Успешно угадал заклинание.\n' +
    '• <b>Жар-птица ремесла</b> — 10 угаданных заклинаний подряд.\n' +
    '• <b>Ремесленник V</b> — Достиг 5 уровня в Гильдии.\n' +
    '• <b>Руки из нужного места</b> — Первая починка экипировки в кузнице.',

    '💰 <b>Экономика и Траты:</b>\n' +
    '• <b>Первые траты</b> — Первая покупка в Косом Переулке.\n' +
    '• <b>Опытный покупатель</b> — Потратил 100 галлеонов.\n' +
    '• <b>Щедрая рука</b> — Потратил 1 000 галлеонов.\n' +
    '• <b>Меценат</b> — Потратил 5 000 галлеонов.',

    '🐾 <b>Снаряжение и спутники:</b>\n' +
    '• <b>Верный спутник</b> — Завёл первого питомца.\n' +
    '• <b>Выбранная палочка</b> — Впервые надел палочку.'
  ]
},
{
  t: 'Большой зал и правила 🏰',
  c: [
    '<b>Проверка профиля по ID:</b>\nПозволяет просмотреть паспорт любого игрока (уровень, курс, семья, гильдии). Единственное, что скрыто от чужих глаз — количество <b>алмазов</b>.',

    '📣 <b>Большой зал и Этикет:</b>\n' +
    '• <b>Общий чат:</b> Место общения всех факультетов без границ.\n' +
    '• <b>Цензура:</b> Открытый мат запрещен (только блюр). Помните, у нас есть участники <b>6+</b>.\n' +
    '• <b>Наказания:</b> За нарушение правил — штраф в галлеонах или бан.',

    '🛋️ <b>Гостиная факультета:</b>\n' +
    'Кнопка возвращает вас в закрытый чат вашего дома. Если ссылка «истекла» — возможно, вы получили ограничение за нарушения. Обратитесь к Декану.',

    '🔄 <b>Перевод между факультетами:</b>\n' +
    'Распределение Шляпы осознанно, поэтому перевод — это серьезный шаг.\n' +
    '• <b>Срок:</b> Подать заявку можно только через <b>3 дня</b> после вступления.\n' +
    '• <b>Цель:</b> Эти дни даны, чтобы вы успели познакомиться с коллективом и Деканом.',

    '💰 <b>Стоимость перевода:</b>\n' +
    '• <b>Первый раз:</b> 400 галлеонов + 30 алмазов.\n' +
    '• <b>Повторно:</b> Возможен только через <b>1 учебный год (6 недель)</b>.\n' +
    '• <b>Цена повтора:</b> 800 галлеонов + 100 алмазов.',

    '📝 <b>Как сменить факультет:</b>\n' +
    'Напишите Декану или Старосте <u>текущего</u> факультета:\n' +
    '1. Куда хотите перевестись.\n' +
    '2. Подтвердите наличие суммы на счету.\n' +
    '3. Укажите причину (это поможет нам стать лучше).',

    '🤫 <b>Конфиденциальность:</b>\n' +
    'Если вам некомфортно обсуждать причину ухода с руководством факультета, вы можете написать напрямую администратору <b>Анастасии</b>.'
  ]
},
{
  t: 'Бальный зал и Песочные часы ⌛',
  c: [
    '✨ <b>Бальный зал:</b>\n' +
    'Главное светское событие Хогвартса! Кнопка появляется <b>один раз в учебный год</b> в период выпускного бала (каждую 6-ю неделю).',

    '📝 <b>Условия участия:</b>\n' +
    'Администрация заранее публикует требования, которые могут включать:\n' +
    '• 🎓 <b>Курс:</b> обычно от 3-го или 4-го и выше.\n' +
    '• 🛍️ <b>Дресс-код:</b> наличие определенных предметов из Косого переулка.\n' +
    '• 🎟️ <b>Билет:</b> покупка входного пропуска.',

    '💃 <b>Программа бала (4–5 часов):</b>\n' +
    'Это отдельный праздничный чат, где вас ждут интерактивы, игровые активности, новые знакомства и секретные сюрпризы.',

    '🏆 <b>5.3. Песочные часы (Рейтинг):</b>\n' +
    'Это главная соревновательная система школы. Учебный год длится <b>6 недель</b> (5 учебных + 1 неделя каникул).',

    '📊 <b>За что начисляются баллы:</b>\n' +
    '• ⚔️ Победы в дуэлях и турнирах.\n' +
    '• 🧠 Участие в викторинах.\n' +
    '• 🏰 Другие активные проявления в жизни факультета.',

    '🎁 <b>Финал года:</b>\n' +
    'В конце 6-й недели подводятся итоги. Факультет-победитель забирает <b>Кубок факультетов</b>, а его студенты получают щедрые награды. В меню также доступен <b>Архив прошлых лет</b> для истории.'
  ]
},
{
  t: 'Рейтинг и ЗАГС 💍',
  c: [
    '<b>Рейтинг студентов:</b>\nВ этом разделе можно сравнить свои успехи с другими магами. Отображаются лучшие студенты по уровню, баллам и достижениям.',

    '🏦 <b>ЗАГС (Семейное положение):</b>\n' +
    'Здесь вы управляете своими узами. Доступно: предложение руки и сердца, развод, добавление или удаление детей.',

    '📜 <b>Заключение брака:</b>\n' +
    '• 🆔 <b>Условие:</b> Нужно знать ID партнера.\n' +
    '• 💰 <b>Пошлина:</b> по <b>1 000 галлеонов</b> с каждого (итого 2 000 G за свадьбу). Списывается в момент принятия.\n' +
    '• ♾️ <b>Лимит:</b> Максимум <b>2 активных брака</b> одновременно.',

    '📊 <b>Семейная информация в профиле:</b>\n' +
    'Отображается дата регистрации, стаж союза, список общих детей и данные о ваших родителях.',

    '💔 <b>Расторжение брака:</b>\n' +
    '• Брак может расторгнуть любой супруг в одностороннем порядке.\n' +
    '• <b>Важно:</b> Пошлина при разводе не возвращается.\n' +
    '• Дети остаются в древе обоих родителей даже после расставания.',

    '👶 <b>Добавление ребёнка:</b>\n' +
    'Чтобы принять ребенка в семью, введите его ID.\n' +
    '• 👨‍👩‍👧 <b>Условие:</b> Добавление возможно только если вы <b>в браке</b>.\n' +
    '• 🔢 <b>Лимит:</b> Не более <b>10 детей</b> на одну семью.\n' +
    '• 🚫 <b>Запреты:</b> Нельзя усыновить супруга, родителя, брата или сестру.\n' +
    '• ⏳ <b>Таймер:</b> У игрока есть <b>10 минут</b>, чтобы принять или отклонить ваш запрос.'
  ]
},
{
  t: 'Учебный корпус и правила уроков 📘',
  c: [
    '<b>7.1. Как попасть на занятие:</b>\n' +
    '• 📅 <b>Расписание:</b> Публикуется каждое воскресенье в боте и закрепах чатов. Обновляется еженедельно.\n' +
    '• 🔔 <b>Уведомление:</b> За 5 минут до начала бот пришлет ссылку. Статус кабинета сменится на «Открыт».\n' +
    '• 🚪 <b>Вход:</b> Нажмите «Войти в кабинет», чтобы попасть в учебную группу. Ознакомьтесь с закрепом внутри.',

    '🎓 <b>Важные правила обучения:</b>\n' +
    '• <b>Запись:</b> Можно выбрать до 3-х предметов. Смена через Декана (ID - старый предмет - новый - причина).\n' +
    '• <b>Норма:</b> Минимум 5 посещений за игровой год (6 недель) для перевода на следующий курс.\n' +
    '• <b>Опоздания:</b> В опоздании нет ничего страшного — просто вежливо извинитесь и входите в класс.',

    '🏆 <b>Баллы и Оценки:</b>\n' +
    '• <b>Успеваемость:</b> Оценка «Превосходно» дает <b>+3 очка</b>, «Тролль» — снимает <b>-3 очка</b>.\n' +
    '• <b>Активность:</b> За работу на уроке можно получить до <b>+30 баллов</b>.\n' +
    '• <b>Домашнее задание:</b> Своевременная сдача приносит дополнительные очки всему факультету.',

    '🎭 <b>Ролевой отыгрыш (Role Play):</b>\n' +
    'Урок — это не просто чат, а проживание роли студента Хогвартса. \n' +
    '✅ <b>Нужно:</b> Описывать действия персонажа («записывает слова профессора»), реагировать на однокурсников, задавать вопросы по теме.\n' +
    '❌ <b>Нельзя:</b> Отвечать односложно («ок», «ясно»), использовать маггловские причины («мама зовет», «телефон сел»). Заменяйте их магическими («нужно покормить сову»).',

    '🚫 <b>Запреты на уроке:</b>\n' +
    '• Перебивать профессора вопросами «а когда конец?» или «когда практика?».\n' +
    '• Общаться не по теме (погода, кошки и т.д.) — для этого есть гостиные.\n' +
    '• Хамить и проявлять межфакультетскую вражду (оскорблять по признаку Слизерин/Гриффиндор).\n' +
    '• Выходить из класса без вежливого разрешения профессора.',

    '⚠️ <b>Наказания за нарушения:</b>\n' +
    '1. Снятие баллов с факультета.\n' +
    '2. Плохая оценка в дневник.\n' +
    '3. БАН в учебном кабинете или во всех чатах/боте проекта.',

    '💡 <b>Совет:</b> Главная цель — создать атмосферу волшебства. Будьте вежливы, внимательны к деталям и не бойтесь фантазировать в рамках темы урока!'
  ]
},
{
  t: 'Выбор учебных предметов 📜',
  c: [
    'На каждом курсе студент формирует свой индивидуальный план обучения, выбирая дисциплины из доступного списка.',

    '🌱 <b>Первый курс (Выбор: 3 предмета из 12):</b>\n' +
    'Зельеварение, Заклинания, Травология, История магии, ЗОТИ, Астрономия, Трансфигурация, Древние руны, УЗМС, Полёты на мётлах, Магловедение, Прорицание.',

    '🌿 <b>Второй курс (Выбор: 3 предмета из 8):</b>\n' +
    'Зельеварение, Заклинания, Травология, ЗОТИ, Трансфигурация, Древние руны, УЗМС, Полёты на мётлах.',

    '🌳 <b>Третий курс (Выбор: 3 предмета из 6):</b>\n' +
    'Зельеварение, Заклинания, Травология, ЗОТИ, Трансфигурация, УЗМС.',

    '🔥 <b>Четвёртый курс (Выбор: 4 предмета — Специализация):</b>\n' +
    '• Магическая ботаника\n' +
    '• Боевая трансфигурация\n' +
    '• История миров\n' +
    '• Числовая астрономия\n' +
    '• Прикладная ксенозоология\n' +
    '• Руны и судьба',

    '🔄 <b>Смена предметов:</b>\n' +
    'Если вы хотите изменить свой выбор, следуйте инструкции в разделе <b>«7. Учебный корпус»</b> (через запрос Декану).'
  ]
},
{
  t: 'Мракоборцы и дисциплина 🛡️',
  c: [
    '<b>Мракоборцы</b> — это специальный отряд из 4-х человек, ответственный за порядок на уроках 1-го курса. Они помогают профессорам пресекать хаос и следить за дисциплиной.',

    '⚠️ <b>Система замечаний:</b>\n' +
    'На занятии действует правило «Трех предупреждений»:\n' +
    '1. <b>Первое замечание</b> — устное предупреждение.\n' +
    '2. <b>Второе замечание</b> — последнее китайское предупреждение.\n' +
    '3. <b>Третье замечание</b> — немедленное удаление с урока и отправка в <b>Азкабан</b> (временный бан).',

    '⛓️ <b>Как выйти из Азкабана:</b>\n' +
    'Чтобы восстановить доступ к учебе, студент обязан написать <b>Мракоборцу, Декану, Старосте или Админу</b>:\n' +
    '• Подтвердить, что причина наказания осознана.\n' +
    '• Пообещать не нарушать правила впредь.\n' +
    '<i>* Без покаяния студент не допускается к урокам и не переводится на следующий курс.</i>',

    '🚫 <b>Серьёзные нарушения (Удаление без предупреждения):</b>\n' +
    'Мракоборцы имеют право применить высшую меру сразу, если зафиксированы:\n' +
    '• Оскорбления других студентов или профессора.\n' +
    '• Хамство, агрессия и токсичное поведение.\n' +
    '• Злостный флуд сообщениями, не связанными с темой урока.',

    '💡 <b>Помните:</b>\n' +
    'Мракоборцы здесь не для того, чтобы мешать вам играть, а для того, чтобы учебный процесс оставался комфортным и магическим для всех участников.'
  ]
},
{
  t: 'Косой переулок 🛍️',
  c: [
    'В Косом переулке открыто множество лавок, где можно приобрести всё необходимое для жизни и сражений в Хогвартсе.',

    '🧹 <b>Спортивный магазин:</b>\n' +
    'Здесь продается форма и снаряжение для <b>Квиддича</b>. Экипировка обязательна для участия в матчах и межфакультетских соревнованиях.',

    '📜 <b>Лавка манускриптов:</b>\n' +
    'Место силы для дуэлянтов. Здесь покупаются новые <b>заклинания</b>, которые после оплаты автоматически попадают в вашу Книгу заклинаний.',

    '🧪 <b>Расходники:</b>\n' +
    'Магазин различных зелий и вспомогательных артефактов. Используются для усиления в дуэлях, рейдах и других боевых активностях.',

    '🥩 <b>Магазин корма:</b>\n' +
    'Здесь вы приобретаете еду для своего спутника. Помните: <b>сытость питомца</b> напрямую влияет на ваши шансы на победу!',

    '🎁 <b>Магазин подарков:</b>\n' +
    'Выбирайте презенты для друзей. После покупки можно:\n' +
    '• <b>Отправить сразу</b> — по ID игрока с личным комментарием.\n' +
    '• <b>Положить в инвентарь</b> — чтобы подарить позже в торжественной обстановке.',

    '🐾 <b>Волшебный зверинец:</b>\n' +
    '• <b>Обычные питомцы:</b> доступны для покупки в зависимости от вашего текущего уровня.\n' +
    '• <b>Эксклюзив:</b> за донат от <b>500 звёзд</b> создаётся уникальный индивидуальный питомец, который будет существовать в единственном экземпляре только у вас.'
  ]
},
{
  t: 'Дуэльный клуб ⚔️',
  c: [
    'Студент вправе вызвать любого игрока на магический поединок. Сражения с сильными противниками приносят значительно больше опыта!',

    '🥊 <b>Как вызвать на дуэль:</b>\n' +
    '• Для вызова необходимо знать <b>ID игрока</b>.\n' +
    '• 📜 <b>Мои дуэли:</b> Кнопка для просмотра логов прошлых битв и анализа тактики врага.\n' +
    '• 🎒 <b>Инвентарь:</b> Здесь можно проверить свои характеристики и сменить экипировку.',

    '⚒️ <b>Гильдия Чарокузнецов:</b>\n' +
    'Не забывайте вовремя чинить вещи! За каждый ремонт вы получаете <b>+1 очко Гильдии</b>. Чем выше ваш уровень в Гильдии, тем мощнее бонусы к характеристикам.',

    '🏆 <b>Баллы факультету (Межфакультетские дуэли):</b>\n' +
    '• <b>+2 очка</b> — при победе над соперником, чей уровень <b>выше</b> вашего.\n' +
    '• <b>+1 очко</b> — при победе над соперником <b>равного</b> уровня.',

    '⚠️ <b>Важные правила и ограничения:</b>\n' +
    '• 🪄 <b>Оружие:</b> Выход на дуэль без выбранной волшебной палочки невозможен.\n' +
    '• ⚡ <b>Мощность:</b> На ваш урон и защиту влияют: инвентарь, бонусы Гильдии, базовые статы и <b>сытость питомца</b>.\n' +
    '• ⏳ <b>Кул-даун:</b> После проигрыша накладывается «оглушение» на <b>5 минут</b> — в это время нельзя вызывать других или принимать вызовы.',

    '💡 <b>Совет:</b> Всегда проверяйте сытость питомца и прочность палочки перед тем, как бросать перчатку сильному оппоненту!'
  ]
},
{
  t: 'Турниры 🏆',
  c: [
    'Турниры — это регулярные магические состязания, где каждый может заработать золото, алмазы и славу для своего факультета.',

    '⏰ <b>Когда проходят:</b>\n' +
    'Ежечасно с <b>12:00 до 00:00 (МСК)</b>. Турнир стартует строго по расписанию при наличии от 2 до 32 участников.',

    '📝 <b>Как попасть на турнир:</b>\n' +
    '1. Зайдите в <b>Дуэльный клуб → Турниры</b>.\n' +
    '2. <b>Обязательно:</b> Включите уведомления в настройках!\n' +
    '3. После получения уведомления мгновенно подтвердите участие.',

    '⚔️ <b>Виды турниров:</b>\n' +
    '• 🏟️ <b>Открытый Кубок (на выбывание):</b> классическая сетка 32 → 16 → 8... до финала.\n' +
    '• ⚡ <b>Открытый Быстрый (20 чел):</b> всего одна попытка атаки — решит один точный удар.\n' +
    '• 🔮 <b>Открытый Волшебный (10 чел):</b> дуэли на истощение HP до полной победы.\n' +
    '• 🩸 <b>Последняя Кровь (10 чел):</b> режим с нарастающим уроном и эффектом «отравления».',

    '⚖️ <b>Великое равенство:</b>\n' +
    'В турнирах <u>НЕ учитываются</u>:\n' +
    '❌ Уровень, палочка, одежда или зелья.\n' +
    '❌ Характеристики и сытость питомца.\n' +
    '<i>* Вещи не изнашиваются, питомец не голодает, а серия побед не сбивается.</i>',

    '📊 <b>Начисление очков и наград:</b>\n' +
    '• Победа — <b>3 очка</b> | Ничья — <b>1 очко</b> | Поражение — <b>0</b>.\n' +
    '• Победители получают: <b>Галлеоны, Алмазы и баллы Факультету.</b>',

    '⚠️ <b>Штрафные санкции:</b>\n' +
    'За техническое поражение (пропуск подтвержденного боя) взимается штраф: <b>до 200 G и 10 алмазов</b>. Относитесь к вызовам ответственно!'
  ]
},
{
  t: 'Хогсмид 🍻',
  c: [
    '<b>Хогсмид</b> — это эксклюзивная зона отдыха и развлечений для студентов, свободная от строгих учебных будней.',

    '🔞 <b>Условие доступа:</b>\n' +
    'Вход разрешен только игрокам возрастом <b>16+</b> (по реальному календарю). Это пространство для более взрослого и неформального общения.',

    '✉️ <b>Как попасть (Система приглашений):</b>\n' +
    'Доступ в Хогсмид предоставляется строго по инвайтам:\n' +
    '• Вас может пригласить только тот, кто уже имеет доступ.\n' +
    '• <b>Лимит:</b> У каждого студента есть всего <b>2 приглашения</b> за всё время.',

    '🏘️ <b>Паб «Три метлы»:</b>\n' +
    'После принятия приглашения в профиле появится кнопка <b>«Хогсмид»</b>, ведущая в закрытый чат паба.',

    '🎲 <b>Активности и развлечения:</b>\n' +
    'В Хогсмиде вас ждут уникальные возможности:\n' +
    '• <b>Неформальное общение</b> без строгих академических рамок.\n' +
    '• <b>Казино и лотереи</b> — испытайте свою удачу на галлеоны.\n' +
    '• <b>Особые игровые ивенты</b> и тематические посиделки.',

    '<i>* Помните, что Хогсмид — это место для отдыха, но базовые правила уважительного общения действуют и здесь.</i>'
  ]
},
{
  t: 'Поле для квиддича 🧹',
  c: [
    'Квиддич в нашем боте — это интерактивная стратегия, где исход матча зависит от слаженности команды, интуиции загонщиков и остроты ума ловца.',

    '👥 <b>Роли в команде (7 слотов):</b>\n' +
    '• <b>Капитан:</b> Формирует состав, бросает жребий и назначает первого тактика. После старта становится зрителем.\n' +
    '• <b>Охотники (3):</b> Главная атакующая сила. Пасуют квоффл и забивают голы.\n' +
    '• <b>Загонщики (2):</b> «Аналитики». Угадывают действия соперника, чтобы совершить перехват или запустить серию атак.\n' +
    '• <b>Вратарь:</b> Защищает кольца и раздает квоффл после сэйва.\n' +
    '• <b>Ловец:</b> Охотник за Снитчем. Разгадывает анаграммы и ребусы для победы.',

    '🏟️ <b>Формат соревнований:</b>\n' +
    '• <b>Тренировки:</b> Внутрифакультетские матчи (без наград, для сыгранности).\n' +
    '• <b>Сезон:</b> 6 обязательных матчей против других факультетов за учебный год.\n' +
    '• <b>Финал:</b> Полуфиналы и финалы проходят на каникулах. Победители получают <b>Кубок по квиддичу</b>.',

    '📝 <b>Основной игровой цикл:</b>\n' +
    '1. <b>Жеребьевка:</b> Капитаны бросают кубики за право первого владения.\n' +
    '2. <b>Фаза угадывания:</b> Загонщики пытаются вычислить, у какого охотника квоффл. Успех = перехват.\n' +
    '3. <b>Атака:</b> Охотник выбирает <b>Бросок</b> (дуэль с вратарем) или <b>Пас</b> (рискованный маневр для комбо).\n' +
    '4. <b>Комбо:</b> Неудачный перехват паса дает сопернику <b>двойной бросок</b> или <b>серию из 3-х атак</b>.',

    '✨ <b>Золотой Снитч:</b>\n' +
    '• Появляется после <b>10 сэйвов</b> вратарей.\n' +
    '• Ловцы получают анаграммы/ребусы. Нужно дать <b>3 правильных ответа подряд</b>.\n' +
    '• Поимка приносит <b>+150 очков</b> и мгновенно завершает матч победой.',

    '⚠️ <b>Дисциплина и Штрафы:</b>\n' +
    '• <b>Тайм-аут:</b> На каждое действие дается ~60 секунд. \n' +
    '• <b>Боты:</b> Если игрок медлит или вышел, его заменяет бот.\n' +
    '• <b>Штраф:</b> За пропуск хода команда теряет <b>10 очков</b> в пользу соперника. За массовый выход — техническое поражение.',

    '🏆 <b>Условия победы:</b>\n' +
    'Матч длится до поимки Снитча или до истечения 1 часа (победа по очкам). Победители получают <b>баллы факультету</b> и игровые ценности.'
  ]
},
{
  t: 'Механика игры в Квиддич 🏟️',
  c: [
    'Квиддич в боте — это пошаговая стратегия. Ваша внимательность и скорость реакции напрямую влияют на счет матча.',

    '⚙️ <b>Подготовка и старт:</b>\n' +
    '1. <b>Сбор:</b> Капитан создает матч, игроки подтверждают участие кнопкой <b>«Готов играть»</b>.\n' +
    '2. <b>Жеребьевка:</b> Капитаны бросают кубики за право первого владения квоффлом.\n' +
    '3. <b>Выбор тактика:</b> Капитан назначает первого Охотника. \n' +
    '<i>* Если капитан медлит, команда получает штраф, а выбор делает бот.</i>',

    '🔄 <b>Основной цикл (Борьба за квоффл):</b>\n' +
    '• <b>Угадай владельца:</b> Когда Охотник получает мяч, Загонщики соперника пытаются угадать, у кого он. Угадали? — Перехват. Промах? — Охотник делает ход.\n' +
    '• <b>Действие Охотника:</b>\n' +
    '  — <b>Бросок:</b> Выбор одного из 3 колец. Если Вратарь не угадал кольцо — <b>Гол (+10 очков)</b>.\n' +
    '  — <b>Пас:</b> Охотник выбирает цель и секретное число (1-5).',

    '🎯 <b>Стадия Паса и Комбо:</b>\n' +
    'Загонщики соперника пытаются угадать число паса:\n' +
    '• <b>Угадали:</b> Команда перехватчиков получает <b>серию из 3-х атак</b> подряд.\n' +
    '• <b>Промахнулись:</b> Получатель паса выполняет <b>двойной бросок</b> по воротам.',

    '✨ <b>Золотой Снитч:</b>\n' +
    '• <b>Появление:</b> После <b>10 успешных сэйвов</b> вратарей Снитч вылетает на поле.\n' +
    '• <b>Механика Ловца:</b> Ловец получает анаграммы или ребусы. \n' +
    '• <b>Победа:</b> Нужно разгадать <b>3 фразы подряд</b>. Поимка дает <b>+150 очков</b> и мгновенно завершает матч.',

    '⏱️ <b>Таймауты и Штрафы:</b>\n' +
    'На каждое действие (ход, угадывание, бросок) дается <b>~60 секунд</b>.\n' +
    '• Если игрок не успел — ход делает бот.\n' +
    '• Команда получает <b>штраф -10 очков</b> за промедление.',

    '🏁 <b>Финал матча:</b>\n' +
    'Игра заканчивается, если:\n' +
    '1. Пойман Снитч (автопобеда по очкам).\n' +
    '2. Истекло время (1 час) — победа по текущему счету.\n' +
    '3. Административное завершение или массовый выход игроков.',

    '💡 <b>Советы новичкам:</b>\n' +
    '• <b>Заранее</b> тренируйтесь разгадывать анаграммы.\n' +
    '• Общайтесь в командном чате: Загонщики должны координировать свои догадки.\n' +
    '• Не игнорируйте уведомления бота — штрафы за таймаут могут лишить вас победы!'
  ]
},
{
  t: 'Гильдии и мастерство 🛠️',
  c: [
    'Гильдии позволяют студентам освоить магические профессии, заработать галлеоны и получить постоянные прибавки к характеристикам (HP, урон, защита).',

    '🧪 <b> Гильдия Волшебного ремесла:</b>\n' +
    'Здесь вы помогаете профессорам, разгадывая магические шифры. \n' +
    '• <b>Задание:</b> Нужно собрать заклинание из перепутанных букв или решить «смайловую» задачу.\n' +
    '• <b>Процесс:</b> Вводите верное слово (напр. <i>Акцио, Люмос</i>) → Ждете завершения работы → Получаете награду.\n' +
    '• <b>Бонус:</b> Повышение уровня гильдии дает постоянный буст к вашим боевым статам.',

    '🛡️ <b>Гильдия Защитников Хогвартса:</b>\n' +
    'Специализируется на обороне школы и патрулировании территорий.\n' +
    '• <b>Деятельность:</b> Отражение атак магических существ и помощь мастерам защиты.\n' +
    '• <b>Бонусы:</b> Развитие в этой гильдии усиливает ваши навыки <b>блока, уклонения и регенерации</b>.',

    '⚒️ <b> Гильдия Чарокузнецов:</b>\n' +
    'Мастерская гоблинов, где поддерживают в порядке вашу экипировку.\n' +
    '• <b>Ремонт:</b> Починка стоит 90% от цены вещи. Внимание: при каждом ремонте <b>максимальная прочность снижается на 1 единицу</b>.\n' +
    '• <b>Улучшение:</b> Можно усилить артефакт до 3-го уровня, увеличив его прочность на +50%. Стоимость — полная магазинная цена в галлеонах.\n' +
    '• <b>Прокачка:</b> За каждый ремонт вы получаете <b>+1 очко гильдии</b>. Высокий уровень снижает цену и ускоряет работу кузнецов.',

    '⏳ <b>Важные правила:</b>\n' +
    '• <b>Кулдауны:</b> После работы или во время ремонта действует таймер. Нужно дождаться его окончания, прежде чем брать новый заказ.\n' +
    '• <b>Отказ:</b> Если нажать «Пропустить» задание в Ремесле, наложится короткий штрафной кулдаун.',

    '💡 <b>Совет мастера:</b>\n' +
    'Следите за износом палочки и мантии! Сломанная вещь в разы снижает вашу эффективность в бою. Чините экипировку заранее, пока её максимальная прочность еще высока.'
  ]
},
{
  t: 'Рейды и Битвы с Боссами 🐲',
  c: [
    'Рейды — это масштабные командные испытания против сверхмощных противников (Рейд-Боссов), которых невозможно одолеть в одиночку.',

    '⚔️ <b>Как устроен Рейд:</b>\n' +
    '• <b>Доступ:</b> Раздел <b>«Гильдия Защитников Хогвартса» → «Рейд»</b>.\n' +
    '• <b>Общая цель:</b> Все участники наносят урон <b>единому запасу HP</b> Босса. Сражаться можно одновременно с другими игроками.\n' +
    '• <b>Механика:</b> В каждом раунде вы выбираете зону атаки и защиты. Босс может критовать, уклоняться и регенерировать.',

    '💰 <b>Награды и Личный вклад:</b>\n' +
    'Система объективно оценивает старания каждого мага:\n' +
    '• <b>Пропорциональность:</b> Галлеоны и алмазы распределяются согласно нанесенному вами урону.\n' +
    '• <b>Ценность участия:</b> Ваш вклад важен, даже если Босса добил другой игрок или если вы потерпели поражение в дуэли.',

    '🩹 <b>Поражение и Восстановление:</b>\n' +
    'Если Босс оказался сильнее, накладывается <b>кулдаун (время отдыха)</b>. Используйте это время, чтобы проверить экипировку, сменить заклинания или покормить питомца.',

    '📜 <b>История и Признание:</b>\n' +
    'Гильдия ведет архив каждого рейда. Имена героев, нанесенный урон и результаты битв навсегда заносятся в летопись славы Хогвартса.',

    '🤝 <b>Принципы Рейда:</b>\n' +
    '• <b>Командный дух:</b> Общий результат выше личной славы.\n' +
    '• <b>Подготовка:</b> Рейд-Босс не прощает ошибок — проверьте палочку и бонусы гильдий перед входом в бой.\n' +
    '• <b>Воля к победе:</b> Каждое столкновение — это бесценный опыт и шаг к вершине мастерства.'
  ]
},
{
  t: 'Зеркало Еиналеж и Банк Гринготтс 🏛️',
  c: [
    '✨ <b>Зеркало Еиналеж:</b>\n' +
    'Магический артефакт в главном меню, который видит ваши сокровенные желания и сомнения. Задайте вопрос магии и дождитесь, пока зеркало соберет ваше отражение.\n' +
    '• 💰 <b>Стоимость:</b> 30 галлеонов за один вопрос.',

    '🏦 <b>Банк Гринготтс:</b>\n' +
    'Здесь сосредоточены все финансовые и партнерские инструменты школы.',

    '👥 <b>Пригласить друга (Реферальная система):</b>\n' +
    'Разделите магию с друзьями и получите за это золото!\n' +
    '• <b>Как это работает:</b> Скопируйте персональную ссылку в Банке и отправьте другу.\n' +
    '• <b>Первая награда:</b> Как только друг распределится на факультет, вы <u>оба</u> получите подарки (галлеоны, алмазы, предметы).\n' +
    '• <b>Бонус за прогресс:</b> Если ваш протеже достигнет <b>5 уровня</b>, награда станет еще значительнее.\n' +
    '• <b>Контроль:</b> В разделе <b>«Мои рефералы»</b> можно следить за успехами приглашенных учеников.',

    '🎫 <b>Промокоды:</b>\n' +
    'Если вы нашли или получили секретный свиток с кодом, введите его в соответствующем поле Банка для мгновенного получения бонусов.',

    '💎 <b>Помочь проекту:</b>\n' +
    'В Гринготтсе также доступна опция поддержки школы, которая помогает нашему магическому миру развиваться и открывать новые горизонты.'
  ]
},
{
  t: ' Помощь проекту и Боксы Тайн ✨',
  c: [
    'Поддержка школы через <b>Звёзды</b> помогает нам развивать новые механики, уроки и редкие награды. Взамен меценаты получают уникальные привилегии.',

    '📈 <b>Система поощрений:</b>\n' +
    '• <b>Алмазы:</b> Начисляются за каждую поддержку.\n' +
    '• <b>Лестница коэффициентов:</b> Чем выше ваш <u>общий</u> вклад в проект, тем выгоднее становится каждый следующий обмен.\n' +
    '• <b>Курс:</b> Актуальный курс перевода и ваш бонус отображаются в <b>Банке Гринготтс</b>.',

    '📦 <b>Бокс «Тайна Хогвартса»:</b>\n' +
    'За каждые полные <b>500 ⭐️</b> общего вклада вам полагается <b>1 Бокс</b> и <b>Индивидуальный питомец</b>.\n' +
    '• Это накопительная система: боксы открываются по мере роста вашего вклада.\n' +
    '• Забрать доступные боксы можно в Банке, после чего они появятся в инвентаре.',

    '📜 <b>Что скрывают Боксы (Случайная награда):</b>\n' +
    'Внутри каждого бокса находится одна ценная магическая вещь с равным шансом выпадения:',

    '✨ <b>Уникальные Сертификаты:</b>\n' +
    'На эксклюзивную внешность и экипировку (Шляпа, Мантия, Обувь, Палочка).',

    '📖 <b>Прогресс и Усиление:</b>\n' +
    '• <b>Свитки опыта:</b> Большой и малый опыт для быстрого роста.\n' +
    '• <b>Боевые свитки:</b> Усиление или получение нового заклинания, прочность вещей.\n' +
    '• <b>Академические бонусы:</b> Сертификат на Книгу заклинаний или оценку «Превосходно».',

    '⚒️ <b>Гильдейские свитки:</b>\n' +
    '• <b>Слава:</b> +10 очков выбранной гильдии.\n' +
    '• <b>Безлимит ГЗХ:</b> 15 минут защиты без ограничений.\n' +
    '• <b>ГВР:</b> Возможность дать 10 ответов подряд без кулдауна.',

    '🌍 <b>Редкое влияние:</b>\n' +
    'Свитки смены <b>Факультета</b> или <b>Роли</b>, а также укрепление и усиление вашего питомца.',

    '💡 <b>Важно для новичка:</b>\n' +
    'Боксы — это не просто косметика. Это легальный способ ускорить прокачку, получить уникальные предметы и временно снять игровые ограничения (кулдауны), делая ваше приключение в Хогвартсе еще ярче!'
  ]
},
{
  t: 'Магистр викторин 📚',
  c: [
    'Вы можете зарабатывать галлеоны и алмазы, помогая расширять базу знаний школы. Составляйте качественные вопросы и присылайте их <b>Магистру викторин</b>.',

    '💰 <b>Система оплаты (Базовая ставка):</b>\n' +
    'За блок из <b>10 качественных вопросов</b> вы получаете на выбор:\n' +
    '• <b>800 галлеонов</b> ИЛИ <b>80 алмазов</b>.',

    '➕ <b>Бонусы за объем:</b>\n' +
    'За каждый последующий вопрос (сверх первых десяти):\n' +
    '• <b>+80 галлеонов</b> ИЛИ <b>+8 алмазов</b>.',

    '⚠️ <b>Штрафы за ошибки (Модерация):</b>\n' +
    'Будьте внимательны! За каждый некорректный или ошибочный вопрос списывается:\n' +
    '• <b>-160 галлеонов</b> ИЛИ <b>-16 алмазов</b>.',

    '📜 <b>Важные правила для авторов:</b>\n' +
    '• <b>Честная игра:</b> Составитель вопросов <u>не имеет права</u> сам отвечать на них в викторинах, а также передавать ответы или подсказывать другим игрокам.\n' +
    '• <b>Редактура:</b> Администрация оставляет за собой право изменять текст вопросов или отклонять их без объяснения причин.',

    '💡 <b>Совет:</b>\n' +
    'Перед отправкой проверьте факты в первоисточниках (книгах или фильмах). Качество важнее количества — один ошибочный вопрос «съедает» оплату за два верных!'
  ]
},
{
  t: 'Технический регламент викторин 📝',
  c: [
    'Чтобы ваша викторина была принята и оплачена в полном объеме, она должна соответствовать строгим правилам оформления.',

    '📝 <b>Золотые правила формулировки:</b>\n' +
    '• <b>Полнота:</b> Вопрос должен быть понятен без лишних уточнений.\n' +
    '• <b>Подсказки:</b> Все особенности ответа (падеж, язык, спецсимволы) указываются в <b>круглых скобках</b> сразу после вопроса.',

    '🔤 <b>Обязательные уточнения в скобках:</b>\n' +
    '• <b>Буквы Ё/Э:</b> «(Ответ дать с буквой ё)» — <i>Грёбень</i>.\n' +
    '• <b>Дефисы:</b> «(Ответ дать через дефис)» — <i>Протего-максима</i>.\n' +
    '• <b>Падежи:</b> «(Ответ дать в творительном падеже)».\n' +
    '• <b>Персонажи:</b> Уточняйте формат: «(Укажите имя)», «(Укажите фамилию)» или «(Укажите имя и фамилию)».\n' +
    '• <b>Язык:</b> «(Ответ дать на английском языке)» — <i>Hogwarts</i>.\n' +
    '• <b>Предложения:</b> «(Ответ дать без знаков препинания)».',

    '📂 <b>Правильное оформление (Пример):</b>\n' +
    '<i>Как зовут полувеликана, лесничего Хогвартса? (Укажите имя и фамилию)</i>\n' +
    '<b>[Нажать Enter]</b>\n' +
    '<i>Рубеус Хагрид</i>\n' +
    '❌ <b>Запрещено:</b> Писать ответ в той же строке или добавлять слова «Ответ:», «Правильно:».',

    '⚖️ <b>Модерация и Штрафы:</b>\n' +
    '• <b>Ответственность:</b> Автор лично отвечает за корректность данных. Модератор проверяет только формат, но не гуглит ответы за вас.\n' +
    '• <b>Цена ошибки:</b> Если в процессе игры выяснится, что ответ неверный или формат нарушен — <b>штраф -160 галлеонов (-16 алмазов)</b> за каждый такой вопрос.\n' +
    '• <b>Рецидив:</b> При регулярных ошибках администрация навсегда прекращает сотрудничество с автором.',

    '💰 <b>Напоминание по оплате:</b>\n' +
    '• Стандартная викторина — <b>10 вопросов</b> (800 G / 80 💎).\n' +
    '• Свыше 10 вопросов — <b>+80 G / +8 💎</b> за каждый.\n' +
    '• Максимальный объем — 20 вопросов (по согласованию).',

    '⚠️ <i>Инструкция находится в разработке и может дополняться. Следите за обновлениями перед отправкой работ!</i>'
  ]
},
{
  t: 'Новые условия и формат викторин 🔔',
  c: [
    'Внимание! Правила проведения и оплаты викторин обновлены. Комиссия по проверке теперь уделяет особое внимание качеству и фактической точности вопросов.',

    '📅 <b>Регулярность:</b>\n' +
    'Викторины проводятся <b>3–4 раза в неделю</b>. Частота напрямую зависит от количества и качества присланных вами работ.',

    '🎁 <b>Новые награды для участников:</b>\n' +
    'За каждый правильный ответ на викторине игрок получает:\n' +
    '• 🛡️ <b>+7 баллов</b> своему факультету.\n' +
    '• 💰 <b>50 галлеонов</b> в личный кошелек.\n' +
    '• 💎 <b>2 алмаза</b> лично.',

    '⚖️ <b>Ужесточение ответственности авторов:</b>\n' +
    'Мелкие технические недочеты (не указали «ё», дефис или падеж) теперь считаются допустимыми погрешностями, НО:\n' +
    '❌ <b>Если в вопросе указан НЕВЕРНЫЙ ответ — штраф увеличивается вдвое!</b>',

    '🚩 <b>Сравнение штрафов за фактическую ошибку:</b>\n' +
    '• <b>Было:</b> -80 галлеонов / -8 алмазов.\n' +
    '• <b>Стало:</b> <b>-160 галлеонов / -16 алмазов</b> за каждый ошибочный вопрос.',

    '📝 <b>Призыв к авторам:</b>\n' +
    'Подходите к составлению заданий ответственно. Попытки сделать «на тяп-ляп» ради быстрой наживы приведут к существенному снижению ваших выплат или полному отказу в сотрудничестве.',

    '<i>* Данные условия вступили в силу и являются приоритетными при расчете вознаграждений.</i>'
  ]
},
{
  t: ' Модерация и тематики викторин 🧐',
  c: [
    'Для повышения качества игры введена <b>Комиссия по проверке вопросов</b>. Теперь каждая викторина проходит строгий отбор на интересность и уникальность.',

    '🔍 <b>Критерии отбора вопросов:</b>\n' +
    '• <b>Качество:</b> Вопросы должны быть увлекательными и требовать размышлений.\n' +
    '• <b>Уникальность:</b> Слишком простые (напр. «Какой патронус у выдры?») или повторяющиеся вопросы будут исключаться из викторины.\n' +
    '• <b>Субъективность:</b> Помните, что Комиссия и Магистр — тоже игроки. Их решения направлены на благо общего игрового процесса.',

    '🗓 <b>Порядок проведения:</b>\n' +
    '• Даты викторин больше <b>не назначаются заранее</b>.\n' +
    '• Сначала блок вопросов проходит полную проверку Комиссией, и только после одобрения назначается день проведения.\n' +
    '• О результатах модерации автору сообщат в личном порядке.',

    '📚 <b>Расширение тематик (Мир Магии):</b>\n' +
    'Мы ценим интересы каждого студента, поэтому теперь допускаются вопросы по:\n' +
    '• 📖 <b>Книгам</b> вселенной Гарри Поттера;\n' +
    '• 🎬 <b>Фильмам</b> (включая «Фантастических тварей»);\n' +
    '• 🎮 <b>Играм</b> по мотивам вселенной (Hogwarts Legacy и др.).',

    '📢 <b>Важно:</b>\n' +
    'Тема предстоящей викторины всегда указывается в объявлении заранее. Просим участников относиться к разнообразию тем с уважением и пониманием.',

    '🤝 <i>Наша цель — сделать викторины честными, качественными и интересными для всех магов Хогвартса!</i>'
  ]
},
{
  t: 'Еженедельный сплетник 📝🗞️',
  c: [
    '<b>Еженедельный сплетник</b> — это главная независимая газета Хогвартса, где оживает вся внутренняя жизнь школы. Здесь нет скучных отчетов, только живая хроника событий!',

    '📅 <b>График выхода:</b>\n' +
    '• <b>Воскресный выпуск:</b> Самый полный, насыщенный и обсуждаемый номер недели.\n' +
    '• <b>Спецвыпуски:</b> Могут появиться в любой день, если в школе произошло что-то громкое, важное или… подозрительное 👀',

    '🔍 <b>О чем пишет Сплетник:</b>\n' +
    '— Освещение открытых уроков, балов, матчей по квиддичу и рейдов.\n' +
    '— Официальные объявления о сменах профессоров и администрации.\n' +
    '— Разбор громких ссор, скандалов и журналистские расследования.\n' +
    '— Огласка спорных ситуаций, которые волнуют всё магическое сообщество.',

    '👤 <b>Как попасть на страницы газеты:</b>\n' +
    'Любой студент может стать героем статьи! Вы можете попасть в номер:\n' +
    '✨ За выдающиеся магические достижения.\n' +
    '😏 Оказавшись в эпицентре скандала или интриги.\n' +
    '🎲 Совершенно случайно, попав в объектив нашего репортера.',

    '📌 <b>Важно:</b>\n' +
    'Следите за выпусками, чтобы быть в курсе всех перестановок и не пропустить обсуждение самых горячих новостей вашего факультета!'
  ]
}


];
const BOOKS=[ {
  id: 1,
  t: "История магии",
  a: "Батильда Бэгшот",
  cat: "История",
  ic: "fa-book-atlas",
  c: ["#4a2c2a", "#261413"],
  ch: [
    {
      t: "Глава 1: Эпоха Основателей",
      p: [
        "История Хогвартса началась более тысячи лет назад, в эпоху, когда граница между миром магии и миром маглов была нестабильной и опасной. Волшебники жили скрытно, но даже это не всегда спасало их от страха, преследований и жестоких расправ. Маглы, не понимая природы магии, часто считали её злом и стремились уничтожить тех, кто обладал сверхъестественными способностями.",
"В те времена дети-волшебники особенно нуждались в защите. Их силы проявлялись спонтанно и неконтролируемо — предметы могли взлетать в воздух, стекла разбивались, а странные события привлекали нежелательное внимание. Без наставничества такие дети рисковали либо навредить себе, либо выдать существование магического мира.",
"Именно в этих условиях четыре выдающихся мага своего времени — Годрик Гриффиндор, Салазар Слизерин, Ровена Рейвенкло и Хельга Хаффлпафф — приняли судьбоносное решение. Они решили создать школу, которая стала бы не просто местом обучения, но настоящим убежищем для молодых волшебников.",
"Легенды гласят, что встреча основателей произошла не случайно. Каждый из них уже был известен своими достижениями, и слухи о них распространялись по всему магическому сообществу. Их союз стал символом силы, знаний и надежды.",
"Выбор места для школы был непростым. Основатели искали укромное, защищённое место, вдали от магловских поселений. В конце концов их выбор пал на шотландские горы — суровый, но величественный край, идеально подходящий для сокрытия магического замка.",
"Строительство Хогвартса стало настоящим чудом магического искусства. Камни укладывались с помощью заклинаний, башни поднимались ввысь за считанные дни, а защитные чары накладывались с такой силой, что замок становился невидимым и недоступным для посторонних.",
"Ровена Рейвенкло внесла огромный вклад в архитектуру замка. Её гениальный ум позволил создать систему движущихся лестниц, скрытых проходов и изменяющихся коридоров. Хогвартс стал не просто зданием, а живым организмом, который мог меняться и даже запутывать тех, кто не был к нему готов.",
"Годрик Гриффиндор уделял особое внимание защите школы. Он разработал множество защитных чар и заклинаний, а также создал Распределяющую шляпу — магический артефакт, способный анализировать личность ученика. Благодаря ей система факультетов продолжила существовать даже после ухода основателей.",
"Хельга Хаффлпафф отвечала за внутреннюю атмосферу школы. Она стремилась сделать Хогвартс местом, где каждый ученик чувствует себя принятым. Именно благодаря ей в школе укоренилась идея равенства и справедливости.",
"Салазар Слизерин, в свою очередь, был мастером древней и сложной магии. Его знания в области тёмных искусств и защиты были непревзойдёнными. Однако его взгляды на чистоту крови вызывали всё больше споров среди основателей.",
"Поначалу разногласия были незначительными, но со временем они начали углубляться. Слизерин всё настойчивее утверждал, что маглорождённые ученики представляют угрозу. Он опасался, что они могут выдать тайны магического мира своим семьям.",
"Гриффиндор и Хаффлпафф категорически не соглашались с этим мнением. Они верили, что способности к магии не зависят от происхождения, а Рейвенкло считала, что интеллект и стремление к знаниям важнее крови.",
"Конфликт достиг своего апогея, когда Слизерин отказался принимать новых учеников в школу, если они не были чистокровными. Это поставило под угрозу саму идею Хогвартса как места для всех магов.",
"В конечном итоге Салазар Слизерин покинул школу. Его уход стал переломным моментом в истории Хогвартса. Однако перед этим он тайно создал Тайную комнату — скрытое пространство глубоко под замком.",
"Согласно легенде, в Тайной комнате он оставил ужасное существо, подчиняющееся только его наследнику. Это существо должно было однажды вернуться и очистить школу от тех, кого Слизерин считал недостойными.",
"После ухода Слизерина оставшиеся основатели продолжили развивать школу. Они укрепили её традиции, расширили программу обучения и приняли ещё больше учеников.",
"С годами Хогвартс стал известен далеко за пределами Британии. Молодые волшебники со всех уголков стремились попасть в его стены, чтобы получить знания и стать частью великой магической истории.",
"Со временем вокруг замка начали возникать легенды. Говорили, что его стены помнят каждое заклинание, каждый спор и каждую победу. Некоторые ученики утверждали, что по ночам можно услышать отголоски голосов прошлого или увидеть тени, напоминающие силуэты самих основателей.",
"Появлялись и истории о скрытых комнатах, которые открываются лишь избранным, о портретах, ведущих собственные беседы, и о магии, которая словно пронизывает сам воздух. Всё это делало Хогвартс не просто школой, а настоящим символом магического мира.",
"Несмотря на прошедшие века, дух основателей до сих пор ощущается в каждом уголке замка. Их идеи, споры и мечты навсегда вписаны в историю Хогвартса.",
"Эпоха Основателей стала фундаментом, на котором была построена вся система магического образования. Без их усилий мир волшебников мог бы выглядеть совершенно иначе.",
"И хотя многие детали тех времён утеряны или обросли легендами, одно остаётся неизменным: Хогвартс был и остаётся символом надежды, знаний и силы магии."
      ]
    },
    {
      t: "Глава 2: Восстания гоблинов",
      p: [
        "Семнадцатый и восемнадцатый века стали одним из самых напряжённых периодов в истории магического мира. В это время отношения между волшебниками и гоблинами достигли критической точки, что привело к серии вооружённых конфликтов, вошедших в историю как Восстания гоблинов.",
    
    "Корни противостояния уходили глубоко в прошлое. Гоблины издавна были признанными мастерами кузнечного дела, особенно в создании зачарованных металлов и артефактов. Они владели собственной формой магии, отличной от человеческой, и гордились своими традициями, однако магическое сообщество волшебников долгое время не признавало их равными.",
    
    "Главным источником конфликта стало право владения волшебными палочками. Министерство магии и большинство волшебников считали, что палочки должны находиться исключительно в руках людей, так как считалось, что только они способны правильно контролировать магию через этот инструмент, тогда как гоблины утверждали, что это форма дискриминации, поскольку они обладали собственной магической системой и не нуждались в палочках для колдовства.",
    
    "Напряжение нарастало десятилетиями. Попытки дипломатических переговоров часто заканчивались провалом из-за взаимного недоверия, так как гоблины считали, что их вклад в магическое общество недооценён, особенно в сфере создания магических предметов и хранения богатств в банках вроде Гринготтса, а волшебники опасались роста их влияния.",
    
    "Ситуация окончательно вышла из-под контроля в конце XVII века, когда вспыхнуло первое крупное восстание, и гоблинские кланы, объединившись под единым знаменем, начали организованные атаки на магические поселения и форпосты волшебников.",
    
    "Одним из самых известных лидеров восстания стал гоблин по прозвищу Ург Грязный, чьё настоящее имя затерялось в истории, но хроники описывают его как харизматичного и крайне жесткого военачальника, сумевшего объединить разрозненные кланы гоблинов, что сделало восстание особенно опасным для волшебников.",
    
    "Боевые действия велись в разных регионах, но особенно ожесточённые столкновения происходили в окрестностях Хогсмида, который не раз переходил из рук в руки, а местные жители были вынуждены покидать свои дома, при этом даже территория Хогвартса временно использовалась как стратегический пункт для координации обороны благодаря своим мощнейшим защитным чарам.",
    
    "В ходе конфликта обе стороны применяли не только боевую магию, но и тактические хитрости: гоблины использовали подземные туннели и знание рудников, а волшебники — заклинания защиты, иллюзии и древние боевые чары, из-за чего война затянулась на годы и привела к большим потерям с обеих сторон.",
    
    "В конце концов конфликт был подавлен силами Министерства магии, однако не был полностью решён, и вместо уничтожения одной из сторон начались переговоры, которые привели к частичному признанию прав гоблинов и созданию новых законов, регулирующих их взаимодействие с волшебным миром.",
    
    "Тем не менее недоверие между гоблинами и волшебниками сохранилось и до сих пор проявляется в экономике, политике и вопросах владения магическими артефактами, где обе стороны продолжают отстаивать свои интересы.",
    
    "Восстания гоблинов стали важным уроком для магического мира, показав, что игнорирование прав разумных магических существ неизбежно приводит к конфликтам, хотя до полного примирения между сторонами ещё далеко."
      ]
    },
    {
      t: "Глава 3: Средневековое сжигание ведьм",
      p: [
       "В четырнадцатом веке преследования со стороны маглов достигли такого уровня, что магическое сообщество оказалось вынуждено разработать целую систему маскировки и самозащиты, направленную на сохранение тайны существования волшебников.",
    
    "Вопреки распространённым магловским легендам, настоящие ведьмы и колдуны крайне редко становились жертвами костров, поскольку обладали целым набором защитных заклинаний, позволяющих не только избегать реальной опасности, но и контролировать восприятие происходящего.",
    
    "Одним из наиболее известных приёмов являлось заклинание, создающее иллюзию горения, при этом фактически заменяя пламя мягким магическим воздействием, которое ощущалось скорее как щекотка или лёгкое тепло, позволяя магу убедительно изображать страдания, не испытывая никакого вреда.",
    
    "Из-за этого среди магов даже возникла своеобразная репутация таких ситуаций как своеобразного испытания стойкости, а некоторые особенно эксцентричные волшебники относились к этому как к неудачному, но терпимому ритуалу маскировки.",
    
    "Известная ведьма Венделина Странная вошла в историю как одна из самых эксцентричных фигур своего времени, поскольку, по преданиям, она была настолько увлечена ощущением магического пламени, что позволяла себя якобы поймать маглам более сорока семи раз, каждый раз меняя облик и личность.",
    
    "Считается, что её поведение было связано не только с чувством юмора, но и с желанием изучить границы маскировочных чар, а также проверить устойчивость магических иллюзий в условиях максимального риска разоблачения.",
    
    "Несмотря на относительную безопасность для опытных магов, ситуация оставалась крайне опасной для детей-волшебников, которые ещё не могли контролировать свои способности и легко могли выдать себя случайным магическим выбросом.",
    
    "Именно страх за молодых волшебников и невозможность обеспечить их безопасность в условиях усиливающихся преследований стали одним из ключевых факторов, подтолкнувших магическое сообщество к созданию Международного статута о секретности.",
    
    "Этот документ стал поворотным моментом в истории магического мира, официально закрепив необходимость полного скрытия магии от маглов и заложив основы современного устройства волшебного общества."
      ]
    },
    {
      t: "Глава 4: Статут о секретности 1692 года",
      p: [
         "Введение Международного статута о секретности в 1692 году стало ключевым историческим событием, которое навсегда изменило структуру магического мира и определило его дальнейшее развитие как скрытого общества, существующего параллельно с миром маглов.",
    
    "К концу XVII века магическое сообщество оказалось в состоянии системного кризиса. С одной стороны, усиливались магловские преследования и религиозные охоты на ведьм, с другой — возрастало количество случайных магических инцидентов, которые становились всё труднее скрывать из-за плотности населения и развития городов.",
    
    "Хотя многие современные историки считают масштабы магловских «охот на ведьм» преувеличенными, в реальности даже единичные случаи разоблачения магии приводили к цепной реакции страха, насилия и массовых обвинений, создавая крайне нестабильную обстановку для волшебников.",
    
    "Внутри магического сообщества существовали серьёзные разногласия относительно будущего сосуществования с маглами. Радикальные фракции предлагали силовое подчинение немагического мира, в то время как реформаторы выступали за полную интеграцию и открытое сосуществование. Однако обе крайние позиции были признаны непрактичными.",
    
    "На международной конференции магов 1692 года было принято компромиссное решение о полном сокрытии магического мира. Это решение стало основой Международного статута о секретности, который обязал все магические правительства координировать действия по изоляции магического сообщества.",
    
    "Статут закрепил принцип абсолютной секретности как высший приоритет магической политики. Любое публичное проявление магии вне контролируемых условий стало считаться нарушением международного законодательства и подлежало немедленному устранению последствий.",
    
    "Для реализации этих требований были расширены полномочия Министерств магии по всему миру. Были созданы специализированные подразделения, отвечающие за сокрытие магических происшествий, включая оперативные группы стирания памяти, зачистки территорий и маскировки следов магической активности.",
    
    "Заклинания модификации памяти, ранее использовавшиеся крайне редко, были стандартизированы и поставлены на поток, что позволило эффективно управлять последствиями контактов между магическим и немагическим мирами.",
    
    "Одновременно началась масштабная реорганизация магической инфраструктуры. Волшебные поселения были либо скрыты мощными чарами иллюзии, либо интегрированы в магловские территории таким образом, чтобы выглядеть как заброшенные, закрытые или незначительные объекты.",
    
    "Хогвартс получил один из самых сложных комплексов защитных чар в магическом мире. Помимо базовой невидимости, замок был окружён слоями магии, создающими искажение восприятия, из-за чего любые попытки маглов приблизиться к нему приводили к ощущению опасности, усталости или необходимости немедленно покинуть местность.",
    
    "Дополнительно были наложены чары пространственного отвода, из-за которых даже точные координаты Хогвартса могли не соответствовать реальному положению замка на местности, что делало его практически недоступным для немагического обнаружения.",
    
    "Экономические последствия статута оказались не менее значительными. Магическому сообществу пришлось полностью перестроить торговые связи, отказаться от открытых рынков и создать скрытые каналы обмена магическими товарами и услугами.",
    
    "Со временем возникла параллельная магическая экономика, включающая специализированные банки, такие как Гринготтс, тайные торговые кварталы и закрытые рынки редких магических ингредиентов и артефактов.",
    
    "Политически статут привёл к централизации власти в Министерствах магии, которые получили исключительное право определять границы взаимодействия с магловским миром и контролировать все случаи возможного нарушения секретности.",
    
    "Несмотря на эффективность этих мер, изоляция имела долгосрочные культурные последствия. Магическое общество постепенно начало развиваться независимо от магловского, что привело к значительным различиям в технологиях, науке и социальном устройстве.",
    
    "Историки отмечают, что именно после 1692 года началось формирование современной идентичности волшебников как отдельного, скрытого народа со своими законами, традициями и мировоззрением.",
    
    "Таким образом, Статут о секретности стал не просто юридическим документом, а фундаментальной точкой перелома, определившей судьбу всего магического мира и его существование в тени истории маглов."
      ]
    }
  ]
},
  {
  id: 2,
  t: "Силы зла: Руководство по самозащите",
  a: "Квентин Тримбл",
  cat: "Защита",
  ic: "fa-shield-halved",
  c: ["#2c3e50", "#000000"],
  ch: [
    {
      t: "Глава 1: Фундаментальная классификация Тьмы",
      p: [
        "Прежде чем поднять палочку для защиты, маг обязан понимать, что тёмная магия не является хаотичным набором опасных заклинаний, а представляет собой строго структурированную систему воздействий, подчиняющуюся определённым магическим законам. Ошибка в классификации угрозы считается одной из главных причин поражений даже среди опытных дуэлянтов и авроров.",
    
    "Согласно классической школе защитной магии, тёмные воздействия делятся на три фундаментальных уровня: Сглазы, Порча и Проклятия. Эти категории различаются не только по силе, но и по глубине воздействия на магическую структуру существа, а также по устойчивости эффекта и сложности снятия.",
    
    "Сглазы (Jinxes) представляют собой базовый уровень магического воздействия, предназначенный для кратковременного нарушения координации, концентрации или сенсорного восприятия цели. Их основная особенность заключается в лёгкости наложения и сравнительно слабой магической нагрузке, что делает их популярным инструментом дуэльной практики начального и среднего уровня.",
    
    "Типичные эффекты сглазов включают временную потерю равновесия, искажение моторики, спонтанные изменения поведения или локальные физические неудобства. Однако их реальная опасность заключается в тактическом применении: даже слабый сглаз, наложенный в нужный момент, способен полностью нарушить ритм боя противника.",
    
    "Некоторые школы дуэльной магии рассматривают сглазы как форму «магического давления», цель которого — не нанесение ущерба, а создание психологического и физического дисбаланса, позволяющего перейти к более мощным атакам.",
    
    "Порча (Hexes) представляет собой промежуточный, но значительно более опасный уровень тёмной магии. Она характеризуется устойчивым воздействием на физическое состояние, поведенческие реакции или локальные магические функции цели. В отличие от сглазов, порча сохраняет эффект даже после прекращения прямого магического контакта.",
    
    "Порча часто используется как инструмент контроля пространства боя, ограничивая возможности противника к передвижению, концентрации или применению сложных заклинаний. Некоторые формы порчи способны накапливаться, усиливая эффект при повторном воздействии, что делает их особенно опасными в затяжных дуэлях.",
    
    "Наиболее известные примеры включают «Летучемышиный сглаз», «Заклятие спутанных мыслей» и ряд производных чар, изучаемых в расширенных курсах Защиты от Тёмных искусств. Их объединяет способность воздействовать не только на тело, но и на когнитивные процессы цели.",
    
    "Отдельные исследования Министерства магии указывают, что некоторые виды порчи могут вызывать частичную дестабилизацию магического поля волшебника, снижая эффективность последующих заклинаний и увеличивая вероятность магического отката.",
    
    "Проклятия (Curses) являются высшей формой тёмной магии и представляют собой долговременные или потенциально необратимые магические воздействия, направленные на разрушение, подчинение или искажение сущности цели. Они считаются крайне опасными даже при косвенном изучении.",
    
    "В отличие от сглазов и порчи, проклятия часто взаимодействуют не с поверхностной магией, а с глубинной структурой магической ауры, что делает их устойчивыми к большинству стандартных методов снятия чар.",
    
    "Некоторые проклятия обладают эффектом магической инерции, при котором любое вмешательство в структуру заклинания может привести к его усилению, перераспределению или переносу на другого носителя, включая самого заклинателя.",
    
    "Существуют также проклятия, воздействующие на временное восприятие, память или способность к колдовству. В особо тяжёлых случаях зафиксированы изменения личности, полная утрата магических способностей или постепенное разрушение магической связи с палочкой.",
    
    "Из-за высокой опасности проклятий их изучение строго регулируется Министерством магии и допускается только для специалистов в области защитной магии, авроров и сотрудников отделов по борьбе с тёмными искусствами.",
    
    "Практическая работа с проклятиями требует не только знания контр-заклятий, но и понимания принципов магической симметрии, отката энергии и поведения заклинаний в нестабильной среде.",
    
    "Ошибки при попытке снять проклятие могут привести к катастрофическим последствиям, включая усиление эффекта, его распространение на окружающих или формирование устойчивого магического искажения в зоне воздействия.",
    
    "Именно поэтому в современной дуэльной практике классификация тёмной магии рассматривается как первоочередной навык: маг должен не просто реагировать на угрозу, а мгновенно определять её природу, уровень и потенциальную динамику развития.",
    
    "Без этого понимания даже опытный волшебник рискует оказаться в ситуации, где выбранное защитное заклинание не только не помогает, но и активирует скрытые механизмы тёмного воздействия.",
    
    "Таким образом, фундаментальная классификация Тьмы является не теоретической дисциплиной, а основой выживания в реальных магических конфликтах, определяющей исход большинства дуэлей ещё до первого произнесённого заклинания."
      ]
    },
    {
      t: "Глава 2: Искусство Протего и щитовые чары",
      p: [
        "Щитовые чары представляют собой основу всей защитной магии и считаются первым и одновременно последним рубежом обороны в дуэльной практике. В отличие от атакующих заклинаний, их цель заключается не в нанесении урона, а в полном перехвате, рассеивании или перенаправлении магического воздействия противника.",
    
    "Заклинание 'Протего' (Protego) является базовым элементом этой системы и формирует невидимый магический барьер, который способен отражать, поглощать или изменять траекторию большинства заклинаний малой и средней силы. При правильном исполнении щит не только блокирует атаку, но и может возвращать её обратно к заклинателю с частичной потерей энергии.",
    
    "Ключевым фактором эффективности 'Протего' является не сила магического выброса, а точность намерения и момент активации. Щит должен формироваться в момент, предшествующий контакту заклинаний, иначе энергия атаки успевает проникнуть в магическое поле волшебника и нарушить стабильность защиты.",
    
    "Опытные дуэлянты рассматривают 'Протего' не как статическую преграду, а как динамическую систему, способную адаптироваться к типу входящего заклинания. В продвинутой практике щит может менять плотность, форму и угол отражения в зависимости от характера угрозы.",
    
    "Однако важно помнить, что стандартная форма 'Протего' имеет существенные ограничения. Она не предназначена для блокировки Непростительных заклятий, а также ряда высокоуровневых тёмных чар, обладающих эффектом проникновения через магическую защиту или разрушения щитовой структуры на уровне ядра магии.",
    
    "Для расширения функциональности были разработаны модификации щитовых чар. 'Протего Тоталум' используется для создания масштабного защитного поля, способного накрывать целые территории или группы волшебников, и применяется преимущественно в условиях осад или массовых магических конфликтов.",
    
    "Другая форма — 'Протего Дуо' — направлена на усиление плотности и устойчивости индивидуального щита. Она увеличивает сопротивление к повторным атакам и позволяет выдерживать серию заклинаний средней силы без разрушения структуры барьера.",
    
    "Существуют также специализированные вариации, адаптированные под конкретные типы угроз, включая магические снаряды, ментальные атаки и заклинания пространственного искажения. Однако их использование требует высокой квалификации и глубокого понимания магической теории защиты.",
    
    "Мастерство защитной магии определяется не количеством произнесённых заклинаний, а скоростью реакции и точностью магического намерения. В дуэльной практике считается, что даже десятая доля секунды задержки между обнаружением угрозы и активацией щита может стать решающей.",
    
    "Щитовые чары требуют полного контроля над концентрацией. Любое отвлечение, эмоциональный всплеск или неуверенность в момент создания барьера приводит к его нестабильности, что выражается в искажении формы щита или частичной утрате его защитных свойств.",
    
    "В экстремальных условиях опытные маги способны удерживать щитовые чары в непрерывном состоянии, создавая эффект постоянного магического купола, однако это требует значительных энергетических затрат и высокого уровня магической выносливости.",
    
    "Следует также учитывать, что взаимодействие нескольких щитов может привести к непредсказуемым эффектам, включая резонанс, взаимное усиление или разрушение магических структур при пересечении полей защиты.",
    
    "Важным аспектом является и стратегическое использование щитовых чар. В бою они применяются не только для защиты, но и для контроля пространства, направления атаки противника и создания временных окон для контрнаступления.",
    
    "Таким образом, 'Протего' и его модификации являются не просто защитными заклинаниями, а фундаментальной системой магической обороны, определяющей выживаемость волшебника в любом магическом конфликте.",
    
    "Игнорирование принципов щитовой магии или недооценка её сложности практически гарантирует поражение даже в дуэли с менее опытным противником, если тот лучше владеет защитными чарами и способен выдерживать давление атаки."
      ]
    },
    {
      t: "Глава 3: Темные существа и способы борьбы",
      p: [
        "Тёмные существа представляют собой особую категорию угроз в магическом мире, отличающуюся от заклинаний тем, что их природа не всегда подчиняется воле заклинателя. Эти формы жизни или полумагические сущности формируются под влиянием древней магии, эмоциональных выбросов, а также длительного воздействия проклятых или нестабильных магических зон.",
    
    "В отличие от человеческой тёмной магии, которая требует намеренного создания заклинания, многие существа обладают инстинктивной магической природой. Их поведение не всегда можно объяснить рационально, однако оно почти всегда связано с базовыми эмоциональными состояниями: страхом, голодом, агрессией или магическим голодом.",
    
    "Красные колпаки относятся к наиболее изученным видам агрессивных существ низшего уровня. Они обитают преимущественно в местах исторических сражений, старых крепостях и заброшенных подземных сооружениях, где магическая энергия насилия и смерти сохраняется в остаточном виде.",
    
    "Их поведение носит засадный характер: они предпочитают атаковать из темноты, используя знание местности и элемент неожиданности. Несмотря на относительно низкий уровень индивидуальной силы, их опасность возрастает при групповых нападениях или в условиях ограниченной видимости.",
    
    "Гриндилоу представляют собой водных хищников, адаптированных к жизни в мутной или стоячей воде. Их анатомия позволяет им эффективно удерживать жертву под водой, используя длинные пальцеобразные конечности с высокой силой захвата.",
    
    "Тактика борьбы с гриндилоу основана на контроле среды: создание воздушных пузырей, изменение плотности воды или временное обездвиживание существа с помощью точечных заклинаний, нарушающих его мышечную координацию.",
    
    "Однако наиболее важным аспектом защиты является предотвращение паники, поскольку гриндилоу реагируют на хаотичные движения и страх, усиливая коллективное нападение при признаках слабости жертвы.",
    
    "Боггарты занимают особое место в классификации тёмных существ из-за своей уникальной способности принимать форму индивидуального страха каждого наблюдателя. Это делает их не просто физической угрозой, а психологическим отражением внутреннего состояния волшебника.",
    
    "Их природа до конца не изучена, однако большинство исследователей сходятся во мнении, что боггарты питаются не материальной энергией, а эмоциональным резонансом страха, усиливая собственную форму в зависимости от интенсивности реакции цели.",
    
    "Метод 'Ридикулус' основан на фундаментальном принципе магической трансформации восприятия. Вместо прямого подавления существа волшебник изменяет интерпретацию угрозы, заставляя магическое ядро боггарта перестроиться в нелогичную или комическую форму.",
    
    "Этот процесс требует высокой концентрации и ментальной устойчивости, поскольку любое колебание в уверенности может привести к усилению исходного образа страха и временной дестабилизации заклинателя.",
    
    "Более опасные категории тёмных существ включают инфери — оживлённые магией тела, лишённые собственной воли и управляемые некромагическими воздействиями. Они не чувствуют боли и не реагируют на обычные формы устрашения, что делает их крайне сложными противниками.",
    
    "Существуют также сущности, известные как дементороподобные, чьё воздействие связано с поглощением положительных эмоций и усилением депрессивных состояний у жертвы. Их присутствие может вызывать массовую потерю магической концентрации и дезориентацию.",
    
    "Общей характеристикой всех тёмных существ является их способность усиливаться за счёт эмоционального дисбаланса окружающей среды. Страх, паника и хаос выступают для них не только сигналами к атаке, но и источниками дополнительной силы.",
    
    "Поэтому одной из ключевых стратегий выживания является эмоциональная стабилизация. Волшебник, сохраняющий контроль над собственными реакциями, автоматически снижает эффективность большинства тёмных существ в радиусе воздействия.",
    
    "Современная теория защиты рассматривает страх не как слабость, а как магический параметр, который может быть либо подавлен, либо использован против самого заклинателя. Опытные маги учатся преобразовывать страх в концентрацию, снижая его влияние на магическое поле.",
    
    "Таким образом, борьба с тёмными существами представляет собой комплексную дисциплину, объединяющую магическую теорию, практическую дуэльную подготовку и глубокое понимание психоэмоциональных процессов, влияющих на магию.",
    
    "Недооценка любой из этих составляющих приводит к резкому снижению эффективности защиты и значительно увеличивает риск поражения даже при наличии правильно изученных заклинаний.",
    
    "Именно поэтому курс изучения тёмных существ считается одним из ключевых элементов подготовки профессиональных волшебников, авроров и исследователей магической безопасности."
      ]
    },
    {
      t: "Глава 4: Психологическая устойчивость и Окклюменция",
      p: [
        "Защита разума считается одной из наиболее сложных и в то же время наиболее недооценённых дисциплин магического искусства. В отличие от физических или энергетических форм магии, ментальная магия воздействует непосредственно на структуру сознания, обходя традиционные защитные чары и атакуя волшебника через его воспоминания, эмоции и ассоциативные связи.",
    
    "Темные маги часто используют Легилименцию — искусство проникновения в разум другого существа — для извлечения информации, выявления слабостей и эмоциональных триггеров. При недостаточной ментальной защите даже краткий контакт может привести к утечке ключевых воспоминаний или стратегически важной информации.",
    
    "Окклюменция представляет собой ответную дисциплину, направленную на создание структурированных ментальных барьеров, препятствующих проникновению извне. В отличие от обычной концентрации, она требует активного управления внутренними состояниями и способности разделять мысли на контролируемые и недоступные слои.",
    
    "Основной принцип Окклюменции заключается не в полном уничтожении мыслей, а в их упорядочивании и изоляции. Опытный практик формирует внутреннюю ментальную архитектуру, в которой эмоционально уязвимые воспоминания скрываются за слоями нейтральных или искусственно созданных образов.",
    
    "Визуализация часто используется как ключевой инструмент: разум представляется в виде закрытой структуры — лабиринта, зеркального зала или ледяной крепости, в зависимости от индивидуальных особенностей волшебника. Эти образы служат не просто метафорой, а реальным механизмом концентрации магической защиты.",
    
    "Цель практики заключается в достижении состояния ментальной неподвижности, при котором эмоции не исчезают полностью, но перестают влиять на доступность информации. Разум становится структурированным и предсказуемым, что значительно затрудняет его считывание извне.",
    
    "Важно понимать, что абсолютная пустота сознания не является целью Окклюменции. Полное подавление мыслей приводит к нестабильности и потере контроля над магическим фоном сознания, что может сделать волшебника более уязвимым для ментальных атак.",
    
    "Вместо этого используется принцип контролируемой многослойности: поверхностные мысли могут оставаться активными и даже намеренно вводящими в заблуждение, в то время как ключевые воспоминания и эмоциональные якоря помещаются в глубинные уровни защиты.",
    
    "Практика Легилименции и Окклюменции часто рассматривается как ментальная дуэль, в которой исход зависит не от силы, а от устойчивости психики и скорости адаптации к вторжению. Даже опытные маги могут испытывать трудности при длительном ментальном давлении.",
    
    "Квентин Тримбл подчёркивает важность регулярных тренировок, направленных на укрепление внутренней дисциплины. Медитация, контролируемое дыхание и визуализация устойчивых образов считаются базовыми методами подготовки.",
    
    "Ежедневная практика позволяет снизить эмоциональную реактивность, что напрямую влияет на эффективность защиты. Чем меньше хаотических эмоциональных всплесков в сознании, тем сложнее противнику найти уязвимые точки для ментального проникновения.",
    
    "Особое внимание уделяется тренировке реакции на внезапное вторжение. В продвинутых курсах маги учатся распознавать попытку Легилименции как можно раньше и мгновенно перестраивать структуру сознания в защитный режим.",
    
    "Некоторые школы Окклюменции используют метод 'разделённого сознания', при котором разум одновременно удерживает несколько независимых потоков мыслей, затрудняя их интерпретацию для внешнего наблюдателя.",
    
    "Однако данный метод требует высокой степени самоконтроля и может привести к ментальной перегрузке при неправильном применении, особенно у начинающих практиков.",
    
    "В условиях реального конфликта ментальная защита часто становится решающим фактором выживания, поскольку позволяет сохранить стратегическое мышление даже при частичном физическом или магическом подавлении.",
    
    "Таким образом, Окклюменция является не просто защитной техникой, а фундаментальной системой ментальной устойчивости, необходимой каждому магу, который может столкнуться с интеллектуально и эмоционально превосходящим противником.",
    
    "Игнорирование развития этой дисциплины значительно повышает риск психологического воздействия, манипуляции и потери контроля над собственными действиями в критических ситуациях."
      ]
    }
  ]
},
  {
  id: 3,
  t: "Расширенное руководство по зельеварению",
  a: "Либациус Бораго",
  cat: "Зельеварение",
  ic: "fa-vial-circle-check",
  c: ["#1e3a2f", "#0a1a14"],
  ch: [
    {
      t: "Глава 1: Теория алхимического резонанса",
      p: [
        "Зельеварение представляет собой строго структурированную дисциплину, находящуюся на границе между естественной магией и прикладной алхимией. В отличие от заклинаний, которые опираются на мгновенный магический импульс, зелья требуют длительного процесса формирования устойчивых магических связей между ингредиентами, температурой и внешней средой.",
    
    "Основополагающим принципом всей алхимической практики является концепция алхимического резонанса — явления, при котором взаимодействие компонентов приводит к возникновению новой магической структуры, не сводимой к сумме свойств исходных ингредиентов. Именно этот резонанс определяет силу, стабильность и характер конечного зелья.",
    
    "Важно понимать, что зелье не является механической смесью. Даже при идентичном рецепте малейшие изменения порядка добавления, времени выдержки или интенсивности магического воздействия способны радикально изменить итоговый результат. В некоторых случаях это приводит к усилению эффекта, в других — к полной дестабилизации состава.",
    
    "Первое фундаментальное правило зельевара формулируется как: 'Слушай котёл'. Это означает необходимость постоянного анализа обратной связи, которую предоставляет сам процесс варки. Котёл в алхимическом смысле рассматривается как активный участник реакции, а не пассивный инструмент.",
    
    "Цвет пара является одним из ключевых индикаторов алхимического состояния смеси. Например, бледно-голубой оттенок указывает на стабильную фазу магического равновесия, при которой энергия компонентов распределена равномерно и не вызывает разрушительных колебаний структуры зелья.",
    
    "Изменение цвета пара в сторону насыщенных или тёмных оттенков часто свидетельствует о накоплении нестабильной магической энергии. Игнорирование этого признака может привести к фазовому срыву реакции и последующему взрыву котла или образованию токсичных побочных соединений.",
    
    "Звуковая активность смеси также является важным диагностическим параметром. Равномерное и ритмичное лопание пузырьков указывает на устойчивый процесс синтеза, тогда как хаотичные всплески свидетельствуют о нарушении алхимического баланса и необходимости корректировки температуры или ингредиентов.",
    
    "Особое значение имеет запах, который формируется на молекулярно-магическом уровне. Опытные зельевары способны по тонким изменениям ароматического профиля определить стадию реакции задолго до появления визуальных признаков нестабильности. Это требует многолетней тренировки сенсорного восприятия.",
    
    "Направление помешивания является критическим параметром контроля реакции. Движение против часовой стрелки снижает скорость магической активности, позволяя стабилизировать сложные или потенциально опасные соединения, требующие постепенного формирования структуры.",
    
    "Напротив, движение по часовой стрелке ускоряет алхимические процессы, усиливая энергетическую насыщенность смеси и сокращая общее время реакции. Однако чрезмерное ускорение может привести к разрыву магических связей и неконтролируемому выбросу энергии.",
    
    "Даже незначительная ошибка в направлении или ритме помешивания способна вызвать каскадные изменения в структуре зелья. В ряде задокументированных случаев отклонение всего на один цикл приводило к превращению лечебных составов в агрессивные коррозионные растворы.",
    
    "Температурный режим рассматривается как один из ключевых факторов стабильности. Каждый ингредиент обладает собственным диапазоном термической устойчивости, в пределах которого его магическая структура сохраняет целостность и способность к взаимодействию.",
    
    "Превышение допустимой температуры приводит к разрушению магических связей на уровне основы вещества, в то время как недостаточный нагрев может полностью блокировать реакцию, оставляя ингредиенты инертными и бесполезными в контексте зелья.",
    
    "Алхимический резонанс также зависит от совместимости компонентов. Некоторые ингредиенты вступают в синергетическое усиление, создавая эффект, превышающий сумму их индивидуальных свойств, тогда как другие вызывают взаимное подавление или разрушение магической структуры.",
    
    "Опытные зельевары ведут сложные таблицы совместимости, однако даже они признают, что абсолютная предсказуемость в алхимии невозможна из-за влияния внешних магических факторов, состояния котла и индивидуальной реакции среды.",
    
    "Таким образом, зельеварение представляет собой дисциплину, в которой строгая наука постоянно взаимодействует с элементами интуиции. Понимание формул недостаточно без умения интерпретировать тонкие сигналы процесса.",
    
    "Именно сочетание точного расчёта, наблюдательности и развитого магического восприятия позволяет мастеру контролировать алхимический резонанс и превращать нестабильные компоненты в управляемые магические структуры."
      ]
    },
    {
      t: "Глава 2: Подготовка и экстракция ингредиентов",
      p: [
       "Подготовка ингредиентов является фундаментальным этапом зельеварения, на котором закладывается будущая стабильность и магическая чистота любого состава. Ошибки, допущенные до момента начала варки, практически невозможно исправить даже самыми точными корректировками температуры или порядка добавления компонентов.",

"Методы экстракции активных веществ зависят не только от физической природы ингредиента, но и от его магической структуры. В отличие от обычной алхимии, магическое зельеварение требует учитывать так называемое внутреннее ядро реагента — скрытую магическую форму, определяющую его поведение при взаимодействии с другими компонентами.",

"Так, при работе с сонными бобами стандартные методики рекомендуют нарезку тонкими ломтиками для равномерного выделения сока. Однако опытные зельевары используют метод механического давления серебряным кинжалом, поскольку серебро снижает энергетические потери и сохраняет целостность эфирных соединений. В результате выход активного сока увеличивается, а его магическая плотность становится более стабильной.",

"Важно учитывать, что инструмент экстракции играет не меньшую роль, чем сам ингредиент. Металлы с высокой магической проводимостью, такие как серебро и платина, усиливают чистоту извлекаемого вещества, тогда как железо и сталь могут частично подавлять магическую активность, особенно у чувствительных компонентов.",

"Особого внимания требует корень мандрагоры, один из наиболее опасных и нестабильных ингредиентов в зельеварении. Его магическая структура напрямую связана с фазами луны, температурой окружающей среды и даже эмоциональным состоянием мага, выполняющего обработку.",

"При приготовлении сыворотки правды корень мандрагоры необходимо шинковать исключительно при полной луне, когда его магическая активность достигает максимальной упорядоченности. В противном случае структура вещества становится хаотичной, что может привести к искажению ментального воздействия конечного зелья.",

"Для противоядий же используется противоположный подход — длительное перетирание в каменной ступке. Камень в данном процессе выступает как стабилизатор магического поля, не вступая в резонанс с активными компонентами и позволяя сохранить природную структуру корня.",

"Использование металлических инструментов при обработке мандрагоры строго запрещено, поскольку металл вызывает паразитный резонанс, разрушающий внутренние магические связи и превращающий ингредиент в инертную массу.",

"Процесс измельчения должен происходить медленно и равномерно. Слишком резкое давление может привести к активации защитных магических механизмов растения, что делает дальнейшее использование корня невозможным без предварительной очистки сложными нейтрализующими заклинаниями.",

"Отдельную категорию ингредиентов составляют златоглазки, используемые в сложных трансформационных и адаптационных зельях. Их магическая структура отличается высокой чувствительностью к времени обработки и требует строго регламентированного цикла термического воздействия.",

"Стандартный процесс обработки длится ровно двадцать один день, что связано с внутренним биомагическим циклом существа. Любое отклонение от этого срока приводит к нарушению формирования магических связей внутри его тканей.",

"Сокращение времени обработки даже на минимальный интервал может вызвать эффект частичной трансформации, при котором конечное зелье приобретает нестабильные свойства и начинает воздействовать на объект непредсказуемым образом.",

"Особенно опасным считается так называемый эффект остаточного резонанса, при котором неправильно обработанный ингредиент продолжает влиять на магическую структуру зелья даже после завершения его приготовления.",

"Помимо этого, важно учитывать фазовое состояние ингредиентов. Некоторые компоненты требуют предварительной сушки в тёмных помещениях, другие — наоборот, должны сохранять естественную влажность до момента добавления в котёл.",

"Любое отклонение от условий хранения приводит к изменению алхимического потенциала вещества, что напрямую влияет на силу, стабильность и безопасность готового зелья.",

"Таким образом, подготовка и экстракция ингредиентов — это не механический процесс, а сложная система взаимодействий между магией, материей и временем, требующая от зельевара не только знаний, но и высокой наблюдательности и точности."
      ]
    },
    {
      t: "Глава 3: Классификация котлов и температурные режимы",
      p: [
        "Котёл в зельеварении рассматривается не как пассивный сосуд, а как активная магико-алхимическая система, способная изменять характер реакции внутри себя. Его материал, форма, толщина стенок и даже история предыдущих варок влияют на поведение будущих зелий, формируя так называемую остаточную магическую память.",

"Эта память может как стабилизировать процесс, так и полностью исказить его, если предыдущие зелья оставили сильный резонансный след. Именно поэтому опытные зельевары проводят регулярную магическую очистку котлов перед приготовлением сложных составов.",

"Оловянные котлы применяются в базовой подготовке не только из-за их дешевизны, но и благодаря высокой инертности. Они почти не взаимодействуют с магическим полем ингредиентов, что снижает риск непредсказуемых реакций, но одновременно ограничивает глубину алхимического резонанса.",

"Однако даже оловянные котлы имеют свои ограничения: при длительном использовании они постепенно накапливают микрорезонансные искажения, особенно если в них варились зелья с нестабильной энергетикой. Это может проявляться в виде случайных вспышек пузырьков или изменения цвета пара без видимой причины.",

"Медные котлы занимают промежуточное положение и считаются наиболее универсальными в практической зельеварной работе. Их структура позволяет эффективно распределять тепло и магическую энергию, создавая равномерное поле реакции.",

"Однако медь обладает повышенной чувствительностью к эмоциональному состоянию мага. При сильном стрессе или нестабильной концентрации зельевара котёл может усиливать хаотические колебания внутри смеси, что приводит к нестабильным результатам даже при идеально соблюдённом рецепте.",

"Золотые котлы представляют собой вершину алхимической инженерии. Их магическая инертность настолько высока, что они практически не искажают структуру зелья, позволяя ему развиваться исключительно по заложенной формуле резонанса.",

"Именно поэтому такие котлы используются для крайне сложных составов, где малейшее вмешательство материала может разрушить хрупкий баланс ингредиентов. Однако высокая стоимость и сложность обработки делают их доступными только опытным мастерам.",

"Существует также категория экспериментальных котлов, созданных из редких сплавов или зачарованных материалов. Они способны усиливать определённые типы зелий, но при этом требуют строгого соответствия рецептуре, иначе становятся источником магических катастроф.",

"Температурный режим в зельеварении рассматривается как форма управления энергией реакции. В отличие от обычного нагрева, магическая температура включает в себя интенсивность магического возбуждения, которое распространяется через котёл и влияет на структуру связей между ингредиентами.",

"Низкотемпературные процессы используются для создания стабильных, медленно формирующихся зелий. В таких условиях магические связи формируются постепенно, что позволяет контролировать каждый этап реакции и минимизировать риск побочных эффектов.",

"Средний температурный режим считается наиболее сбалансированным и используется в большинстве стандартных рецептов. Он обеспечивает оптимальное сочетание скорости реакции и стабильности магического поля.",

"Высокотемпературные процессы требуют особой подготовки и применяются только для агрессивных или трансформационных зелий. В таких условиях магическая энергия начинает вести себя нестабильно, и контроль над процессом становится критически важным.",

"При превышении критического порога температуры возникает явление магического перегрева, при котором алхимический резонанс выходит за пределы стабильности. Это может привести к самопроизвольной детонации зелья или образованию магически активных шлаков.",

"Особо опасным считается явление резонансного всплеска, при котором котёл начинает усиливать собственную магическую активность, вступая в обратную связь с содержимым. В таких случаях процесс становится неконтролируемым в течение нескольких секунд.",

"Поэтому в профессиональной практике используется строгая система температурного мониторинга, включающая визуальные, магические и тактильные индикаторы. Опытный зельевар способен определить перегрев по изменению плотности пара или искажению отражения поверхности жидкости.",

"Также важным фактором является расположение котла относительно источника тепла. Даже незначительный перекос или неравномерный нагрев может привести к локальной дестабилизации смеси и образованию опасных зон внутри зелья.",

"Таким образом, управление котлом и температурой представляет собой сложную систему взаимодействий между физикой, магией и человеческим контролем, где каждая деталь может стать решающей для исхода процесса.",

"В профессиональной алхимии считается, что мастерство зельевара определяется не количеством выученных рецептов, а способностью удерживать стабильность системы в условиях постоянной магической нестабильности."
      ]
    },
    {
      t: "Глава 4: Продвинутые антидоты и яды",
      p: [
        "Продвинутая токсикология зельеварения рассматривает яды не как отдельные вещества, а как сложные магико-алхимические системы, предназначенные для нарушения стабильности живого организма на нескольких уровнях одновременно: физическом, энергетическом и магическом.",

"В отличие от обычных зелий, яды обладают направленной деструктивной природой. Их структура часто включает не только активные компоненты, но и так называемый остаточный магический импульс, который продолжает действовать даже после разрушения химической основы вещества.",

"По этой причине стандартные методы нейтрализации оказываются недостаточными: устранение физической токсичности не гарантирует исчезновения магического воздействия, закреплённого в ауре или тканевой структуре жертвы.",

"Антидоты высокого уровня представляют собой многофазные составы, в которых каждый компонент активируется последовательно, создавая цепную реакцию очищения организма или магического поля.",

"Первичная фаза антидота обычно направлена на стабилизацию состояния жертвы. Она блокирует дальнейшее распространение яда, замедляя его взаимодействие с магическими каналами и биологическими системами.",

"Вторичная фаза отвечает за разрушение токсических соединений на уровне магического резонанса. Здесь происходит разрыв алхимических связей, удерживающих яд в активной форме.",

"Третья фаза — восстановительная — направлена на регенерацию повреждённых структур и возвращение магического баланса. Без неё даже успешно нейтрализованный яд может оставить долговременные последствия.",

"Безоар, несмотря на свою историческую репутацию универсального средства, в современной магической практике рассматривается лишь как экстренный стабилизатор. Он способен выиграть время, но не устраняет сложные магические токсины полностью.",

"Особенно опасны яды василиска, которые сочетают в себе физическую токсичность и крайне устойчивую магическую деструкцию. Их воздействие не ограничивается телесным уровнем и часто вызывает разрушение магических каналов даже при минимальном контакте.",

"Проклятые эликсиры представляют собой отдельную категорию, где яд связан с намерением заклинателя. В таких случаях токсичность усиливается эмоционально-магическим отпечатком, что делает стандартные антидоты частично неэффективными без дополнительного снятия проклятия.",

"Слёзы феникса в составе антидотов выполняют роль универсального стабилизатора. Их ключевое свойство заключается в восстановлении целостности магической структуры после разрушения, независимо от причины повреждения.",

"Рог двурога действует как структурный разрыватель. Он способен разрушать даже устойчивые магические соединения, включая те, что защищены естественной сопротивляемостью организма или наложенными чарами.",

"Драконья кровь, используемая в очищенной и стабилизированной форме, усиливает скорость реакции антидота, одновременно связывая остаточные магические токсины в инертные соединения, безопасные для организма.",

"Важным аспектом является время реакции. При большинстве сложных отравлений окно эффективного вмешательства составляет считанные минуты, после чего яд начинает интегрироваться в магическую структуру жертвы, становясь частью её ауры.",

"В таких случаях даже идеально приготовленный антидот может оказаться недостаточным без дополнительного магического вмешательства извне, включая ритуальное очищение или специализированные заклинания восстановления.",

"Опытные зельевары классифицируют яды не только по их эффекту, но и по глубине проникновения: поверхностные, системные и интеграционные. Последние являются наиболее опасными, так как становятся частью магического ядра существа.",

"Поверхностные яды воздействуют на физические процессы и относительно легко нейтрализуются стандартными средствами. Системные проникают глубже, затрагивая магические каналы и энергетические потоки.",

"Интеграционные яды изменяют саму структуру магической личности объекта, что делает их фактически частью существа до момента полного магического перезапуска или сложной реконструкции.",

"Поэтому работа с антидотами требует не только знания рецептов, но и понимания того, как яд взаимодействует с магической природой живого организма.",

"Ошибки в приготовлении антидотов столь же опасны, как и ошибки в создании ядов, поскольку неправильная нейтрализация может усилить токсичность или вызвать непредсказуемую реакцию между компонентами.",

"Таким образом, продвинутая токсикология является одной из наиболее сложных и ответственных областей зельеварения, где точность, скорость и понимание магической структуры вещества определяют границу между спасением и катастрофой.",

"Истинное мастерство в этой области заключается не только в умении создавать противоядия, но и в способности предсказывать поведение яда до его полного проявления."
      ]
    }
  ]
},
  {
  id: 4,
  t: "Тысяча магических трав",
  a: "Филлида Спора",
  cat: "Травология",
  ic: "fa-leaf",
  c: ["#2d5a27", "#162d13"],
  ch: [
    {
      t: "Глава 1: Классификация магической флоры",
      p: [
        "Мир магических растений представляет собой сложную, многослойную экосистему, в которой каждая форма жизни обладает уникальным уровнем взаимодействия с магией. Ошибка в классификации растения на этапе изучения почти всегда приводит к травмам, а в отдельных случаях — к полной утрате лаборатории.",

"Магическая флора делится не только по внешним признакам, но и по уровню внутренней активности. Пассивные растения, такие как большинство лечебных трав, требуют лишь базового ухода и правильных условий роста. Однако даже они могут реагировать на магическое присутствие непредсказуемым образом при нарушении баланса среды.",

"Полуразумные растения обладают примитивной формой восприятия. Они не думают в привычном смысле, но способны распознавать угрозу и реагировать на неё защитными механизмами. Бубонтюбер, например, способен изменять давление внутри своих структур, чтобы выбрасывать агрессивные соединения при контакте.",

"Хищные растения представляют наивысший уровень опасности среди флоры. Они не просто реагируют на окружение, а активно ищут источники магической или биологической энергии. Ядовитая тентакула способна удлинять свои отростки на значительное расстояние, адаптируясь к движению цели.",

"При работе с любой категорией магических растений необходимо учитывать фактор непредсказуемости. Даже пассивное растение может перейти в агрессивное состояние при изменении температуры, освещения или магического фона.",

"Правильный траволог всегда носит защитное снаряжение, включающее перчатки из драконьей кожи, очки с усиленной фильтрацией магических частиц и универсальные нейтрализующие растворы. Отсутствие хотя бы одного элемента увеличивает риск инцидента в несколько раз.",

"Теплица в магической академии — это не просто место выращивания растений, а зона контролируемого риска. Здесь каждое движение должно быть осознанным, а каждое решение — обоснованным, поскольку растения способны запоминать поведение человека и адаптироваться к нему."
      ]
    },
    {
      t: "Глава 2: Секреты пересадки Мандрагоры",
      p: [
        "Мандрагора (Mandragora officinarum) занимает особое место в магической травологии, являясь одновременно лекарственным ресурсом высшего класса и одним из наиболее опасных живых организмов, культивируемых в тепличных условиях. Её двойственная природа — ключ к её ценности и причине множества несчастных случаев среди неопытных травологов.",

"В биологическом смысле мандрагора представляет собой симбиотическую форму жизни, сочетающую растительные ткани с устойчивым магическим ядром. Именно это ядро отвечает за её защитные реакции, включая знаменитый крик, который является не просто звуковым явлением, а концентрированным выбросом сырой магической энергии.",

"На ранних стадиях развития мандрагора относительно безопасна, однако с каждым этапом роста её магическая активность экспоненциально увеличивается. Это означает, что даже кратковременное воздействие на взрослый экземпляр без защиты может привести к мгновенной потере сознания или летальному исходу.",

"Крик мандрагоры классифицируется как широкоспектральное магическое воздействие. Он поражает одновременно слуховую систему, магические каналы и нервную структуру организма. Поэтому стандартные шумоподавляющие средства абсолютно бесполезны без наложения специализированных заклинаний фильтрации магических волн.",

"Защитные наушники для работы с мандрагорой создаются из многослойных материалов, включающих зачарованную кожу, звукопоглощающие руны и стабилизирующие амулеты. Их правильная герметизация проверяется не менее трёх раз перед началом любых работ.",

"Пересадка мандрагоры требует строгой синхронизации с её биологическими циклами. Оптимальным считается период частичного магического покоя, когда корневая система снижает активность, а надземная часть не проявляет признаков агрессии.",

"Любое вмешательство вне этого окна считается грубым нарушением техники безопасности и практически гарантирует пробуждение растения в состоянии стресса, что усиливает мощность его защитного крика.",

"Подготовка почвы для мандрагоры является отдельной дисциплиной в травологии. Используемый субстрат должен обладать высокой магической нейтральностью, стабильной влажностью и минимальным содержанием реактивных минералов.",

"Добавление лунной росы в почву не только улучшает рост, но и снижает агрессивность растения, стабилизируя его магическое ядро. Однако избыток может привести к чрезмерной чувствительности и нестабильным реакциям.",

"При пересадке категорически запрещено использовать металлические инструменты, так как мандрагора способна вступать в резонанс с металлом, усиливая свою магическую реакцию и ускоряя пробуждение.",

"Вместо этого применяются инструменты из обработанного дерева или камня, не проводящие магические колебания. Даже незначительное нарушение этого правила может привести к неконтролируемому выбросу энергии.",

"Особую опасность представляет так называемый эффект 'ложного покоя', при котором растение визуально кажется неактивным, но на самом деле накапливает энергию перед защитной реакцией.",

"Опытные травологи всегда проводят предварительное сканирование состояния мандрагоры с помощью диагностических заклинаний, позволяющих определить уровень внутреннего напряжения и готовность к реакции.",

"Соседство мандрагоры с другими магическими растениями строго регламентировано. Некоторые виды, такие как прыгучие луковицы, способны вызывать резонансные конфликты корневых систем, приводящие к локальным выбросам магии.",

"В случае массового выращивания мандрагор необходимо соблюдать строгую дистанцию между экземплярами, чтобы избежать цепной реакции пробуждения, при которой крик одного растения активирует остальные.",

"Сбор зрелой мандрагоры осуществляется только в присутствии нескольких квалифицированных специалистов, каждый из которых отвечает за отдельный этап процесса: фиксацию, защиту, извлечение и стабилизацию.",

"Нарушение координации между участниками процесса считается одной из самых частых причин аварий в тепличных комплексах магических школ.",

"Таким образом, пересадка мандрагоры является не просто агротехнической процедурой, а сложной магико-биологической операцией, требующей абсолютной точности, дисциплины и понимания природы магической жизни.",

"Даже малейшая ошибка в обращении с этим растением превращает потенциально полезный экземпляр в источник неконтролируемой опасности, последствия которой могут ощущаться на уровне всей теплицы."
      ]
    },
    {
      t: "Глава 3: Сбор и применение редких соцветий",
      p: [
        "Редкие магические соцветия представляют собой один из наиболее сложных объектов изучения в травологии, поскольку их свойства зависят не только от вида растения, но и от внешних магических условий, в которых происходит их формирование. Даже незначительное изменение окружающего фона способно полностью изменить алхимический потенциал цветка.",

"Основная трудность работы с редкими соцветиями заключается в их нестабильной природе. Многие из них существуют в состоянии магического равновесия, которое легко нарушается при неправильном сборе, транспортировке или даже простом прикосновении без должной подготовки.",

"Цветы Моли (Moly) являются классическим примером защитной флоры высшего уровня. Их магическая структура активируется только в момент рассвета, когда переходная энергия между ночью и днём создаёт кратковременный резонанс в атмосфере.",

"Если сбор происходит слишком рано или слишком поздно, магическая матрица растения не активируется, и цветок теряет свои защитные свойства, превращаясь в обычный биологический объект без алхимической ценности.",

"Техника сбора цветов Моли требует полной синхронизации с природным циклом. Траволог должен учитывать не только время суток, но и сезонные колебания магического поля, которые могут сдвигать момент активации на несколько минут.",

"Бубонтюберы представляют собой особый класс полуагрессивных растений, чьи выделения обладают двойственной природой. При правильной обработке их гной используется в лечебных зельях, однако при нарушении технологии он становится крайне опасным химико-магическим агентом.",

"Процесс извлечения требует использования инертных контейнеров, устойчивых к коррозии магического происхождения. Любой контакт с органическими материалами может привести к непредсказуемой реакции и разрушению образца.",

"При сборе Бубонтюбера необходимо учитывать давление внутри его внутренних полостей. Переполнение или резкое изменение температуры может вызвать неконтролируемое выделение вещества, опасного для зрения и дыхательных путей.",

"Дьявольские силки (Devil’s Snare) относятся к категории интеллектуально-реактивных растений. Они не просто реагируют на движение, но анализируют поведенческие сигналы цели, усиливая захват при попытке сопротивления.",

"Их основная слабость заключается в реакции на свет и тепло. Однако важно понимать, что эффект должен быть достаточной интенсивности: слабое освещение лишь временно замедляет их активность, не устраняя угрозу полностью.",

"Заклинание 'Люмос Солем' является стандартным методом подавления активности Дьявольских силок, но требует точного направления и концентрации магической энергии. Ошибка в фокусировке может привести к частичной нейтрализации без освобождения жертвы.",

"Некоторые редкие соцветия обладают так называемым эффектом магического запоминания. Это означает, что растение может реагировать на конкретного человека, усиливая защитные механизмы при повторном контакте.",

"По этой причине опытные травологи никогда не работают с особо опасными видами в одиночку и регулярно меняют схемы взаимодействия с тепличными зонами.",

"Дополнительную сложность создаёт феномен временной нестабильности цветения, при котором растение может входить в фазу активности вне предсказуемого цикла под воздействием внешней магии.",

"В таких случаях стандартные методы сбора становятся неэффективными, и требуется использование стабилизирующих чар, замедляющих биологические процессы растения до безопасного уровня.",

"Таким образом, работа с редкими соцветиями требует не только знания ботаники, но и глубокого понимания магических циклов, энергетических резонансов и поведенческой адаптации растений.",

"Ошибки при сборе почти всегда необратимы: растение либо теряет свои свойства, либо становится значительно более опасным, чем до вмешательства.",

"Именно поэтому в травологии существует негласное правило: редкое растение никогда не прощает спешки."
      ]
    },
    {
      t: "Глава 4: Грибы и плесень — скрытая угроза",
      p: [
        "Магические грибы и плесень представляют собой одну из наиболее недооценённых, но при этом крайне опасных форм магической флоры. В отличие от обычных растений, они развиваются не только в физической среде, но и активно используют магические остаточные поля, что делает их практически незаметными на ранних стадиях заражения.",

"Основная сложность работы с магическими грибами заключается в их способности к скрытому распространению. Их споры могут находиться в состоянии пассивной активности длительное время, ожидая подходящих условий для мгновенного роста и колонизации пространства.",

"Некоторые виды, такие как пятнистая хмырь-поганка, обладают выраженным психомагическим эффектом. При попадании в зелья или воздух они вызывают длительные и устойчивые галлюцинации, которые воздействуют не только на зрение, но и на магическое восприятие реальности.",

"Особенность этих галлюцинаций заключается в том, что они могут сохраняться даже после прекращения контакта с источником заражения, поскольку гриб активирует изменения в восприятии на уровне магической памяти.",

"С другой стороны, существуют виды грибов с выраженными лечебными свойствами. Например, порошок сушёных дождевиков-пуховиков используется для мгновенной коагуляции крови и остановки сильных кровотечений в полевых условиях.",

"Однако даже полезные виды требуют строгого контроля, поскольку их споры легко мутируют при контакте с нестабильной магической средой, теряя свои лечебные свойства и приобретая агрессивные характеристики.",

"Наиболее опасным аспектом магических грибов является их взаимодействие с артефактами и инструментами. Споры способны проникать в древесину палочек, изменяя структуру магического проводника и вызывая искажения в заклинаниях.",

"В запущенных случаях это приводит к так называемому эффекту 'магической коррозии', при котором палочка начинает работать нестабильно, создавая непредсказуемые результаты даже при стандартных заклинаниях.",

"По этой причине после любого контакта с грибами требуется обязательная процедура полной магической стерилизации инструментов. Используются как физические методы очистки, так и заклинания антисептического спектра.",

"Отдельного внимания заслуживает феномен грибной синхронизации. В колониях некоторые виды способны обмениваться магическими импульсами, формируя единое биомагическое сознание, реагирующее на угрозу как единый организм.",

"Такие колонии практически невозможно уничтожить полностью без разрушения всей среды обитания, поскольку даже один оставшийся фрагмент мицелия способен восстановить всю структуру.",

"Магические плесени, в отличие от грибов, действуют медленнее, но более разрушительно. Они постепенно изменяют структуру материалов, превращая их в нестабильные магические субстанции.",

"Особенно опасны они в закрытых помещениях, где концентрация магии и влажности способствует ускоренному росту и усилению их разрушительных свойств.",

"Некоторые плесени способны даже влиять на заклинания, искажая формулы произнесения и приводя к частичной или полной деформации магического эффекта.",

"В условиях теплиц и лабораторий плесень считается одной из главных причин постепенной деградации оборудования и магических инструментов.",

"Таким образом, грибы и плесень требуют не менее строгого контроля, чем хищные растения или ядовитые существа, поскольку их опасность заключается не в мгновенной атаке, а в медленном, скрытом и системном воздействии.",

"Игнорирование протоколов безопасности при работе с грибами практически гарантирует постепенное разрушение как лабораторной среды, так и магической стабильности исследователя."
      ]
    },
	{
      t: "Список магических растений и ингредиентов",
  p: [ "Аконит",
    
	"Абиссинская смоковница", "Алихоция", "Асфодель", "Бадьян", "Белладонна", "Болиголов", "Бубонтюбер", "Валериана", "Визгопёрка", "Водоросли", "Гудящий нарцисс",
    "Дремоносное растение",
    "Дьявольские силки",
    "Жабросли",
    "Заунывники",
    "Зубастая герань",
    "Имбирь",
    "Калган-трава",
    "Китайская жующая капуста",
    "Клещевина",
    "Колючка обыкновенная",
    "Крапива",
    "Лаванда",
    "Лирный корень",
    "Ложечница",
    "Любисток",
    "Мандрагора",
    "Манжетка",
    "Маргаритка",
    "Мимбулус мимблетония",
    "Моли",
    "Мята",
    "Омела",
    "Паффопод",
    "Полынь",
    "Прыгающая поганка",
    "Прыгучая луковица",
    "Роза",
    "Рута",
    "Спорыш",
    "Стандартный ингредиент",
    "Сумасшедшая ягода",
    "Трепетливые кустики",
    "Цапень",
    "Цикута",
    "Чемерица",
    "Чихотник",
    "Ядовитая тентакула",

    "Магические деревья:",
    "Волшебная рябина",
    "Горячительное дерево",
    "Гремучая ива",
    "Змеиное дерево",

    "Древесина для палочек:",
    "Акация",
    "Английский дуб",
    "Берёза",
    "Боярышник",
    "Бузина",
    "Бук",
    "Виноград",
    "Вишня",
    "Вяз",
    "Граб",
    "Грецкий орех",
    "Груша",
    "Ель",
    "Ива",
    "Каштан",
    "Кедр",
    "Кизил",
    "Кипарис",
    "Клён",
    "Красное дерево",
    "Красный дуб",
    "Лавр",
    "Липа",
    "Лиственница",
    "Ольха",
    "Орешник",
    "Осина",
    "Остролист",
    "Пихта",
    "Розовое дерево",
    "Рябина",
    "Саксаул",
    "Сикомор",
    "Смоковница",
    "Сосна",
    "Терновник",
    "Тис",
    "Тополь",
    "Чёрное (эбеновое) дерево",
    "Чёрный орешник",
    "Яблоня",
    "Ясень",

    "Ингредиенты (растительного происхождения): Аконит, Асфодель, Бадьян, Белладонна, Болиголов, Валериана, Водоросли, Волшебная рябина, Горячительное дерево, Гной бубонтюбера, Жабросли, Заунывник, Имбирь, Крапива, Мандрагора, Моли, Омела, Полынь, Цикута",

    "Ингредиенты (животного происхождения): Бородавки пурпурной жабы, Волосы кельпи, Выделения бундимунов, Глаза жуков, Глаза тритона, Глаза угря, Драконий навоз, Златоглазки, Когти грифона, Кровь дракона, Кровь единорога, Перо феникса, Печень дракона, Рог единорога, Сердце дракона, Слёзы феникса, Шкура бумсланга, Яд василиска",

    "Ингредиенты (минеральные): Безоар, Лунный камень, Опал, Рубин",

    "Ингредиенты (прочие): Мёд, Медовая вода, Вода из реки Лета, Утренняя роса, Лунная роса, Стандартный ингредиент"
      ]
	}
  ]
},
  {
  id: 5,
  t: "Теория магии",
  a: "Адальберt Уоффлинг",
  cat: "Теория",
  ic: "fa-wand-magic-sparkles",
  c: ["#4b0082", "#1a0033"],
  ch: [
    {
      t: "Глава 1: Первичный постулат Трансмутации Энергии",
      p: [
        "Магия не является самостоятельной субстанцией, возникающей из вакуума. Это фундаментальная энергия мироздания, которую волшебник черпает из своего внутреннего источника — Магического Ядра. Согласно Первому закону Уоффлинга, любое заклинание — это акт контролируемого преобразования потенциальной энергии духа в кинетическую энергию реальности.",
        "Процесс колдовства начинается с 'Точки Сингулярности Намерения'. В этот миг воля мага должна быть абсолютно доминирующей над физическими законами. Если вы пытаетесь зажечь огонь (Люмос), ваше сознание должно не просто желать света, оно должно диктовать молекулам воздуха необходимость свечения. Любое колебание в ментальном потоке приводит к рассеиванию энергии, что на практике означает либо полное фиаско, либо опасный магический выброс.",
        "Важно понимать роль проводника. Волшебная палочка не производит магию, она лишь фокусирует её. Представьте палочку как плотину: она сдерживает хаотичный поток силы и выпускает его через узкое русло в виде четко структурированного заклинания. Без палочки магия становится дикой, непредсказуемой и разрушительной для самого носителя."
      ]
    },
    {
      t: "Глава 2: Триединство Заклинания: Формула, Жест и Воля",
      p: [
        "Каждое классическое заклинание опирается на три неразрывных столпа. Первый — Вербальная формула. Звуковая вибрация латыни или древних наречий служит ключом, настраивающим магическое ядро на определенную частоту. Ошибка в произношении (вспомните классическое 'Левио́са' против 'Левиоса́') меняет частоту резонанса, что может привести к поджогу вместо левитации.",
        "Второй столп — Кинетическая геометрия или Жест. Движение палочки — это чертеж в пространстве, по которому течет магический ток. Малейшее отклонение угла наклона кисти разрывает структуру заклинания. Профессиональные дуэлянты оттачивают жесты до автоматизма, чтобы магический контур замыкался мгновенно.",
        "Третий и самый сложный столп — Намерение (Ментальный Вектор). Это способность мага удерживать в уме конечный результат в момент произнесения формулы. При изучении Непростительных заклятий этот столп становится определяющим: недостаточно просто знать слова, нужно обладать сокрушительной волей к доминированию или уничтожению, иначе заклятие останется лишь слабой вспышкой."
      ]
    },
    {
      t: "Глава 3: Закон Гэмпа и Метафизические Ограничения",
      p: [
        "Мироздание имеет встроенные предохранители, известные как Пять принципиальных исключений из Закона Гэмпа об элементарных трансфигурациях. Эти барьеры невозможно преодолеть даже самой могущественной палочкой. Первое — Еда. Магия может изменить форму съедобного, увеличить объем уже имеющегося хлеба, но она не способна создать питательные вещества из воздуха. Иллюзорный пирог не насытит кровь глюкозой, и маг умрет от голода, несмотря на стол, полный 'еды'.",
        "Второе исключение — Жизнь и Смерть. Никакое заклинание не вернет душу в тело, если серебряная нить была разорвана. Теневые копии, возвращаемые Камнем Воскрешения или Приори Инкантатем, — это лишь эхо, магические слепки памяти, лишенные искры живого сознания.",
        "Третье — Драгоценные металлы. Магическое золото 'Лепреконов' — это классический пример нарушения стабильности. Оно рассыпается в пыль спустя часы, так как его молекулярная структура поддерживается только волей колдуна, а не естественным природным балансом. Истинное превращение свинца в золото доступно только Высшей Алхимии через философский камень."
      ]
    },
    {
      t: "Глава 4: Поля Магического Искажения и Электроника",
      p: [
        "Магия и технология маглов — это две конфликтующие парадигмы управления миром. В местах с высокой концентрацией магических полей, таких как Хогвартс, Министерство магии или Годрикова Впадина, электромагнитные колебания подавляются магическим фоном. Электричество в приборах маглов 'замещается' магическим током, что приводит к мгновенному выходу из строя компьютеров, радаров и мобильных устройств.",
        "Для мага важно понимать феномен 'Магического Эха'. Если в помещении было совершено мощное темное проклятие, стены здания впитывают эту энергию. Со временем это создает поле искажения, влияющее на психику живущих там людей. Очистка таких мест требует многолетней работы разрушителей заклятий и использования мощных нейтрализующих солей.",
        "Резонанс между двумя магами — еще одна опасная тема. Если два заклинания сталкиваются в воздухе, они могут образовать 'Узел Силы'. В этот момент исход борьбы решает не знание слов, а чистая мощь магического ядра и способность мага вливать свою энергию в этот узел, пока противник не истощится."
      ]
    },
	{
      t: "Глава 5: Философия Высшего Чародейства",
      p: [
        "В конечном итоге, Теория магии учит нас, что единственным реальным ограничением является сам волшебник. Страх, неуверенность и нежелание принимать ответственность за свои действия — вот те оковы, которые делают магию слабой. Истинный мастер теории понимает: мир вокруг — это пластичная глина, а магия — это воля, придающая этой глине форму.",
        "Мы изучаем эти правила не для того, чтобы им следовать, а для того, чтобы понимать, на каком фундаменте стоит реальность. Только познав законы Уоффлинга до конца, маг может позволить себе выйти за их рамки, создавая новые заклинания и открывая горизонты, недоступные простым исполнителям формул.",
        "Помните слова великих: 'Магия — это риск. Магия — это бремя. Но прежде всего, магия — это свобода'. Тот, кто постиг теорию, больше никогда не будет рабом обстоятельств."
      ]
    }
  ]
},
  {
  id: 6,
  t: "Древние руны",
  a: "Батшеда Бабблинг",
  cat: "Руны",
  ic: "fa-scroll",
  c: ["#5d4037", "#2e1b15"],
  ch: [
    {
      t: "Глава 1: Этимология и Магия Знака",
      p: [
        "Изучение Древних рун — это не просто лингвистическое упражнение, это работа с первоосновами магической фиксации. В отличие от заклинаний, которые рассеиваются после произнесения, руна — это застывшее заклятие, способное черпать энергию из окружающего мира веками.",
        "Каждая черта руны должна быть выверена до миллиметра. Ошибка в одну наклонную линию может превратить защитный амулет в энергетическую ловушку, которая начнет поглощать жизненные силы владельца вместо того, чтобы оберегать его. Помните: руна не прощает небрежности.",
        "Древний Футарк — это база, на которой строится вся европейская рунология. Каждая руна имеет три аспекта: её фонетическое значение, её символическую связь и её магический потенциал. Только поняв все три, маг может переходить к начертанию."
      ]
    },
    {
      t: "Глава 2: Руны Созидания и Разрушения",
      p: [
        "Рассмотрим руну Турисаз (Thurisaz). Она воплощает силу молота и шипа. В защитных связках она служит непреодолимым барьером, но если её начертать в зеркальном отражении без должной стабилизации, она обернет свою мощь против самого резчика, вызывая неконтролируемые вспышки агрессивной магии.",
        "Руна Феху (Fehu), напротив, отвечает за поток и накопление. Она используется в создании бездонных кошельков и магических хранилищ. Однако опытный рунолог знает: избыток Феху без уравновешивающих рун контроля (таких как Иса) приводит к 'магическому ожирению' предмета, из-за чего он может буквально лопнуть от избыточного давления энергии.",
        "Особое внимание уделяется руне Эйваз (Eihwaz) — символу связи между мирами. Это мост между жизнью и смертью. Ошибки в её трактовке в древности приводили к тому, что целые поселения оказывались заперты в межпространственных петлях."
      ]
    },
    {
      t: "Глава 3: Рунические связки и Гальдраставы",
      p: [
        "Сложная магия требует объединения одиночных знаков в связки (биндуруны). Здесь вступает в силу закон синергии: руны не просто суммируют свои эффекты, они создают новую магическую сущность.",
        "При создании Гальдраставов — исландских магических знаков — важно соблюдать порядок начертания. Начинать нужно строго от центра к периферии, заклиная каждую линию на древнескандинавском наречии. Если прервать контакт инструмента с поверхностью до завершения круга, связь будет прервана, и артефакт станет нестабильным.",
        "Самый известный пример опасной связки — 'Шлем Ужаса'. Правильно созданный, он делает мага невидимым для вражеских чар, но его длительное ношение вызывает необратимые изменения в психике, делая сознание холодным и безжалостным, подобно древним северным богам."
      ]
    },
    {
      t: "Глава 4: Материаловедение: Камень, Дерево и Кость",
      p: [
        "Материал, на котором высекается руна, определяет её долговечность и тип проводимой энергии. Гранит и базальт лучше всего подходят для стационарных защитных барьеров (например, тех, что охраняют границы Хогвартса). Камень инертен, он долго накапливает заряд, но держит его тысячелетиями.",
        "Дерево — ясень, дуб или тис — идеально для временных амулетов и палочек. Руны на дереве 'дышат' вместе с магом, реагируя на его эмоциональное состояние. Однако деревянные руны подвержены 'магическому выгоранию', если через них пропустить слишком мощный поток силы.",
        "Кость (особенно кость магических существ, таких как дракон или сфинкс) используется в некромантии и высшей защите. Кость сохраняет остаточную магию существа, что усиливает эффект рун в десятки раз, но работа с таким материалом требует специального разрешения Министерства магии."
      ]
    },
    {
      t: "Глава 5: Чтение Рунических Свитков",
      p: [
        "Перевод древних текстов — это детективная работа. Часто древние маги использовали шифры, меняя руны местами или вписывая одни знаки внутрь других. Это делалось для того, чтобы непосвященный, прочитав свиток вслух, не активировал разрушительное заклятие.",
        "При переводе всегда используйте 'Словарь сложной магии' и никогда не прикасайтесь к рунам голыми руками, если свитку более трехсот лет. Старые руны имеют свойство 'впитываться' в кожу, переписывая линии судьбы человека на ладони.",
        "Завершая этот вводный курс, Батшеда Бабблинг напоминает: магия рун — это магия тишины. В отличие от шумных дуэлей на палочках, руны работают молча, меняя реальность в тени. Будьте достойны этого знания."
      ]
    }
    ]
  },
  {
  id: 7,
  t: "Фантастические твари",
  a: "Ньют Саламандер",
  cat: "Магозоология",
  ic: "fa-paw",
  c: ["#8b4513", "#3e2723"],
  ch: [
    {
      t: "Глава 1: Введение и Классификация М.М.",
      p: [
        "Магозоология — это не просто изучение животных, это понимание магической экосистемы нашего мира. Многие волшебники до сих пор боятся того, чего не понимают, но моя цель — показать, что даже самый опасный дракон заслуживает уважения и защиты.",
        "Министерство магии разработало классификацию опасности существ: от 'X' (скучные, домашние) до 'XXXXX' (известные убийцы волшебников, не поддающиеся дрессировке).",
        "Помните: знание — это ваш лучший щит. Если вы столкнулись с существом класса 'XXXX', не пытайтесь его приручить, если у вас нет с собой запаса вяленого мяса или, в крайнем случае, очень быстрого метлы."
      ]
    },
    {
      t: "Глава 2: Нюхлер (Niffler) — Маленький воришка",
      p: [
        "Классификация М.М.: XXX. Нюхлер — это британское существо, пушистое, черное и длинноносое. У них есть одна непреодолимая страсть: всё, что блестит. Золото, монеты, пряжки ремней или даже ваши зубы, если они из золота — всё окажется в бездонном брюшном кармане этого зверька.",
        "Несмотря на их милый вид, Нюхлеры могут нанести серьезный ущерб вашему дому, пытаясь достать блестящую ложку из-под паркета. Они живут в норах на глубине до двадцати футов и размножаются с поразительной скоростью (от 6 до 8 детенышей в помете).",
        "Совет от автора: если вы держите Нюхлера, никогда не носите украшения в его присутствии. Если золото уже пропало — просто переверните зверька вверх ногами и пощекочите ему живот. Вы будете удивлены объемом его 'добычи'."
      ]
    },
    {
      t: "Глава 3: Пикирующий Злыдень и скрытые угрозы",
      p: [
        "Пикирующий Злыдень (Swooping Evil) — одно из самых недооцененных существ. В спокойном состоянии он выглядит как небольшой кокон, но при угрозе расправляет свои яркие, шипастые крылья. Его яд, если его правильно разбавить, является мощнейшим средством для стирания плохих воспоминаний.",
        "Однако будьте осторожны: Злыдень питается мозгами (в буквальном смысле), если его не контролировать. Это существо идеально подходит для защиты дома, но только если вы уверены, что он признает в вас хозяина.",
        "Также стоит упомянуть демимаску — существо, способное становиться невидимым и предсказывать ближайшее будущее. Их шерсть используется для изготовления мантий-невидимок, что делает их постоянной мишенью для браконьеров."
      ]
    },
    {
      t: "Глава 4: Птица-гром и магия климата",
      p: [
        "Птица-гром (Thunderbird) — величественное существо родом из Аризоны, США. Она близкий родственник феникса и обладает способностью создавать штормы во время полета. Цвет её крыльев меняется в зависимости от погоды: от ослепительно-белого до глубокого индиго.",
        "Эти птицы крайне чувствительны к опасности. Если Птица-гром начинает беспокойно хлопать крыльями — ждите беды или масштабного магического вмешательства. В 1926 году именно Птица-гром помогла спасти целый город от разоблачения магического сообщества.",
        "Их перья — мощнейший, но крайне капризный сердцевинный материал для волшебных палочек. Палочка с пером Птицы-гром может самопроизвольно выпускать заклинания, если почувствует угрозу владельцу."
      ]
    },
    {
      t: "Глава 5: Кодекс магозоолога",
      p: [
        "В завершение хочу сказать: мы не хозяева этих существ, мы их хранители. Каждый раз, когда вы встречаете фантастическую тварь, помните о трех принципах: Наблюдение, Понимание и Сосуществование.",
        "Мир магии стал бы серым и безжизненным без песен фениксов, ворчания нюхлеров или танцев лунных тельцов. Относитесь к ним с добротой, и они ответят вам тем же. Или, по крайней мере, не попытаются вас съесть.",
        "С уважением к природе, Ньют Саламандер."
      ]
    }
  ]
},
  {
  id: 8,
  t: "Тёмные искусства",
  a: "Морган Лэ Фей",
  cat: "Запретная секция",
  ic: "fa-skull",
  c: ["#310d0d", "#000000"],
  ch: [
    {
      t: "Глава 1: Природа Теневого Плетения",
      p: [
        "Тёмная магия не является отдельной школой заклинаний — это фундаментальный процесс взаимодействия мага с первичной энтропией мироздания. В отличие от созидательной магии, которая упорядочивает материю и энергию, Тёмные Искусства опираются на разрушение структуры как на источник силы.",

"Согласно древним трактатам, Тьма представляет собой не субстанцию, а состояние реальности, при котором вероятность хаоса становится доминирующей над вероятностью порядка. Маг, обращающийся к Теневому Плетению, фактически изменяет локальную статистику бытия.",

"Каждое заклинание Тьмы — это акт локального разрушения реальности, в котором маг временно разрывает естественные законы причинности. В момент произнесения проклятия пространство как бы 'забывает' о стабильности, позволяя энергии хаоса проникнуть в физический мир.",

"Этот процесс сопровождается феноменом, известным как 'разгерметизация реальности', при котором границы между возможным и невозможным временно стираются, создавая условия для формирования аномальных магических эффектов.",

"Первичный принцип Теневого Плетения заключается в том, что любая сила должна быть оплачена. Энергия Тьмы не создаётся — она извлекается из уже существующих структур: эмоций, памяти, боли или самой ткани магического ядра.",

"Чем выше интенсивность заклинания, тем глубже происходит структурное заимствование энергии, вплоть до уровня личностной идентичности мага, что приводит к частичной утрате отдельных аспектов психики.",

"Чем глубже маг погружается в Теневое Плетение, тем сильнее меняется его магическое ядро. Оно становится менее стабильным, более резонансным и начинает самостоятельно притягивать разрушительные потоки энергии даже вне акта колдовства.",

"В некоторых случаях ядро начинает функционировать автономно, формируя так называемый 'паразитический резонанс', при котором магическая энергия генерируется даже без сознательного контроля, но за счёт постоянной деградации носителя.",

"Существует явление, известное как 'Магическое затемнение ауры'. Оно проявляется у практиков Тёмных Искусств как постепенная утрата эмоциональной спектральности: радость, сострадание и страх становятся менее выраженными, уступая место холодной рациональности и доминирующей воле.",

"На поздних стадиях затемнения наблюдается полное подавление эмоциональной обратной связи, при котором маг перестаёт воспринимать моральные категории как значимые, заменяя их чисто функциональной логикой эффективности.",

"В отличие от светлой магии, которая требует гармонии с природными законами, Тьма требует их частичного отрицания. Маг должен не просто игнорировать ограничения реальности — он должен активно противостоять им, вызывая локальные нарушения стабильности.",

"Это противостояние создаёт эффект 'контр-резонанса', при котором магическая энергия начинает конфликтовать с базовой структурой мира, усиливая разрушительный потенциал заклинаний.",

"Каждое использование Теневого Плетения оставляет след в пространстве. Эти следы называются 'шрамами реальности' и могут сохраняться десятилетиями, влияя на поведение живых существ и структуру окружающей среды.",

"Со временем такие шрамы могут накапливаться и образовывать устойчивые зоны искажённой реальности, где привычные законы магии перестают действовать предсказуемо.",

"Сильные концентрации тёмной магии способны создавать автономные зоны искажения, где законы физики и магии начинают конфликтовать. В таких местах время может замедляться, а магические эффекты приобретать непредсказуемую форму.",

"Иногда внутри таких зон формируются самоподдерживающиеся петли энергии, в которых одно заклинание бесконечно подпитывает другое, создавая локальные магические коллапсы.",

"Важно понимать, что Тёмные Искусства не являются злом в философском смысле. Они являются инструментом, отражающим намерение пользователя. Однако высокая цена использования делает их опасными даже для опытных магов.",

"В академической магической теории это называется 'этической нейтральностью разрушения', где сама сила не имеет морали, но её последствия всегда выходят за пределы контроля пользователя.",

"Первый и самый важный урок Теневого Плетения заключается в осознании собственной ответственности. Маг, вступающий на этот путь, должен понимать, что он не управляет Тьмой полностью — он вступает с ней в договор.",

"Этот договор основан на принципе асимметричного обмена, где маг всегда получает силу немедленно, а последствия распределяются во времени и пространстве непредсказуемым образом.",

"Этот договор всегда асимметричен. Тьма предоставляет силу, но никогда не объясняет последствия полностью. Истинное понимание приходит только после того, как цена уже уплачена.",

"Именно поэтому древние школы Тёмных Искусств всегда начинали обучение не с заклинаний, а с философии страха и контроля. Без внутренней дисциплины даже слабое проклятие может стать точкой необратимого падения.",

"Финальный принцип гласит: тот, кто понимает Тьму, перестаёт её бояться, но тот, кто перестаёт её бояться полностью, рано или поздно становится её частью."
      ]
    },
    {
      t: "Глава 2: Психомагия боли и подчинения",
      p: [
        "Психомагия боли и подчинения является одной из наиболее опасных дисциплин Тёмных Искусств, поскольку она опирается не на внешние силы, а на прямое воздействие на сознание живого существа. В отличие от разрушительных заклятий, здесь целью становится не уничтожение материи, а перестройка воли.",

"Согласно древним исследованиям, разум существа можно рассматривать как многослойную структуру, состоящую из импульсов, памяти, эмоций и базовых инстинктов. Психомагия воздействует сразу на все эти уровни, нарушая их естественную иерархию.",

"Заклятие подчинения, известное как 'Империус', работает через полное подавление автономной воли цели. В момент наложения заклинания сознание жертвы входит в состояние 'когнитивного растворения', при котором граница между собственными желаниями и внешним приказом исчезает.",

"Однако наиболее опасным аспектом является то, что маг вынужден временно синхронизировать своё магическое ядро с ядром жертвы. Это создаёт явление, называемое 'резонансом контроля', при котором часть мыслительных процессов мага начинает отражаться в сознании цели.",

"Чем дольше удерживается контроль, тем сильнее размывается граница личности. В некоторых случаях наблюдается эффект 'обратной проекции', когда элементы сознания жертвы начинают проникать в мышление заклинателя.",

"Проклятие боли, известное как 'Круциатус', относится к иной категории воздействия. Оно не изменяет волю, а перегружает нервно-магическую систему цели до состояния предельной сенсорной перегрузки.",

"Боль в данном случае рассматривается не как физическое ощущение, а как магически усиленный сигнал, многократно превышающий естественные пределы восприятия. Это создаёт состояние, называемое 'сенсорным коллапсом'.",

"Для успешного применения подобных заклятий требуется не просто знание формулы, но и абсолютная ментальная концентрация. Любая эмоциональная нестабильность мага приводит к искажению заклинания и частичному отражению эффекта на самого заклинателя.",

"Древние тексты подчёркивают, что ключевым компонентом является не слово и не жест, а состояние сознания, известное как 'безэмпатийная фиксация цели'. Без него психомагия становится нестабильной и непредсказуемой.",

"Существует также явление, называемое 'магическая отдача психики'. При длительном использовании заклятий контроля маг начинает терять способность различать собственные желания и навязанные импульсы.",

"Это приводит к постепенной деградации личностной структуры, при которой границы между 'я' и 'другими' становятся размытыми, создавая риск когнитивного распада.",

"Особое внимание уделяется феномену коллективной психомагии, когда несколько магов одновременно воздействуют на одну цель. В таких случаях возникает эффект 'усиленного подчинения', при котором воля цели полностью вытесняется внешними потоками сознания.",

"Однако при этом возрастает риск образования 'петли влияния', когда маги начинают непроизвольно влиять друг на друга через общую цель, что может привести к хаотическим ментальным конфликтам.",

"Психомагия также тесно связана с понятием 'ментальной инерции'. Даже после прекращения заклятия след воздействия может сохраняться в виде остаточных командных паттернов, влияющих на поведение цели.",

"Именно поэтому опытные маги считают психомагию не инструментом контроля, а инструментом ответственности, поскольку каждое вмешательство в разум оставляет долгосрочные следы, которые невозможно полностью удалить.",

"Финальный принцип этой дисциплины формулируется следующим образом: тот, кто однажды коснулся чужой воли, уже не может быть полностью свободен от последствий этого прикосновения.",

"Именно поэтому Психомагия боли и подчинения считается не просто разделом Тёмных Искусств, а испытанием границ собственной личности и её устойчивости к разрушению."
]
    },
    {
      t: "Глава 3: Некромантия и зов праха",
      p: [
        "Некромантия представляет собой одну из древнейших и наиболее спорных дисциплин Тёмных Искусств, поскольку она касается границы между жизнью и смертью — границы, которую природа стремится сохранять неизменной. В основе некромантии лежит принцип вмешательства в посмертную структуру магического следа существа.",

"Согласно теории посмертного резонанса, после смерти физического тела магическая структура личности не исчезает мгновенно, а постепенно распадается, оставляя так называемый 'остаточный контур сознания'. Именно этот контур и является основным объектом воздействия некроманта.",

"Поднятие мёртвых, известных как 'инферналы', не является истинным воскрешением. Это процесс магической реконфигурации материи, при котором тело приводится в движение за счёт внешнего источника энергии, но без участия подлинного сознания.",

"Такие существа функционируют в состоянии, которое древние тексты называют 'пустой активностью'. Это означает, что тело сохраняет форму и способность к действию, но полностью лишено автономной воли и эмоциональной структуры.",

"Главный принцип некромантии формулируется как 'отрицание необратимости'. Маг временно игнорирует фундаментальный закон мироздания о завершённости жизненного цикла, создавая искусственную имитацию продолжения существования.",

"Однако каждое такое вмешательство вызывает феномен 'разрыва посмертного баланса'. В этом состоянии граница между миром живых и мёртвых становится нестабильной, что может привести к непредсказуемым магическим утечкам.",

"Особо опасным аспектом является возможность формирования так называемых 'аномальных оболочек' — структур, в которых неконтролируемая магическая энергия заполняет пустые участки сознания, создавая искажённые формы поведения.",

"Древние источники предупреждают, что некромантия никогда не взаимодействует только с телом. Любое воздействие затрагивает и окружающее пространство, создавая эффект 'памяти смерти' — устойчивого магического следа в месте воскрешения.",

"Эта память может проявляться в виде холодных зон, искажённого восприятия времени или спонтанных магических всплесков, возникающих без видимой причины.",

"Существует также феномен 'эхо личности', при котором остаточные фрагменты сознания умершего могут непредсказуемо проявляться внутри поднятого тела, создавая кратковременные вспышки поведения, не соответствующего текущему состоянию существа.",

"Некроманты высокого уровня иногда пытаются использовать эти фрагменты для частичного восстановления когнитивных функций, однако такие попытки крайне нестабильны и часто приводят к разрушению всей структуры подчинения.",

"Отдельно выделяется категория так называемой 'глубинной некромантии', где маг взаимодействует не с телом, а с чистым посмертным следом сознания. Это направление считается наиболее опасным, поскольку границы между личностью и её следом становятся практически неразличимыми.",

"В таких практиках возникает риск 'слияния контуров', при котором маг может частично перенять характеристики умершего субъекта, теряя собственную идентичность.",

"Некромантия также тесно связана с явлением 'магического истощения пространства'. Частое использование подобных практик делает локальные области мира менее устойчивыми к процессам жизни, создавая зоны медленного угасания.",

"Именно поэтому многие магические школы рассматривают некромантию не как способ возвращения жизни, а как форму диалога с остатками существования, в котором всегда доминирует тишина.",

"Финальный принцип некромантии гласит: 'всё, что было возвращено из смерти, уже не принадлежит полностью ни миру живых, ни миру мёртвых'.",

"Именно это промежуточное состояние делает некромантию одной из самых опасных и непредсказуемых дисциплин Тёмных Искусств."
  ]
    }
  ]
},
{
  id: 9,
  t: "Тайны алхимии",
  a: "Николас Фламель",
  cat: "Алхимия",
  ic: "fa-gem",
  c: ["#b22222", "#4b0000"],
  ch: [
    {
      t: "Глава 1: Великое Делание (Magnum Opus)",
      p: [
        "Алхимия — это не просто наука о превращении веществ, но глубокая философская система, направленная на постижение фундаментальных законов мироздания. Великое Делание (Magnum Opus) рассматривается как путь, на котором алхимик преобразует не только материю, но и самого себя.",

    "Согласно древним трактатам, весь процесс Великого Делания является отражением космического цикла: рождение, разрушение, очищение и возрождение. Алхимик, вступающий на этот путь, должен осознавать, что каждое изменение в тигле неизбежно отражается на его собственном сознании.",

    "Первая стадия — Нигредо (чернение) — символизирует распад. На этом этапе вещество разрушается до первичной хаотической массы. Внутренне это соответствует разрушению старых убеждений, страхов и иллюзий мага. Без этой стадии невозможно дальнейшее развитие.",

    "Нигредо часто сопровождается состояниями внутреннего кризиса. Алхимик сталкивается с собственными слабостями, страхами и скрытыми желаниями. Это необходимый этап, поскольку только через полное разрушение старой структуры возможно создание новой.",

    "Вторая стадия — Альбедо (отбеливание) — представляет собой очищение. После хаоса Нигредо материя начинает упорядочиваться, освобождаясь от примесей. В духовном смысле это этап ясности, самоконтроля и восстановления внутреннего равновесия.",

    "На стадии Альбедо алхимик учится управлять своими эмоциями и магической энергией. Его сознание становится более стабильным, а восприятие — более точным. Однако любое отклонение от чистоты намерения может привести к разрушению всей работы.",

    "Третья стадия — Цитринитас (желтение) — является переходным этапом, на котором проявляется истинное понимание. Это момент, когда алхимик начинает видеть взаимосвязи между всеми элементами мира и осознает природу энергии, с которой он работает.",

    "Цитринитас часто описывается как пробуждение внутреннего света. Это состояние, в котором магическое ядро начинает функционировать с максимальной эффективностью, а воля мага становится устойчивой и направленной.",

    "Финальная стадия — Рубедо (краснение) — символизирует завершение Великого Делания. Именно на этом этапе происходит создание философского камня. Материя достигает состояния абсолютной гармонии, а энергия становится стабильной и бесконечной.",

    "Рубедо — это не просто физическое превращение, а состояние полной интеграции всех аспектов бытия. Алхимик, достигший этой стадии, обретает способность к истинной трансмутации, как внешней, так и внутренней.",

    "Важно понимать, что философский камень является не столько объектом, сколько результатом процесса. Он представляет собой материализацию совершенного баланса между энергией и формой, волей и материей.",

    "Многие начинающие алхимики совершают ошибку, воспринимая Великое Делание исключительно как способ получения золота. Однако золото в алхимии является лишь символом — символом совершенства и неизменности.",

    "Истинная цель алхимии — это достижение состояния, в котором маг способен осознанно взаимодействовать с фундаментальными структурами реальности. Это требует не только знаний, но и глубокой внутренней дисциплины.",

    "Эликсир Жизни, создаваемый на основе философского камня, является побочным продуктом Великого Делания. Он способен продлевать жизнь, но не освобождает от ответственности за её содержание.",

    "Бессмертие, даруемое алхимией, следует рассматривать как испытание. Оно требует от мага устойчивости, мудрости и способности принимать неизбежные изменения мира.",

    "Алхимик, достигший Великого Делания, становится частью более широкой структуры реальности. Его действия начинают оказывать влияние не только на материю, но и на энергетические потоки мира.",

    "Таким образом, Magnum Opus — это путь трансформации, на котором материя и сознание развиваются параллельно. Невозможно завершить одно без завершения другого.",

    "Именно поэтому древние мастера утверждали: тот, кто ищет камень ради богатства, никогда не найдет его. Лишь тот, кто стремится к пониманию, способен завершить Великое Делание."
      ]
    },
    {
      t: "Глава 2: Стадии Нигредо и Альбедо",
  p: [
    "Стадии Нигредо и Альбедо являются фундаментом всего Великого Делания. Без их полного и осознанного прохождения невозможно достичь ни Цитринитас, ни Рубедо. Именно здесь происходит основная трансформация как материи, так и сознания алхимика.",

    "Нигредо, или 'чернение', символизирует распад и возвращение к первичной материи. В реторте вещество темнеет, теряет структуру и начинает разлагаться. Этот процесс отражает универсальный закон: всё сложное должно быть разрушено, чтобы быть пересобранным заново.",

    "На физическом уровне Нигредо сопровождается процессами гниения, окисления и распада. Вещество становится нестабильным, выделяет тёмные испарения и может вести себя непредсказуемо. Это самая опасная стадия для неопытного алхимика.",

    "На внутреннем уровне Нигредо проявляется как психологический кризис. Алхимик сталкивается с подавленными эмоциями, страхами и внутренними противоречиями. Этот этап требует полной честности перед самим собой.",

    "Существует понятие 'алхимической тени' — совокупности всех подавленных аспектов личности. В стадии Нигредо эта тень выходит на поверхность, и игнорировать её невозможно. Попытка избежать этого этапа приводит к искажению результата.",

    "Важно отметить, что Нигредо нельзя ускорить. Любое вмешательство в естественный процесс распада приводит к формированию нестабильной материи, которая впоследствии разрушает весь алхимический цикл.",

    "После полного распада начинается переход к Альбедо — стадии очищения. Это момент, когда из хаоса начинает формироваться порядок. Вещество постепенно светлеет, а его структура стабилизируется.",

    "Альбедо символизирует очищение от примесей — как материальных, так и духовных. В реторте это проявляется в виде прозрачности, светлого пара и мягкого свечения состава.",

    "Внутренне Альбедо соответствует состоянию ясности. Алхимик обретает способность наблюдать без искажений, его мышление становится точным и структурированным.",

    "На этом этапе крайне важно сохранять чистоту намерения. Даже незначительное эмоциональное искажение может повлиять на структуру вещества, вызывая так называемую 'алхимическую мутацию'.",

    "Альбедо требует дисциплины и терпения. В отличие от Нигредо, где доминирует хаос, здесь ключевым становится контроль. Алхимик должен поддерживать стабильные условия, не допуская резких изменений температуры или энергии.",

    "Существует также феномен 'ложного Альбедо', при котором вещество внешне кажется очищенным, но сохраняет скрытые дефекты. Такие составы нестабильны и разрушаются при переходе к следующей стадии.",

    "Опытные алхимики проверяют чистоту Альбедо с помощью тонких магических тестов, включая анализ вибрационного отклика вещества и его реакции на внешние энергетические импульсы.",

    "Успешное завершение Альбедо означает, что материя готова к дальнейшей трансформации. Она становится проводником, способным удерживать более сложные формы энергии.",

    "Параллельно с этим алхимик достигает внутреннего равновесия. Его сознание становится устойчивым, а магическое ядро — сбалансированным и предсказуемым.",

    "Именно на стадии Альбедо формируется основа будущего успеха. Любая ошибка, допущенная здесь, проявится на более поздних этапах с многократно усиленным эффектом.",

    "Таким образом, Нигредо и Альбедо представляют собой не просто этапы, а фундаментальную двойственность алхимического пути: разрушение и очищение, хаос и порядок.",

    "Только тот, кто способен полностью принять оба состояния, может двигаться дальше по пути Великого Делания."
      ]
    },
    {
      t: "Глава 3: Универсальный растворитель и трансмутация",
      p: [
        "Алкагест — универсальный растворитель, считающийся одной из величайших и наиболее опасных целей алхимии. Это вещество способно расщепить любую форму материи до её первичного состояния — эфира или Prima Materia, из которой происходят все элементы мира.",

    "В отличие от обычных кислот или магических реагентов, Алкагест не разрушает вещество в привычном смысле. Он не уничтожает структуру, а возвращает её к исходной, недифференцированной форме, стирая различия между элементами.",

    "Именно поэтому Алкагест невозможно хранить в стандартных сосудах. Любой материал, вступающий с ним в контакт, постепенно теряет свою структуру. Единственным известным исключением является обсидиан, прошедший магическую закалку в драконьем пламени.",

    "Даже в таком сосуде Алкагест остаётся нестабильным. Он стремится взаимодействовать с окружающей средой, реагируя на магические поля, тепло и даже присутствие живых существ.",

    "Работа с Алкагестом требует абсолютной концентрации. Малейшее отвлечение может привести к тому, что растворитель начнёт взаимодействовать с лабораторией, воздухом или самим алхимиком.",

    "Существует теория, что Алкагест обладает формой 'псевдо-сознания'. Он как бы 'ищет' структуру для разрушения, реагируя на наличие упорядоченной материи вокруг себя.",

    "Основное применение Алкагеста связано с процессом трансмутации. Разрушив вещество до состояния эфира, алхимик получает возможность заново задать его структуру, создавая абсолютно новые формы материи.",

    "Трансмутация на этом уровне не ограничивается простыми превращениями. Речь идёт о полном переписывании свойств вещества: плотности, температуры, проводимости и даже магического резонанса.",

    "Опытный алхимик способен, используя Алкагест, превратить газ в твёрдое тело, жидкость — в энергию, а свет — в материальный объект. Эти процессы требуют не заклинаний, а чистой воли и точного понимания структуры эфира.",

    "Ключевым понятием здесь является 'вибрационный код материи'. Каждое вещество имеет собственную частоту существования. Изменяя эту частоту, алхимик изменяет саму природу объекта.",

    "Однако любое вмешательство в структуру эфира требует энергетической компенсации. Алхимик платит за трансмутацию собственной жизненной силой или запасами магического ядра.",

    "Чрезмерное использование трансмутации может привести к истощению, потере контроля над магией или даже разрушению собственной энергетической структуры.",

    "Существует также риск 'обратной трансмутации', при которой изменённое вещество возвращается к исходному состоянию, высвобождая накопленную энергию. Это может привести к взрыву или пространственному искажению.",

    "Особо опасным является создание нестабильных форм материи. Такие объекты могут существовать лишь ограниченное время, после чего распадаются, иногда с разрушительными последствиями.",

    "Некоторые алхимики пытались использовать Алкагест для создания идеальных материалов — неразрушимых, вечных и полностью устойчивых. Однако такие эксперименты часто заканчивались катастрофами.",

    "Причина этого заключается в том, что мир стремится к равновесию. Любая попытка создать абсолютную стабильность вызывает ответную реакцию, направленную на её разрушение.",

    "Алкагест также используется для очистки артефактов от магических загрязнений. Он способен удалить даже самые древние заклятия, возвращая объект к его изначальному состоянию.",

    "Тем не менее, такой процесс стирает не только вредные, но и полезные свойства. Артефакт теряет свою историю, превращаясь в чистую, но пустую форму.",

    "Таким образом, универсальный растворитель является не просто инструментом, а символом абсолютной власти над материей. Он даёт возможность создавать, разрушать и изменять всё существующее.",

    "Однако эта власть всегда сопровождается риском. Алхимик, работающий с Алкагестом, должен понимать, что он имеет дело не с веществом, а с самой основой реальности.",

    "Именно поэтому древние мастера предупреждали: тот, кто полностью овладеет Алкагестом, сможет изменить мир, но малейшая ошибка приведёт к его разрушению."
      ]
    }
  ]
},
  {
  id: 10,
  t: "Прорицание: Будущее",
  a: "Кассандра Ваблатски",
  cat: "Прорицание",
  ic: "fa-eye",
  c: ["#483d8b", "#191970"],
  ch: [
    {
      t: "Глава 1: Природа Внутреннего Ока",
      p: [
        "Прорицание — это самая сложная и недооцененная ветвь магических искусств. В отличие от Зельеварения или Трансфигурации, здесь недостаточно просто следовать формуле. Вам нужно научиться отключать логический разум и позволять Внутреннему Оку видеть сквозь завесу настоящего.",
        "Будущее не является статичной картиной; это бесконечный океан вероятностей. Однако существуют 'Узловые точки Судьбы' — события, которые произойдут неизбежно. Истинный провидец не просто видит картинки, он чувствует вкус грядущего времени.",
        "Помните: если вы не видите ничего, кроме тумана, возможно, ваше Внутреннее Око просто затуманено бытовыми заботами. Начните с очистки сознания и помните — будущее не любит тех, кто слишком настойчиво в него всматривается."
      ]
    },
    {
      t: "Глава 2: Тассеомантия — Чтение чайных листьев",
      p: [
        "Гадание на чайных листьях — это классический метод, требующий тонкой интуиции. Выпив чай, вы оставляете в чашке отпечаток своего магического поля, который структурирует чаинки в символы. Грим в чашке — это не повод для немедленной паники, если вы умеете отличать знаки судьбы от плохой заварки или случайного мусора.",
        "Символы нужно читать по часовой стрелке: от ручки чашки (настоящее) к противоположному краю (далекое будущее). Сокол — это наличие врага, Кинжал — опасность, а Череп — предвестие суровых испытаний. Но помните: символы могут противоречить друг другу.",
        "Никогда не интерпретируйте знаки в состоянии гнева или страха. Ваше собственное состояние может исказить узор, превращая безобидное Солнце (счастье) в зловещее Око (слежка). Слушайте шепот своей интуиции, а не стук своего сердца."
      ]
    },
    {
      t: "Глава 3: Хрустальные шары и вглядывание в пустоту",
      p: [
        "Кристалломантия — это искусство для терпеливых. Хрустальный шар не показывает кино; он служит фокусом для вашего разума. Сначала вы увидите лишь молочный туман внутри камня. Не пытайтесь заставить шар показать что-то конкретное — это путь к самообману.",
        "Расслабьте взгляд, позвольте зрачкам расшириться. В какой-то момент туман начнет расступаться, открывая образы, которые могут показаться бессвязными. Пылающая башня, плачущий ребенок или серебряная лань — это метафоры, которые ваше подсознание извлекает из потока времени.",
        "Опасность этого метода заключается в 'застревании'. Некоторые провидцы так увлекаются видениями, что их разум навсегда остается в лабиринтах возможного будущего, оставляя тело пустой оболочкой. Всегда держите рядом зажженную свечу — её пламя будет вашим якорем в реальности."
      ]
    },
    {
      t: "Глава 4: Хиромантия и линии жизни",
      p: [
        "Ваша ладонь — это карта, на которой магическая энергия прочертила маршруты вашей жизни еще до вашего рождения. Линия Жизни, Линия Ума и Линия Сердца постоянно меняются, отражая сделанный вами выбор, но основные 'перекрестки' остаются неизменными.",
        "Короткая Линия Жизни вовсе не означает раннюю смерть; она может указывать на резкую перемену курса или начало совершенно новой главы. Аномальные знаки, такие как кресты или решетки на холмах планет, предупреждают о кармических долгах, которые придется выплатить.",
        "Хиромантия требует тактильного контакта. Вы должны чувствовать тепло кожи и пульсацию магии в венах того, кому гадаете. Только так вы сможете понять, являются ли линии на руке истинным путем или лишь временным отражением мимолетных желаний."
      ]
    },
    {
      t: "Глава 5: Ответственность Провидца",
      p: [
        "Видеть будущее — это бремя, а не дар. Самый большой вопрос, который стоит перед каждым из нас: стоит ли рассказывать человеку о грядущей беде? Иногда знание о пророчестве становится тем самым механизмом, который заставляет его исполниться.",
        "Истинное мастерство Кассандры заключается не в том, чтобы напугать, а в том, чтобы подготовить. Магия — это воля, и даже самый мрачный прогноз — это лишь предупреждение о том, что случится, если ничего не менять.",
        "Завершая это руководство, я напоминаю: Третий Глаз видит правду, но только ваше сердце решает, что с этой правдой делать. Будьте осторожны в своих видениях, ибо туманы будущего коварны и не знают жалости к тем, кто забрел в них слишком далеко."
      ]
    }
  ]
},
  {
  id: 11,
  t: "Уход за существами",
  a: "Рубеус Хагрид",
  cat: "Магозоология",
  ic: "fa-dragon",
  c: ["#556b2f", "#2f4f4f"],
  ch: [
    {
      t: "Глава 1: Драконы — Непонятые гиганты",
      p: [
        "Многие думают, что драконы — это просто груда чешуи и пламени, но на самом деле они куда сложнее. Драконы — древние существа с собственной логикой поведения, и если вы научитесь её понимать, то шансы остаться в живых заметно возрастут.",

    "Прежде всего, запомните: дракон никогда не нападает без причины. Ну… почти никогда. Чаще всего вы просто подошли слишком близко, слишком быстро или слишком вкусно пахнете. Особенно если на вас остались следы мяса или магических зелий.",

    "Разные виды драконов ведут себя по-разному. Норвежский горбатый, например, более любопытный и может подойти сам. А вот Венгерская хвосторога — совсем другое дело. Эта сначала поджарит, а потом будет разбираться, кто вы такой.",

    "Очень важно следить за сигналами. Если дракон прижимает крылья и смотрит на вас неподвижно — это ещё терпимо. Если начинает рыть землю когтями — лучше медленно отступать. А если из ноздрей пошёл дым — тут уже поздно философствовать.",

    "Некоторые считают, что дракона можно 'задобрить'. В этом есть доля правды. Драконы уважают уверенность. Если вы ведёте себя как добыча — вы и есть добыча. Если ведёте себя спокойно и уверенно — есть шанс, что он просто потеряет к вам интерес.",

    "Контактировать с драконом напрямую — дело крайне рискованное. Но если уж пришлось, используйте длинные инструменты: кочерги, посохи, зачарованные перчатки. Никогда не тянитесь к нему голыми руками, даже если он кажется спокойным.",

    "Место под подбородком действительно у многих драконов чувствительное. Но это не значит, что стоит сразу туда лезть. Сначала убедитесь, что он не пытается вас съесть, а уже потом думайте о дружбе.",

    "Кормление — отдельная история. Молодые драконы требуют частого питания, иначе становятся агрессивными. Лучше всего подходит свежее мясо, желательно ещё тёплое. Некоторые заводчики добавляют немного алкоголя, чтобы успокоить темперамент, но тут важно не переборщить.",

    "Никогда не кормите дракона с рук. Даже маленького. Сегодня он аккуратно берёт кусок, а завтра откусывает вам руку по локоть. У них, знаете ли, быстро меняются привычки.",

    "Температура вокруг дракона — тоже важный фактор. Их пламя может нагревать воздух до опасных значений, поэтому всегда используйте защитную одежду: жаропрочные рукавицы, плащи с охлаждающими чарами и крепкую обувь.",

    "Особое внимание уделяйте хвосту. Многие забывают, что у дракона опасна не только пасть. Один взмах хвоста может сбить вас с ног или переломать кости быстрее, чем вы успеете сказать 'ой'.",

    "Драконьи яйца — это вообще отдельная тема. Они крайне чувствительны к температуре и магическому фону. Если яйцо начинает трескаться — держитесь подальше. Мама-дракон в этот момент становится особенно… ну… недружелюбной.",

    "И ещё одно: никогда не поворачивайтесь к дракону спиной. Даже если он кажется спокойным. Особенно если он кажется спокойным.",

    "Несмотря на всё это, драконы — удивительные создания. У них есть своя привязанность, свои привычки и даже что-то вроде характера. Просто этот характер обычно сопровождается огнём и клыками.",

    "Если вы научитесь уважать дракона, а не бояться его бездумно — у вас появится шанс не только выжить, но и понять одно из самых величественных существ магического мира.",

    "Ну и запомните главное правило: если сомневаетесь — бегите. Быстро. Очень быстро."
      ]
    },
    {
      t: "Глава 2: Гиппогрифы и кодекс чести",
      p: [
        "Гиппогрифы — существа не только сильные, но и невероятно гордые. Они не потерпят неуважения ни в какой форме. В отличие от многих магических животных, с ними нельзя действовать силой — только уважением и пониманием.",

    "Первое правило при встрече с гиппогрифом — никогда не подходите без подготовки. Прежде чем сделать шаг вперёд, остановитесь, выпрямитесь и установите зрительный контакт. Это не просто жест — это начало диалога.",

    "Поклон — обязательная часть взаимодействия. Он должен быть чётким и уверенным. Слишком резкий поклон может показаться насмешкой, слишком слабый — признаком неуважения. Гиппогрифы чувствуют такие нюансы лучше любого мага.",

    "После поклона наступает самый напряжённый момент — ожидание ответа. Если гиппогриф поклонился в ответ, значит, вы приняты. Это редкая честь, и её нельзя воспринимать как должное.",

    "Если же он не отвечает, а начинает двигаться, расправлять крылья или бить копытом — немедленно прекращайте попытки сближения. Отступайте медленно, не отрывая взгляда. Резкие движения могут спровоцировать атаку.",

    "Гиппогрифы крайне чувствительны к поведению человека. Они воспринимают страх, агрессию и неуверенность. Если вы нервничаете — он это заметит. А если он решит, что вы представляете угрозу, защищаться будет без предупреждения.",

    "Установив доверие, вы можете подойти ближе. Однако даже в этом случае нельзя сразу прикасаться к существу. Дайте гиппогрифу самому сократить дистанцию. Это его право.",

    "Прикосновение должно быть осторожным и уважительным. Лучше всего начинать с лёгкого контакта по шее или боку. Никогда не трогайте крылья без явного разрешения — это считается грубым нарушением.",

    "Кормление гиппогрифов требует особого внимания. Они предпочитают свежее мясо, но подача должна быть аккуратной. Бросать пищу или размахивать ей перед мордой — плохая идея.",

    "Также важно учитывать территориальность. Гиппогриф защищает своё пространство и особенно — своё гнездо. Приближение к яйцам почти всегда воспринимается как угроза.",

    "Если гиппогриф начинает издавать резкие звуки или расправляет крылья в полный размах — это предупреждение. Следующее действие, скорее всего, будет атакой.",

    "Однако при правильном подходе гиппогриф может стать надёжным союзником. Некоторые из них позволяют себя оседлать, но только тем, кому полностью доверяют.",

    "Полёт на гиппогрифе требует координации и спокойствия. Любое резкое движение может нарушить баланс и привести к падению.",

    "Запомните: гиппогриф не подчиняется — он соглашается. Это ключевое различие. Вы не управляете им, вы взаимодействуете с ним.",

    "И ещё одно: гиппогрифы никогда не забывают. Ни хорошего, ни плохого. Один неверный поступок может закрыть для вас путь к доверию навсегда.",

    "Поэтому относитесь к ним с уважением, как к равным. И тогда, возможно, вы увидите, как это — летать рядом с существом, которое выбрало доверять вам."
  ]
    },
    {
      t: "Глава 3: Соплохвосты и другие 'радости'",
      p: [
        "Вывели мы тут как-то Соплохвостов... Удивительные существа! Смесь рака, скорпиона и, похоже, чистого безумия. У них нет головы в привычном смысле, зато есть мощный хвост, из которого они с радостью стреляют огнём при любом удобном случае.",

    "С самого рождения соплохвосты проявляют крайне активное поведение. Они постоянно двигаются, копают, грызут и, если им скучно, начинают разрушать всё вокруг. Поэтому вольеры для них должны быть усиленными — обычное дерево они прогрызают за считанные минуты.",

    "Молодые особи особенно опасны из-за своей непредсказуемости. Сегодня он просто ползает по кругу, а через секунду уже пытается поджечь вам ботинок. Никогда не недооценивайте даже самого маленького соплохвоста.",

    "Кормление — отдельная проблема. Они едят практически всё: мясо, кожу, ткань, иногда даже металлические предметы, если те достаточно мягкие. Лучше всего подходит сырая печень или плотные органические материалы.",

    "Если соплохвост начал активно щёлкать или издавать треск — это признак возбуждения. Обычно за этим следует выброс пламени. В этот момент лучше держаться на расстоянии и не делать резких движений.",

    "Особую опасность представляет их способность присасываться. У них есть крючкообразные зубы, которые позволяют им закрепляться на поверхности. Если один из них вцепился в вас — не паникуйте и ни в коем случае не дёргайте.",

    "Лучший способ избавиться от такого 'пассажира' — отвлечь его. Бросьте рядом что-то съедобное или просто менее важное, чем вы сами. Как правило, они быстро переключаются на новую цель.",

    "Сезон роста у соплохвостов сопровождается резкими изменениями поведения. В этот период они становятся агрессивнее, быстрее и… взрывоопаснее. Да, буквально. Некоторые особи способны к самопроизвольным огненным выбросам повышенной мощности.",

    "Именно в это время необходимо соблюдать максимальную осторожность. Защитная экипировка обязательна: плотные перчатки, огнеупорные плащи и защитные очки.",

    "Содержать нескольких соплохвостов в одном вольере — рискованная идея. Они склонны к агрессии друг к другу, особенно при нехватке еды или пространства.",

    "Тем не менее, при правильном подходе их можно изучать и даже частично контролировать. Некоторые исследователи считают, что соплохвосты реагируют на магический резонанс и могут распознавать знакомых людей.",

    "Однако это не делает их безопасными. Даже если существо 'узнало' вас, это не значит, что оно не попытается вас поджечь через минуту.",

    "Работа с соплохвостами требует не только знаний, но и быстрой реакции. Это не те существа, с которыми можно расслабиться хоть на секунду.",

    "И, пожалуй, самое главное: если вам кажется, что всё под контролем — скорее всего, вы просто ещё не заметили, как один из них уже подкрался сзади.",

    "Соплохвосты — это не просто опасные существа. Это настоящее испытание для любого магозоолога. Но, признаюсь честно… чертовски интересное испытание."
      ]
    }
  ]
},
  {
  id: 12,
  t: "Пособие по трансфигурации",
  a: "Эмерик Эттич",
  cat: "Трансфигурация",
  ic: "fa-cat",
  c: ["#4682b4", "#000080"],
  ch: [
    {
      t: "Глава 1: Фундаментальные законы превращения",
      p: [
        "Трансфигурация — самая точная и опасная ветвь магии. Ошибка в расчетах при превращении неживого в живое может привести к созданию 'недосуществ' — гротескных гибридов, обреченных на вечные мучения. Ваша палочка должна двигаться строго по вектору, описанному в параграфе 4.2.",
        "Никогда не пытайтесь превратить пуговицу или спичку в еду. Это прямо нарушает Первый закон элементарных исключений Гэмпа. Вы получите нечто, выглядящее как хлеб, но не обладающее его питательными свойствами. Попытка съесть такую трансфигурацию приведет к тяжелому магическому отравлению желудка.",
        "Помните: чем больше масса объекта, тем больше магического давления требуется на его молекулярную решетку. Начинайте с малого — превращения иголок в спички, и только через годы практики переходите к межвидовым превращениям."
      ]
    },
    {
      t: "Глава 2: Анимагия и риски самопревращения",
      p: [
        "Высшая ступень нашей дисциплины — Анимагия. Способность превращаться в животное по собственной воле требует не только огромной силы, но и безупречного самоконтроля. Процесс становления анимагом занимает годы и может закончиться плачевно: если в момент трансформации вы потеряете концентрацию, вы навсегда застрянете в получеловеческой форме.",
        "Регистрация в Министерстве магии обязательна. Незарегистрированные анимаги подвергаются преследованию, так как их способности представляют угрозу для Статута о секретности. Ваша животная форма всегда отражает вашу внутреннюю сущность — вы не выбираете её, она выбирает вас."
      ]
    },
    {
      t: "Глава 3: Исчезающие и Проявляющие заклятия",
      p: [
        "Заклинание 'Эванеско' не уничтожает предмет, оно отправляет его в 'небытие', которое, если выражаться научно, является состоянием абсолютного отсутствия формы. Это гораздо сложнее, чем просто сделать предмет невидимым.",
        "Проявляющие чары действуют в обратном порядке, восстанавливая структуру из информационного поля реальности. Ошибка здесь может привести к 'расщеплению' — когда часть предмета возвращается, а часть остается в небытии. Работайте с осторожностью."
      ]
    }
  ]
},
  {
    id: 13,
    t: "Магическое искусство полета",
    a: "Кеннилуорти Уисп",
    cat: "Полеты",
    ic: "fa-location-arrow",
    c: ["#1a472a", "#05140a"],
    ch: [
      {
        t: "Уход за метлой",
        p: [
          "Никогда не забывайте смазывать прутья маслом для древесины раз в месяц. Это предотвратит заносы на крутых виражах.",
          "Если ваша метла начала вибрировать на высоте более десяти футов, скорее всего, в рукояти завелся докси."
        ]
      }
    ]
  },
  {
  id: 14,
  t: "Нумерология и грамматика",
  a: "Л. Бесплатный",
  cat: "Нумерология",
  ic: "fa-calculator",
  c: ["#483d8b", "#2a2a5e"],
  ch: [
    {
      t: "Глава 1: Гептада — Сакральная сила Семёрки",
      p: [
        "Семь — самое могущественное магическое число, признанное всеми великими цивилизациями. Оно лежит в основе основ мироздания: семь дней недели, семь цветов радуги, семь курсов обучения в Хогвартсе и семь Пяти принципиальных исключений из закона Гэмпа. Это число завершенности и магической стабильности.",
        "В нумерологических расчетах Семёрка действует как усилитель. Если заклинание произносится семь раз или магический круг имеет семь узловых точек, его мощность возрастает в геометрической прогрессии. Однако Семёрка крайне ревнива: малейшая ошибка в расчетах на таком уровне силы ведет к катастрофическому коллапсу магического поля.",
        "При расчете личного числа судьбы всегда учитывайте фазу луны в момент вашего рождения. Лунный цикл, разделенный на четыре четверти по семь дней, напрямую влияет на то, будет ли ваша 'Семёрка' созидательной или разрушительной в текущем году."
      ]
    },
    {
      t: "Глава 2: Число Три и Магический Треугольник",
      p: [
        "Если Семь — это стабильность, то Три — это динамика и первоначальный импульс. Магия Тройки проявляется в триединстве 'Формула — Жест — Намерение', без которого немыслимо ни одно заклинание. Три — это число выбора, баланса между прошлым, настоящим и будущим.",
        "В Нумерологии мы используем Треугольник Пифагора для анализа совместимости мага с его палочкой. Если сумма числовых значений имени владельца и древесины палочки кратна трем, связь будет гармоничной. Если же возникает диссонанс, палочка может 'капризничать', выдавая искры вместо четких чар.",
        "Помните: трижды произнесенное проклятие практически невозможно снять обычными анти-заклятиями, так как оно образует замкнутый энергетический контур."
      ]
    },
    {
      t: "Глава 3: Грамматика имен и нумерологический код",
      p: [
        "Грамматика в магии — это не правописание, а искусство вычисления истинного веса слов. Каждая буква алфавита соответствует определенному числу. Сумма этих чисел в имени человека определяет его предрасположенность к тем или иным дисциплинам.",
        "Например, имена, чей код равен Девяти, часто принадлежат выдающимся провидцам или алхимикам, так как Девять — это число высшего знания, стоящее на пороге перехода в новый цикл. Имена с кодом Пять указывают на талант к Трансфигурации, отражая изменчивую природу этого числа.",
        "Будьте осторожны при смене имени или получении прозвища. Новый звуковой код меняет вашу нумерологическую частоту, что может привести к временной потере магических способностей или изменению характера ваших заклинаний."
      ]
    },
    {
      t: "Глава 4: Золотое сечение и архитектура заклятий",
      p: [
        "Нумерология тесно связана с геометрией. Наиболее эффективные защитные барьеры строятся на принципах Золотого сечения ($1.618$). Когда пропорции магического щита соответствуют этому числу, он становится практически непроницаемым, так как вибрирует в унисон с самой природой.",
        "При создании собственных заклинаний (что разрешено только мастерам высшей категории) расчет ритмики произнесения формулы должен основываться на числах Фибоначчи. Паузы между слогами, рассчитанные таким образом, позволяют магической энергии накапливаться плавно, без рывков, предотвращая перегрузку палочки.",
        "Математика — это язык, на котором магия разговаривает с реальностью. Тот, кто игнорирует числа, обречен на хаос."
      ]
    }
  ]
},
  {
  id: 15,
  t: "Искусство легилименции",
  a: "Салазар Слизерин",
  cat: "Ментальная магия",
  ic: "fa-eye",
  c: ["#0a2e1c", "#000000"],
  ch: [
    {
      t: "Глава 1: Природа Ментального Слоения",
      p: [
        "Разум — это не книга, которую можно открыть и прочесть на досуге. Это слоистый лабиринт из чувств, обрывочных образов и глубоко запрятанных страхов. Обыватели ошибочно полагают, что легилимент просто слышит голоса в чужой голове. На деле же мы считываем саму суть сознания.",
        "Легилименция требует полного отсутствия эмоций у самого заклинателя. Если вы разгневаны или напуганы, ваши собственные чувства создадут шум, который сделает проникновение невозможным. Вы должны стать холодным зеркалом, в котором отражается чужая душа.",
        "Первый слой — это поверхностные мысли. Их легко подделать. Истинный мастер ищет 'ядро памяти' — те моменты, которые сформировали личность жертвы. Найти их — значит получить полную власть над человеком."
      ]
    },
    {
      t: "Глава 2: Визуальный контакт и Вектор Взгляда",
      p: [
        "Глаза — это не только зеркало души, но и её главные ворота. Заклинание 'Легилименс' наиболее эффективно при прямом визуальном контакте. В этот момент барьеры между двумя сознаниями истончаются, позволяя вашей воле скользнуть внутрь.",
        "Опытному легилименту не всегда нужна палочка или вербальное заклинание. Достаточно поймать взгляд собеседника на долю секунды, чтобы ощутить его истинные намерения. Именно поэтому те, кому есть что скрывать, никогда не смотрят в глаза прямо.",
        "Помните: если ваша цель осознает атаку, она может попытаться 'выбросить' вас из своего разума, создав ментальный шок. Это может привести к повреждению вашего собственного магического ядра."
      ]
    },
    {
      t: "Глава 3: Преодоление Окклюменции",
      p: [
        "Окклюменция — это жалкая попытка слабых защитить свои секреты. Она заключается в очистке разума от эмоций, чтобы легилимент не нашел за что зацепиться. Но у каждой стены есть трещина.",
        "Чтобы взломать окклюмента, нужно спровоцировать у него резкий эмоциональный всплеск. Боль, ярость или внезапный страх заставляют барьеры рушиться. В этот краткий миг вы должны ударить по самому болезненному воспоминанию. Как только первая брешь пробита, разум жертвы раскрывается перед вами, как увядающий цветок.",
        "Никогда не задерживайтесь в чужом разуме слишком долго. Есть риск 'растворения', когда чужие воспоминания начинают казаться вашими собственными. Держите свою личность запертой в стальной сейф."
      ]
    },
    {
      t: "Глава 4: Этическая тирания и Власть",
      p: [
        "Министерство магии считает легилименцию 'сомнительным' искусством. Они боятся того, что не могут контролировать. Но истина в том, что знание правды — это высшее право сильного. Ложь — удел слабых и маглов.",
        "Используя легилименцию, вы всегда будете на шаг впереди. Вы будете знать о предательстве до того, как оно будет совершено, и о любви до того, как она будет высказана. В этом мире нет тайн для того, чье око открыто.",
        "Завершая этот труд, я, Салазар Слизерин, напоминаю: разум — это крепость. Если вы не научитесь захватывать чужие крепости, однажды вы обнаружите, что ваша собственная превратилась в руины."
      ]
    }
  ]
},
  {
  id: 16,
  t: "Справочник по палочкам",
  a: "Гаррик Олливандер",
  cat: "Артефакторика",
  ic: "fa-wand-sparkles",
  c: ["#5c4033", "#2b1b12"],
  ch: [
    {
      t: "Глава 1: Выбор палочки и Закон Родства",
      p: [
        "Каждая палочка, которую я когда-либо создавал, является уникальным магическим существом. Самая большая ошибка молодого мага — думать, что он может просто купить инструмент. Помните: палочка выбирает волшебника, а не наоборот. Это союз, основанный на резонансе душ.",
        "Когда палочка находит своего хозяина, происходит вспышка — не просто света, а узнавания. Если вы попытаетесь колдовать чужой палочкой, магия будет слабой, непокорной и тусклой. Палочка, добытая в бою, может сменить верность, но она никогда не забудет прикосновение своего первого создателя.",
        "Длина палочки часто соответствует росту или силе характера мага, но это не догма. Короткие палочки обычно ищут тех, чья магия изящна и точна, в то время как длинные предпочитают масштабные и порывистые натуры."
      ]
    },
    {
      t: "Глава 2: Сердцевины — Душа инструмента",
      p: [
        "Волос единорога дает самую стабильную магию. Такие палочки меньше всего подвержены колебаниям настроения и реже всего выдают случайные вспышки. Они преданы своему первому владельцу и крайне редко переходят на сторону Темных Искусств. Но будьте осторожны: волос единорога может 'загрустить', если с ним плохо обращаться, и палочка просто откажется работать.",
        "Сердечная жила дракона производит самые мощные заклинания. Они быстрее всего обучаются новым чарам и обладают колоссальным темпераментом. Однако палочки с жилой дракона наиболее склонны к несчастным случаям и легче всего переходят к новому владельцу, если тот победил прежнего.",
        "Перо феникса — самая редкая сердцевина. Эти палочки обладают собственной инициативой. Иногда они могут действовать по своему усмотрению, что пугает многих магов. Они крайне избирательны, так как феникс — существо независимое. Если такая палочка выбрала вас, знайте: вам суждено совершить нечто великое."
      ]
    },
    {
      t: "Глава 3: Древесина — Тело и Темперамент",
      p: [
        "Остролист (Holly) — одна из редких пород, которая идеально подходит для защитной магии. Она выбирает тех, кому часто суждено преодолевать гнев и импульсивность. Тис (Yew) же имеет репутацию дерева, дарующего власть над жизнью и смертью. Тисовые палочки никогда не выбирают посредственностей.",
        "Виноградная лоза ищет личностей с глубокими скрытыми целями, чья натура сложнее, чем кажется на первый взгляд. Вяз предпочитает магов с врожденным достоинством и магической ловкостью. Акация — дерево капризное, она отказывается колдовать для любого, кроме своего хозяина, и часто приберегает свои лучшие силы для самых сложных заклятий.",
        "Черное дерево (Ebony) идеально для боевой магии и трансфигурации. Оно любит тех, кто имеет мужество быть самим собой и не склоняется перед чужим мнением."
      ]
    },
    {
      t: "Глава 4: Уход и этика обращения",
      p: [
        "Палочка — это продолжение вашей руки и вашей воли. Никогда не оставляйте её в сырости или под прямыми лучами палящего солнца. Если дерево начнет трескаться, магический канал искривится, и заклинание может ударить по вам самим.",
        "Регулярная полировка маслом из семян бадьяна помогает сохранить гибкость древесины. И помните: никогда, ни при каких обстоятельствах, не пытайтесь самостоятельно заменить сердцевину. Это равносильно попытке пересадить сердце живому человеку без наркоза. Магия — это не механика, это жизнь.",
        "Я помню каждую палочку, которую продал. Я помню их радость и их печаль. Относитесь к своей палочке как к лучшему другу, и она спасет вам жизнь в самый темный час."
      ]
    }
  ]
},
  {
  id: 17,
  t: "Славянские заговоры",
  a: "Баба Яга",
  cat: "Древняя магия",
  ic: "fa-mortar-pestle",
  c: ["#8b0000", "#3a0000"],
  ch: [
    {
      t: "Глава 1: Слово заветное и Сила Намерения",
      p: [
        "Славянская магия не в палочке живет, а в голосе да в сердце. Заговор — это не просто шепот, это мост между миром живых и миром духов. Коли слово твое твердо, как камень Алатырь, то и стихии тебе подчинятся. Но помни: коли в сердце черная зависть притаилась, заговор обернется против тебя самого, как дикий зверь на охотника.",
        "Прежде чем за дело браться, нужно три дня постить да росой утренней умываться. Духи леса не любят суеты и городского шума. Магия заговора требует ритма — он должен литься, как лесная река, без запинок и сомнений. Ошибешься в слове — потеряешь силу, а то и вовсе в болото угодишь.",
        "Помни: заговор без жертвы (пусть то будет горсть зерна или лента на березе) — это воровство у природы. Умей отдавать, чтобы получить."
      ]
    },
    {
      t: "Глава 2: Заклятье невидимой избушки",
      p: [
        "Чтобы скрыть свое жилище от незваных гостей и глаз завистливых, нужно посыпать порог солью заговоренной, смешанной с пыльцой феи и тертой корой старого дуба. Соль путь преградит, а пыльца глаза отведет.",
        "Встань в центре двора, обернись трижды вокруг своей оси против солнца и произнеси громко и четко: 'Встань ко мне передом, к лесу задом. Замкнись на замок костяной, на ключ железный. Кто без зова придет — мимо пройдет, дороги не найдет'.",
        "После этого нужно воткнуть в косяк двери старую иглу. Пока игла на месте — дом твой для чужака будет как морок туманный: вроде есть, а вроде и нет его."
      ]
    },
    {
      t: "Глава 3: Травница — Сила Корений",
      p: [
        "Не всякая трава полезна, коли сорвана не в срок. Полынь-трава отгоняет нечисть, если её под кровать положить, а зверобой силу дает, коли в котел на Купалу брошен. Но самое сильное средство — разрыв-трава. Добыть её трудно, цветет она миг один, но замок любой перед ней открывается, как лист осенний перед ветром.",
        "Корни деревьев старых тоже магию знают. Корень ивы плакучей в настойке помогает сердце разбитое склеить, а корень дуба — волю закалить. Вари коренья долго, на медленном огне, шепча: 'Как земля корень держит, так и слово мое силу берет'.",
        "Никогда не бери у леса больше, чем нужно. Сорвешь лишний куст — леший тропы запутает так, что и до зимы не выйдешь."
      ]
    },
    {
      t: "Глава 4: Обереги и Узелковая магия (Наузы)",
      p: [
        "Наузы — это магия узелков. Каждый узел — это желание, в нить завязанное. Хочешь удачи в пути — вяжи узел на красной нитке шерстяной, приговаривая о дороге ровной. Хочешь защиты от сглаза — вяжи узел сложный, 'мертвый', на нити льняной.",
        "Обереги из кости или камня с вырезанными знаками солнца тоже великую силу имеют. Они как щит невидимый, который стрелы магические ловит. Носи оберег ближе к телу, чтобы он теплом твоим питался.",
        "Коли почувствуешь, что оберег потяжелел или треснул — значит, удар он на себя принял. Поблагодари его и предай земле или огню, а себе новый делай."
      ]
    },
    {
      t: "Глава 5: Советы Старой Яги",
      p: [
        "Приходят ко мне добры молодцы да красны девицы, все власти хотят, а сами тени своей боятся. Запомни, милок: магия — это не игрушка. Это ответственность. Коли решил заговоры учить — будь готов, что мир для тебя другим станет.",
        "Слушай лес, слушай ветер, слушай воду. В них все ответы есть, если уши открыты. И никогда не смейся над тем, чего не понимаешь — в магии смех часто слезами заканчивается.",
        "А теперь ступай. Знания я тебе дала, а как ты их применишь — то твоя доля. Но помни: я за тобой приглядываю."
      ]
    }
  ]
},
  {
  id: 18,
  t: "Живая трансфигурация",
  a: "Минерва Макгонагалл",
  cat: "Трансфигурация",
  ic: "fa-hat-wizard",
  c: ["#1e3a5f", "#0a1a2f"],
  ch: [
    {
      t: "Глава 1: Этический кодекс и молекулярный резонанс",
      p: [
        "Трансфигурация живых существ — это не просто изменение формы, это временная перезапись кода самой жизни. Ошибка в один градус при взмахе палочкой может привести к тому, что подопытный объект окажется в состоянии перманентного страдания. Вы обязаны осознавать ответственность за каждое движение.",
        "Превращение неживого в живое (например, стола в свинью) требует колоссальной концентрации. Вы не просто придаете форму, вы имитируете искру жизни. Помните: трансфигурированные существа не обладают истинной душой и памятью, они — лишь сложнейшие магические проекции вашей воли.",
        "Первое правило класса: никакой небрежности. Если вы не способны превратить спичку в иголку со стопроцентной точностью, вам нечего делать в кабинете живой трансфигурации."
      ]
    },
    {
      t: "Глава 2: Анимагия — Высшая форма трансформации",
      p: [
        "Анимагия — это процесс слияния человеческого разума с животной инстинктивностью. В отличие от обычного превращения, анимаг сохраняет способность мыслить как человек, находясь в теле зверя. Однако этот путь смертельно опасен: малейшая потеря контроля над своей человеческой сущностью в момент перехода может привести к необратимым последствиям — вы навсегда останетесь существом без разума.",
        "Процесс становления анимагом занимает годы. Он включает в себя хранение листа мандрагоры во рту в течение целого месяца, создание сложнейшего зелья и ожидание грозы. Это испытание воли, которое проходят лишь единицы.",
        "Регистрация в Министерстве магии обязательна и не обсуждается. Незарегистрированный анимаг — это преступник, представляющий угрозу для всего магического сообщества. Ваша анимагическая форма — это отражение вашего внутреннего 'Я'. Вы не выбираете её; примите её с достоинством, будь то величественный орел или скромная кошка."
      ]
    },
    {
      t: "Глава 3: Межвидовые перекрестные превращения",
      p: [
        "Превращение одного живого существа в другое требует понимания анатомического сходства. Проще превратить сову в оперные очки, чем в кошку, так как структура костей и магическая проводимость перьев легче поддаются деформации под конкретную цель.",
        "При межвидовой трансфигурации важно удерживать в сознании 'сетку стабильности'. Если вы превращаете морскую свинку в морской котел, вы должны следить, чтобы биологические ритмы грызуна были полностью подавлены магическим полем, иначе котел начнет 'дышать' или проявлять признаки испуга, что сделает его использование невозможным.",
        "Сложные гибриды — это табу. Попытка создать существо, сочетающее черты разных видов, без санкции научного совета карается немедленным исключением. Магия должна служить порядку, а не порождать хаос."
      ]
    },
    {
      t: "Глава 4: Продвинутые защитные трансформации",
      p: [
        "В дуэли трансфигурация может стать вашим самым грозным оружием. Способность превратить летящее в вас заклятие в стаю птиц или каменную стену в живого змея требует мгновенной реакции. Здесь нет времени на раздумья — палочка должна стать продолжением вашей мысли.",
        "Использование элементов окружения против врага — признак истинного мастера. Превратите ковер под ногами противника в зыбучий песок или его собственную мантию в стальные путы. Но помните: опытный противник может вернуть трансфигурацию обратно, направив её против вас.",
        "Дисциплина ума — вот ваш главный щит. Тот, кто владеет собой, владеет и формой окружающего мира."
      ]
    },
    {
      t: "Заключение: Слово профессора Макгонагалл",
      p: [
        "Трансфигурация — это путь длиной в жизнь. Я занимаюсь этой наукой десятилетиями и каждый день нахожу в ней что-то новое. Не стремитесь к быстрым результатам. Уважайте законы природы, следуйте букве учебника, и магия откроет вам свои самые сокровенные тайны.",
        "А теперь, уберите свои учебники и достаньте чистые пергаменты. Нас ждет практическая работа по превращению ежей в подушечки для иголок. И попрошу без лишнего писка — как со стороны ежей, так и с вашей."
      ]
    }
  ]
},
 {
  id: 19,
  t: "Защита от темных сил",
  a: "Аластор Грюм",
  cat: "Боевая магия",
  ic: "fa-user-shield",
  c: ["#2f4f4f", "#1a1a1a"],
  ch: [
    {
      t: "Глава 1: ПОСТОЯННАЯ БДИТЕЛЬНОСТЬ!",
      p: [
        "Слушайте внимательно, потому что я повторю это только один раз: в мире Тёмных Искусств ваша жизнь висит на волоске каждую секунду. Тёмная магия не играет по правилам, она бьет в спину, когда вы меньше всего этого ждете. Никогда не поворачивайтесь спиной к противнику, даже если он кажется поверженным. Помните: мертвый Пожиратель Смерти безопаснее живого, но и за мертвыми нужно приглядывать.",
        "Тёмная магия коварна: она часто маскируется под обычные бытовые предметы. Проклятое ожерелье, кубок с ядом или даже ваша собственная обувь могут стать порталом в могилу. Если предмет выглядит подозрительно — уничтожьте его. Если он выглядит безобидно — уничтожьте его на всякий случай.",
        "Всегда проверяйте свою еду и питье. Я пью только из собственной фляги не потому, что я старый безумец, а потому, что я всё еще жив."
      ]
    },
    {
      t: "Глава 2: Непростительные заклятия и как (не) выжить",
      p: [
        "Существует три заклятия, за использование которых дают путевку в Азкабан в один конец. Но я здесь не для того, чтобы читать вам лекции по праву. Я здесь, чтобы вы знали, с чем столкнетесь. 'Авада Кедавра' — убивающее заклятие. От него нет щита. Никакое 'Протего' не остановит этот зеленый луч. Единственный способ выжить — не оказаться на его пути. Прячьтесь за колонны, за камни, за мебель — плевать, лишь бы между вами и палочкой врага была физическая преграда.",
        "Круциатус (пыточное заклятие) превратит ваши мозги в кашу, если вы позволите противнику удерживать контакт. Ваша задача — прервать его концентрацию любым способом. Ответное заклятие, бросок камня, удар в челюсть — в бою все средства хороши.",
        "Империус. Подчинение воли. Если вы почувствуете странное тепло и легкость, а голос в голове велит вам сделать что-то глупое — боритесь! Тренируйте свой разум так же, как тренируете руку с палочкой."
      ]
    },
    {
      t: "Глава 3: Контрзаклятия и тактика боя",
      p: [
        "Защита — это не просто умение выставить щит. Это умение двигаться. Стоячая мишень — это труп. Ваше 'Протего' должно быть мгновенным, как рефлекс. Но помните: щит поглощает вашу энергию. Лучшая защита — это атака, которая заставит врага обороняться.",
        "Используйте 'Экспеллиармус' не просто чтобы выбить палочку, а чтобы сломать кисть противнику. 'Остолбеней' должен бить как таран. Если враг использует темных тварей — дементоров или боггартов — вы должны знать их слабости назубок. Экспекто Патронум — это не просто красивое облачко, это ваше единственное спасение от поцелуя дементора. Нет счастливых мыслей? Значит, придумайте их, или дементор сожрет вашу душу раньше, чем вы успеете моргнуть."
      ]
    },
    {
      t: "Глава 4: Распознавание засады",
      p: [
        "Тёмные маги любят засады. Если вы заходите в помещение и там подозрительно тихо — это засада. Если вы видите на двери охранные руны — это засада. Если старый друг предлагает вам выпить и не пробует первым — это, черт возьми, определенно засада!",
        "Научитесь использовать заклинания обнаружения: 'Ревелио' и 'Гоменум Ревелио' должны стать вашими лучшими друзьями. Проверяйте каждый угол, каждый шкаф. И никогда не доверяйте внешности. Оборотное зелье позволяет превратиться в кого угодно. Даже в меня. Хотя я бы посмотрел на того идиота, который решится на такое."
      ]
    },
    {
      t: "Заключительный приказ Грюма",
      p: [
        "Вы можете выучить все заклинания из этой книги, но если у вас нет воли к победе — вы проиграете. Бой выигрывается в голове до того, как будет выпущена первая искра. Будьте готовы, будьте бдительны и, ради Мерлина, смотрите под ноги!",
        "ПОСТОЯННАЯ БДИТЕЛЬНОСТЬ!"
      ]
    }
  ]
},
  {
  id: 20,
  t: "Кулинарная магия",
  a: "Молли Уизли",
  cat: "Бытовое",
  ic: "fa-utensils",
  c: ["#cd853f", "#8b4513"],
  ch: [
    {
      t: "Глава 1: Основы магической кухни и Самопомешивание",
      p: [
        "Магия на кухне — это не способ лениться, а способ успеть всё на свете, особенно когда у вас полон дом голодных детей и садовых гномов. Чтобы рагу не пригорело, пока вы заняты более важными делами, наложите заклятье 'Агито' на половник. Главное — правильно настроить ритм, иначе соус окажется на потолке!",
        "Помните: еда, приготовленная с любовью, — это самое сильное защитное средство. Щепотка сушеной крапивы, добавленная в воскресное жаркое, не только придает соусу особую пикантность, но и служит мягким оберегом от сглаза и мелких бытовых проклятий.",
        "Всегда держите палочку сухой. Кухонные пары могут сделать древесину скользкой, и вместо того, чтобы нарезать лук, вы случайно трансфигурируете его в аквариумную рыбку. Это очень неудобно, когда гости уже на пороге."
      ]
    },
    {
      t: "Глава 2: Чистящие чары и борьба с беспорядком",
      p: [
        "Заклинание 'Тергео' — ваш лучший друг после большого семейного обеда. Оно аккуратно всасывает соусы и жир, не повреждая фамильный фарфор. Если же на сковороде пригорел жир от сосисок, используйте 'Скургфай', но будьте осторожны с дозировкой силы, чтобы не стереть рисунок с тарелок.",
        "Для сушки посуды нет ничего лучше мягкого согревающего заклинания 'Флагрэйт' на низких частотах. Посуда будет сиять, как будто её полировали эльфы. И никогда, слышите, никогда не пытайтесь ускорить мытье полов с помощью левитации мебели, если у вас в комнате летает снитч — это закончится катастрофой."
      ]
    },
    {
      t: "Глава 3: Хранение продуктов и чары расширения",
      p: [
        "Кладовая — сердце дома. Используйте чары незримого расширения для банок с вареньем, чтобы сэкономить место на полках. Маленькая баночка может вместить запас джема на всю зиму, если наложить заклятие аккуратно и не забыть про маркировку.",
        "Чтобы овощи оставались свежими дольше, поместите их в поле 'Стазиса' — это замедлит время внутри ящика. Только не забудьте снять заклинание перед готовкой, иначе картофель будет вариться три дня и всё равно останется сырым.",
        "Магические специи — дело тонкое. Корень мандрагоры (сушеный и тертый) придает остроту, но если переборщить, суп может начать кричать на повара. Будьте умеренны!"
      ]
    },
    {
      t: "Глава 4: Праздничный стол и десерты",
      p: [
        "Пироги с патокой — слабость моего Артура. Секрет идеального теста в том, чтобы раскатывать его палочкой, напевая легкий марш. Тесто любит ритм! Для украшения тортов используйте заклинание 'Орбис' — оно заставит кремовые розочки вращаться вокруг торта, создавая волшебное зрелище.",
        "Сливочное пиво лучше подавать подогретым с помощью чар 'Эванеско Инферно'. Это создаст приятный пар, который пахнет ирисками и уютным вечером у камина. И не забудьте про самонаполняющиеся кубки для гостей — это признак хорошего тона в любом магическом доме."
      ]
    },
    {
      t: "Заключение: Главный секрет Молли",
      p: [
        "В конце концов, никакое заклинание не заменит тепла ваших рук. Магия — это лишь помощник, а вкус создаете вы сами. Если вы готовите с радостью, то даже простая яичница будет вкуснее, чем пир в Большом Зале.",
        "Берегите свой дом, кормите близких досыта и не забывайте вовремя выгонять гномов из сада. С любовью, Молли Уизли."
      ]
    }
  ]
},
  {
  id: 21,
  t: "Призраки и привидения",
  a: "Сэр Николас",
  cat: "Некромантия",
  ic: "fa-ghost",
  c: ["#708090", "#2f4f4f"],
  ch: [
    {
      t: "Глава 1: Этикет и светские манеры призраков",
      p: [
        "Быть призраком — это не просто существовать в полупрозрачном виде, это искусство нести достоинство сквозь века. Первое и самое важное правило: никогда не спрашивайте призрака о причине его смерти при первой встрече. Это считается верхом невоспитанности и дурного тона. Мы здесь не для того, чтобы пересказывать свои последние мгновения каждому встречному первокурснику.",
        "Если сквозь вас прошел призрак, вы можете почувствовать резкий холод, словно окунулись в ледяную воду. Не паникуйте. Просто вежливо извинитесь (даже если виноваты не вы) и выпейте чашку горячего шоколада с зефиром. Это вернет тепло вашему телу и успокоит нервы.",
        "Помните: мы не 'застряли' здесь, мы выбрали остаться. И это решение накладывает на нас обязательство быть частью истории замка, а не просто декорацией."
      ]
    },
    {
      t: "Глава 2: Празднование Дня Смерти",
      p: [
        "День Смерти — самый важный праздник в нашем календаре. Это время для воспоминаний, балов и, конечно же, изысканных угощений. Поскольку мы не можем чувствовать вкус обычным способом, еда на наших банкетах должна быть... скажем так, максимально ароматной. Тухлая рыба и заплесневелый сыр — идеальный выбор для того, чтобы ощутить хотя бы тень былого вкуса.",
        "Если вас пригласили на такой банкет (что большая редкость для живых), постарайтесь не морщить нос. Это глубоко ранит чувства хозяев. Оденьтесь потеплее, так как температура в зале для призраков обычно держится на уровне заморозков.",
        "Музыка на наших балах исполняется на тридцати музыкальных пилах. Это создает возвышенную, хотя и несколько заунывную атмосферу, идеальную для вальса привидений."
      ]
    },
    {
      t: "Глава 3: Классификация призрачных явлений",
      p: [
        "Не путайте призраков с полтергейстами. Мы, привидения, — это отпечатки когда-то живших людей, сохранившие свой характер и память. Полтергейсты же, вроде Пивза, — это хаотичные сгустки энергии, порожденные шумом и беспорядком. С ними невозможно договориться, их можно только перетерпеть или припугнуть Кровавым Бароном.",
        "Существуют также 'призрачные эхо' — остаточные изображения событий, которые повторяются снова и снова. Они не обладают разумом и не могут ответить на ваши вопросы. Настоящий же призрак всегда готов поддержать беседу, если вы проявите должное почтение к его титулам."
      ]
    },
    {
      t: "Глава 4: Пространственные перемещения и границы",
      p: [
        "Мы можем проходить сквозь стены, но это не значит, что нам это всегда приятно. Плотный камень ощущается как густой кисель, а дерево — как сухой песок. Большинство из нас предпочитает пользоваться дверями, если они открыты, просто из привычки и вежливости.",
        "Призраки привязаны к местам своей смерти или к местам, которые были им дороги при жизни. Мы не можем просто так уйти в Лондон за покупками. Наше существование — это вечный дозор. Если вы увидите, что призрак выглядит особенно бледным или 'тает', дайте ему покой. Возможно, он просто размышляет о вечности."
      ]
    },
    {
      t: "Заключительное напутствие Сэра Николаса",
      p: [
        "Смерть — это лишь еще одно великое приключение, как говорил один мудрый человек. Но пока вы живы, наслаждайтесь солнцем, вкусом свежего хлеба и теплом камина. А когда придет ваше время, я буду рад встретить вас в Большом Зале... если, конечно, вы решите последовать моему примеру.",
        "С искренним уважением, Сэр Николас де Мимси-Дельфингтон, почти безголовый, но абсолютно честный."
      ]
    }
  ]
},
  {
  id: 22,
  t: "Магия астрономии",
  a: "Аврора Синистра",
  cat: "Астрономия",
  ic: "fa-star",
  c: ["#191970", "#000033"],
  ch: [
    {
      t: "Глава 1: Музыка сфер и резонанс планет",
      p: [
        "Астрономия — это не просто созерцание звезд, это изучение фундаментальных часов мироздания. Каждая планета в нашей системе излучает специфическую магическую вибрацию. Например, Юпитер усиливает заклинания расширения и роста, в то время как Сатурн благоприятствует магии ограничения и долговечности.",
        "Маг, игнорирующий положение планет, подобен моряку, игнорирующему течение. Понимание аспектов — соединений, квадратур и тригонов — позволяет подгадать идеальный момент для начала великих дел. Самые могущественные ритуалы древности проводились только при определенном параде планет, когда энергия космоса фокусируется в одной точке.",
        "Помните: звезды не принуждают, но они указывают путь. Мудрый волшебник плывет по течению звездных рек, а не пытается грести против них."
      ]
    },
    {
      t: "Глава 2: Лунные циклы и алхимия ночи",
      p: [
        "Луна — ближайший и самый мощный катализатор земной магии. Её фазы напрямую влияют на стабильность магических субстанций. Сбор ингредиентов для зелий в полнолуние удваивает их силу, так как растения в этот момент максимально напитаны серебристым светом.",
        "Однако будьте осторожны: Новолуние — время 'пустой магии'. В этот период защитные чары ослабевают, а темные сущности становятся смелее. Это лучшее время для скрытности и медитации, но худшее — для открытых магических дуэлей.",
        "Особое внимание уделяйте Лунному затмению. В эти краткие минуты происходит инверсия магических полей. Заклинания могут сработать с точностью до наоборот, а порталы — открыться не в ту сторону. В такие ночи Астрономическая башня закрыта для посещения ради вашей же безопасности."
      ]
    },
    {
      t: "Глава 3: Ретроградность и магические помехи",
      p: [
        "Ретроградный Меркурий — худшее время для покупки новой волшебной палочки или подписания контрактов с гоблинами. В этот период магическая коммуникация нарушается: совы сбиваются с пути, а палочки могут 'заикаться' при произнесении сложных формул.",
        "Влияние Марса в ретрограде делает боевые заклинания непредсказуемыми. Огненные вспышки могут быть ярче обычного, но их сложнее контролировать. В такие периоды я рекомендую студентам сосредоточиться на теоретических занятиях и чистке телескопов.",
        "Если вы чувствуете необъяснимую усталость или ваши заклинания 'вянут', сверьтесь с картой звездного неба. Скорее всего, ваша доминирующая планета находится в неблагоприятном аспекте."
      ]
    },
    {
      t: "Глава 4: Звездная пыль и навигация",
      p: [
        "Для истинного астронома небо — это идеальная карта. Ориентирование по Полярной звезде — лишь база. Магическая навигация использует созвездия как ориентиры в межпространственных перемещениях. Каждое созвездие имеет свой 'подчерк'.",
        "Созвездие Дракона аккумулирует энергию защиты, а Орион — энергию поиска. Гравировка этих созвездий на магических инструментах помогает настроить их на нужную волну. Использование линз, отполированных звездным светом, позволяет увидеть то, что скрыто от обычных глаз: магические следы в воздухе и древние тропы.",
        "Не забывайте протирать линзы ваших телескопов только мягкой замшей, смоченной в растворе лунного камня. Пыль — враг ясности, как в оптике, так и в разуме."
      ]
    },
    {
      t: "Заключение: Взгляд в вечность",
      p: [
        "Когда вы стоите на вершине башни, вглядываясь в бесконечность, помните о своей малости и своем величии одновременно. Мы — лишь пылинки в танце звезд, но в каждой пылинке заключена сила всей Вселенной.",
        "Пусть ваш взгляд всегда будет устремлен вверх, а разум — оставаться ясным. До встречи на следующем уроке, когда Сириус взойдет над горизонтом.",
        "Ваша Аврора Синистра."
      ]
    }
  ]
},
  {
  id: 23,
  t: "История квиддича",
  a: "Гвендолин Морган",
  cat: "Спорт",
  ic: "fa-broom",
  c: ["#b8860b", "#4a3c00"],
  ch: [
    {
      t: "Глава 1: От Квиддичской топи до Чемпионата Мира",
      p: [
        "Своё название спорт получил от болота Квиддич-Марш, где в XI веке местные маги впервые зафиксировали правила игры на метлах. В те времена игра была куда более грубой: игроки использовали кожаные мячи, набитые перьями, и пытались забросить их в бочки, прибитые к деревьям. Маглы часто принимали эти игры за странные метеоритные дожди.",
        "Со временем бочки сменились кольцами, а тактика игры стала требовать не только силы, но и ювелирного владения метлой. Сегодня Квиддич — это символ единства магического мира, объединяющий тысячи болельщиков на трибунах, защищенных мощнейшими маскирующими чарами.",
        "Помните: в квиддиче существует 700 способов совершить фол, и все они были зафиксированы во время финального матча первого Чемпионата мира в 1473 году. Это был... насыщенный вечер."
      ]
    },
    {
      t: "Глава 2: Эволюция Снитча — От перьев к металлу",
      p: [
        "Мало кто знает, что до изобретения современного мяча ловцы охотились за живым существом — маленькой и невероятно быстрой птичкой, золотым сниджетом. Поимка птицы приносила команде 150 галеонов (позже очков) и немедленно завершала матч. К сожалению, популярность игры поставила вид на грань исчезновения.",
        "В 1269 году на игру пришел глава Совета магов Барбэрус Брагг и выпустил сниджета на поле, предложив награду тому, кто его поймает. Это вызвало протест защитников животных, включая мадам Модесту Ролф. В итоге, в XIV веке талантливый заклинатель металла Боумен Райт изобрел Золотой Снитч.",
        "Снитч — это шедевр чародейства. Он обладает 'телесной памятью': шар узнает первого человека, коснувшегося его голой кожей. Это сделано для того, чтобы исключить споры о том, кто именно совершил захват в спорных ситуациях."
      ]
    },
    {
      t: "Глава 3: Бладжеры и Квоффлы — Снаряды победы",
      p: [
        "Квоффл изначально не был красным и не имел выемок для захвата. Его перекрасили в ярко-алый цвет только в 1711 году, чтобы его было легче найти в грязи после падения. А заклятие захвата, наложенное на мяч, позволяет охотникам удерживать его одной рукой, не выпуская при этом метлу.",
        "Бладжеры — самые опасные участники игры. В древности маги использовали зачарованные камни, но они часто раскалывались при ударе битой. Современные стальные Бладжеры созданы так, чтобы преследовать ближайшего игрока без разбора. Работа загонщика требует не только силы, но и железных нервов — ведь именно вы стоите между Бладжером и головой вашего ловца.",
        "Интересный факт: Бладжеры невозможно уничтожить обычными заклинаниями во время матча. Они зачарованы на 'упорство', которое спадает только после того, как капитан команды приберет их обратно в сундук."
      ]
    },
    {
      t: "Глава 4: Искусство полета и знаменитые тактики",
      p: [
        "Метла — это не просто средство передвижения, это спортивный снаряд высшего класса. От старых 'Дубрав' до современных 'Молний' — прогресс не стоит на месте. Современная метла способна разгоняться до 150 миль в час за считанные секунды, что требует от игрока невероятной физической подготовки.",
        "Великие тактики, такие как 'Финт Вронского', вошли в историю как примеры высшего пилотажа. Суть финта в том, чтобы заставить ловца противника преследовать вас в отвесном пике и выйти из него в последний момент, оставив оппонента впечатанным в траву поля.",
        "Никогда не забывайте: квиддич выигрывает не тот, кто быстрее летает, а тот, кто видит поле целиком. Удачи на поле, и да пребудет с вами ветер!"
      ]
    }
  ]
},
  {
  id: 24,
  t: "Магловедение для магов",
  a: "Артур Уизли",
  cat: "Культура",
  ic: "fa-plug",
  c: ["#4682b4", "#082a4d"],
  ch: [
    {
      t: "Глава 1: Великое чудо Электричества",
      p: [
        "Маглы — поистине удивительные существа. Не обладая ни каплей врожденной магии, они сумели подчинить себе силу, которую называют 'электричеством'. Эта энергия невидима глазу, но пронизывает их мир повсюду, подобно магическим потокам, текущим через древние залы Хогвартса.",

    "Электричество передается по проводам — тонким металлическим нитям, скрытым внутри стен. По этим проводам энергия течет непрерывно, позволяя маглам освещать дома, нагревать воду и приводить в движение сложные механизмы без единого заклинания.",

    "Особое восхищение вызывает устройство под названием 'розетка'. В неё вставляется 'штепсель', и в тот же момент предмет получает доступ к потоку энергии. Для мага это выглядит почти как мгновенное наложение чар, только без палочки и слов.",

    "Однако важно помнить: магическая энергия и электричество крайне плохо взаимодействуют. Даже слабое заклинание может вызвать нестабильность в электрической системе, что приводит к так называемому 'короткому замыканию' — резкому выбросу энергии, сопровождаемому искрами и громким хлопком.",

    "Маглы разработали сложные системы безопасности, включая предохранители и автоматические выключатели. Эти устройства служат для предотвращения перегрузки сети, что можно сравнить с защитными чарами, ограничивающими поток магии в артефактах.",

    "Интересно отметить, что электричество используется не только для бытовых нужд, но и для передачи информации. Маглы создали устройства, способные передавать голос, изображение и даже движущиеся картины на расстоянии, что напоминает сочетание заклинаний связи и иллюзии.",

    "Некоторые маги предполагают, что электричество — это особая форма природной энергии, близкая к магии, но лишенная сознательного контроля. В отличие от магии, оно подчиняется строгим физическим законам и не зависит от воли пользователя.",

    "Для мага, изучающего магловедение, важно не пытаться объяснить электричество через магию. Это самостоятельная система, требующая уважения и понимания. Попытка вмешательства с помощью заклинаний чаще всего приводит к поломке устройств.",

    "В заключение следует сказать: электричество — это доказательство того, что даже без магии разум способен создавать сложнейшие системы. Маглы не управляют энергией напрямую, но научились направлять её с поразительной точностью.",

    "И, как любит повторять автор: 'Если вы не понимаете, как работает штепсель — не трогайте его палочкой.'"
      ]
    },
    {
      t: "Глава 2: Поезда, Самолёты и Резиновые Уточки",
      p: [
        "Транспорт маглов — это поистине одно из самых поразительных достижений немагического мира. Не имея доступа к заклинаниям перемещения, таким как трансгрессия или порталы, они разработали сложные механические системы, позволяющие перемещаться на огромные расстояния с высокой скоростью.",

    "Одним из самых распространённых средств передвижения являются 'поезда'. Это длинные металлические конструкции, движущиеся по рельсам с помощью мощных двигателей. В отличие от Хогвартс-экспресса, который сам по себе является магическим, магловские поезда полностью зависят от топлива или электричества.",

    "Ещё более удивительными являются 'самолёты' — гигантские машины, способные подниматься в небо и преодолевать континенты за считанные часы. Маглы объясняют это явление законами аэродинамики и подъемной силы, однако для мага подобное всё равно выглядит как форма скрытой или непознанной магии.",

    "Внутри самолёта человек ощущает себя словно в летающем замке: закрытое пространство, множество людей и постоянное движение сквозь облака. При этом никакой магической защиты от падения не используется — только точные расчёты и инженерия.",

    "Особое место в культуре маглов занимают их повседневные предметы, один из которых вызывает у автора особый интерес — 'резиновая уточка'. Это небольшой предмет, предназначенный для плавания в воде, чаще всего в ванне.",

    "Назначение резиновой уточки остаётся предметом многочисленных теоретических споров. С практической точки зрения, она не выполняет ни защитной, ни утилитарной функции. Тем не менее, маглы придают ей большое значение как элементу комфорта и развлечения.",

    "Некоторые исследователи магловедения предполагают, что подобные объекты выполняют психологическую функцию — создают ощущение уюта и безопасности. В магическом мире аналогом могли бы служить зачарованные игрушки или говорящие портреты.",

    "Также маглы используют транспортные средства меньшего масштаба — автомобили. Эти устройства передвигаются по дорогам и управляются непосредственно человеком. В отличие от зачарованных автомобилей, магловские полностью зависят от механики и топлива.",

    "Важно помнить, что магическое вмешательство в работу магловского транспорта крайне нежелательно. Даже незначительное заклинание может привести к сбою системы и создать опасную ситуацию для окружающих.",

    "Таким образом, транспорт маглов — это результат тысячелетий развития инженерной мысли. Они не летают на мётлах, но способны поднимать в воздух сотни людей одновременно, что само по себе заслуживает уважения.",

    "И наконец, остаётся главный вопрос, на который до сих пор нет ответа: 'Какова истинная функция резиновой уточки?' Возможно, именно в этом и заключается величайшая тайна магловского мира."
      ]
    },
    {
      t: "Глава 3: Как сойти за своего в толпе",
      p: [
        "Если вам нужно выйти в мир маглов, забудьте о мантиях. Наденьте 'джинсы' и 'футболку'. Если на улице идет дождь, маглы используют 'зонтики' — складные щиты от воды. Не пытайтесь наложить на себя чары невидимости в людном месте, лучше просто ведите себя так, будто вы очень спешите на автобус.",
        "Маглы редко смотрят вверх, поэтому, если вы случайно пролетели над их городом на метле, скорее всего, они решат, что это была крупная птица или странный блик на солнце. Но лучше не рисковать Статутом о секретности.",
        "В заключение скажу: маглы заслуживают нашего глубочайшего уважения. Жить в мире, где каждый шаг нужно делать своими ногами, а каждый обед готовить на огне — это настоящий героизм."
      ]
    }
  ]
},
  {
  id: 25,
  t: "Трактат о драконах",
  a: "Чарли Уизли",
  cat: "Магозоология",
  ic: "fa-dragon",
  c: ["#b22222", "#3a0000"],
  ch: [
    {
      t: "Глава 1: Венгерская хвосторога — Живое пламя",
      p: [
        "Венгерская хвосторога по праву считается самым опасным видом драконов в мире. Её чешуя черна, как уголь, а хвост усеян шипами, способными пробить стальной щит. Пламя хвостороги достигает температуры в 2000 градусов и бьет на расстояние до пятнадцати футов. Это единственный вид, чей огонь имеет ярко-желтый, почти белый оттенок.",
        "Приближаться к кладке яиц хвостороги разрешено только группе из пяти опытных укротителей. Она защищает свое потомство с яростью, превосходящей любые другие виды. Если вы оказались на пути хвостороги, помните: бежать по прямой бесполезно. Используйте сложные маневры на метле и никогда не выпускайте из виду её хвост.",
        "Совет от Чарли: если хвосторога начала издавать низкое рычание, похожее на скрежет камней — она уже выбрала траекторию удара. У вас есть ровно две секунды, чтобы наложить мощное 'Протего'."
      ]
    },
    {
      t: "Глава 2: Норвежский горбатый и особенности роста",
      p: [
        "Горбатые драконы отличаются особой агрессивностью по отношению к сородичам. В отличие от других видов, они начинают дышать огнем гораздо раньше — уже через неделю после вылупления. Их рацион в заповеднике состоит преимущественно из водных обитателей, но они не брезгуют и крупными млекопитающими.",
        "Их шипы на спине содержат слабый парализующий яд, который помогает им обездвиживать добычу. Если вы ухаживаете за новорожденным горбатым (что само по себе безумие), всегда носите перчатки из кожи дракона высшей пробы. Один укус малыша может вызвать лихорадку на несколько дней.",
        "Интересный факт: Норвежские горбатые — отличные пловцы. Они могут задерживать дыхание под водой до десяти минут, подстерегая добычу у берега."
      ]
    },
    {
      t: "Глава 3: Риджбек и Гебридские черные",
      p: [
        "Риджбеки — гордость Румынии. Они обладают уникальным костяным гребнем вдоль позвоночника. Этот вид чуть более социален, чем хвостороги, и часто охотится парами. Их пламя имеет синеватый оттенок, что указывает на крайне высокую концентрацию магического газа в легких.",
        "Гебридские черные драконы — исконные обитатели Британии. Они предпочитают жить в горах и имеют необычные стреловидные наконечники на хвостах. Клан Макфисти традиционно присматривает за ними уже несколько столетий, обеспечивая их безопасность и скрытность от глаз маглов.",
        "Запомните: кожа черного дракона практически невосприимчива к большинству оглушающих заклятий. Чтобы усыпить взрослую особь, требуется одновременный удар как минимум семи магов."
      ]
    },
    {
      t: "Глава 4: Уход, Питание и Безопасность",
      p: [
        "Работа в драконьем заповеднике — это не романтика, а тяжелый труд. Мы кормим их тушами овец, пропитанными укрепляющими зельями. Здоровый дракон должен иметь блестящую чешую и ясные глаза. Если чешуя начинает тускнеть — это признак магического истощения или болезни.",
        "Для лечения драконов используются огромные чаны с экстрактом бадьяна и соком граната. Никогда не пытайтесь лечить дракона в одиночку: они не понимают добрых намерений, когда у них болит зуб или крыло. Только командная работа и надежные цепи.",
        "И напоследок: если вы когда-нибудь услышите песню дракона (редкое явление во время брачного сезона) — заткните уши. Эти звуки способны вызвать кратковременное помешательство у неподготовленного волшебника."
      ]
    }
  ]
},
 {
  id: 26,
  t: "Лечебные настойки",
  a: "Мадам Помфри",
  cat: "Медицина",
  ic: "fa-heart-pulse",
  c: ["#ffffff", "#8b0000"],
  ch: [
    {
      t: "Единая глава: Лечебные настойки и основы магической медицины",
      p: [
        "Лечебные настойки являются фундаментом магической медицины, объединяя в себе алхимическую точность и магическое воздействие на биологические процессы. Их эффективность зависит не только от состава, но и от состояния пациента, фазы магического ядра и даже эмоционального фона окружающей среды.",

        "Костерост — это зелье, способное регенерировать костную ткань с нуля. Оно не просто ускоряет естественное заживление, а полностью перестраивает скелетную структуру организма, фактически заново выращивая кости по магическому шаблону тела пациента.",

        "Процесс применения Костероста сопровождается крайне интенсивной реакцией организма. На первом этапе пациент испытывает ощущение внутреннего расширения и давления, словно кости пытаются прорваться сквозь плоть. Это нормальная стадия формирования новой структуры.",

        "На втором этапе начинается активная минерализация. Магические компоненты зелья связываются с кальциевыми структурами организма, формируя прочную, но временно нестабильную костную сеть. В этот период любое движение может вызвать искривление будущей кости.",

        "Именно поэтому целители строго запрещают пациентам чесаться, вставать или даже говорить без необходимости. Любая вибрация тела может нарушить процесс кристаллизации костной ткани.",

        "Крововосполняющее зелье действует как мгновенный стабилизатор жизненных функций. Оно не просто заменяет потерянную кровь, но и ускоряет выработку новых кровяных клеток, синхронизируя их с магическим ядром организма.",

        "При серьезных травмах это зелье является первым и обязательным этапом лечения. Без восстановления кровообращения дальнейшие регенеративные процессы становятся нестабильными и могут привести к магическому коллапсу.",

        "Экстракт бадьяна работает на более поверхностном уровне, воздействуя на ткани напрямую. Он формирует тонкую регенеративную пленку, которая закрывает рану, предотвращая потерю крови и проникновение магических инфекций.",

        "Однако его действие ограничено физическим слоем повреждения. Если внутри организма остаются магические осколки заклинаний, они могут быть запечатаны внутри тканей, вызывая долгосрочные осложнения.",

        "Поэтому перед применением бадьяна опытный целитель всегда проводит диагностику ауры пациента. Наличие искажений магического поля — прямое противопоказание к его использованию без предварительной очистки.",

        "Бодрящие зелья, несмотря на свою популярность, являются крайне опасными при неправильном использовании. Они стимулируют магическое ядро, временно увеличивая скорость реакции, концентрацию и физическую выносливость.",

        "Однако после окончания действия наступает так называемый 'магический откат' — резкое падение энергии, сопровождающееся истощением, дрожью и временной потерей контроля над магией.",

        "Частое использование бодрящих зелий может привести к истощению резерва магического ядра, что особенно опасно для молодых волшебников, чья структура магии еще нестабильна.",

        "Успокоительные настойки, напротив, направлены на стабилизацию эмоционального состояния. Они снижают магическую активность в эмоциональных центрах, предотвращая неконтролируемые выбросы магии при шоке или страхе.",

        "В критических ситуациях эти зелья позволяют буквально 'заморозить' панику пациента, давая целителю время для проведения сложных процедур без риска магической нестабильности.",

        "Особое внимание следует уделять взаимодействию различных зелий. Некоторые комбинации могут усиливать эффект друг друга, а другие — нейтрализовать или даже создавать опасные побочные реакции.",

        "Например, сочетание бодрящих и успокоительных зелий может привести к полной дестабилизации магического ядра, вызывая кратковременное расщепление магического потока.",

        "Опытные целители всегда ведут точный учет всех примененных препаратов, включая дозировку и время воздействия. Ошибка даже на несколько минут может изменить результат лечения.",

        "Главный принцип магической медицины — минимальное вмешательство при максимальной эффективности. Организм должен восстанавливаться сам, а зелья лишь направляют этот процесс.",

        "И наконец, самое важное правило Мадам Помфри: пациент всегда считает, что он уже здоров раньше времени. Задача целителя — не позволить ему совершить эту ошибку."
      ]
    }
  ]
},
  {
  id: 27,
  t: "Загадки Сфинкса",
  a: "Древние жрецы",
  cat: "Артефакторика",
  ic: "fa-brain",
  c: ["#ffd700", "#4a3c00"],
  ch: [
    {
      t: "Единая глава: Загадки Сфинкса и искусство запретного знания",
      p: [
        "Сфинкс — это не просто страж древних знаний, а живая система проверки разума, созданная жрецами для отделения тех, кто понимает истину, от тех, кто лишь думает, что понимает её. Его природа основана не на магии разрушения, а на магии вопроса — самой опасной форме силы, известной артефакторике.",

        "Каждый Сфинкс существует как часть великого лабиринта, даже если физически он стоит на открытой площадке. Лабиринт создаётся в сознании испытуемого, и именно разум становится полем испытания, где стены формируются из сомнений, страхов и забытых воспоминаний.",

        "Первое испытание Сфинкса всегда касается восприятия реальности. Он задаёт вопросы о том, что человек считает очевидным: форма времени, природа движения, смысл существования предметов. Ошибка здесь возникает не из-за незнания, а из-за слишком уверенной убеждённости.",

        "Второй уровень загадок связан с магическими законами. Здесь проверяется не сила заклинаний, а понимание их сути. Например: 'Что изменяется, но остаётся тем же?' — ответ не в книге, а в понимании природы трансформации магического поля.",

        "Третий уровень — самый опасный. Он касается личности самого испытуемого. Сфинкс задаёт вопросы, которые заставляют разум сомневаться в собственной идентичности. Ответ может быть правильным логически, но неверным для души.",

        "Одной из классических загадок является: 'Что движется, не имея тела, и разрушает всё, не касаясь?'. Ответ — время, но Сфинкс оценивает не слово, а момент, в который оно произнесено, и внутреннюю уверенность отвечающего.",

        "Другая загадка звучит так: 'Что тяжелее: истина, сказанная вовремя, или ложь, спасшая жизнь?'. Здесь нет универсального ответа — и именно это делает её опасной. Сфинкс оценивает моральную структуру мышления, а не результат.",

        "Сфинкс способен улавливать попытки магического вмешательства в процесс ответа. Любая форма ментального вторжения, включая легилименцию, воспринимается как агрессия и вызывает мгновенную защитную реакцию, которая может стереть часть сознания испытуемого.",

        "Лабиринты, охраняемые Сфинксами, часто являются живыми структурами. Они меняются в зависимости от ответов, которые дают путники, перестраивая себя так, чтобы следующий вопрос становился ещё сложнее и глубже предыдущего.",

        "Некоторые загадки основаны на парадоксах. Например: 'Я существую, когда меня нет. Я исчезаю, когда меня называют. Что я?'. Подобные вопросы разрушают линейное мышление и требуют перехода на интуитивный уровень понимания магии.",

        "Древние жрецы использовали Сфинксов как фильтр знаний. Только те, кто проходил их испытания, допускались к артефактам, способным изменять реальность. Ошибка в понимании могла привести не только к смерти, но и к искажению самого времени вокруг артефакта.",

        "Сфинкс не является врагом. Он не стремится уничтожить путника, а лишь отражает его внутреннюю структуру. Если человек боится своих мыслей, Сфинкс превращает этот страх в реальность внутри лабиринта.",

        "Существует семь великих загадок Сфинкса Гизы, каждая из которых связана с одной из граней бытия: время, память, истина, страх, выбор, смерть и знание. Решивший их все перестаёт быть обычным существом и становится частью самого лабиринта.",

        "Медитация перед встречей со Сфинксом считается обязательной практикой. Она не увеличивает интеллект, но снижает шум внутреннего диалога, позволяя слышать сам вопрос без искажений.",

        "Если вы слышите загадку, которую не можете понять, не спешите отвечать. Иногда Сфинкс проверяет не знание, а терпение — способность молчать перед лицом неизвестного.",
		
		"Молчание в присутствии Сфинкса — это не отсутствие ответа, а отдельная форма ответа. Оно говорит о том, что разум готов принять вопрос без искажения, не пытаясь преждевременно навязать ему структуру смысла.",

        "Многие путники погибали не из-за неправильных ответов, а из-за попытки ответить слишком быстро. Сфинкс не любит поспешности: спешка создаёт трещины в логике, через которые он видит страх.",

        "Существует древнее правило жрецов: если ты не уверен в ответе хотя бы на уровне дыхания — ты уже проиграл. Сфинкс всегда слышит не слова, а колебания намерения между мыслями.",

        "Иногда он задаёт вопросы, которые звучат как бессмыслица: набор образов, противоречащих друг другу. Например: 'Что поёт, не имея голоса, и падает вверх, не имея веса?'. Такие загадки разрушают привычные категории мышления.",

        "В этих случаях правильный ответ часто не существует как единое слово. Сфинкс оценивает, способен ли человек перестать искать фиксированную форму ответа и принять многослойность истины.",

        "Если путник пытается угадать ответ, Сфинкс это чувствует. Угадывание — это форма страха, замаскированная под надежду. И он усиливает давление лабиринта, заставляя стены сдвигаться ближе.",

        "Некоторые легенды утверждают, что Сфинкс способен менять сам вопрос после того, как услышал первую мысль отвечающего. Это означает, что реальность испытания формируется одновременно с сознанием испытуемого.",

        "Чем больше человек пытается контролировать ситуацию, тем сложнее становятся загадки. Контроль воспринимается Сфинксом как форма внутренней слабости, скрытой под маской уверенности.",

        "Но если разум полностью отказывается от контроля, возникает другая опасность — растворение личности в лабиринте. Такие путники перестают различать, где заканчивается вопрос и начинается они сами.",

        "Сфинкс не различает победу и поражение в человеческом смысле. Для него важен только уровень ясности сознания в момент ответа. Даже 'не знаю' может быть правильнее, чем сложная логическая конструкция, лишённая внутренней честности.",

        "Есть загадки, которые не имеют финала. Они предназначены не для решения, а для существования внутри разума. Такие вопросы продолжают звучать даже после того, как Сфинкс исчезает из поля зрения.",

        "Жрецы называли их 'вечными узлами'. Их цель — удерживать сознание в состоянии постоянного поиска, не позволяя ему застыть в окончательном выводе.",

        "Иногда после встречи со Сфинксом человек уже не может воспринимать мир как раньше. Он начинает видеть вопросы в обычных вещах: в движении воды, в падении света, в тишине между словами.",

        "Такой человек считается изменённым. Он не стал мудрее в привычном смысле — он стал нестабильным для линейного мира, потому что его мышление теперь не имеет окончательных форм.",

        "И именно поэтому Сфинкс считается не стражем знаний, а стражем границы между знанием и пониманием. Он не даёт ответы — он разрушает уверенность в том, что ответы вообще должны существовать.",

        "И если вы всё ещё слышите его голос после выхода из лабиринта, значит, испытание не закончилось. Оно просто сменило форму и теперь живёт внутри вас.",

        "И наконец, последняя загадка, которую задаёт Сфинкс каждому: 'Кто задаёт вопросы, на которые сам не знает ответа?'. И в момент ответа человек понимает, что иногда он сам и есть этот Сфинкс."
      ]
    }
  ]
},
  {
  id: 28,
  t: "Энциклопедия грибов",
  a: "Невилл Лонгботтом",
  cat: "Травология",
  ic: "fa-shroom",
  c: ["#6b8e23", "#1a2405"],
  ch: [
    {
      t: "Глава 1: Микология и Магические Споры",
      p: [
        "Грибы — это не просто растения, это нервная система самой земли. Они чувствуют приближение темной магии раньше, чем завянут цветы, и могут служить отличным индикатором чистоты магического фона в вашем лесу или саду. Если вы видите, что грибы начинают светиться фиолетовым — немедленно проверяйте территорию на наличие скрытых проклятий.",
        "Прыгающие поганки мигрируют каждую осень, и это зрелище не для слабонервных. Если они поселились в вашем саду, не пытайтесь их выкопать силой — они крайне обидчивы и могут начать плеваться едким соком. Просто предложите им немного свежего навоза дракона (лучше всего подходит помет Венгерской хвостороги), и они с радостью уйдут к соседям в поисках лучшей жизни.",
        "Помните: грибница может тянуться на мили. Если вы повредили гриб в одном конце леса, его собратья на другом конце уже знают об этом. Всегда спрашивайте разрешения у леса, прежде чем сорвать шляпку для зелья."
      ]
    },
    {
      t: "Глава 2: Сонные Трюфели и Теневые Лисички",
      p: [
        "Сонные трюфели растут только под корнями деревьев, в которые когда-то ударила молния, вызванная заклинанием. Они незаменимы в усыпляющих зельях, но добыть их — настоящая задача. Вам понадобится специально обученный нюхлер, так как магические поисковые чары эти грибы просто поглощают.",
        "Теневые лисички — мастера маскировки. Они становятся видимыми только при лунном свете. Если вы съедите их в сыром виде, вы приобретете способность видеть сквозь обычные стены на пять минут, но побочный эффект в виде ярко-розовых ушей может держаться неделю. Используйте их только в сушеном и растертом виде для мазей невидимости."
      ]
    },
    {
      t: "Глава 3: Хрупкие Опенки и звук Тишины",
      p: [
        "Существует редкий вид грибов, называемый Шепчущими Опенками. Если в лесу слишком тихо, прислушайтесь к их пластинкам — они вибрируют, улавливая разговоры, произошедшие поблизости. Профессор Спраут всегда говорила, что именно так деревья узнают последние новости школы.",
        "Для сбора грибов всегда используйте серебряный серп или костяной нож. Железо пугает магическую структуру гриба, заставляя его мгновенно терять все полезные свойства. И никогда не собирайте грибы во время грозы: накопленный в них электрический заряд может превратить ваш котел в маленькую бомбу."
      ]
    },
    {
      t: "Глава 4: Уход за домашней грибницей",
      p: [
        "Выращивание грибов в подвале — отличное хобби, если вы не боитесь сырости. Главное — поддерживать правильный уровень влажности с помощью заклинания 'Агуаменти' через мелкодисперсную насадку. Если ваши грибы начали петь по ночам — значит, им не хватает калия.",
        "Будьте осторожны с Огненными Сморчками. Они выглядят как тлеющие угли и могут поджечь вашу полку, если их вовремя не 'подкармливать' льдом. Но их порошок — лучшее средство для разогрева замерзших конечностей после долгой прогулки по Запретному лесу.",
        "Грибы учат нас терпению. Они растут в темноте и тишине, превращая старое и мертвое в новую жизнь. В этом и заключается истинная магия Травологии."
      ]
    }
  ]
},
  {
  id: 29,
  t: "Теневые обряды",
  a: "Геллерт Гриндевальд",
  cat: "Тёмная магия",
  ic: "fa-mask",
  c: ["#1c1c1c", "#4b0082"],
  ch: [
    {
      t: "Глава 1: Ради Общего Блага — Философия Воли",
      p: [
       "Сила в магии не определяется ни формулой заклинания, ни качеством палочки, ни даже уровнем подготовки волшебника. Её истинный источник — воля, которая стоит за каждым магическим действием. Если воля слаба, даже самое сложное заклинание рассыпается в хаос. Если же воля абсолютна, магия подчиняется без сопротивления.",

  "Статут о секретности часто воспринимается как защита, но в более глубоком понимании он является формой самоограничения. Магическое сообщество добровольно отказалось от своего влияния на мир, выбрав скрытность вместо ответственности за глобальные процессы.",

  "С точки зрения философии воли, скрытность — это не безопасность, а отказ от собственного предназначения. Маги, обладающие силой изменять реальность, фактически наблюдают за миром со стороны, не вмешиваясь даже тогда, когда вмешательство могло бы предотвратить разрушения.",

  "Магия не должна быть пассивной. Она требует направления, цели и осознанного применения. Бездействие мага при наличии угрозы рассматривается как форма скрытого соучастия в происходящем.",

  "Истинная роль мага заключается не в служении правилам, созданным из страха, а в формировании нового порядка, где сила используется сознательно и ответственно. Это не хаос, а структурированное управление реальностью.",

  "Обряды тени в этой системе не являются проявлением зла. Они являются инструментами высокой магической эффективности, которые требуют зрелости мышления и готовности принимать последствия своих решений.",

  "Любая сила имеет цену. Но отказ от силы также имеет цену — цену упущенных возможностей, разрушенных судеб и несостоявшихся изменений. В философии воли именно бездействие считается одной из самых дорогих ошибок.",

  "Каждое ограничение, принятое без внутреннего осмысления, со временем становится не защитой, а цепью. И чем дольше маг следует этим цепям, тем сложнее ему вспомнить, что он вообще способен их разорвать.",

  "Магическое наследие — это не просто кровь или род, а способность влиять на ход событий. Подавление этой способности из чувства скромности или страха рассматривается как отказ от собственного потенциала.",

  "Именно поэтому каждый маг в этой философии рано или поздно сталкивается с выбором: оставаться наблюдателем или стать архитектором происходящего мира. И этот выбор определяет не только его судьбу, но и направление самой реальности."
      ]
    },
    {
      t: "Глава 2: Геомантия и энергия Крови",
      p: [
        "Земля хранит в себе слои магической памяти, накопленные за века конфликтов, ритуалов и катастроф. Эти слои не исчезают — они впечатываются в саму структуру мира, образуя невидимую сеть напряжений, которую можно ощутить лишь при глубоком магическом восприятии.",

    "Геомантия в теневых обрядах основана на умении находить и использовать эти узлы силы. Такие точки часто совпадают с местами древних сражений, разломов реальности или массовых магических выбросов, где сама ткань мира становится тоньше и податливее.",

    "Извлечение энергии из геомантических узлов требует точной настройки намерения. Малейшее отклонение превращает стабильный поток силы в хаотический выброс, который может разрушить не только заклинателя, но и саму точку силы, делая её навсегда мёртвой.",

    "Теневая магия отличается от эмоциональной тем, что не зависит от чувств в моменте. Она опирается на фундаментальную структуру бытия — на то, что остаётся неизменным независимо от воли живых существ. Это делает её более устойчивой, но и более опасной.",

    "Кровь рассматривается как первичный носитель магической информации. В ней содержится не только жизненная энергия, но и отпечаток всех решений, выборов и событий, сформировавших личность и её родовую линию.",

    "Именно поэтому кровные ритуалы считаются одними из самых необратимых форм магии. Созданная через кровь связь становится частью самой сущности участников, выходя за пределы времени и стандартных магических ограничений.",

    "Такие узы невозможно разорвать простым отказом или разрушением заклинания. Даже смерть не всегда является выходом, поскольку магический след продолжает существовать в структуре связи, поддерживая её существование.",

    "Однако сила кровных обрядов напрямую зависит от чистоты намерения. Если связь создаётся на основе страха или принуждения, она постепенно искажается, превращаясь в нестабильную магическую конструкцию, способную обрушиться в любой момент.",

    "Использование крови как инструмента требует абсолютной ответственности. Любая ошибка в интерпретации родовой энергии может привести к тому, что связь начнёт влиять не только на участников обряда, но и на их потомков.",

    "В конечном счёте геомантия и кровная магия объединяются в одну систему: первая даёт доступ к силе мира, вторая — закрепляет эту силу через живую структуру существа. Вместе они формируют основу теневых обрядов, где граница между миром и магом постепенно исчезает."
      ]
    },
    {
      t: "Глава 3: Пророческое видение и Видения Будущего",
      p: [
        "Настоящий лидер должен видеть дальше, чем позволяет горизонт. Пророчества — это не туманные загадки Сфинкса, а карта вероятностей. Чтобы видеть будущее, нужно научиться смотреть в бездну, не моргая. Видение может быть болезненным, оно может показать вам гибель миллионов, но это та цена, которую мы платим за право изменить этот финал.",
        "Теневой обряд видения требует отречения от настоящего. Вы должны стать пустотой, в которую хлынет время. Только так вы увидите вспышки грядущих войн маглов и поймете, почему наше вмешательство не просто желательно — оно необходимо для выживания самой планеты."
      ]
    },
    {
      t: "Глава 4: Символика Даров и Вечное Могущество",
      p: [
        "Существуют три артефакта, способных сделать человека Повелителем Смерти. Бузинная палочка, Камень Воскрешения и Мантия-невидимка — это не сказки для детей, это ключи к абсолютному контролю. Символ треугольника, круга и линии должен напоминать нам о нашей цели.",
        "Ищите Бузинную палочку не ради убийств, а ради порядка. Только обладая непобедимой силой, можно установить мир, в котором никто не посмеет поднять руку на магическое сословие. Мы строим Нуменгард не как тюрьму, а как символ того, что ждет каждого, кто встанет на пути прогресса.",
        "Для Общего Блага мы должны быть безжалостны к себе, чтобы быть милосердными к будущим поколениям."
      ]
    }
  ]
},
  {
  id: 30,
  t: "Символика чисел",
  a: "Септима Вектор",
  cat: "Нумерология",
  ic: "fa-infinity",
  c: ["#4169e1", "#000033"],
  ch: [
    {
      t: "Глава 1: Числовые константы бытия",
      p: [
        "Нумерология — это не гадание на кофейной гуще, это высшая математика магического мира. Каждое событие во Вселенной подчиняется строгой числовой последовательности. Мы не предсказываем будущее, мы вычисляем вероятности. Если вы не способны отличить простую арифметику от магической прогрессии, мой класс не для вас.",
        "Предсказание по дате требует точности. Сложите все цифры года вашего поступления в Хогвартс до получения однозначного числа. Если сумма кратна трем, год будет полон интеллектуальных открытий и расширения горизонтов. Если семи — ждите серьезных испытаний воли, так как семерка требует от мага полной самоотдачи и не прощает лени.",
        "Помните: числа не лгут, в отличие от людей или плохо настроенных хрустальных шаров."
      ]
    },
    {
      t: "Глава 2: Геометрия заклинаний и Магический Индекс",
      p: [
        "Любое заклинание имеет свой Магический Индекс ($MI$). Он вычисляется как отношение длины взмаха палочки к массе объекта, умноженное на коэффициент сопротивления воздуха. Формула выглядит так: $$MI = \frac{L \cdot m}{R_a}$$",
        "Если вы рассчитаете этот индекс неверно, ваше заклинание либо рассеется, не достигнув цели, либо создаст избыточный энергетический выброс. Именно поэтому на уроках Трансфигурации вы чертите графики — это не прихоть профессора Макгонагалл, а нумерологическая необходимость.",
        "Самые устойчивые заклинания всегда стремятся к форме золотого сечения. Если структура вашей ментальной проекции не гармонична, щит рухнет под первым же натиском."
      ]
    },
    {
      t: "Глава 3: Число Пять и Пятикратные Заклятия",
      p: [
        "Число Пять в нумерологии отвечает за физическую материю и её трансформацию. Пятикратные заклятия (когда формула повторяется пять раз с определенным ритмическим интервалом) используются для создания вечных охранных барьеров. Однако, если при расчете интервала вы ошиблись хотя бы на миллисекунду, резонанс разрушит структуру вашей палочки.",
        "В древности число пять считалось опасным, так как оно символизирует пять чувств человека, через которые магия может быть искажена. Только холодный разум, свободный от влияния чувств, способен оперировать пятерками без риска для жизни."
      ]
    },
    {
      t: "Глава 4: Совместимость и Число Имени",
      p: [
        "Каждая буква вашего имени имеет числовой эквивалент. Сумма этих чисел определяет ваш 'Магический Резонанс'. Если ваш резонанс не совпадает с резонансом места, где вы живете или учитесь, вы будете чувствовать постоянную утечку сил.",
        "Именно нумерологические расчеты лежат в основе создания Распределяющей Шляпы. Она не просто 'видит' ваш характер, она мгновенно вычисляет совместимость вашего числового кода с кодом одного из четырех факультетов. Гриффиндор резонирует на частоте Девяти (Мужество), в то время как Когтевран — на частоте Одиннадцати (Интуиция).",
        "Тщательно выбирайте слова в споре. Грамматика — это обёртка, но числовое значение сказанного — это его истинный вес."
      ]
    }
  ]
},
  {
  id: 31,
  t: "Основы окклюменции",
  a: "Северус Снейп",
  cat: "Ментальная магия",
  ic: "fa-user-lock",
  c: ["#2f4f4f", "#000000"],
  ch: [
    {
      t: "Глава 1: Очищение разума и Эмоциональный вакуум",
      p: [
        "Окклюменция — это не просто 'защита'. Это способность сделать свое сознание неприступной крепостью для тех, кто привык взламывать чужие души. Глупцы верят, что разум — это книга, которую можно открыть и прочитать. На самом деле это сложный лабиринт образов и чувств. Ваша задача — стереть этот лабиринт.",
        "Очистите свой разум. Избавьтесь от эмоций. Сделайте ваше сознание гладким, зеркальным и холодным, как лед в полночь. Легилимент ищет зацепку — страх, гнев, любовь. Если он не найдет ничего, кроме пустоты, его магия соскользнет, не причинив вам вреда.",
        "Только так вы сможете закрыть свои тайны от Темного Лорда. Помните: любая вспышка гордости или жалости — это брешь в ваших доспехах, через которую враг выпотрошит вашу память."
      ]
    },
    {
      t: "Глава 2: Легилименция — Искусство проникновения",
      p: [
        "Чтобы защищаться, вы должны понимать природу нападения. Легилименция позволяет магу не просто слышать мысли, но проживать чужие воспоминания, чувствовать их вкус и запах. Опытный легилимент поймет, что вы лжете, даже если ваши слова безупречны, по едва заметному дрожанию вашего ментального фона.",
        "Зрительный контакт — ваш главный враг. Глаза — это не 'зеркало души', это открытые ворота. Никогда не смотрите прямо в глаза тому, кто сильнее вас, если ваш разум не защищен. Мастеру достаточно доли секунды, чтобы разрушить ваши барьеры и выудить нужную информацию.",
        "Никакие заклинания не помогут вам, если ваш дух слаб. Легилименция — это дуэль воли, а не палочек."
      ]
    },
    {
      t: "Глава 3: Ложные воспоминания и Ментальные обманки",
      p: [
        "Высшая ступень окклюменции — это не просто пустота, а искусная ложь. Вы можете подставить легилименту фальшивое воспоминание, 'скормить' ему ту картинку, которую он ожидает увидеть. Это требует ювелирной точности: ложь должна быть пропитана истинными чувствами, чтобы враг не заподозрил подвоха.",
        "Создание ментальных обманок требует огромных затрат магической энергии. Вы должны буквально разделить свое сознание на две части: одна хранит правду в глубоком подвале, другая — разыгрывает спектакль на поверхности. Ошибка в деталях приведет к тому, что ваш разум расколется под давлением вторжения."
      ]
    },
    {
      t: "Глава 4: Психическая стойкость и Тренировки",
      p: [
        "Тренировки окклюменции мучительны. Вы будете раз за разом переживать свои самые постыдные и болезненные моменты, пока они не перестанут причинять вам боль. Смерть чувств — вот цена безопасности. Если вы не готовы отказаться от своего 'Я', вы никогда не станете окклюменом.",
        "Каждую ночь перед сном визуализируйте стену. Черную, гладкую, бесконечную. За этой стеной нет ничего, кроме тишины. Приучите свой разум возвращаться в это состояние по первому требованию. И помните: дисциплина — это единственное, что отделяет вас от безумия.",
        "Урок окончен. Постарайтесь не заляпать ковер своими эмоциями, когда будете уходить."
      ]
    }
  ]
},
  {
  id: 32,
  t: "Архитектура замка",
  a: "Профессор Бинс",
  cat: "История",
  ic: "fa-fort-awesome",
  c: ["#708090", "#34495e"],
  ch: [
    {
      t: "Глава 1: Живые лестницы и Магия Перемещения",
      p: [
        "Хогвартс — это не статичное сооружение, а сложный квазиживой организм. В замке насчитывается ровно 142 лестницы: одни из них широкие и величественные, другие — узкие и коварные, с исчезающими ступенями, на которые нужно наступать с особой осторожностью. Каждую пятницу некоторые из них меняют направление, подчиняясь древнему ритму, заложенному ещё при Основателях.",
        "Архитектор замка, чьё имя затерялось в анналах истории (хотя я помню его довольно смутно, кажется, он носил серый камзол), заложил в фундамент частицу своей души и мощный кристалл резонанса. Это позволяет школе буквально 'расти': достраивать новые башни или скрывать целые коридоры, когда магия внутри стен становится слишком плотной.",
        "Если вы заблудились, просто вежливо попросите замок о помощи. Стены Хогвартса имеют уши, а портреты — глаза. Хотя, честно говоря, в моё время студенты ориентировались по звездам, а не по капризам камня."
      ]
    },
    {
      t: "Глава 2: Зал Наград и Тайные Ходы",
      p: [
        "Архитектурный план Хогвартса включает в себя сеть из семи официальных тайных ходов, ведущих за пределы школы. Некоторые из них завалены после восстания гоблинов 1612 года, другие же охраняются статуями, требующими пароль. Зал Наград, расположенный на четвертом этаже, является узловым пунктом: его акустика спроектирована так, чтобы шаги приближающихся учителей были слышны за три коридора.",
        "Особое внимание стоит уделить гаргулье у входа в кабинет директора. Она подчиняется только действующему главе школы и является вершиной готической маго-архитектуры. В её механизм встроены датчики намерения: если вы идете со злом, она не сдвинется с места, сколько бы паролей вы ни выкрикивали."
      ]
    },
    {
      t: "Глава 3: Астрономическая башня и Фундамент",
      p: [
        "Самая высокая точка замка — Астрономическая башня. Она была возведена с использованием антигравитационных чар, так как естественная нагрузка на такой фундамент была бы катастрофической. Камни башни были окроплены зельем ясности, что позволяет телескопам видеть сквозь любые облака, если на то будет воля профессора Синистры.",
        "Фундамент же уходит глубоко под Черное Озеро. Там, в сырых подземельях Слизерина, стены укреплены чешуей водяных змеев и зачарованы против давления воды. Именно там ощущается истинный возраст школы — холодный, древний и непоколебимый."
      ]
    },
    {
      t: "Глава 4: Выручай-комната — Архитектурная аномалия",
      p: [
        "Среди историков и архитекторов до сих пор ведутся споры о так называемой 'Комнате так и сяк'. Это пространство не имеет фиксированных координат и появляется лишь тогда, когда в нем возникает острая необходимость. С точки зрения архитектуры — это пространственный карман, подпитываемый общей магией Хогвартса.",
        "Никогда не пытайтесь нанести её на карту. Картографирование живого пространства — неблагодарное занятие, которое часто приводит к тому, что чернила начинают течь в обратную сторону. Просто примите как факт: замок знает, что вам нужно, лучше, чем вы сами.",
        "А теперь, если позволите, я продолжу лекцию о влиянии каменной кладки на восстания эльфов в четырнадцатом веке... О, кажется, вы уже спите. Похвальное единодушие."
      ]
    }
  ]
},
  {
  id: 33,
  t: "Дух соревнований",
  a: "Альбус Дамблдор",
  cat: "Культура",
  ic: "fa-trophy",
  c: ["#8e44ad", "#2c3e50"],
  ch: [
    {
      t: "Глава 1: Кубок Огня и Триада Народов",
      p: [
        "Турнир Трех Волшебников — это не просто соревнование в силе и ловкости, это древний мост, перекинутый между народами и школами. Когда-то он был символом раздора, но сегодня мы видим в нем шанс для объединения. Кубок Огня — беспристрастный судья, он видит не ваши оценки, а ваше истинное 'Я', скрытое под мантией студента.",
        "Помните: слава — это лишь побочный эффект мужества, а не его цель. Тот, кто жаждет только победы, часто теряет самое ценное — свою человечность. Истинный чемпион тот, кто в пылу схватки не забудет протянуть руку упавшему сопернику.",
        "А теперь, прежде чем мы перейдем к правилам, я хотел бы сказать несколько слов: Олух! Пузырь! Остаток! Уловка! Спасибо."
      ]
    },
    {
      t: "Глава 2: Пятьсот очков Гриффиндору! (И не только)",
      p: [
        "Система баллов в Хогвартсе — это не способ наказать нерадивых, а способ поощрить единство. Когда один из вас совершает добрый поступок, весь факультет становится сильнее. Соперничество между Гриффиндором, Слизерином, Когтевраном и Пуффендуем должно напоминать нам о том, что разные пути могут вести к одной цели.",
        "Иногда требуется немалое мужество, чтобы противостоять врагам, но еще больше — чтобы противостоять друзьям. За такое мужество я всегда готов начислить те самые решающие десять баллов, которые могут изменить исход года. Магия цифр в песочных часах — это магия ваших ежедневных решений."
      ]
    },
    {
      t: "Глава 3: Рождественский бал и магия общения",
      p: [
        "Турнир не заканчивается на поле боя. Рождественский бал — это испытание иного рода. Умение вести беседу, танцевать и уважать традиции других стран ничуть не менее важно, чем умение сражаться с драконом. Это время, когда мы снимаем доспехи и открываем сердца.",
        "Для директоров школ это тоже соревнование — чьи студенты окажутся самыми галантными? Но за всем этим блеском скрывается простая истина: мы сильнее, когда мы едины, и слабее, когда разобщены. Любое соревнование должно заканчиваться крепким рукопожатием, а не обидой."
      ]
    },
    {
      t: "Глава 4: Выбор и Последствия",
      p: [
        "В конце любого пути нас ждет не награда, а осознание того, кто мы есть. Настанет время, когда каждому из вас придется выбирать между тем, что правильно, и тем, что легко. Дух соревнований готовит вас к этому выбору. Он закаляет волю и учит проигрывать с достоинством.",
        "Не забывайте, что лимонные дольки значительно улучшают настроение после любого поражения. Жизнь слишком коротка, чтобы тратить её на мрачные мысли. Идите вперед с поднятой головой, верьте в своих друзей и помните: счастье можно найти даже в самые темные времена, если не забывать обращаться к свету.",
        "Да будет ваш путь освещен звездами и вашим собственным добрым сердцем."
      ]
    }
  ]
},
  {
  id: 34,
  t: "Этикет чистокровных",
  a: "Люциус Малфой",
  cat: "Традиции",
  ic: "fa-crown",
  c: ["#2ecc71", "#0a3d1d"],
  ch: [
    {
      t: "Глава 1: Социальная иерархия и Манеры за столом",
      p: [
        "Манеры — это то, что отделяет нас от дикарей и, разумеется, от маглов. Настоящий лорд узнается по чистоте его мантии и остроте его ума. За столом ваша осанка должна быть безупречной, как фамильный герб. Никогда не используйте магию для подачи блюд в присутствии министра или высокопоставленных гостей — это признак нетерпеливости и неуважения к труду домашних эльфов.",
        "Помните: столовое серебро должно быть только из гоблинской ковки. Если вы не можете отличить вилку для рыбы от вилки для салата, вам не место в высшем обществе. И никогда, слышите, никогда не заговаривайте о делах до того, как будет подан десерт. Терпение — это добродетель королей.",
        "Если ваш собеседник совершил оплошность, не указывайте на неё прямо. Достаточно одного ледяного взгляда, чтобы он понял всю глубину своего падения."
      ]
    },
    {
      t: "Глава 2: Искусство светской беседы и Намеков",
      p: [
        "Слова — это кинжалы, обернутые в шелк. Чистокровный волшебник никогда не говорит прямо то, что он думает. Мы используем полутона, намеки и вежливые улыбки, за которыми скрывается истинное намерение. Умение вести беседу о погоде так, чтобы ваш оппонент почувствовал себя ничтожеством — это вершина дипломатии.",
        "Никогда не повышайте голос. Громкий крик — удел тех, кому нечего сказать. Шепот в правильное ухо в коридорах Министерства магии стоит дороже, чем сотня выкрикнутых заклинаний. Учитесь слушать то, что не было сказано, и читать между строк официальных писем.",
        "Если вам приходится общаться с... менее удачливыми представителями нашего мира, сохраняйте дистанцию. Вежливость — лучший способ подчеркнуть пропасть между вами."
      ]
    },
    {
      t: "Глава 3: Традиции Рода и Наследие",
      p: [
        "Ваша фамилия — это не просто слово, это капитал, накопленный веками. Каждое ваше действие либо приумножает славу рода, либо ложится на него пятном. Тщательно выбирайте круг общения. Дружба с 'неправильными' людьми может закрыть перед вами двери, которые не откроет ни одно 'Алохомора'.",
        "Священные двадцать восемь семейств — это костяк нашего общества. Уважение к старшим в роду и соблюдение древних обрядов (таких как бал в честь летнего солнцестояния) — это не прихоть, а обязанность. Мы храним чистоту крови не из ненависти к другим, а из любви к совершенству.",
        "Галстук должен быть завязан узлом 'Виндзор', а трость — всегда начищена до блеска. Внешний вид — это ваше первое и самое эффективное защитное заклинание."
      ]
    },
    {
      t: "Глава 4: Влияние и Подарки",
      p: [
        "Влияние не покупается, оно культивируется. Правильный подарок в правильное время может решить исход голосования в Визенгамоте. Но помните: подарок не должен выглядеть как взятка. Это должен быть 'знак глубочайшего почтения'. Редкая рукопись, бутылка вина урожая 1812 года или антикварный артефакт сделают больше, чем мешок золота.",
        "Будьте щедры к тем, кто вам полезен, и беспощадны к тем, кто стоит на пути. Но делайте это изящно. Настоящий Малфой никогда не пачкает руки — для этого всегда найдутся те, кто готов выполнить грязную работу за обещание покровительства.",
        "Мир принадлежит тем, кто умеет ждать и смотреть на него сверху вниз. Постарайтесь соответствовать."
      ]
    }
  ]
},
  {
  id: 35,
  t: "Зеркало Еиналеж",
  a: "Николас Фламель",
  cat: "Артефакторика",
  ic: "fa-wand-magic",
  c: ["#d4af37", "#1a1a1a"],
  ch: [
    {
      t: "Глава 1: Отражение души и Тайное Имя",
      p: [
        "Зеркало Еиналеж (Erised) — это не обычный магический инструмент, это ментальный резонатор колоссальной силы. Оно не показывает ваше лицо, не показывает ваше будущее. Оно показывает нечто гораздо более интимное и опасное — самое сокровенное, самое отчаянное желание вашего сердца. Его имя, начертанное на раме, нужно читать задом наперед, чтобы понять его суть: 'Я показываю не ваше лицо, но ваше желание'.",
        "Для большинства волшебников, даже самых сильных, это отражение становится наркотиком. Многие сходили с ума, завороженные его видениями. Они проводили дни и недели перед ним, истощенные, веря, что видение — это истина, которую они могут достичь. Это не так. Зеркало дает не знание и не силу, оно дает лишь морок.",
        "Совет от Николаса: если вы встретите это Зеркало, не смотрите в него слишком долго. Оно обнажает вашу самую уязвимую часть. Настоящее счастье — это принять реальность, а не жить в иллюзии, даже если эта иллюзия создана вашей собственной душой."
      ]
    },
    {
      t: "Глава 2: Психосоматика Иллюзии",
      p: [
        "Магия Зеркала работает на принципе зеркального резонанса с вашими подсознательными желаниями. Оно считывает ваши страхи, надежды и невыраженные амбиции. Например, если вы хотите вернуть потерянного близкого, Зеркало создаст его проекцию, которая будет выглядеть и звучать так, как вы его помните. Но это лишь проекция вашего собственного ума, а не душа покойного.",
        "Взаимодействие с Зеркалом может вызвать 'эффект Еиналеж' — состояние, когда реальный мир теряет краски и смысл. Волшебник перестает есть, спать и колдовать, полностью погружаясь в созерцание своего желания. Это ментальная ловушка, которая может привести к летальному исходу.",
        "Именно поэтому я, Николас Фламель, никогда не пытался использовать это Зеркало для возвращения моей возлюбленной Перенеллы. Я знаю, что настоящее бессмертие — это не бесконечная иллюзия, а бесконечное развитие души в реальности."
      ]
    },
    {
      t: "Глава 3: Хранение и Защита",
      p: [
        "Зеркало Еиналеж — это артефакт, который должен быть скрыт от посторонних глаз. Не только из-за его опасности, но и из-за того, что его магия может быть использована для ментального шпионажа. Если вы знаете, что показывает Зеркало вашему врагу, вы знаете его самую большую слабость.",
        "Его рама, сделанная из редчайшего черного дерева и инкрустированная серебряными рунами, не только украшает, но и служит фильтром, предотвращающим распространение ментального излучения на большие расстояния. Если рама будет повреждена, Зеркало может начать сводить с ума целые поселения.",
        "Лучший способ хранить Зеркало — поместить его в комнату, которая появляется только тогда, когда в ней есть крайняя необходимость, и запечатать мощнейшими чарами забвения. Путь к нему должен быть известен только тем, кто готов пожертвовать своим желанием ради его сохранения."
      ]
    },
    {
      t: "Напутствие Николаса Фламеля",
      p: [
        "Я прожил более шестисот лет, и я видел много чудес. Но ни одно из них не сравнится по силе с тем, что скрыто внутри нас. Ваше сердце — это самый могущественный артефакт, который когда-либо существовал. Не позволяйте иллюзиям затмить его свет.",
        "Берегите свою душу, живите в реальности и помните: самое великое желание — это быть самим собой, здесь и сейчас. Иначе вы рискуете стать лишь тенью в чужом зеркале.",
        "Прощайте, и пусть ваше сердце всегда знает, что оно действительно желает."
      ]
    }
  ]
},
  {
  id: 36,
  t: "Сказки для эльфов",
  a: "Добби",
  cat: "Фольклор",
  ic: "fa-socks",
  c: ["#f39c12", "#e67e22"],
  ch: [
    {
      t: "Глава 1: Великая История о Волшебном Носке",
      p: [
        "Добби хочет рассказать всем-всем эльфам: самый прекрасный, самый сияющий день в жизни — это день, когда хозяин дарит вам одежду! Для мага это просто кусочек ткани, но для эльфа — это ключ от клетки. Один старый, дырявый носок может стать ценнее, чем весь золотой запас Гринготтса, если он дан с намерением освободить!",
        "Добби помнит, как это было. Ткань коснулась рук Добби, и магия службы, которая жгла сердце, вдруг превратилась в магию выбора. Свобода — это не значит перестать помогать. О нет! Свобода — это право служить тем, кого ты любишь, по своей собственной воле, а не потому, что тебя заставляют биться головой о лампу.",
        "Если вам подарили одежду, не прячьте её! Носите её с гордостью. Добби носит сразу семь шляп и разные носки, потому что это красиво и... потому что Добби может!"
      ]
    },
    {
      t: "Глава 2: Легенда о Добром Волшебнике",
      p: [
        "Давным-давно, среди эльфов ходил шепот о Гарри Поттере. Добби знал: этот волшебник другой. Он не смотрит на эльфов как на мебель, он говорит 'пожалуйста' и 'спасибо'. В сказках эльфов Гарри Поттер — это великий защитник слабых, тот, кто победил Сами-Знаете-Кого не ради славы, а ради друзей.",
        "Добби учит других эльфов: дружба с волшебником — это не рабство. Это когда вы вместе спасаете мир, даже если вы совсем маленький, а враг очень страшный. Главная сказка Добби — это правда: даже крохотный эльф может изменить ход истории, если у него храброе сердце и чистые помыслы."
      ]
    },
    {
      t: "Глава 3: Кулинарные секреты Хогвартса",
      p: [
        "На кухнях Хогвартса работают сотни эльфов, и у каждого есть своя история. Мы готовим пудинги и пироги с почками, вкладывая в них магию уюта. Знаете ли вы, что если эльф готовит в плохом настроении, соус может начать кусаться? Магия эльфов очень сильно зависит от того, насколько они счастливы.",
        "Добби советует: если вы работаете на кухне, всегда оставляйте немного лишнего крема для домовиков, которые заглянут в гости. И никогда не жалейте сливочного масла для Гриффиндорского стола — молодым героям нужно много сил для подвигов!"
      ]
    },
	{
      t: "Глава 4: Тайный язык кухонь и чуланов",
      p: [
        "Хозяева думают, что эльфы просто исчезают и появляются с громким 'хлоп!'. Но мы слышим всё! Стены замка шепчут нам секреты, а пыль под кроватями рассказывает, кто прячет шпаргалки, а кто — запрещенные сладости из Хогсмида.",
        "У эльфов есть свои знаки. Если вы видите, что домовик чистит один и тот же подсвечник три раза — значит, за дверью злой волшебник! Если эльф пересолил суп — он предупреждает о проверке из Министерства. Мы храним секреты замка лучше, чем портреты на стенах."
      ],
      analogies: [
        { 
          func: "Шепот в коридоре", 
          magic: "Подслушивание через камин", 
          alchemy: "Сбор сплетен для безопасности" 
        },
        { 
          func: "Лишняя ложка сахара", 
          magic: "Знак одобрения студенту", 
          alchemy: "Поддержка боевого духа" 
        },
        { 
          func: "Перепутанные ботинки", 
          magic: "Мелкая месть обидчику", 
          alchemy: "Восстановление справедливости" 
        }
      ]
    },
    {
      t: "Глава 5: Модный приговор Свободного Эльфа",
      p: [
        "Многие эльфы боятся одежды, но Добби говорит: одежда — это весело! Необязательно ждать, пока хозяин даст вам носок, можно найти потерянные вещи или связать что-то самому, если вы уже свободны.",
        "Главное правило Добби: чем больше цветов, тем лучше! Если ваши носки одинаковые — это скучно. Если у вас на голове меньше трех шляп — вам может надуть уши магическим сквозняком."
      ],
      dossier: {
        title: "Гардероб Мечты по версии Добби",
        data: [
          ["Головной убор", "Вязаная шапка с помпоном (минимум 2 штуки)"],
          ["Левая нога", "Ярко-красный носок в желтую полоску"],
          ["Правая нога", "Зеленый носок с розовыми гиппогрифами"],
          ["Аксессуар", "Чайное полотенце с гербом Хогвартса (выстиранное!)"],
          ["Особый шик", "Значок 'Г.А.В.Н.Э.' от мисс Грейнджер"]
        ]
      }
    },
    {
      t: "Заключительное слово Свободного Эльфа",
      p: [
        "Добби счастлив быть здесь. Добби счастлив быть другом. Моя сказка заканчивается тем, что каждый эльф находит свой 'носок'. Не бойтесь просить оплаты за свой труд — Галлеон в неделю и один выходной сделают вас самым богатым эльфом на свете!",
        "Будьте добры друг к другу. Берегите своих друзей-волшебников. И помните: Добби всегда будет присматривать за вами из самого светлого места в мире магии.",
        "С любовью и вечной преданностью, Свободный Эльф Добби."
      ]
    }
  ]
},
  {
    id: 37,
    t: "Карта Мародеров: Истоки",
    a: "Лунатик и Сохатый",
    cat: "Артефакторика",
    ic: "fa-map-location-dot",
    c: ["#7d6b4a", "#3e321e"],
    ch: [
      {
        t: "Торжественно клянусь...",
        p: [
          "Этот пергамент — плод нашей дружбы и бессонных ночей в коридорах замка.",
          "Мы запечатлели каждый тайный ход, каждую лестницу и даже привычки Филча.",
          "Помните: шалость удалась только тогда, когда вас не поймали."
        ]
      }
    ]
  },
  {
  id: 38,
  t: "Справочник по ядам",
  a: "Гораций Слизнорт",
  cat: "Зельеварение",
  ic: "fa-skull-crossbones",
  c: ["#4a0e4e", "#1a051d"],
  ch: [
    {
      t: "Глава 1: Великая Триада Опасности — Яд Василиска",
      p: [
        "Ах, яды... Тема столь же интригующая, сколь и фатальная. Начнем с короля всех токсинов — яда василиска. Это вещество обладает такой разрушительной силой, что способно уничтожить даже самые защищенные магические объекты, включая, если верить легендам, крестражи. Его капля прожигает металл и камень, не оставляя шанса на ошибку.",
        "Противоядие существует только одно — слезы феникса. Но, увы, эти великолепные птицы крайне редко делятся своими чувствами с отравителями. Помните: если вы работаете с зубом василиска, ваши перчатки из кожи дракона должны быть двойными. Одна маленькая царапина — и ваш следующий ужин будет уже в компании Сэра Николаса.",
        "Интересно, что яд сохраняет свою активность десятилетиями даже в высохшем состоянии. Это делает старые руины крайне неуютным местом для прогулок."
      ]
    },
    {
      t: "Глава 2: Напиток Живой Смерти и Тонкости Дозировки",
      p: [
        "Грань между лекарством и ядом тонка, как волос акромантула. Напиток Живой Смерти погружает человека в глубочайший транс, который легко принять за конец пути. Если вы добавили слишком много сока дремоносных бобов, пациент может просто не проснуться, превратившись в спящую статую.",
        "Для идеального результата сок нужно не резать, а давить плоской стороной серебряного кинжала — этот старый трюк я узнал от одного... очень талантливого студента. Помните, что противоядие — Глоток Живой Смерти — требует в три раза больше усилий при варке. Всегда держите флакон под рукой, если вы склонны к рассеянности."
      ]
    },
    {
      t: "Глава 3: Безоар — Универсальный спаситель",
      p: [
        "Если вы не знаете, какой именно яд попал в ваш кубок с медовухой (а в наше неспокойное время это случается чаще, чем хотелось бы), просто запихните бедняге в глотку безоар. Этот сморщенный камень из желудка козы нейтрализует большинство известных токсинов растительного и животного происхождения.",
        "Конечно, безоар не панацея от яда василиска или проклятых субстанций, но он даст вам те драгоценные минуты, чтобы добежать до мадам Помфри. Я всегда ношу один в кармане своей лучшей мантии. На всякий случай. Особенно когда обедаю в компании подозрительных личностей."
      ]
    },
    {
      t: "Глава 4: Этикет и Осторожность",
      p: [
        "Использование ядов в светском обществе считается дурным тоном, но знание их свойств — признак образованного мага. Никогда не хвастайтесь своей коллекцией токсинов перед аврорами. Это может привести к крайне затяжной и скучной беседе в Министерстве Магии.",
        "Наслаждайтесь жизнью, мои дорогие, ешьте сладости и пейте хорошее вино, но всегда смотрите на цвет пузырьков в вашем бокале. Если они пахнут горелым миндалем или светятся неестественным зеленым цветом — возможно, стоит предложить этот тост кому-нибудь другому.",
        "Ваш преданный учитель и ценитель редких настоек, Гораций Слизнорт."
      ]
    }
  ]
},
  {
  id: 39,
  t: "Величие чистой крови",
  a: "Кантанкерус Нотт",
  cat: "Традиции",
  ic: "fa-crown",
  c: ["#0b3d1d", "#051a0d"],
  ch: [
    {
      t: "Глава 1: Священные двадцать восемь — Код Чести",
      p: [
        "Существует лишь двадцать восемь фамилий, чья кровь осталась истинно чистой в Британии. Этот список — не просто перечень имен, это карта нашей магической мощи. Каждое имя, от Эбботов до Яксли, несет в себе отпечаток столетий селекции и магической дисциплины. Мы — хранители пламени, которое маглы и предатели крови пытаются затушить своим невежеством.",
        "Сохранение наследия — это не гордыня, а священный долг перед самим искусством магии. Когда чистокровный маг вступает в союз с маглом, он не просто разбавляет кровь — он разрушает вековую настройку магических каналов, превращая бурную реку в грязный ручей. Мы обязаны быть бдительными, ибо чистота — это единственное, что отделяет нас от забвения.",
        "Помните: фамильное древо — это живой артефакт. Если на нем появляется гнилая ветвь, долг главы рода — отсечь её, пока зараза не достигла корней."
      ]
    },
    {
      t: "Глава 2: Генеалогия как Высшая Наука",
      p: [
        "Изучение родословных важнее изучения заклинаний. Зная, чья кровь течет в жилах вашего оппонента, вы заранее знаете предел его возможностей. Магия — это наследственный дар, и чем меньше в ней 'примесей', тем стабильнее и предсказуемее результат. Чистокровный маг не просто творит заклинание — он резонирует с силой своих предков.",
        "Гобелены в наших поместьях — это не просто украшения. Это магические документы. Каждая нить, связывающая Блэков, Малфоев и Ноттов, усиливает наш общий щит против внешнего мира. Если вы встретите мага, чье имя не значится в Справочнике, знайте: его магия — лишь бледная тень того великолепия, что доступно нам по праву рождения."
      ]
    },
    {
      t: "Глава 3: Традиции и Изоляционизм",
      p: [
        "Статут о секретности был создан не для того, чтобы прятаться, а для того, чтобы не оскверняться. Общение с немагическим населением ведет к деградации разума и духа. Мы должны создавать свои закрытые общины, где голос предков звучит громче, чем шум прогресса. Истинное величие требует одиночества и дистанции.",
        "Наши праздники, наши ритуалы, даже наш язык должны оставаться недоступными для непосвященных. Тот, кто пытается навести мосты между нами и низшими сословиями, совершает преступление против самой природы магии. История не простит тех, кто променял золото чистой крови на медные монеты толерантности."
      ]
    },
    {
      t: "Глава 4: Долг перед будущим",
      p: [
        "Ваша единственная задача в этой жизни — передать дар чистым. Вы лишь звено в бесконечной цепи. Если вы прервете её своей слабостью, проклятие падет на все ваши будущие воплощения. Ищите союза только среди равных, укрепляйте связи между Семьями, и тогда магический мир вновь обретет ту силу, которая была у него в эпоху Основателей.",
        "Чистота превыше всего. Традиция превыше желаний. Кровь превыше жизни.",
        "Пусть эти слова станут вашим девизом, как стали они девизом Кантанкеруса Нотта."
      ]
    }
  ]
},
  {
  id: 40,
  t: "Атлас созвездий",
  a: "Кентавр Флоренц",
  cat: "Астрономия",
  ic: "fa-meteor",
  c: ["#1b263b", "#0d1b2a"],
  ch: [
    {
      t: "Глава 1: Марс сегодня яркий — Предвестие бури",
      p: [
        "Звезды не лгут, но волшебники часто слышат в их шепоте только то, что хотят услышать. Когда Красная планета, Марс, разгорается на небосклоне ярче обычного, это не просто игра света. Это предвестие скорой войны, великих перемен и крови, которая прольется на землю. Кентавры читали эти знаки веками, пока люди строили свои крохотные замки.",
        "Не ищите в звездах ответов на мелкие вопросы — кто победит в матче по квиддичу или что ждет вас на обед. Небо говорит о судьбах миров. Если Орион склоняется к западу в необычный час, значит, старый порядок рушится. Мы лишь наблюдатели в этом великом театре, и наше дело — понимать течение времени, а не пытаться его повернуть.",
        "Помните: даже самое яркое предсказание может быть омрачено дымом костров или туманом сомнений. Смотрите на небо чистым взором."
      ]
    },
    {
      t: "Глава 2: Созвездия Древних и Путь Кентавра",
      p: [
        "Для кентавра небо — это открытая книга, где каждая искра имеет свое имя и значение. Созвездие Дракона напоминает нам о силе, которая дремлет в недрах земли, а Лира поет о гармонии, которую люди так часто нарушают. Мы учим наших жеребят читать карту небес раньше, чем они научатся скакать по лесу.",
        "Магия созвездий пассивна. Она не дает вам палочку, чтобы накладывать чары, но она дает вам знание о том, когда эти чары будут наиболее эффективны. Те, кто родился под знаком Сатурна, обладают врожденной стойкостью, но часто холодны сердцем. Те же, чья звезда — Венера, несут свет, но сгорают слишком быстро."
      ]
    },
    {
      t: "Глава 3: Дым и Видения — Техники Леса",
      p: [
        "Сжигание определенных трав — шалфея и мальвы — помогает настроить взор на частоту небесных сфер. В дыму костра кентавры видят тени того, что еще не случилось. Но будьте осторожны: видения приходят не к тем, кто их ищет, а к тем, кто готов их принять. Гордыня — это пелена, которая скрывает истину от глаз мага.",
        "Если вы увидите падающую звезду, знайте: это не просто камень, сгоревший в атмосфере. Это знак того, что великая душа покинула этот мир или только что в него вошла. Небо всегда отмечает приход и уход героев, даже если люди в это время заняты своими мелкими заботами."
      ]
    },
    {
      t: "Глава 4: Единство Неба и Земли",
      p: [
        "В конечном счете, мы все состоим из звездной пыли. Каждое движение планеты находит отражение в капле росы на листе дуба. Разделение на астрономию и жизнь — это иллюзия, созданная человеческим разумом для удобства. Кентавры знают: мы — часть единого узора.",
        "Когда вы закончите читать этот атлас, выйдите из башни. Погасите свет палочки. Поднимите глаза вверх и просто слушайте тишину между звездами. Там, в пустоте, скрыта самая могущественная магия из всех существующих.",
        "Пусть ваши тропы будут светлыми, а небо над вами — ясным. Флоренц из Запретного леса."
      ]
    }
  ]
},
  {
  id: 41,
  t: "Грамматика заклятий",
  a: "Миранда Гуссокл",
  cat: "Теория",
  ic: "fa-spell-check",
  c: ["#2c3e50", "#34495e"],
  ch: [
    {
      t: "Глава 1: Фонетика и Сила Ударения",
      p: [
        "Магия — это не только воля, но и точность. Помните: Леви-О-са, а не Левио-СА. Ошибка в ударении — это не просто лингвистическая оплошность, это искажение магического потока. В лучшем случае заклинание не сработает; в худшем — оно превратит легкое перышко в наковальню на вашей ноге или вызовет взрыв, который лишит вас бровей.",
        "Каждый слог в заклинании должен резонировать с вашим внутренним магическим ядром. Если вы мямлите или не уверены в произношении, палочка почувствует это и ответит лишь искрами. Практикуйте дикцию перед зеркалом, пока каждое слово не станет звучать как чистый звон колокола."
      ]
    },
    {
      t: "Глава 2: Кинетика палочки — Взмах и Удар",
      p: [
        "Ваша рука — это продолжение палочки, а не её хозяин. Держите палочку уверенно, но без лишнего напряжения в кисти; зажатые мышцы блокируют передачу энергии. Движение 'взмахнуть и ударить' должно быть плавным, как полет совы, и точным, как удар молнии.",
        "Для защитных чар используйте круговые движения по часовой стрелке, для атакующих — резкие вертикальные выпады. Помните, что лишние движения только рассеивают магию. Истинный мастер творит чудеса едва заметным движением пальцев."
      ]
    },
    {
      t: "Глава 3: Латынь и Древние корни",
      p: [
        "Большинство современных заклинаний базируются на магической латыни, но их корни уходят гораздо глубье. Понимая этимологию слова, вы понимаете саму суть чар. Например, 'Экспеллиармус' буквально означает 'изгнание оружия'. Направляйте свою мысль на это действие, и заклинание станет втрое мощнее."
      ]
    }
  ]
},
{
  id: 42,
  t: "Запретный лес",
  a: "Сильванус Кеттлберн",
  cat: "Магозоология",
  ic: "fa-tree",
  c: ["#013220", "#002210"],
  ch: [
    {
      t: "Глава 1: Территория Восьминогих",
      p: [
        "Если вы забрели слишком далеко в чащу и увидели паутину размером с простыню — бегите, причем желательно не по прямой. Вы вошли на территорию Акромантулов. Эти пауки обладают острым разумом и даже способны к человеческой речи, но не обольщайтесь: их аппетит гораздо сильнее их вежливости.",
        "Они не любят яркий свет и шум, поэтому заклинание 'Люмос Солем' может дать вам пару секунд форы. Но лучшее средство от встречи с Акромантулом — это здравый смысл и умение вовремя повернуть назад к хижине лесничего."
      ]
    },
    {
      t: "Глава 2: Лунные Тельцы и единороги",
      p: [
        "Не всё в лесу жаждет вашей смерти. В полнолуние на полянах можно встретить Лунных Тельцов, исполняющих свои причудливые танцы. Это зрелище завораживает, но не подходите близко — их легко спугнуть. Что касается единорогов, помните: они доверяют только чистым душой. Если вы нашли серебристую кровь на траве — значит, в лесу затаилось нечто по-настоящему злое."
      ]
    },
    {
      t: "Глава 3: Выживание без палочки",
      p: [
        "В Запретном лесу ваша палочка может подвести вас, если вы наткнетесь на зоны подавления магии. В такие моменты полагайтесь на свои чувства. Слушайте шепот листвы (иногда это кентавры, а иногда — просто ветер) и всегда носите с собой мешочек с солью и сушеную крапиву. Кеттлберн плохого не посоветует, хоть у него и осталась всего одна целая нога!"
      ]
    }
  ]
},
  {
  id: 43,
  t: "Меч и магия",
  a: "Годрик Гриффиндор",
  cat: "История",
  ic: "fa-sword",
  c: ["#8b0000", "#d4af37"],
  ch: [
    {
      t: "Глава 1: Кодекс Храбрости и Закалка Духа",
      p: [
        "Магия — это не только палочка, послушно лежащая в руке, но и отважное сердце, бьющееся в груди. В те темные времена, когда мы строили Хогвартс, маг должен был уметь постоять за себя не только словом, но и делом. Истинный гриффиндорец найдет выход там, где другие видят стену, и сделает шаг вперед там, где другие отступят в тень.",
        "Меч, выкованный гоблинами специально для меня, — это не просто оружие. Это символ. Он явится лишь тому, кто достоин его силы в час величайшей нужды, когда все заклинания бессильны, а надежда почти угасла. Помните: сталь впитывает в себя только то, что делает её сильнее. Ваша душа должна быть такой же — впитывайте опыт, но не позволяйте злу запятнать вашу суть.",
        "Будьте дерзки в своих мечтах, но смиренны в своих поступках. Храбрость без мудрости — это лишь безрассудство, которое ведет к гибели."
      ]
    },
    {
      t: "Глава 2: Искусство честного поединка",
      p: [
        "Дуэль — это не способ убить, а способ доказать свою правоту. Настоящий рыцарь магии никогда не нападет со спины и не ударит безоружного. Мы учили наших студентов, что магия должна служить щитом для слабых, а не плетью для угнетенных. Если вы вынуждены обнажить меч или поднять палочку — делайте это с ясным умом и чистой совестью.",
        "В бою важно не то, сколько заклятий вы знаете, а то, насколько крепко вы стоите на земле. Колебание — это предвестник поражения. Верьте в свою правду так, как верит клинок в руку мастера."
      ]
    },
    {
      t: "Глава 3: Распределяющая Шляпа — Память Основателей",
      p: [
        "Когда придет время, и нас не станет, кто разделит юных магов по их талантам? Я снял свою шляпу и мы, четверо Основателей, вложили в неё частицы своего разума. Она видит то, что скрыто от глаз учителей — потенциал, который еще не раскрылся. Мой факультет — для тех, чья отвага граничит с безумием, а благородство — с самопожертвованием.",
        "Не бойтесь вердикта Шляпы. Она лишь указывает путь, но идти по нему — вам. Каждый из нас — кузнец своего характера."
      ]
    },
    {
      t: "Глава 4: Наследие Льва",
      p: [
        "Хогвартс стоит на камнях, скрепленных нашей дружбой (хотя порой она и подвергалась суровым испытаниям). Я завещаю вам это место не как убежище, а как тренировочный зал для духа. Будьте готовы защищать эти стены так, как защищали их мы в первый день.",
        "Лев не прячется от грозы — он встречает её рыком. Идите навстречу своим страхам, и они растают, как туман под утренним солнцем.",
        "С верой в ваше мужество, Годрик Гриффиндор."
      ]
    }
  ]
},
 {
  id: 44,
  t: "Магические шахматы",
  a: "Рон Уизли",
  cat: "Игры",
  ic: "fa-chess-knight",
  c: ["#3d3d3d", "#1a1a1a"],
  ch: [
    {
      t: "Глава 1: Гамбит Коня — Характер Фигур",
      p: [
        "В магических шахматах фигуры живут своей жизнью, и это первое, что вы должны усвоить. Они не просто куски камня, они — воины с характером. Если ваш ход откровенно глуп, ладья может отказаться двигаться, а пешка начнет ворчать себе под нос. Чтобы побеждать, нужно заставить их уважать вас.",
        "Мой старый дедушкин набор, например, постоянно спорит со мной о защите Каро-Канн. Но в этом и суть: шахматы — это диалог. Кони — самые дерзкие ребята на доске, они обожают прыгать в самую гущу событий. Используйте их, чтобы запутать противника, но помните: если конь пал, он сделает это громко и эффектно, разбившись на тысячу осколков.",
        "Никогда не жертвуйте ферзем, если не уверены в мате через два хода. Потеря ферзя в магических шахматах — это как потеря генерала на поле боя: боевой дух остальных фигур падает мгновенно."
      ]
    },
    {
      t: "Глава 2: Психология и Блеф",
      p: [
        "Магические шахматы — это битва воли. Иногда стоит сделать не самый сильный ход, но сделать его так уверенно, чтобы противник занервничал. Если вы играете против Когтевранца, он будет искать логику. Сломайте её неожиданным выпадом слона. Если против Слизеринца — ждите ловушек на каждом поле.",
        "Слушайте, что говорят ваши фигуры. Иногда они видят доску лучше вас. Но не позволяйте им полностью брать управление на себя — в конце концов, это ваша партия. И ради всего святого, не пытайтесь играть набором Гермионы: они слишком много рассуждают о теории и почти не атакуют!"
      ]
    },
    {
      t: "Глава 3: Партия Профессора Макгонагалл",
      p: [
        "Я видел шахматы, которые были больше человека и в сто раз опаснее. В таких партиях вы не просто двигаете фигуру — вы сами становитесь фигурой. В большой игре важно понимать: иногда приходится жертвовать собой, чтобы другие могли идти дальше. Это не жестокость, это стратегия выживания.",
        "Если вам когда-нибудь придется занять место коня на гигантской доске — не оборачивайтесь. Просто делайте то, что должны. Истинный гроссмейстер знает, что победа всей команды важнее личной безопасности."
      ]
    },
    {
      t: "Глава 4: Уход за набором",
      p: [
        "Никогда не чистите шахматы заклинанием 'Тергео' слишком часто — они становятся скользкими и начинают выпадать из рук в самый ответственный момент. Лучше всего — старая добрая тряпочка и немного воска. И не забывайте кормить их... ладно, это шутка, они не едят. Но внимание они любят.",
        "Удачи на доске. И помните: если проиграли — не вините фигуры. Скорее всего, вам просто нужно попрактиковаться еще пару лет. Или спросить совета у меня!",
        "Ваш Рон Уизли, Чемпион Хогвартса по шахматам."
      ]
    }
  ]
},
  {
  id: 45,
  t: "Символика Феникса",
  a: "Альбус Дамблдор",
  cat: "Древняя магия",
  ic: "fa-feather",
  c: ["#ff4500", "#800000"],
  ch: [
    {
      t: "Глава 1: Путь перерождения и Урок Пепла",
      p: [
        "Феникс — удивительное создание. Когда наступает срок, он сгорает, чтобы вновь восстать из пепла маленьким, беззащитным, но обновленным. В этом кроется величайший урок для каждого мага: конец — это всегда начало. Мы слишком часто цепляемся за свои старые обиды и ошибки, боясь 'сгореть', но именно через очищающее пламя перемен мы обретаем истинную силу.",
        "День сжигания может показаться со стороны печальным — птица выглядит старой и немощной. Но посмотрите внимательнее в эти мгновения. В глазах феникса нет страха, только знание. Он принимает свою смерть как необходимый этап. Если бы люди умели так же легко отпускать свое прошлое, мир магии был бы избавлен от множества проклятий.",
        "Помните: чтобы взлететь выше прежнего, иногда нужно позволить себе превратиться в горстку серой пыли."
      ]
    },
    {
      t: "Глава 2: Слезы и Песня — Исцеление духа",
      p: [
        "Магия феникса не агрессивна, она созидательна. Его слезы — самое мощное из известных мне исцеляющих средств. Они способны залечить раны от яда василиска, когда любая другая магия бессильна. Но гораздо важнее то, что слезы феникса исцеляют плоть, в то время как его песня — израненную душу.",
        "Песнь феникса — это чистый звук надежды. Она придает храбрости тем, кто чист сердцем, и вселяет ужас в тех, кто погряз во тьме. Когда вы слышите этот небесный голос, знайте: вы не одиноки. Даже в самой глубокой тьме отзвук этой песни укажет вам путь к свету. Это магия, которую невозможно выучить по учебникам — её можно только почувствовать."
      ]
    },
    {
      t: "Глава 3: Преданность и Огненный телепорт",
      p: [
        "Феникс выбирает себе спутника на всю жизнь, но он никогда не становится рабом. Его преданность — это дар, который нужно заслуживать каждый день. Фоукс не раз спасал меня не потому, что я его хозяин, а потому, что наши цели в тот момент совпадали. Это партнерство, основанное на высшем доверии.",
        "Уникальная способность феникса перемещаться в пространстве в столпе пламени обходит любые антитрансгрессионные чары, даже в Хогвартсе. Это напоминает нам о том, что древняя природная магия всегда будет стоять на ступень выше человеческих законов и запретов. Огонь феникса не обжигает друзей, он лишь переносит их туда, где они нужнее всего."
      ]
    },
    {
      t: "Глава 4: Прощание и Вечный Свет",
      p: [
        "Когда маг, связанный с фениксом, уходит навсегда, птица поет свою последнюю песнь. Это плач, в котором нет горечи, лишь благодарность за пройденный вместе путь. После этого феникс улетает, чтобы никогда не возвращаться к людям. Его миссия окончена.",
        "Я пишу эти строки, глядя на золотое оперение Фоукса в лучах заката. Магия — это свет. И если когда-нибудь вам покажется, что свет гаснет, вспомните о фениксе. Всегда можно начать заново. Всегда можно восстать.",
        "Да пребудет с вами тепло вечного огня. Ваш Альбус Дамблдор."
      ]
    }
  ]
},
  {
  id: 46,
  t: "Странности маглов",
  a: "Гермиона Грейнджер",
  cat: "Культура",
  ic: "fa-lightbulb",
  c: ["#1e90ff", "#00008b"],
  ch: [
    {
      t: "Глава 1: Интернет и Глобальная Связь",
      p: [
        "Маглы, лишенные возможности использовать каминную сеть или трансгрессию, создали невидимую сеть, которая связывает весь мир без помощи сов. Они называют это 'Интернетом'. Это колоссальное хранилище информации, своего рода цифровая Библиотека Хогвартса, доступная из любой точки планеты через небольшие светящиеся коробочки в их карманах.",
        "Удивительно, как они справляются без магии, используя лишь двоичный код и электрические импульсы. Вместо того чтобы отправлять громовещатель, они посылают 'электронные письма'. Это намного тише, хотя и лишает сообщение того драматизма, к которому мы привыкли. Однако скорость передачи данных сопоставима с самым быстрым патронусом.",
        "Для мага это может показаться странным, но маглы буквально построили вторую реальность, где они работают, общаются и даже влюбляются, не выходя из дома."
      ]
    },
    {
      t: "Глава 2: Электричество — Магия без палочки",
      p: [
        "То, что мы называем магией, маглы называют физикой. Электричество — это их основной источник силы. Они приручили энергию молнии, чтобы освещать свои города, готовить еду и приводить в движение огромные машины. В Хогвартсе слишком много магии в воздухе, поэтому их приборы здесь 'сходят с ума', но в обычном Лондоне это работает безупречно.",
        "Артур Уизли часто спрашивает меня о назначении 'резиновых уточек', но на самом деле маглы используют гораздо более сложные вещи. Например, самолеты — это огромные металлические птицы, которые перевозят сотни людей по небу. И нет, внутри них нет невидимых гиппогрифов. Это чистая аэродинамика."
      ]
    },
    {
      t: "Глава 3: Медицина против Зелий",
      p: [
        "Магловская медицина заслуживает отдельного изучения. Не имея костероста или бадьяна, они научились заменять органы, исправлять зрение с помощью лазеров и создавать вакцины от смертельных болезней. Это требует десятилетий обучения и невероятной точности. Иногда их методы кажутся варварскими (например, использование скальпелей), но результаты порой поражают даже целителей из Мунго.",
        "Они не могут 'починить' сломанный нос одним заклинанием, но они понимают структуру ДНК — фундаментальный код самой жизни, о котором маги знают до обидного мало."
      ]
    },
    {
      t: "Заключение: Мост между мирами",
      p: [
        "Нам есть чему поучиться у маглов. Их изобретательность — это следствие их ограничений. Когда у тебя нет палочки, ты вынужден заставлять свой разум работать на пределе возможностей. Я призываю каждого студента хотя бы раз прочитать газету 'Таймс', чтобы понять: мир за пределами Хогвартса не менее чудесен, чем замок.",
        "Знание — это единственная сила, которая одинаково эффективно работает в обоих мирах. Постарайтесь быть любознательными.",
        "Искренне ваша, Гермиона Грейнджер."
      ]
    }
  ]
},
  {
  id: 47,
  t: "Тайны Хогвартса",
  a: "Том Реддл",
  cat: "Запретная секция",
  ic: "fa-book-dead",
  c: ["#000000", "#1a1a1a"],
  ch: [
    {
      t: "Глава 1: Наследие Слизерина — Тайная комната",
      p: [
        "Хогвартс — это не просто школа, это крепость, хранящая секреты, которые посредственные умы боятся даже представить. Салазар Слизерин оставил после себя не только факультет, но и величайшее испытание для достойного. Тайная комната — это алтарь истины. Наследник Слизерина откроет то, что было скрыто веками, и очистит эти стены от недостойных.",
        "Внутри стен замка спит древний ужас, подчиняющийся только змеиному языку (Parseltongue). Это существо — символ нашей власти над жизнью и смертью. Враги наследника, трепещите, ибо когда печать будет сорвана, ни одно заклинание учителей не сможет остановить правосудие самой крови.",
        "Помните: истинная тайна открывается не тому, кто ищет её в книгах, а тому, кто готов признать своё превосходство."
      ]
    },
    {
      t: "Глава 2: Хоркрукс — Магия Вечности",
      p: [
        "Смерть — это слабость, которую маглы и обычные волшебники принимают как неизбежность. Но есть путь к бессмертию, требующий высшей жертвы и железной воли. Раскол души — это акт величайшего творения. Поместив частицу своего естества в неодушевленный предмет, маг становится призраком, которого невозможно изгнать из этого мира.",
        "Этот процесс болезненен, но боль — это лишь цена за освобождение от оков времени. Выбирайте сосуд с умом: он должен быть достоин великой души. Артефакты основателей — вот идеальные вместилища для тех, кто желает править вечно."
      ]
    },
    {
      t: "Глава 3: Дневник — Живая Память",
      p: [
        "Бумага может хранить не только чернила, но и саму личность. Мой дневник — это не просто записи юности, это живой слепок моего разума. Тот, кто доверит ему свои мысли, получит нечто большее, чем просто собеседника. Он получит наставника, способного направить его руку к истинному величию.",
        "Магия связи через записи позволяет влиять на реальность, не будучи в ней физически. Это тончайший инструмент манипуляции, доступный лишь тем, кто понимает силу слова и тени."
      ]
    },
    {
      t: "Заключение: Власть над Неизбежным",
      p: [
        "Существует лишь одна истинная сила — власть. Всё остальное — лишь иллюзии для слабых. Хогвартс отдаст свои ключи только тому, кто не побоится заглянуть в самую бездну Запретной секции и вернуться оттуда с триумфом.",
        "Я был здесь до вас. Я буду здесь после вас. Моё имя останется в истории, когда ваши имена будут стерты временем.",
        "Твой друг, Том Реддл."
      ]
    }
  ]
},
  {
  id: 48,
  t: "Дневник Полумны",
  a: "Полумна Лавгуд",
  cat: "Фольклор",
  ic: "fa-glasses",
  c: ["#ff69b4", "#da70d6"],
  ch: [
    {
      t: "Глава 1: Мозгошмыги и Гигиена Мыслей",
      p: [
        "Мир полон вещей, которые большинство людей просто разучились замечать. Если вы вдруг почувствовали, что ваши мысли путаются, а в голове стало как-то пусто и туманно, не пугайтесь — это наверняка мозгошмыги (Wrackspurts). Они невидимы и обожают залетать в уши, чтобы навести там беспорядок. Лучший способ избавиться от них — просто энергично помахать руками или подумать о чем-то совершенно неуместном.",
        "Мой папа говорит, что министерство скрывает правду о мозгошмыгах, потому что боится их влияния на выборы. Но мы-то с вами знаем: если ваши мысли ясны, ни один невидимый вредитель не сможет заставить вас совершить глупость. По крайней мере, не ту глупость, которую вы не планировали совершить сами."
      ]
    },
    {
      t: "Глава 2: Нарглы в Омеле",
      p: [
        "Омела — прекрасное растение, но в ней часто заводятся нарглы. Это маленькие озорные существа, которые обожают воровать обувь и другие мелкие вещи, когда вы этого совсем не ждете. Если вы потеряли правый ботинок, не спешите винить домовых эльфов — скорее всего, это нарглы.",
        "Носите ожерелье из пробок от сливочного пива — это отличная защита. Нарглы терпеть не могут запах старого пива и звон пробок. Это выглядит немного эксцентрично, зато ваши вещи всегда будут при вас. Ну, или почти всегда. В конце концов, вещи имеют обыкновение возвращаться к нам самым неожиданным образом."
      ]
    },
    {
      t: "Глава 3: В поисках Морщерогого Кизляка",
      p: [
        "Многие говорят, что Морщерогих кизляков (Crumple-Horned Snorkack) не существует. Но разве отсутствие доказательств — это доказательство отсутствия? Мы с папой планируем экспедицию в Швецию этим летом. Мы уверены, что они живут в низинах, где растет фиолетовый мох. Кизляки очень застенчивы, поэтому увидеть их можно только через Спектрально-астральные очки.",
        "Если вы встретите существо с большим рогом и добрыми глазами — не пугайте его. Попробуйте предложить ему немного сушеного лука. Это их любимое лакомство, хотя об этом не пишут в скучных учебниках профессора Грабли-Дёрг."
      ]
    },
    {
      t: "Заключение: О пользе странностей",
      p: [
        "Быть нормальным — это довольно скучно, вы не находите? Вера в чудеса делает мир ярче, даже если эти чудеса немного кусаются. Не бойтесь, если люди смотрят на вас косо. Это просто значит, что у них в ушах слишком много мозгошмыгов.",
        "Слушайте свое сердце, носите серьги-редиски и помните: самое важное — это друзья. Даже если они иногда забывают, что вы существуете. Я вот никогда не забываю.",
        "С любовью и верой в Кизляков, ваша Полумна."
      ]
    }
  ]
},
  {
  id: 49,
  t: "Я — волшебник",
  a: "Златопуст Локонс",
  cat: "Мемуары",
  ic: "fa-camera",
  c: ["#e1ad01", "#f4e4bc"],
  ch: [
    {
      t: "Глава 1: Моя идеальная улыбка и победа над собой",
      p: [
        "Пять раз подряд я получал приз за самую обаятельную улыбку от еженедельника 'Ведьмин досуг'. Это достижение, которое требует не только природных данных, но и титанического труда над собой. Секрет прост: капля сока мандрагоры для блеска эмали и непоколебимая уверенность в собственном превосходстве.",
        "В этой главе я расскажу, как я (почти в одиночку) победил оборотня из Вагга-Вагга. Пока другие прятались по кустам, я вышел к чудовищу с одной лишь расческой и ослепительной улыбкой. Конечно, пара парализующих заклятий тоже пригодилась, но, поверьте, мой шарм сработал куда эффективнее!",
        "Помните, мои юные поклонники: неважно, насколько сильна ваша магия, если ваша мантия помята, а волосы лишены объема."
      ]
    },
    {
      t: "Глава 2: Борьба с домашними вредителями (и фанатами)",
      p: [
        "Многие спрашивают меня: 'Златопуст, как вам удается сохранять спокойствие, когда на вас нападают корнуолльские пикси?'. Ответ кроется в моей книге 'Прогулки с оборотнями', но я повторю: главное — это контроль над аудиторией. Если пикси утащили вашу палочку, просто примите героическую позу. В 90% случаев они будут так заворожены вашим профилем, что забудут о шалостях.",
        "Что касается фанатов, мой совет: всегда носите с собой пачку подписанных колдографий. Это лучший способ быстро скрыться от толпы, создав эффектное 'звездное' облако. Магия — это шоу, а я — его главный режиссер."
      ]
    },
    {
      t: "Глава 3: Искусство Забвения (Мой маленький секрет)",
      p: [
        "Иногда мне приходится слышать завистливые шепотки о том, что мои подвиги совершил кто-то другой. Какая нелепость! Но я признаюсь: есть одно заклинание, которым я владею в совершенстве — 'Обливиэйт'. Оно помогает людям... э-э... забыть о своих мелких невзгодах и сосредоточиться на моем величии.",
        "Конечно, я использую его только в самых исключительных случаях. Например, когда кто-то слишком настойчиво пытается приписать себе мои лавры. В конце концов, миру нужен один герой, и я с радостью взял на себя эту непосильную ношу."
      ]
    },
    {
      t: "Глава 4: Пожелания будущим героям",
      p: [
        "Если вы хотите стать такими же, как я, начните с подбора правильного оттенка фиолетовой мантии. И никогда не отказывайте в автографе — даже если вы только что упали с метлы. Настоящий волшебник сияет всегда, даже если у него временно... скажем так, стерта память.",
        "С любовью к себе и немного к вам, Златопуст Локонс, кавалер ордена Мерлина третьей степени и почетный член Лиги защиты от темных сил.",
        "P.S. Не забудьте купить мой новый автобиографический календарь 'Год с Златопустом'!"
      ]
    }
  ]
},
  {
  id: 50,
  t: "Тайны нумерологии",
  a: "Бриджит Венлок",
  cat: "Нумерология",
  ic: "fa-list-ol",
  c: ["#2c3e50", "#000000"],
  ch: [
    {
      t: "Глава 1: Магические свойства семи — Золотой стандарт",
      p: [
        "Число семь — это не просто цифра, это костяк магической реальности. Оно обладает уникальной способностью стабилизировать даже самые нестабильные заклинания. В ходе моих исследований в тринадцатом веке я доказала: семерка является мощнейшим ментальным и физическим якорем. Если вы создаете новый артефакт, убедитесь, что количество рун на нем кратно семи.",
        "Это золотое правило спасло не одну сотню жизней начинающих артефакторов. Магия стремится к гармонии, а число семь — это и есть гармония в чистом виде. Семь этажей Хогвартса, семь лет обучения, семь игроков в квиддиче — это не случайности, а следование фундаментальному закону природы.",
        "Помните: попытка обойти нумерологический баланс ведет к магическому откату, который может разорвать само пространство-время."
      ]
    },
    {
      t: "Глава 2: Число Три и Магия Процесса",
      p: [
        "Если семь — это стабильность, то три — это движение. Большинство великих заклинаний требуют трехкратного повторения или трех компонентов. Это число завершенности действия: начало, середина и конец. В нумерологии тройка символизирует рост и расширение магического поля.",
        "При варке зелий помешивание трижды по часовой стрелке часто активирует скрытые свойства ингредиентов, которые игнорируют обычное смешивание. Никогда не недооценивайте силу ритма, основанного на числе три."
      ]
    },
    {
      t: "Глава 3: Опасность Числа Тринадцать",
      p: [
        "Число тринадцать часто называют несчастливым, и в этом есть доля правды, хотя причины лежат глубже суеверий. Тринадцать — это число избыточности. Оно нарушает идеальный двенадцатилетний цикл и вносит хаос в структуру предсказаний. Когда тринадцать человек обедают вместе, первый, кто встанет, столкнется с дестабилизацией своего магического фона.",
        "Это не проклятие, а простая арифметика магических потоков. Если вас тринадцать — просто пригласите четырнадцатого, хотя бы в виде домашнего эльфа или кота, чтобы восстановить баланс."
      ]
    },
    {
      t: "Глава 4: Расчет Будущего по Именам",
      p: [
        "Ваше имя — это числовой код. Переведя буквы вашего имени в цифры по методу Агриппы, вы получите число вашего характера и число вашего пути. Эти данные позволяют предсказать склонность к определенным дисциплинам магии с точностью до 98%.",
        "Математика — это единственный язык, на котором магия говорит без акцента. Учитесь считать, если хотите по-настоящему понимать силу своей палочки.",
        "С уважением к порядку, Бриджит Венлок."
      ]
    }
  ]
},
  {
  id: 51,
  t: "Кухня Хогвартса",
  a: "Эльфы-домовики",
  cat: "Бытовое",
  ic: "fa-bread-slice",
  c: ["#d2691e", "#8b4513"],
  ch: [
    {
      t: "Глава 1: Праздничный пудинг и Ритм Кухни",
      p: [
        "Магия кухни — это магия заботы. Для идеального пудинга нужно взбивать сливки ровно три минуты по часовой стрелке, напевая при этом тихую мелодию. Если сбиться с ритма, пудинг выйдет тяжелым и грустным. Мы, эльфы Хогвартса, знаем: еда чувствует ваше сердце. Никогда не начинайте готовить, если вы в плохом настроении или на кого-то сердитесь, иначе пирог обязательно подгорит, а соус станет кислым.",
        "Праздничные ужины требуют особого подхода. Мы начинаем подготовку за три дня до начала пира. Огромные столы в Большом зале — это верхушка айсберга. Настоящая жизнь кипит под ними, где сотни рук работают в унисон, чтобы каждый студент почувствовал себя как дома."
      ]
    },
    {
      t: "Глава 2: Секреты Сливочного пива и Тыквенного сока",
      p: [
        "Сливочное пиво должно быть теплым, но не обжигающим. Секрет его бархатистого вкуса — в капле растопленного масла из молока магических коров и щепотке сахара Мусковадо. Мы взбиваем пену так долго, пока она не станет похожа на облако, застрявшее в кружке.",
        "Тыквенный сок же требует ледяной свежести. Мы выжимаем тыквы сразу после того, как их приносят с огорода Хагрида. Главное — процедить сок через волшебную марлю, которая задерживает лишнюю горечь, оставляя только сладость осеннего солнца."
      ]
    },
    {
      t: "Глава 3: Исчезающая еда — Телепортация блюд",
      p: [
        "Многие спрашивают, как еда попадает на столы в Большом зале. Всё дело в точной синхронизации. Под каждым столом факультета на кухне стоит точно такой же стол. Когда профессор Дамблдор хлопает в ладоши, мы активируем чары перемещения. Блюда взлетают вверх и занимают свои места за доли секунды.",
        "Это требует огромной концентрации. Если один эльф чихнет в этот момент, индейка может приземлиться на голову декану. Но за последние сто лет у нас не было ни одной осечки! Мы очень гордимся своей точностью."
      ]
    },
    {
      t: "Глава 4: Этикет для невидимых поваров",
      p: [
        "Лучший повар — это тот, кого не видно. Мы чистим котлы до блеска, пока студенты спят, и печем печенье, пока они на уроках. Если вы когда-нибудь забредете на кухню, пощекотав грушу на картине, знайте: вы всегда желанный гость. Мы напоим вас чаем и дадим с собой целый поднос эклеров.",
        "Помните: чистота на кухне — это чистота в душе волшебника. Мойте руки перед тем, как коснуться муки, и всегда благодарите огонь в печи за его тепло.",
        "Приятного аппетита! Ваши верные друзья из подземелий."
      ]
    }
  ]
},
  {
    id: 52,
    t: "Защитные обереги",
    a: "Флимон Поттер",
    cat: "Защита",
    ic: "fa-shield-heart",
    c: ["#b22222", "#4b0082"],
    ch: [
      {
        t: "Снадобье 'Простоблеск'",
        p: [
          "Даже защита может быть стильной. Мой дед изобрел это средство не только для укладки волос, но и для отражения мелких сглазов.",
          "Блестящие волосы — это первый признак того, что волшебник готов к дуэли."
        ]
      }
    ]
  },
  {
    id: 53,
    t: "Мир призраков",
    a: "Профессор Бинс",
    cat: "История",
    ic: "fa-spaghetti-monster-flying",
    c: ["#cfd8dc", "#455a64"],
    ch: [
      {
        t: "Восстание гоблинов",
        p: [
          "Это было в 1612 году. Гоблины требовали права на использование волшебных палочек.",
          "Кровавое сражение произошло прямо в Хогсмиде, в пабе 'Три метлы'.",
          "Хотя, если честно, я могу ошибаться в датах, после смерти память уже не та..."
        ]
      }
    ]
  },
  {
  id: 54,
  t: "Высшая алхимия",
  a: "Парацельс",
  cat: "Алхимия",
  ic: "fa-mortar-pestle",
  c: ["#ff8c00", "#5d4037"],
  ch: [
    {
      t: "Метафизика и лингвистика высшей алхимии: Интегральный анализ Великого Делания и змеиного наречия",
      p: [
        "Высшая алхимия в магическом мире представляет собой не просто примитивную попытку обогащения, а сложнейшую систему метафизического познания, стремящуюся к пониманию глубинных основ мироздания. В отличие от тривиального превращения свинца в золото, которое рассматривается мастерами как побочный эффект или «детская забава», истинное Великое Делание (Magnum Opus) направлено на исследование «невидимого клея», удерживающего структуру материи. Согласно учению Парацельса, алхимия служит мостом между грубой физической формой и магической искрой божественного происхождения, позволяя магу стать истинным хозяином элементов через постижение пропорций первичной субстанции.",
        "Парацельс и философский фундамент высшей алхимии - Филипп Ауреол Теофраст Бомбаст фон Гогенгейм, известный в истории магии как Парацельс, является ключевой фигурой в систематизации высшего алхимического знания. Его подход к магии сочетал в себе медицинский гений и глубокую приверженность герметическим традициям. Парацельс утверждал, что мудрость не может прийти в нечистое сердце, подобно тому как золото не способно родиться в грязном тигле [User Query]. Эта этическая установка подчеркивает, что алхимический процесс неотделим от личной трансформации самого оператора.",
     "Биографический контекст и научное наследие - Парацельс родился в 1493 году и прославился как врач, хирург и алхимик, чьи теории бросили вызов средневековым догмам. В магическом сообществе он признан «скрытным волшебником», чьи работы в области медицины и алхимии оказали влияние на последующие поколения, включая современных исследователей, таких как Альбус Дамблдор и Ньют Скамандер. Его имя стало частью магического лексикона; фраза «Благодаря Парацельсу» используется волшебниками для выражения облегчения, что подчеркивает его статус защитника и спасителя в критических ситуациях.",
"Важным аспектом его деятельности было изучение психотерапии и подсознания. Парацельс верил, что для поддержания здоровья человеку необходим баланс металлов и минералов в организме, что послужило основой для использования алхимических препаратов в целительстве. Его наследие увековечено в Хогвартсе в виде бюста, установленного в одном из коридоров, и карточек в шоколадных лягушках, которые коллекционируют ученики."
	 ],
     dossier: {
        title: "Характеристика: Парацельса",
        data: [
          ["Годы жизни", "1493 — 1541"],
          ["Происхождение", "Австрийский волшебник"],
          ["Основные титулы", "Гений медицины, Секретный алхимик"],
          ["Ключевые достижения", "Открытие Парселтанга, интеграция металлов в медицину"],
          ["Связанные артефакты", "Бюст в Хогвартсе, Карточка шоколадной лягушки №10"]
        ]
      }
    },
	{
      t: "Трансмутация металлов и Дух Материи: Первая глава Делания",
      p: [
        "Первый этап высшей алхимии фокусируется на понимании того, что материя не является инертной субстанцией. Каждое вещество обладает внутренним «духом», который определяет его форму и свойства. Трансмутация свинца в благородный металл является лишь символическим подтверждением того, что маг осознал структуру первичной материи.",
        "Процесс Nigredo: Черная фаза очищения - Процесс очищения, известный как Nigredo, требует от алхимика абсолютной ясности ума и терпения. Это фаза распада и гниения (putrefaction), в ходе которой все внешние атрибуты вещества уничтожаются, чтобы обнажить скрытую суть. В духовном плане Nigredo символизирует «смерть» старой личности мага. Это темный, меланхоличный этап, находящийся под управлением Сатурна и связанный с металлом свинцом.",
        "В высшей алхимии огонь в тигле должен быть ровным, что метафорически указывает на необходимость стабильного эмоционального состояния волшебника [User Query]. Любое отклонение, вызванное гневом или алчностью, приводит к осквернению процесса. Именно поэтому многие, приходящие в алхимию ради золота, терпят неудачу: их «нечистое сердце» не позволяет материи пройти через очистительный огонь Nigredo."
      ],
	  // ВОТ ТВОЯ ТАБЛИЦА:
      stages: [
        { name: "Nigredo", class: "nigredo-bg", symbols: "Черный ворон, жаба", metal: "Свинец", psy: "Растворение эго" },
        { name: "Albedo", class: "albedo-bg", symbols: "Белый лебедь, луна", metal: "Серебро", psy: "Очищение, надежда" },
        { name: "Citrinitas", class: "citrinitas-bg", symbols: "Солнце, желтый орел", metal: "Золото (нач.)", psy: "Пробуждение" },
        { name: "Rubedo", class: "rubedo-bg", symbols: "Феникс, красный лев", metal: "Золото (сов.)", psy: "Бессмертие" }
      ]
    },
    {
      t: "Эликсир Жизни — За гранью плоти",
      p: [
        "Создание Эликсира Жизни считается венцом алхимического искусства. Это субстанция, извлекаемая из Философского камня, способная остановить биологическое время в живой клетке. Однако высшая алхимия предостерегает против бездумного стремления к бессмертию. Парацельс отмечал, что жизнь, продленная искусственно, превращает мага в раба собственной жажды существования, требуя постоянной подпитки",
        "Механика действия и цена бессмертия - Эликсир не дарует абсолютного бессмертия в божественном смысле; он лишь бесконечно отодвигает момент физического распада. Потребитель эликсира остается уязвимым для заклинаний и физических травм. Более того, хотя смерть от старости отступает, тело продолжает изнашиваться, принимая древний и иссохший вид, если алхимик не достиг совершенства в процессе очищения. Пример Николаса Фламеля и его супруги Пернеллы демонстрирует, что лишь при достижении полной гармонии можно сохранять человеческий облик на протяжении столетий. Фламель смог довести процесс до конца, создав Камень, который не только давал золото, но и поддерживал жизнь, не превращая ее в «проклятое существование». В отличие от Волдеморта, который искал бессмертия через насилие и разделение души, Фламель использовал алхимию как инструмент естественного продления жизненного пути до тех пор, пока выполнение его миссии не было завершено."
      ]
    },
	{
	t: "Лингвистика змей: Парселтанг как алхимический ключ",
      p: [
	  "Парселтанг (Змеиный язык) занимает особое место в высшей алхимии. Хотя в массовом сознании он ассоциируется исключительно с Темными искусствами, Парацельс видел в нем глубокий инструмент познания. Именно Парацельсу приписывается заслуга «открытия» Парселтанга в шестнадцатом веке — не в смысле его изобретения, а в смысле его научного описания и каталогизации для магического сообщества.",
	  "Парадокс Парацельса и Парселтанга - Историческая аномалия заключается в том, что такие фигуры, как Герпи Злостный и Салазар Слизерин, использовали змеиный язык за много веков до рождения Парацельса. Тем не менее, именно Парацельс, будучи «гением медицины», смог выделить Парселтанг как отдельный лингвистический и магический феномен, независимый от врожденных способностей. Существует теория, что Парацельс обнаружил возможность имитации или понимания змеиного языка через алхимические процессы, что сделало это знание доступным за пределами прямой наследственности Слизеринов.",
	  "Лингвистические характеристики и магические функцииПарселтанг представляет собой последовательность шипящих и свистящих звуков, которые не-змееустам кажутся бессвязными. Однако для носителя это полноценная речь, воспринимаемая так же ясно, как и любой человеческий язык. Магическая природа Парселтанга позволяет не только общаться с рептилиями, но и манипулировать магическими конструктами, созданными древними мастерами. В высшей алхимии змея — это символ мудрости, исцеления и вечного обновления (через сбрасывание кожи). Способность говорить на ее языке означает доступ к первобытным силам земли и трансформации."
        ]
		},
{
      t: "Таблица высших соответствий Парселтанга",
      p: [
        "Ниже приведен систематизированный свод данных, связывающий лингвистические функции змеиного наречия с практическими магическими действиями и их алхимическими эквивалентами, согласно записям Гогенгейма."
      ],
      analogies: [
        { 
          func: "Управление волей", 
          magic: "Командование василисками и другими змеевидными существами", 
          alchemy: "Контроль над «животной душой» материи" 
        },
        { 
          func: "Ключевая фраза", 
          magic: "Открытие Тайной комнаты, Скрипториума", 
          alchemy: "Доступ к скрытому (оккультному) знанию" 
        },
        { 
          func: "Передача души", 
          magic: "Использование в ритуалах создания Хоркруксов (Герпи Злостный)", 
          alchemy: "Искажение процесса вечной жизни" 
        },
        { 
          func: "Целительство", 
          magic: "Исследование ядов и антидотов (Род Асклепия)", 
          alchemy: "Принцип «подобное лечится подобным»" 
        }
      ]
    }, // <-- ЭТА ЗАПЯТАЯ ОБЯЗАТЕЛЬНА, она отделяет старую главу от новой

    // === ВСТАВЛЯЙ НОВУЮ ГЛАВУ ВОТ СЮДА ===
    {
      t: "Архетип змеи в алхимии и магии",
      p: [
        "Змея является амбивалентным символом. С одной стороны, она представляет опасность и темную магию (Василиск, Нагайна), с другой — является символом медицины и спасения (посох Асклепия). В высшей алхимии образ змеи напрямую связан с «путем змеи» — процессом пробуждения энергии, проходящей через позвоночник мага (аналог Кундалини), что символизирует восхождение души от невежества к просветлению.",
        "Змеиный огонь и трансфигурация сознания - В текстах, приписываемых Парацельсу, упоминается, что «змеиный огонь» — это сила, формирующая ядро личности. Тот, кто способен укротить эту силу внутри себя, получает способность изменять мир вокруг. В контексте истории Хогвартса, Салазар Слизерин выбрал змею своим символом не только из-за личного дара, но и как знак признания этой фундаментальной алхимической силы. Однако использование Парселтанга и змеиной символики для создания Хоркруксов рассматривается высшей алхимией как деградация. Вместо того чтобы позволить змее сбросить старую кожу (смертную оболочку) и возродиться, темный маг пытается «заморозить» процесс распада, создавая статичную, искаженную версию бессмертия.  Скрипториум Слизерина: Алхимический лабиринт В исследование Скрипториума Салазара Слизерина, проведенное в рамках современных изысканий (включая записи из Хогвартс-Наследия), показывает, как глубоко алхимия и Парселтанг были интегрированы в саму структуру школы. Скрипториум — это не просто библиотека, а испытательный полигон для алхимика. Загадки и символика замков - Вход в Скрипториум требует активации трех жаровен огнем, что символизирует первичный этап нагревания материи в Великом Делании. Дальнейшие преграды в виде дверей, управляемых Парселтангом, требуют от вошедшего не только знания языка, но и понимания нумерологических и алхимических соответствий."
      ],
      dossier: {
        title: "Алхимическая нумерология существ",
        data: [
          ["Демигуиза (0)", "Пустота, невидимый эфир"],
          ["Единорог (1)", "Первичное единство (Monas)"],
          ["Грапхорн (2)", "Дуализм серы и ртути"],
          ["Рунспур (3)", "Триада (Соль, Сера, Ртуть)"],
          ["Фвупер (4)", "Четыре элемента (Земля, Воздух, Огонь, Вода)"],
          ["Квинтапед (5)", "Квинтэссенция, пятый элемент"],
          ["Саламандра (6)", "Философский огонь"],
          ["Осьминог (7)", "Семь планетных влияний"],
          ["Паук (8)", "Гармония материальных структур"],
          ["Гидра (9)", "Завершение материального цикла"]
        ]
      }
    },
{ t: "Вещества и ингредиенты Высшей работы - Согласно текстам 1557 года, переведенным на магический латинский, компоненты «совершенного лекарства» включают в себя такие субстанции:",
      p: [
        "как Vinegar (Уксус), Salt (Соль), Urine (Моча), Sal Ammoniac (Нашатырь) и Sulphur Vive (Живая Сера). Эти ингредиенты не являются просто химическими реактивами; в высшей алхимии они представляют собой энергетические состояния. риада начал: Соль, Сера и Ртуть. В системе Парацельса всё сущее состоит из трех начал (Tria Prima): Сера (Sulphur): Дух, горючесть, мужское начало. Символизирует волю мага. Ртуть (Mercury): Душа, летучесть, женское начало. Символизирует магическую интуицию и связь с астральным планом. «Python Mercury» (Змеиная ртуть) — это особая форма очищенной ртути, используемая для стабилизации Эликсира.",
        "Николас Фламель, наиболее известный мастер этой дисциплины, подчеркивал, что Камень — это не просто инструмент для получения золота, а зеркало чистоты духа. Если алхимик сохраняет в себе хоть тень алчности или гнева, Камень обратится в пыль, не успев коснуться свинца. В этом и заключается защита 'Высшей алхимии' от недостойных. Соль (Salt): Тело, плотность, материальная основа. Символизирует физическую оболочку.",
      "Алхимик должен уметь разделять эти начала (Spagyric) и вновь соединять их в очищенном виде. Этот процесс позволяет достичь «Chrysopoeia» — не только производства золота, но и облагораживания самой души волшебника."
	  ],
      analogies: [
        { 
          func: "Живая Сера (Sulphur Vive)", 
          magic: "Энергия горения", 
          alchemy: "Трансмутация основ" 
        },
        { 
          func: "Змеиная Ртуть (Python Mercury)", 
          magic: "Посредник духа", 
          alchemy: "Связующее звено форм" 
        },
        { 
          func: "Соль Аммония (Sal Ammoniac)", 
          magic: "Реагент возгонки", 
          alchemy: "Глубокая очистка" 
        },
        { 
          func: "Вода Жизни (Aqua Vitæ)", 
          magic: "Растворитель эссенций", 
          alchemy: "Экстракция чистой силы" 
        },
        { 
          func: "Alkahest", 
          magic: "Разделение на первоэлементы", 
          alchemy: "Универсальный растворитель" 
        }
      ]
    }

    // =====================================

  ] // Эта скобка закрывает массив всех глав (ch)
}, // Эта скобка закрывает всю книгу (id: 54)
 {
  id: 56,
  t: "Лесные нимфы",
  a: "Ксенофилиус Лавгуд",
  cat: "Фольклор",
  ic: "fa-wand-sparkles",
  c: ["#e91e63", "#880e4f"],
  ch: [
    {
      t: "Глава 1: Где искать фей и как им понравиться",
      p: [
        "Феи — существа крайне тщеславные. Если вы хотите их увидеть, не нужно использовать заклинания обнаружения, они их пугают. Просто оставьте на старом мшистом пне зеркальце, блестящую пуговицу или кусочек сахара. Феи обожают своё отражение и всё, что переливается на солнце.",
        "Они не разговаривают с магами (слишком заняты самолюбованием), но могут указать путь к спрятанным сокровищам или редким травам, если им по-настоящему понравится ваш подарок. Но будьте осторожны: если вы попытаетесь поймать фею в банку, она нашлет на вас икоту, которая продлится ровно три лунных цикла."
      ]
    },
    {
      t: "Глава 2: Танцы в кругах грибов",
      p: [
        "Лесные нимфы часто путают следы, создавая так называемые 'ведьмины круги' из грибов. Если вы случайно забрели в такой круг, немедленно наденьте шляпу задом наперед. Это собьет нимф с толку и позволит вам выйти. Помните: нимфы видят мир не так, как мы, они смотрят сквозь время, поэтому их советы всегда звучат как загадки."
      ]
    }
  ]
},
  {
  id: 57,
  t: "Справочник по мётлам",
  a: "Циклон",
  cat: "Полеты",
  ic: "fa-wind",
  c: ["#03a9f4", "#01579b"],
  ch: [
    {
      t: "Глава 1: Нимбус-2000 vs 2001 — Битва технологий",
      p: [
        "Многие новички совершают ошибку, выбирая модель только по ценнику. Нимбус-2001 безусловно быстрее на прямой дистанции, но 2000-я модель (классика!) гораздо отзывчивее на крутых поворотах. Инженеры из 'Скоростных мётел Нимбус' в погоне за скоростью немного пожертвовали углом атаки хвоста.",
        "Для ловца важна маневренность и способность мгновенно менять вектор движения, а не только чистая тяга. Если вы весите меньше восьми стоунов, 2001-я модель может показаться вам слишком 'жесткой' в управлении. Мой совет: проверьте полировку прутьев. Плохая полировка увеличивает сопротивление воздуха на 5%."
      ]
    },
    {
      t: "Глава 2: Уход за хвостом в домашних условиях",
      p: [
        "Никогда не используйте обычное масло для дерева! Только специализированную смазку 'Летучая ласка'. Один кривой прутик в хвосте может привести к заносу на левое крыло во время выполнения финта Вронского. Стрижка хвоста должна быть симметричной, иначе вибрация на высоких скоростях станет невыносимой."
      ]
    }
  ]
},
  {
  id: 58,
  t: "Тайны русалок",
  a: "Меркус",
  cat: "Магозоология",
  ic: "fa-water",
  c: ["#00bcd4", "#006064"],
  ch: [
    {
      t: "Глава 1: Песни Черного озера — Истина в глубине",
      p: [
        "На суше наши голоса звучат для вас как скрежет металла по камню, но это лишь потому, что воздух слишком груб для истинной гармонии. Под водой, в объятиях холодного течения, это самая прекрасная музыка в мире, способная усыпить кракена или заставить камни плакать. Мы не поем для тех, кто дышит воздухом; мы поем для самой бездны.",
        "Русалки Хогвартса — гордый народ с древней историей. Не пытайтесь заплывать в нашу деревню без приглашения, если не хотите познакомиться с нашими трезубцами. Мы не домашние рыбки в пруду, мы — хозяева глубин, и наше гостеприимство нужно заслужить, как это когда-то сделал великий бородатый старик из замка."
      ]
    },
    {
      t: "Глава 2: Язык пузырьков и теней",
      p: [
        "Маги часто совершают ошибку, пытаясь говорить с нами, используя заклинания перевода. Истинное понимание приходит через вибрацию воды. Мы видим ваши намерения по тому, как меняется температура вокруг вашего тела. Если вы пришли с миром, вода останется спокойной. Если в вашем сердце тьма — озеро само вытолкнет вас на берег, как ненужный мусор."
      ]
    }
  ]
},
  {
  id: 59,
  t: "Право магов",
  a: "Министерство Магии",
  cat: "Законы",
  ic: "fa-scale-balanced",
  c: ["#607d8b", "#263238"],
  ch: [
    {
      t: "Глава 1: Международный Статут о секретности",
      p: [
        "Статья 13: Любое использование магии в присутствии магла (немагического населения) карается немедленным исключением из школы и уничтожением волшебной палочки без права на апелляцию. Магический мир обязан оставаться невидимым. Ваше любопытство или желание похвастаться не стоят безопасности всей нации.",
        "Исключение составляют только документально подтвержденные случаи непосредственной угрозы жизни мага или окружающих. Помните: Сектор борьбы с неправомерным использованием магии отслеживает каждый ваш взмах палочки через 'Надзор' (The Trace). Мы узнаем о вашем 'Люмосе' раньше, чем он успеет погаснуть."
      ]
    },
    {
      t: "Глава 2: Запрет на экспериментальную селекцию",
      p: [
        "Создание новых видов магических существ без лицензии Министерства запрещено указом от 1792 года. Нарушители, пытающиеся вывести 'огнедышащих крабов' или 'летающих свиней', будут отправлены в Азкабан на срок до пяти лет. Порядок в природе — это порядок в государстве."
      ]
    }
  ]
},
  {
    id: 60,
    t: "Магия любви",
    a: "Амортенция",
    cat: "Зельеварение",
    ic: "fa-heart",
    c: ["#f06292", "#ad1457"],
    ch: [
      {
        t: "Феноменология и алхимия Амортенции: Комплексное исследование сильнейшего приворотного зелья и Введение в теорию магического принуждения",
      p: [
        "В академической среде зельеварения Амортенция классифицируется не просто как любовный напиток, но как наиболее мощное и опасное средство химической суггестии и эмоционального подавления. Понимание природы этого субстанции требует глубокого анализа дихотомии между истинным чувством и его искусственным симулякром. В то время как подлинная любовь является результатом свободного выбора, жертвенности и времени, Амортенция генерирует состояние, которое можно охарактеризовать как тотальную обсессию или безумное увлечение.Фундаментальная опасность этого зелья заключается в его способности превращать субъекта в «бледную тень самого себя», лишая его автономности и воли [User Query]. Жертва Амортенции становится ведомой чужой волей, при этом её поведение деформируется до неузнаваемости, что делает это зелье более опасным инструментом, чем Сыворотка Правды или Оборотное зелье. В рамках магической юриспруденции и этики использование Амортенции часто приравнивается к нарушению базовых прав личности на когнитивную свободу.Физико-химические свойства и идентификацияИдентификация Амортенции в лабораторных условиях основывается на ряде уникальных визуальных и кинетических признаков, которые отличают её от менее сложных приворотных составов, таких как «Кристаллы Купидона» или «Любовная эссенция».Визуальные и динамические маркерыПервым и наиболее очевидным признаком правильно приготовленной Амортенции является её характерный перламутровый блеск. Эта иризация поверхности напоминает внутреннюю сторону раковины моллюска, создавая эффект переливающихся слоев. Второй ключевой маркер — пар, который поднимается из котла не хаотично, а изящными, характерными спиралями. Эти спирали символизируют циклическую, самозамкнутую природу одержимости, которую вызывает напиток.",
        ],
      /* ДОСЬЕ ВНУТРИ ГЛАВЫ */
      dossier: {
        title: "Лабораторный паспорт: Amortentia",
        data: [
          ["Поверхностное натяжение", "Иризация, перламутровый отблеск. Жемчужная пыль ✧"],
          ["Кинетика пара", "Устойчивые спиралевидные завихрения. Магическая плотность ♨"],
          ["Цвет состава", "Прозрачный с металлическим отливом. Высокая очистка ✨"],
          ["Вязкость", "Средняя, текучая. Высокая степень диффузии 🧪"]
        ]
      }
    },
      {
        t: "Ольфакторная адаптивность: зеркало подсознания",
        p: [
          "Самым интригующим и психологически значимым свойством Амортенции является её аромат. В отличие от стандартных эликсиров, обладающих фиксированным запахом, Амортенция пахнет для каждого человека по-разному, адаптируясь к его самым сокровенным предпочтениям и воспоминаниям. Это явление описывается как «голос подсознания, превращенный в жидкость» нализ клинических случаев показывает, что запахи, воспринимаемые субъектами, часто связаны с глубокими эмоциональными привязками:Гарри Поттер в 1996 году ощутил аромат паточного пирога (комфорт), запах древка метлы (страсть к полетам) и цветочный аромат, ассоциирующийся с волосами Джинни Уизли.Гермиона Грейнджер зафиксировала запахи свежескошенной травы (природа), нового пергамента (интеллектуальные стремления) и аромат волос Рона Уизли.Эта адаптивность делает Амортенцию идеальной ловушкой: аромат взывает к тому, что человек находит наиболее привлекательным, даже если он сам еще не осознает своей симпатии к объекту.",
          "Алхимический состав: Ингредиенты и их символизм-Приготовление Амортенции требует исключительной точности и использования редких компонентов, каждый из которых несет в себе определенный магический потенциал. Ошибка в пропорциях или температуре может превратить любовный напиток в смертельный яд [User Query]. Биологические компоненты: Пеплозмей Ключевым элементом, обеспечивающим «жар» и интенсивность чувств, являются замороженные яйца пеплозмея. Пеплозмей — это магическая змея, возникающая из искр магического огня, который был оставлен без присмотра. 🎔",
        "Использование яиц пеплозмея в составе Амортенции объясняет её «огненную» природу: одержимость вспыхивает мгновенно, подобно пожару в доме, вызванному ненайденным гнездом этого существа.Ботанические и минеральные составляющиеПомимо животных компонентов, формула включает в себя ряд растительных и минеральных элементов, собранных с учетом лунных циклов.Лепестки розы: Собранные в полнолуние, они служат классическим символом красоты и романтики, однако в контексте Амортенции их роль заключается в создании эстетической иллюзии.Мята (Peppermint): Используется для стимуляции чувств и придания зелью бодрящего, почти возбуждающего эффекта.Лунный камень (Moonstone): В порошкообразном виде он стабилизирует эмоциональные колебания состава, делая эффект зелья более устойчивым.Жемчужная пыль (Pearl Dust): Именно она дарит напитку его знаменитый блеск и завершает процесс гармонизации ингредиентов.Технологический протокол приготовленияПроцесс варки Амортенции — это испытание для мастера-зельевара. В профессиональных руководствах, таких как «Расширенный курс зельеварения», подчеркивается, что секретный ингредиент всегда остается за гранью чистой химии — это способность самого мага чувствовать и сопереживать [User Query].Этапы синтезаСогласно историческим рецептурам, процесс занимает от 9 до 21 дня, в зависимости от используемого типа котла (золотой, серебряный или медный).Подготовительная фаза: В котел (предпочтительно золотой для максимальной чистоты) заливается стандартная вода для зелий и нагревается на очень медленном огне.Введение стабилизаторов: Мята растирается в ступке и добавляется в воду вместе с порошком лунного камня. На этом этапе критически важно перемешивание против часовой стрелки.Экстракция страсти: Замороженные яйца пеплозмея вводятся по одному. В этот момент состав начинает выделять тепло, и мастер должен тщательно контролировать температуру, чтобы не допустить денатурации белков магического существа.Персонализация: Для того чтобы зелье работало на конкретного человека, в него часто добавляется «якорь» — прядь волос или капля крови того, в кого жертва должна влюбиться.Математическое обоснование стабильности составаМагическая стабильность Амортенции может быть описана через коэффициент эмоциональной резонансности , который зависит от точности температурного режима  и интенсивности магического поля ",
		"Где  — угол наклона палочки при финальном пассе, а — время созревания состава. Малейшее отклонение в знаменателе (температуре) приводит к экспоненциальному росту токсичности продукта.Клиническая картина и симптоматологияВоздействие Амортенции на человеческий организм носит инвазивный характер. Симптомы проявляются практически мгновенно после употребления.Физиологические измененияЖертва часто приобретает «болезненную бледность», что свидетельствует о том, что магическая энергия зелья перераспределяет ресурсы организма на поддержание эмоционального возбуждения в ущерб физическому здоровью. Наблюдается неестественный блеск в глазах и повышенная возбудимость.Психопатологические проявленияКлючевым признаком является возникновение фатальной зависимости. Человек начинает говорить об объекте своей «любви» так, будто он озарен «лучами чистейшего солнечного света». Все рациональные аргументы отвергаются; приоритеты (учеба, работа, семья) полностью игнорируются в пользу объекта обсессии. В запущенных случаях, когда зелье принимается долго, одержимость может перерасти в опасную эмоциональную нестабильность и даже агрессию по отношению к конкурентам.Противоядия и методы нейтрализацииНесмотря на мощь Амортенции, она не является необратимой. Существует специфическое «Противоядие от любовных зелий», которое способно мгновенно снять пелену с глаз.Состав и приготовление антидотаАнтидот представляет собой прозрачную, бесцветную жидкость, хотя в некоторых версиях он может иметь розовый или красный оттенок до финальной стадии очистки.",
		"Процесс деконструкции чарПри введении антидота зелье проходит через ряд цветовых трансформаций, что является визуальным подтверждением распада магических связей Амортенции:Смесь становится зеленой после добавления солодки.Переходит в оранжевый при перемешивании.Становится синей и фиолетовой после введения касторового масла.Краснеет при добавлении экстракта Гурдикорня.В финале становится розовой и, наконец, прозрачной.Важно понимать: антидот лечит химическую зависимость, но не «разбитое сердце». Постыдные воспоминания о совершенных поступках остаются с человеком навсегда.Исторические и социальные кейс-стадиИстория магического мира знает немало примеров того, как Амортенция меняла судьбы целых наций и династий.Трагедия Меропы Гант и Тома Реддла-старшегоНаиболее значимым примером является случай Меропы Гант, которая использовала любовное зелье (вероятно, именно Амортенцию), чтобы заставить богатого магла Тома Реддла жениться на ней. Этот случай демонстрирует «эффект привыкания»: когда Меропа перестала давать мужу зелье, надеясь, что он полюбил её по-настоящему, он немедленно бросил её.Последствия этого союза были катастрофическими. Считается, что зачатие под действием Амортенции наложило отпечаток на личность их сына, который впоследствии стал Лордом Волан-де-Мортом. Хотя современные исследователи спорят, было ли его неумение любить биологическим следствием зелья или результатом отсутствия материнской заботы, очевидно, что Амортенция послужила катализатором этой трагедии.Инцидент с Ромильдой Вейн и Роном УизлиВ 1997 году студентка Ромильда Вейн попыталась подсыпать Амортенцию Гарри Поттеру в шоколадные котелки. Однако их случайно съел Рон Уизли. Этот инцидент показал, что чем дольше хранится зелье в продукте, тем сильнее становится его эффект — Рон впал в состояние практически невменяемой эйфории, что едва не закончилось трагедией из-за последующего отравления медовухой.Этический и юридический статусВопрос легальности Амортенции остается предметом острых дискуссий. С одной стороны, любовные зелья запрещены в Хогвартсе как опасные предметы. С другой стороны, они открыто продаются в магазинах приколов под видом «шуточных» товаров.Амортенция как инструмент насилияМногие правозащитники в магическом мире приравнивают Амортенцию к «наркотику для свиданий» (date rape drug). Она лишает человека возможности дать осознанное согласие на отношения. Тот факт, что это зелье не классифицируется Министерством Магии как «Непростительное», вызывает критику со стороны экспертов по этике.Коммерциализация: Продукция близнецов УизлиМагазин «Всевозможные волшебные вредилки» предлагает целый спектр любовных напитков. Несмотря на их игривые названия, такие как «Кристаллы Купидона», их механизм действия схож с Амортенцией, пусть и в ослабленном виде."
		]
    }
  ]
},

];
const SPELLS = [
  {n:'Алохомора',i:'Алохомора',d:'Открывает запертые двери, окна и сундуки. Не работает против сложных магических замков и гоблинских печатей.',t:'Бытовое',cat:'daily',ic:'fa-lock-open'},
  {n:'Люмос',i:'Люмос',d:'Зажигает тёплый свет на конце палочки. Нокс гасит. Незаменимое заклинание для передвижения в темноте.',t:'Бытовое',cat:'daily',ic:'fa-lightbulb'},
  {n:'Нокс',i:'Нокс',d:'Гасит свет, созданный заклинанием Люмос. Простейшее заклинание, доступно даже первокурсникам.',t:'Бытовое',cat:'daily',ic:'fa-moon'},
  {n:'Акцио',i:'Акцио + предмет',d:'Призывает предмет к волшебнику. Дальность зависит от силы и массы. Нельзя призвать живых существ.',t:'Бытовое',cat:'daily',ic:'fa-magnet'},
  {n:'Репаро',i:'Репаро',d:'Восстанавливает разбитые предметы. Не работает на магически разрушенных. Слегка склеивает осколки.',t:'Бытовое',cat:'daily',ic:'fa-screwdriver-wrench'},
  {n:'Вингардиум Левиоса',i:'Вингардиум Левиоса',d:'Левитация объектов. Требует точного взмаха «и шёлковисто». Высота и длительность зависят от навыка.',t:'Бытовое',cat:'daily',ic:'fa-feather'},
  {n:'Экспеллиармус',i:'Экспеллиармус',d:'Выбивает палочку из руки противника. Подписное заклинание Гарри Поттера. Создаёт красную вспышку.',t:'Боевое',cat:'combat',ic:'fa-hand'},
  {n:'Ступефай',i:'Ступефай',d:'Оглушает противника. Красная вспышка. Жертва теряет сознание на несколько секунд.',t:'Боевое',cat:'combat',ic:'fa-bolt'},
  {n:'Петрификус Тоталус',i:'Петрификус Тоталус',d:'Полный паралич тела. Руки и ноги слипаются. Жертва осознаёт происходящее, но не может пошевелиться.',t:'Боевое',cat:'combat',ic:'fa-person-falling'},
  {n:'Протего',i:'Протего',d:'Магический щит. Отражает заклинания обратно. Существует долю секунды — нельзя поставить заранее.',t:'Защитное',cat:'light',ic:'fa-shield-halved'},
  {n:'Протего Максима',i:'Протего Максима',d:'Усиленный щит-купол. Защищает группу людей. Использован при обороне Хогвартса.',t:'Защитное',cat:'light',ic:'fa-shield'},
  {n:'Ридикулус',i:'Ридикулус',d:'Превращает боггарта в смешную форму. Смех разрушает существо. Требует концентрации на абсурде.',t:'Защитное',cat:'light',ic:'fa-face-laugh'},
  {n:'Флиппендо',i:'Флиппендо',d:'Отбивающее заклинание. Толкает объект или противника. Учебное заклинание первого курса.',t:'Боевое',cat:'combat',ic:'fa-hand-back-fist'},
  {n:'Депульсо',i:'Депульсо',d:'Отталкивает объект от волшебника. Противоположность Акцио. Сила зависит от воли.',t:'Боевое',cat:'combat',ic:'fa-arrows-alt'},
  {n:'Инсендио',i:'Инсендио',d:'Создаёт направленный огонь. Температура регулируется силой заклинания. Не работает под водой.',t:'Боевое',cat:'combat',ic:'fa-fire-flame-curved'},
  {n:'Конфринго',i:'Конфринго',d:'Взрывное проклятие. Вызывает мощный огненный взрыв в точке попадания. Крайне опасно в узких пространствах.',t:'Боевое',cat:'combat',ic:'fa-fire-burner'},
  {n:'Диффиндо',i:'Диффиндо',d:'Разрезает объекты. Точность определяет глубину разреза. Может разрезать магические ткани.',t:'Боевое',cat:'daily',ic:'fa-scissors'},
  {n:'Обливиейт',i:'Обливиейт',d:'Стирает воспоминания. Селективно или полностью. Неконтролируемое применение — непоправимый вред психике.',t:'Ментальное',cat:'mental',ic:'fa-eraser'},
  {n:'Конфундо',i:'Конфундо',d:'Вызывает путаницу и дезориентацию. Жертва принимает ошибочные решения. Использовалось во время войны.',t:'Ментальное',cat:'mental',ic:'fa-circle-nodes'},
  {n:'Легилименс',i:'Легилименс',d:'Чтение мыслей через зрительный контакт. Видны воспоминания и эмоции в виде потоков образов.',t:'Ментальное',cat:'mental',ic:'fa-eye'},
  {n:'Империо',i:'Империо',d:'Полный контроль над волей жертвы. Непростительное. Сильный волшебник может сопротивляться. Пожизненное Азкабан.',t:'Тёмное',cat:'dark',ic:'fa-brain'},
  {n:'Круцио',i:'Круцио',d:'Причиняет невыносимую боль. Требует искреннее желание причинить боль. Непростительное.',t:'Тёмное',cat:'dark',ic:'fa-bolt-lightning'},
  {n:'Авада Кедавра',i:'Авада Кедавра',d:'Мгновенная смерть. Вспышка зелёного света. Нет защиты. Единственное выживание — Гарри Поттер. Непростительное.',t:'Тёмное',cat:'dark',ic:'fa-skull'},
  {n:'Сектумсемпра',i:'Сектумсемпра',d:'Тёмное заклинание Снейпа. Глубокие порезы, как невидимый меч. Может быть смертельным. Восанги замедляет.',t:'Тёмное',cat:'dark',ic:'fa-droplet'},
  {n:'Мортморде',i:'Мортморде',d:'Вызывает Знак Тёмного Лорда — череп со змеёй на небе. Сигнал Пожирателей. Виден на огромном расстоянии.',t:'Тёмное',cat:'dark',ic:'fa-cloud'},
  {n:'Мобиликорпус',i:'Мобиликорпус',d:'Перемещает неподвижное тело. Использовался Снейпом для переноса тела Поттера.',t:'Бытовое',cat:'daily',ic:'fa-person-walking'},
  {n:'Агуаменти',i:'Агуаменти',d:'Создаёт струю чистой питьевой воды из палочки. Напор регулируется.',t:'Бытовое',cat:'daily',ic:'fa-droplet'},
  {n:'Феррула',i:'Феррула',d:'Создаёт магические бинты из воздуха. Обездвиживает конечность и останавливает кровотечение.',t:'Целительное',cat:'heal',ic:'fa-bandage'},
  {n:'Эписки',i:'Эписки',d:'Лечит мелкие повреждения: порезы, синяки, вывихи. Не работает на магических ранах и проклятиях.',t:'Целительное',cat:'heal',ic:'fa-heart-pulse'},
  {n:'Коллокпортус',i:'Коллокпортус',d:'Магически запирает дверь. Даже ключ не поможет. Можно сочетать с Алохоморой для ловушек.',t:'Бытовое',cat:'daily',ic:'fa-lock'},
  {n:'Экспекто Патронум',i:'Экспекто Патронум',d:'Вызывает Патронуса для защиты от дементоров. Требует концентрации на самом счастливом воспоминании.',t:'Защитное',cat:'light',ic:'fa-wand-magic-sparkles'},
  {n:'Приори Инкантатем',i:'Приори Инкантатем',d:'Показывает последнее заклинание, выпущенное палочкой. При столкновении родственных палочек создает золотую сеть.',t:'Информационное',cat:'daily',ic:'fa-history'},
  {n:'Сонорус',i:'Сонорус',d:'Магический рупор. Усиливает громкость голоса до громоподобного уровня. Квиетус возвращает голос в норму.',t:'Бытовое',cat:'daily',ic:'fa-volume-high'},
  {n:'Квиетус',i:'Квиетус',d:'Возвращает голос в нормальное состояние после использования Сонорус. Магическая тишина.',t:'Бытовое',cat:'daily',ic:'fa-volume-low'},
  {n:'Импедимента',i:'Импедимента',d:'Тормозящее заклинание. Замедляет или полностью останавливает объект или человека на короткое время.',t:'Боевое',cat:'combat',ic:'fa-hand-stop'},
  {n:'Редукто',i:'Редукто',d:'Разрушающее заклинание. Превращает твердые объекты в пыль или мелкие осколки. Неэффективно против живых целей.',t:'Боевое',cat:'combat',ic:'fa-burst'},
  {n:'Оппуньо',i:'Оппуньо',d:'Заставляет вызванных существ или зачарованные предметы атаковать указанную цель. Использовалось Гермионой (птицы).',t:'Боевое',cat:'combat',ic:'fa-dove'},
  {n:'Эванеско',i:'Эванеско',d:'Заклинание исчезновения. Заставляет предмет исчезнуть «в небытие». Одно из сложнейших заклинаний на СОВ.',t:'Бытовое',cat:'daily',ic:'fa-ghost'},
  {n:'Тергео',i:'Тергео',d:'Всасывает жидкости (кровь, грязь, вино) с поверхностей. Деликатный аналог Экскуро для очистки ран или пергаментов.',t:'Целительное',cat:'heal',ic:'fa-soap'},
  {n:'Экскуро',i:'Экскуро',d:'Очищающее заклинание. Создает мыльную пену и отмывает грязь. Бытовой аналог Тергео для посуды или одежды.',t:'Бытовое',cat:'daily',ic:'fa-bubbles'},
  {n:'Силенцио',i:'Силенцио',d:'Заклинание немоты. Лишает жертву возможности издавать звуки. Часто используется в дуэлях против болтливых магов.',t:'Ментальное',cat:'mental',ic:'fa-comment-slash'},
  {n:'Флагрейт',i:'Флагрейт',d:'Позволяет рисовать в воздухе или на предметах светящиеся огненные знаки (кресты, буквы).',t:'Бытовое',cat:'daily',ic:'fa-pen-nib'},
  {n:'Апарекиум',i:'Апарекиум',d:'Проявляет невидимые чернила или скрытые сообщения на пергаменте. Магическое обнаружение.',t:'Информационное',cat:'daily',ic:'fa-file-magnifying-glass'},
  {n:'Пиертотум Локомотор',i:'Пиертотум Локомотор',d:'Оживляет статуи и доспехи, заставляя их выполнять приказы мага. Мощная боевая трансфигурация.',t:'Защитное',cat:'light',ic:'fa-chess-rook'},
  {n:'Сальвио Гексия',i:'Сальвио Гексия',d:'Оберегающие чары. Делают область невидимой для нежелательных гостей и защищают от обнаружения.',t:'Защитное',cat:'light',ic:'fa-eye-slash'},
  {n:'Глиссео',i:'Глиссео',d:'Превращает лестницу в ровную покатую горку. Использовалось для защиты женских спален в Хогвартсе.',t:'Бытовое',cat:'daily',ic:'fa-stairs'},
  {n:'Метеолоджикс Реканто',i:'Метеолоджикс Реканто',d:'Прекращает действие магических погодных эффектов (дождь или снег внутри помещения).',t:'Бытовое',cat:'daily',ic:'fa-cloud-sun'},
  {n:'Драконифорс',i:'Драконифорс',d:'Превращает небольшие предметы в крошечных живых дракончиков, подчиняющихся магу.',t:'Трансфигурация',cat:'daily',ic:'fa-dragon'},
  {n:'Мортмордре',i:'Мортмордре',d:'Вызывает Черную Метку в небе. Используется исключительно Пожирателями Смерти.',t:'Тёмное',cat:'dark',ic:'fa-skull-crossbones'},
  {n:'Протего Тоталум',i:'Протего Тоталум',d:'Создаёт защитный купол над большой территорией. Защищает от большинства тёмных заклинаний и обнаружения.',t:'Защитное',cat:'light',ic:'fa-shield-cat'},
  {n:'Инкарцеро',i:'Инкарцеро',d:'Связывает противника толстыми верёвками, которые появляются из ниоткуда. Путает ноги и прижимает руки к телу.',t:'Боевое',cat:'combat',ic:'fa-mound'},
  {n:'Анапнео',i:'Анапнео',d:'Очищает дыхательные пути. Позволяет спасти человека, если он поперхнулся или задыхается.',t:'Целительное',cat:'heal',ic:'fa-lungs'},
  {n:'Левикорпус',i:'Левикорпус',d:'Поднимает жертву за щиколотку и удерживает вниз головой. Невербальное, изобретено Снейпом.',t:'Боевое',cat:'combat',ic:'fa-arrow-up-long'},
  {n:'Либеракорпус',i:'Либеракорпус',d:'Контрзаклинание для Левикорпуса. Роняет жертву на землю.',t:'Бытовое',cat:'daily',ic:'fa-arrow-down-long'},
  {n:'Энгорджио',i:'Энгорджио',d:'Увеличивает объект в размерах. Редуцио возвращает в исходное состояние.',t:'Бытовое',cat:'daily',ic:'fa-maximize'},
  {n:'Вунера Санентур',i:'Вунера Санентур',d:'Исцеляющее заклинание, затягивающее глубокие раны. Требует троекратного повторения: первое останавливает кровотечение, второе очищает рану, третье заживляет её.',t:'Целительное',cat:'heal',ic:'fa-hand-holding-medical'},
  {n:'Каве Инимикум',i:'Каве Инимикум',d:'Защитное заклинание, создающее невидимый барьер. Оно предупреждает мага о приближении врагов к защищенной зоне.',t:'Защитное',cat:'light',ic:'fa-bell'},
  {n:'Редуцио',i:'Редуцио',d:'Возвращает увеличенным объектам их нормальный размер. Контрзаклинание для Энгорджио.',t:'Бытовое',cat:'daily',ic:'fa-minimize'},
  {n:'Пискипитус',i:'Пискипитус',d:'Заклинание, превращающее объект в рыбу. Основа школьной программы по трансфигурации.',t:'Бытовое',cat:'daily',ic:'fa-fish'},
  {n:'Десцендо',i:'Десцендо',d:'Заставляет объект опуститься вниз или упасть. Часто используется для открытия чердачных люков или спуска лестниц.',t:'Бытовое',cat:'daily',ic:'fa-arrow-down'},
  {n:'Дуро',i:'Дуро',d:'Заклинание окаменения. Превращает мягкие объекты (например, ткань или гобелен) в твердый камень.',t:'Бытовое',cat:'daily',ic:'fa-gem'},
  {n:'Гермивизио',i:'Гермивизио',d:'Позволяет видеть сквозь твердые поверхности, такие как стены или двери. Редкое заклинание магического зрения.',t:'Информационное',cat:'daily',ic:'fa-binoculars'},
  {n:'Ваддивази',i:'Ваддивази',d:'Заставляет летящий объект (например, жвачку в замочной скважине) вылететь с огромной скоростью в выбранную цель.',t:'Боевое',cat:'combat',ic:'fa-rocket'},
  {n:'Локомотор Мортис',i:'Локомотор Мортис',d:'Заклинание подножки. Склеивает ноги жертвы вместе, мешая ей передвигаться.',t:'Боевое',cat:'combat',ic:'fa-shoe-prints'},
  {n:'Протего Нерибус',i:'Протего Нерибус',d:'Специфическая форма щита, защищающая от физических объектов (стрел, камней), а не только от магии.',t:'Защитное',cat:'light',ic:'fa-shield-virus'},
  {n:'Дженнивизио',i:'Дженнивизио',d:'Позволяет видеть сквозь твёрдые поверхности (стены, двери). Редкое заклинание магического зрения.',t:'Информационное',cat:'daily',ic:'fa-binoculars'}
];
const POTIONS = [
    { n: 'Зелье от простуды', d: 'Снимает симптомы за 30 минут. Классика первого курса.', diff: 1, ing: ['Корень мартына', 'Листья крапивы', 'Талая вода'], eff: 'Излечение простуды', color: '#4a8c3f', cat: 'Целительные', form: 'classic', rare: 'Common' },
    { n: 'Усыпляющее зелье', d: 'Вызывает глубокий сон. Передозировка ведет к коме.', diff: 1, ing: ['Лавандовые стебли', 'Стандартная основа', 'Порошок сна'], eff: 'Глубокий сон', color: '#6a5acd', cat: 'Бытовые', form: 'round', rare: 'Common' },
    { n: 'Зелье от прыщей', d: 'Очищает кожу. Популярно среди студентов Хогвартса.', diff: 1, ing: ['Кожа змеи', 'Листья герани', 'Корень первоцвета'], eff: 'Чистая кожа', color: '#e8a0c0', cat: 'Косметические', form: 'flask', rare: 'Common' },
    { n: 'Противоядие (базовое)', d: 'Нейтрализует яды. Безоар — ключевой ингредиент.', diff: 2, ing: ['Безоар', 'Стандартная основа', 'Сок столетника'], eff: 'Нейтрализация яда', color: '#7cba6a', cat: 'Целительные', form: 'classic', rare: 'Uncommon' },
    { n: 'Зелье забвения', d: 'Стирает недавние воспоминания. Доза критична.', diff: 3, ing: ['Валериановые корни', 'Мятные листья', 'Основа забвения'], eff: 'Потеря памяти', color: '#8a7aaa', cat: 'Ментальные', form: 'flask', rare: 'Rare' },
    { n: 'Оборотное зелье', d: 'Трансформация на 1 час. Требует частичку цели (волос).', diff: 4, ing: ['Образец цели', 'Кожа бумслэнга', 'Рог двурога'], eff: 'Трансформация', color: '#c06040', cat: 'Трансформационные', form: 'round', rare: 'Epic' },
    { n: 'Веритасерум', d: 'Сыворотка правды. Три капли — и ты расскажешь всё.', diff: 4, ing: ['Опыльница', 'Листья мурло-травы', 'Три капли росы'], eff: 'Правда', color: '#40a0a0', cat: 'Ментальные', form: 'triangle', rare: 'Epic' },
    { n: 'Амортеция', d: 'Самое мощное любовное зелье. Перламутровый блеск.', diff: 4, ing: ['Перламутровая пыль', 'Розовые перья', 'Едкий лютик'], eff: 'Одержимость', color: '#e060a0', cat: 'Опасные', form: 'round', rare: 'Epic' },
    { n: 'Феликс Фелицис', d: 'Жидкая удача. Варится полгода. Очень редкое.', diff: 5, ing: ['Эльфийское вино', 'Ноготь василиска', 'Слёзы феникса'], eff: 'Удача', color: '#d4a017', cat: 'Легендарные', form: 'triangle', rare: 'Legendary' },
    { n: 'Зелье живой смерти', d: 'Вводит в состояние мнимой смерти. Жертва всё слышит.', diff: 5, ing: ['Водяной болиголов', 'Асфодель', 'Полынь'], eff: 'Мнимая смерть', color: '#3a3a4a', cat: 'Тёмные', form: 'poison', rare: 'Legendary' },
    { n: 'Зелье регенерации', d: 'Заживляет раны в 10 раз быстрее. Для мракоборцев.', diff: 3, ing: ['Драконья кровь', 'Слёзы единорога', 'Мандрагора'], eff: 'Регенерация', color: '#60b060', cat: 'Целительные', form: 'flask', rare: 'Rare' },
    { n: 'Усиленное зелье ярости', d: 'Вызывает ярость. Атакует всех подряд. Запрещено.', diff: 4, ing: ['Кровь вампира', 'Яд скорпиона', 'Белладонна'], eff: 'Ярость', color: '#a02020', cat: 'Тёмные', form: 'poison', rare: 'Epic' },
{ n: 'Дыбоволосное зелье', d: 'Заставляет волосы потребителя стоять дыбом. Любимая шалость в Хогвартсе.', diff: 1, ing: ['Крысиные хвосты', 'Листья крапивы', 'Змеиные зубы'], eff: 'Вертикальная прическа', color: '#ffd700', cat: 'Бытовые', form: 'round', rare: 'Common' },
    { n: 'Зелье невидимости', d: 'Дарует временную невидимость. Не заменяет мантию, но полезно для шпионажа.', diff: 4, ing: ['Чешуя скрытня', 'Пыльца лунного камня', 'Настой морозника'], eff: 'Скрытие тела', color: '#a0d8ef', cat: 'Трансформационные', form: 'flask', rare: 'Epic' },
	 { n: 'Противоперцовое зелье', d: 'Согревает пьющего. Использовалось во время Турнира Трех Волшебников.', diff: 2, ing: ['Рог двурога', 'Иглы ежа', 'Красный перец'], eff: 'Мгновенный согрев', color: '#ff4500', cat: 'Целительные', form: 'classic', rare: 'Uncommon' },

    { n: 'Зелье ясности', d: 'Помогает сосредоточиться. Часто варится студентами перед экзаменами С.О.В.', diff: 3, ing: ['Мята', 'Толченый жемчуг', 'Лепестки лотоса'], eff: 'Концентрация ума', color: '#f0f8ff', cat: 'Ментальные', form: 'triangle', rare: 'Rare' },

    { n: 'Умокрепляющее зелье', d: 'Повышает устойчивость к Легилименции и магическому внушению.', diff: 4, ing: ['Перья книззла', 'Святая вода', 'Корень имбиря'], eff: 'Защита разума', color: '#7fffd4', cat: 'Ментальные', form: 'flask', rare: 'Epic' },

    { n: 'Волчье противоядие', d: 'Позволяет оборотню сохранять разум при превращении. Очень сложное.', diff: 5, ing: ['Аконит', 'Лунная пыль', 'Серебряный порошок'], eff: 'Разум оборотня', color: '#1a1a2e', cat: 'Целительные', form: 'poison', rare: 'Legendary' },

    { n: 'Зелье уменьшения', d: 'Уменьшает объекты или существ в размере. При ошибке превращает в жабу.', diff: 3, ing: ['Маргаритки', 'Горлица', 'Сок инжира'], eff: 'Уменьшение роста', color: '#adff2f', cat: 'Трансформационные', form: 'round', rare: 'Rare' },

    { n: 'Зелье «Жгучая ярость»', d: 'Кипящая жидкость, вызывающая ярость при вдыхании паров. Опасно.', diff: 4, ing: ['Яд саламандры', 'Семена огненного куста', 'Черный порох'], eff: 'Боевая агрессия', color: '#e63946', cat: 'Тёмные', form: 'poison', rare: 'Epic' },

    { n: 'Напиток красоты', d: 'Временно исправляет внешние недостатки. Обожаемо Гилдероем Локхартом.', diff: 2, ing: ['Крылья феи', 'Утренняя роса', 'Лепестки роз'], eff: 'Обаяние', color: '#ffb6c1', cat: 'Косметические', form: 'flask', rare: 'Uncommon' },

    { n: 'Зелье памяти', d: 'Позволяет вспомнить мельчайшие детали прошлого с идеальной точностью.', diff: 3, ing: ['Слезы книззла', 'Сушеная лаванда', 'Белый шалфей'], eff: 'Идеальная память', color: '#fff5ee', cat: 'Ментальные', form: 'classic', rare: 'Rare' },
	 { n: 'Драконий тоник', d: 'Специальное зелье для лечения больных драконов. Очень едкое.', diff: 4, ing: ['Огненные семена', 'Хребет рыбы-льва', 'Слизь флоббер-червя'], eff: 'Лечение существ', color: '#8b0000', cat: 'Бытовые', form: 'round', rare: 'Epic' },

    { n: 'Зелье ходьбы по воде', d: 'Позволяет стоять на поверхности воды в течение часа.', diff: 3, ing: ['Эфир', 'Лапки водомерки', 'Озерная трава'], eff: 'Левитация над водой', color: '#00ced1', cat: 'Бытовые', form: 'triangle', rare: 'Rare' },

    { n: 'Эликсир жизни', d: 'Продлевает жизнь. Варится только с помощью Философского камня.', diff: 5, ing: ['Философский камень', 'Чистая ртуть', 'Золотая эссенция'], eff: 'Бессмертие', color: '#ffffff', cat: 'Легендарные', form: 'classic', rare: 'Legendary' },

    { n: 'Зелье гербицида', d: 'Уничтожает сорняки и ядовитые магические лозы. Смертельно для дьявольских силков.', diff: 2, ing: ['Хребет рыбы-иглы', 'Кислота', 'Сок иглобрюха'], eff: 'Смерть растений', color: '#556b2f', cat: 'Бытовые', form: 'flask', rare: 'Uncommon' },

    { n: 'Зелье старения', d: 'Увеличивает возраст пьющего. Не обманет Кубок Огня (обычно).', diff: 3, ing: ['Корень старого мандрагора', 'Песок времени', 'Спирт'], eff: 'Взросление', color: '#808080', cat: 'Трансформационные', form: 'classic', rare: 'Rare' },

    { n: 'Костерост', d: 'Ужасное на вкус зелье. Заставляет кости отрастать заново за одну ночь.', diff: 4, ing: ['Толченый кальций', 'Сушеный навоз дракона', 'Мед'], eff: 'Рост костей', color: '#f5f5f5', cat: 'Целительные', form: 'round', rare: 'Epic' },

	{ n: 'Напиток отчаяния', d: 'Изумрудное зелье, охранявшее Медальон Слизерина. Вызывает невыносимый страх и жажду.', diff: 5, ing: ['Вода из пещеры', 'Яд инфернала', 'Корень белладонны'], eff: 'Галлюцинации и пытка', color: '#00ff7f', cat: 'Тёмные', form: 'triangle', rare: 'Legendary' },

    { n: 'Растворитель магической липкости', d: 'Единственное средство против супер-клея волшебного мира и некоторых проклятий сцепления.', diff: 2, ing: ['Сок иглобрюха', 'Масло клещевины', 'Уксусная эссенция'], eff: 'Разъединение предметов', color: '#db7093', cat: 'Бытовые', form: 'flask', rare: 'Uncommon' },
	 { n: 'Уморительное зелье', d: 'Вызывает неудержимый смех. В больших дозах ведет к истерике и потере сознания.', diff: 2, ing: ['Крылья феи', 'Листья лимонника', 'Корень чихотки'], eff: 'Безудержный смех', color: '#ff69b4', cat: 'Бытовые', form: 'round', rare: 'Common' },

    { n: 'Охранное зелье', d: 'Используется для защиты ценных свитков. Делает бумагу неуязвимой к огню и воде.', diff: 3, ing: ['Драконья кожа (порошок)', 'Слизь флоббер-червя', 'Канифоль'], eff: 'Магическая защита вещей', color: '#4682b4', cat: 'Бытовые', form: 'classic', rare: 'Rare' },

    { n: 'Сыворотка роста', d: 'Заставляет растения или мелких животных мгновенно увеличиваться. Опасно при приеме внутрь.', diff: 3, ing: ['Помет дракона', 'Дрожжи', 'Экстракт мандрагоры'], eff: 'Гигантизм', color: '#32cd32', cat: 'Трансформационные', form: 'flask', rare: 'Rare' },

    { n: 'Зелье забвения (мощное)', d: 'Стирает не конкретные моменты, а целые пласты личности. Опасно для рассудка.', diff: 4, ing: ['Вода из реки Лета', 'Ягоды омелы', 'Валериана'], eff: 'Амнезия личности', color: '#e6e6fa', cat: 'Ментальные', form: 'round', rare: 'Epic' },

    { n: 'Кровь единорога (ритуал)', d: 'Не совсем зелье, но алхимический компонент. Дарует жизнь на грани смерти, но проклинает.', diff: 5, ing: ['Чистая кровь единорога'], eff: 'Полужизнь', color: '#c0c0c0', cat: 'Легендарные', form: 'classic', rare: 'Legendary' },
	{ n: 'Зелье «Глаз орла»', d: 'Позволяет видеть сквозь туман и на огромные расстояния. Глаза светятся золотом.', diff: 3, ing: ['Глаза слепозмейки', 'Перья совы', 'Золотая пыль'], eff: 'Сверхзрение', color: '#ffd700', cat: 'Ментальные', form: 'triangle', rare: 'Rare' },

    { n: 'Противоожоговая мазь', d: 'Густая оранжевая паста. Мгновенно снимает боль от магического огня дракона.', diff: 2, ing: ['Мякоть тыквы', 'Слизь улитки', 'Листья алоэ'], eff: 'Заживление ожогов', color: '#ff8c00', cat: 'Целительные', form: 'classic', rare: 'Uncommon' },

    { n: 'Удушающий газ', d: 'При разбивании флакона выпускает плотное облако, лишающее противника возможности дышать.', diff: 4, ing: ['Ядовитые грибы', 'Серый порошок', 'Сушеная жаба'], eff: 'Паралич дыхания', color: '#708090', cat: 'Тёмные', form: 'poison', rare: 'Epic' },
	{ n: 'Зелье вечного спокойствия', d: 'Убирает любые эмоции, делая человека абсолютно безразличным ко всему.', diff: 3, ing: ['Слезы дементора (синтез)', 'Лед из Арктики', 'Белый мак'], eff: 'Эмоциональный блок', color: '#f0ffff', cat: 'Ментальные', form: 'flask', rare: 'Rare' },

    { n: 'Зелье выносливости', d: 'Позволяет обходиться без сна и еды в течение трех суток без вреда для здоровья.', diff: 4, ing: ['Рог единорога (щепотка)', 'Корень женьшеня', 'Сердце медведя'], eff: 'Неутомимость', color: '#cd853f', cat: 'Целительные', form: 'round', rare: 'Epic' },

    { n: 'Зелье «Ложная смерть»', d: 'Останавливает сердце на 10 минут. Тело становится холодным. Нужно противоядие для пробуждения.', diff: 4, ing: ['Мускус', 'Яд кобры', 'Ночной настой'], eff: 'Полная имитация смерти', color: '#2f4f4f', cat: 'Тёмные', form: 'poison', rare: 'Epic' },

    { n: 'Эликсир эйфории', d: 'Вызывает состояние блаженства. Побочный эффект — пение и пощипывание носа.', diff: 3, ing: ['Сушеная смоква', 'Иглы дикобраза', 'Мятная веточка'], eff: 'Блаженство', color: '#ffff00', cat: 'Бытовые', form: 'flask', rare: 'Rare' },

    { n: 'Зелье «Грозовая туча»', d: 'При открытии создает над головой врага маленькую тучу, бьющую молниями.', diff: 4, ing: ['Эссенция бури', 'Вода из воронки', 'Медная стружка'], eff: 'Локальный шторм', color: '#191970', cat: 'Тёмные', form: 'triangle', rare: 'Epic' },

    { n: 'Универсальное чистящее средство', d: 'Разработка миссис Уизли. Отмывает даже самые старые пятна от магических экспериментов.', diff: 1, ing: ['Мыльный корень', 'Чеснок', 'Сода'], eff: 'Идеальная чистота', color: '#f5f5dc', cat: 'Бытовые', form: 'round', rare: 'Common' },
	{ n: 'Зелье невидимости (улучшенное)', d: 'Полностью скрывает силуэт. Действует дольше обычного, но вызывает легкое головокружение.', diff: 4, ing: ['Чешуя скрытня', 'Ветвь спорыша', 'Мухи-златоглазки'], eff: 'Длительная невидимость', color: '#b0e0e6', cat: 'Трансформационные', form: 'flask', rare: 'Epic' },

    { n: 'Зелье Эдурус', d: 'Покрывает кожу каменной коркой, значительно повышая защиту в бою. Популярно у боевых магов.', diff: 3, ing: ['Яйца пеплавки', 'Мех дворняжки', 'Земляной корень'], eff: 'Каменная кожа', color: '#708090', cat: 'Бытовые', form: 'round', rare: 'Rare' },

    { n: 'Зелье Максима', d: 'На короткое время значительно увеличивает урон от заклинаний пьющего. Флакон светится изнутри.', diff: 4, ing: ['Пиявки', 'Клык паука', 'Сок мурлокома'], eff: 'Мощь заклинаний', color: '#ff00ff', cat: 'Трансформационные', form: 'triangle', rare: 'Epic' },

    { n: 'Грозовой напиток', d: 'При использовании создает вокруг мага электрический вихрь, бьющий ближайших врагов молниями.', diff: 4, ing: ['Сок стемпеля', 'Инжир', 'Запах грозы'], eff: 'Электрический щит', color: '#483d8b', cat: 'Опасные', form: 'flask', rare: 'Epic' },

    { n: 'Фокусирующее зелье', d: 'Сокращает время перезарядки магических способностей. Пахнет свежескошенной травой.', diff: 3, ing: ['Златоглазки', 'Стебель водоросли', 'Язык лягушки'], eff: 'Ускорение магии', color: '#32cd32', cat: 'Ментальные', form: 'classic', rare: 'Rare' },

    { n: 'Зелье «Глоток мира»', d: 'Успокаивает тревогу и усмиряет гнев. Часто используется в Больничном крыле после потрясений.', diff: 2, ing: ['Толченый лунный камень', 'Сироп чемерицы', 'Лаванда'], eff: 'Успокоение', color: '#add8e6', cat: 'Целительные', form: 'round', rare: 'Uncommon' },

    { n: 'Зелье «Собачий выдох»', d: 'Заставляет жертву лаять при попытке произнести любое заклинание. Идеально для дуэлей.', diff: 2, ing: ['Шерсть пса', 'Корень аконита', 'Перец'], eff: 'Блокировка речи', color: '#8b4513', cat: 'Тёмные', form: 'poison', rare: 'Uncommon' },

    { n: 'Зелье «Чистое золото»', d: 'Временно превращает любые предметы в золото. Увы, эффект исчезает через час.', diff: 5, ing: ['Золотая чешуя дракона', 'Солнечный луч', 'Ртуть'], eff: 'Временная трансмутация', color: '#ffd700', cat: 'Легендарные', form: 'classic', rare: 'Legendary' },

    { n: 'Зелье «Летейская вода»', d: 'Заставляет забыть всё, что произошло за последние 24 часа. Сладкое на вкус.', diff: 3, ing: ['Вода из ручья', 'Ягоды омелы', 'Мята'], eff: 'Краткосрочная амнезия', color: '#e6e6fa', cat: 'Ментальные', form: 'flask', rare: 'Rare' },

    { n: 'Противоядное зелье (от ядов мантикоры)', d: 'Единственное средство, способное остановить действие яда мантикоры.', diff: 5, ing: ['Сердце мантикоры', 'Безоар высшего качества', 'Слезы феникса'], eff: 'Спасение жизни', color: '#ff6347', cat: 'Целительные', form: 'triangle', rare: 'Legendary' },
	{ n: 'Зелье «Драконий чих»', d: 'При вдыхании вызывает извержение искр из ноздрей. Опасно для бровей.', diff: 1, ing: ['Порох', 'Сера', 'Сушеная мята'], eff: 'Огненное чихание', color: '#ff4500', cat: 'Бытовые', form: 'round', rare: 'Common' },

    { n: 'Сыворотка «Второй шанс»', d: 'Позволяет пережить один смертельный удар в течение следующего часа.', diff: 5, ing: ['Перо феникса', 'Звездный свет', 'Кровь единорога'], eff: 'Защита от смерти', color: '#fffacd', cat: 'Легендарные', form: 'classic', rare: 'Legendary' },

	{ n: 'Икотное зелье', d: 'Вызывает бесконечную икоту. Студенты подливают его врагам в сок перед уроками Снейпа.', diff: 1, ing: ['Корень чихотки', 'Иглы ежа', 'Уксус'], eff: 'Непрерывная икота', color: '#9acd32', cat: 'Бытовые', form: 'round', rare: 'Common' },

    { n: 'Зелье «Мгновенная тьма»', d: 'Разработка Перуанского импорта. При разбивании гасит любой свет в радиусе 10 метров.', diff: 3, ing: ['Порошок мгновенной тьмы', 'Сажа', 'Масло черного ореха'], eff: 'Абсолютная слепота', color: '#121212', cat: 'Опасные', form: 'flask', rare: 'Rare' },

    { n: 'Противокошачье зелье', d: 'Вызывает у кошек непреодолимое желание держаться от вас подальше. Помогает против миссис Норрис.', diff: 2, ing: ['Валериана (антидот)', 'Чесночный сок', 'Листья руты'], eff: 'Отпугивание фамильяров', color: '#bc8f8f', cat: 'Бытовые', form: 'classic', rare: 'Uncommon' },

    { n: 'Зелье «Глоток Красоты»', d: 'Разглаживает морщины и придает волосам неестественный блеск. Эффект длится 6 часов.', diff: 2, ing: ['Крылья эльфа', 'Розовая вода', 'Экстракт лаванды'], eff: 'Идеальная внешность', color: '#ff69b4', cat: 'Косметические', form: 'flask', rare: 'Uncommon' },

    { n: 'Напиток «Жидкий ужас»', d: 'Жертва видит свои самые страшные кошмары наяву. Опасно для психики.', diff: 4, ing: ['Слезы дементора', 'Яд паука-акромантула', 'Корень белладонны'], eff: 'Галлюцинации страха', color: '#2f4f4f', cat: 'Тёмные', form: 'poison', rare: 'Epic' },

    { n: 'Зелье «Кошачий глаз»', d: 'Позволяет видеть в полной темноте. Зрачки становятся вертикальными.', diff: 3, ing: ['Глаза крысы', 'Трава-пучеглазка', 'Настойка полыни'], eff: 'Ночное зрение', color: '#ffd700', cat: 'Ментальные', form: 'triangle', rare: 'Rare' },

    { n: 'Эликсир «Вечная юность»', d: 'Замедляет старение клеток. Рецепт передается только в семьях чистокровных магов.', diff: 5, ing: ['Золотое яблоко', 'Кровь саламандры', 'Роса с лепестка лотоса'], eff: 'Остановка старения', color: '#fff5ee', cat: 'Легендарные', form: 'classic', rare: 'Legendary' },

    { n: 'Зелье «Каменная дробь»', d: 'Превращает жидкость в твердые снаряды при контакте с воздухом. Используется в ловушках.', diff: 4, ing: ['Толченый гранит', 'Слизь тролля', 'Серная кислота'], eff: 'Магическая шрапнель', color: '#696969', cat: 'Опасные', form: 'round', rare: 'Epic' },

    { n: 'Зелье «Успокоительный бальзам»', d: 'Более мягкая версия "Глотка мира". Помогает при легком испуге и заикании.', diff: 1, ing: ['Ромашка', 'Мята', 'Мед'], eff: 'Снятие стресса', color: '#afeeee', cat: 'Целительные', form: 'classic', rare: 'Common' },
	 { n: 'Зелье «Ледяное дыхание»', d: 'Позволяет замораживать жидкости дыханием. Будьте осторожны с горячим чаем!', diff: 3, ing: ['Мятные кристаллы', 'Снег с пика Эвереста', 'Чешуя белого дракона'], eff: 'Крио-эффект', color: '#f0ffff', cat: 'Трансформационные', form: 'triangle', rare: 'Rare' },

    { n: 'Зелье «Правдоречивое эхо»', d: 'Слабая версия Веритасерума. Заставляет человека повторять правду только в ответ на эхо.', diff: 3, ing: ['Горное эхо (магия)', 'Роса', 'Листья вербены'], eff: 'Частичная правда', color: '#7b68ee', cat: 'Ментальные', form: 'flask', rare: 'Rare' },

    { n: 'Зелье «Кровавый след»', d: 'Позволяет выследить существо по одной капле его крови. След светится красным.', diff: 4, ing: ['Кровь искомого', 'Корень мандрагоры', 'Перо ворона'], eff: 'Магическое отслеживание', color: '#8b0000', cat: 'Тёмные', form: 'poison', rare: 'Epic' },

    { n: 'Зелье «Прыгучая радость»', d: 'Заставляет человека прыгать от счастья буквально. Использовалось на свадьбах.', diff: 2, ing: ['Ноги кузнечика', 'Сахар', 'Пыльца феи'], eff: 'Эйфория и прыжки', color: '#ffa500', cat: 'Бытовые', form: 'round', rare: 'Uncommon' },

    { n: 'Зелье «Железный желудок»', d: 'Позволяет съесть что угодно (даже яд или камни) без вреда. Популярно у гоблинов.', diff: 4, ing: ['Желудок дракона', 'Уголь', 'Слизь флоббер-червя'], eff: 'Иммунитет к пище', color: '#556b2f', cat: 'Трансформационные', form: 'flask', rare: 'Epic' },

    { n: 'Зелье «Лунный свет»', d: 'Флакон освещает пещеры ярче любого Люмоса. Свет не пугает магических существ.', diff: 2, ing: ['Пыль лунного камня', 'Светлячки', 'Чистая вода'], eff: 'Магический фонарь', color: '#fafad2', cat: 'Бытовые', form: 'triangle', rare: 'Uncommon' },

    { n: 'Напиток «Забытая любовь»', d: 'Противоядие от Амортенции. Снимает наваждение и возвращает рассудок.', diff: 3, ing: ['Слезы того, кто любит', 'Корень имбиря', 'Листья шалфея'], eff: 'Снятие приворота', color: '#db7093', cat: 'Целительные', form: 'classic', rare: 'Rare' },
];
const CREATURES = [
    { id: 'dragon', n: 'Дракон', d: 'Огромный огнедышащий рептил с бронированной чешуёй. 10 известных пород. Самое опасное существо волшебного мира. Питается крупным рогатым скотом.', danger: 'XXXXX', hab: 'Горные регионы, заповедники', feat: 'Огненная струя до 20м. Броня отражает заклинания. Ядовитые зубы.', cat: 'Драконы', ic: 'icons/dragon.png' },
    { id: 'fenix', n: 'Феникс', d: 'Редчайшее существо. Самовозгорается в конце жизненного цикла и возрождается из пепла птенцом. Слёзы исцеляют любые раны.', danger: 'XXXX', hab: 'Неизвестно, рядом с любимым хозяином', feat: 'Apparate без ограничений. Песня успокаивает чистых сердцем. Невероятно лёгкие.', cat: 'Мифические', ic: 'icons/fenix.png' },
    { id: 'edinorog', n: 'Единорог', d: 'Чисто белое существо с серебристым спиральным рогом. Жеребёнок золотой, к 7 годам сереет. Символ чистоты.', danger: 'XXXX', hab: 'Северные леса Европы, Запретный лес', feat: 'Кровь продлевает жизнь, но проклятие убийцы. Слёзы целительны. Пугливы.', cat: 'Мифические', ic: 'icons/edinorog.png' },
    { id: 'akromantul', n: 'Акромантул', d: 'Гигантский магический паук до 4.5м размахом. Создан Герпом Слизортом. Говорит по-человечески. Живёт колониями.', danger: 'XXXXX', hab: 'Тёмные леса, пещеры', feat: 'Смертельный яд. Паутина — солнцезащитная. Самки крупнее и агрессивнее.', cat: 'Паукообразные', ic: 'icons/akromantul.png' },
    { id: 'bogart', n: 'Боггарт', d: 'Нелетающее существо, принимающее форму величайшего страха зрителя. Истинная форма неизвестна — в темноте аморфная тень.', danger: 'III', hab: 'Тёмные замкнутые пространства', feat: 'Питается страхом. Уязвим к Ридикулус (смех разрушает). Не выдерживает радость.', cat: 'Прочие', ic: 'icons/bogart.png' },
    { id: 'elf', n: 'Домовой эльф', d: 'Разумное существо, обязанное служить волшебной семье. Обладает мощной магией, отличной от волшебной. Освобождается подарком одежды.', danger: 'II', hab: 'Дома волшебников', feat: 'Apparate без ограничений (даже в Хогвартс). Обезоруживают щелчком. Создают защитные барьеры.', cat: 'Разумные', ic: 'icons/elf.png' },
    { id: 'vampir', n: 'Вампир', d: 'Живой мертвец, питающийся человеческой кровью. Не боится чеснока, крестов и солнца (но предпочитает тьму).', danger: 'XXXX', hab: 'Тёмные места, подземелья', feat: 'Убит только полным уничтожением тела. Гипноз. Полукровки — частичные способности.', cat: 'Тёмные', ic: 'icons/vampir.png' },
    { id: 'veela', n: 'Веела', d: 'Полулюди-полуптицы. Женщины невероятно красивы с магическим обаянием, подчиняющим мужчин.', danger: 'XXX', hab: 'Франция, Восточная Европа', feat: 'Обаяние активно при стрессе (волосы→перья, голос→гипнотический).', cat: 'Гибриды', ic: 'icons/veela.png' },
    { id: 'rusalka', n: 'Русалка', d: 'Водное разумное существо. Мерроу (шотландские) более человечны, селки (ирландские) — с рыбьими чертами.', danger: 'XXX', hab: 'Озёра, реки, моря', feat: 'Свой язык, культура, законы.', cat: 'Водные', ic: 'icons/rusalka.png' },
    { id: 'mantikora', n: 'Мантикора', d: 'Гибрид человека, льва и скорпиона размером с лошадь. Яд смертелен, панцирь отражает заклинания.', danger: 'XXXXX', hab: 'Греция, Малая Азия', feat: 'Яд — мгновенная смерть.', cat: 'Опасные', ic: 'icons/mantikora.png' },
    { id: 'himera', n: 'Химера', d: 'Гибрид козы, льва и дракона. Очень редка, крайне опасна.', danger: 'XXXXX', hab: 'Греция', feat: 'Три головы.', cat: 'Опасные', ic: 'icons/himera.png' },
    { id: 'troll', n: 'Тролль', d: 'Крупное тупое сильное существо.', danger: 'XXXX', hab: 'Горы, леса', feat: 'Сила.', cat: 'Гиганты', ic: 'icons/troll.png' },
    { id: 'festral', n: 'Фестрал', d: 'Летающая лошадь-скелет.', danger: 'XXXX', hab: 'Британские острова', feat: 'Видят смерть.', cat: 'Мифические', ic: 'icons/festral.png' },
    { id: 'kelpi', n: 'Келпи', d: 'Водной дух-лошадь.', danger: 'XXXX', hab: 'Озёра', feat: 'Утопляет.', cat: 'Водные', ic: 'icons/kelpi.png' },
    { id: 'grindiloy', n: 'Гриндилоу', d: 'Водной демон.', danger: 'III', hab: 'Пресные воды', feat: 'Атака.', cat: 'Водные', ic: 'icons/grindiloy.png' },
    { id: 'werewolfe', n: 'Оборотень', d: 'Человек в полнолуние...', danger: 'XXXX', hab: 'Везде', feat: 'Укус', cat: 'Тёмные', ic: 'icons/werewolfe.png' },
    { id: 'nundu', n: 'Нунду', d: 'Гигантская магическая кошка.', danger: 'XXXXX', hab: 'Африка', feat: 'Антимагия.', cat: 'Опасные', ic: 'icons/nundu.png' },
    { id: 'bundimun', n: 'Бундимун', d: 'Плесневой комок.', danger: 'III', hab: 'Дома', feat: 'Разрушает дом.', cat: 'Прочие', ic: 'icons/bundimun.png' },

    // --- СУЩЕСТВА С ОСОБЫМИ ГЛАВАМИ (Твои фото) ---
    { 
        id: 'dementor', n: 'Дементор', d: 'Тёмная фигура в чёрном плаще. Питается счастьем, оставляя холод.', danger: 'XXXXX', hab: 'Азкабан', feat: 'Поцелуй души', cat: 'Тёмные', 
        ic: 'icons/dementor.png', 
        fullPageImg: 'dementor2222.jpg'
    },
    { 
        id: 'vasilisk', n: 'Василиск', d: 'Царь змей. Огромный змей, чей прямой взгляд убивает на месте.', danger: 'XXXXX', hab: 'Тайная Комната', feat: 'Взгляд', cat: 'Тёмные', 
        ic: 'icons/vasilisk.png', 
        fullPageImg: 'vasiliskaaaa2.png'
    },
    { 
        id: 'gipogrif', n: 'Гиппогриф', d: 'Полуконь-полуорёл. Очень гордое существо, требующее уважения.', danger: 'XXXX', hab: 'Горные луга', feat: 'Полет', cat: 'Гибриды', 
        ic: 'icons/gipogrif.png', 
        fullPageImg: 'gipogriffka2.png'
    },

    // --- А ---
    { id: 'abarimon', n: 'Абаримон', d: 'Люди с вывернутыми назад стопами. Невероятно быстры.', danger: 'XX', hab: 'Гималаи', cat: 'Разумные волшебные существа', ic: 'https://static.wikia.nocookie.net/mythological-creation/images/e/e0/%D0%90%D0%B1%D0%B0%D1%80%D0%B8%D0%BC%D0%BE%D0%BD.jpg/revision/latest?cb=20191217153136&path-prefix=ru' },
    { id: 'abraxan', n: 'Абраксанский конь', d: 'Гигантские крылатые кони. Пьют только виски.', danger: 'XXXX', hab: 'Франция', cat: 'Крылатые кони', ic: 'https://static.wikia.nocookie.net/harry-potter-hogwarts-mystery/images/c/ce/Abraxan.png/revision/latest/scale-to-width-down/200?cb=20190414200223&path-prefix=ru' },
    { id: 'avgurey', n: 'Авгурей', d: 'Птица, чье пение предвещает дождь. Ранее считалась вестником смерти.', danger: 'XX', hab: 'Британия', cat: 'Авгуреи', ic: 'https://static.wikia.nocookie.net/harrypotter/images/6/6e/Augurey_FBIE.jpeg/revision/latest?cb=20180726092210' },

    // --- Б ---
    { id: 'banshee', n: 'Банши', d: 'Женщина-дух. Её крик несет смерть.', danger: 'XXXX', hab: 'Ирландия', cat: 'Банши', ic: 'https://static.wikia.nocookie.net/harrypotter/images/9/9f/B3C7M11_backgroundless_Banshee_PM.png/revision/latest?cb=20170731154217' },
    { id: 'imp', n: 'Бес малый', d: 'Проказливое существо, не умеет летать.', danger: 'XX', hab: 'Болота', cat: 'Волшебные существа', ic: 'https://static.wikia.nocookie.net/harrypotter/images/8/85/Imp_HM_Icon.png/revision/latest?cb=20210108185939' },
    { id: 'jobberknoll', n: 'Болтрушайка', d: 'Птица, которая кричит только перед смертью.', danger: 'XX', hab: 'Север', cat: 'Птицы', ic: 'https://static.wikia.nocookie.net/harrypotter/images/8/8c/Jobberknoll.png/revision/latest/scale-to-width-down/535?cb=20171229043112&path-prefix=ru' },

    // --- В ---
    { id: 'wampus', n: 'Вампус', d: 'Магическая кошка, мастер гипноза.', danger: 'XXXXX', hab: 'Америка', cat: 'Волшебные существа', ic: 'https://static.wikia.nocookie.net/harrypotter/images/f/fc/Wampus.jpg/revision/latest?cb=20170315040559&path-prefix=ru' },
    { id: 'billywig', n: 'Веретенница', d: 'Летающее насекомое. Укус вызывает левитацию.', danger: 'XXX', hab: 'Австралия', cat: 'Насекомые', ic: 'https://static.wikia.nocookie.net/harrypotter/images/b/b5/Billywig_FBCFWW.png/revision/latest?cb=20170129163653&path-prefix=ru' },
   

    // --- Г ---
    { id: 'hippocampus', n: 'Гиппокамп', d: 'Морской конь с рыбьим хвостом.', danger: 'XXX', hab: 'Моря', cat: 'Водные', ic: 'https://static.wikia.nocookie.net/harrypotter/images/7/7d/Hippocampus2.png/revision/latest?cb=20161203141809&path-prefix=ru' },
    { id: 'gnome', n: 'Гном садовый', d: 'Садовый вредитель.', danger: 'XX', hab: 'Сады', cat: 'Волшебные существа', ic: 'https://static.wikia.nocookie.net/harrypotter/images/b/be/Gnome_painting_HL.jpeg/revision/latest/scale-to-width-down/536?cb=20250123043548' },
    { id: 'griffin', n: 'Грифон', d: 'Лев с головой и крыльями орла.', danger: 'XXXX', hab: 'Греция', cat: 'Волшебные существа', ic: 'https://static.wikia.nocookie.net/harrypotter/images/6/62/Griffin_FBCFWW.png/revision/latest?cb=20170413065807&path-prefix=ru' },

    // --- З / К / Л ---
    { id: 'snidget', n: 'Золотой сниджет', d: 'Птичка, которую ловили в квиддиче.', danger: 'XXXX', hab: 'Заповедники', cat: 'Птицы', ic: 'https://static.wikia.nocookie.net/harrypotter/images/e/e4/Golden_Snidget_HL.jpeg/revision/latest/scale-to-width-down/536?cb=20250917140057' },
    { id: 'bowtruckle', n: 'Лукотрус', d: 'Хранитель деревьев. Похож на ветку.', danger: 'XX', hab: 'Леса', cat: 'Лукотрусы', ic: 'https://static.wikia.nocookie.net/harrypotter/images/2/22/Bowtruckle.png/revision/latest?cb=20161203145244&path-prefix=ru' },
    { id: 'kappa', n: 'Каппа', d: 'Японский водный демон.', danger: 'XXXX', hab: 'Япония', cat: 'Демоны', ic: 'https://static.wikia.nocookie.net/harrypotter/images/4/49/Kappa_FBCOG.png/revision/latest/scale-to-width-down/536?cb=20190216164221' },
    { id: 'fire_crab', n: 'Огненный краб', d: 'Стреляет пламенем из хвоста.', danger: 'XXX', hab: 'Фиджи', cat: 'Волшебные существа', ic: 'https://static.wikia.nocookie.net/harrypotter/images/3/32/Fire_Crab.png/revision/latest?cb=20161203144105&path-prefix=ru' },

    // --- П / Р / С / Ф ---
    { id: 'puffskein', n: 'Пушишка', d: 'Розовый комок шерсти.', danger: 'X', hab: 'Дома', cat: 'Пушишки', ic: 'https://static.wikia.nocookie.net/harrypotter/images/3/35/Puffskein.jpg/revision/latest?cb=20200224145712&path-prefix=ru' },
    { id: 'salamander', n: 'Саламандра', d: 'Ящерица, живущая в огне.', danger: 'XXX', hab: 'Очаги', cat: 'Рептилии', ic: 'https://static.wikia.nocookie.net/harrypotter/images/f/f9/SalamanderWU.png/revision/latest?cb=20200817153532' },
    { id: 'sphynx', n: 'Сфинкс', d: 'Загадывает загадки.', danger: 'XXXX', hab: 'Египет', cat: 'Разумные волшебные существа', ic: 'https://static.wikia.nocookie.net/harrypotter/images/f/f4/Sphinx_FBCFWW.png/revision/latest?cb=20170413065710&path-prefix=ru' },
	// --- Г (Продолжение) ---
	{ id: 'gytrash', n: 'Гитраш', d: 'Огромный призрачный пес с горящими глазами. Обитает в темных переулках и на кладбищах.', danger: 'XXX', hab: 'Северная Англия', cat: 'Потусторонние волшебные существа', ic: 'https://static.wikia.nocookie.net/harrypotter/images/d/d6/Gytrash.png/revision/latest/scale-to-width-down/535?cb=20180625034223&path-prefix=ru' },
    { id: 'glock', n: 'Глиноклок', d: 'Существо, состоящее из комочков грязи. Прыгает по полям, пугая овец.', danger: 'XX', hab: 'Поля Британии', cat: 'Волшебные существа', ic: 'https://static.wikia.nocookie.net/harrypotter/images/4/42/Porlock.jpg/revision/latest/scale-to-width-down/536?cb=20170705131051&path-prefix=ru' },
    { id: 'dwarf', n: 'Гном (Дварф)', d: 'Невысокое коренастое существо. В отличие от садовых гномов, они разумны и часто работают в сфере обслуживания.', danger: 'XX', hab: 'Города магов', cat: 'Разумные волшебные существа', ic: 'https://static.wikia.nocookie.net/harrypotter/images/4/46/Dwarf.jpg/revision/latest/scale-to-width-down/536?cb=20160902100111&path-prefix=ru' },

    // --- Д (Продолжение) ---
    { id: 'triton_2', n: 'Двупалый тритон', d: 'Магическая амфибия. Его печень — редкий ингредиент для зелий.', danger: 'XX', hab: 'Болота', cat: 'Амфибии', ic: 'https://static.wikia.nocookie.net/harrypotter/images/6/6b/Double-ended_newt_HP2_PS2.png/revision/latest/scale-to-width-down/535?cb=20250128192137' },
    { id: 'doxy_2', n: 'Джарви', d: 'Похож на переросшего хорька. Умеет говорить, но его речь состоит из грубых ругательств.', danger: 'XXX', hab: 'Норы', cat: 'Волшебные существа', ic: 'https://static.wikia.nocookie.net/harrypotter/images/9/98/Jarvey.png/revision/latest?cb=20180105072856&path-prefix=ru' },
    { id: 'dukuvaka', n: 'Дукувака', d: 'Существо-оборотень из океана, способное принимать облик акулы или человека.', danger: 'XXXX', hab: 'Фиджи', cat: 'Водные', ic: 'data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAAAQABAAD/2wCEAAkGBwgHBgkIBwgKCgkLDRYPDQwMDRsUFRAWIB0iIiAdHx8kKDQsJCYxJx8fLT0tMTU3Ojo6Iys/RD84QzQ5OjcBCgoKDQwNGg8PGjclHyU3Nzc3Nzc3Nzc3Nzc3Nzc3Nzc3Nzc3Nzc3Nzc3Nzc3Nzc3Nzc3Nzc3Nzc3Nzc3Nzc3N//AABEIAFwAXAMBIgACEQEDEQH/xAAbAAACAwEBAQAAAAAAAAAAAAAEBQIDBgEAB//EADYQAAIBAwMCBAQFAwMFAAAAAAECAwAEEQUSITFBIlFhcRMUMoEGQqGx0VKR4TPB8CMkYmOi/8QAGAEAAwEBAAAAAAAAAAAAAAAAAAECAwT/xAAdEQACAgMBAQEAAAAAAAAAAAAAAQIREiExQWEi/9oADAMBAAIRAxEAPwD5aLcfnkQenJNeEaIfCN3luFEiPccKBx1OOlVSbScRknHU0AR+EfqIXzyTUW5+kVakJam9jp5RBNuiMg5AZhxVRi5ClNRBdN0Wa5Akkjk2HsFOKb3EMGm2TmCB2cocMkeQpx1J6UdpWqPE6pdzQhmztihG4ryRgnPlg/fHu01S0+chlgCmKV1IcdM5HWt1BY66c0pyy3w+ZkE8k5JOcmvBMHxeH3Boi4imtZTFIgQjuOc+x71T6muU670Q6etcNT6VbaxLJMFmLIuMkgcj1phZVBD8WQIXVAfzP0HHpRdzpjJJi2njnjxneG28+1NHafTdPhmgvI5ondozHHx8Nl/waEuFgDiS4fMkqiQrAgRUz2x/vVUkRbYG7O67ScL/AE9qsitZCnxNhCf1HgUWxBwyRpGFHXqfcmomT4jbuDgcseABU8GtlUlo0iDa3PkKjBAhkIuCVCnBCjJq6x1GCSYo+VXs5H7125eK6JMUbKFI8ecbh7UWHuw23msrQFkU5I5Gdxpub+aa2iu7obS5KJFgHw+uaSWtsHdM/Tnj1pq+majfyolrbuUXpu4qoNpinFNBU9nDqkP/AHRyByGeQ5X2AIApUn4ctJLgJazSTjONn8Y60zk0hrDaNWudqHBEUPO/zyf9utaPSbi1t0xFDGkXZe59zW34fhjUktMRJ+G9Nto83UCs+PoXkj3pF+JbaO0iWazIjiYBGQcluvJbqfLn0re3+t2EirY2sEUsg+uVlGF9eP2pL+KtN0+H8OXE0UkjzlAPH3Ynt5UpYtBHJS2fOXvMLtA46/eh2lJbnANV4JOADn2qxLc7eW2+nWudts6qSGd/cDISEF1HPuatgjLaeyOwaR18XYKPKhLgmKL4zxBRKTtyev2qzTLee9nVIFkk4yyouQB646CkC4F6XovzQLsxC9jjrTebRTBbgo7HLBVBTAyafaTbWtvbKZpEOAPAjA/r/FL/AMQaoj3sUMAEcEX1bTxvP+K0pJGdtsY6Lp0NuA0xy/Tg5Y+/lWhhTcw+GAo7AH96yEWrxQRBUUsR3qu5127lQqh2g/01SkkQ4Skxx+I/k44j8edprgfQFPhT2H881jb3WHhiZA3JHCg1G9u2AbH/AFZvLy96T3drcREPOjKXGQWHWodstJLQ303UhEvJ8Tck17XtUee0jQOTh8kHp0IpZb6fePbieKNXUngbhn9aY6do+o6hMI5bUpADiVwFOB5ADv8AzzxS2U6FVtZNdyR/GX4QkI2lhkv57R1PenA/CsYyPm2JHXEB4/8AqvXWoaZZx7NHzBLgiR2j+Jk+W/8AMfXoO3FI2gac/EdPiE/mk8R/uaAuxreWllZS51Wb527UkNHbTDaOD4dw4C+3PligrW/ljRrbKwW7ncI4+n3PU/ehcISCgAB6gdBV0NpcXjMLWMuUG40mNMb2dxIXEay7FJ5bypjbNGl+6BHePII34bnHU1m7aYhVZR70xt5nkmyzbfWknQ2rNxJLp0dti4t4iD/6x+4rOX9pALd5I43VWPhw/b2Ne8ITcLjnyxUXkCRlgg8Qxk8nFaLZlWIJbGKB/oAX1IqrVpzeGOCHxMzDbjzrrWfzKk28fjXlj2xUre7tNLLGFRLdYIMreIKfIDp+/r5Vo34RSbsfRWEOn6fG08jLIBiKMdRjufX+3v2pHrmob4vlYozl8FixJJHP2A9MVQ2qXMzFiQzHqcf5qBmkY5YLk9azaKX0DgseQ83b8tXtwcDgVZvLcEYqDLk1JdnBos7/AESxL75oy10EIctckkddgx+tVHVAv0xknzJxQN1qM0+VZzj+heBVaJdsrlVba7uIVcMgc7DnOR1omFDKVO8AdzSOWR2mZgSOcDFX29zKUKxy4cdsc474rNmq4a0Q26RA7yx9TU3tlZFnvJ0tbNBn4jnl+2FHf3rPWWppbt8SQ/MSD6I34QerAcn2zjzz0oa+urm9d5LyZ5pd3Bk4x6AdB7AVSlQnGxjq/wCIFljFtp8bw2i9MnxOe5NCRyqqjaAR54pbsHV2+1dSbZweV/ajITiNBdY6CoveHHHBoJW3/Qc+1G22ns43zEqPLuadsmkjtsZJn3k4Rf1oliM0zsrGSa2cQuIoiNpB53nPAH8+hooaXaWgEd5tebGWw5GPTigZi9zM2OacXMcFhpsjLGpm24DtzyeM0rtlBuYwem6jdbY/JqPOQfsaQxPbKGbb0yCoOehxwalCwbG4A+h5qELEYYHBBrj8SsPWkihnHHbsP9MHyxx9xVOE/wBNnIkBAjYjhh5H1oeNj4vSuzkkYbn3piJOu0kMoyPSqmC1fY5uGMchOF6HvTxIYNOcfBgieTAIklXeRz2B4H9qQMR2ljPc4kgBVe0vIH2IpzpMs63Hw7sLN2BIyAfU9/Y023M5leRmdgq8saWX91LAmYyAAOnbrT4S3Y2vrsm0Yi5itEzh9gAkI/55UoE+m46B/wDylclj+tZ+e4lmfMjk1zG3gZpio//Z' },

    // --- И / К (Продолжение) ---
    { id: 'ippo', n: 'Ипопаточник', d: 'Насекомое, выделяющее патоку, которая вызывает состояние эйфории.', danger: 'XXX', hab: 'Европа', cat: 'Насекомые', ic: 'https://static.wikia.nocookie.net/harrypotter/images/e/e2/Glumbumble_HM_icon.png/revision/latest?cb=20210127135054' },
    { id: 'ishaka', n: 'Ишака', d: 'Крупное африканское копытное. Чрезвычайно пугливо и быстро.', danger: 'XX', hab: 'Африка', cat: 'Волшебные существа', ic: 'https://static.wikia.nocookie.net/harrypotter/images/f/f4/Moke.png/revision/latest/scale-to-width-down/536?cb=20171229042110&path-prefix=ru' },
    { id: 'caipora', n: 'Кайпора', d: 'Духи-защитники лесов Бразилии. Очень озорные и следят за школой Кастелобрушу.', danger: 'XXXX', hab: 'Бразилия', cat: 'Потусторонние волшебные существа', ic: 'https://static.wikia.nocookie.net/harrypotter/images/3/34/Caipora.png/revision/latest?cb=20190902082237&path-prefix=ru' },
    { id: 'knarl', n: 'Нарл', d: 'Похож на ежа, но крайне подозрителен. Если предложить ему еду, он решит, что его хотят отравить, и разгромит сад.', danger: 'XXX', hab: 'Северная Европа', cat: 'Волшебные существа', ic: 'https://static.wikia.nocookie.net/harrypotter/images/8/8d/%D0%9D%D0%B0%D1%80%D0%BB.png/revision/latest/scale-to-width-down/536?cb=20150411020657&path-prefix=ru' },

    // --- Л / М (Продолжение) ---
    { id: 'lekrotta', n: 'Лекротта', d: 'Крупное существо, напоминающее гиену, способное имитировать человеческие голоса.', danger: 'XXXX', hab: 'Индия', cat: 'Волшебные существа', ic: 'https://static.wikia.nocookie.net/harrypotter/images/4/40/Leucrotta_COG.png/revision/latest/scale-to-width-down/535?cb=20210914042841' },
    { id: 'merrow', n: 'Мерроу', d: 'Шотландская разновидность водяного народа. Менее красивые, чем сирены, но очень музыкальные.', danger: 'XXX', hab: 'Шотландия', cat: 'Обитатели Озера', ic: 'https://static.wikia.nocookie.net/harrypotter/images/3/3f/Merpeople_%28Concept_Artwork_01%29.jpg/revision/latest/scale-to-width-down/536?cb=20170101044538&path-prefix=ru' },
    { id: 'morklake', n: 'Морщерогий кизляк', d: 'Существо, в которое верит Ксенофилиус Лавгуд. Говорят, живет в Швеции и не умеет летать.', danger: '?', hab: 'Швеция', cat: 'Существа, выдуманные К. Лавгудом', ic: 'https://static.wikia.nocookie.net/harrypotter/images/e/e2/Crumple-Horned_Snorkack.png/revision/latest?cb=20250201011352' },
    { id: 'murtlap_2', n: 'Мурлокомль', d: 'Существо, напоминающее розовый гриб с щетиной. Питается земляными червями.', danger: 'XX', hab: 'Скандинавия', cat: 'Волшебные существа', ic: 'https://static.wikia.nocookie.net/harrypotter/images/7/72/Horklump.png/revision/latest/scale-to-width-down/536?cb=20171229045705&path-prefix=ru' },

    // --- О (Продолжение) ---
    { id: 'ogre', n: 'Огр', d: 'Магическое существо, похожее на великана, но меньшего размера. Часто упоминается в сказках магов.', danger: 'XXXX', hab: 'Британия', cat: 'Волшебные существа', ic: 'https://static.wikia.nocookie.net/harrypotter/images/8/85/OgreHPFS.PNG/revision/latest?cb=20240223142331' },
    { id: 'oni', n: 'Они', d: 'Японский демон, обладающий огромной силой и рогами. Родственник тролля.', danger: 'XXXX', hab: 'Япония', cat: 'Демоны', ic: 'https://static.wikia.nocookie.net/harrypotter/images/b/bc/Oni.png/revision/latest?cb=20190821125629&path-prefix=ru' },

    // --- П (Продолжение) ---
    { id: 'plimp', n: 'Пухлый заглот', d: 'Шарообразная рыба с двумя длинными ногами. Обожает кусать ноги пловцов.', danger: 'XXX', hab: 'Глубокие озёра', cat: 'Рыбы', ic: 'https://static.wikia.nocookie.net/harrypotter/images/a/a6/Gulping_Plimpy_PP.jpg/revision/latest/scale-to-width-down/180?cb=20250723010627' },
    { id: 'lobster', n: 'Пятнистый клешнепод', d: 'Похож на сухопутного омара. Его укус делает жертву крайне невезучей на 24 часа.', danger: 'XXX', hab: 'Побережья', cat: 'Волшебные существа', ic: 'https://static.wikia.nocookie.net/harrypotter/images/7/7a/Macled_Malaclaw_FBCFWW.png/revision/latest?cb=20170413070029&path-prefix=ru' },

    // --- ДРАКОНЫ (Специфические виды из списка) ---
    { id: 'dragon_welsh', n: 'Валлийский зелёный дракон', d: 'Относительно мирный вид. Издает мелодичный рев. Питается овцами.', danger: 'XXXXX', hab: 'Уэльс', cat: 'Драконы', ic: 'https://static.wikia.nocookie.net/harrypotter/images/c/c3/Welsh_Green.jpg/revision/latest/scale-to-width-down/536?cb=20181017103141&path-prefix=ru' },
    { id: 'dragon_hebridean', n: 'Гебридский чёрный дракон', d: 'Имеет чешую, похожую на кольчугу, и стреловидный хвост. Очень агрессивен.', danger: 'XXXXX', hab: 'Гебридские острова', cat: 'Драконы', ic: 'https://static.wikia.nocookie.net/harrypotter/images/b/bd/Hebridean_Black_01_HL.png/revision/latest/scale-to-width-down/536?cb=20230625153037' },
    { id: 'dragon_hungarian', n: 'Венгерский хвосторог', d: 'Самый опасный вид. Шипы на хвосте и пламя на 15 метров.', danger: 'XXXXX', hab: 'Венгрия', cat: 'Драконы', ic: 'https://static.wikia.nocookie.net/harrypotter/images/1/1f/%D0%92%D0%B5%D0%BD%D0%B3%D0%B5%D1%80%D1%81%D0%BA%D0%B0%D1%8F_%D1%85%D0%B2%D0%BE%D1%81%D1%82%D0%BE%D1%80%D0%BE%D0%B3%D0%B0_%D0%BD%D0%B0_%D0%90%D1%80%D0%B5%D0%BD%D0%B5_%28%D0%93%D0%9F%D0%B8%D0%9A%D0%9E%29.jpg/revision/latest/scale-to-width-down/536?cb=20150116005338&path-prefix=ru' },
    { id: 'dragon_chinese', n: 'Китайский огненный шар', d: 'Единственный восточный дракон. Чешуя алого цвета, из ноздрей идет грибовидное пламя.', danger: 'XXXXX', hab: 'Китай', cat: 'Драконы', ic: 'https://static.wikia.nocookie.net/harrypotter/images/d/df/Chinese_Fireball.png/revision/latest?cb=20161203144845&path-prefix=ru' },
    { id: 'dragon_norwegian', n: 'Норвежский горбатый дракон', d: 'Похож на хвосторога, но имеет гребень на спине. Ядовитые клыки.', danger: 'XXXXX', hab: 'Норвегия', cat: 'Драконы', ic: 'https://static.wikia.nocookie.net/harrypotter/images/9/9b/Norwegian_Ridgeback_PM.jpg/revision/latest/scale-to-width-down/536?cb=20240725185047' },
    { id: 'dragon_ukrainian', n: 'Украинский железнобрюхий дракон', d: 'Самый крупный вид. Вес до 6 тонн. Медленный, но давит дома своим весом.', danger: 'XXXXX', hab: 'Украина', cat: 'Драконы', ic: 'https://static.wikia.nocookie.net/harrypotter/images/0/0d/%D0%A2%D1%80%D0%B8%D0%BE_%D0%BD%D0%B0_%D0%B4%D1%80%D0%B0%D0%BA%D0%BE%D0%BD%D0%B5_%28%D0%93%D0%9F%D0%B8%D0%94%D0%A12%29.jpg/revision/latest/scale-to-width-down/536?cb=20150103195627&path-prefix=ru' },

    // --- ТРОЛЛИ ---
    { id: 'troll_mountain', n: 'Горный тролль', d: 'Самый свирепый и крупный вид троллей. Серая кожа, огромная дубина.', danger: 'XXXX', hab: 'Горы', cat: 'Тролли', ic: 'https://static.wikia.nocookie.net/harrypotter/images/b/ba/Mountain_Troll.png/revision/latest?cb=20161203150627&path-prefix=ru' },
    { id: 'troll_forest', n: 'Лесной тролль', d: 'Имеет бледно-зеленую кожу. Меньше горного, но хитрее.', danger: 'XXXX', hab: 'Леса', cat: 'Тролли', ic: 'https://static.wikia.nocookie.net/harrypotter/images/4/4f/Forest_troll.png/revision/latest?cb=20161203150701&path-prefix=ru' },
    { id: 'troll_river', n: 'Речной тролль', d: 'Имеет рога и косматую шкуру. Часто прячется под мостами.', danger: 'XXXX', hab: 'Реки', cat: 'Тролли', ic: 'https://static.wikia.nocookie.net/harrypotter/images/3/35/RiverTroll.png/revision/latest/scale-to-width-down/535?cb=20160422114651&path-prefix=ru' },

    // --- Ш / Э / Я ---
    { id: 'shishuga', n: 'Шишуга', d: 'Похожа на джарви, но живет в воде. Нападает на рыбаков, затягивая их в тину.', danger: 'XXX', hab: 'Заводи', cat: 'Водные', ic: 'https://static.wikia.nocookie.net/harrypotter/images/5/56/Crup.png/revision/latest/scale-to-width-down/536?cb=20170531052432&path-prefix=ru' },
    { id: 'shleppi', n: 'Шлёппи', d: 'Маленькое существо, которое цепляется к одежде и вызывает чувство сильной усталости.', danger: 'XX', hab: 'Везде', cat: 'Паразиты', ic: 'https://static.wikia.nocookie.net/harrypotter/images/b/b9/Plimpy.png/revision/latest/scale-to-width-down/536?cb=20171229041743&path-prefix=ru' },
    { id: 'vampire_bumblebee', n: 'Шмель-вампир', d: 'Насекомое, которое вместо нектара пытается выпить капельку крови мага.', danger: 'XX', hab: 'Поля', cat: 'Насекомые', ic: 'https://static.wikia.nocookie.net/harrypotter/images/f/f0/Vampyr_Mosp.png/revision/latest/scale-to-width-down/535?cb=20130820024041' },
    { id: 'erkling_2', n: 'Эрклинг', d: 'Похож на гоблина с острым лицом. Заманивает детей смехом, чтобы съесть.', danger: 'XXXX', hab: 'Шварцвальд', cat: 'Волшебные существа', ic: 'https://static.wikia.nocookie.net/harrypotter/images/4/48/Erkling.png/revision/latest/scale-to-width-down/536?cb=20171229051015&path-prefix=ru' },
    { id: 'ethonon_wing', n: 'Этонский крылатый конь', d: 'Редкая порода. Считается самой выносливой среди крылатых коней.', danger: 'XX', hab: 'Британия', cat: 'Крылатые кони', ic: 'https://static.wikia.nocookie.net/harrypotter/images/2/23/Aethonan.jpg/revision/latest/scale-to-width-down/536?cb=20170705130914&path-prefix=ru' },
    { id: 'toxic_duck', n: 'Ядовитая утка', d: 'Выглядит как обычная утка, но её перья покрыты ядовитой слизью. Защитный механизм.', danger: 'XXX', hab: 'Пруды', cat: 'Птицы', ic: 'https://static.wikia.nocookie.net/find-the-ducks/images/3/35/Insertanyfandomhere.png/revision/latest?cb=20220815013810' },
    { id: 'yambo_fin', n: 'Ямбо', d: 'Африканское существо, похожее на смесь зебры и жирафа, но с магическими свойствами невидимости.', danger: 'XXX', hab: 'Африка', cat: 'Волшебные существа', ic: 'https://static.wikia.nocookie.net/harrypotter/images/8/87/House-Elves_at_the_Quidditch_World_Cup_01.jpg/revision/latest/scale-to-width-down/250?cb=20180426141332&path-prefix=ru' },

    // --- НОВЫЕ СУЩЕСТВА (Звери Ньюта) ---
    { 
        id: 'niffler', n: 'Нюхлер', 
        d: 'Пушистый воришка с бездонной сумкой на животе. Одержим золотом и всем блестящим.', 
        danger: 'XXX', hab: 'Британия', feat: 'Поиск сокровищ', cat: 'Звери Ньюта', ic: 'nuhler.png' 
    },
    { 
        id: 'occamy', n: 'Оками', 
        d: 'Крылатый змей. Хоранаптик: мгновенно меняет размер, заполняя всё доступное пространство.', 
        danger: 'XXXX', hab: 'Индия', feat: 'Смена размера', cat: 'Звери Ньюта', ic: 'okami.png' 
    },
	{ 
        id: 'thunderbird', n: 'Птица-гром', 
        d: 'Мощная птица с тремя парами крыльев. Создает штормы и чувствует угрозы.', 
        danger: 'XXXX', hab: 'Аризона', feat: 'Магия шторма', cat: 'Мифические', ic: 'pticagrom.png' 
    },
    { 
        id: 'poluvid', n: 'Полувид', 
        d: 'Мирное существо-обезьяна. Умеет становиться невидимым и видеть ближайшее будущее.', 
        danger: 'XXXX', hab: 'Восток', feat: 'Невидимость', cat: 'Звери Ньюта', ic: 'poluviditch.png' 
    },
    { 
        id: 'vzrivopotam', n: 'Взрывопотам', 
        d: 'Гигантское животное с бронированной шкурой. Рог наполнен взрывоопасной жидкостью.', 
        danger: 'XXXX', hab: 'Африка', feat: 'Взрывной рог', cat: 'Опасные', ic: 'vzrivopotam.png' 
    },
	
    {
        id: 'bicorn',
        n: 'Двурог',
        d: 'Редкое магическое животное. Его растёртый рог входит в состав Оборотного и Бодроперцового зелий, поэтому считается запрещённым ингредиентом.',
        danger: 'XX',
        hab: 'Тайные местности, лаборатории и зельевыхарни',
        cat: 'Магические животные',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/a/af/Bicorn.jpg/revision/latest?cb=20110815200812&path-prefix=ru'
    },
    {
        id: 'demiguise',
        n: 'Демимаска',
        d: 'Мягкошёрстное, кроткое существо, способное становиться невидимым. Его шубка очень прочна и используется для изготовления плащей‑невидимок.',
        danger: 'X',
        hab: 'Леса, горные долины',
        cat: 'Магические существа',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/a/a1/Demiguise.png/revision/latest?cb=20170216193938&path-prefix=ru'
    },
    {
        id: 'dirinal',
        n: 'Диринар',
        d: 'Маленький пушистый котоподобный зверёк с крайне хорошим чутьём на опасность и хозяев‑магов.',
        danger: 'X',
        hab: 'Дома магов, семьи, живёт в качестве питомца',
        cat: 'Домашние магические животные',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/9/99/%D0%94%D0%B8%D1%80%D0%B8%D0%BA%D0%BE%D0%BB_%D0%B8%D0%BB%D0%BB%D1%8E%D1%81%D1%82%D1%80%D0%B0%D1%86%D0%B8%D1%8F_%D0%B8%D0%B7_%D0%BA%D0%BD%D0%B8%D0%B3%D0%B8_%D0%97%D0%B0%D0%BF%D0%B8%D1%81%D0%BA%D0%B8_%D0%BC%D0%B0%D0%B3%D0%BE%D0%B7%D0%BE%D0%BE%D0%BB%D0%BE%D0%B3%D0%B0.jpg/revision/latest?cb=20171112122822&path-prefix=ru'
    },
    {
        id: 'doxy',
        n: 'Докси',
        d: 'Маленький, похожий на мокрицу магический существ, обитающий в домах и магических зонах. Кусается, если его потревожить.',
        danger: 'X',
        hab: 'Дома магов, в старых мебели и магических запасниках',
        cat: 'Насекомые',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/1/1c/%D0%94%D0%BE%D0%BA%D1%81%D0%B8.png/revision/latest?cb=20170129163721&path-prefix=ru'
    },
    {
        id: 'kneazle',
        n: 'Жмыр',
        d: 'Очень умная, чистопородная магическая кошка, способная находить и предупреждать хозяина об опасности.',
        danger: 'X',
        hab: 'Дома и семьи магов, особенно в магических кварталах',
        cat: 'Магические животные',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/f/f6/Kneazle.jpg/revision/latest?cb=20170528133521&path-prefix=ru'
    },
	
    {
        id: 'pygmy_puff',
        n: 'Карликовый пушистик',
        d: 'Маленькое шарообразное магическое существо, покрытое мягким мехом. Очень любит ласку и легко приручается как питомец.',
        danger: 'X',
        hab: 'Дома магов, магазины волшебных существ',
        cat: 'Пушишки',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/c/ca/Arnold_Pygmy_Puff_Concept_Art.jpg/revision/latest?cb=20170101150931'
    },
    {
        id: 'catalonian_fireball',
        n: 'Каталонский огненный шар',
        d: 'Дракон, похожий на маленького гигантского шара. Его пламя ярко‑оранжевое и чрезвычайно горячее. Считается одной из редких пород драконов.',
        danger: 'XXXXX',
        hab: 'Горы Каталонии, Испания',
        cat: 'Драконы',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/4/4f/Дракон_Каталонский_огненный_шар_(Catalonian_Fireball).jpg/revision/latest?cb=20150117032628&path-prefix=ru'
    },
	{
        id: 'matagot',
        n: 'Матагот',
        d: 'Магический кот‑призрак, внешне похожий на худую, почти бесшёрстную кошку с неоново‑голубыми глазами. Считается, что он наполняет дом владельца деньгами и удачей, но за эту щедрость приходится платить очень высокую цену.',
        danger: 'XXXX',
        hab: 'Частные дома магов, особенно в Европе',
        cat: 'Магические животные',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/0/0f/Matagot.jpg/revision/latest?cb=20181105174424&path-prefix=ru'
    },
    {
        id: 'brain_shrivel',
        n: 'Мозгошмыг',
        d: 'Маленькое, невидимое летающее существо, которое проникает в ухо и вызывает размягчение мозга, из‑за чего человек становится рассеянным и забывчивым.',
        danger: 'XXX',
        hab: 'Воздух, жилые помещения, магические дома',
        cat: 'Насекомые / духи',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/6/67/Harry_Potter_and_the_Half-Blood_Prince_%282009%29_BDRip_-2xukr%2C_eng-_-Hurtom--%28039144%2923-30-24-.jpg/revision/latest?cb=20140310173353&path-prefix=ru'
    },
    {
        id: 'sea_serpent',
        n: 'Морской змей',
        d: 'Гигантское змееподобное существо, живущее в океанах. Способно опрокидывать магические корабли и нападать на морских магов.',
        danger: 'XXXXX',
        hab: 'Океаны, глубокие моря',
        cat: 'Змеи',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/2/2f/Sea_Serpent.png/revision/latest?cb=20161203141238&path-prefix=ru'
    },
    {
        id: 'antipodean_opaleye',
        n: 'Опаловоглазый антипод',
        d: 'Дракон из Новой Зеландии с ярким, переливающимся огнём и глазами, напоминающими опал. Считается одним из самых красивых драконов.',
        danger: 'XXXXX',
        hab: 'Магические зоны Новой Зеландии',
        cat: 'Драконы',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/9/97/Antipodean_Opaleye.png/revision/latest?cb=20171229044433&path-prefix=ru'
    },
    {
        id: 'obscurus',
        n: 'Обскур',
        d: 'Сгусток тёмной магии, образующийся в детях, которые подавляют свою магию. Представляет собой неуправляемую силу, разрушающую всё вокруг.',
        danger: 'XXXXX',
        hab: 'Города магов, магические зоны',
        cat: 'Тёмные магические сущности',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/c/c7/Capture.PNG/revision/latest?cb=20181122064837'
    },
    {
        id: 'ashwinder',
        n: 'Огневица',
        d: 'Магическое змееподобное существо, которое появляется из очень горячего костра, способного прожечь камень. Сама по себе выглядит как тонкая змея с ярко‑оранжевой кожей.',
        danger: 'XXXX',
        hab: 'Большие огни, магические костры, лагеря',
        cat: 'Змеи',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/4/4d/Ashwinder_HM.png/revision/latest?cb=20220320183354'
    },
    {
        id: 'fire_chicken',
        n: 'Огнедышащий цыплёнок',
        d: 'Маленький куропаткообразный магический цыплёнок, способный испускать короткие языки пламени. Часто используется как забавная игрушка или магическое украшение.',
        danger: 'X',
        hab: 'Дома магов, магазины сувениров',
        cat: 'Птицы',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/6/65/Fire-breathing_chicken_%28cropped%29_WU.png/revision/latest?cb=20220207160637'
    },
    {
        id: 'pukwudgie',
        n: 'Пакваджи',
        d: 'Маленький, злопамятный магический гном, обитающий в лесах. Считается, что он может мстить магам, в чём‑то его обидевшим.',
        danger: 'XXX',
        hab: 'Леса Северной Америки',
        cat: 'Магические существа',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/4/42/PukwudgieWU.png/revision/latest?cb=20200219024323&path-prefix=ru'
    },
    {
        id: 'peruvian_vipertooth',
        n: 'Перуанский змеезуб',
        d: 'Небольшой, но крайне быстрый и ядовитый дракон, живущий в горах Перу. Его ярко‑зелёное тело и тонкие зубы делают его особенно опасным.',
        danger: 'XXXXX',
        hab: 'Горы Перу, высокогорные зоны',
        cat: 'Драконы',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/7/71/Peruvian_Vipertooth.png/revision/latest?cb=20161203150121&path-prefix=ru'
    },
    {
        id: 'swooping_evil',
        n: 'Пикирующий злыдень',
        d: 'Маленький, быстрый, летающий магический монстр, похожий на насекомое. Способен атаковать магов, сбивая их с ног и внося в растерянность.',
        danger: 'XXX',
        hab: 'Горы, магические зоны',
        cat: 'Магические монстры',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/0/0d/Swooping_Evil_concept_art.png/revision/latest?cb=20170103060229&path-prefix=ru'
    },
    {
        id: 'flesh_eating_slug',
        n: 'Плотоядные слизняки',
        d: 'Огромные слизни, питающиеся живой плотью. Медленно и мучительно съедают жертву, оставляя после себя только кости.',
        danger: 'XXXXX',
        hab: 'Тёмные магические зоны, подземелья',
        cat: 'Слизни',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/c/ce/FleshEatingSlugWU.png/revision/latest?cb=20200715141847'
    },
    {
        id: 'poltergeist',
        n: 'Полтергейст',
        d: 'Шумный, проказливый дух, который швыряет мебель, ломает вещи и пугает магов. Обычно привязан к одному месту.',
        danger: 'XX',
        hab: 'Старые дома, магические здания, замки',
        cat: 'Призраки',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/5/53/%D0%9F%D0%B8%D0%B2%D0%B7.png/revision/latest?cb=20170224045005&path-prefix=ru'
    },
    {
        id: 'post_owls',
        n: 'Почтовые совы',
        d: 'Обычные совы‑курьеры, доставляющие письма и посылки магам по всему миру. Спокойны и предсказуемы, но иногда могут быть очень шумными.',
        danger: 'X',
        hab: 'Почтовые отделения, дома магов',
        cat: 'Птицы',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/7/7c/%D0%A1%D0%BE%D0%B2%D1%8B_%D0%BD%D0%B0_%D1%81%D1%82%D0%BE%D0%BB%D0%B5_%D0%93%D1%80%D0%B8%D1%84%D1%84%D0%B8%D0%BD%D0%B4%D0%BE%D1%80%D0%B0.jpg/revision/latest?cb=20150320113120&path-prefix=ru'
    },
	{
        id: 'ramora',
        n: 'Рамора',
        d: 'Серебристая магическая рыба, обитающая в Индийском океане. Обладает сильной магией, способной приостанавливать движение кораблей и помогать морякам.',
        danger: 'X',
        hab: 'Индийский океан, морские магические зоны',
        cat: 'Рыбы',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/6/63/Ramora_FB_IE.jpg/revision/latest?cb=20210914155800'
    },
    {
        id: 'murtlap',
        n: 'Растопырник',
        d: 'Маленькое магическое прибрежное существо, похожее на морской огурец. Его щупальца используют для приготовления настойки, защищающей от заклинаний и заживляющей раны.',
        danger: 'X',
        hab: 'Прибрежные зоны, морские береги',
        cat: 'Магические существа',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/d/da/Murtlap_FBCFWW.png/revision/latest?cb=20170413065853&path-prefix=ru'
    },
    {
        id: 'kreeem',
        n: 'Ре-эм',
        d: 'Магическое существо, похожее на гигантского червя‑слизень. Сильный и громко кричащий, может сильно напугать мага, ударяясь о него.',
        danger: 'XXX',
        hab: 'Тропические зоны, магические леса',
        cat: 'Магические существа',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/6/67/Re%27em.png/revision/latest?cb=20170103060625&path-prefix=ru'
    },
    {
        id: 'horned_serpent',
        n: 'Рогатый змей',
        d: 'Сильный магический змей с рогами, обитающий в Северной Америке. Его рога и чешуя ценятся в магии и магических зельях.',
        danger: 'XXXX',
        hab: 'Магические леса Северной Америки',
        cat: 'Змеи',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/f/f6/Horned_Serpent.jpg/revision/latest?cb=20170315040449&path-prefix=ru'
    },
    {
        id: 'rougarou',
        n: 'Ругару',
        d: 'Полу‑человеческий магический монстр из французской магической мифологии, похожий на оборотня‑волка. Считается крайне опасным для магов.',
        danger: 'XXXXX',
        hab: 'Французские магические зоны, леса',
        cat: 'Магические монстры',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/d/dc/Rougarou.png/revision/latest?cb=20250426014958'
    },
    {
        id: 'romania_longhorn',
        n: 'Румынский длиннорог',
        d: 'Крупный дракон с длинными рогами и ярко‑оранжевым пламенем. Один из самых малоизученных и дикого драконов.',
        danger: 'XXXXX',
        hab: 'Румынские горы, магические зоны',
        cat: 'Драконы',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/3/3a/Румынский_длиннорог.gif/revision/latest?cb=20120116133509&path-prefix=ru'
    },
    {
        id: 'runespoor',
        n: 'Рунеспур',
        d: 'Трёхголовый змей, каждый из голов которого выполняет свою функцию: одна думает, вторая решает, третья действует. Считается очень умным.',
        danger: 'XXXX',
        hab: 'Тропические магические зоны',
        cat: 'Змеи',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/6/6e/Runespoor.png/revision/latest?cb=20161203145811&path-prefix=ru'
    },
	{
        id: 'scrutty',
        n: 'Скрытень',
        d: 'Маленький магический зверёк, живущий в гарнизонах и магических домах, часто прячется в укрытиях и магических зонах. Считается очень редким существом.',
        danger: 'X',
        hab: 'Магические гарнизоны, магические дома',
        cat: 'Магические существа',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/6/6c/Скрытень_Хайдбихайнд_художник_Томислав_Томич.jpg/revision/latest?cb=20171112124353&path-prefix=ru'
    },
    {
        id: 'sirens',
        n: 'Сирены',
        d: 'Водные магические существа с красивым голосом, способные очаровать моряков и магов своим пением. Обычно живут в морях и озёрах.',
        danger: 'XXXX',
        hab: 'Морские магические зоны, моря',
        cat: 'Водные магические существа',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/4/4b/Tumblr_mzvnqh5R3C1rb19a9o1_250.gif/revision/latest?cb=20140306120036&path-prefix=ru'
    },
    {
        id: 'sasquatch',
        n: 'Сасквотч',
        d: 'Гигантский магический монстр, живущий в лесах. Считается крайне редким и пугающим магов, особенно в горных регионах.',
        danger: 'XXXX',
        hab: 'Горные леса, магические зоны',
        cat: 'Магические монстры',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/8/8d/Sasquatch.png/revision/latest?cb=20161127114835&path-prefix=ru'
    },
    {
        id: 'selkie',
        n: 'Селки',
        d: 'Водные магические существа, моряки‑сирены, помогающие морякам в магических морях. Их магия связана с водой и морскими существами.',
        danger: 'X',
        hab: 'Морские магические зоны, озёра',
        cat: 'Морские магические существа',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/a/a4/One_of_the_Merpeople_of_Hogwarts_Lake_%28Concept_Artwork%29.jpg/revision/latest?cb=20170101055820&path-prefix=ru'
    },
    {
        id: 'selma',
        n: 'Сельма',
        d: 'Маленькая магическая сущность, обитающая на солнечных магиях и магических зонах. Считается защитницей магических зон.',
        danger: 'X',
        hab: 'Солнечные магические зоны',
        cat: 'Магические сущности',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/e/ee/Selma.png/revision/latest?cb=20180318013257'
    },
	{
        id: 'smrkt',
        n: 'Смеркут',
        d: 'Чёрное, похожее на плащ магическое существо, обитающее в тропиках. Полу‑разумное хищное создание, которое подкрадывается к спящему и задушает его, обмотав телом, поэтому его считают крайне опасным.',
        danger: 'XXXXX',
        hab: 'Тропические магические зоны',
        cat: 'Нежить / хищники',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/0/03/LethifoldWU.png/revision/latest?cb=20200220112809&path-prefix=ru'
    },
    {
        id: 'snallygaster',
        n: 'Сналлигастер',
        d: 'Магическое монструозное существо, похожее на гибрид дракона и хищной птицы, обитающее в США. Считается крайне агрессивным и опасным для магов и магических животных.',
        danger: 'XXXXX',
        hab: 'Магические зоны США',
        cat: 'Магические монстры',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/c/c3/Snallygaster.jpg/revision/latest?cb=20170728161720&path-prefix=ru'
    },
    {
        id: 'blastended_skrewt',
        n: 'Соплохвост',
        d: 'Складное магическое существо с выбрасывающимся огненным хвостом, созданное в результате экспериментов. Существование крайне опасно для магов и магических зон.',
        danger: 'XXXXX',
        hab: 'Магические лаборатории, магические зоны',
        cat: 'Экспериментальные магические монстры',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/5/5c/Blast-Ended_Skrewt.png/revision/latest?cb=20150903180619&path-prefix=ru'
    },
	{
        id: 'shrimp_creature',
        n: 'Существо-креветка',
        d: 'Магическая морская креветка, обитающая в океанах. Обычно служит пищей для морских магических существ и магов, но может быть опасна при контакте с заклинаниями.',
        danger: 'X',
        hab: 'Океаны, морские магические зоны',
        cat: 'Рыбы',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/e/ec/FBaWtFT_Newt_Scamander_11.jpg/revision/latest?cb=20161209180917&path-prefix=ru'
    },
    {
        id: 'tebo',
        n: 'Тибо',
        d: 'Магический мелкий грызун‑крот, обитающий в подземных зонах. Считается очень редким и интересным существом для магических зоопарков.',
        danger: 'X',
        hab: 'Подземные зоны, магические зоны',
        cat: 'Магические животные',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/9/9a/Tebo.png/revision/latest?cb=20161203142333&path-prefix=ru'
    },
    {
        id: 'burybog',
        n: 'Топеройка',
        d: 'Маленький, грязный магический существа, живущий в болотах. Считается крайне непривлекательным и вредным для магов.',
        danger: 'X',
        hab: 'Болота, магические зоны',
        cat: 'Магические существа',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/a/a5/Dugbog_HL.jpeg/revision/latest?cb=20250917141928'
    },
    {
        id: 'three_headed_dog',
        n: 'Трёхголовая собака',
        d: 'Гигантская магическая собака с тремя головами, служащая охранником. Обычно живёт в подземельях и магических зонах.',
        danger: 'XXXXX',
        hab: 'Подземелья, магические зоны',
        cat: 'Магические животные',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/1/12/Fluffy.jpg/revision/latest?cb=20160109091741&path-prefix=ru'
    },
    {
        id: 'troll_nadrog',
        n: 'Тролль из Надрож',
        d: 'Магический тролль, живущий в горах Надрож. Считается крайне опасным и агрессивным к магам.',
        danger: 'XXXXX',
        hab: 'Горы Надрож',
        cat: 'Тролли',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/c/c9/Тролль_из_Надрож.jpg/revision/latest?cb=20120613054640&path-prefix=ruE'
    },
    {
        id: 'vampyr',
        n: 'Упырь',
        d: 'Магическое существо‑упырь, живущее в темных зонах. Считается опасным и опасным к магам.',
        danger: 'XXXXX',
        hab: 'Тёмные магические зоны',
        cat: 'Нежить',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/c/cb/Ghoul_HM.png/revision/latest?cb=20181104134630&path-prefix=ru'
    },
    {
        id: 'fwooper',
        n: 'Фвупер',
        d: 'Магическая птица, напоминающая куропатку. Считается очень редкой и интересной для магических зон.',
        danger: 'X',
        hab: 'Магические зоны',
        cat: 'Птицы',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/1/14/Fwooper_concept_art.png/revision/latest?cb=20170103064300&path-prefix=ru'
    },
    {
        id: 'phoenix',
        n: 'Феникс',
        d: 'Магическая птица, живущая в магических зонах. Считается символом магической магии.',
        danger: 'X',
        hab: 'Магические зоны',
        cat: 'Птицы',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/c/c1/Phoenix.png/revision/latest?cb=20161203142511&path-prefix=ru'
    },
    {
        id: 'fairy',
        n: 'Фея',
        d: 'Маленькая магическая существа, живущая в магических зонах. Считается магическим магическим существом.',
        danger: 'X',
        hab: 'Магические зоны',
        cat: 'Магические существа',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/4/43/Fairy.png/revision/latest?cb=20161203144610&path-prefix=ru'
    },
	{
        id: 'hodag',
        n: 'Ходаг',
        d: 'Рогатое магическое существо размером примерно с большую собаку, с длинными клыками и красными светящимися глазами. Обитает в Северной Америке и считается очень опасным, но его рога ценятся в магии.',
        danger: 'XXXX',
        hab: 'Леса Северной Америки',
        cat: 'Магические монстры',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/7/77/Hodag.jpeg/revision/latest?cb=20180820075721&path-prefix=ru'
    },
    {
        id: 'hoo_hoo',
        n: 'Ху-ху',
        d: 'Маленькая магическая сова с очень выразительными глазами, живущая в лесах и магических деревнях. Способна предупреждать магов об опасности своим криком.',
        danger: 'X',
        hab: 'Леса, магические деревни',
        cat: 'Птицы',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/2/24/Hoo-hoo.png/revision/latest?cb=20210220162203&path-prefix=ru'
    },
	
    {
        id: 'qilin',
        n: 'Цилинь',
        d: 'Рогатое магическое существо, похожее на китайского кирина: благородное, с мягким светящимся ореолом и чешуйчатой шкурой. Считается символом справедливости, удачи и выбора «правильной» стороны в магическом конфликте.',
        danger: 'X',
        hab: 'Магические зоны Азии, священные леса',
        cat: 'Магические мифические существа',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/a/a9/Qilin_SODM.png/revision/latest?cb=20220620161738'
    },
    {
        id: 'chizpurfle',
        n: 'Чизпурфл',
        d: 'Крошечное паразитическое магическое существо, похожее на клеща. Его укусы вызывают сильный зуд, а накопление в больших количествах может повредить магическим механизмам и артефактам.',
        danger: 'XX',
        hab: 'Жилища магов, магические зоны, рядом с магическими предметами',
        cat: 'Паразиты',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/9/9b/Chizpurfle.png/revision/latest?cb=20161203140349&path-prefix=ru'
    },
    {
        id: 'chupacabra',
        n: 'Чупакабра',
        d: 'Мифическое магическое существо, похожее на рептилию или гибрид животных, с шипами и темными глазами. Считается, что оно атакует и «высасывает» кровь домашних животных, особенно в магических деревнях Латинской Америки.',
        danger: 'XXXX',
        hab: 'Сельские магические зоны Латинской Америки',
        cat: 'Магические монстры',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/e/e8/Chupacabra_COGC.jpeg/revision/latest?cb=20241215141252'
    },
	{
        id: 'nogtail',
        n: 'Штырехвост',
        d: 'Маленькое, злобное магическое существо, похожее на крысу с остроконечным шипастым хвостом. Считается предвестником бедствий и часто появляется перед катастрофами.',
        danger: 'XXXX',
        hab: 'Сельские зоны, магические деревни',
        cat: 'Магические монстры',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/2/2f/Nogtail-PM.png/revision/latest?cb=20161222114410&path-prefix=ru'
    },
    {
        id: 'shrake',
        n: 'Шпротва',
        d: 'Огромная морская магическая рыба, напоминающая гигантскую акулу с ярко‑синей чешуёй. Обитает в холодных морях и может атаковать магических мореплавателей.',
        danger: 'XXXXX',
        hab: 'Холодные моря, магические зоны',
        cat: 'Рыбы',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/c/c6/Shrake_-_Neal_Murren_for_Pottermore.png/revision/latest?cb=20180513194245'
    },
    {
        id: 'spiny_matriarch_spider',
        n: 'Шипастый паук-матриарх',
        d: 'Гигантский паук с шипастым телом и когтистыми ногами, живущий в подземельях. Считается крайне опасным и агрессивным к магам.',
        danger: 'XXXXX',
        hab: 'Подземелья, магические зоны',
        cat: 'Паукообразные',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/e/e5/TheInsatiableSpider.png/revision/latest?cb=20230910061224'
    },
    {
        id: 'swedish_short_snout',
        n: 'Шведский короткорылый дракон',
        d: 'Один из самых красивых драконов, с блестящими серебристо‑синими чешуйками и короткой шеей. Его пламя очень яркое и горячее.',
        danger: 'XXXXX',
        hab: 'Горы Швеции, магические зоны',
        cat: 'Драконы',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/e/e5/Swedish_Short-Snout.jpg/revision/latest?cb=20181017103358&path-prefix=ru'
    },
    {
        id: 'flobberworm',
        n: 'Флоббер-червь',
        d: 'Магический червь, живущий в магических зонах. Считается магическим существом.',
        danger: 'X',
        hab: 'Магические зоны',
        cat: 'Черви',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/0/01/FlobberwormWU.png/revision/latest?cb=20200715131604'
    },
    {
        id: 'hinkypunk',
        n: 'Болотный фонарник',
        d: 'Магическое существо, напоминающее фонарь, живущий в болотах. Считается магическим магическим существом.',
        danger: 'X',
        hab: 'Болота',
        cat: 'Магические существа',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/e/e2/Hinkypunk_PM.png/revision/latest?cb=20161225125639&path-prefix=ru'
    },
	
    {
        id: 'quintaped',
        n: 'Пятиног',
        d: 'Чрезвычайно опасный хищник, живущий только на острове Дрир. Пятилапое существо с клешнеобразными лапами и густой шерстью, питающееся человеческой плотью.',
        danger: 'XXXXX',
        hab: 'Остров Дрир, Шотландия',
        cat: 'Хищники',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/3/3c/%D0%9A%D0%B2%D0%B8%D0%BD%D1%82%D0%BE%D0%BB%D0%B0%D0%BF.jpeg/revision/latest?cb=20120718103428&path-prefix=ru'
    },
	
    {
        id: 'clabbert',
        n: 'Клабберт',
        d: 'Магическая примата-обезьяна, живущая в горах. Считается хитрой и скрытной, часто ведёт себя враждебно по отношению к магам.',
        danger: 'XXX',
        hab: 'Горные районы, вулканические зоны',
        cat: 'Магические животные',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/8/8e/Clabbert.png/revision/latest?cb=20161203145404&path-prefix=ru'
    },
    {
        id: 'fang_puffskein',
        n: 'Клыкастый пушистик',
        d: 'Редкая разновидность пушистика, отличающаяся острыми зубами и более агрессивным поведением по сравнению с обычными пушистиками.',
        danger: 'XX',
        hab: 'Дома магов и магические зоопарки',
        cat: 'Пушишки',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/0/0b/Fanged_Puffskein_HM.png/revision/latest?cb=20210930152648'
    },
    {
        id: 'coccathris',
        n: 'Кокатрис',
        d: 'Птица‑дракон, похожая на феникса, но с более устрашающим видом. Считается, что может быть предвестником смерти.',
        danger: 'XXXX',
        hab: 'Горные районы, магические зоны',
        cat: 'Птицы',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/c/c8/Кокатрис_на_эмблеме_сборной_Франции_по_квиддичу.png/revision/latest?cb=20210924071223&path-prefix=ru'
    },
    {
        id: 'red_caps',
        n: 'Красные колпаки',
        d: 'Маленькие, гномы‑гномы, живущие под землёй и в горах. Носит ярко‑красную шляпу и крайне агрессивен по отношению к магам.',
        danger: 'XXX',
        hab: 'Горы, подземные магические зоны',
        cat: 'Магические существа',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/e/e6/Chaporouge.png/revision/latest?cb=20150329165059&path-prefix=ru'
    },
    {
        id: 'krillmar',
        n: 'Крильмар',
        d: 'Маленький морской магический рыбешка, живущий в океанах. Считается символом морской магии.',
        danger: 'X',
        hab: 'Моря, океаны',
        cat: 'Рыбы',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/1/1a/Lobalug.png/revision/latest?cb=20171229045308&path-prefix=ru'
    },
    {
        id: 'leprechaun',
        n: 'Лепрекон',
        d: 'Ирландский магический существа‑гном, живущий под землёй. Считается, что каждый лепрекон хранит горш с золотом.',
        danger: 'X',
        hab: 'Ирландские поля, магические зоны',
        cat: 'Магические существа',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/3/3f/Leprechaun.png/revision/latest?cb=20200224120709&path-prefix=ru'
    },
    {
        id: 'vampire_bats',
        n: 'Летучие мыши-вампиры',
        d: 'Магические летучие мыши, живущие в горах и магических зонах. Питаются кровью магов и животных.',
        danger: 'XXX',
        hab: 'Горы, магические зоны, тёмные зоны',
        cat: 'Насекомые',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/0/0d/Vampire_bat_QTA_Emily_Gravett.png/revision/latest?cb=20201210133710'
    },
    {
        id: 'loch_ness_monster',
        n: 'Лох-несское чудовище',
        d: 'Огромное морское магическое существо, живущее в озере Лох-Несс. Считается одним из самых загадочных магических существ.',
        danger: 'XXXX',
        hab: 'Озеро Лох-Несс, Шотландия',
        cat: 'Водные существа',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/9/96/Loch_Ness_Kelpie.jpg/revision/latest?cb=20171126073758&path-prefix=ru'
    },
    {
        id: 'moon_frog',
        n: 'Лунная лягушка',
        d: 'Легендарное магическое существо, предположительно живущее на Луне. Считается, что маг мог доставить нескольких лунных лягушек обратно на Землю.',
        danger: 'X',
        hab: 'Легендарные магические зоны',
        cat: 'Легендарные существа',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/3/3d/Moon_frog_PP.jpg/revision/latest?cb=20250723013615'
    },
    {
        id: 'mooncalf',
        n: 'Лунный телец',
        d: 'Чрезвычайно застенчивое магическое существо, выходит из укрытия только в полнолуние. Считается, что его навоз имеет магические свойства.',
        danger: 'X',
        hab: 'Полнолуние, магические зоны',
        cat: 'Магические существа',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/4/4d/Mooncalf_Concept_Art_FB1.png/revision/latest?cb=20170304200812'
    },
    {
        id: 'charmed_skeleton',
        n: 'Заколдованный скелет',
        d: 'Скелет, оживлённый заклинанием. Обычно встречается в магических лабораториях и на практике по защите, может атаковать магов.',
        danger: 'XXX',
        hab: 'Магические классы и подземелья, тренировочные залы',
        cat: 'Нежить',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/d/d8/Charmed_skeleton_HP4_PC.png/revision/latest?cb=20250127150807'
    },
    {
        id: 'zouwu',
        n: 'Зуву',
        d: 'Гигантское магическое существо, напоминающее гибрид медведя, червя и кошки, способное двигаться под землёй и летать.',
        danger: 'XXX',
        hab: 'Подземные регионы, магические зоны и парки',
        cat: 'Магические существа',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/7/78/Zouwu.jpg/revision/latest?cb=20181112215324&path-prefix=ru'
    },
    {
        id: 'inferi',
        n: 'Инфернал',
        d: 'Оживлённые мертвецы, созданные с помощью запрещённой магии. Служат как пешки Темного мага, атакуют живых магов.',
        danger: 'XXXX',
        hab: 'Тёмные регионы, магические зоны Темного мага',
        cat: 'Нежить',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/8/8d/Inferi_%28Concept_Artwork%29.jpg/revision/latest?cb=20170113053515&path-prefix=ru'
    },
    {
        id: 'yeti',
        n: 'Йети',
        d: 'Огромный, покрытый белым мехом существо, живущее в горах. Склонен к одиночеству, но может быть крайне опасен.',
        danger: 'XXXX',
        hab: 'Горные регионы, снежные склоны',
        cat: 'Гигантские магические существа',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/7/7d/Yeti.png/revision/latest?cb=20161203140733&path-prefix=ru'
    },
    {
        id: 'puffskein_appaloosa',
        n: 'Апполосская пушишка',
        d: 'Разновидность пушишки с пятнистым окрасом. Такая же добродушная, плюшевая и покрыта мягким ворсом.',
        danger: 'X',
        hab: 'Дома, питомцы магов',
        cat: 'Пушишки',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/1/1f/Appaloosa_Puffskein_FBA.jpeg/revision/latest?cb=20250227095852'
    },
    {
        id: 'akromantula_forest',
        n: 'Акромантулы Запретного леса',
        d: 'Колонии акромантулов, живущие в Запретном лесу. Крупные, говорящие пауки, очень агрессивны к людям.',
        danger: 'XXXXX',
        hab: 'Запретный лес, Хогвартс',
        cat: 'Паукообразные',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/8/8c/Tumblr_mldi4dBLzw1qb5c2jo4_250.gif/revision/latest?cb=20140709130459&path-prefix=ru'
    },
    {
        id: 'white_river_monster',
        n: 'Белый речной монстр',
        d: 'Гигантское существо, обитающее в реках. Пугает местных магов своим размером и внезапными появлениями.',
        danger: 'XXXX',
        hab: 'Глубокие реки, водные озёра',
        cat: 'Водные',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/2/20/Белый_речной_монстр_HL.jpg/revision/latest?cb=20230721145958&path-prefix=ru'
    },
    {
        id: 'giant_purple_frog',
        n: 'Большая пурпурная жаба',
        d: 'Огромная пурпурная жаба, способная к прыжкам на большие расстояния. Живёт в болотах и влажных лесах.',
        danger: 'XXX',
        hab: 'Болота, магические леса',
        cat: 'Амфибии',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/2/20/Большая_пурпурная_жаба_HL.jpg/revision/latest?cb=20230721145958&path-prefix=ru'
    },
    {
        id: 'witch_being',
        n: 'Ведьма',
        d: 'Женщина‑маг, умеющая творить магию, часто связана с магическими существами и редкими зельями.',
        danger: 'XXXX',
        hab: 'Города магов, магические деревни',
        cat: 'Разумные магические существа',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/5/50/HagWU.png/revision/latest?cb=20190428053709&path-prefix=ru'
    },
    {
        id: 'giants_being',
        n: 'Великаны',
        d: 'Огромные полу‑магические существа, живущие в горах или отдалённых регионах. обладают невыносимой силой.',
        danger: 'XXXXX',
        hab: 'Горы, удалённые долины',
        cat: 'Гиганты',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/4/4f/PottermoreGiantClutchingDeerConceptArt_-_cropped.png/revision/latest?cb=20160428154037&path-prefix=ru'
    },
    {
        id: 'wyvern_being',
        n: 'Виверна',
        d: 'Крылатый змей с двумя лапами. Живёт в скалах и горных местах, опасен своим ядом.',
        danger: 'XXXX',
        hab: 'Горы, скальные ущелья',
        cat: 'Змеи',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/6/63/Wyvern_-_Secrets_of_Dumbledore.png/revision/latest?cb=20220219041527'
    },
    {
        id: 'heliopath',
        n: 'Гелиопат',
        d: 'Огненный дух, мчащийся в воздухе, оставляя за собой огненный хвост. Появляется в места, где много солнечной магии.',
        danger: 'XXXX',
        hab: 'Солнечные зоны, магические поля',
        cat: 'Огненные духи',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/1/1c/Heliopath_PP.jpg/revision/latest?cb=20250723010907'
    },
    {
        id: 'giant_grubs',
        n: 'Гигантские личинки',
        d: 'Огромные личинки магических существ. Питаются органическим мусором и иногда могут быть опасны для магов.',
        danger: 'XX',
        hab: 'Гниющие леса, магические мусорные зоны',
        cat: 'Насекомые',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/0/08/Giant_grub_PM.png/revision/latest?cb=20250130174128'
    },
    {
        id: 'giant_storm_petrel',
        n: 'Гигантский буревестник',
        d: 'Огромные птицы, живущие на побережьях. способны предсказывать штормы своими магическими способностями.',
        danger: 'XXX',
        hab: 'Побережья, океанские острова',
        cat: 'Птицы',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/5/51/Mahoutokoro_storm_petrels.png/revision/latest?cb=20200221155741&path-prefix=ru'
    },
    {
        id: 'giant_snake',
        n: 'Гигантский змей',
        d: 'Огромный змей, способный напасть на магов и магические деревни. живёт в глубоких туннелях.',
        danger: 'XXXX',
        hab: 'Подземелья, пещеры',
        cat: 'Змеи',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/d/d8/Serpenthill.png/revision/latest?cb=20140823193536'
    },
    {
        id: 'giant_squid',
        n: 'Гигантский кальмар',
        d: 'Огромный морской монстр, обитающий в озёрах Хогвартса. Спокойный, но сильный.',
        danger: 'XXXX',
        hab: 'Озёра Хогвартса',
        cat: 'Водные существа',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/2/23/Giant_Squid_MA.jpeg/revision/latest?cb=20240618102645'
    },
    {
        id: 'giant_dung_beetle',
        n: 'Гигантский навозный жук',
        d: 'Огромный жук, живущий в магических навозных кучах. Помогает перерабатывать магический мусор.',
        danger: 'X',
        hab: 'Навозные кучи, магические фермы',
        cat: 'Насекомые',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/3/3e/Giant_Dung_Beetle.jpeg/revision/latest?cb=20181009055540&path-prefix=ru'
    },
    {
        id: 'streeler',
        n: 'Глизень',
        d: 'Ядовитый магический организм, напоминающий слизь. Способен ослеплять магов.',
        danger: 'XXX',
        hab: 'Тёмные магические леса',
        cat: 'Ядовитые существа',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/a/a5/Streeler.jpg/revision/latest?cb=20170706041834&path-prefix=ru'
    },
    {
        id: 'goblin_being',
        n: 'Гоблины',
        d: 'Маленькие, крайне умные магические существа‑банкиры. Живут в магических банках и городах.',
        danger: 'XXX',
        hab: 'Магические банки, подземные магические города',
        cat: 'Гоблины',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/0/09/Goblin-pottermore.png/revision/latest?cb=20130616104000&path-prefix=ru'
    },
    {
        id: 'grim',
        n: 'Грим',
        d: 'Огромная чёрная собака, призрак‑предвестник смерти. Появляется перед гибелью магов.',
        danger: 'XXXX',
        hab: 'Тёмные аллеи, заброшенные леса',
        cat: 'Призраки',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/7/74/Grim_HPWA.jpeg/revision/latest?cb=20240324051727'
    },
    {
        id: 'giant_white_worm',
        n: 'Громадный белый змей',
        d: 'Огромный белый змей‑червь, живущий под землёй. Питается магией.',
        danger: 'XXXX',
        hab: 'Подземные тоннели, магические земли',
        cat: 'Черви',
        ic: 'https://static.wikia.nocookie.net/harrypotter/images/c/c5/Gigantic_white_worm.jpeg/revision/latest?cb=20240609091055'
    }

];

let state={screen:'home',sub:null,libTab:'books',libSearch:'',courseTab:'1',articles:[],lessons:{},notif:true,
  ibSearch:'',ibFilter:''};
function loadState(){try{const s=localStorage.getItem('hg2');if(s){const p=JSON.parse(s);pts=p.pts||pts;articles=p.articles||[];lessons=p.lessons||{};notif=p.notif!==undefined?p.notif:true}}catch(e){}}
function saveState(){localStorage.setItem('hg2',JSON.stringify({pts,articles,lessons,notif}))}
function toast(m){const t=document.createElement('div');t.className='toast';t.textContent=m;document.body.appendChild(t);setTimeout(()=>t.remove(),2000)}
function esc(s){return String(s).replace(/</g,'&lt;').replace(/>/g,'&gt;')}
function maxPts(){return Math.max(pts.g,pts.s,pts.r,pts.h,1)}
// Переход по главным табам (Меню ОБЯЗАНО быть)
function go(s) {
    state.screen = s;
    state.sub = null;
    
    // Принудительно включаем видимость меню
    const nav = document.getElementById('bnav');
    if(nav) nav.style.display = 'flex'; 
    document.body.classList.remove('reading-mode');

    render();
    
    // Подсветка активной иконки
    document.querySelectorAll('.ni').forEach(n => {
        n.classList.toggle('on', n.dataset.n === s);
    });
    window.scrollTo(0, 0);
}

// Открытие подразделов (Устав, Книги)
function sub(s) {
    state.sub = s;
    
    // Если мы открыли книгу (s не null), можем скрыть меню, 
    // НО если ты хочешь его ВСЕГДА, просто закомментируй строку ниже
    if (s) {
        document.body.classList.add('reading-mode');
    } else {
        document.body.classList.remove('reading-mode');
    }
    
    render();
    window.scrollTo(0, 0);
}

function render() {
    const a = document.getElementById('app');
    
    // Базовая отрисовка экранов
    switch(state.screen) {
        case 'home': a.innerHTML = rHome(); break;
      
        case 'lessons': a.innerHTML = rLessons(); break;
        case 'library': a.innerHTML = rLibrary(); break;
        
    }

    // Отрисовка контента поверх (Устав, Персонал и т.д.)
    if (state.sub === 'ustav') a.innerHTML = rUstav();
    if (state.sub === 'staff') a.innerHTML = rStaff();
    
    // Если вдруг меню пропало, эта проверка вернет его, если мы не в режиме чтения
    if (!state.sub) {
        document.body.classList.remove('reading-mode');
    }
}
function rHome(){let h=`<div class="hdr"><h1>HOGWARTS</h1><div class="sub">Школа чародейства и волшебства</div></div><div class="dv"></div><div class="hgrid">`;const mx=maxPts();for(const k of KEYS){const f=FC[k],p=pts[k],pct=Math.round(p/mx*100);h+=`<div class="hc" data-h="${k}"><div class="hg"><div class="sd" data-h="${k}" style="height:${pct}%"></div></div><div class="em">${f.e}</div><div class="hn">${f.n}</div><div class="hp">${p}</div></div>`}h+=`</div><div class="dv"></div><div class="nv"><div class="nvl" onclick="sub('ustav')"><i class="fas fa-scroll"></i><span>Устав школы</span><i class="fas fa-chevron-right ar"></i></div><div class="nvl" onclick="sub('staff')"><i class="fas fa-users"></i><span>Персонал Хогвартса</span><i class="fas fa-chevron-right ar"></i></div></div>`;return h}
// В кнопках "Назад" в Уставе и Персонале используем sub(null)
function rUstav() {
    let h = `<div class="sh"><button onclick="sub(null)"><i class="fas fa-arrow-left"></i></button><h2>Устав школы</h2></div>`;
    // ... твой остальной код устава ...
    USTAV.forEach((u,i)=>{
        h+=`<div class="us${i===0?' op':''}" onclick="this.classList.toggle('op')"><h3>${esc(u.t)}<i class="fas fa-chevron-down"></i></h3><div class="uc">`;
        u.c.forEach(p=>{h+=`<p>${p}</p>`});
        h+=`</div></div>`
    });
    return h;
}

function rStaff() {
    let h = `<div class="sh"><button onclick="sub(null)"><i class="fas fa-arrow-left"></i></button><h2>Персонал Хогвартса</h2></div>`;
    // ... твой остальной код персонала ...
    [...new Set(STAFF.map(s=>s.c))].forEach(cat=>{
        h+=`<div class="cd"><div class="cd-t"><i class="fas fa-users"></i>${esc(cat)}</div>`;
        STAFF.filter(s=>s.c===cat).forEach(s=>{
            h+=`<div class="sc"><div class="sa"><i class="fas ${s.i}"></i></div><div class="si"><div class="sn">${esc(s.n)}${s.tg?' <span style="color:var(--gold);font-size:10px">'+esc(s.tg)+'</span>':''}</div><div class="sr">${esc(s.r)}</div></div></div>`
        });
        h+=`</div>`
    });
    return h;
}
function pubArt(){const t=document.getElementById('artT').value.trim(),b=document.getElementById('artB').value.trim();if(!t||!b)return toast('Заполните поля');articles.push({title:t,body:b,author:user.name,date:new Date().toLocaleDateString('ru-RU')});saveState();toast('Опубликовано!');render()}
function rLessons(){let h=`<div class="sh"><h2>Учебный корпус</h2></div><div class="tabs">`;for(let i=1;i<=7;i++)h+=`<div class="tb${state.courseTab==i?' on':''}" onclick="state.courseTab=${i};render()">${i} курс</div>`;h+=`</div>`;const k='c'+state.courseTab,ls=lessons[k]||[];if(!ls.length)h+=`<div class="es"><i class="fas fa-calendar-xmark"></i><p>Расписание ${state.courseTab}-го курса пусто.</p></div>`;else ls.forEach((l,i)=>{h+=`<div class="li"><div class="lt">${esc(l.time)}</div><div class="lnfo"><div class="ln">${esc(l.name)}</div><div class="ld">${esc(l.teacher)} · ${esc(l.place)}</div></div>${isAdmin()?`<button class="btn btn-d btn-sm" onclick="delL('${k}',${i})"><i class="fas fa-trash"></i></button>`:''}</div>`});if(isAdmin()){h+=`<div class="as" style="margin-top:14px"><h4><i class="fas fa-plus"></i>Добавить урок</h4>`;['lTime|Время (10:00)','lName|Предмет','lTeach|Преподаватель','lPlace|Кабинет'].forEach(x=>{const[id,ph]=x.split('|');h+=`<input id="${id}" placeholder="${ph}" style="width:100%;background:var(--card2);border:1px solid rgba(201,168,76,.12);color:var(--txt);padding:7px;border-radius:6px;font-size:12px;outline:none;margin-bottom:5px">`});h+=`<button class="btn btn-f btn-sm" onclick="addL()">Добавить</button></div>`}return h}
function addL(){const t=document.getElementById('lTime').value.trim(),n=document.getElementById('lName').value.trim(),tc=document.getElementById('lTeach').value.trim(),p=document.getElementById('lPlace').value.trim();if(!t||!n)return toast('Время и предмет');const k='c'+state.courseTab;if(!lessons[k])lessons[k]=[];lessons[k].push({time:t,name:n,teacher:tc||'—',place:p||'—'});lessons[k].sort((a,b)=>a.time.localeCompare(b.time));saveState();toast('Добавлено');render()}
function delL(k,i){if(!lessons[k])return;lessons[k].splice(i,1);saveState();toast('Удалено');render()}

/* ===== БИБЛИОТЕКА ===== */

function rLibrary(){let h=`<div class="sh"><h2>Библиотека</h2></div><div class="tabs">`;
[['books','Книги'],['spells','Заклинания'],['potions','Зелья'],['creatures','Существа']].forEach(([t,n])=>{h+=`<div class="tb${state.libTab===t?' on':''}" onclick="state.libTab='${t}';state.libSearch='';render()">${n}</div>`});
h+=`</div>`;
if(state.libTab==='books'){h+=`<div class="sb"><i class="fas fa-search"></i><input placeholder="Поиск книг..." value="${esc(state.libSearch)}" oninput="state.libSearch=this.value;render()"></div>`;h+=rBooks(state.libSearch.toLowerCase())}
else if(state.libTab==='spells')h+=rSpellCover();
else if(state.libTab==='potions')h+=rPotionCover();
else h+=rBeastCover();
return h}

function rBooks(q){const fl=BOOKS.filter(b=>!q||b.t.toLowerCase().includes(q)||b.a.toLowerCase().includes(q)||b.cat.toLowerCase().includes(q));let h=`<div class="bgrid">`;fl.forEach(b=>{h+=`<div class="bc" onclick="openBook(${b.id})"><div class="bcv" style="background:linear-gradient(135deg,${b.c[0]},${b.c[1]})"><i class="fas ${b.ic}" style="color:rgba(255,255,255,.7)"></i></div><div class="bt">${esc(b.t)}</div><div class="ba">${esc(b.a)}</div></div>`});h+=`</div>`;if(!fl.length)h+=`<div class="es"><i class="fas fa-book"></i><p>Не найдено</p></div>`;return h}

/* ===== ОБЛОЖКИ КНИГ-СБОРНИКОВ ===== */
function rSpellCover(){return `<div class="grimoire-wrap"><div class="grimoire-cover gcover-spells" onclick="openGrimoire('spells')"><div class="spine"></div><div class="inner-border"></div><div class="corner tl">❧</div><div class="corner tr">❧</div><div class="corner bl">❧</div><div class="corner br">❧</div><div class="cover-icon">✦</div><div class="cover-title">Сборник заклинаний</div><div class="cover-line"></div><div class="cover-sub">от Алохоморы до Авады Кедавры</div><div class="cover-orn-row">◆ ◇ ◆ ◇ ◆</div><div class="cover-meta">${SPELLS.length} заклинаний в собрании</div><div class="cover-seal"><i class="fas fa-wand-sparkles"></i></div></div><p style="font-size:11px;color:var(--txt2);font-style:italic;text-align:center;margin-top:6px">Нажмите, чтобы открыть</p></div>`}

function rPotionCover(){return `<div class="grimoire-wrap"><div class="grimoire-cover gcover-potions" onclick="openGrimoire('potions')"><div class="spine"></div><div class="inner-border"></div><div class="corner tl">❧</div><div class="corner tr">❧</div><div class="corner bl">❧</div><div class="corner br">❧</div><div class="cover-icon">⚗</div><div class="cover-title">Книга зелий</div><div class="cover-line"></div><div class="cover-sub">рецепты и составы волшебных зелий</div><div class="cover-orn-row">◆ ◇ ◆ ◇ ◆</div><div class="cover-meta">${POTIONS.length} рецептов в собрании</div><div class="cover-seal"><i class="fas fa-flask"></i></div></div><p style="font-size:11px;color:var(--txt2);font-style:italic;text-align:center;margin-top:6px">Нажмите, чтобы открыть</p></div>`}

function rBeastCover(){return `<div class="grimoire-wrap"><div class="grimoire-cover gcover-beasts" onclick="openGrimoire('creatures')"><div class="spine"></div><div class="inner-border"></div><div class="corner tl">❧</div><div class="corner tr">❧</div><div class="corner bl">❧</div><div class="corner br">❧</div><div class="cover-icon">🐾</div><div class="cover-title">Бестиарий</div><div class="cover-line"></div><div class="cover-sub">фантастические звери и где они обитают</div><div class="cover-orn-row">◆ ◇ ◆ ◇ ◆</div><div class="cover-meta">${CREATURES.length} существ в каталоге</div><div class="cover-seal"><i class="fas fa-paw"></i></div></div><p style="font-size:11px;color:var(--txt2);font-style:italic;text-align:center;margin-top:6px">Нажмите, чтобы открыть</p></div>`}
/* ===== БИБЛИОТЕКА: СПИСОК КНИГ В СТИЛЕ ГРИМУАРОВ ===== */
function openBook(id) {
    console.log("Пытаюсь открыть книгу с ID:", id); // Это появится в консоли при клике
    
    const b = BOOKS.find(x => x.id === id);
    if (!b) {
        console.error("Книга не найдена в массиве BOOKS!");
        return;
    }

    const modal = document.getElementById('br');
    const container = document.getElementById('brB');
    const title = document.getElementById('brT');

    if (!modal || !container) {
        alert("Ошибка: Не найдены элементы HTML (id='br' или id='brB'). Проверь index.html!");
        return;
    }

    // Заполняем данные
    title.textContent = b.t;
    
    // Генерируем страницы
    let html = '';
    b.ch.forEach((ch, i) => {
        html += `
            <div class="page-spread">
                <div class="page">
                    <h2>${ch.t}</h2>
                    <div class="page-text">${ch.p.map(p => `<p>${p}</p>`).join('')}</div>
                </div>
            </div>`;
    });

    container.innerHTML = html;

    // ПОКАЗЫВАЕМ ОКНО (Важно!)
    modal.style.setProperty('display', 'flex', 'important'); 
    modal.classList.add('open');
    
    // Скрываем навигацию если она мешает
    const nav = document.getElementById('bnav');
    if(nav) nav.style.display = 'block';
}

// Функция закрытия
function closeBook() {
    document.getElementById('br').style.display = 'none';
}
function rBooks(q){
    const fl = BOOKS.filter(b => !q || b.t.toLowerCase().includes(q) || b.a.toLowerCase().includes(q) || b.cat.toLowerCase().includes(q));
    
    // Создаем сетку для книг
    let h = `<div class="bgrid-grimoire">`;
    
    fl.forEach(b => {
        h += `
            <div class="book-item-wrap" onclick="openBook(${b.id})">
                <div class="bc-grimoire" style="background:linear-gradient(135deg, ${b.c[0]}, ${b.c[1]})">
                    <div class="spine"></div>
                    <div class="inner-border"></div>
                    
                    <div class="corner tl">❧</div>
                    <div class="corner tr">❧</div>
                    <div class="corner bl">❧</div>
                    <div class="corner br">❧</div>

                    <div class="cover-icon"><i class="fas ${b.ic}" style="opacity:0.6"></i></div>
                    <div class="cover-title">${esc(b.t)}</div>
                    <div class="cover-sub">${esc(b.a)}</div>
                    <div class="cover-meta">${esc(b.cat)}</div>
                    
                    <div class="cover-line"></div>
                    <div class="cover-orn-row">◆ ◇ ◆</div>
                </div>
            </div>
        `;
    });
    
    h += `</div>`;
    
    if(!fl.length) h += `<div class="es"><i class="fas fa-book-dead"></i><p>В секции пусто.</p></div>`;
    return h;
}

/* ===== ОТКРЫТИЕ КНИГ-СБОРНИКОВ ===== */
function openGrimoire(type){state.ibSearch='';state.ibFilter='';const titles={spells:'Сборник заклинаний',potions:'Книга зелий',creatures:'Бестиарий'};document.getElementById('brT').textContent=titles[type];document.getElementById('brB').innerHTML=buildGrimoireContent(type);document.getElementById('br').classList.add('open');document.getElementById('br').style.display='flex';document.getElementById('bnav').style.display='none';state._openGrim=type}

function buildGrimoireContent(type) {
    const q = (state.ibSearch || "").toLowerCase();
    const f = state.ibFilter;
    if (!state.ibLetter) state.ibLetter = 'ВСЕ'; 
    if (!state.ibCatType) state.ibCatType = 'ВСЕ'; // Звери / Существа / Призраки
    let h = '';

    // --- СЕКЦИЯ ЗАКЛИНАНИЙ ---
    if (type === 'spells') {
        const cats = [{ k: '', n: 'Все' }, { k: 'daily', n: 'Бытовые' }, { k: 'combat', n: 'Боевые' }, { k: 'light', n: 'Защитные' }, { k: 'mental', n: 'Ментальные' }, { k: 'dark', n: 'Тёмные' }, { k: 'heal', n: 'Целительные' }];
        const fl = SPELLS.filter(s => {
            if (q && !s.n.toLowerCase().includes(q) && !s.i.toLowerCase().includes(q) && !s.d.toLowerCase().includes(q)) return false;
            if (f && s.cat !== f) return false;
            return true;
        });
        h += `<div class="ib-sb"><i class="fas fa-search"></i><input placeholder="Поиск заклинания..." value="${esc(state.ibSearch)}" oninput="state.ibSearch=this.value;document.getElementById('brB').innerHTML=buildGrimoireContent('spells')"></div>`;
        h += `<div class="ib-tabs">`; cats.forEach(c => { h += `<div class="ib-tb${f === c.k ? ' on' : ''}" onclick="state.ibFilter='${c.k}';document.getElementById('brB').innerHTML=buildGrimoireContent('spells')">${c.n}</div>` }); h += `</div>`;
        h += `<div class="ib-count">— ${fl.length} ${fl.length === 1 ? 'заклинание' : fl.length < 5 ? 'заклинания' : 'заклинаний'} —</div>`;
        fl.forEach(s => { h += `<div class="sp"><div class="sp-head"><div class="sp-circle"><i class="fas ${s.ic}"></i><div class="ring"></div></div><div class="sp-info"><div class="sp-name">${esc(s.n)}</div><div class="sp-incant">${esc(s.i)}</div></div></div><div class="sp-orn">❧</div><div class="sp-badges"><span class="sp-badge ${s.cat}">${esc(s.t)}</span></div><div class="sp-desc">${esc(s.d)}</div></div>` });
        if (!fl.length) h += `<div class="cp-empty">Заклинания не найдены</div>`;
    } 
    // --- МЕГА-СЕКЦИЯ ЗЕЛИЙ (ПУШКА) ---
else if (type === 'potions') {
    const cats = [{ k: '', n: 'Все' }, { k: 'Целительные', n: 'Целительные' }, { k: 'Бытовые', n: 'Бытовые' }, { k: 'Ментальные', n: 'Ментальные' }, { k: 'Тёмные', n: 'Тёмные' }, { k: 'Опасные', n: 'Опасные' }, { k: 'Легендарные', n: 'Легендарные' }];
    
    const fl = POTIONS.filter(p => {
        if (q && !p.n.toLowerCase().includes(q) && !p.d.toLowerCase().includes(q)) return false;
        if (f && p.cat !== f) return false;
        return true;
    });

    // Поиск и Табы
    h += `<div class="ib-sb"><i class="fas fa-search"></i><input placeholder="Поиск в рецептах..." value="${esc(state.ibSearch)}" oninput="state.ibSearch=this.value;document.getElementById('brB').innerHTML=buildGrimoireContent('potions')"></div>`;
    h += `<div class="ib-tabs">`; 
    cats.forEach(c => { h += `<div class="ib-tb${f === c.k ? ' on' : ''}" onclick="state.ibFilter='${c.k}';document.getElementById('brB').innerHTML=buildGrimoireContent('potions')">${c.n}</div>` }); 
    h += `</div>`;
    h += `<div class="ib-count">— Манускриптов найдено: ${fl.length} —</div>`;

    fl.forEach(p => { 
        const liqH = 30 + p.diff * 12; 
        const isOpen = state.openPotion === p.n;
        
        h += `
        <div class="pp ${isOpen ? 'open' : ''} rare-${p.rare || 'Common'}" 
             data-form="${p.form || 'classic'}" 
             style="--p-col: ${p.color}; --p-glow: ${p.color}44"
             onclick="state.openPotion = state.openPotion === '${p.n.replace(/'/g, "\\'")}' ? null : '${p.n.replace(/'/g, "\\'")}'; document.getElementById('brB').innerHTML=buildGrimoireContent('potions')">
            
            <div class="pp-glow-layer"></div>
            
            <div class="pp-rare-badge">${p.rare || 'Standard'}</div>
            
            <div class="pp-head">
                <div class="pp-bottle">
                    <div class="bk-cork"></div>
                    <div class="bk-neck"></div>
                    <div class="bk-body">
                        <div class="bk-liq" style="height:${liqH}%; background: linear-gradient(to top, ${p.color}, ${p.color}cc, transparent)">
                            <div class="bk-bubbles"></div>
                        </div>
                        <div class="bk-shine"></div>
                    </div>
                </div>
                
                <div class="pp-info">
                    <div class="pp-name">${esc(p.n)}</div>
                    <div class="pp-cat">${esc(p.cat)}</div>
                    <div class="pp-diff-row">
                        ${[1, 2, 3, 4, 5].map(i => `<div class="pp-dot ${i <= p.diff ? 'active' : ''}"></div>`).join('')}
                    </div>
                </div>
                <div class="pp-arrow ${isOpen ? 'rotated' : ''}"><i class="fas fa-magic"></i></div>
            </div>

            <div class="pp-preview-eff">
                <span>Эффект:</span> ${esc(p.eff)}
            </div>

            ${isOpen ? `
            <div class="pp-expanded-content">
                <div class="magic-divider"></div>
                <div class="pp-grid">
                    <div class="pp-sect">
                        <div class="sect-label"><i class="fas fa-flask-vial"></i> Состав</div>
                        <div class="ing-tags">
                            ${p.ing.map(i => `<span class="ing-tag">✦ ${esc(i)}</span>`).join('')}
                        </div>
                    </div>
                    <div class="pp-sect">
                        <div class="sect-label"><i class="fas fa-feather-alt"></i> Рецептура</div>
                        <div class="recipe-box">${esc(p.d)}</div>
                    </div>
                </div>
                <div class="pp-footer-note">
                    Сложность варки: ${p.diff}/5 • Требуется концентрация уровня ${p.diff > 3 ? 'Магистра' : 'Студента'}
                </div>
            </div>
            ` : ''}
        </div>`; 
    });
    if (!fl.length) h += `<div class="cp-empty">Библиотека пуста. Такого рецепта нет в запретной секции.</div>`;
}
    // --- УЛУЧШЕННЫЙ БЕСТИАРИЙ С АЛФАВИТОМ ---
else {
    if (state._selectedCreature) {
        const c = CREATURES.find(x => x.id === state._selectedCreature);
        if (!c) {
            // Если выбранный объект не найден, сбрасываем и перерисовываем
            state._selectedCreature = null;
            return buildGrimoireContent('creatures');
        }

        // Отображение выбранного существа
        h += `
        <div style="animation: fadeIn 0.5s ease; padding: 0 10px 40px;">
            <button class="book-back-btn" onclick="state._selectedCreature=null; document.getElementById('brB').innerHTML=buildGrimoireContent('creatures')" 
                    style="background:rgba(201,168,76,0.1); border:1px solid var(--gold); color:var(--gold); padding:8px 15px; border-radius:20px; font-family:'Cinzel'; cursor:pointer; font-size:11px; margin-bottom:25px; transition:0.3s; display:flex; align-items:center; gap:8px;">
                <i class="fas fa-chevron-left"></i> К ОГЛАВЛЕНИЮ БЕСТИАРИЯ
            </button>
            <div style="background: linear-gradient(145deg, #1a1a1a, #0a0a0a); padding:30px; border-radius:20px; border:1px solid rgba(201,168,76,0.3); box-shadow: 0 20px 50px rgba(0,0,0,0.8);">
                <div style="text-align:center; margin-bottom: 25px;">
                    <img src="${c.ic}" style="max-width: 180px; filter: drop-shadow(0 0 15px rgba(201,168,76,0.4));">
                </div>
                <div style="text-align:center; margin-bottom:25px;">
                    <div style="font-family:'UnifrakturMaguntia', cursive; color:#f5e2b8; font-size:42px; text-shadow: 2px 2px 4px #000;">${esc(c.n)}</div>
                    <div style="font-family:'Cinzel'; color:var(--gold); font-size:12px; letter-spacing:4px;">✦ ${esc(c.cat || 'СУЩЕСТВО').toUpperCase()} ✦</div>
                </div>
                <div style="display:grid; grid-template-columns:1fr 1fr; gap:1px; background:rgba(201,168,76,0.2); border-radius:10px; overflow:hidden; margin-bottom:25px;">
                    <div style="background:#111; padding:15px; text-align:center;">
                        <div style="font-size:10px; color:var(--gold); opacity:0.6;">АРЕАЛ</div>
                        <div style="color:#ddd;">${esc(c.hab)}</div>
                    </div>
                    <div style="background:#111; padding:15px; text-align:center;">
                        <div style="font-size:10px; color:var(--gold); opacity:0.6;">ОПАСНОСТЬ</div>
                        <div style="color:var(--gold); font-weight:bold;">${esc(c.danger)}</div>
                    </div>
                </div>
                <div style="background:rgba(0,0,0,0.3); padding:20px; border-radius:10px; border-left:3px solid var(--gold); margin-bottom:30px;">
                    <p style="font-family:'Crimson Text'; font-size:16px; line-height:1.6; color:#d1c7b7; font-style:italic;">"${esc(c.d)}"</p>
                </div>
                ${c.fullPageImg ? `
                <div style="text-align:center;">
                    <button onclick="openFullscreen('${c.fullPageImg}')" style="background:none; border:1px solid var(--gold); color:var(--gold); padding:10px 20px; cursor:pointer; font-family:'Cinzel';">ОТКРЫТЬ АРХИВНЫЙ МАНУСКРИПТ</button>
                </div>` : ''}
            </div>
        </div>`;
        } else {
           // --- ШАПКА КАТЕГОРИЙ ---
            const mainTypes = [
                {k:'ВСЕ', n:'Все'},
                {k:'Зверь', n:'Звери'},
                {k:'Существо', n:'Существа'},
                {k:'Призрак', n:'Призраки'}
            ];
            
            h += `<div class="ib-tabs" style="margin-bottom:10px;">`;
            mainTypes.forEach(t => {
                h += `<div class="ib-tb${state.ibCatType === t.k ? ' on' : ''}" 
                           onclick="state.ibCatType='${t.k}'; document.getElementById('brB').innerHTML=buildGrimoireContent('creatures')">${t.n}</div>`;
            });
            h += `</div>`;

            // --- АЛФАВИТНЫЙ УКАЗАТЕЛЬ ---
            const alphabet = 'АБВГДЕЖЗИКЛМНОПРСТУФХЦЧШЩЭЮЯ'.split('');
            h += `<div style="display:flex; flex-wrap:wrap; gap:4px; margin-bottom:20px; background:rgba(0,0,0,0.2); padding:10px; border-radius:10px; border:1px solid rgba(201,168,76,0.1); justify-content:center;">`;
            h += `<div onclick="state.ibLetter='ВСЕ'; document.getElementById('brB').innerHTML=buildGrimoireContent('creatures')" 
                       style="color:var(--gold); cursor:pointer; font-size:11px; padding:4px; opacity:${state.ibLetter==='ВСЕ'?1:0.4}">#</div>`;
            alphabet.forEach(l => {
                const hasC = CREATURES.some(c => c.n[0].toUpperCase() === l);
                h += `<div onclick="${hasC ? `state.ibLetter='${l}'; document.getElementById('brB').innerHTML=buildGrimoireContent('creatures')` : ''}" 
                           style="color:var(--gold); cursor:pointer; font-size:11px; padding:4px; width:18px; text-align:center; opacity:${state.ibLetter===l?1:hasC?0.5:0.1}">${l}</div>`;
            });
            h += `</div>`;

            // ФИЛЬТРАЦИЯ
            const fl = CREATURES.filter(c => {
                const matchSearch = !q || c.n.toLowerCase().includes(q) || c.cat.toLowerCase().includes(q);
                const matchLetter = state.ibLetter === 'ВСЕ' || c.n[0].toUpperCase() === state.ibLetter;
                const matchType = state.ibCatType === 'ВСЕ' || c.type === state.ibCatType;
                return matchSearch && matchLetter && matchType;
            }).sort((a, b) => a.n.localeCompare(b.n));

            h += `<div style="display:grid; grid-template-columns: 1fr 1fr; gap:15px;">`;
            fl.forEach(c => {
                h += `
                <div onclick="state._selectedCreature='${c.id}'; document.getElementById('brB').innerHTML=buildGrimoireContent('creatures')" 
                     class="creature-card"
                     style="background:rgba(201,168,76,0.05); border:1px solid rgba(201,168,76,0.2); border-radius:12px; padding:12px; text-align:center; cursor:pointer;">
                    <div style="font-size:8px; color:var(--gold); opacity:0.4; letter-spacing:1px; margin-bottom:5px;">${c.cat.toUpperCase()}</div>
                    <img src="${c.ic}" style="height:50px; margin-bottom:8px; filter:sepia(0.5);">
                    <div style="font-family:'Cinzel'; color:var(--gold); font-size:11px; font-weight:bold;">${c.n}</div>
                </div>`;
            });
            h += `</div>`;
        }
    }
    return h;
}

/* ===== ИСПРАВЛЕННЫЙ БЛОК ЧТЕНИЯ КНИГ ===== */

function openBook(id) {
    const b = BOOKS.find(x => x.id === id);
    if (!b) return;

    const modal = document.getElementById('br');
    const container = document.getElementById('brB');
    const title = document.getElementById('brT');

    // Берем основной цвет книги из массива c (например, красный для Гриффиндора или зеленый для Слизерина)
    const bookColor = (b.c && b.c[0]) ? b.c[0] : '#8b0000';
    
    // Передаем цвет в CSS переменную, чтобы всё внутри окрасилось в цвет обложки
    container.style.setProperty('--active-book-color', bookColor);

    title.textContent = b.t;
    title.style.color = bookColor; // Заголовок вверху тоже под цвет книги

    let html = `
        <div class="book-reader-wrap">
            <div class="parchment-page">
                <div class="book-internal-header">
                    <i class="fas ${b.ic}" style="color: var(--active-book-color)"></i>
                    <div class="book-internal-author">${esc(b.a)}</div>
                    <div class="magic-divider">✧ ✦ ✧</div>
                </div>
    `;

    b.ch.forEach((ch, i) => {
    html += `
        <div class="book-chapter-section">
            <h2 class="chapter-title">${esc(ch.t)}</h2>
            <div class="book-text-body">
    `;
    
    // Вывод текста
    const paragraphs = ch.p || [...(ch.L || []), ...(ch.R || [])];
    paragraphs.forEach(p => {
        html += `<p>${esc(p)}</p>`;
    });
	
// 1. Блок стадий (Nigredo, Albedo и т.д.)
    if (ch.stages) {
        const glyphs = ["♄", "🜛", "🜚", "☿"]; 
        html += `
        <div class="alchemy-scroll">
            <div class="scroll-header">Magnum Opus</div>
            ${ch.stages.map((s, index) => `
                <div class="alchemy-step">
                    <div class="step-seal">
                        <span class="seal-glyph">${glyphs[index] || '🜁'}</span>
                        <span class="seal-label">Phase ${index + 1}</span>
                    </div>
                    <div class="step-content">
                        <div class="step-metal-tag">${esc(s.metal)}</div>
                        <h4 class="step-name ${s.class.replace('-bg', '-ink')}">${esc(s.name)}</h4>
                        <div class="step-symbols">◈ ${esc(s.symbols)}</div>
                        <p class="step-psy">${esc(s.psy)}</p>
                    </div>
                </div>
            `).join('')}
            <div class="alchemy-signature">Paracelsus Pinxit</div>
        </div>`;
    }

    // 2. Блок Досье (Характеристика Парацельса)
    if (ch.dossier) {
        html += `
            <div class="custom-dossier">
                <div class="dossier-line"></div>
                <div class="dossier-title">${esc(ch.dossier.title)}</div>
                <div class="dossier-grid">
                    ${ch.dossier.data.map(row => `
                        <div class="dossier-row">
                            <span class="d-label">${esc(row[0])}</span>
                            <span class="d-dots"></span>
                            <span class="d-value">${esc(row[1])}</span>
                        </div>
                    `).join('')}
                </div>
                <div class="dossier-line"></div>
            </div>`;
    }

    // 3. НОВЫЙ БЛОК: Таблица соответствий (Парселтанг)
    if (ch.analogies) {
        html += `
            <div class="alchemy-analogies">
                ${ch.analogies.map(item => `
                    <div class="analogy-item">
                        <div class="analogy-func">${esc(item.func)}</div>
                        <div class="analogy-row">
                            <div class="analogy-column">
                                <span class="analogy-label">Magia</span>
                                <div class="analogy-text">${esc(item.magic)}</div>
                            </div>
                            <div class="analogy-column">
                                <span class="analogy-label">Alchimia</span>
                                <div class="analogy-text italic">${esc(item.alchemy)}</div>
                            </div>
                        </div>
                    </div>
                `).join('')}
            </div>`;
    }

    // Закрываем контейнер главы (проверь, чтобы эти теги совпадали с твоим началом)
    html += `
            </div>
            <div class="chapter-end-ornament">❧</div>
        </div>`;
}); // Конец цикла .forEach(ch => ...)

// Финальный подвал книги
html += `
        <div class="book-internal-footer">
            Архив Хогвартса • ${esc(b.cat)}
        </div>
    </div>
</div>`;

    container.innerHTML = html;
	

    // Эффекты открытия
    modal.style.display = 'flex';
    modal.scrollTop = 0; 
    
    // Скрываем навигацию приложения
    const bnav = document.getElementById('bnav');
    if (bnav) bnav.style.display = 'none';

}

function addPts(dir){const house=document.getElementById('ptsH').value,val=parseInt(document.getElementById('ptsV').value)||0;if(val<=0)return toast('Укажите значение');pts[house]+=val*dir;saveState();toast(`${dir>0?'Добавлено':'Убавлено'} ${val} → ${FC[house].n}`);render()}
function initStars(){const c=document.getElementById('stars'),ctx=c.getContext('2d');let w,h,stars=[];function resize(){w=c.width=innerWidth;h=c.height=innerHeight;stars=[];for(let i=0;i<80;i++)stars.push({x:Math.random()*w,y:Math.random()*h,r:Math.random()*1.2+.3,a:Math.random(),s:Math.random()*.005+.002})}resize();window.addEventListener('resize',resize);function draw(){ctx.clearRect(0,0,w,h);stars.forEach(s=>{s.a+=s.s;if(s.a>1)s.s=-Math.abs(s.s);if(s.a<0)s.s=Math.abs(s.s);ctx.beginPath();ctx.arc(s.x,s.y,s.r,0,Math.PI*2);ctx.fillStyle=`rgba(201,168,76,${Math.abs(s.a)*.6})`;ctx.fill()});requestAnimationFrame(draw)}draw()}
initStars();initUser();render();

</script>
</body>
</html>
