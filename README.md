from flask import Flask, request, jsonify
import httpx
import logging

app = Flask(__name__)

@app.route('/fetch')
async def fetch_player():
    uid = request.args.get('uid')
    if not uid:
        return jsonify({"error": "No UID provided"}), 400

    url = "https://client.ind.freefiremobile.com/GetPlayerPersonalShow"
    payload = f"region=IND&uid={uid}"
    headers = {
        "Content-Type": "application/x-www-form-urlencoded",
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36"
    }

    async with httpx.AsyncClient(verify=False) as client:
        try:
            # We use a longer timeout (20s) for slow IND servers
            response = await client.post(url, data=payload, headers=headers, timeout=20.0)
            
            # This catches cases where Garena blocks the IP
            if response.status_code != 200:
                return jsonify({"error": f"Garena Error {response.status_code}", "details": response.text}), response.status_code
            
            return jsonify(response.json())
            
        except httpx.TimeoutException:
            return jsonify({"error": "Connection Timed Out. Garena is too slow."}), 504
        except Exception as e:
            # This returns the actual error message to your browser
            return jsonify({"error": str(e)}), 500

if __name__ == "__main__":
    app.run()# my-ff-api
