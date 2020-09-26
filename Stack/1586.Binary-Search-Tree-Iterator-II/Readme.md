### 1586.Binary-Search-Tree-Iterator-II

本题的本质是 094.Binary Tree Inorder Traversal. 用栈可以实现单步的next()操作。具体的方法是维护一个栈(nexts)存放未来需要访问的节点。其中马上能访问的下一个节点next()就在这个栈顶。当我们需要调用next()时就只需要读取栈顶的元素，然后将其弹出。接下来，为了给之后的next()做准备，然后我们需要走到刚才访问的节点的右节点，然后一路向左把所有节点都压入栈内。此番操作之后，此时的栈顶元素保证就是next greater element.

那么如何实现prev()呢？其实很"无赖"，只要把曾经访问过的所有节点都存入一个叫visited的栈里面。于是每次调用next()之后，visited就会新增一个元素。注意，我们调用prev()的时候，将visited的栈顶元素弹出，以便返回里面的“次”栈顶元素（因为visited的栈顶是刚访问过的，而prev()需要返回的是再早一次访问过的）。

那么我们怎么处理和利用visited的栈顶元素呢，白白扔掉吗？这里有个非常巧妙的处理方法：将visited的栈顶元素取出来直接放入nexts里面。这样如果下一次调用next()，就直接从nexts里面取栈顶元素就行了。但是我们需要将这类已经访问过的、再次塞回nexts的节点做个标记以示区别，因为如果调用next()读取的是这些值的话我们并不需要额外的“访问右节点再一路向左”的操作。可以想象，你连续调用了多少次prev()，那么就有多少个这样的已经访问过的节点被“暂存”在了nexts里面，如果你的next()读取的是这类的节点，那么他们一定全部在nexts的栈顶等着你。

另外一个细节是，hasPrev()的条件是```visited.size()>1```，因为返回的是visited的次栈顶元素。