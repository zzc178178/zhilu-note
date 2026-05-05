# RAG笔记图解页签组件分析（目标参照）

## 一、容器结构

| 层级 | CSS 类/元素 | 说明 |
|---|---|---|
| 外层包裹 | `#tab-diagram` | 图解页签容器 |
| 模块 | `.diagram-section` | 每个图解模块的白色卡片 |
| 模块标题 | `h3` | 带 emoji 前缀（如 🏗️📦🎯） |
| 模块描述 | `.diagram-desc` | 灰色说明文字 |

---

## 二、内容组件（6种布局模式）

### 模式① Mermaid flowchart TB（纵向流程图）

适用：复杂路由/多分支流程

```html
<div class="diagram-section">
  <h3>🏗️ RAG系统整体架构</h3>
  <div class="diagram-desc">...</div>
  <div class="mermaid">
flowchart TB
    A --> B
    B --> C
  </div>
</div>
```

关键特征：
- `flowchart TB` 纵向布局
- 多条路由分支（4条以上）
- `-.->` 虚线连接附属资源
- 循环回路

---

### 模式② 纯CSS三列网格卡片（无大图标）

适用：并列的三个方案/概念/要点

```html
<div class="diagram-section">
  <h3>📦 数据准备：...</h3>
  <div class="diagram-desc">...</div>
  <div style="display:grid;grid-template-columns:1fr 1fr 1fr;gap:12px;margin-top:8px;">
    <div class="diagram-card" style="background:#e8f0fe;padding:16px;border:1px solid #d0def5;">
      <div style="font-size:0.7rem;font-weight:700;color:#667eea;text-transform:uppercase;letter-spacing:0.5px;">📄 小标签</div>
      <div style="font-weight:700;font-size:1rem;margin:6px 0;color:#1a2332;">标题</div>
      <div style="font-size:0.85rem;color:#4a5a72;">描述文字</div>
    </div>
    <!-- 重复 ×3 -->
  </div>
  <div style="margin-top:12px;padding:10px 14px;background:#e6f7ed;border-radius:10px;font-size:0.85rem;color:#1a8c4a;">
    <strong>📊 效果：</strong>...
  </div>
</div>
```

卡片结构：
- `.diagram-card` 基座
- 标签行：`font-size:0.7rem; font-weight:700; color:#667eea; text-transform:uppercase`
- 标题行：`font-weight:700; font-size:1rem; color:#1a2332`
- 描述行：`font-size:0.85rem; color:#4a5a72`

---

### 模式③ 纯CSS三列网格卡片（带大图标+边框）

适用：三种方法的对比，需要视觉锤

```html
<div class="diagram-section">
  <h3>🎯 混合检索：...</h3>
  <div class="diagram-desc">...</div>
  <div style="display:grid;grid-template-columns:1fr 1fr 1fr;gap:12px;margin-top:8px;">
    <div class="diagram-card" style="background:#e8f0fe;padding:16px;border:2px solid #667eea;text-align:center;">
      <div style="font-size:0.7rem;font-weight:700;color:#667eea;text-transform:uppercase;letter-spacing:0.5px;margin-bottom:4px;">方法一</div>
      <div style="font-size:1.8rem;margin:4px 0;">🧮</div>
      <div style="font-weight:700;font-size:1rem;color:#1a2332;">向量检索</div>
      <div style="font-size:0.8rem;color:#4a5a72;margin:4px 0;">BGE-large-zh模型</div>
      <div style="font-size:0.78rem;color:#5a6a7e;">负责语义相似度匹配</div>
    </div>
    <!-- 重复 ×3 -->
  </div>
  <!-- 底部标签行 -->
  <div style="display:flex;align-items:center;justify-content:center;gap:8px;margin:14px 0 6px;">
    <span style="font-size:0.85rem;color:#5a6a7e;">标签项</span>
    <span style="color:#d0d5dd;">|</span>
    <span style="font-size:0.85rem;color:#5a6a7e;">分隔</span>
  </div>
  <!-- 底部效果条 -->
  <div style="margin-top:10px;padding:10px 14px;background:#e6f7ed;border-radius:10px;font-size:0.85rem;color:#1a8c4a;">
    <strong>📊 效果：</strong>...
  </div>
</div>
```

新特征（相比模式②）：
- `border:2px solid` 加粗边框
- `text-align:center` 居中布局
- **大 emoji** `font-size:1.8rem`
- **方法编号**标签
- 底部标签信息条（`display:flex` + `|` 分隔）

