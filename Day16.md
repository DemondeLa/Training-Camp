## Blog

### 算法

#### 详细任务

- 530 二叉搜索树的最小绝对差
- 501 二叉搜索树中的众数
- 236 二叉树的最近公共祖先



#### 530 二叉搜索树的最小绝对差

需要领悟一下二叉树遍历上双指针操作，优先掌握递归 

题目链接/文章讲解：[https://programmercarl.com/0530.%E4%BA%8C%E5%8F%89%E6%90%9C%E7%B4%A2%E6%A0%91%E7%9A%84%E6%9C%80%E5%B0%8F%E7%BB%9D%E5%AF%B9%E5%B7%AE.html](https://programmercarl.com/0530.二叉搜索树的最小绝对差.html) 

视频讲解：https://www.bilibili.com/video/BV1DD4y11779

直接看的随想录的视频

本题最直接的思路就是，先使用中序遍历一遍二叉树，因为是二叉搜索树，使用中序能够得到一个递增的数组，这样再遍历一遍这个数组，逐个比较相邻元素之间的差，即可得到答案。但是这么做没有充分的利用二叉搜索树的特性，而且还有额外的空间开销

以下是双指针法的思路：也是基于上面一种思路的优化。使用一个`pre`指针来替代之前的`vec`数组，也是中序遍历的思路，更加充分的利用了BST的特性，在遍历的时候就是直接进行比较并记录相邻节点之间的最小差。

思路比较清晰：先走到最左边，并记录该节点，然后根据中序的顺序进行遍历，比较，并更新前节点的标记

```cpp
class Solution {
public:
    int minDif = INT_MAX;
    TreeNode *pre = nullptr;
    void traversal(TreeNode *node) {
        if (!node) return;
        traversal(node->left);
        if (pre) minDif = min(node->val - pre->val, minDif);
        pre = node;
        traversal(node->right);
    }
    int getMinimumDifference(TreeNode* root) {
        traversal(root);
        return minDif;
    }
};
```

迭代法的思路和上面一样，只是使用自定义的栈来模拟系统栈

```cpp
class Solution {
public:
    int getMinimumDifference(TreeNode* root) {
        stack<TreeNode *> st;
        TreeNode *cur = root;
        TreeNode *pre = nullptr;
        int minDif = INT_MAX;
        while (cur || !st.empty()) {
            if (cur) {
                st.push(cur);
                cur = cur->left;
            } else {
                cur = st.top();
                st.pop();
                if (pre)
                    minDif = min(cur->val - pre->val, minDif);
                pre = cur;
                cur = cur->right;
            }
        }
        return minDif;
    }
};
```



#### 501 二叉搜索树中的众数

和 530差不多双指针思路，不过 这里涉及到一个很巧妙的代码技巧。

可以先自己做做看，然后看我的视频讲解。

[https://programmercarl.com/0501.%E4%BA%8C%E5%8F%89%E6%90%9C%E7%B4%A2%E6%A0%91%E4%B8%AD%E7%9A%84%E4%BC%97%E6%95%B0.html](https://programmercarl.com/0501.二叉搜索树中的众数.html)  

视频讲解：https://www.bilibili.com/video/BV1fD4y117gp  

看到本题，首先想到的思路就是构造一个`map`用来存储节点与该节点元素出现的次数，可以使用前序遍历，遍历树中的每个节点，并将其全部存储到`map`中。然后再遍历`map`中的所有元素，找出其中出现频率最多的

```cpp
class Solution {
public:
    unordered_map<int, int> up;
    void traversal(TreeNode *node) {
        if (!node) return ;
        up[node->val]++;
        if (node->left)
            traversal(node->left);
        if (node->right)
            traversal(node->right);
    }
    vector<int> findMode(TreeNode* root) {
        traversal(root);
        vector<int> ret;
        for (auto it : up) {
            if (ret.empty()) ret.push_back(it.first);
            else {
                if (it.second > up[ret.back()])
                    ret.clear();
                else if (it.second < up[ret.back()])
                    continue;
                ret.push_back(it.first);
            }
        }
        return ret;
    }
};
```

但是这么做，提交的时候，可以看出效率非常差。看了随想录的视频

第一种思路，当作普通的二叉树，先进行遍历，用`map`来统计频率，然后将`map`转变成`vector`，然后再进行排序，从而求出众数

而这里是BST，遍历的顺序一定是中序遍历，这样得到的元素才是有序的（单调递增）。至此，问题就转化为了在一个有序的数组里求众数。还可以进一步的进行优化，使用双指针法，在遍历的时候就直接求众数。

```cpp
class Solution {
    TreeNode *pre;
    int count = 0, maxCount = 0;
    vector<int> res;
public:
    void traversal(TreeNode *node) {
        if (!node) return ;
        traversal(node->left);

        if (!pre || pre->val == node->val)
            count++;
        else {
            if (count > maxCount) {
                maxCount = count;
                res.clear();
                res.push_back(pre->val);
            } else if (count == maxCount)
                res.push_back(pre->val);
            count = 1;
        }
        pre = node;

        traversal(node->right);
    }
    vector<int> findMode(TreeNode* root) {
        traversal(root);
        if (count > maxCount) {
            maxCount = count;
            res.clear();
            res.push_back(pre->val);
        } else if (count == maxCount)
            res.push_back(pre->val);
        return res;
    }
};
```

一开始的时候，听了Carl哥的讲解就开始做，但是发现最后一个节点无法更新，即`pre` 指针是在遍历过程中更新的，但在遍历的最后一个节点时，它不会再被更新，从而导致最后一组相同的值没有被正确处理。

解决方法：

- 在 `traversal` 方法结束时，应该额外检查一次最后一个连续值的情况。
- 在处理节点时，如果当前节点与前一个节点的值相同，增加 `count`，否则比较 `count` 和 `maxCount` 更新结果。



#### 236 二叉树的最近公共祖先

本题其实是比较难的，可以先看我的视频讲解 

[https://programmercarl.com/0236.%E4%BA%8C%E5%8F%89%E6%A0%91%E7%9A%84%E6%9C%80%E8%BF%91%E5%85%AC%E5%85%B1%E7%A5%96%E5%85%88.html](https://programmercarl.com/0236.二叉树的最近公共祖先.html) 

视频讲解：https://www.bilibili.com/video/BV1jd4y1B7E2  

本题在看了题目后没有思路，直接看视频。基本理解的思路，但是对于代码的理解还不是很清楚（递归比较多）

基本思路是通过后序遍历的方式递归遍历树，来找LCA。对每个节点 `root`，函数首先检查它是否为空，如果为空，说明该子树已经遍历完毕，直接返回 `nullptr`。如果当前节点是 `p` 或者 `q`，说明已经找到了一个目标节点，直接返回当前节点；否则，递归地在当前节点的左子树和右子树中查找 `p` 和 `q`。

对于每个节点 `root`，分别递归查询它的左子树和右子树，结果存储在 `left` 和 `right` 中。

通过对 `left` 和 `right` 返回值的判断，决定如何处理：

- **情况 1**：如果 `left` 和 `right` 都不为空，意味着 `p` 和 `q` 分别位于当前节点的左右子树，那么当前节点 `root` 就是它们的最近公共祖先，直接返回 `root`。
- **情况 2**：如果只有 `left` 有值（即 `left` 不为空，`right` 为空），说明 `p` 和 `q` 都位于左子树中，此时返回 `left`。
- **情况 3**：如果只有 `right` 有值（即 `left` 为空，`right` 不为空），说明 `p` 和 `q` 都位于右子树中，此时返回 `right`。
- **情况 4**：如果 `left` 和 `right` 都为空，说明在左右子树中都没找到 `p` 或 `q`，返回 `nullptr`。

```cpp
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (!root) return nullptr;
        if (root == p || root == q)
            return root;

        TreeNode *left = lowestCommonAncestor(root->left, p, q);
        TreeNode *right= lowestCommonAncestor(root->right, p, q);

        if (left && right) return root;
        if (left && !right) return left;
        else if (!left && right) return right;
        else return nullptr;
    }
};
```



### 八股文

#### undo log、redo log、binlog 有什么用？

掌握程度：

- **理解每个日志的定义和作用**：能够清晰地解释Undo Log、Redo Log和Binlog的基本功能。
- **知道它们在事务处理中的角色**：理解它们如何确保事务的ACID属性（原子性、一致性、隔离性、持久性）。
- **了解它们在故障恢复中的应用**：能够描述在系统故障时，这些日志如何帮助恢复数据。
- **了解它们在数据库复制中的作用**：知道Binlog在主从复制中的重要性。

参考资料：

- 小林coding: undo log、redo log、bin log 有什么用？

    https://xiaolincoding.com/mysql/log/how_update.html#为什么需要-undo-log

- 【视频：undo log、redo log、 bin log的作用是什么？】

    https://www.bilibili.com/video/BV18u4y1Z7LA/?share_source=copy_web&vd_source=9bb0aa9c2c3cc1b12ca6f343a55b4e80



`undo log`和`redo log`是InnoDB引擎提供的事务日志；`bin log`是由MySQL的server层实现的，所有引擎公用的归档日志

##### undo log

**undo log（回滚日志）**：是 Innodb 存储引擎层生成的日志，实现了事务中的**原子性**，主要**用于事务回滚和 MVCC**。

###### 基本概念

undo log 是一种用于撤销回退的日志，在数据库事务开始之前，MySQL会先记录更新前的数据到undo log日志文件里面，当事务回滚时或者数据库崩溃时，可以利用undo log来进行回退

undo log 在事务开始之前产生；事务在提交时，并不会立即删除undo log，InnoDB会将该事务对应的undo log放入到删除列表中，后面会通过后台线程purge thread进行回收处理

> undo log 也会产生对应的redo log，因为undo log 也是一条SQL，也会对数据库的数据产生一些影响，因此也要实现持久性保护

![](pic/6_36.png)

###### 作用：

1. 提供回滚操作【undo log 实现事务的==原子性==】

    在数据修改的时候，不仅记录了redo log，还记录了相对应的undo log，如果因为某些原因导致事务执行失败了，可以借助undo log进行回滚

    undo log 和 redo log 记录物理日志不一样，它是逻辑日志。可以认为当delete 一条记录时，undo log会记录一条对应的insert记录，

    - 在**删除**一条记录时，要把这条记录中的内容都记下来，这样之后回滚时再把由这些内容组成的记录**插入**到表中就好了；

    - delete操作实际上不会立即直接删除，而是将delete对象打上delete flag，标记为删除，最终的删除操作是purge线程完成的。

    反之亦然，在**插入**一条记录时，要把这条记录的主键值记下来，这样之后回滚时只需要把这个主键值对应的记录**删掉**就好了；

    当update一条记录时，它记录一条对应相反的updata记录。在**更新**一条记录时，要把被更新的列的旧值记下来，这样之后回滚时再把这些列**更新为旧值**就好了。

    update分为两种情况：update的列是否是主键列。

    - 如果不是主键列，在undo log中直接反向记录是如何update的。即update是直接进行的。
    - 如果是主键列，update分两部执行：先删除该行，再插入一行目标行。

2. 提供多版本控制MVCC【undo log 实现多版本并发控制MVCC】

    MVCC 多版本控制。在MySQL数据库InnoDB存储引擎中，用undo log 来实现多版本并发控制。当读取的某一行被其他事务锁定时，它可以从undo log中分析出该行记录以前的数据版本是怎样的，从而让用户能够读取当前事务操作之前的数据（快照读）
    
    对于「读提交」和「可重复读」隔离级别的事务来说，它们的快照读（普通 select 语句）是通过 Read View + undo log 来实现的，它们的区别在于创建 Read View 的时机不同：
    
    - 「读提交」隔离级别是在每个 select 都会生成一个新的 Read View，也意味着，事务期间的多次读取同一条数据，前后两次读的数据可能会出现不一致，因为可能这期间另外一个事务修改了该记录，并提交了事务。
    - 「可重复读」隔离级别是启动事务时生成一个 Read View，然后整个事务期间都在用这个 Read View，这样就保证了在事务期间读到的数据都是事务启动前的记录。

> undo log 和数据页的刷盘策略是一样的，都需要通过 redo log 保证持久化

##### redo log

**redo log（重做日志）**：是 Innodb 存储引擎层生成的日志，实现了事务中的**持久性**，主要**用于掉电等故障恢复**；

###### 基本概念

InnoDB引擎对事物的更新，先更新内存（同时标记为脏页），然后将更新记录写入redo log 日志。

后续，在系统空闲的时候或者是按照设定的更新策略，InnoDB由后台线程将缓存在Buffer Pool的脏页即日志中的内容，更新到磁盘中。这就是所谓的预写式技术（WAL, Write Ahead logging）：**MySQL 的写操作并不是立刻写到磁盘上，而是先写日志，然后在合适的时间再写到磁盘上**。这种技术可以大大减少IO操作的频率，提升数据刷新的效率

redo log 被称为重做日志，包括2部分：一个是内存中的日志缓冲 redo log buffer，另一个是磁盘上的日志文件 redo log file

![](pic/6_37.png)

###### 作用

MySQL每执行一条DML语句（DML（Data Manipulation Language，数据操作语言）是 SQL 中用于操作数据库中数据的语言），先将记录写入redo log buffer。后序某个时间点再一次性将多个操作记录写到redo log file。当故障发生致使内存数据丢失后，InnoDB会在重启时，经过重仿redo，将Page恢复到崩溃之前的状态，**通过redo log 可以实现事务的持久性**

WAL 技术的另外一个优点：**MySQL 的写操作从磁盘的「随机写」变成了「顺序写」**，提升语句的执行性能

> redo log 和 undo log 区别在哪？
>
> - redo log 记录了此次事务「**修改后**」的数据状态，记录的是更新**之后**的值，**主要用于事务崩溃恢复，保证事务的持久性**。
> - undo log 记录了此次事务「**修改前**」的数据状态，记录的是更新**之前**的值，**主要用于事务回滚，保证事务的原子性**。

##### bin log

**binlog （归档日志）**：是 Server 层生成的日志，主要**用于数据备份和主从复制**；

###### 基本概念

bin log 是一个二进制格式的文件，用于记录用户对数据库更新的SQL语句信息，即所有数据库表结构变更和表数据修改的日志，例如更改数据库表和更改内容的SQL语句都会记录到bin log里，但是不会记录`SELECT`和`SHOW`这类查询操作

bin log在MySQL的Server层实现（引擎共用）。MySQL 在完成一条更新操作后，Server 层还会生成一条 binlog，等之后事务提交的时候，会将该事物执行过程中产生的所有 binlog 统一写 入 binlog 文件。

bin log为逻辑日志，记录的是一条SQL语句的原始逻辑

- bin log 不限制大小，追加写入，不会覆盖以前的日志
- 默认情况下，bin log日志是二进制格式的，不能使用查看文本工具的命令（比如，cat、vi等）查看，而使用mysql bin log解析查看

###### 作用

1. 主从复制：在主库中开始bin log 功能，这样主库就可以把bin log 传递给从库，从库拿到bin log 后实现数据恢复，达到主从数据一致性
2. 数据恢复：通过mysql bin log 工具恢复数据

> redo log 和 binlog 有什么区别？
>
> *1、适用对象不同：*
>
> - binlog 是 MySQL 的 Server 层实现的日志，所有存储引擎都可以使用；
> - redo log 是 Innodb 存储引擎实现的日志；
>
> *2、文件格式不同：*
>
> - binlog 有 3 种格式类型，分别是 STATEMENT（默认格式）、ROW、 MIXED，区别如下：
>     - STATEMENT：每一条修改数据的 SQL 都会被记录到 binlog 中（相当于记录了逻辑操作，所以针对这种格式， binlog 可以称为逻辑日志），主从复制中 slave 端再根据 SQL 语句重现。但 STATEMENT 有动态函数的问题，比如你用了 uuid 或者 now 这些函数，你在主库上执行的结果并不是你在从库执行的结果，这种随时在变的函数会导致复制的数据不一致；
>     - ROW：记录行数据最终被修改成什么样了（这种格式的日志，就不能称为逻辑日志了），不会出现 STATEMENT 下动态函数的问题。但 ROW 的缺点是每行数据的变化结果都会被记录，比如执行批量 update 语句，更新多少行数据就会产生多少条记录，使 binlog 文件过大，而在 STATEMENT 格式下只会记录一个 update 语句而已；
>     - MIXED：包含了 STATEMENT 和 ROW 模式，它会根据不同的情况自动使用 ROW 模式和 STATEMENT 模式；
> - redo log 是物理日志，记录的是在某个数据页做了什么修改，比如对 XXX 表空间中的 YYY 数据页 ZZZ 偏移量的地方做了AAA 更新；
>
> *3、写入方式不同：*
>
> - binlog 是追加写，写满一个文件，就创建一个新的文件继续写，不会覆盖以前的日志，保存的是全量的日志。
> - redo log 是循环写，日志空间大小是固定，全部写满就从头开始，保存未被刷入磁盘的脏页日志。
>
> *4、用途不同：*
>
> - binlog 用于备份恢复、主从复制；
> - redo log 用于掉电等故障恢复。



#### 什么是慢查询？原因是什么？可以怎么优化？

掌握程度：

- **能够清晰地定义慢查询**：解释慢查询的概念及其对应用性能的影响。
- **理解慢查询的常见原因**：列举并解释导致慢查询的常见因素。
- **知道优化慢查询的方法**：描述几种常用的慢查询优化策略。

参考资料：

- 【视频：如何做慢查询优化？】

    https://www.bilibili.com/video/BV1Vc411P7q9/?share_source=copy_web&vd_source=9bb0aa9c2c3cc1b12ca6f343a55b4e80

慢查询（Slow Query）是指在数据库执行过程中，耗时较长的查询操作。

通常，数据库管理系统（DBMS）会设定一个阈值，超过这个阈值的查询被标记为“慢查询”，并可以记录到日志中以供分析和优化。例如，MySQL中通常通过配置`long_query_time`参数来设置慢查询阈值，超过这个时间的查询会被记录到慢查询日志中。具体的定义包括：

1. **查询时间长**：如果查询需要的时间超出了一个预设的时间阈值（如1秒、5秒、10秒等），它就会被视为慢查询。
2. **资源消耗大**：即使查询时间不算长，但如果它占用了大量的CPU、内存或I/O资源，也可能被认为是“慢查询”，并需要优化。

慢查询的出现通常意味着数据库的查询效率不高，可能是因为查询设计不当、数据量过大、索引缺失或系统资源不足等原因。

##### 慢查询的常见原因

导致 SQL 查询缓慢的潜在原因有多种，但大多数源于数据库设计效率低下、查询公式或资源限制。以下是最常见的原因：

1. 缺少适当的索引：如果没有索引，数据库必须扫描整个表（全表扫描）才能找到相关行，这会大大增加查询时间。

2. 差的查询结构：编写不当的查询（例如，不必要的 JOIN、不正确的筛选条件）可能会导致处理时间延长。例如：

    - 使用 `SELECT *` 查询全部字段，而不是仅选择需要的字段。
    - `JOIN` 过多或子查询设计复杂。
    - 使用不必要的`ORDER BY`、`GROUP BY` 或没有索引支持的过滤条件。

3. 大型数据集处理：当表的数据量较大而查询条件无法高效过滤时，会导致慢查询。即使用大型数据集的查询可能需要优化，以避免一次处理过多数据。

    例如，在百万级记录的表中，未加索引的简单条件查询可能耗费大量时间。

4. 数据库设计不合理：表设计不规范，例如字段类型选择不合适（比如把数字存储为字符串）。表结构过度复杂（如过度规范化或完全未规范化）。

5. 过时的统计数据：数据库优化器依靠统计数据做出决策。如果统计信息已过时，优化器可能会选择效率低下的查询执行计划。

6. 硬件资源限制：数据库所在的硬件（CPU、内存、磁盘 I/O）无法满足查询需求，特别是在高并发场景中。即CPU 速度慢、RAM 不足或磁盘速度慢

7. 锁和竞争：高并发和不当的事务管理会导致数据库上锁，从而延迟查询。长时间运行的查询可能会占用锁，导致其他事务或查询等待，进而形成性能瓶颈。

8. 临时表和排序：当查询涉及大量排序或分组时，数据库可能使用磁盘上的临时表，这会极大降低性能。

##### 优化慢查询的方法

1. 使用适当的索引

    索引通过允许数据库更高效地查找所需的行来加快查询执行速度。如为查询中经常使用的字段（如 `WHERE`、`JOIN`、`ORDER BY`、`GROUP BY` 等）创建索引。使用复合索引（多列索引）提升多条件查询的效率。

    但是，过度索引可能与没有索引一样糟糕，因此请仅在 WHERE、JOIN 或 ORDER BY 子句中最常涉及的列上创建索引。

    > Tip：复合索引（多列上的索引）对于按多列筛选的查询可能很有用。

2. 优化SQL查询：

    - 避免使用 `SELECT *`，只查询必要的字段。
    - 重构复杂查询，尽量减少嵌套子查询或多表关联。
    - 通过执行计划（如 MySQL 的 `EXPLAIN`）分析查询执行路径，定位瓶颈。

3. 优化联合查询和筛选

    高效使用 JOIN 语句可以大大减少查询执行时间：

    - 确保已为要联接的列编制索引。
    - 尽可能首选内部联接而不是外部联接，因为它们通常更快。

4. 控制数据范围

    - 为查询添加合适的过滤条件（如时间范围）。
    - 对大结果集使用分页查询（`LIMIT` 和 `OFFSET`）。

5. 维护索引和更新统计信息

    需要维护索引才能保持有效。定期重建或重新组织碎片化索引。此外，请确保定期更新数据库统计信息，因为这有助于查询优化器选择最有效的查询计划。

6. 优化数据库的设计

    - 合理选择字段类型，例如使用整型存储数值字段，而不是字符串。
    - 根据查询模式，适度规范化或去规范化表结构。
    - 对大表进行分区或分表，减少单表数据量。

7. 调整事务和锁

    - 减少长时间持有的事务锁。
    - 降低事务的隔离级别（如从 `SERIALIZABLE` 调整为 `READ COMMITTED`），平衡性能和一致性。

8. 考虑查询缓存

    缓存频繁运行的查询可以通过避免重复的数据库点击来节省时间。使用缓存机制，如 SQL Server 的计划缓存或 MySQL 的查询缓存。或者，使用外部缓存工具（如 Redis、Memcached）存储高频查询结果，减少数据库压力。

9. 增加硬件资源

    - 升级数据库服务器的硬件（如增加内存、升级 CPU 或使用 SSD）。
    - 在高并发场景中，通过主从复制或分库分表分散负载

10. 利用高性能解决方案

    为了获得大型复杂数据集的最佳性能，请考虑使用专为海量数据处理设计的高性能解决方案。这种类型的平台利用 GPU 加速等先进技术，能够跨 TB 甚至 PB 级数据快速执行复杂查询，从而减少对大量查询简化的需求。

11. 分布式架构：

    - 使用分片技术，将数据分布到多个节点中以减少单个节点的压力。

12. 持续监控和优化

    - 利用数据库的慢查询日志或性能分析工具（如 MySQL 的慢查询日志、PostgreSQL 的 `pg_stat_statements`）。
    - 根据业务需求和数据增长动态优化查询。



#### MySQL 有哪些锁

掌握程度

- **能够清晰地定义各种锁类型和性质**：解释表锁、行锁、间隙锁和意向锁的概念及其特点。
- **理解锁的实现方式**：描述悲观锁和乐观锁的实现机制。
- **知道死锁的原因和解决方法**：列举死锁产生的条件和避免死锁的策略。
- **了解锁的优化方法**：描述如何通过索引优化、事务管理和监控调优来提高数据库性能。

参考资料：

- 小林coding：MySQL有哪些锁

    https://xiaolincoding.com/mysql/lock/mysql_lock.html

- 【史上最全】MySQL各种锁详解：1小时彻底搞懂MySQL的各种锁】

    https://www.bilibili.com/video/BV1po4y1M7k5/?share_source=copy_web&vd_source=9bb0aa9c2c3cc1b12ca6f343a55b4e80

笔记：[MySQL锁](./MySQL 有哪些锁.md)



### 总结

今天的题目还是比较难的，虽然代码看上去比较简单，但是递归的思路是不太容易想到的，特别是最后一题的各种情况，没有carl哥带着分析，自己是想不出的；对于前两题，都是需要记录前面的节点。总的来说，首先还是要考虑二叉树的遍历顺序，像前面两题，都是对需要利用BST的特性的，使用中序遍历会比较好，而最后一题，需要找父节点，这就需要使用后序遍历了。

总结一下，还是在考察二叉树的各种遍历，一个要想清楚需要使用哪种遍历顺序，另一个则是要清楚在当前一层的具体逻辑是什么
