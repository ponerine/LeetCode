### 1719.Number-Of-Ways-To-Reconstruct-A-Tree

此题是LC周赛历史难题的top 1，难度分是3018。特别注意，题意的意思是，任意两点如果是一对pair（即是在一条直系链上），它们必须出现在pairs里面。

我们定义如果x和y是一对pair，那么他们彼此是直系亲属关系。我们定义每个节点的degree为它的直系亲属的个数。对于节点x，我们将它的所有直系亲属都收集起来看，根据定义，它们必然是一条直系链。

我们观察一下如果仅是一条直系链，那么里面的每个节点的degree都是相等的。并且任意两点互换，都不改变这个结构的pairs。也就是说，这样的“直系链”的拓扑结构不唯一。

如果这条直系链的某个祖先有一条分叉，那么我们发现原来的直系链上的节点，从该祖宗以上的部分的degree都会增加（因为它们有其他更多的直系子孙了）。这就告诉我们，对于同一条直系链上的节点，从高到低的degree必然是下降/不增的。所以我们如果把relative[x]的所有节点按照degree从大到小排列，那么x的父节点应该就是degree恰好大于或等于x的那个节点。当然，这样的节点可能会有多个，我们可以强制选定其中一个，因为之前说过，同一个直系链里面degree相同的节点可以互换。但是注意，这种“强制”必须是全局一致的，举个例子
```
    a
    b
   c  e
  d    f
```
在直系链a-b-c-d中，a和b的度相等，如果我们制定c的父节点是b的话，那么在直系链a-b-e-f中，e的父节点就不能随意指定，必须也是b而不能是a。为了保证这种“全局一致性”，我们需要将全部节点nodes按照度来从小到大排序编号，比如说上面的例子就是"dcfeba"，我们规定属于同一个直系链、度相同的两个节点，排序靠前的节点更靠下层。这样对于c而言，它有两个度相同的祖先a和b，根据规则b是它的父亲。同理对于e而言，也可以判定b是它的父亲，而不是a。

有了上述的nodes数组，对于任意的节点nodes[i]，我们都可以确定它的父节点：那就是在i之后的第一个j，s.t. (1) nodes[i]和nodes[j]是直系亲属，并且 (2)nodes[j]的度大于等于nodes[i]。于是我们可以依次方法确定每个点的父节点。整张树的拓扑结构也就确定了下来。

注意树的根是没有父节点的。如果我们发现有两个或以上的节点没有父节点，说明这张图是被割裂成几部分的，直接返回0.

接下来的思路有两种：
1. 利用这个树的拓扑结构，来验证是否和题目所给的pairs一致。对于每个点而言，它的直系亲属的个数应该是它的深度+它的子孙的个数。前者可以从pairs中统计，后者可以从树的遍历中统计。两者如果有任何不匹配，则返回0.
2. 有一个不是很直观的充要条件来判定构建的这棵树是否与pairs一致。对于nodes[i]，我们找到了nodes[j]是它的父亲，那么nodes[i]的所有亲属，必然是nodes[j]的亲属。如果发现nodes[i]的某个直系亲属r不是nodes[j]的直系亲属，那么显然是矛盾的。如果所有的r都满足，则说明这棵树的结构与pairs没有矛盾。

以上的算法判断是否存在这样的树。那么怎么判断这棵树是否唯一呢？其实很简单，如果任何一个nodes[i]和它的父亲的度相同，那么根据之前的分析，这两个节点其实是可以互换而不影响pairs的。如果最终答案是“树可行”（非0）的话，那么就可以输出2.
