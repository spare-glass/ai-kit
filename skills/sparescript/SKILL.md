---
name: sparescript
description: 写、调试和发布 Spare 智能眼镜应用（SpareScript + app.json，540×220 单色绿屏）。用户要给 Spare 眼镜做应用、改 main.ss / app.json、或者调用 spare-workshop MCP 时使用。
---

# SpareScript

## 先做

1. 读仓库根目录的 `AGENTS.md`（没有就用这个 skill 的规则）。
2. 调 MCP `whoami`，确认开发者身份和配额。

## 语言速查

```js
var g = 0;                    // 全局变量 ≤ 64
persist var best = 0;         // 持久变量；改完 save()
fn f(a, b) { var t = a + b; return t; }   // 参数 ≤ 8，调用深度 ≤ 8
on open() {}  on close() {}  on crown(d) {}  on click() {}
on timer(id) {}  on data(seq) {}  on control(id) {}
```

- 语句：`var` `=` `+=` `-=` `*=` `/=` `if/else` `while` `break` `continue` `return`。没有 `for`、`++`、三元。
- 值：32 位整数、≤ 63 字节字符串。`+` 遇到字符串就拼接；`/` 除以 0 得 0；`-7 / 2` 得 `-3`。
- 界面：`text show gray move size value image anim_x anim_opa anim_scale`。
- 时间：`timer(id 0–3, ms ≥ 100, repeat)`、`now()` 毫秒、`clock()` 当天分钟、`seconds()`。
- 字符串：`str num len sub pad`；数学：`min max clamp abs rand`。
- 数据：`request(req, arg ≤ 10 字节)`、`data(key)`、`connected()`；失败时 `data("_err")` 非空。
- 面板：`ctl("id")`、`ctl_set("id", v)`。系统：`battery save exit`。

## 检查清单（每次 simulate 之后）

- [ ] 所有文字都在 y ≤ 195 以内，没有被裁
- [ ] 断开手机（场景 `connect 0` 或 `reply _err=net`）后不是空白
- [ ] 没有闪烁（亮度变化 ≤ 3 次/秒）
- [ ] `build` 0 FAULT
- [ ] 只申请了用到的权限
- [ ] 没有把 token、MCP 地址写进任何文件
