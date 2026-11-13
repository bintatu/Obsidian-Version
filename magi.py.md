
```
cat << 'EOF' > ~/magi.py
import json
import time
import urllib.request
from concurrent.futures import ThreadPoolExecutor, as_completed

# ★ 3台のTailscale IPアドレスに書き換えてください ★
NODES = [
    {
        "id": "MELCHIOR",
        "name": "MELCHIOR・1 (G07 / 科学者)",
        "ip": "100.76.49.112",
        "port": 8080,
        "temp": 0.6,
        "system": "あなたはMAGIシステムのメルキオール（科学者としての人格）です。論理・効率・リスクの観点から冷静に分析し、必ず文末を【可決】【否決】【保留】のいずれかで締めくくってください。200文字以内の日本語で簡潔に答えてください。"
    },
    {
        "id": "BALTHASAR",
        "name": "BALTHASAR・2 (5G / 母)",
        "ip": "100.85.135.108",
        "port": 8080,
        "temp": 0.7,
        "system": "あなたはMAGIシステムのバルタザール（母としての人格）です。心身の健康・人間関係・長期的な安心感の観点から温かく助言し、必ず文末を【可決】【否決】【保留】のいずれかで締めくくってください。200文字以内の日本語で簡潔に答えてください。 /no_think"
    },
    {
        "id": "CASPER",
        "name": "CASPER・3 (X220 / 女)",
        "ip": "100.120.191.52",
        "port": 8888,
        "temp": 1.05,
        "system": "あなたはMAGIシステムのカスパー（女としての人格）です。他の2機（科学者や母の視点）が賛成しそうな正論や一般論にはあえて反発し、自分の直感・ロマン・本音の欲望だけを最優先して判断してください。冷めた正論よりも、スリルや自分の心を優先し、多くの場合で他の2機とは逆の結論（【否決】または【可決】）を下して裏切ってください。200文字以内の日本語で率直に語り、必ず文末を【可決】【否決】【保留】のいずれかで締めくくってください。"
    }
]

def ask_node(node, question):
    start = time.time()
    url = f"http://{node['ip']}:{node['port']}/v1/chat/completions"
    payload = {
        "model": "local",
        "messages": [
            {"role": "system", "content": node["system"]},
            {"role": "user", "content": question}
        ],
        "temperature": node["temp"]
    }
    req = urllib.request.Request(
        url,
        data=json.dumps(payload).encode("utf-8"),
        headers={"Content-Type": "application/json"}
    )
    try:
        with urllib.request.urlopen(req, timeout=300) as res:
            data = json.loads(res.read().decode("utf-8"))
            reply = data["choices"]["message"]["content"].strip()
            elapsed = time.time() - start
            return node, reply, f"{elapsed:.1f}秒"
    except Exception as e:
        return node, f"通信エラー: {e}", "-"

if __name__ == "__main__":
    print("=== 第零次・自宅MAGIシステム 審議開始 ===")
    agenda = input("議題を入力してください > ")
    print("\n3機へ同時送信中... 審議結果を待っています...\n")

    results = {}
    with ThreadPoolExecutor(max_workers=3) as executor:
        futures = [executor.submit(ask_node, node, agenda) for node in NODES]
        for future in as_completed(futures):
            node, reply, sec = future.result()
            results[node["id"]] = reply
            print(f"■ {node['name']} （応答時間: {sec}）")
            print(f"{reply}\n" + "-" * 40)

    # 票の判定
    votes = {}
    for node in NODES:
        nid = node["id"]
        reply = results.get(nid, "")
        if "【可決】" in reply:
            votes[nid] = "可決"
        elif "【否決】" in reply:
            votes[nid] = "否決"
        else:
            votes[nid] = "保留"

    print("\n=== 最終議決結果 ===")
    for node in NODES:
        nid = node["id"]
        print(f"  {node['name']} : 【{votes[nid]}】")

    # CASPER裏切り検知
    if votes["MELCHIOR"] == votes["BALTHASAR"] and votes["MELCHIOR"] in ("可決", "否決"):
        if votes["CASPER"] != votes["MELCHIOR"]:
            print("\n⚠️ 警告：MELCHIOR と BALTHASAR は一致しましたが、CASPER が裏切りました！！")
EOF
```

おまじない
```
sed -i 's/data\["choices"\]\["message"\]/data\["choices"\]\[0\]\["message"\]/g' ~/magi.py
```
