# 简易领地 SimpleClaims

一个面向 **Minecraft Java Edition 26.3 / Fabric** 的轻量服务器领地保护模组。

目标只有一个：**让普通玩家不需要看文档，也能在 30 秒内圈出自己的第一块领地。**

- 完全服务端运行，原版客户端不需要安装任何东西。
- 金铲右键选角 1，左键点对角，领地立即创建。
- 领主和被信任的玩家可以放置/破坏方块、打开容器。
- OP 管理员（原版权限等级 2 及以上）自动拥有所有领地的全部权限，并可管理领地。
- 数据实时保存，服务器重启不丢。

---

## 1. 环境要求

| 项目 | 版本 |
| --- | --- |
| Minecraft Java Edition | **26.3** |
| Fabric Loader | **0.20.1 或更高** |
| Fabric API | **0.165.2+26.3**（对应 26.3 的版本） |
| Java | **25** |

> 本模组构建时使用的实际版本：Minecraft 26.3、Fabric Loader 0.20.1、Fabric API 0.165.2+26.3、Loom 1.19.4、Java 25。

---

## 2. 安装

1. 给服务器安装 Fabric Loader（26.3）。
2. 把下面两个文件放进服务器的 `mods/` 文件夹：
   - `simpleclaims-1.0.0.jar`
   - `fabric-api-0.165.2+26.3.jar`
3. 启动服务器。第一次启动会自动生成：
   - `config/simpleclaims.json`：配置
   - `world/simpleclaims-data.json`：第一次圈地后生成
4. 进入游戏，输入 `/claim`。

客户端不需要安装这个模组；服务器装了就能保护。单人游戏也可以把模组放进客户端 `mods/` 使用。

---

## 3. 三步圈地

1. 输入 `/claim tool`，获得一把**领地工具**（金铲）。
2. **右键**点击一个方块，选择角 1。
3. **左键**点击对角方块，领地**立即创建**。

创建成功后会显示范围、粒子边界和提示。之后：
- 在领地里输入 `/claim trust 玩家名`，允许朋友使用。
- 在领地里输入 `/claim info`，查看领主、范围、信任名单和自己的权限。
- 在领地里输入 `/claim delete`，删除领地（30 秒内输入 `/claim delete confirm` 或点击聊天栏按钮确认）。

> 领地是 **2D 全高**的：只按 X/Z 坐标判定，保护从基岩到天空的整列。每个世界分别计算。
> 默认每人最多 3 块，每块最小 3×3，单边最大 64 格；都可以在配置中改。

---

## 4. 指令一览

所有指令都可以在聊天栏用 Tab 补全。

| 指令 | 作用 |
| --- | --- |
| `/claim` | 查看脚下领地、自己的领地数量和快捷按钮 |
| `/claim help` | 完整帮助 |
| `/claim tool` | 领取金铲（丢失可重复领取） |
| `/claim create` | 用已选的两个角重试创建（通常用不到，左键时自动创建） |
| `/claim info` | 查看脚下领地详情 |
| `/claim list` | 查看自己全部领地 |
| `/claim trust <玩家>` | 在脚下**自己的**领地里信任一名在线玩家 |
| `/claim untrust <玩家>` | 取消信任（支持输入历史名字，不需要在线） |
| `/claim delete` | 删除脚下自己的领地，需二次确认 |
| `/claim delete confirm` | 确认删除，30 秒内有效 |

### 管理员指令（OP 等级 2+）

| 指令 | 作用 |
| --- | --- |
| `/claim admin list [玩家]` | 列出全部领地，或某玩家的领地 |
| `/claim admin info` | 查看脚下领地完整信息（含 ID / UUID） |
| `/claim admin unclaim` | 立即删除脚下的任意领地 |
| `/claim admin delete <玩家>` | 删除某玩家的全部领地，需再加 `confirm` |
| `/claim admin reload` | 重新读取配置文件（不删除已有领地） |

示例：

```text
/claim admin list Steve
/claim admin delete Steve
/claim admin delete Steve confirm
```

---

## 5. 权限规则

| 身份 | 放置/破坏方块 | 打开容器 | 管理信任名单 | 删除领地 |
| --- | --- | --- | --- | --- |
| 领主 | 允许 | 允许 | 允许 | 允许 |
| 被信任玩家 | 允许 | 允许 | 不允许 | 不允许 |
| 其他玩家 | 拒绝 | 拒绝 | 不允许 | 不允许 |
| OP 等级 2+ | 允许（绕过保护） | 允许 | 用管理员指令 | 用管理员指令 |

“管理员”使用原版命令权限等级判定：`op-permission-level` 至少为 2。控制台始终拥有全部权限。

保护为服务器权威判定：即使客户端不装模组、使用快捷操作，也不能绕过。

### 具体保护范围（1.0.0）

- 玩家放置方块（包括潜行放置）。
- 玩家破坏方块。
- 打开带界面的容器：箱子、陷阱箱、木桶、潜影盒、熔炉、高炉、烟熏炉、发射器、投掷器、工作台、铁砧、附魔台、切石机、织布机、制图台、锻造台、酿造台、信标、末影箱等。
- 双箱子跨领地边界时，会同时检查两半所属的领地。
- 领地创建时检查重叠、尺寸和数量上限。

