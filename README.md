# 动中觉察 · 永久跳转器

**永久 URL**：https://wcaca.github.io/atm-redirector/

每次访问这个 URL，会自动跳转到**当前最新**的动中觉察主站（`https://<最新URL>.space.minimaxi.com`）。

## 怎么用

把 `https://wcaca.github.io/atm-redirector/lesson/l1` 收藏到浏览器书签。
以后即使主站 URL 变了，你打开书签还是会自动跳到最新版。

**支持的路径**：
- `/` → 跳到主站首页
- `/lesson/l1` → 跳到主站第 1 课
- `/lesson/l6` → 跳到主站第 6 课
- `/bodyParts` → 跳到按部位浏览
- `/search` → 跳到搜索页
- `/progress` → 跳到我的进度

---

## 每次部署后怎么更新

1. 在 `latest.json` 里把 `url` 字段改成新部署的 URL：
   ```json
   {
     "version": "v4",
     "url": "https://新部署.space.minimaxi.com",
     "note": "新增了什么...",
     "updatedAt": "2026-06-17"
   }
   ```

2. 推送到 GitHub（任选一种方式）：

   **方式 A: Git 命令行（最快）**
   ```bash
   cd /workspace/gh-pages
   # 编辑 latest.json 后
   git add latest.json
   git commit -m "Update to v4"
   git push
   ```

   **方式 B: GitHub API（适合自动化）**
   ```bash
   #!/bin/bash
   CONTENT=$(base64 -w 0 latest.json)
   SHA=$(curl -s -H "Authorization: token $GITHUB_TOKEN" \
     "https://api.github.com/repos/wcaca/atm-redirector/contents/latest.json" | \
     python3 -c "import json,sys; print(json.load(sys.stdin)['sha'])")
   curl -X PUT -H "Authorization: token $GITHUB_TOKEN" \
     -H "Content-Type: application/json" \
     "https://api.github.com/repos/wcaca/atm-redirector/contents/latest.json" \
     -d "{\"message\":\"Update latest.json\",\"content\":\"$CONTENT\",\"sha\":\"$SHA\"}"
   ```

   **方式 C: 浏览器手动改**
   去 https://github.com/wcaca/atm-redirector/edit/main/latest.json 直接改

3. 等 ~30 秒（GitHub Pages 自动重建）

4. 验证：访问 https://wcaca.github.io/atm-redirector/latest.json 看内容是否更新

---

## 进度数据迁移（重要）

用户的练习进度存在**主站域名**的 localStorage 里。换新 URL 后，老进度**不会**自动过来。

**目前的方案**（手动）：
1. 用户在**老主站**打开"我的进度"页面
2. 找到"导出进度"功能（如果没做，加上 `progress.js` 的 export）
3. 拿到 JSON 文件
4. 在**新主站**打开"我的进度"页面
5. 选"导入进度"恢复

**未来可以做的**（如需要）：在 `latest.json` 里加 `?import=base64` 自动迁移。

---

## 备用入口

`https://atm.noteverse.space/` 同样作为入口（DNS 还没配置，等 Cloudflare token 权限修好）。

---

## 故障排查

**访问 /lesson/l1 直接 404？**
- 跳转器被跳到了主站，但主站没这个路径
- 验证 latest.json 的 url 是正确的

**latest.json 修改后没生效？**
- GitHub Pages 重建需要 30 秒左右
- 检查 `https://github.com/wcaca/atm-redirector/actions` 看 build 状态

**跳转后样式错乱？**
- 跳转时丢失了 cookie/storage，正常
- 进度数据存在新主站 localStorage

**GitHub Pages build 一直 errored？**
- 缺 `.nojekyll` 文件
- 加一个空文件即可
