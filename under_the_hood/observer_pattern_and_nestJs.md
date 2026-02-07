# Observer Pattern & NestJS 🧠⚡

---

## 1️⃣ Observer Pattern কী?

Observer Pattern হচ্ছে একটি behavioral design pattern যেখানে একটা source (Observable / Subject) থাকে এবং তার সাথে একাধিক listener (Observer / Subscriber) attach থাকে। Source যখনই কোনো নতুন data emit করে, তখন attached সব observer সেই data automatically receive করে।

এখানে গুরুত্বপূর্ণ বিষয় হলো — producer (source) এবং consumer (observer) একে অপরের সম্পর্কে tightly coupled না। Producer জানেই না কে কে তার data consume করছে। এই loose coupling-ই Observer Pattern কে scalable এবং extensible করে তোলে।

Real-life example ধরলে: YouTube channel একটা Observable, আর subscribers রা Observer। নতুন video publish হলেই সবাই notify পায় — channel কিন্তু individual subscriber কে manually call করে না।


```
+-----------+        notify()        +------------+
|  Subject  | --------------------> |  Observer  |
| (State)   |                       | (Listener) |
+-----------+ <-------------------- +------------+
       |          subscribe()
       v
  State Change
```

---

## 2️⃣ Observer Pattern কোথা থেকে এসেছে? (Reactive Programming Perspective)

Observer Pattern-কে বলা হয় রিঅ্যাক্টিভ প্রোগ্রামিংয়ের আদি পিতা বা Foundation। রিঅ্যাক্টিভ প্রোগ্রামিং কোনো হঠাত আসা ধারণা নয়, বরং এটি ওবজারভার প্যাটার্নের একটি ম্যাথমেটিক্যাল এবং স্ট্রাকচার্ড বিবর্তন।

### Reactive Programming মানে কী?

Reactive Programming হলো এমন একটি programming paradigm যা মূলত Asynchronous Data Streams এবং Propagation of Change নিয়ে কাজ করে। সহজ কথায়, যখনই কোনো ডেটা পরিবর্তন হয় বা নতুন কোনো ইভেন্ট ঘটে, সিস্টেম স্বয়ংক্রিয়ভাবে সেই পরিবর্তনের বিপরীতে সাড়া দেয়।

আগে programming মানেই ছিল:
```
Call a function → wait → get result
```
Reactive world এ চিন্তাটা উল্টো:
```
Data আসবে, তখন react করবো
```

বাস্তব জগতের GUI systems, hardware interrupts, কিংবা network packets — এগুলো সবই Event-based। প্রথাগত Sequential code দিয়ে এই ইভেন্টগুলোকে হ্যান্ডেল করা ছিল অনেক জটিল এবং রিসোর্স-হাংরি।

Observer Pattern প্রথম এই real-world asynchronous nature-কে সফটওয়্যার ডিজাইনে একটি ফরমাল স্ট্রাকচার দেয়। সময়ের সাথে সাথে এই ধারণাই আরও শক্তিশালী হয়ে RxJS, ReactiveX, বা Project Reactor-এর মতো powerful abstraction তৈরি করেছে।

Rx-এর উদ্ভাবক Erik Meijer-এর মতে, রিঅ্যাক্টিভ প্রোগ্রামিং কোনো একক বিষয় নয়। এটি মূলত তিনটি শক্তিশালী কনসেপ্টের একটি চমৎকার মিশ্রণ:
```
Reactive Programming = Observer Pattern + Iterator Pattern + Functional Programming
```
* Observer Pattern: ইভেন্ট বা ডেটা আসা মাত্রই লিসেনারদের "Push" করে জানানো।
* Iterator Pattern: ডেটার একটি বড় সংগ্রহ বা স্ট্রিমের ওপর একের পর এক লুপ চালিয়ে প্রসেস করা।
* Functional Programming: ডেটা স্ট্রিমগুলোকে map, filter, বা reduce-এর মতো অপারেটর দিয়ে ক্লিন এবং ডিক্লারেটিভভাবে হ্যান্ডেল করা।

---

## 3️⃣ RxJS সংক্ষেপে

RxJS (Reactive Extensions for JavaScript) হচ্ছে JavaScript এর জন্য Reactive Programming library। এর core concept হলো Observable stream — যেটা সময়ের সাথে সাথে multiple value emit করতে পারে।

RxJS শুধু Observable দেয় না, বরং দেয়:
* powerful operators (map, mergeMap, switchMap, retry, timeout)
* Schedulers (event loop এর সাথে integrate করার জন্য)
* built-in cancellation & cleanup mechanism

Promise যেখানে একবার resolve বা reject হয়, Observable সেখানে multiple value emit করতে পারে, cancel (unsubscribe) করতে পারে, এবং operators দিয়ে flow control করতে পারে, এবং complex async workflow express করতে পারে declaratively।

### Simple RxJS example:

```ts
import { Observable } from 'rxjs';

const stream$ = new Observable(observer => {
  observer.next('Hello');
  observer.next('World');
  observer.complete();
});

stream$.subscribe(value => {
  console.log(value);
});
```

### Output:

```
Hello
World
```

RxJS heavily uses **Observer Pattern** internally.

---

## 4️⃣ কেন NestJS Observable stream ব্যবহার করে?

NestJS built on top of:

* Node.js
* Express / Fastify
* RxJS

