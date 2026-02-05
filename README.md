from flask import Flask, request, jsonify
import httpx

app = Flask(__name__)

@app.route('/fetch')
async def fetch_player():
    uid = request.args.get('uid')
    if not uid:
        return jsonify({"error": "No UID provided"}), 400

    # Stable 2026 IND Endpoint
    url = f"https://client.ind.freefiremobile.com/GetPlayerPersonalShow"
    payload = f"region=IND&uid={uid}"
    headers = {"Content-Type": "application/x-www-form-urlencoded"}

    async with httpx.AsyncClient(verify=False) as client:
        try:
            # Added a longer timeout for slow server responses
            response = await client.post(url, data=payload, headers=headers, timeout=20)
            return jsonify(response.json())
        except Exception as e:
            # This will show you the ACTUAL error in your browser
            return jsonify({"error": str(e)}), 500

if __name__ == "__main__":
    app.run()
