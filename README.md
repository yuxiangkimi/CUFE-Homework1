# CUFE-Homework1 关佳昕
作业1内容
1. 成功启动TuGraph平台，文字描述安装/部署步骤，展示系统登录的截图
2. 模型建立、数据导入、增删改查，截图展示运行结果
3. 自己设计一个聚合查询的例子，截图展示运行结果
4. 通过github/csdn提交报告，把链接放到学习通回答中

###  1  成功启动TuGraph平台，文字描述安装/部署步骤，展示系统登录的截图

<br> 部署步骤如下：
1. 在 Windows 中启用 WSL 与虚拟机平台功能，安装新版 WSL 组件。
2. 安装 Docker Desktop，并在 Docker Engine 配置中添加镜像加速地址，解决 Docker Hub 直连超时问题。
3. 拉取 TuGraph 运行时镜像：

<img width="865" height="340" alt="image" src="https://github.com/user-attachments/assets/bb7d6976-5ad5-426a-8f87-0f70c7c5d7be" />

4. 创建两个命名卷，分别挂载数据目录和日志目录：

5. 启动 TuGraph 容器，映射 Web、Bolt、RPC 三个端口：

<img width="865" height="164" alt="image" src="https://github.com/user-attachments/assets/1397d4dd-f897-44a2-88b1-347102d09c6e" />




系统登陆截图如下
<img width="2160" height="1391" alt="image" src="https://github.com/user-attachments/assets/57d7476d-f2a8-4f9a-8747-4ca315937b74" />


### 2. 模型建立、数据导入、增删改查，截图展示运行结果

图项目名称为 `tugraph_homework1`，采用 Movie 示例图，围绕“演员—电影—用户”关系建模。

<br>
接下来进行模型的导入与建立，使用tugraph自带的movies模型
<img width="2160" height="1323" alt="image" src="https://github.com/user-attachments/assets/2e1e5479-b61a-4ebe-a536-4dea38778eea" />

**点类型（5 个）：**

| 点类型 | 含义 | 主要属性 |
| --- | --- | --- |
| `person` | 演员/人物 | `id`、`name`、`born`、`poster_image` |
| `movie` | 电影 | `id`、`title`、`duration`、`rated`、`tagline`、`summary`、`poster_image` |
| `genre` | 电影类型 | — |
| `keyword` | 关键词 | — |
| `user` | 用户 | — |

**边类型（8 个）：**

| 边类型 | 含义 | 方向 |
| --- | --- | --- |
| `acted_in` | 出演 | `person` → `movie` |
| `directed` | 导演 | `person` → `movie` |
| `produce` | 制片 | `person` → `movie` |
| `write` | 编剧 | `person` → `movie` |
| `has_genre` | 所属类型 | `movie` → `genre` |
| `has_keyword` | 包含关键词 | `movie` → `keyword` |
| `rate` | 用户评分 | `user` → `movie` |
| `is_friend` | 朋友关系 | `user` → `user` |


### 数据导入

新建图项目时选择 Movie 模板，系统自动完成图模型创建和示例数据导入。导入后通过查询验证数据已写入：

```cypher
MATCH (m:movie) RETURN m LIMIT 25
```
<img width="986" height="406" alt="image" src="https://github.com/user-attachments/assets/c2decaf5-287e-4e73-9fe2-cb7d14f5afeb" />



## 增删改查操作

以下操作均在图查询控制台中以 Cypher 语句执行。

### 1 新增

新增一名演员：

```cypher
CREATE (p:person {id: 9001, name: 'Zhang San', born: 1995, poster_image: ''}) RETURN p
```

<img width="1319" height="388" alt="image" src="https://github.com/user-attachments/assets/53419f34-f518-4870-9c8d-e9096f4e7ba4" />


新增一部电影：

```cypher
CREATE (m:movie {id: 9001, title: 'Homework Movie', duration: 120, rated: 'PG-13', tagline: 'For TuGraph homework', summary: 'placeholder text', poster_image: ''}) RETURN m
```

<img width="1643" height="338" alt="image" src="https://github.com/user-attachments/assets/a2a0ffb6-dee0-4125-a7f3-1a654b06791f" />


为演员和电影建立“出演”关系：

```cypher
MATCH (p:person {id: 9001}), (m:movie {id: 9001})
CREATE (p)-[r:acted_in {role: 'lead actor'}]->(m)
RETURN r
```

<img width="1490" height="267" alt="image" src="https://github.com/user-attachments/assets/95e491dd-7326-4113-9e50-f845286357b0" />


### 2 查询

查询刚新建的演员、电影及其关系：

```cypher
MATCH (p:person)-[r:acted_in]->(m:movie)
WHERE p.id = 9001
RETURN p.name AS actor, m.title AS movie, r.role AS role
```
<img width="1612" height="275" alt="image" src="https://github.com/user-attachments/assets/ecd3da47-4c58-4d4c-8ee5-d401c1ec84a0" />


### 3 修改

修改演员的姓名和出生年份：

```cypher
MATCH (p:person {id: 9001})
SET p.born = 1996, p.name = 'Zhang San Updated'
RETURN p
```

<img width="1351" height="351" alt="image" src="https://github.com/user-attachments/assets/524a337c-9b36-4bcb-ac16-5b7a298ec9d4" />


### 4 删除

删除演员节点（`DETACH` 同时删除其关联边）：

```cypher
MATCH (p:person {id: 9001}) DETACH DELETE p
```

删除电影节点：

```cypher
MATCH (m:movie {id: 9001}) DETACH DELETE m
```

删除后再次查询，确认数据已不存在：

```cypher
MATCH (n) WHERE n.id = 9001 RETURN n
```
<img width="1009" height="266" alt="image" src="https://github.com/user-attachments/assets/b0a671af-ba5f-4a46-9935-849bd30c9589" />




## 3. 自己设计一个聚合查询的例子，截图展示运行结果

### 查询设计

统计每位演员出演的电影数量，按出演数量从高到低排序，取前 10 名。

### 查询语句

```cypher
MATCH (p:person)-[:acted_in]->(m:movie) RETURN p.name AS actor, count(*) AS movieCount

MATCH (p:person)-[:acted_in]->(m:movie)
RETURN p.name AS actor, count(*) AS movieCount
ORDER BY movieCount DESC
LIMIT 10

```

###  查询结果


<img width="2160" height="1261" alt="image" src="https://github.com/user-attachments/assets/fafe3620-45af-4047-8f9c-02a3285b466b" />


