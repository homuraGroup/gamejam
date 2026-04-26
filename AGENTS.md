# AGENTS.md

本项目是 RPG Maker MV 工程。代码、剧情、地图和资源都以生成文件为主，多人同时编辑时最容易在 `data/*.json`、`js/plugins.js`、`save/*.rpgsave` 上冲突。所有 Agent 和开发者在改动前先读本文件。

## 项目结构

- `Game.rpgproject`：RPG Maker MV 工程入口。
- `index.html`、`package.json`：NW.js/RPG Maker 运行入口。
- `js/rpg_*.js`、`js/libs/*`：RPG Maker MV 引擎和第三方库，默认不要改。
- `js/plugins/*.js`：插件源码，可按插件文件分工。
- `js/plugins.js`：RPG Maker 自动生成的插件启用与参数表，高冲突文件。
- `data/*.json`：数据库、地图、事件和系统配置，高冲突文件。
- `data/MapNNN.json`：单张地图数据，是地图协作的最小归属单位。
- `data/MapInfos.json`：地图树、名称、顺序和展开状态，高冲突文件。
- `character/*.json`：角色/剧情相关自定义数据。
- `img/*`、`audio/*`、`fonts/*`、`icon/*`：美术、音频和字体资源。
- `save/*.rpgsave`：本地运行存档，不应参与协作提交。

## 本地运行与校验

- 直接用 RPG Maker MV 打开 `Game.rpgproject` 进行编辑。
- 浏览器预览可打开 `index.html`，完整运行建议使用 RPG Maker MV/NW.js。
- 改完 JSON 后至少执行一次 JSON 解析校验：

```bash
node -e "const fs=require('fs'); for (const f of fs.readdirSync('data').filter(f=>f.endsWith('.json'))) JSON.parse(fs.readFileSync('data/'+f,'utf8')); console.log('data json ok')"
```

- 提交前检查实际改动：

```bash
git status --short
git diff --name-only
```

## 协作总原则

- 每个任务先声明文件归属，再开始编辑。没有归属就不要同时打开 RPG Maker 改同一批数据。
- 地图按 `data/MapNNN.json` 分工，同一时间一张地图只能有一个负责人。
- 数据库按功能分工，同一时间同一个数据库文件只能有一个负责人，例如 `Actors.json`、`Items.json`、`Skills.json`。
- 需要改 `CommonEvents.json`、`System.json`、`MapInfos.json`、`js/plugins.js` 时，先在群里锁文件并说明预计改什么。
- 不要提交运行存档、`.DS_Store`、临时导出文件或只由本地环境产生的变化。
- 使用 RPG Maker 后，一定用 `git diff --name-only` 检查它是否顺手改了无关 JSON。无关文件不要提交。

## 推荐分工边界

### 地图与事件

以地图文件为边界分工：

- `Map001`：教室 魔女化-第一次轮回
- `Map002`：第1次轮回
- `Map003`：天台-第一次轮回
- `Map004`：第n次轮回
- `Map005`：序幕
- `Map006`：走廊-第一次轮回
- `Map007`：城市-第一次轮回
- `Map008`：保健室-第一次轮回
- `Map009`：教室-第一次轮回
- `Map010`：军事设施
- `Map011`：学姐战斗场景
- `Map012`：蓝毛战斗场景
- `Map013`：魔女之夜战斗场景
- `Map014`：教室-多次轮回
- `Map015`：教室 魔女化-多次轮回
- `Map016`：保健室-多次轮回
- `Map017`：学校外-第一次轮回
- `Map018`：城市-多次轮回
- `Map019`：走廊-多次轮回
- `Map020`：学校外-多次轮回
- `Map021`：去天台的路
- `Map022`：魔女之夜战斗场景
- `Map023`：教室-有枪
- `Map024`：蒙太奇
- `Map025`：学校外-蒙太奇
- `Map026`：魔女之夜-蒙太奇
- `Map027`：天台-蒙太奇
- `Map028`：城市-战斗胜利

地图负责人可以改自己的 `data/MapNNN.json` 和该地图新增引用的资源文件。跨地图公共事件、变量、开关、物品、技能等必须单独声明。

### 数据库

- 角色、职业、敌人、队伍：`Actors.json`、`Classes.json`、`Enemies.json`、`Troops.json`
- 战斗数值与技能：`Skills.json`、`States.json`、`Weapons.json`、`Armors.json`
- 道具与掉落：`Items.json`
- 公共事件：`CommonEvents.json`
- 图块和系统设置：`Tilesets.json`、`System.json`

