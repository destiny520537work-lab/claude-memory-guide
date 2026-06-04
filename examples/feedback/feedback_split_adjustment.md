---
name: feedback_split_adjustment
description: WRDS/CRSP历史价格做跨期比较前，必须先做拆股调整，否则涨跌幅严重失真
metadata:
  type: feedback
---

做任何用历史股价跨期比较涨跌幅的分析，**必须先做拆股/分拆调整**，且这是默认前置步骤，不能等错误被别人指出后再补救。

**Why:** 直接用 CRSP `DlyClose`（未调整收盘价）首尾相比——拆股日价格人为跳变（某股票拆股后从$1208→$121），把"峰值→当前"算成假崩盘。多只股票数据被污染，推翻了原报告的核心论断。实际只是温和回调，被错误算成暴跌70-80%。

**How to apply:**
- CRSP 数据用 `DlyRet`（已含拆股+分红调整）累乘 `(1+DlyRet)` 构建总收益指数
- 或用 `prc/cfacpr`、`prccq/ajexdi` 做价格还原
- 分拆（spin-off）与拆股同理，`DlyRet` 同样已正确处理
- 调研阶段就需要考虑到这些因素，不能事后补救
