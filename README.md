📦 SME Supply Chain Data Pipeline & Analytics
โปรเจกต์จำลองและวิเคราะห์ข้อมูลห่วงโซ่อุปทาน (Supply Chain) สำหรับธุรกิจขนาดกลางและขนาดย่อม (SME) เพื่อปูทางสู่การทำ Demand Forecasting และ Inventory Optimization

💡 1. Core Idea & Problem Statement (วิธีคิดและโจทย์ธุรกิจ)
ปัญหาหลักของธุรกิจค้าปลีก/SME คือ " Inventory Invisibility" (การมองไม่เห็นสถานะสต็อก) และ "Demand Uncertainty" (ความไม่แน่นอนของความต้องการซื้อ) ทำให้เกิดสภาวะสินค้าขาดมือ (Stockout) หรือสินค้าล้นคลังจนทุนจม (Overstock)

แนวคิดของโปรเจกต์นี้:
เราสร้าง Data Pipeline จำลองระบบนิเวศน์ธุรกิจที่สมจริงขึ้นมา โดยเชื่อมโยง ตารางธุรกรรม (Fact Tables) และ ตารางหลัก (Master Data) เข้าด้วยกัน พร้อมทั้งใส่ปัจจัยภายนอก (External Factors) เช่น สภาพอากาศ และวันหยุด เพื่อให้โมเดล Machine Learning ในอนาคตสามารถพยากรณ์ยอดขายและวางแผนเติมสินค้า (Replenishment) ได้อย่างแม่นยำ

🔀 2. Workflow & Data Lineage Diagram
กระบวนการไหลของข้อมูลและการจัดเก็บในคลังข้อมูล:

```mermaid
graph TD
    %% ปัจจัยนำเข้า
    Weather[🌤️ Weather Data] --> Sales[📊 Sales & Stock Transactions]
    Holidays[📅 Holiday Calendar] --> Sales
    Products[📦 Product Master] --> Sales
    Stores[🏢 Store Master] --> Sales
    Customers[👥 Customer Master] --> Sales
    
    %% คลังจัดเก็บข้อมูลหลัก
    Sales --> SQLite[🗄️ Central Database: SQLite .db]
    
    %% การกระจายข้อมูลออก
    SQLite --> CSV[💾 Data Export: CSV Files<br>สำหรับ Model / Data Scientist]
    SQLite --> Excel[📈 Business Report: Excel .xlsx<br>สำหรับผู้บริหาร / Business Analyst]

    %% ใส่สีสันให้สวยงาม
    style SQLite fill:#fff2cc,stroke:#d6b656,stroke-width:2px
    style CSV fill:#dae8fc,stroke:#6c8ebf,stroke-width:1px
    style Excel fill:#d5e8d4,stroke:#82b366,stroke-width:1px

📖 3. Data Dictionary (บางส่วนที่เป็นแกนหลัก)
sales_transaction: ตารางบันทึกประวัติธุรกรรมการขายหน้าร้าน (แกนหลักในการทำ Forecasting)

po_id (String - PK): รหัสใบสั่งซื้อสุ่มขึ้นต้นด้วย "PO"

datetime (Timestamp): วันและเวลาที่เกิดการซื้อขาย

product_id (String - FK): รหัสสินค้า อ้างอิงตาราง Product Master

qty (Integer): จำนวนชิ้นที่ขายได้ (Distribution Weight เน้นขายปลีก 1-5 ชิ้น)

price (Float): ราคาขาย ณ ช่วงเวลานั้น ๆ

product_master: ตารางข้อมูลสินค้า

product_id (String - PK): รหัสสินค้า (SKU)

cost (Float): ต้นทุนสินค้า (เพิ่มเข้ามาเพื่อใช้คำนวณ Net Profit / Gross Margin)

shelf_life_days (Integer): อายุของสินค้า (ใช้ตัดเกรดสินค้าหมดอายุใน Waste Log)

🤖 4. AI Tools Usage & Verification Note
AI Tools ที่ใช้: ใช้ Gemini AI เป็นคู่คิด (AI Collaborator) ในการร่วมออกแบบ Schema ของฐานข้อมูล, เขียนโค้ดสุ่มกระจายน้ำหนักความน่าจะเป็น (Probability Weights) ของยอดขาย และช่วยดีบั๊กโค้ดระบบท่อส่งข้อมูล (Data Pipeline)

กระบวนการตรวจสอบผลลัพธ์ (Human-in-the-loop Validation):

โครงสร้างสอดคล้อง (Schema Check): ตรวจสอบคอลัมน์ของไฟล์ Output ทุกไฟล์ว่าตรงตาม Data Mockup Guide ของโปรเจกต์ 100%

ความสมเหตุสมผลเชิงธุรกิจ (Business Logic Check): ตรวจสอบว่าราคาสินค้า (price) ต้องสูงกว่าต้นทุน (cost) เสมอ และปริมาณของเสียใน waste_log ต้องไม่เกินยอดสต็อกคงคลัง
