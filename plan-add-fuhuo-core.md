# 添加 FUHUO.md 到 Core Files 的修复方案

## 问题描述

FUHUO.md 虽然存在于 workspace 中，但不会在 OpenClaw UI 的 "Core Files" 列表中显示。

## 根本原因

在 `/root/clawd/temp-repo/src/gateway/server-methods/agents.ts` 中：

1. ✅ `DEFAULT_FUHUO_FILENAME` 已在 `workspace.ts` 中定义
2. ✅ `DEFAULT_FUHUO_FILENAME` 已被 import 到 `agents.ts`
3. ❌ **但是** `BOOTSTRAP_FILE_NAMES` 数组中没有包含 `DEFAULT_FUHUO_FILENAME`
4. ❌ **导致** `listAgentFiles()` 函数不会列出 FUHUO.md

## 修复方案

### 1. 修改文件

文件：`src/gateway/server-methods/agents.ts`

在第 43 行的 `BOOTSTRAP_FILE_NAMES` 数组中添加：

```typescript
const BOOTSTRAP_FILE_NAMES = [
  DEFAULT_AGENTS_FILENAME,
  DEFAULT_SOUL_FILENAME,
  DEFAULT_TOOLS_FILENAME,
  DEFAULT_IDENTITY_FILENAME,
  DEFAULT_USER_FILENAME,
  DEFAULT_HEARTBEAT_FILENAME,
  DEFAULT_BOOTSTRAP_FILENAME,
  DEFAULT_FUHUO_FILENAME,  // ← 添加这一行
] as const;
```

### 2. 应用补丁

```bash
cd /root/clawd/temp-repo
git apply add-fuhuo-to-core-files.patch
```

### 3. 提交更改

```bash
git add src/gateway/server-methods/agents.ts
git commit -m "feat: add FUHUO.md to Core Files list

- Add DEFAULT_FUHUO_FILENAME to BOOTSTRAP_FILE_NAMES
- FUHUO.md will now appear in the Core Files UI section
- Allows users to view and edit FUHUO.md from the web interface"

git push
```

### 4. 验证

修复后，FUHUO.md 将会：
- ✅ 出现在 "Core Files" 列表中
- ✅ 可以通过 UI 编辑
- ✅ 被自动加载到 Agent 上下文
- ✅ 与其他核心文件（SOUL.md、IDENTITY.md 等）同等地位

## 影响范围

- **修改文件**：1 个 (`src/gateway/server-methods/agents.ts`)
- **新增行数**：1 行
- **风险等级**：低（只是添加文件名到列表）
- **向后兼容**：是（不影响现有功能）

## 测试建议

1. 应用补丁后重启 OpenClaw
2. 打开 Web UI → Agents → Status → Files
3. 确认 FUHUO.md 出现在 "Core Files" 列表中
4. 点击 FUHUO.md，确认可以读取和编辑

## 相关文件

- 补丁文件：`/root/clawd/temp-repo/add-fuhuo-to-core-files.patch`
- 修复文件：`src/gateway/server-methods/agents.ts`
- 常量定义：`src/agents/workspace.ts` (第 30 行)
- UI 显示：`ui/src/ui/views/agents-panels-status-files.ts`

## 时间戳

创建时间：2026-02-14 15:21 UTC
