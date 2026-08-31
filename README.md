import sqlite3
from twilio.rest import Client
from flask import Flask, request

app = Flask(__name__)

# 
account_sid = "ضع الـ SID هنا"
auth_token = "ضع الـ Token هنا"
client = Client(account_sid, auth_token)


def init_db():
    conn = sqlite3.connect("products.db")
    cursor = conn.cursor()
    cursor.execute("""
        CREATE TABLE IF NOT EXISTS products (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            name TEXT NOT NULL,
            price REAL NOT NULL,
            description TEXT
        )
    """)
    # إدخال بيانات تجريبية
    cursor.execute("INSERT INTO products (name, price, description) VALUES (?, ?, ?)",
                   ("هاتف سامسونغ", 2500, "هاتف ذكي بشاشة 6.5 بوصة"))
    cursor.execute("INSERT INTO products (name, price, description) VALUES (?, ?, ?)",
                   ("حاسوب محمول", 6000, "حاسوب محمول بمعالج i7 وذاكرة 16GB"))
    conn.commit()
    conn.close()


def get_product_info(product_name):
    conn = sqlite3.connect("products.db")
    cursor = conn.cursor()
    cursor.execute("SELECT name, price, description FROM products WHERE name LIKE ?", ('%' + product_name + '%',))
    result = cursor.fetchone()
    conn.close()
    if result:
        return f"المنتج: {result[0]}\nالسعر: {result[1]} درهم\nالوصف: {result[2]}"
    else:
        return "عذراً، هذا المنتج غير موجود في قاعدة البيانات."

@app.route("/whatsapp", methods=["POST"])
def whatsapp_reply():
    incoming_msg = request.values.get("Body", "").lower()
    from_number = request.values.get("From")

    if "سعر" in incoming_msg or "منتج" in incoming_msg:
        product_name = incoming_msg.replace("سعر", "").replace("منتج", "").strip()
        reply = get_product_info(product_name)
    elif "مشكلة" in incoming_msg:
        reply = "من فضلك صف مشكلتك وسنساعدك فوراً."
    else:
        reply = "شكراً لتواصلك معنا، كيف يمكنني مساعدتك؟"

    client.messages.create(
        body=reply,
        from_="whatsapp:+14155238886",  # رقم واتساب الخاص بـ Twilio
        to=from_number
    )
    return "OK"

if __name__ == "__main__":
    init_db()
    app.run(port=5000)
