# 静态演示站点 - New Study (生理状态研究)

## 📋 与 Flask 5000 端口的完全匹配

此静态站点完全匹配 Flask 应用的 New Study 流程。

### ✅ 生成的文件

```
dist_exact/
├── index.html                  # 入口页
├── consent_new.html           # 新版知情同意书
├── consent_declined.html      # 拒绝同意页
├── baseline_new.html          # 新版基线问卷
├── instructions_blind.html    # 说明页（盲测）
├── attention_test.html        # 注意力测试（从 attention_extreme.html 生成）
├── content_browsing.html      # WESAD 推荐/内容浏览（从 content_browsing_new.html 生成）
├── post_survey.html          # 最终问卷
├── debrief.html              # 结束页
├── hr_monitor.html           # 心率监控（演示）
└── static/                   # CSS/JS/图片资源
```

---

## 🔄 完整流程（匹配 Flask 5000 端口）

### New Study (生理状态研究)

```
1. index.html
   └─ 点击 "Start New Study"
   
2. consent_new.html
   └─ 勾选4个复选框，选择 "Yes, I consent"
   └─ [JS 提交] → baseline_new.html
   
3. baseline_new.html
   └─ 填写基线问卷（PSS-4, 年龄, 性别等）
   └─ [JS 提交] → instructions_blind.html
   
4. instructions_blind.html
   └─ 阅读说明，勾选3个确认框
   └─ 点击 "I understand - Start Session 1"
   └─ [链接] → attention_test.html
   
5. attention_test.html (Session 1)
   ├─ 注意力测试游戏（数字1-9，看到7按空格）
   ├─ 20-40秒后自动触发诱导（Visual: 红屏闪烁）
   ├─ 8秒后自动跳转
   └─ [JS 自动] → content_browsing.html
   
6. content_browsing.html (Session 1)
   ├─ 显示 WESAD 推荐列表（12 items）
   ├─ 可以点击查看详情
   ├─ 点击 "Continue to Next Session"
   └─ [JS] → attention_test.html (Session 2)
   
7. attention_test.html (Session 2)
   ├─ 自动显示 "Session 2 of 3"
   ├─ 条件: RA+Mix (Privacy-Preserving)
   ├─ 诱导方法: Auditory（声音惊吓）
   └─ [JS 自动] → content_browsing.html (Session 2)
   
8. content_browsing.html (Session 2)
   ├─ 显示不同的推荐列表（混合策略）
   └─ [JS] → attention_test.html (Session 3)
   
9. attention_test.html (Session 3)
   ├─ 自动显示 "Session 3 of 3"
   ├─ 条件: State-Agnostic (Maximum Privacy)
   ├─ 诱导方法: Cognitive（社交压力）
   └─ [JS 自动] → content_browsing.html (Session 3)
   
10. content_browsing.html (Session 3)
    ├─ 显示固定推荐列表
    └─ 点击 "Complete Study"
    └─ [JS] → post_survey.html
    
11. post_survey.html
    └─ 填写最终问卷
    └─ [JS 提交] → debrief.html
    
12. debrief.html
    └─ 结束页面，显示完成代码
```

---

## 🎯 三种实验条件

| Session | 条件 | 名称 | 诱导方法 | 推荐策略 |
|---------|------|------|----------|----------|
| 1 | baseline_r | No Privacy Protection | Visual (红屏闪烁) | 纯压力相关 (I1-I6) |
| 2 | ra_mix | Privacy-Preserving (α=0.6) | Auditory (声音惊吓) | 混合策略 (多样化) |
| 3 | state_agnostic | Maximum Privacy | Cognitive (社交压力) | 固定列表 |

---

## 🔧 Session 状态管理

静态页面使用 `SessionManager` JavaScript 对象管理状态：

```javascript
// 在浏览器控制台使用
SessionManager.getState()           // 查看当前状态
SessionManager.nextSession()        // 手动跳到下一session
SessionManager.reset()              // 重置整个流程
```

