
# 一度現在のhbbsとhbbrをすべて強制終了する
pkill -f hbbs
pkill -f hbbr

# 再度バックグラウンドで立ち上げる（IPアドレスはご自身のものに変えてください）
nohup ./hbbs -r 192.168.x.x:21117 > hbbs.log 2>&1 &
nohup ./hbbr > hbbr.log 2>&1 &
