# 更新与部署说明

纯静态站点：没有构建步骤、没有后端、没有依赖安装。

## 目录

| 路径 | 内容 |
|---|---|
| `index.html` | 实验台本体（判定逻辑与离线版一致，改为相对路径 + 浏览器内本地导入） |
| `licenses.html` | 许可与署名：公开字体清单、版权行、许可正文链接、未公开文件及原因 |
| `data/fontlab-data.js` | 字体清单 + cmap 码点区间 + Unicode 区段表（不含任何本机路径） |
| `fonts/` | 12 个公开字体文件（原样复制，未做任何修改） |
| `licenses/` | 随附的许可正文与字体内嵌声明 |
| `netlify.toml` | `publish = "."`，站点根目录即本目录 |

## 本地预览

站点本身不需要任何运行时或后端，只需要一个能提供静态文件的服务器：

```
npx serve .            # 或 VS Code 的 Live Server，或任何静态服务器
```

然后用浏览器打开它给出的地址。注意：**直接双击 `index.html`（`file://`）不行** ——
浏览器会拦截 `file://` 页面读取字体文件，字体无法加载。

## 更新内容

1. 改页面：直接编辑 `index.html` / `licenses.html`。
2. 改公开字体集合：在交付目录的 `_scripts/publish_build.py` 里调整 `PUBLIC_IDS` 与 `LICENSE_MAP`，
   按下方顺序重跑脚本，会同时重建 `data/`、`fonts/`、`licenses/`、`licenses.html`、`netlify.toml`。
3. 提交推送：

```
git add -A
git commit -m "update"
git push
```

推送到关联的 GitHub 仓库后，Netlify 会自动重新发布（默认几十秒）。
若用 Netlify 手动部署，把本目录整体拖到 Netlify 的 Deploys 面板即可。

## 重新生成与验收（脚本在交付目录 `07_字体盘点/_scripts/`，不在本仓库内）

```
python _scripts/publish_nametable.py   # 读取公开字体 name 表里的版权 / 许可声明
python _scripts/publish_build.py       # 复制字体 + 生成 data/ licenses/ licenses.html netlify.toml
node   _scripts/publish_verify.js      # 真实 Chrome 验收：73 项断言
```

`publish_build.py` 会逐文件校验复制后的 SHA-256 与源文件一致，不一致直接中止。
生成脚本只读源字体目录，不会修改任何原始字体文件。

