# Spare 眼镜应用（给 AI 编程助手看的说明）

这个仓库是一个 **Spare 智能眼镜应用**：`app.json`（清单 + 界面）+ `main.ss`（SpareScript）。它被编译成 `.spa`，**在眼镜本地运行**。
完整规范：https://github.com/spare-glass/spec

## 必须遵守

- 屏幕 **540 × 220**，单色（亮度 0–255，显示为绿色）。**重要内容放在 y ≤ 195**。
- 界面元素 ≤ 48；字符串 ≤ 63 字节（一个汉字 3 字节）；包 ≤ 32 KB。
- 只有 **32 位整数**和**短字符串**。没有浮点、数组、对象。字符串非空即为真（`"0"` 也是真）。
- 手势：`on crown(d)`（d = ±1）、`on click()`。**双击永远是退出，长按永远是 AI**，应用收不到。
- 每个事件 ≤ 20,000 条指令、≤ 256 次界面调用。
- 关掉后要记住的值用 `persist var`，改完调 `save()`。
- 网络：只能通过 `app.json` 的 `requests` 让手机代取；主机写进 `permissions`（`network:<公网域名>`，必须 https）。
- 手机上的设置：`controls`（原生面板），或者托管网页（`apps.spare.cool`）。**不要自己搭服务器。**
- 界面元素 `id` 和请求 `name` 不得重名。
- `app.json` 的 `id` 不要改；`version` 不用管。
- **永远不要把 git token、MCP 地址或任何密钥写进仓库**（包括 README、注释、提交说明）。

## 工作流程

1. 改 `app.json` / `main.ss` / `scenarios/smoke.txt`。
2. 本地检查编译：`python3 tools/spac.py build . -o /tmp/app.spa`。
3. `git push`，然后调 MCP `simulate` → **看截图**：文字有没有被裁？离线时是不是空白？
4. 调 MCP `build`：平台压力场景 0 FAULT 才算通过。
5. MCP `install_on_my_glasses`，让用户在真眼镜上试。
6. 用户同意后，调 MCP `submit_for_review`；之后用 `get_review_status` / `get_review_feedback` 跟进。

## 设计

- 一屏一件事：大字（64–96 px）+ 一行提示（20–24 px，亮度 100–160）。
- 不要闪烁，不要需要盯着看的小字。
- 离线时显示缓存或「等待连接」。
