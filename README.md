# fuckTronclass

中国传媒大学畅课（TronClass）视频页助手 userscript。

> 适用于 `https://courses.cuc.edu.cn`。脚本不包含任何账号信息，任何用户登录自己的账号后即可使用。
> 其他学校的 TronClass 实例需修改 `config.js` / `webpack.config.js` 中的域名与路由。

## 功能

- [x] 视频十倍速（可切 1x / 2x / 4x / 10x / 16x）
- [x] 一键完成（自动上报观看进度）
- [x] 自动完成开关（进入视频页自动上报）
- [x] 屏蔽暂停开关
- [x] 抓包器（显示站点真实请求与响应，便于排障）

## 进度上报说明

畅课驱动「完成度」的接口是：

```
POST /api/course/activities-read/{activityId}
Content-Type: application/json
{ "start": 0, "end": 363 }
```

**关键坑**：服务器限制**单次上报时长 ≤ 125 秒**，超过会返回
`The set time duration（...）is too long, maximum is 125s`。
畅课自己的播放器上报的是整段时长（如 363s），因此**它自己的上报也会失败**，这就是进度长期不动的原因。

本脚本的「一键完成」按 **120 秒分段**连续上报（0-120、120-240…直到视频时长），
全部返回 `completeness: "full"` 即完成。

## 修复说明

原项目失效的原因，以及本版本的修复：

1. **依赖仓库已删除** — `git+https://github.com/CUC-Life-Hack/userscript-base.git` 已 404。
   → 已把 `userscript-base`（`Hack` / `Panel` / `Delay` / `PostAjax`）内置到 `src/base/`。

2. **构建工具链冲突** — `webpack-userscript@2` 锁死 `webpack@4`，与 `webpack@5` 冲突。
   → 升级到 `webpack@5` + `webpack-userscript@3`（`UserscriptPlugin`）。

3. **构建输出目录错误** — 产物错误写进 `dev/`。
   → 改为读取 `--mode`，`npm run build` 正确输出到 `dist/`。

4. **站点升级** — 当前站点不再暴露全局 `videojs`，改为直接操作 `<video>` 元素。

5. **路由变化** — 视频页从 `/learning-activity/full-screen#/{id}` 变为 `/learning-activity#/{id}`，且为 SPA 无刷新跳转。
   → `@include` 放宽到整站，运行时判断路由并监听 `hashchange`。

6. **进度不上报** — 见上「进度上报说明」，站点自身上报因 125s 限制失败。


## 安装

构建后把 `dist/main.user.js` 导入 TamperMonkey；或直接访问发布路径：

```
https://github.com/AcuraIntegurl1211/fuckTronclass/raw/main/dist/main.user.js
```

安装步骤：

1. 浏览器安装 [Tampermonkey](https://www.tampermonkey.net/) 扩展。
2. 打开上面的发布路径（或 Tampermonkey → 实用工具 → 从文件导入 `dist/main.user.js`）。
3. Tampermonkey 会弹出安装页，点「安装」。
4. 登录畅课，进入任意视频活动页，右上角出现面板即生效。

## 开发

```bash
npm install
npm run build      # 生产构建 → dist/main.user.js
npm run dev        # 开发监听 → dev/main.user.js
```

## 目录

```
src/
  main.js           # 畅课 Hack 主逻辑
  base/
    index.js        # 内置 userscript-base 出口
    hack.js         # Hack 运行器（初始化重试 + 面板）
    panel.js        # 右上角浮层面板
    panel.css       # 面板样式
    ajax.js         # Delay / Ajax / PostAjax / unsafeWindow 桥接
```

## 已知限制

- 「屏蔽暂停」通过拦截播放器的 `pause` 事件实现，开启后无法手动暂停（默认关闭）。
- 进度上报按 120 秒分段，超长视频会连续发多段请求（每段间隔 0.4s）。
- 若畅课再次改版（域名/路由/接口/时长上限变化），可能需要跟进调整。

## 免责声明

本项目仅供学习与技术研究，使用者需自行承担使用风险。请遵守学校与平台的相关规定。

## License

GPL-3.0-or-later