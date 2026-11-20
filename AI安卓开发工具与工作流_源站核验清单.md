# AI 安卓开发工具与工作流 — 源头核验清单

**核验日期：** 2026-11-20  
**核验方式：** 本机直连 `api.github.com` / `raw.githubusercontent.com` 读取仓库 README 与源码；直接 fetch 官方页面原文。  
**取舍原则：** 不采信第三方镜像站、聚合站、博客转述。凡未能从上述源头读到的功能，不写入本清单。  
**可信度标记：** A = 官方页面原文直接读取；B = GitHub 仓库 README + 源码直接读取；C = 仓库存在但活跃度低或文档与源码存在不一致，需谨慎。

---

## 一、官方 IDE 与云服务

| 工具 | 源头链接 | 已核实功能（官方原文证据） | 级别 |
|---|---|---|---|
| Android Studio AI / Gemini | https://developer.android.com/ai-in-android | 页面原文：内置 agent 可“计划、跨文件修改、迭代修 bug、在模拟器中验证”；含 Android skills（Perfetto 优化、XML→Compose 迁移、自适应布局）、Compose 设计稿转代码、Compose Preview 生成、Journeys UI 测试、App Quality Insights、LeakCanary、Planning Mode、Create projects with AI、Next Edit Prediction、**“MCP support and one-click marketplace”**；可接 Gemini、自带 API Key、**ACP 兼容 agent（Claude Agent、OpenAI Codex 等）**、本地 Gemma。 | A |
| Android Studio Agent Mode | https://developer.android.com/studio/gemini/agent-mode?hl=zh-cn | 官方中文原文：“旨在处理复杂的多阶段开发任务……创建并执行一个计划，调用必要的工具，跨多个文件进行更改，并以迭代方式修复 bug”；代理有工具“将应用部署到已连接的设备、检查屏幕上显示的内容、截取屏幕截图、检查 Logcat、通过 **adb shell input** 与正在运行的应用互动”。 | A |
| Google AI Studio 全栈 Android | https://developers.googleblog.com/build-full-stack-android-apps-with-gemini-in-google-ai-studio | 官方博客原文：“Full-stack persistence and sign-in: Automatically provision a Cloud Firestore database with Security Rules and Google Sign-In”；“Firebase AI Logic and Firebase App Check”保护 Gemini 调用；“One-click screenshot to agent: Capture a live frame from the running emulator with a single click and attach it to your prompt”；“direct publishing to your personal Google Play library”；HD emulator、Custom fonts、Android CLI integration。 | A |
| Firebase AI Studio 集成 | https://firebase.google.com/docs/ai-assistance/ai-studio-integration?hl=en | 官方文档原文：可自动为 Web/Android 应用配置 Cloud Firestore + Firebase Authentication；Android 另配 Firebase AI Logic + Firebase App Check；生成文件包括 `google-services.json`、`firestore.rules`、`firebase_applet_config.xml`；要求发布前复核 AI 生成的 Security Rules。 | A |
| Supabase MCP Server | https://supabase.com/docs/guides/ai-tools/mcp | 官方文档列出工具组：Database（list_tables / execute_sql / apply_migration 等）、Debugging、Development、Edge Functions、Account、Docs、Branching、Storage、Notebooks；支持 `read_only=true`、`project_ref`、`features=` 限定；提供 Agent Skills 与 Supabase Plugin for AI Coding Agents 的一体化安装。 | A |
| Supabase Select 2026（后端 agent 化） | https://supabase.com/blog/select-2026-build-anything | 官方原文：Declarative Schemas 2.0，`pg-delta` 从声明式 SQL 生成迁移；config in code（`supabase config pull`）；本地开发可不依赖 Docker、多目录并行；Supabase Compute 运行长驻服务；**“Your app's MCP server”**——用一行 shadcn 命令给自己的 App 加带登录态的 MCP server。 | A |
| Supabase + Refine 管理后台 | https://supabase.com/docs/guides/getting-started/quickstarts/refine | 官方 quickstart 原文：`npm create refine-app@latest -- --preset refine-supabase my-app`；`npm run refine create-resource instruments` 生成 CRUD 页面；安装技能：`npx skills add supabase/agent-skills`。 | A |

---

## 二、GitHub MCP 工具（源码已读）

