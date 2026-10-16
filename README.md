# 更新与部署说明

在线字体实验台 · 逐字体覆盖比较 + 浏览器内本地字体导入。

- 线上地址：https://fontlab-online.netlify.app
- 源码仓库：https://github.com/vryTfmcat/fontlab-online
- 许可与署名：https://fontlab-online.netlify.app/licenses.html

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

然后用浏览器打开它给出的地址。注意：**直接双击 `index.html` 打开不行** ——
浏览器的本地文件协议会拦截字体文件的读取，字体无法加载。线上访问不受此限制。

## 部署

线上地址：**https://fontlab-online.netlify.app**（Netlify 项目 `fontlab-online`，免费套餐）

更新内容后：

```
git add -A
git commit -m "update"
git push
netlify deploy --prod --dir=.     # 在本目录执行
```

目前站点是 CLI 部署的，**还没有绑定 Git 自动部署**。想接上（一次性操作）：
Netlify → 本项目 → Project configuration → Build & deploy → Continuous deployment → Link repository，
选 `vryTfmcat/fontlab-online`，构建命令留空、发布目录填 `.`；之后 `git push` 就会自动重新发布。

## 重新生成与验收（脚本在交付目录 `07_字体盘点/_scripts/`，不在本仓库内）

```
python _scripts/publish_nametable.py   # 读取公开字体 name 表里的版权 / 许可声明
python _scripts/publish_build.py       # 复制字体 + 生成 data/ licenses/ licenses.html netlify.toml
node   _scripts/publish_verify.js      # 真实 Chrome 验收：73 项断言
```

`publish_build.py` 会逐文件校验复制后的 SHA-256 与源文件一致，不一致直接中止。
生成脚本只读源字体目录，不会修改任何原始字体文件。