数据库文件冲突通常不适合手动猜着合并。多人需要改同一个数据库时，先排队，前一个人合并后下一个人再基于最新分支继续。

### 插件

- 新插件放在 `js/plugins/` 下，一个插件一个文件。
- 开关插件或改插件参数会修改 `js/plugins.js`，需要提前锁文件。
- 不要直接改 `js/rpg_core.js`、`js/rpg_objects.js`、`js/rpg_scenes.js`、`js/rpg_sprites.js`、`js/rpg_windows.js`、`js/rpg_managers.js`。需要改引擎行为时，优先新增插件覆盖。

### 资源

- 新图片、音频、字体用语义化英文或拼音文件名，避免同名覆盖。
- 替换已有资源前先确认没有其他分支正在使用同一路径。
- 音频和图片是二进制文件，Git 冲突无法可靠合并。多人不要同时改同一个资源文件。

## 分支与提交流程

1. 从最新主线创建短分支，例如 `feature/map-009-classroom-event`、`feature/skill-balance-sayaka`。
2. 开始前在群里声明：分支名、负责人、预计修改文件、是否需要锁高冲突文件。
3. 每次打开 RPG Maker 前先同步最新代码。
4. 每次编辑后立刻看 `git status --short`，只保留本任务文件。
5. 提交粒度按功能拆分，不要把地图、插件、素材大杂烩放进一个提交。
6. 合并前 rebase 到最新主线，并重新跑一遍游戏入口或关键场景。

推荐提交说明格式：

```text
type(scope): summary

例如：
feat(map009): add first-loop classroom event
fix(plugin): correct weapon skill trigger
asset(audio): add montage bgm
```

## 高冲突文件锁定规则

以下文件改动前必须先声明并等待确认：

- `data/CommonEvents.json`
- `data/System.json`
- `data/MapInfos.json`
- `data/Tilesets.json`
- `js/plugins.js`
- `Game.rpgproject`
- 任意已有二进制资源文件

锁文件声明模板：

```text
锁文件：data/CommonEvents.json
目的：新增结局判定公共事件
预计时间：今天 20:00 前
相关地图/系统：Map024, Map028
```

解锁时说明：

```text
已解锁：data/CommonEvents.json
分支/提交：feature/ending-common-event / abc1234
需要其他人注意：新增开关 41，变量 18
```

## 变量、开关和公共事件

- 新增变量、开关、公共事件前先登记用途和负责人。
- 不要复用名字不明确的空位，先确认没有剧情或地图事件隐式依赖。
- 命名要带模块或剧情段，例如 `Loop1_Classroom_SeenIntro`、`Montage_ResultScore`。
- 改公共事件时，列出受影响地图和触发条件。

建议维护一张外部共享表，字段至少包括：

- 类型：开关/变量/公共事件
- ID
- 名称
- 用途
- 负责人
- 关联地图
- 当前状态：使用中/废弃/待迁移

## 冲突处理

- `data/MapNNN.json` 冲突：找地图负责人处理。非负责人不要手动合并事件页。
- `data/MapInfos.json` 冲突：优先保留地图树结构和名称的真实改动，去掉仅由编辑器视图滚动位置产生的变化。
- 数据库 JSON 冲突：由文件锁持有人处理。无法确认时，回到 RPG Maker 里基于最新主线重新录入。
- 二进制资源冲突：不能合并，只能选一个版本或换新文件名。
- `save/*.rpgsave` 或 `.DS_Store` 冲突：不应提交。若已经被跟踪，统一执行 `git rm --cached .DS_Store save/*.rpgsave` 后提交一次清理。

## Agent 工作规则

- 改代码前先读 `AGENTS.md`、`package.json`、相关 `data/MapInfos.json` 和目标文件。
- 不要格式化整个 `data` 目录。RPG Maker 生成 JSON 的格式应尽量保持原样。
- 不要批量重排 JSON key、地图事件数组或插件参数。
- 不要改与任务无关的地图、数据库、资源和存档。
- 遇到已存在的未提交改动，先判断是否属于当前任务；不属于当前任务就绕开，不要回滚。
- 提交前必须说明改了哪些高冲突文件，以及如何验证。

