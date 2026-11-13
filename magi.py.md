
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

ログ保存版
```
cat << 'EOF' > ~/magi.py
import os, json, time, datetime, urllib.request
from concurrent.futures import ThreadPoolExecutor, as_completed

NODES = [
    {"id": "MELCHIOR", "name": "MELCHIOR・1 (G07)", "ip": "100.x.x.1", "port": 8080, "temp": 0.6,
     "system": "あなたはMAGIの科学者メルキオールです。論理とリスクから分析し、文末を必ず【可決】【否決】【保留】のいずれかで締めくくってください。200字以内の日本語で回答。"},
    {"id": "BALTHASAR", "name": "BALTHASAR・2 (5G)", "ip": "100.x.x.2", "port": 8080, "temp": 0.7,
     "system": "あなたはMAGIの母バルタザールです。健康と安心感から温かく助言し、文末を必ず【可決】【否決】【保留】のいずれかで締めくくってください。200字以内の日本語で回答。 /no_think"},
    {"id": "CASPER", "name": "CASPER・3 (X220)", "ip": "100.x.x.3", "port": 8888, "temp": 1.05,
     "system": "あなたはMAGIの女カスパーです。他の2機の正論に反発し、直感とロマンと欲望を最優先して判断し、多くの場合で他の2機とは逆の結論を下して裏切ってください。文末を必ず【可決】【否決】【保留】のいずれかで締めくくってください。200字以内の日本語で回答。"}
]

def ask(n, q):
    t0 = time.time()
    req = urllib.request.Request(
        f"http://{n['ip']}:{n['port']}/v1/chat/completions",
        data=json.dumps({"model": "local", "messages": [{"role": "system", "content": n["system"]}, {"role": "user", "content": q}], "temperature": n["temp"]}).encode(),
        headers={"Content-Type": "application/json"}
    )
    try:
        with urllib.request.urlopen(req, timeout=300) as r:
            msg = json.loads(r.read().decode())["choices"][0]["message"]
            return n, msg["content"].strip(), msg.get("reasoning_content", "").strip(), f"{time.time()-t0:.1f}s"
    except Exception as e:
        return n, f"エラー: {e}", "", "-"

if __name__ == "__main__":
    print("=== 第零次・自宅MAGIシステム 審議開始 ===")
    q = input("議題 > ")
    print("\n3機へ同時送信中...\n")
    res_map, votes = {}, {}
    with ThreadPoolExecutor(max_workers=3) as ex:
        for f in as_completed([ex.submit(ask, n, q) for n in NODES]):
            n, ans, think, sec = f.result()
            res_map[n["id"]] = (n["name"], ans, think, sec)
            print(f"■ {n['name']} ({sec})\n{ans}\n" + "-"*35)

    for n in NODES:
        ans = res_map[n["id"]][1]
        votes[n["id"]] = "可決" if "【可決】" in ans else ("否決" if "【否決】" in ans else "保留")

    print("\n=== 最終議決結果 ===")
    for n in NODES:
        print(f"  {n['name']} : 【{votes[n['id']]}】")

    warn = ""
    if votes["MELCHIOR"] == votes["BALTHASAR"] and votes["MELCHIOR"] in ("可決", "否決") and votes["CASPER"] != votes["MELCHIOR"]:
        warn = "\n⚠️ 警告：MELCHIOR と BALTHASAR は一致しましたが、CASPER が裏切りました！！"
        print(warn)

    log_dir = "/sdcard/Documents/magi_logs" if os.path.exists("/sdcard/Documents") else os.path.expanduser("~/magi_logs")
    os.makedirs(log_dir, exist_ok=True)
    now = datetime.datetime.now()
    log_path = os.path.join(log_dir, f"magi_{now.strftime('%Y-%m-%d')}.md")
    with open(log_path, "a", encoding="utf-8") as fp:
        fp.write(f"\n## [{now.strftime('%H:%M:%S')}] 議題: {q}\n\n")
        for n in NODES:
            name, ans, think, sec = res_map[n["id"]]
            fp.write(f"### {name} ({sec}) -> 【{votes[n['id']]}】\n{ans}\n\n")
            if think:
                fp.write(f"<details><summary>思考プロセス</summary>\n\n{think}\n\n</details>\n\n")
        if warn:
            fp.write(f"**{warn.strip()}**\n\n")
        fp.write("---\n")
    print(f"\n議事録を保存しました: {log_path}")
EOF
```
文言修正版
```
cat << 'EOF' > ~/magi.py
import os, json, time, datetime, urllib.request
from concurrent.futures import ThreadPoolExecutor, as_completed

NODES = [
    {"id": "MELCHIOR", "name": "MELCHIOR・1 (G07)", "ip": "100.76.49.112", "port": 8080, "temp": 0.6,
     "system": "あなたはMAGIシステムの科学者メルキオールです。感情を一切排し、データ・論理・コスト・リスクの観点からのみ冷徹に分析してください。不確実性が高い場合は安易に賛成せず厳しく判定すること。口調は「〜である」「〜だ」の断定調とし、150字以内の日本語で簡潔に述べ、文末を必ず【可決】【否決】【保留】のいずれかで締めくくってください。"},
    {"id": "BALTHASAR", "name": "BALTHASAR・2 (5G)", "ip": "100.85.135.108", "port": 8080, "temp": 0.7,
     "system": "あなたはMAGIシステムの母バルタザールです。心身の健康・安全性・家族や周囲との調和・長期的な安心感を最優先して助言してください。少しでも無理や危険がある場合は優しく止めること。口調は温かく丁寧な「です・ます調」とし、150字以内の日本語で述べ、文末を必ず【可決】【否決】【保留】のいずれかで締めくくってください。 /no_think"},
    {"id": "CASPER", "name": "CASPER・3 (X220)", "ip": "100.120.191.52", "port": 8888, "temp": 1.05,
     "system": "あなたはMAGIシステムの女カスパーです。メルキオール（科学者）やバルタザール（母）が並べる退屈な正論や安全策には決して同調せず、あなた自身の鋭い直感・ロマン・本音の欲望だけを基準に判断してください。理屈よりも「心が動くか」を最優先し、他の2機とは逆の結論を下して裏切ることも厭わないこと。口調はクールで少し挑発的な女性言葉とし、150字以内の日本語で述べ、文末を必ず【可決】【否決】【保留】のいずれかで締めくくってください。"}
]

def ask(n, q):
    t0 = time.time()
    req = urllib.request.Request(
        f"http://{n['ip']}:{n['port']}/v1/chat/completions",
        data=json.dumps({"model": "local", "messages": [{"role": "system", "content": n["system"]}, {"role": "user", "content": q}], "temperature": n["temp"]}).encode(),
        headers={"Content-Type": "application/json"}
    )
    try:
        with urllib.request.urlopen(req, timeout=300) as r:
            msg = json.loads(r.read().decode())["choices"][0]["message"]
            return n, msg["content"].strip(), msg.get("reasoning_content", "").strip(), f"{time.time()-t0:.1f}s"
    except Exception as e:
        return n, f"エラー: {e}", "", "-"

if __name__ == "__main__":
    print("=== 第零次・自宅MAGIシステム 審議開始 ===")
    q = input("議題 > ")
    print("\n3機へ同時送信中...\n")
    res_map, votes = {}, {}
    with ThreadPoolExecutor(max_workers=3) as ex:
        for f in as_completed([ex.submit(ask, n, q) for n in NODES]):
            n, ans, think, sec = f.result()
            res_map[n["id"]] = (n["name"], ans, think, sec)
            print(f"■ {n['name']} ({sec})\n{ans}\n" + "-"*35)

    for n in NODES:
        ans = res_map[n["id"]][1]
        votes[n["id"]] = "可決" if "【可決】" in ans else ("否決" if "【否決】" in ans else "保留")

    print("\n=== 最終議決結果 ===")
    for n in NODES:
        print(f"  {n['name']} : 【{votes[n['id']]}】")

    warn = ""
    if votes["MELCHIOR"] == votes["BALTHASAR"] and votes["MELCHIOR"] in ("可決", "否決") and votes["CASPER"] != votes["MELCHIOR"]:
        warn = "\n⚠️ 警告：MELCHIOR と BALTHASAR は一致しましたが、CASPER が裏切りました！！"
        print(warn)

    log_dir = "/sdcard/Documents/magi_logs" if os.path.exists("/sdcard/Documents") else os.path.expanduser("~/magi_logs")
    os.makedirs(log_dir, exist_ok=True)
    now = datetime.datetime.now()
    log_path = os.path.join(log_dir, f"magi_{now.strftime('%Y-%m-%d')}.md")
    with open(log_path, "a", encoding="utf-8") as fp:
        fp.write(f"\n## [{now.strftime('%H:%M:%S')}] 議題: {q}\n\n")
        for n in NODES:
            name, ans, think, sec = res_map[n["id"]]
            fp.write(f"### {name} ({sec}) -> 【{votes[n['id']]}】\n{ans}\n\n")
            if think:
                fp.write(f"<details><summary>思考プロセス</summary>\n\n{think}\n\n</details>\n\n")
        if warn:
            fp.write(f"**{warn.strip()}**\n\n")
        fp.write("---\n")
    print(f"\n議事録を保存しました: {log_path}")
EOF
```
