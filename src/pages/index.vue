<script setup>
import { onMounted, onUnmounted, ref } from 'vue';
import gsap from 'gsap';
import { ScrollTrigger } from 'gsap/ScrollTrigger';

gsap.registerPlugin(ScrollTrigger);

const main = ref();
let ctx;

onMounted(() => {
  ctx = gsap.context((self) => {
    // ВАЖНО: Анимируем сам `.box`, но триггером служит `.sticky-wrapper`
    const box = self.selector('.box');

    // 1. Создаем Timeline, привязанную к скроллу
    const tl = gsap.timeline({
      scrollTrigger: {
        trigger: ".sticky-wrapper", // Весь длинный контейнер
        start: "top top",         // Начинаем, когда верх контейнера доходит до верха экрана
        end: "bottom bottom",     // Заканчиваем, когда низ контейнера доходит до низа экрана
        scrub: 1,                 // Плавная привязка к скроллу (1 секунда задержки "догона")
        pin: true,                // ВАЖНО: Фиксирует .sticky-wrapper на экране
        markers: false,            // Включите true для отладки
      }
    });

    // Расчет дистанции для перемещения
    const getTravelDistance = () => window.innerWidth - box[0].offsetWidth - 40; // 40 - это margin от краев

    // 2. Добавляем этапы анимации в Timeline
    // В GSAP вращение лучше делать через `rotation`, а движение через `x`

    tl
      // --- Этап 1: Едем ВПРАВО и вращаемся ---
      .to(box, {
        x: getTravelDistance,     // Едем в правый конец
        rotation: 360,            // Делаем полный оборот
        ease: "none",             // Без сглаживания внутри этапа, т.к. есть scrub
        duration: 1               // Условная длительность этапа (для баланса)
      })

      // --- Этап 2: Едем ОБРАТНО ВЛЕВО и вращаемся дальше ---
      .to(box, {
        x: 0,                     // Возвращаемся в x:0
        rotation: 0,              // Вращаемся в обратную сторону
        ease: "none",
        duration: 1               // Условная длительность этапа
      });

    // 3. Обновление значений при ресайзе окна
    ScrollTrigger.addEventListener("refreshInit", () => {
      // Обновляем Timeline при ресайзе (чтобы x корректно пересчитался)
      tl.progress(0); // Сбрасываем в начало для корректного пересчета
    });

  }, main.value); // Scope
});

onUnmounted(() => {
  if (ctx) ctx.revert(); // Очистка
});
</script>

<template>
  <div ref="main">
    <!-- Пустая секция ДО, чтобы показать эффект появления sticky -->
    <section class="section green">
      <h1>Scroll Down</h1>
    </section>

    <!-- ЭТОТ КОНТЕЙНЕР ЗАДАЕТ ДЛИНУ СКРОЛЛА АНИМАЦИИ -->
    <!-- Его высота (500vh) определяет, как долго будет длиться анимация "туда-сюда" -->
    <section class="sticky-wrapper">
      <div class="viewport-panel">
        <div class="box gradient-green">Box</div>
      </div>
    </section>

    <!-- Пустая секция ПОСЛЕ -->
    <section class="section blue">
      <h1>The End</h1>
    </section>
  </div>
</template>

<style scoped>
/* Общие стили секций */
.section {
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  font-family: sans-serif;
}
.green { background-color: #2ecc71; }
.blue { background-color: #3498db; }

/* Wrapper, который занимает 5 экранных высот */
.sticky-wrapper {
  height: 500vh; /* Увеличьте это число, чтобы анимация была медленнее */
  background: #ecf0f1;
  position: relative;
  overflow: hidden; /* Скрывает бокс, если он вылетит за край */
}

/* Панель размером с экран, внутри которой находится бокс */
.viewport-panel {
  height: 100vh;
  width: 100%;
  display: flex;
  align-items: center; /* Центрируем бокс по вертикали */
  padding: 0 20px; /* Отступы от краев экрана */
  box-sizing: border-box;
}

.box {
  width: 150px;
  height: 150px;
  border-radius: 20px;
  background-color: #0f0;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: bold;
  font-family: sans-serif;
  box-shadow: 0 10px 30px rgba(0,0,0,0.2);
  /* position: absolute; - Не нужно при flex-align, GSAP использует transform */
}

.gradient-green {
  background: linear-gradient(135deg, #2ecc71, #27ae60);
  color: white;
  text-transform: uppercase;
}
</style>