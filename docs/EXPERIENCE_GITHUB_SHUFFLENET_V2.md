# 经验总结：ShuffleNet V2 水果分类上 GitHub

本文档与 **`mobileNetV3large_backend`** 中 [EXPERIENCE_GITHUB_MOBILENET_V3.md](https://github.com/yhlkxkzs/mobileNetV3large_backend/blob/main/docs/EXPERIENCE_GITHUB_MOBILENET_V3.md) 结构平行，仅模型与仓库不同。

## 要点

| 项 | 说明 |
|----|------|
| 权重 | `runs/shufflenet_v2/weights/best.pt` → 本仓 `models/shufflenet_fruit_cls_best.pt`（约 5MB） |
| 架构 | **ShuffleNet V2**，checkpoint 含 **`variant`**：`x1_0` 或 `x0_5`，推理脚本自动读取 |
| 类别 | 与 MobileNet 水果实验一致时为 **10 类**，见 `models/classes.json` |
| Actions | Workflow 名 **Fruit classification (ShuffleNet V2)** |
| 手机上传 | OAuth + PUT 到 **`yhlkxkzs/ShuffleNetV2_fruitsclassify`** 的 `incoming/` |
| 可选 POST 服务器 | Secrets：`FRUIT_SERVER_UPLOAD_URL` / `FRUIT_SERVER_UPLOAD_TOKEN`，`repo` 字段区分来源仓 |

## 与 MobileNet 仓的差异

- 推理入口均为 `scripts/infer_fruit.py`，**权重文件名与内部 `load_model` 不同**（ShuffleNet 使用 `model.fc`，非 MobileNet 的 `classifier`）。  
- 两仓可并存：App 内可让用户选择「提交到 MobileNet 仓」或「提交到 ShuffleNet 仓」，对应不同 `owner/repo` 的 Contents API。

## 相关链接

- 移动端细则：**[MOBILE_APP_INTEGRATION.md](./MOBILE_APP_INTEGRATION.md)**  
- 姊妹仓（MobileNet V3 Large）：<https://github.com/yhlkxkzs/mobileNetV3large_backend>