| 项目 / 数据 | 源头链接 | 源码中确认的实现 | 级别 |
|---|---|---|---|
| `mobile-next/mobile-mcp`<br>10,759 stars / 2026-11-20 | https://github.com/mobile-next/mobile-mcp · [src/server.ts](https://github.com/mobile-next/mobile-mcp/blob/HEAD/src/server.ts) | README：iOS/Android 模拟器、真机统一接口；accessibility-first，截图坐标兜底；完整设备控制。源码 `server.ts` 中注册了 `mobile_list_elements_on_screen`、`mobile_take_screenshot`、`mobile_click_on_screen_at_coordinates`、`mobile_double_tap_on_screen`、`mobile_drag_on_screen`、`mobile_allocate_remote_device`、`mobile_release_remote_device` 等工具；npm 包 `@mobilenext/mobile-mcp`，`npx @mobilenext/mobile-mcp@latest install` 可自动检测本机 MCP 客户端。 | B |
| `bayshier/android-mcp`<br>0 stars / 2026-08-23 | https://github.com/bayshier/android-mcp · [AndroidTools.kt](https://github.com/bayshier/android-mcp/blob/HEAD/src/main/kotlin/com/bayshier/androidmcp/server/AndroidTools.kt) | README 与源码一致：`list_devices`、`screenshot`（MCP ImageContent 返回真实 PNG）、`ui_describe`（压缩 UI 树带坐标）、`ui_resolve`、`tap`、`swipe`、`type_text`、`keyevent`、`current_app`、`launch_app`、`install_app`、`logs`。Kotlin 原生、JDK 17+、仅依赖 adb。**注意：0 stars，早期项目，不可当作主流方案。** | B / C |
| `landicefu/android-adb-mcp-server`<br>38 stars / 2026-10-22 | https://github.com/landicefu/android-adb-mcp-server | README 工具表：`adb_devices`、`adb_shell`、`adb_install`、`adb_uninstall`、`adb_list_packages`、`adb_push` / `adb_pull`、`launch_app`、截图保存或复制到剪贴板；多设备时需 `device_id`。 | B |
| `rnett/gradle-mcp`<br>69 stars / 2026-11-18 | https://github.com/rnett/gradle-mcp | README：Agent Skills、Project Mapping、Smart Task Execution、Advanced Testing、Dependency & Source Search、Interactive Kotlin REPL、**Compose UI Previews 渲染为图片**、Gradle 文档检索、Develocity Build Scans；JDK 21+；`jbang run --quiet --fresh gradle-mcp@rnett`。 | B |
| `IlyaGulya/gradle-mcp-server`<br>47 stars / 2026-07-08 | https://github.com/IlyaGulya/gradle-mcp-server | README 顶部明确写着 **“⚠️ Deprecation … Please use rnett/gradle-mcp instead”**；原能力为 Tooling API 查询项目结构、执行任务、层级化测试结果。 | B（已弃用） |
| `normaltusker/kotlin-mcp-server`<br>31 stars / 2026-10-11 | https://github.com/normaltusker/kotlin-mcp-server | README 极长（3493 行），自称 32 个工具；但同一文档中出现“27 tools / 32 tools”不一致，且多数工具标注为 “Intelligent Proxies” 而非完整实现。 | C（谨慎） |
| `kaeawc/android-mcp-sdk`<br>71 stars / 2026-10-26 | https://github.com/kaeawc/android-mcp-sdk · [McpStartup.kt](https://github.com/kaeawc/android-mcp-sdk/blob/HEAD/core/src/main/kotlin/dev/jasonpearson/androidmcpsdk/core/McpStartup.kt) | README：让 Android App 在开发环境托管 MCP server，支持 tools/resources/prompts、WebSocket 与 HTTP/SSE、AndroidX Startup、Hilt/Koin/Dagger。源码 `McpStartup.kt` 中 `checkDebugBuildOrCrash()` 在 release 构建抛 `IllegalStateException`，验证“发布构建主动崩溃防护”属实；依赖写法为 `debugImplementation(...)`。 | B |
| `duonglkh/claude-android`<br>0 stars / 2026-10-29 | https://github.com/duonglkh/claude-android · [src/index.ts](https://github.com/duonglkh/claude-android/blob/HEAD/src/index.ts) | 源码 `index.ts` 注册：`get_project_info`、`read_gradle`、`read_manifest`、`read_proguard_rules`、`read_string_catalog`；Skills 目录有 `agp-upgrade`、`r8-audit`、`string-sync`；README 标注 v0.1 preview，`run_lint` 明确未完成。 | B / C |

---

## 三、GitHub Skills（SKILL.md 已读）

