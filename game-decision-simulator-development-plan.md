# 🎮 Game Decision Simulator 开发计划

> 一个基于真实游戏数据 + 模拟计算 + AI 分析的游戏决策模拟平台。
>
> **核心定位：** 用户输入一个具体游戏场景，选择角色、装备、打法等条件，系统进行多次模拟，并结合最新互联网信息由 AI 对结果进行解释。

---

## 1. 项目定位

### 1.1 核心问题

传统游戏攻略通常是：

> “这个英雄推荐出什么装备？”

本项目希望变成：

> “在我的当前场景下，不同选择分别可能产生什么结果？”

例如：

```text
游戏：英雄联盟
角色：亚索
对手：鳄鱼
等级：6
金币：1000
打法：激进

选择：
A. 多兰剑
B. 十字镐
C. 攻速鞋
```

系统运行模拟后展示：

```text
方案 A
平均经济：...
平均击杀：...
平均死亡：...

方案 B
平均经济：...
平均击杀：...
平均死亡：...
```

最后由 AI 结合游戏数据和最新版本信息解释结果。

---

# 2. MVP 第一版范围

第一版不要做成完整游戏百科，而是只做一个非常具体的场景：

## 「英雄联盟——对线期装备选择模拟器」

### 第一版只支持

- 2 个英雄
- 5 个装备
- 一个固定游戏场景
- 等级 1~10
- 3 种打法
  - 激进
  - 正常
  - 稳健
- 100 / 1000 次模拟

### 两种模式

- **A 手动试玩**：用户每回合亲手选择行动，引擎记录其选择，输出"你的打法倾向"分析（如换血频率 70% ≈ 偏激进）
- **B 自动模拟（主线）**：用户选定打法 + 候选装备，引擎按打法概率自动选择行动，1000 局 Monte Carlo → 各装备胜率对比 → 推荐 + AI 解读
- **教练模式（v2）**：A 的实局表现 vs B 的最优策略对比，AI 给出差异解读

两种模式共用同一个引擎，区别只在"谁做选择"（ActionChooser 接口：策略实现 / 真人实现）。

### 用户输入

```text
英雄
对手
段位（双方水平）
等级
当前金币
当前装备
打法
候选装备
```

### 输出

```text
模拟次数

胜率（核心指标）
推荐装备
平均经济
平均伤害
平均击杀
平均死亡
存活率
关键事件

AI 分析
```

---

# 3. 总体技术架构

```text
                         Internet
                            │
             ┌──────────────┴──────────────┐
             │                             │
       Python Crawler                 AI / Web Search
             │                             │
             ↓                             ↓
       结构化游戏数据                  最新外部信息
             │                             │
             └──────────────┬──────────────┘
                            ↓
                         MySQL
                            │
                            ↓
                    Spring Boot / Java
                            │
                 ┌──────────┴──────────┐
                 ↓                     ↓
          Simulation Engine          AI Service
                 │                     │
                 └──────────┬──────────┘
                            ↓
                         REST API
                            │
                            ↓
                          React
                            │
                            ↓
                         Browser
```

---

# 4. 各技术负责什么

## Python

负责**数据采集和数据处理**：

- Requests
- BeautifulSoup
- Selenium
- 数据清洗
- 数据格式转换
- 定时采集

主要获取：

- 英雄属性
- 装备属性
- 技能数据
- 版本信息
- 历史统计数据

---

## MySQL

负责保存：

- 英雄
- 装备
- 技能
- 游戏版本
- 历史数据
- 模拟记录
- 模拟结果
- 用户数据

---

## Java / Spring Boot

负责整个系统的后端业务：

- REST API
- 用户系统
- 游戏数据查询
- 模拟任务
- 模拟结果
- AI 请求
- 权限
- 数据库访问

---

## Simulation Engine

这是项目的核心。

负责：

```text
GameState
    ↓
计算属性
    ↓
执行行动
    ↓
更新状态
    ↓
重复模拟
    ↓
统计结果
```

例如：

```text
初始状态
↓
角色属性
↓
装备属性
↓
双方决策
↓
伤害计算
↓
生命值变化
↓
击杀 / 死亡
↓
经济变化
↓
下一轮
```

---

## AI

AI 不负责所有计算。

程序负责：

- 伤害计算
- 属性计算
- 经济计算
- 模拟
- 统计

AI 负责：

- 理解用户问题
- 分析模拟结果
- 解释数据
- 总结版本变化
- 搜索最新信息
- 生成策略分析

---

## React

负责：

- 参数选择
- 模拟页面
- 结果展示
- 数据图表
- 历史记录
- AI 分析结果

---

# 5. 开发阶段

> **调整说明**（2026-09-16 讨论决定）：Phase 0 后先做"行走骨架"——手写 JSON 假数据 + 纯 Java 模拟引擎 + JUnit + 控制台输出，先验证模型；A 手动模式（先做控制台版）优先于 B 策略模式实现；其余 Phase 顺序不变。

---

# Phase 0：产品设计

**预计：1 天**

目标：先把项目规则确定下来。

### 任务

