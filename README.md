# tries
前缀树的学习
前缀树（**Trie**，读音同 "Try"），又称**字典树**或**单词查找树**，是一种专门处理字符串匹配的数据结构。

它的核心思想是：**用空间换时间**。利用字符串的公共前缀来降低查询时间的开销。

---

## 1. 核心结构

在前缀树中，每一个节点代表字符串中的一个字符。从根节点到某一节点的路径连起来，就是该节点对应的字符串。

### 关键特征：

* **根节点不包含字符**：根节点通常是空的。
* **公共前缀共享**：如果两个单词有共同的前缀（比如 "apple" 和 "apply"），它们会共享前面的 "appl" 节点。
* **标记结束位**：节点通常有一个布尔标志（如 `isEnd`），用来表示当前节点是否是一个单词的结尾。

---

## 2. 为什么需要它？（对比哈希表）

你可能会问：既然有了哈希表（Hash Table），为什么还要用前缀树？

| 特性 | 哈希表 (Hash Table) | 前缀树 (Trie) |
| --- | --- | --- |
| **查询效率** | 平均 $O(1)$，但在哈希冲突或计算长字符串哈希值时会变慢。 | $O(m)$，$m$ 是字符串长度，与词库大小无关。 |
| **空间开销** | 相对集中。 | 较高（每个节点都要存储子节点的指针/数组）。 |
| **前缀匹配** | 不支持。必须输入完整字符串。 | **极其强大**。可以轻松找“所有以 'pre' 开头的单词”。 |
| **有序性** | 无序。 | 按照字典序排列。 |

---

## 3. 基本操作流程

### 插入 (Insert)

从根节点开始，遍历单词的每个字符：

1. 如果字符对应的子节点已存在，移动到子节点。
2. 如果不存在，创建一个新节点。
3. 单词遍历结束时，将最后一个节点标记为 `isEnd = true`。

### 查找 (Search)

同样从根节点开始：

1. 遍历字符，如果中途某个字符对应的节点不存在，返回 `false`。
2. 遍历结束，检查当前节点的 `isEnd` 是否为 `true`。

---

## 4. 前缀树的实现（Python 示例）

这是一个典型的 26 叉树（假设只处理小写字母）：

```python
class TrieNode:
    def __init__(self):
        self.children = {} # 使用字典或长度为26的数组
        self.isEnd = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word: str):
        node = self.root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.isEnd = True

    def startsWith(self, prefix: str) -> bool:
        node = self.root
        for char in prefix:
            if char not in node.children:
                return False
            node = node.children[char]
        return True

```

---

## 5. 实际应用场景

前缀树在处理文本和搜索建议时简直是“神器”：

1. **搜索框自动补全**：当你输入 "pyt" 时，搜索引擎会迅速在 Trie 中找到以 "pyt" 为前缀的所有分支（如 python, pytorch）。
2. **拼写检查**：快速判断一个单词是否在字典中。
3. **IP 路由查找**：在网络协议中，使用“最长前缀匹配”算法来决定数据包的转发路径。
4. **敏感词过滤**：构建一个包含所有违禁词的前缀树，扫描文章时效率极高。

---

## 6. 缺点

* **空间消耗大**：如果字符集很大（比如包含所有 Unicode 字符），每个节点维护的指针会占用大量内存。
* **优化方案**：可以使用 **双数组 Trie (Double-Array Trie)** 或 **基数树 (Radix Tree)** 来压缩空间。