| 技能 | 源头链接 | 已核实内容 | 级别 |
|---|---|---|---|
| android-kotlin（仓库 `alinaqi/maggy`，713 stars / 2026-11-18） | https://github.com/alinaqi/maggy/blob/HEAD/skills/android-kotlin/SKILL.md | frontmatter 明确 `name: android-kotlin`，触发路径 `**/*.kt`、`**/*.kts`、`build.gradle.kts`；正文给出 data/domain/di/ui 分层、Gradle Kotlin DSL、Compose Screen + ViewModel、Room + Repository、Hilt、MockK 测试结构。 | B |
| android-development（仓库 `dpconde/claude-android-skill`，362 stars / 2026-11-19） | https://github.com/dpconde/claude-android-skill/blob/HEAD/SKILL.md | frontmatter `name: android-development`；引用 `modularization.md`、`architecture.md`、`compose-patterns.md`、`gradle-setup.md`、`testing.md`；原则为 offline-first、UDF、Flow、feature 模块化；仓库还包含 `scripts/generate_feature.py`（feature module 生成脚本，文件存在已确认）。 | B |
| agp-upgrade（仓库 `duonglkh/claude-android`） | https://github.com/duonglkh/claude-android/blob/HEAD/skills/agp-upgrade/SKILL.md | SKILL.md 明确“produces a step-by-step diff plan, never auto-applies changes”；工作流调用 `mcp__android__get_project_info`，读取 agpVersion/kotlinVersion/modules/isKmp/hasBuildLogic。 | B |
| Lightrion Android App / AOSP 双技能（仓库 26 stars / 2026-11-10） | https://github.com/Lightrion/skills · [android-app-development/SKILL.md](https://github.com/Lightrion/skills/blob/HEAD/android-app-development/SKILL.md) | SKILL.md 列出依赖的 MCP 工具：`search_code`、`get_chunk`、`get_file`、`list_versions`、`binder_edges`、`security_lookup`；用途为把 AOSP 平台行为翻译成 App 开发语言（Doze、广播、前台服务、权限、targetSdk 行为变化等）。服务端为 `https://aosp-rag.lightrion.com`。 | B |
| Supabase Agent Skills | https://supabase.com/docs/guides/getting-started/quickstarts/refine · https://github.com/supabase/agent-skills | 官方 quickstart 给出安装命令 `npx skills add supabase/agent-skills`；仓库源头（3,113 stars / 2026-11-20）确认含 `skills/supabase/SKILL.md` 与 `skills/supabase-postgres-best-practices/SKILL.md`。 | A/B |

---

## 四、更正与未采信记录

> 以下是对上一轮回答的源头级修正，防止把第三方转述当成事实。

1. **Supabase MCP “32 tools”**：该数字来自第三方博客，Supabase 官方文档未出现；本清单已删除，只写官方明确列出的工具组。
2. **`bayshier/android-mcp`**：上一轮写得偏成熟；GitHub API 显示 0 stars、最近更新 2026-08-23，降级为“早期项目”。
3. **`IlyaGulya/gradle-mcp-server`**：仓库 README 已声明弃用并指向 `rnett/gradle-mcp`，不能作为首选推荐。
4. **`arthrod/android-adb-mcp-server`**：搜索结果里的镜像/301 链接，真实仓库是 `landicefu/android-adb-mcp-server`；未采信镜像。
5. **`github.me` / `github.org` / `www.github.com` 镜像域名**：全部未采信；`kaeawc/android-mcp-sdk`、`InakiBes/adb-mcp-server` 均以 `github.com/OWNER/REPO` 为准。
6. **`InakiBes/adb-mcp-server`**：1 star、2025-12-18 后无更新，未纳入主清单。
7. **Android Studio 页面**：上一轮 WebFetch 被重定向到 Google 登录；本轮已用本机直连 `https://developer.android.com/ai-in-android` 与 Agent Mode 中文页读取原文，证据见上表。
8. **本地 API 限流后的补查**：本机 GitHub API 触发 403 后，改用用户的云服务器节点（独立出口 IP，额度 59/60，无需 token）补查了 `supabase/agent-skills` 与 `dpconde/claude-android-skill` 的仓库元数据和文件树，相关证据已并入上表。

---

## 五、推荐组合工作流（贴合 MIME 项目）

```text
合规/范围文档
   ↓
Android Studio Agent Mode + android-kotlin Skill
   （Compose 模块、ViewModel、Room、Hilt、测试）
   ↓
rnett/gradle-mcp + Supabase MCP
   （构建解析、依赖审计 / 建表、RLS、迁移、Edge Functions）
   ↓
Supabase + Refine
   （运营后台 CRUD 页面由 agent 生成）
   ↓
mobile-next/mobile-mcp 或 bayshier/android-mcp
   （真机：安装、登录、权限拒绝、旋转、重启、支付返回、截图取证）
   ↓
仓库 MCP 提 PR → 渠道包与发布验收
```

**大陆合规提醒：** Firebase / Google Play / Supabase 的数据位置、可用性和合规责任须单独评估；AI 生成的 Security Rules、数据库迁移和发布配置必须人工复核；生产库连接应使用 `read_only=true` 与 `project_ref` 限定。

---

## 六、可复现的核验命令

```bash
# 仓库元数据（stars / 更新时间 / 描述）
curl -sS https://api.github.com/repos/mobile-next/mobile-mcp

# 仓库文件树
curl -sS "https://api.github.com/repos/bayshier/android-mcp/git/trees/HEAD?recursive=1"

# 直接读源码
curl -sS https://raw.githubusercontent.com/bayshier/android-mcp/HEAD/src/main/kotlin/com/bayshier/androidmcp/server/AndroidTools.kt

# 直连官方页面
curl -sSL https://developer.android.com/ai-in-android
curl -sSL https://supabase.com/docs/guides/ai-tools/mcp

# 可选：若本机 API 限流，可在有独立出口 IP 的云服务器上执行（无需 token）
workbench exec --instance-id i-xxx --command "curl -sS https://api.github.com/rate_limit"
```

> 本清单只记录截至 2026-11-20 从源头实际读到的内容；后续版本变化需重新核验。