NestJS ডিজাইন করা হয়েছে একটি Transport-agnostic ফ্রেমওয়ার্ক হিসেবে, Design once, run on any transport। অর্থাৎ, কোড HTTP, WebSocket, Kafka বা RabbitMQ—সবক্ষেত্রেই যেন একইভাবে কাজ করে। এই লক্ষ্য অর্জনে Observable Stream-এর ভূমিকা অপরিসীম:

* Unified Programming Model: একটি সাধারণ HTTP Request হোক বা কোনো Kafka Event—NestJS সবকিছুর ইন্টারনাল হ্যান্ডলিং করে Observable Stream হিসেবে। এর ফলে ভিন্ন ভিন্ন প্রোটোকলের জন্য ডেভেলপারকে বারবার নতুন আর্কিটেকচার শিখতে হয় না।
* Powerful Stream Manipulation: RxJS-এর অপারেটরগুলো (যেমন: retry, timeout, fallback) ব্যবহার করে অত্যন্ত জটিল লজিকও খুব স্বচ্ছভাবে (Cleanly) ইমপ্লিমেন্ট করা যায়। যা প্রথাগত try-catch বা callback দিয়ে হ্যান্ডেল করা অনেক সময় ম্যানেজমেন্টের বাইরে চলে যেত।


HTTP request, WebSocket message, Kafka/RabbitMQ event — NestJS সবকিছুকেই internally Observable stream হিসেবে treat করে। এর ফলে:

* একই programming model সব transport এ কাজ করে
* request lifecycle easily intercept / extend করা যায়
* retry, timeout, fallback খুব clean ভাবে add করা যায়

NestJS controller method যদি Promise return করে, NestJS internally সেটাকে Observable এ convert করে নেয়। অর্থাৎ Observable হচ্ছে NestJS এর native execution model।

---

## 5️⃣ NestJS Request–Response Lifecycle (Text Diagram)

```
Client
  |
  | HTTP Request
  v
+-------------------+
|  Node.js (OS)     |
|  Event Loop       |
+-------------------+
          |
          v
+-------------------+
|  Express/Fastify  |
+-------------------+
          |
          v
+-------------------+
|  NestJS Pipeline  |
|-------------------|
| Middleware        |
| Guards            |
| Interceptors      |
| Pipes             |
| Controller        |
| Service           |
+-------------------+
          |
          v
     Observable
          |
          v
      Response
```

---

## 6️⃣ NestJS-এ Observer Pattern কীভাবে কাজ করে

OS level এ যখন client থেকে request আসে, সেটা প্রথমে kernel → network stack → Node.js event loop এ পৌঁছায়। Node.js event loop এই request কে JavaScript execution context এ নিয়ে আসে।

NestJS এই request কে immediately একটা Observable producer হিসেবে wrap করে। Middleware, Guard, Interceptor — সবাই আসলে সেই Observable stream এর subscriber বা operator হিসেবে কাজ করে।

যখন Controller থেকে data emit হয়, সেটা stream দিয়ে flow করতে করতে response হিসেবে client এ ফিরে যায়। Client disconnect করলে Observable unsubscribe হয় এবং Node.js resources cleanup করে।

### Diagram:

```
[OS Socket Event]
        |
        v
[Node Event Loop]
        |
        v
[Observable Stream]
        |
  +-----+------+
  |            |
[Interceptor] [Guard]
  |            |
        v
   [Controller]
        |
   observer.next()
        |
   HTTP Response
```

### NestJS Controller Example:

```ts
@Get('users')
getUsers(): Observable<User[]> {
  return this.userService.findAll();
}
```

---

## 7️⃣ Promise vs Observable

Promise একবার resolve বা reject হয়। Cancel করা যায় না। Multiple value handle করতে পারে না।

Observable:
* multiple value emit করতে পারে
* lazy (subscribe না করলে execute হয় না)
* cancel / unsubscribe support করে
* streaming + async I/O এর জন্য perfect

এই কারণেই NestJS Promise support করলেও internally Observable prefer করে।

### Promise example:

```ts
fetchData().then(data => console.log(data));
```

### Observable example:

```ts
data$.subscribe(data => console.log(data));
```

---

## 8️⃣ Event Loop এবং Observable

Observable নিজে event loop replace করে না। বরং:

async I/O → event loop handle করে

Observable → execution flow & composition handle করে

RxJS Scheduler decide করে কোন কাজটা microtask queue এ যাবে, কোনটা macrotask queue এ যাবে। ফলে Node.js concurrency model এর সাথে perfectly aligned থাকে।

### Diagram:

```
Event
  |
  v
Event Loop
  |
  v
Observable.next()
  |
  v
Subscriber Callback
```

---

## 9️⃣ কেন Observable Stream NestJS কে আলাদা করে?

NestJS Observable stream ব্যবহার করার কারণে:

* HTTP + Microservice + Event-driven সব এক model এ আসে
* Cross-cutting concern (logging, retry, timeout) clean ভাবে handle হয়
* High-concurrency system এ resource efficient হয়

Use cases:
* API Gateway
* Microservices (Kafka, RabbitMQ)
* Real-time systems
* High-load enterprise backend

NestJS মূলত Node.js কে শুধু web framework না বানিয়ে, একটা reactive backend platform বানিয়েছে — আর এর কেন্দ্রবিন্দুতে আছে Observer Pattern এবং Observable stream।