> 1.0.0 **不包含**：爆炸（TNT/苦力怕）、火焰蔓延、活塞推动、实体交互（物品展示框/盔甲架）、漏斗矿车等非玩家直接行为。如果你需要“防炸/防活塞”的完整生存保护，请在配置或后续版本中扩展。当前版本严格覆盖需求中的“玩家放置/破坏方块 + 打开箱子”。

---

## 6. 配置文件

路径：`config/simpleclaims.json`（首次启动自动生成）。

```json
{
  "maxClaimsPerPlayer": 3,
  "minClaimWidth": 3,
  "maxClaimWidth": 64,
  "showEnterMessages": true,
  "showBorderParticles": true,
  "protectContainers": true,
  "joinHint": true
}
```

| 字段 | 默认 | 说明 |
| --- | --- | --- |
| `maxClaimsPerPlayer` | `3` | 每名玩家最多领地数（1–100） |
| `minClaimWidth` | `3` | 最小单边长度（格） |
| `maxClaimWidth` | `64` | 最大单边长度（格） |
| `showEnterMessages` | `true` | 进入/离开领地时的 ActionBar 提示 |
| `showBorderParticles` | `true` | 选点和创建时的粒子边界 |
| `protectContainers` | `true` | 是否保护容器；关闭后只保护方块放置/破坏 |
| `joinHint` | `true` | 玩家进服时的一行提示 |

修改后可以重启服务器，或让管理员执行 `/claim admin reload`。已有领地不会因为缩小上限而被删除。

---

## 7. 数据与备份

- 领地数据：`world/simpleclaims-data.json`
  - 每次创建、信任、取消信任、删除都会立即写入。
  - 采用“临时文件 + 原子替换”，并保留上一版 `simpleclaims-data.json.bak`。
  - 如果文件损坏，服务器会记录日志并备份为 `simpleclaims-data.json.corrupt-<时间戳>`。
- 配置：`config/simpleclaims.json`
- 领地 ID、领主 UUID、信任玩家 UUID 都会保存；玩家改名不会丢失权限。

---

## 8. 常见问题

**Q：朋友不在线，能提前信任吗？**  
A：当前版本要求被信任玩家在线，因为要记录准确的 UUID。让朋友先上线一次即可。

**Q：为什么拿着金铲不能正常挖方块？**  
A：金铲是圈地工具。右键选角 1，左键选角 2；挖方块请切换其他物品。工具丢失可以再次 `/claim tool`。

**Q：管理员为什么能直接破坏我的领地？**  
A：这是设计行为：OP 等级 2+ 拥有所有领地的全部权限，也可以使用 `/claim admin` 管理。

**Q：服务器重启后领地还在吗？**  
A：在。数据实时写入 `world/simpleclaims-data.json`；停止服务器时也会再保存一次。

**Q：支持模组维度吗？**  
A：支持。领地按维度 ID 分开存储，任意 `ServerLevel` 都可以圈地。

**Q：为什么不能打开别人领地里的工作台/熔炉？**  
A：它们属于“带界面的容器/功能方块”，默认一并保护。可以用 `protectContainers: false` 关闭容器保护（方块保护仍然生效）。

---

## 9. 从源码构建

要求 JDK 25。

```bash
./gradlew build
```

Windows：

```bat
gradlew.bat build
```

产物在 `build/libs/simpleclaims-1.0.0.jar`。

项目结构：

```text
src/main/java/cn/simpleclaims/
  SimpleClaims.java              入口、生命周期、配置
  claim/                         领地模型、管理器、JSON 持久化
  command/                       /claim 全部指令与 Tab 补全
  config/                        配置模型与校验
  protection/                    方块/容器保护、选区、权限判定
  mixin/                         服务端 useItemOn 拦截（覆盖潜行放置）
  presence/                      进入/离开领地提示
  ui/                            中文消息与可点击按钮
  util/                          金铲工具、粒子与音效
```

---

## 10. 验证情况

本项目在交付前的实际测试：

- `gradle build` 编译通过；5 个领地几何/权限单元测试全部通过。
- 在 Fabric 26.3 + Loader 0.20.1 开发服务器启动，运行时自测通过：
  - 未信任玩家在领地内放置方块被拒绝；
  - 未信任玩家打开领地内箱子被拒绝；
  - 领主放置方块、打开箱子被允许；
  - 被信任玩家放置方块被允许；
  - 创建/信任/删除后数据正确写入，重启可读回。
- 使用构建出的 jar（不是开发类路径）+ Fabric API，在独立的 Fabric 26.3 服务器启动成功；`/claim` 指令、配置生成、中文反馈与数据读取正常。

---

## 11. 许可

MIT License。可以自由用于服务器、修改和再发布，保留版权声明即可。

非官方 Minecraft 产品。未获得 Mojang 或 Microsoft 的批准或关联。
