# Lab-test
# 🎓 ข้อสอบซ้อม (Mock Exam) — CP353002 Principles of Software Design
> จำลองรูปแบบข้อสอบปฏิบัติจริง ครอบคลุม Lab1–Lab7 ให้ boss ลองจับเวลาทำเอง (แนะนำ 2–3 ชม.)
> **กติกาสำคัญ:** ห้ามต่อฐานข้อมูลจริง เก็บข้อมูลด้วย `List` ใน memory เท่านั้น (เหมือน Lab5 ไม่ใช่ Lab7 แบบ JPA/Postgres)

---

## 🎯 โจทย์สถานการณ์: "ระบบเช่าหนังสือห้องสมุด (Library Rental System)"

ร้านเช่าหนังสือแห่งหนึ่งต้องการเว็บแอปสำหรับจัดการ **รายการหนังสือ** และคำนวณ **ค่าเช่าตามประเภทสมาชิก** โดยใช้ Spring Boot

### สิ่งที่ต้องสร้าง (ครบทุกชั้นตามหลัก Layered Architecture)

| ชั้น | คลาส | หน้าที่ |
|---|---|---|
| Model | `Book` | เก็บข้อมูลหนังสือ |
| Strategy | `RentalDiscountStrategy` (interface) + 3 คลาส implement | คำนวณส่วนลดค่าเช่าตามประเภทสมาชิก |
| Strategy | `DiscountContext` | เลือก strategy ตาม type แล้วเรียกคำนวณ |
| Service | `BookService` | เก็บ `List<Book>` ใน memory + logic CRUD |
| Controller | `BookController` | รับ HTTP request, เรียก Service, คืน view (Thymeleaf) |
| Config | `ThymeleafConfig` | ชี้ template ไปที่โฟลเดอร์ `custom-templates/` แทน `templates/` (เหมือน Lab6) |

### Entity: `Book`

| Field | Type | ตัวอย่าง |
|---|---|---|
| `id` | `Long` | 1 |
| `title` | `String` | "Clean Code" |
| `author` | `String` | "Robert C. Martin" |
| `pricePerDay` | `double` | 20.0 |
| `available` | `boolean` | true |

### Strategy Pattern: ส่วนลดค่าเช่า

| ประเภทสมาชิก | รหัส | ส่วนลด |
|---|---|---|
| สมาชิกทั่วไป | `NONE` | ไม่ลด |
| นักศึกษา | `STUDENT` | ลด 15% |
| สมาชิก VIP | `VIP` | ลด 30% |

สูตร: `ค่าเช่าสุทธิ = pricePerDay × จำนวนวัน × (1 - ส่วนลด)`

### Endpoints ที่ต้องมี (REST + Thymeleaf ผสมกัน)

| Method | Path | ทำอะไร |
|---|---|---|
| `GET` | `/books` | แสดงหนังสือทั้งหมด (หน้า Thymeleaf) |
| `GET` | `/books/add` | ฟอร์มเพิ่มหนังสือ |
| `POST` | `/books/save` | บันทึกหนังสือใหม่ → redirect `/books` |
| `GET` | `/books/edit/{id}` | ฟอร์มแก้ไข |
| `POST` | `/books/update/{id}` | อัปเดต → redirect `/books` |
| `GET` | `/books/delete/{id}` | ลบหนังสือ → redirect `/books` |
| `GET` | `/books/rent/{id}?memberType=STUDENT&days=3` | คำนวณค่าเช่าสุทธิ แล้วแสดงผล (ทดสอบ Strategy Pattern) |

> ตั้งข้อมูลตัวอย่างไว้ล่วงหน้าอย่างน้อย 3 เล่มตอนแอปเริ่มทำงาน

---

## 📝 ส่วนที่ 1 — Multiple Choice / Short Answer (ตัวอย่างแนวคำถาม)

1. `@Controller` กับ `@RestController` ต่างกันอย่างไร แล้วในโจทย์นี้ควรใช้ตัวไหนกับ `BookController`? เพราะอะไร
2. ถ้า `BookService` เก็บข้อมูลด้วย `List<Book>` ไม่ใช้ฐานข้อมูล ข้อมูลจะหายตอนไหน และมีวิธีแก้แบบใดบ้าง (ตอบเชิงแนวคิด)
3. `ViewResolver` ทำหน้าที่อะไร ถูกเรียกใช้ตอนไหนใน request lifecycle ของ Spring MVC
4. อธิบายว่าทำไม `BookController` ไม่ควรมี logic คำนวณส่วนลดเอง ควรอยู่ที่ไหน (โยงกับ GRASP: Information Expert)
5. Strategy Pattern ในโจทย์นี้ช่วยให้ระบบสอดคล้องกับหลัก **Open/Closed Principle** อย่างไร ถ้าจะเพิ่มส่วนลด "วันเกิดสมาชิก" ต้องแก้โค้ดตรงไหนบ้าง (ควรตอบว่า "เพิ่มคลาสใหม่ ไม่ต้องแก้โค้ดเดิม")
6. `@Autowired` แบบ Constructor Injection ต่างจาก Field Injection อย่างไร ทำไม Spring แนะนำแบบ Constructor

