#PC #Tips #tenki 

`TEXT`

```bash
/alias add wttr /exec -pipe "/mute /set plugins.var.wttr" url:wttr.in/akita?format=%l:+%c+%f+%h+%p+%P+%m+%w+%S+%s;/wait 3 /item refresh wttr
/trigger add wttr timer 60000;0;0 "" "" "/wttr"
/item add wttr "" "${plugins.var.wttr}"
/eval /set weechat.bar.status.items ${weechat.bar.status.items},spacer,wttr
/eval /set weechat.startup.command_after_plugins ${weechat.startup.command_after_plugins};/wttr
/wttr
```