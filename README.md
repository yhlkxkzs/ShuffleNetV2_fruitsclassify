# ShuffleNetV2_fruitsclassify

GitHub 侧用于存放**水果分类**推理权重（**ShuffleNet V2**，默认 variant **x1_0**），供 Actions 或本地脚本拉取使用。

**移动端（App）对接说明**（OAuth、Contents API、与 Actions 配合）：见 **[docs/MOBILE_APP_INTEGRATION.md](docs/MOBILE_APP_INTEGRATION.md)**。  
**全流程经验总结**：见 **[docs/EXPERIENCE_GITHUB_SHUFFLENET_V2.md](docs/EXPERIENCE_GITHUB_SHUFFLENET_V2.md)**。  
若需 **Actions 将 `incoming/` 新图 POST 到自建 HTTPS 服务**，在仓库 **Settings → Secrets** 中配置 `FRUIT_SERVER_UPLOAD_URL`（及可选 `FRUIT_SERVER_UPLOAD_TOKEN`），详见 `MOBILE_APP_INTEGRATION.md` **§2.1**（与 `mobileNetV3large_backend` 使用相同 Secret 名，便于同一接收端按 `repo` 字段区分来源）。

## 当前权重说明

| 文件 | 说明 |
|------|------|
| `models/shufflenet_fruit_cls_best.pt` | 水果分类 checkpoint（**ShuffleNet V2**，checkpoint 内 `variant` 一般为 `x0_5` 或 `x1_0`） |

- **类别数**：10  
- **类别顺序**：见 `models/classes.json`  
- **来源**：本地训练产物 `runs/shufflenet_v2/weights/best.pt` 复制并重命名。

## 加载示例（PyTorch）

```python
import torch
import torch.nn as nn
from torchvision.models import shufflenet_v2_x1_0, ShuffleNet_V2_X1_0_Weights

ckpt = torch.load("models/shufflenet_fruit_cls_best.pt", map_location="cpu", weights_only=False)
variant = ckpt.get("variant", "x1_0")
# x0_5 时改用 shufflenet_v2_x0_5 与对应 Weights
model = shufflenet_v2_x1_0(weights=ShuffleNet_V2_X1_0_Weights.IMAGENET1K_V1)
model.fc = nn.Linear(model.fc.in_features, ckpt["num_classes"])
model.load_state_dict(ckpt["model_state_dict"])
model.eval()
```

## 克隆与推送

```bash
git clone git@github.com:yhlkxkzs/ShuffleNetV2_fruitsclassify.git
```

维护者：

```bash
git remote add origin git@github.com:yhlkxkzs/ShuffleNetV2_fruitsclassify.git
git branch -M main
git push -u origin main
```

## 用 GitHub Actions 识别图片

1. 将图片放入 **`incoming/`** 并 **push**，或 **Actions → Run workflow**。  
2. 打开 **Fruit classification (ShuffleNet V2)** 运行记录 → **Artifacts** 下载 `predictions`，或查看 **Summary**。

### 本地推理

```bash
pip install torch torchvision pillow
python scripts/infer_fruit.py
```

默认输出：`output/predictions.json`（已 `.gitignore`）。
