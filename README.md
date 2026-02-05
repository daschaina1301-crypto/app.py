from flask import Flask, request, jsonify
import requests

app = Flask(__name__)

@app.route('/fetch')
def fetch_player():
    uid = request.args.get('uid')
    if not uid:
        return jsonify({"error": "No UID provided"}), 400

    url = "https://client.ind.freefiremobile.com/GetPlayerPersonalShow"
    payload = f"region=IND&uid={uid}"
    headers = {"Content-Type": "application/x-www-form-urlencoded"}

    try:
        # Standard requests work without the 'async' extra
        r = requests.post(url, data=payload, headers=headers, timeout=15)
        return jsonify(r.json())
    except Exception as e:
        return jsonify({"error": str(e)}), 500

if __name__ == "__main__":
    app.run()
