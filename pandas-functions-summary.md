## 一、DataFrame 创建与概览

**现实生活目标：** 把零散信息整理成「行=记录、列=属性」的表格，并快速了解数据规模、类型和质量，相当于 Excel 打开文件后先看总行数、列名和数据概况。

**示例数据集 `df`：**

| A | B | C |
|---|---|---|
| 1 | 2 | 3 |
| 4 | 5 | 6 |
| 7 | 8 | 9 |

| 函数 | 输入参数 | 输出 | 示例输出 |
|------|----------|------|----------|
| `pd.DataFrame(data, columns=...)` | `data`：二维数据；`columns`：列名列表 | 新的 DataFrame | 3 行 3 列，`A/B/C` |
| `df.head(n)` | `n`：显示前 n 行，默认 5 | DataFrame 前 n 行 | 显示全部 3 行 |
| `df.tail(n)` | `n`：显示后 n 行，默认 5 | DataFrame 后 n 行 | 显示最后一行 `[7,8,9]` |
| `df.columns` | 无 | Index 对象（列名） | `Index(['A','B','C'])` |
| `df.index = [...]` | 行索引标签列表 | 修改行索引（原地） | `['x','y','z']` |
| `df.shape` | 无 | `(行数, 列数)` 元组 | `(3, 3)` |
| `df.info()` | 无 | 打印各列类型、非空数、内存 | `3 entries, 3 columns, int64` |
| `df.describe()` | 无 | 数值列统计摘要 DataFrame | count/mean/std/min/max |
| `df.nunique()` | 无 | 每列唯一值个数 Series | `A:3, B:3, C:3` |
| `df["A"].nunique()` | 无 | 该列唯一值个数 int | `3` |
| `df.drop(label, axis=1)` | `label`：列名；`axis=1` 删列；`inplace` 是否原地修改 | 删除后的 DataFrame | 删除 `zz` 列后剩 A/B/C |

---

## 二、数据读取与导出

**现实生活目标：** 从 CSV、Excel、Parquet 等不同来源导入业务数据，分析完再导出，对应「从各部门系统拉数据 → 分析 → 回写结果」。

**示例数据集 `coffee.csv`：**

| Day | Coffee Type | Units Sold |
|-----|-------------|------------|
| Monday | Espresso | 25 |
| Monday | Latte | 15 |
| Tuesday | Espresso | 30 |

| 函数 | 输入参数 | 输出 | 示例输出 |
|------|----------|------|----------|
| `pd.read_csv(path)` | `path`：文件路径 | DataFrame | 14 行咖啡销售数据 |
| `pd.read_parquet(path)` | `path`：Parquet 文件路径 | DataFrame | 30 万+ 行奥运成绩 |
| `pd.read_excel(path)` | `path`：Excel 路径 | 默认读第一个 sheet | 多 sheet 奥运数据 |
| `pd.read_excel(path, sheet_name=...)` | `sheet_name`：指定 sheet 名 | 指定 sheet 的 DataFrame | `results` sheet |
| `df.to_csv(path, index=False)` | `path`：保存路径；`index=False` 不写行号 | 无（写文件） | 生成 `coffee_updated.csv` |
| `pd.read_csv(..., engine="pyarrow", dtype_backend="pyarrow")` | `engine`：解析引擎；`dtype_backend`：内存类型 | 更省内存的 DataFrame | 46MB → 37.5MB |

---

## 三、索引与行/列选取（loc / iloc）

**现实生活目标：** 像 Excel 里点选单元格、拖选区域一样，精确取出或修改特定行、列，是几乎所有分析的第一步。

**示例数据集 `coffee`（同上，索引 0–2）：**

| Day | Coffee Type | Units Sold |
|-----|-------------|------------|
| Monday | Espresso | 25 |
| Monday | Latte | 15 |
| Tuesday | Espresso | 30 |

