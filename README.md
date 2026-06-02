# Lerning Jsreport

**jsreport** เป็นเซิร์ฟเวอร์สำหรับสร้างรายงาน (Report Server) ที่ทรงพลังมาก โดยใช้เทคโนโลยีของเว็บ (HTML, CSS, JavaScript) ในการออกแบบ และสามารถแปลงออกมาเป็นไฟล์ต่างๆ เช่น PDF, Excel หรือ Word ได้

การรัน jsreport ผ่าน Docker เป็นวิธีที่ง่ายและสะอาดที่สุด นี่คือคำแนะนำแบบทีละขั้นตอน:

### ขั้นตอนที่ 1: รัน jsreport ผ่าน Docker

เปิด Terminal หรือ Command Prompt ของคุณ แล้วรันคำสั่งนี้:

```bash
docker run -p 5488:5488 -v jsreport_data:/app/data jsreport/jsreport

```

**คำอธิบายคำสั่ง:**

* `-p 5488:5488` : ทำการเชื่อมพอร์ต 5488 ของเครื่องคุณ เข้ากับพอร์ต 5488 ของ Container (พอร์ตเริ่มต้นของ jsreport)
* `-v jsreport_data:/app/data` : สร้าง Docker Volume ชื่อ `jsreport_data` เพื่อเก็บข้อมูลรายงานของคุณไว้ ไม่ให้หายไปเมื่อปิด Container
* `jsreport/jsreport` : เป็นชื่อ Image อย่างเป็นทางการจาก jsreport

*(หมายเหตุ: การรันครั้งแรกอาจจะใช้เวลาสักครู่ในการดาวน์โหลด Image จาก Docker Hub)*

---

### ขั้นตอนที่ 2: เข้าสู่ระบบหน้า jsreport Studio

เมื่อระบบรันเสร็จเรียบร้อย ให้เปิด Web Browser ของคุณแล้วเข้าไปที่:
👉 **http://localhost:5488**

คุณจะพบกับหน้าตาของ **jsreport Studio** ซึ่งเป็นพื้นที่สำหรับออกแบบรายงานทั้งหมด

---

### ขั้นตอนที่ 3: สร้างข้อมูลตัวอย่าง (Data)

ปกติแล้วรายงานต้องมีข้อมูล (Data) มาแสดงผล เราจะสร้างข้อมูลจำลองขึ้นมาก่อน:

1. ที่แถบเมนูด้านซ้ายบน (Entity Tree) ให้คลิกขวาพื้นที่ว่าง เลือก **New entity -> data**
2. ตั้งชื่อว่า `MyData` แล้วกด **Create**
3. ในหน้าต่างแก้ไขทางขวา ให้ใส่ข้อมูลรูปแบบ JSON ลงไป เช่น:

```json
{
  "title": "รายงานยอดขายประจำเดือน",
  "items": [
    { "name": "เมาส์ไร้สาย", "price": 500 },
    { "name": "คีย์บอร์ด", "price": 1200 },
    { "name": "หน้าจอ 24 นิ้ว", "price": 4500 }
  ]
}

```

4. กดปุ่ม **Save** (รูปแผ่นดิสก์) ที่แถบเมนูด้านบนซ้าย หรือกด `Ctrl + S`

---

### ขั้นตอนที่ 4: สร้างเทมเพลตรายงาน (Template)

1. กลับไปที่เมนูด้านซ้าย คลิกขวาพื้นที่ว่าง เลือก **New entity -> template**
2. ตั้งชื่อว่า `MyInvoice` แล้วกด **Create**
3. เมื่อหน้าต่าง Template เปิดขึ้นมา ให้สังเกตการตั้งค่าด้านบน (Properties):
* **Engine:** เลือก `handlebars` (เป็นตัวอ่านตัวแปรที่เราจะใช้)
* **Recipe:** เลือก `chrome-pdf` (เพื่อให้ออกมาเป็นไฟล์ PDF)


4. เลื่อนลงมาที่หัวข้อ **Sample data** ในแถบเมนูตั้งค่าทางซ้ายหรือขวา ให้เลือก `MyData` ที่เราสร้างไว้ในขั้นตอนที่ 3 เพื่อทำการผูกข้อมูลเข้ากับเทมเพลตนี้

---

### ขั้นตอนที่ 5: เขียนโค้ด HTML เพื่อออกแบบรายงาน

ในช่องสำหรับเขียนโค้ดตรงกลาง ให้คัดลอกโค้ด HTML ผสม Handlebars นี้ไปวาง:

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <style>
        body { font-family: 'Sarabun', Tahoma, sans-serif; padding: 20px; }
        h1 { color: #2c3e50; }
        table { width: 100%; border-collapse: collapse; margin-top: 20px; }
        th, td { border: 1px solid #bdc3c7; padding: 10px; text-align: left; }
        th { background-color: #ecf0f1; }
    </style>
</head>
<body>
    <h1>{{title}}</h1>

    <table>
        <thead>
            <tr>
                <th>ลำดับ</th>
                <th>ชื่อสินค้า</th>
                <th>ราคา (บาท)</th>
            </tr>
        </thead>
        <tbody>
            {{#each items}}
            <tr>
                <td>{{@index}}</td>
                <td>{{name}}</td>
                <td>{{price}}</td>
            </tr>
            {{/each}}
        </tbody>
    </table>
</body>
</html>

```

---

### ขั้นตอนที่ 6: ดูผลลัพธ์ (Preview & Run)

1. กดปุ่ม **Run** (ปุ่มสีเขียวที่มีสัญลักษณ์ Play มุมขวาบน)
2. jsreport จะทำการรวมโค้ด HTML เข้ากับข้อมูล JSON และเรนเดอร์ออกมาเป็นไฟล์ **PDF** ให้คุณดูในหน้าต่างด้านขวาทันที!
3. หากคุณแก้ไขข้อมูลใน `MyData` หรือเปลี่ยนสีใน HTML แล้วกด Run ใหม่ ไฟล์ PDF ก็จะอัปเดตตามทันที

### การนำไปใช้งานจริง (API)

ในการใช้งานจริงกับระบบของคุณ (เช่น Node.js, Python, PHP, C#) คุณไม่ต้องมานั่งกด Run เอง แต่ระบบของคุณจะส่ง HTTP POST Request ที่มีข้อมูล JSON ไปที่ `http://localhost:5488/api/report` และ jsreport จะตอบกลับมาเป็นไฟล์ PDF ให้ระบบของคุณดาวน์โหลดหรือส่งต่อให้ผู้ใช้งานได้เลย
