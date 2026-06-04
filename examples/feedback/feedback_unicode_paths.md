---
name: feedback-unicode-paths
description: macOS 混合 Unicode 目录名会导致 Shell/Read/Edit 工具失效，且破坏性操作前必须人工确认
metadata:
  type: feedback
---

macOS 路径中混用两种文字体系（如中文+韩文，或中文+日文）会触发 NFD/NFC 不一致，导致 Shell 命令静默失败、Read/Edit 工具报"File does not exist"。

**Why:** 因此类路径问题，误删过整个项目目录，代码幸好在 GitHub 才恢复。

**How to apply:** 遇到含非 ASCII 的路径时，先用以下命令拿到真实字节路径再操作：

```bash
python3 -c "import os; [print(repr(e.path)) for e in os.scandir(parent_dir)]"
```

路径异常时执行任何删除操作前，必须打印目标列表让用户确认，不得自动执行。