| 函数 | 输入参数 | 输出 | 示例输出 |
|------|----------|------|----------|
| `df.sample(n, random_state=...)` | `n`：随机行数；`random_state`：固定随机种子 | 随机 n 行 DataFrame | 每次相同种子结果一致 |
| `df.loc[row]` | `row`：标签或标签列表/切片（**闭区间**） | 选中行（Series 或 DataFrame） | `loc[0]` → 第 0 行 |
| `df.loc[row, col]` | `row`：行标签；`col`：列名或列名列表 | 单个值 / Series / DataFrame | `loc[5:8,"Day"]` → Day 列 |
| `df.loc[条件]` | 布尔 Series | 满足条件的行 | `loc[height_cm > 215]` |
| `df.iloc[row, col]` | 整数位置（**左闭右开**） | 按位置选取 | `iloc[0:2]` → 第 0、1 行 |
| `df.index = df["Day"]` | 某列作为新索引 | 行索引变为 Day 值 | 可用 `loc["Monday"]` |
| `df.reset_index(drop=True)` | `drop=True`：旧索引不保留为列 | 恢复默认 0,1,2… 索引 | Day 变回普通列 |
| `df.loc[row, col] = value` | 行、列、新值 | 原地修改 | `loc[1,"Units Sold"]=10` |
| `df["列名"]` | 列名 str | Series | `coffee["Day"]` |

**loc vs iloc 关键区别：**

| | loc | iloc |
|---|-----|------|
| 索引类型 | 标签 | 整数位置 |
| 切片 | `[0:2]` 含 2 | `[0:2]` 不含 2 |

---

## 四、排序

**现实生活目标：** 找出销量最高/最低的记录，或按多字段优先级排序，类似 Excel「数据 → 排序」。

| 函数 | 输入参数 | 输出 | 示例输出 |
|------|----------|------|----------|
| `df.sort_values(by, ascending=...)` | `by`：排序列；`ascending`：True 升序 / False 降序 | 排序后的 DataFrame | Units Sold 从 15→35 |
| `df.sort_values([col1,col2], ascending=[0,1])` | 多列及各自升降序 | 多关键字排序 | 先按销量降序，再按类型升序 |

---

## 五、筛选与过滤

**现实生活目标：** 从海量记录中找出符合条件的数据，如「身高>215cm 的美国运动员」「名字含 Keith 的人」。

**示例数据集 `bios`（3 行）：**

| name | height_cm | born_country | born_city |
|------|-----------|--------------|-----------|
| Jean-François Blanchy | NaN | FRA | Bordeaux |
| Arnaud Boetsch | 183.0 | FRA | Meulan |
| Jean Borotra | 183.0 | FRA | Biarritz |

| 函数 | 输入参数 | 输出 | 示例输出 |
|------|----------|------|----------|
| `df[布尔条件]` | 返回 True/False 的 Series | 过滤后的 DataFrame | `df[height_cm > 215]` |
| `df.loc[条件, 列]` | 条件 + 指定列 | 条件行 + 指定列 | `loc[>215, ["name","height_cm"]]` |
| `df[(条件1) & (条件2)]` | 多个布尔条件用 `&` 连接 | 同时满足的行 | 高个 + 美国籍 |
| `Series.str.contains(pat, case=, regex=)` | `pat`：匹配串；`case`：大小写；`regex`：是否正则 | 布尔 Series | `"keith"` case=False 可匹配 Keith |
| `Series.str.startswith(pat)` | 前缀字符串 | 布尔 Series | 名字以 Keith 开头 |
| `Series.isin(list)` | 值列表 | 布尔 Series | born_country 在 USA/UK |
| `df.query("表达式")` | SQL 风格字符串 | 过滤后的 DataFrame | `born_country=='USA' and born_city=='Seattle'` |

---

## 六、列的增删改与复制

**现实生活目标：** 清洗字段名、增删列、按规则算新指标（价格、收入），并避免误改原始数据。

| 函数 | 输入参数 | 输出 | 示例输出 |
|------|----------|------|----------|
| `df["新列"] = 值/Series` | 列名 + 标量或 Series | 新增/覆盖列 | `Price = 4.99` |
| `df["新列"] = df["A"] * df["B"]` | 列间运算 | 计算列 | `Revenue = Units Sold × Price` |
| `np.where(条件, 真值, 假值)` | 条件数组；两分支值 | 按条件赋值数组 | Espresso→5.99，其余→4.99 |
| `df[["col1","col2"]]` | 列名列表 | 只含指定列的 DataFrame | 保留 Day/Type/Units/Price |
| `df.drop(columns=..., inplace=...)` | `columns`：列名；`inplace`：是否原地 | 删除列后的 DataFrame | 删除 Price 列 |
| `df.rename(columns={旧:新}, inplace=...)` | 列名映射字典 | 重命名后的 DataFrame | `New_Price` → `Price` |
| `df.copy()` | 无 | 独立副本 DataFrame | 修改副本不影响原表 |

---

## 七、字符串与时间处理