---

### 模式④ 纯CSS纵向流程卡片（带序号圆形标记）

适用：多步骤递进流程/阶梯方案

```html
<div class="diagram-section">
  <h3>🧠 Query理解：三级意图识别方案</h3>
  <div class="diagram-desc">...</div>
  <div style="display:flex;flex-direction:column;gap:10px;margin-top:8px;">
    <div class="diagram-card" style="display:flex;align-items:center;gap:12px;background:#e8f0fe;padding:14px 18px;border:1px solid #d0def5;">
      <div style="width:32px;height:32px;border-radius:50%;background:#667eea;color:#fff;display:flex;align-items:center;justify-content:center;font-weight:700;font-size:0.9rem;flex-shrink:0;">1</div>
      <div style="flex:1;">
        <div style="font-weight:700;color:#1a2332;font-size:0.95rem;">第一级 · 规则匹配 <span style="font-size:0.75rem;color:#667eea;font-weight:600;">⚡ 零延迟</span></div>
        <div style="font-size:0.85rem;color:#4a5a72;margin-top:2px;">描述文字</div>
      </div>
    </div>
    <!-- 重复 2/3 级，颜色递进 -->
  </div>
  <!-- 底部路由说明条 -->
  <div style="margin-top:12px;padding:10px 14px;background:#f8f9fb;border-radius:10px;border:1px solid #eef0f4;">
    <div style="font-size:0.85rem;color:#4a5a72;text-align:center;">
      <strong>🔀 路由分发：</strong>A → B | C → D
    </div>
  </div>
</div>
```

卡片结构：
- 横向 flex：`display:flex; align-items:center; gap:12px`
- 序号圆：`width:32px;height:32px;border-radius:50%`
- 内容区：`flex:1`，标题行 + 描述行
- 颜色递进：蓝→紫→橙

---

### 模式⑤ Mermaid flowchart LR（横向流程图）

适用：简单线性流程/小规模

```html
<div class="diagram-section">
  <h3>✍️ 生成阶段：双重质量保障</h3>
  <div class="diagram-desc">...</div>
  <div class="mermaid">
flowchart LR
    A --> B
    B --> C
  </div>
</div>
```

关键特征：
- `flowchart LR` 横向
- 节点数少（≤6个）
- 1-2个条件分支

---

### 模式⑥ 纯CSS两列网格卡片（带标签组）

适用：多维度信息分组/工程要点

```html
<div class="diagram-section">
  <h3>⚡ 工程协同：系统性优化</h3>
  <div class="diagram-desc">...</div>
  <div style="display:grid;grid-template-columns:1fr 1fr;gap:10px;margin-top:8px;">
    <div class="diagram-card" style="background:#f8f9fb;padding:14px 16px;border:1px solid #eef0f4;">
      <div style="font-weight:700;font-size:0.95rem;color:#1a2332;margin-bottom:6px;">📤 标题</div>
      <div style="display:flex;gap:6px;flex-wrap:wrap;">
        <span style="background:#e8f0fe;color:#1a5fb4;padding:3px 10px;border-radius:8px;font-size:0.78rem;">标签1</span>
        <span style="background:#e8f0fe;color:#1a5fb4;padding:3px 10px;border-radius:8px;font-size:0.78rem;">标签2</span>
      </div>
    </div>
    <!-- 重复 ×n -->
  </div>
</div>
```

卡片结构：
- 标题 + 标签组
- 标签：`display:inline-block; padding:3px 10px; border-radius:8px; font-size:0.78rem`
- 不同颜色区分类别（蓝/紫/绿/橙）

---

## 三、选型规则总结

| 内容类型 | 使用组件 | 原因 |
|---|---|---|
| 多条路由/复杂分支 | Mermaid flowchart TB | 需要箭头+虚线+循环 |
| 3个并列概念 | 三列卡片网格（模式②） | 并列对比，一眼扫完 |
| 3个方法对比+特征 | 三列卡片网格+大图标（模式③） | 需要视觉锤 |
| 步骤递进/阶梯 | 纵向flex卡片+序号（模式④） | 1→2→3 顺序关系 |
| 简单线性流程 | Mermaid flowchart LR | 节点少，箭头连接即可 |
| 多维信息分组 | 两列网格+标签组（模式⑥） | 分组+标签不占行 |