---

## 💻 ส่วนที่ 2 — ภาคปฏิบัติ (สิ่งที่ต้อง implement จริง)

1. สร้างโปรเจกต์ Spring Boot ชื่อ `exam-<รหัสนักศึกษา>-sec<X>` (Dependencies: Spring Web, Thymeleaf)
2. เขียน `Book.java`, `BookService.java`, `BookController.java` ให้ทำงานครบตามตาราง endpoint ด้านบน
3. เขียน Strategy package ครบ 5 ไฟล์ (`RentalDiscountStrategy`, `NoDiscountStrategy`, `StudentDiscountStrategy`, `VipDiscountStrategy`, `DiscountContext`)
4. ตั้งค่า custom ViewResolver ให้ template อยู่ที่ `resources/custom-templates/books/` แทน `templates/`
5. ทดสอบด้วย browser หรือ curl แล้วแคปหน้าจอ: list, add, edit, delete, และ `/books/rent/{id}` ที่คำนวณส่วนลดถูกต้องทั้ง 3 ประเภทสมาชิก

---

## 📊 เกณฑ์ให้คะแนน (จำลองจาก Lab7)

| หัวข้อ | คะแนน |
|---|---|
| Layered Design ถูกต้อง (Controller ไม่เก็บ logic เอง) | 20% |
| Strategy Pattern implement ถูกต้อง + สอดคล้อง OCP | 20% |
| CRUD ครบ 6 endpoint ทำงานถูกต้อง | 25% |
| Custom ViewResolver ตั้งค่าใช้งานได้ | 10% |
| คำตอบ GRASP/SOLID/Strategy (ส่วนที่ 1) | 15% |
| Code quality + README | 10% |

---

## 🚀 ขั้นตอน generate โปรเจกต์ (แบบย่อ ใช้ได้ทุกข้อสอบแนวนี้)

```bash
# วิธีที่ 1: ผ่าน curl เรียก Spring Initializr โดยตรง (เร็วที่สุด)
curl https://start.spring.io/starter.zip \
  -d dependencies=web,thymeleaf \
  -d type=maven-project \
  -d javaVersion=17 \
  -d bootVersion=3.3.0 \
  -d groupId=com.example \
  -d artifactId=exam-project \
  -d name=ExamProject \
  -o exam-project.zip

unzip exam-project.zip -d exam-project
cd exam-project
mvn spring-boot:run
```

```bash
# วิธีที่ 2: ผ่านเว็บ start.spring.io
# เลือก Maven, Java 17, Spring Boot 3.3.x, dependency: Spring Web + Thymeleaf
# กด Generate → แตกไฟล์ → เปิดใน IDE
```

โครงสร้างโฟลเดอร์ที่ควรได้ (อ้างอิงรูปแบบเดียวกับ Lab6/Lab7):

```
exam-project/
├── pom.xml
├── src/main/java/com/example/demo/
│   ├── DemoApplication.java
│   ├── model/Book.java
│   ├── strategy/
│   │   ├── RentalDiscountStrategy.java
│   │   ├── NoDiscountStrategy.java
│   │   ├── StudentDiscountStrategy.java
│   │   ├── VipDiscountStrategy.java
│   │   └── DiscountContext.java
│   ├── service/BookService.java
│   ├── config/ThymeleafConfig.java
│   └── controller/BookController.java
└── src/main/resources/
    ├── application.properties
    └── custom-templates/books/
        ├── list.html
        ├── add.html
        └── edit.html
```

---

## ✅ Checklist ก่อนเข้าสอบจริง

- [ ] เขียน layered CRUD (Model/Service/Controller) ด้วยมือได้โดยไม่เปิดดูตัวอย่าง (จับเวลา ~30 นาที)
- [ ] เขียน Strategy Pattern 3-4 คลาส + Context ได้โดยไม่ลืม interface
- [ ] ตั้งค่า custom `ViewResolver` ได้โดยไม่เปิด Lab6 ดู
- [ ] อธิบาย GRASP (Controller, Information Expert, Low Coupling, High Cohesion) เป็นคำพูดตัวเองได้ พร้อมยกตัวอย่างจากโจทย์
- [ ] อธิบาย SOLID ทีละตัวเป็นคำพูดตัวเอง พร้อมชี้ตำแหน่งในโค้ดที่ทำตามหลักนั้น
- [ ] จำ syntax `@GetMapping`, `@PostMapping`, `@PathVariable`, `@RequestParam`, `@ModelAttribute`, `@RequestBody` ได้ว่าใช้ต่างกันตรงไหน
- [ ] เขียน `.gitignore` + push repo ตามชื่อรูปแบบที่กำหนดได้ในเวลาจำกัด