**现实生活目标：** 从「Jean-François Blanchy」提取名、从「1886-12-12」提取年份，用于分组统计出生年代趋势。

| 函数 | 输入参数 | 输出 | 示例输出 |
|------|----------|------|----------|
| `Series.str.split()` | 无（按空格分） | 列表 Series | `["Jean-François","Blanchy"]` |
| `.str[0]` | 索引 | 取分割后第 0 段 | `"Jean-François"` |
| `pd.to_datetime(col)` | 日期列 | datetime64 Series | `1886-12-12` → datetime |
| `pd.to_datetime(col, format="%Y-%m-%d")` | `format`：日期格式 | 按格式解析 | 精确解析标准日期 |
| `Series.dt.year` | 无 | 年份 int Series | `1886` |
| `Series.dt.month` | 无 | 月份 int Series | `12` |

---

## 八、apply 自定义逻辑

**现实生活目标：** 当现有函数不够用时，按业务规则自定义分类，如身高分级、体重级别。

| 函数 | 输入参数 | 输出 | 示例输出 |
|------|----------|------|----------|
| `Series.apply(func)` | `func`：接收单个值的函数 | 变换后的 Series | `height_cm` → short/medium/tall |
| `DataFrame.apply(func, axis=1)` | `func`：接收整行；`axis=1` 按行 | 每行一个结果的 Series | 根据身高+体重 → Light/Middle/HeavyWeight |

**示例：**

```python
bios["height_category"] = bios["height_cm"].apply(
    lambda x: "short" if x <= 160 else "medium" if x <= 180 else "tall"
)
# 输出示例：183.0 → "medium"
```

---

## 九、缺失值处理

**现实生活目标：** 真实数据常有空白（传感器故障、用户未填），需要检测、填充或删除，保证分析可靠。

**示例数据集（含缺失）：**

| Day | Coffee Type | Units Sold | price |
|-----|-------------|------------|-------|
| Monday | Espresso | NaN | 4.99 |
| Monday | Latte | 15 | NaN |
| Tuesday | Espresso | 30 | 4.99 |

| 函数 | 输入参数 | 输出 | 示例输出 |
|------|----------|------|----------|
| `Series.isna()` | 无 | 布尔 Series（是 NaN 为 True） | `[True, False, False]` |
| `Series.notna()` | 无 | 布尔 Series（非 NaN 为 True） | 与 isna 相反 |
| `df.isna().sum()` | 无 | 每列缺失个数 Series | `Units Sold: 2, price: 1` |
| `df.fillna(value)` | 填充值（标量/Series/字典） | 填充后的 DataFrame | `fillna(10000)` |
| `df.fillna(Series.mean())` | 统计量 | 用均值填充 | Units Sold 空位→均值 |
| `Series.interpolate()` | 无 | 线性插值后的 Series | 25→NaN→30 插为 27.5 |
| `df.dropna()` | 无 | 删除含 NaN 的行 | 只保留完整行 |
| `df.dropna(subset=[列])` | `subset`：检查的列 | 指定列有 NaN 才删 | 只删 price 为空的行 |

---

## 十、聚合与透视

**现实生活目标：** 回答「哪种咖啡卖最多？」「每天各品类收入多少？」「哪年出生运动员最多？」——从明细汇总成报表。

**示例数据集 `coffee`（含 Price）：**

| Day | Coffee Type | Units Sold | Price |
|-----|-------------|------------|-------|
| Monday | Espresso | 25 | 4.99 |
| Monday | Latte | 15 | 5.99 |
| Tuesday | Espresso | 30 | 4.99 |

| 函数 | 输入参数 | 输出 | 示例输出 |
|------|----------|------|----------|
| `Series.value_counts()` | 无 | 各值出现次数 Series（降序） | England: 4824, Ontario: 1710… |
| `df.groupby(列)[目标列].sum()` | 分组列；聚合列 | 分组求和 Series | Espresso:265, Latte:195 |
| `df.groupby(列)[目标列].mean()` | 同上 | 分组均值 Series | Espresso:37.86, Latte:27.86 |
| `df.groupby(列)[目标列].count()` | 同上 | 分组计数 Series | 1970年: 239 人 |
| `df.groupby(列).agg({列:函数})` | 字典：列→聚合函数 | 多指标 DataFrame | Units Sold→sum, Price→mean |
| `df.pivot(index=, columns=, values=)` | 行索引列；列索引列；值列 | 透视表 DataFrame | 行=Day，列=Coffee Type，值=revenue |
| `pivot.sum()` | 无 | 全部汇总 | 各列/行总和 |
| `pivot.sum(axis=1)` | `axis=1` 按行求和 | 每行总和 Series | 周一总收入 |
| `groupby(...).reset_index()` | 无 | 分组结果变普通 DataFrame | born_year 从索引变列 |

