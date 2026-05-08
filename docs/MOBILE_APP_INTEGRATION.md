# 移动端与 GitHub 水果分类仓库对接说明（ShuffleNet V2）

本文档供 **手机 App 开发** 与 **本仓库（含 GitHub Actions）** 对齐使用：完成「用户授权 → 上传图片到 `incoming/` → 触发自动推理」的集成。

---

## 1. 仓库与约定

| 项 | 值 |
|----|-----|
| 仓库 | `yhlkxkzs/ShuffleNetV2_fruitsclassify` |
| 默认分支 | `main` |
| 图片上传目录 | `incoming/`（可子目录） |
| 权重文件 | `models/shufflenet_fruit_cls_best.pt` |
| 推理 Workflow 名称 | `Fruit classification (ShuffleNet V2)` |
| 工作流文件 | `.github/workflows/infer_fruit.yml` |

**触发条件**：向 **`incoming/**`** 产生 **push**（含 Contents API 提交）后启动 Workflow。

---

## 2. 架构角色

- **App**：GitHub OAuth、`access_token` 存密钥库、**Contents API** 写入 `incoming/`。  
- **GitHub**：托管仓库、Actions 内运行 `scripts/infer_fruit.py`（ShuffleNet V2）。  
- **结果**：**Actions → Artifacts → `predictions`**（`predictions.json`），**Summary** 可能含同内容。

> 分类结果**不会**自动推送到手机；需轮询 API、自建后端或引导浏览器查看。

---

## 2.1 自建服务器接收图片（Actions + `curl` POST）

与 `mobileNetV3large_backend` **相同机制**：可选将本次 `incoming/` 变更图片 **POST** 到你的 HTTPS 接口。

**Secrets**（本仓库单独配置；名称可与 MobileNet 仓一致，便于共用接收端）：

| Secret | 说明 |
|--------|------|
| `FRUIT_SERVER_UPLOAD_URL` | 不配则跳过转发 |
| `FRUIT_SERVER_UPLOAD_TOKEN` | 可选 Bearer |

**表单字段**：`file`、`path`、`commit`、`repo`（本仓库为 `yhlkxkzs/ShuffleNetV2_fruitsclassify`）。  
**要求**：URL **公网 HTTPS** 可达（托管 Runner 无法直连纯内网 IP）。

---

## 3. 服务端 / 产品侧（OAuth App）

与 MobileNet 仓库相同：注册 OAuth App、回调 URL、`public_repo` scope（公开仓）、**禁止**在 App 内硬编码 `client_secret` / PAT。详见 [GitHub OAuth 文档](https://docs.github.com/en/apps/oauth-apps/building-oauth-apps/authorizing-oauth-apps)。

---

## 4. App 侧要点

### 4.1 上传（PUT Contents）

**本仓库** 的 API 路径示例：

```http
PUT https://api.github.com/repos/yhlkxkzs/ShuffleNetV2_fruitsclassify/contents/incoming/{filename}
```

Body：`message`、`content`（Base64）、`branch: main`；更新已存在文件需带 `sha`。

### 4.2 其余流程

授权 URL、`code` 换 token、Keychain 存储、错误处理等与通用 GitHub 移动端集成一致；亦可对照姊妹仓 `mobileNetV3large_backend` 的 `MOBILE_APP_INTEGRATION.md`（将 owner/repo 换成本仓即可）。

---

## 5. 联调检查清单

- [ ] PUT 指向 **`yhlkxkzs/ShuffleNetV2_fruitsclassify`**。  
- [ ] push 后 **Fruit classification (ShuffleNet V2)** 出现新运行。  
- [ ] Artifacts 含 `predictions`（`incoming/` 内有图时）。

---

## 6. 文档版本

与 `main` 分支结构一致；仓库改名时请更新 §1、§4.1。