- [ ] 确定具体游戏
- [ ] 确定第一版场景
- [ ] 确定用户输入
- [ ] 确定模拟输出
- [ ] 确定哪些数据自己采集
- [ ] 确定哪些信息交给 AI 搜索
- [ ] 设计第一版页面

### 最终产物

```text
产品需求文档
数据字段设计
模拟规则说明
页面草图
```

---

# Phase 1：Python 数据采集

**预计：3~5 天**

目标：

> 能够从真实网站获取游戏数据，并转换成自己的结构化数据。

### 学习 / 使用

- Requests
- BeautifulSoup
- Selenium
- JSON
- 数据清洗
- 异常处理

### 任务

- [ ] 获取英雄列表
- [ ] 获取英雄属性
- [ ] 获取装备数据
- [ ] 获取技能数据
- [ ] 获取版本数据
- [ ] 清洗数据
- [ ] 保存 JSON
- [ ] 编写统一的数据结构

最终：

```text
crawler/
├── hero_crawler.py
├── item_crawler.py
├── patch_crawler.py
├── clean.py
└── main.py

data/
├── heroes.json
├── items.json
└── patches.json
```

---

# Phase 2：MySQL 数据库

**预计：2~3 天**

目标：

> 把 Python 采集的数据正式存入数据库。

### 第一版表

```text
users
heroes
items
skills
patches
hero_stats
simulation_records
simulation_results
```

### 任务

- [ ] 设计 ER 图
- [ ] 创建数据库
- [ ] 创建表
- [ ] 设置主键 / 外键
- [ ] Python 写入 MySQL
- [ ] 测试查询

数据流：

```text
Python
 ↓
清洗
 ↓
MySQL
```

---

# Phase 3：Spring Boot 后端

**预计：5~7 天**

目标：

> 让 Java 成为整个项目的核心业务层。

### 技术

- Spring Boot
- Spring Web
- Spring Data JPA / MyBatis
- MySQL
- REST API
- Maven

### 第一版 API

```text
GET /api/heroes
GET /api/heroes/{id}

GET /api/items
GET /api/items/{id}

POST /api/simulation

GET /api/simulation/{id}

GET /api/simulations
```

### 任务

- [ ] 创建 Spring Boot 项目
- [ ] 连接 MySQL
- [ ] Entity
- [ ] Repository / Mapper
- [ ] Service
- [ ] Controller
- [ ] DTO
- [ ] 异常处理
- [ ] API 测试

最终：

```text
React
 ↓
HTTP
 ↓
Spring Boot
 ↓
MySQL
```

---

# Phase 4：Simulation Engine

**预计：5~10 天**

这是整个项目最重要的阶段。

目标：

> 不依赖 AI，先让程序自己完成可靠的模拟。

## 核心对象

```text
GameState
Player
Enemy
Hero
Item
Action
Simulation
SimulationResult
```

例如：

```text
GameState

HP
Mana
Gold
Level
Attack
Defense
Items
Time
```

---

## 模拟流程

```text
创建 GameState
      ↓
读取英雄数据
      ↓
读取装备数据
      ↓
计算初始属性
      ↓
选择行动
      ↓
计算结果
      ↓
更新 GameState
      ↓
是否结束？
   ↓       ↓
  否       是
  ↓        ↓
继续      保存结果
```

---

## Monte Carlo 模拟

例如：

```text
模拟 1000 次

Simulation 1
Simulation 2
Simulation 3
...
Simulation 1000
```

统计：

```text
平均伤害
平均金币
平均击杀
平均死亡
存活率
```

最终：

```text
SimulationResult
```

---

# Phase 5：React 前端

**预计：4~6 天**

目标：

> 做出一个真正可以使用的网页。

### 页面

```text
/
首页

/simulation
模拟器

/result
模拟结果

/history
历史记录
```

### 模拟器页面

```text
┌───────────────────────────┐
│ 游戏                      │
│ [League of Legends]       │
│                           │
│ 我的英雄                  │
│ [Yasuo]                   │
│                           │
│ 对手                      │
│ [Renekton]                │
│                           │
│ 等级                      │
│ [6]                       │
│                           │
│ 金币                      │
│ [1000]                    │
│                           │
│ 打法                      │
│ ○ 激进 ○ 正常 ○ 稳健     │
│                           │
│ 候选装备                  │
│ □ 多兰剑                  │
│ □ 十字镐                  │
│ □ 攻速鞋                  │
│                           │
│       [开始模拟]          │
└───────────────────────────┘
```

---

# Phase 6：AI 接入

**预计：3~5 天**

目标：

> 让 AI 成为“分析器”，而不是计算器。

## 输入

把模拟结果整理成结构化信息：

```json
{
  "hero": "Yasuo",
  "enemy": "Renekton",
  "level": 6,
  "simulations": 1000,
  "results": [
    {
      "item": "A",
      "avg_damage": 532,
      "avg_gold": 4210,
      "death_rate": 0.31
    }
  ]
}
```

然后提供给 AI。

---

## AI 输出

例如：

