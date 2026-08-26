# Counter-va-Memo-factory-lar-closure-bilan-
1. Modul Kodi (Closures & Factory Pattern)
JavaScript
// 1. createCounter factory — qadam, max, min sozlamalari bilan (Closure state)
const createCounter = ({ initial = 0, step = 1, min = Number.MIN_SAFE_INTEGER, max = Number.MAX_SAFE_INTEGER } = {}) => {
  let current = initial;

  return {
    increment: () => {
      if (current + step <= max) {
        current += step;
      }
      return current;
    },
    decrement: () => {
      if (current - step >= min) {
        current -= step;
      }
      return current;
    },
    get: () => current,
    reset: () => {
      current = initial;
      return current;
    }
  };
};

// 2. memoize(funk) — og'ir hisob-kitoblar natijasini cache'lash uchun closure
const memoize = (funk) => {
  const cache = new Map(); // Private cache storage

  return (...args) => {
    const key = JSON.stringify(args);
    if (cache.has(key)) {
      return cache.get(key);
    }
    const result = funk(...args);
    cache.set(key, result);
    return result;
  };
};

// 3. once(funk) — funksiyani faqat bir marta chaqirishni ta'minlaydigan closure
const once = (funk) => {
  let executed = false;
  let result;

  return (...args) => {
    if (!executed) {
      executed = true;
      result = funk(...args);
      return result;
    }
    return result; // Oldingi saqlangan natijani qaytaradi
  };
};

// 4. debounce(funk, ms) — oxirgi chaqiruvdan keyin belgilangan ms kutib ishlatuvchi closure
const debounce = (funk, ms) => {
  let timeoutId = null;

  return (...args) => {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => {
      funk(...args);
    }, ms);
  };
};
2. Demo: Fibonacci va Memoize Tejamkorligini Taqqoslash
Quyidagi demo orqali memoize funksiyasi closure yordamida natijalarni qanday saqlab qolishi va rekursiv hisoblash tezligini qanchalik keskin oshirishini ko'rishingiz mumkin.

JavaScript
// Oddiy (sekin ishlaydigan) rekursiv Fibonacci
const fibonacciRaw = (n) => {
  if (n <= 1) return n;
  return fibonacciRaw(n - 1) + fibonacciRaw(n - 2);
};

// Memoize qilingan Fibonacci
const fibonacciMemoized = memoize((n) => {
  if (n <= 1) return n;
  return fibonacciMemoized(n - 1) + fibonacciMemoized(n - 2);
});

// --- TAQQOSLASH TESTI ---
const nValue = 40;

console.log("=== CLOSURE & FACTORY DEMO ===");

// 1. Counter test
const myCounter = createCounter({ initial: 10, step: 5, max: 25 });
console.log("Counter boshlang'ich:", myCounter.get()); // 10
console.log("Increment:", myCounter.increment()); // 15
console.log("Increment:", myCounter.increment()); // 20
console.log("Increment:", myCounter.increment()); // 25
console.log("Max chegaradan oshirishga urinish:", myCounter.increment()); // 25 (oshmaydi)

// 2. Once test
const initSystem = once(() => {
  console.log(" tizim ishga tushdi (faqat 1 marta ishlaydi)");
  return "OK";
});
console.log(initSystem()); // Ishlaydi va "OK" qaytaradi
console.log(initSystem()); // Qaytadan ishlamaydi, shunchaki "OK" beradi

// 3. Fibonacci Speed Test (Memoize bilan)
console.log(`\nFibonacci(${nValue}) hisoblanmoqda...`);

console.time("Memoized vaqt");
console.log("Memoized natija:", fibonacciMemoized(nValue));
console.timeEnd("Memoized vaqt"); // Bir zumda bajariladi (~0-2ms)

// Katta sonlarda oddiy fibonacci juda qotib qolgani uchun kichikroq qiymatda yoki birinchi chaqiruvda ko'riladi:
console.time("Oddiy (1-chi marta) vaqt");
console.log("Raw natija:", fibonacciRaw(30)); // 30 soni uchun ham sekinlashadi
console.timeEnd("Oddiy (1-chi marta) vaqt");
