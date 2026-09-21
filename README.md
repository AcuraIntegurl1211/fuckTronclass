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
