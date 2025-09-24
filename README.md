# my-eleventh-repo
testing
from flask import Flask, request, redirect
import sqlite3, string, random

app = Flask(__name__)
db = sqlite3.connect("urls.db", check_same_thread=False)
db.execute("CREATE TABLE IF NOT EXISTS urls (code TEXT, url TEXT)")

def generate_code():
    return ''.join(random.choices(string.ascii_letters + string.digits, k=6))

@app.route("/shorten", methods=["POST"])
def shorten():
    url = request.form["url"]
    code = generate_code()
    db.execute("INSERT INTO urls VALUES (?, ?)", (code, url))
    db.commit()
    return f"Short URL: http://localhost:5000/{code}"

@app.route("/<code>")
def redirect_to_url(code):
    cur = db.execute("SELECT url FROM urls WHERE code=?", (code,))
    row = cur.fetchone()
    return redirect(row[0]) if row else "Not found", 404

if __name__ == "__main__":
    app.run(debug=True)