**pivot 示例输出：**

| Day | Espresso | Latte |
|-----|----------|-------|
| Monday | 124.75 | 89.85 |
| Tuesday | 149.70 | 119.80 |

---

## 十一、时间序列高级函数（shift / rank / cumsum / rolling）

**现实生活目标：** 分析趋势与对比——昨日收入、累计营收、排名、移动平均，类似股票「MA5」或销售「近 3 天销量」。

| 函数 | 输入参数 | 输出 | 示例输出 |
|------|----------|------|----------|
| `Series.shift(n)` | `n`：向下移动行数 | 前移 n 行的 Series | `shift(2)`：第 2 行 revenue→124.75 |
| `Series.rank()` | 无 | 排名 Series（默认升序） | 身高 183→某排名 |
| `Series.cumsum()` | 无 | 累计求和 Series | 124.75→214.60→364.30… |
| `Series.rolling(window).sum()` | `window`：窗口大小 | 滑动窗口求和 Series | 近 3 天 Units Sold 之和 |

**shift 示例（3 行）：**

| revenue | yesterday_revenue (shift(2)) |
|---------|------------------------------|
| 124.75 | NaN |
| 89.85 | NaN |
| 149.70 | 124.75 |

---

## 十二、表合并与拼接（merge / concat）

**现实生活目标：** 多张表通过共同字段关联（用户 ID、国家代码），或把多个月份/地区数据纵向堆叠成一张大表。

**表示例：**

**bios：** `born_country=FRA`  
**nocs：** `NOC=FRA, region=France`

| 函数 | 输入参数 | 输出 | 示例输出 |
|------|----------|------|----------|
| `pd.merge(left, right, left_on=, right_on=, how=)` | 左表、右表、关联键、`how`：left/right/inner | 关联合并的 DataFrame | FRA 匹配 region=France |
| `pd.merge(..., suffixes=(,))` | 重名列后缀 | 避免列名冲突 | `(bios, nocsdf)` |
| `pd.concat([df1, df2])` | DataFrame 列表 | 纵向堆叠 | USA 运动员 + GBR 运动员 |

**merge 示例：** `bios` 的 `born_country` 与 `nocs` 的 `NOC` 左连接 → 新增 `region` 列（国家全名）。

---

## 十三、函数速查总表（按 notebook 出现顺序）

| 模块 | 函数 | 一句话用途 |
|------|------|------------|
| 1-5 基础 | `pd.DataFrame`, `head`, `tail`, `columns`, `shape`, `info`, `describe`, `nunique`, `drop` | 创建与概览 |
| 1-5 读写 | `read_csv`, `read_parquet`, `read_excel` | 导入数据 |
| 1-5 选取 | `sample`, `loc`, `iloc`, `reset_index` | 取行取列 |
| 1-5 排序 | `sort_values` | 排序 |
| 1-5 筛选 | 布尔索引, `str.contains`, `str.startswith`, `isin`, `query` | 过滤 |
| 1-5 列操作 | 列赋值, `np.where`, `drop`, `rename`, `copy` | 增删改列 |
| 1-5 字符串/时间 | `str.split`, `to_datetime`, `dt.year/month` | 文本与日期 |
| 1-5 apply | `Series.apply`, `DataFrame.apply` | 自定义规则 |
| 1-5 合并 | `pd.merge`, `pd.concat` | 多表关联 |
| 6 缺失值 | `isna`, `notna`, `fillna`, `interpolate`, `dropna`, `to_csv` | 处理空值 |
| 7 聚合 | `value_counts`, `groupby`, `agg`, `pivot`, `sum` | 汇总报表 |
| 8 高级 | `shift`, `rank`, `cumsum`, `rolling` | 趋势与排名 |
| 9 性能 | `read_csv(engine="pyarrow")` | 大文件省内存 |

---
 pandas 分析链路：

```
读入 → 概览 → 选取/筛选 → 清洗(缺失值) → 变换(列/字符串/日期)
     → 聚合/透视 → 高级时序 → 多表合并 → 导出
```