状态存储在 localStorage 中：
- `participantId`: 随机生成的参与者ID
- `currentSession`: 当前session (1, 2, 3)
- `conditionOrder`: ['baseline_r', 'ra_mix', 'state_agnostic']
- `inductionMethods`: ['visual', 'auditory', 'cognitive']

---

## 🎭 Mock API

所有 Flask API 端点都被 JavaScript Mock：

| API 端点 | Mock 响应 |
|----------|-----------|
| `/api/wesad_recommendations` | 根据条件返回12个WESAD items |
| `/api/log_event` | {status: 'logged', success: true} |
| `/induction_trigger/*` | {status: 'induction_triggered', redirect: 'content_browsing.html'} |
| `/api/session_feedback` | {status: 'success'} |

---

## 🚀 使用方法

### 本地测试
```bash
cd dist_exact
python -m http.server 8000
# 访问 http://localhost:8000
```

### 部署到 GitHub Pages
```bash
cd dist_exact
git init
git add .
git commit -m "Add exact static demo"
git push -f https://github.com/YOUR_USERNAME/music_privacy_prototype.git main:gh-pages
```

访问: `https://YOUR_USERNAME.github.io/music_privacy_prototype`

---

## 📝 论文使用建议

### 描述文字
```markdown
We provide a static demonstration of our New Study interface at 
https://username.github.io/music_privacy_prototype.

The demo replicates the complete 3-session physiological study:
- Session 1: Baseline-R condition with visual stress induction
- Session 2: RA+Mix condition with auditory stress induction  
- Session 3: State-Agnostic condition with cognitive stress induction

Note: This is a frontend demonstration with mocked API responses.
Real-time heart rate monitoring and personalized recommendations 
require Flask backend deployment.
```

### 截图建议
1. **consent_new.html** - 知情同意书（4个checkbox）
2. **baseline_new.html** - 基线问卷
3. **instructions_blind.html** - 说明页（盲测设计）
4. **attention_test.html** - 注意力测试（Session 1）
5. **content_browsing.html** - WESAD推荐（显示3种不同条件）
6. **post_survey.html** - 最终问卷

---

## ⚠️ 与 Flask 5000 端口的差异

| 功能 | Flask 5000 | 静态版本 | 说明 |
|------|------------|----------|------|
| **Session 状态** | 服务端 Session | localStorage | 刷新页面会保留 |
| **推荐算法** | Python 实时计算 | JavaScript Mock | 预定义3种策略 |
| **数据保存** | SQLite 数据库 | 不保存 | Console 日志 only |
| **心率监控** | 真实设备数据 | 模拟数值 | 演示用 |
| **诱导触发** | 基于表现触发 | 固定时间 | 20-40秒随机 |

---

## 🐛 调试

打开浏览器开发者工具 (F12)：
- **Console**: 查看 SessionManager 状态和 API 调用
- **Network**: 查看所有 Mock API 请求
- **Application > Local Storage**: 查看/修改 session 状态

---

## 📊 WESAD 目录 Items

12个推荐项目（来自论文）：

| ID | 名称 | 类别 | 敏感度 |
|----|------|------|--------|
| I1 | Deep Breathing Exercise | wellness | 0.9 |
| I2 | Progressive Muscle Relaxation | wellness | 0.85 |
| I3 | Mindfulness Meditation | wellness | 0.8 |
| I4 | Calm Music Playlist | wellness | 0.75 |
| I5 | Guided Visualization | wellness | 0.7 |
| I6 | Gentle Stretching | wellness | 0.65 |
| I7 | Task Manager Tool | productivity | 0.3 |
| I8 | Email Template Library | productivity | 0.25 |
| I9 | Meeting Scheduler | productivity | 0.2 |
| I10 | Social Media Feed | social | 0.4 |
| I11 | Stress Tracking Dashboard | high_sens | 1.0 |
| I12 | Mood Journal | high_sens | 0.95 |

---

**生成日期**: 2026
**匹配版本**: Flask app.py (New Study flow)