```text
当前场景下：

方案 A 的平均伤害较低，但死亡率也较低。

方案 B 的伤害更高，同时风险增加。

根据当前版本信息，XXX 装备最近发生了变化。

因此，在你选择“激进打法”的情况下，
需要重点关注……
```

---

# Phase 7：联网信息融合

**预计：2~4 天**

这一阶段才加入 AI 搜索能力。

### 自己数据库

负责：

```text
结构化数据
历史数据
模拟数据
```

### AI 搜索

负责：

```text
最新版本
官方公告
新闻
社区讨论
机制变化
```

最终：

```text
数据库
   +
最新互联网信息
   ↓
AI
   ↓
分析
```

---

# Phase 8：用户系统

**预计：2~4 天**

加入：

- [ ] 注册
- [ ] 登录
- [ ] JWT
- [ ] 用户信息
- [ ] 模拟历史
- [ ] 收藏场景

用户可以看到：

```text
我的模拟

2026-09-16
亚索 vs 鳄鱼
十字镐
1000 次

2026-09-15
亚索 vs 鳄鱼
攻速鞋
1000 次
```

---

# Phase 9：Redis + 异步任务

**预计：2~4 天**

当模拟次数变多：

```text
1000 次
10000 次
100000 次
```

不适合让 HTTP 请求一直等待。

可以变成：

```text
用户
 ↓
POST /simulation
 ↓
Java
 ↓
Redis / 消息队列
 ↓
Simulation Worker
 ↓
完成
 ↓
保存 MySQL
```

前端查询：

```text
GET /simulation/{id}
```

看到：

```text
模拟中 73%
```

---

# Phase 10：Docker + Nginx + Linux

**预计：3~5 天**

最终部署：

```text
                    Internet
                       │
                     Nginx
                       │
          ┌────────────┴────────────┐
          │                         │
       React                  Spring Boot
                                    │
                         ┌──────────┴──────────┐
                         │                     │
                       MySQL                 Redis
```

Python：

```text
Python Crawler
      ↓
定时执行
      ↓
更新数据库
```

---

# 6. 最终项目结构

最终可以整理成：

```text
game-decision-simulator/
│
├── frontend/
│   ├── src/
│   ├── public/
│   └── package.json
│
├── backend/
│   ├── src/
│   │   └── main/
│   │       ├── controller/
│   │       ├── service/
│   │       ├── repository/
│   │       ├── entity/
│   │       ├── dto/
│   │       └── simulation/
│   └── pom.xml
│
├── crawler/
│   ├── hero_crawler.py
│   ├── item_crawler.py
│   ├── patch_crawler.py
│   ├── clean.py
│   └── main.py
│
├── data/
│   ├── heroes.json
│   └── items.json
│
├── database/
│   ├── schema.sql
│   └── init.sql
│
├── docs/
│   ├── architecture.md
│   ├── api.md
│   └── simulation.md
│
├── docker/
│   ├── Dockerfile.backend
│   ├── Dockerfile.frontend
│   └── docker-compose.yml
│
├── nginx/
│   └── nginx.conf
│
└── README.md
```

---

# 7. 开发顺序总览

```text
                    产品设计
                       ↓
                  Python 爬虫
                       ↓
                    MySQL
                       ↓
                Spring Boot
                       ↓
               Simulation Engine
                       ↓
                    React
                       ↓
                      AI
                       ↓
                  联网信息
                       ↓
                 用户系统
                       ↓
              Redis / 异步任务
                       ↓
             Docker + Nginx
                       ↓
                    Linux
                       ↓
                  公网部署
```

---

# 8. 第一阶段不要做什么

为了防止项目越做越大，MVP 阶段暂时不要做：

- ❌ 游戏百科
- ❌ 全英雄
- ❌ 全装备
- ❌ 多游戏
- ❌ 社区论坛
- ❌ 好友系统
- ❌ 推荐算法
- ❌ 复杂权限
- ❌ 微服务
- ❌ Kubernetes

第一版只解决：

> **“给定一个具体游戏场景，我选择不同方案，系统能不能告诉我这些选择在模拟中的结果？”**

---

# 9. 项目完成后的技术栈

```text
前端
React
JavaScript / TypeScript

后端
Java
Spring Boot
REST API

数据库
MySQL
Redis

数据采集
Python
Requests
BeautifulSoup
Selenium

AI
LLM API
联网搜索
Prompt / Structured Output

部署
Linux
Docker
Nginx
HTTPS

工程能力
Git
GitHub
日志
异常处理
定时任务
异步任务
```

---

# 10. 项目的核心亮点

这个项目最终可以总结成：

> **一个融合实时互联网信息、结构化游戏数据、Monte Carlo 模拟和大语言模型分析的游戏决策模拟平台。**

真正有技术含量的部分不是“接了一个 AI API”，而是：

```text
数据采集
    ↓
数据清洗
    ↓
结构化存储
    ↓
后端业务
    ↓
模拟引擎
    ↓
大量模拟
    ↓
统计结果
    ↓
AI 分析
    ↓
可视化
```

这样 Python、Java、AI、React、MySQL、Linux 就不是几个孤立的技术点，而是共同组成一个完整产品。
