# Разбор бровки · Touchline Breakdown

Тактическая доска и подготовка к матчу для любительского и детского футбола. React + TypeScript + Tailwind CSS v4 + SVG/Canvas. Без бэкенда, всё хранится в `localStorage`.

## Запуск

```bash
npm install
npm run dev      # разработка
npm run build    # сборка → dist/index.html (один файл)
```

## Архитектура

```
src/
  App.tsx                      навигация, режим матча, общее состояние заявки
  types.ts                     все доменные типы
  lib/
    geometry.ts                проекции поля (нормализованные координаты ↔ SVG), режимы поля, ориентация
    formations.ts              схемы 5/7/8/9/11 игроков и генерация токенов
    sceneKit.ts                SB — билдер сцен для seed-данных (координаты в метрах)
    storage.ts                 useLocalStorage, useHistory (undo/redo с «живыми» правками)
    exporters.ts               PNG доски (SVG → Canvas, 2× DPI), ссылка (deflate+base64url), буфер обмена
    lineup.ts / matchCard.ts   подбор состава, текст для чата, PNG-карточка матча
  components/
    pitch/PitchScene.tsx       единый чистый SVG-рендер: разметка, оверлеи, стрелки, зоны, игроки
    board/TacticalBoard.tsx    доска: pointer-события, инструменты, кадры, плейбук, экспорт
    situations/Situations.tsx  библиотека разборов + drawer
    matchday/*                 конструктор матча, визуальный состав, режим матча
    trainer/DecisionTrainer.tsx тренажёр решений и статистика
  data/                        seed: плейбук, 11 разборов, 10 сценариев тренажёра, заявка
```

Ключевые решения: координаты объектов нормализованы относительно видимой области поля, поэтому смена режима (всё поле / треть / половина / мини-футбол) и ориентации не ломает схему; один и тот же компонент `PitchScene` рисует доску, мини-диаграммы, тренажёр, состав и PNG-экспорт; на SVG стоит `touch-action: none`, а перетаскивание идёт через `setPointerCapture`.
