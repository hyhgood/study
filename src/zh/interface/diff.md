---
title: DOM diff算法
sidebar: auto
sidebarDepth: 2
---
+ ##  传统diff算法  

计算一棵树形结构转换为另一棵树形结构需要最少步骤，如果使用传统的diff算法通过循环递归遍历节点进行对比，其复杂度要达到O(n^3)，其中n是节点总数，效率十分低下，假设我们要展示1000个节点，那么我们就要依次执行上十亿次的比较。
下面附上一则简单的传统diff算法：
```
let result = [];
// 比较叶子节点
const diffLeafs = function (beforeLeaf, afterLeaf) {
    // 获取较大节点树的长度
    let count = Math.max(beforeLeaf.children.length, afterLeaf.children.length);
    // 循环遍历
    for (let i = 0; i < count; i++) {
        const beforeTag = beforeLeaf.children[i];
        const afterTag = afterLeaf.children[i];
        // 添加 afterTag 节点
        if (beforeTag === undefined) {
            result.push({ type: "add", element: afterTag });
            // 删除 beforeTag 节点
        } else if (afterTag === undefined) {
            result.push({ type: "remove", element: beforeTag });
            // 节点名改变时，删除 beforeTag 节点，添加 afterTag 节点
        } else if (beforeTag.tagName !== afterTag.tagName) {
            result.push({ type: "remove", element: beforeTag });
            result.push({ type: "add", element: afterTag });
            // 节点不变而内容改变时，改变节点
        } else if (beforeTag.innerHTML !== afterTag.innerHTML) {
            if (beforeTag.children.length === 0) {
                result.push({
                    type: "changed",
                    beforeElement: beforeTag,
                    afterElement: afterTag,
                    html: afterTag.innerHTML
                });
            } else {
                // 递归比较
                diffLeafs(beforeTag, afterTag);
            }
        }
    }
    return result;
}
```
+ ##  DOM diff算法
![](https://img-blog.csdn.net/20180717182348969)
DIFF算法在执行时有三个维度，分别是Tree DIFF、Component DIFF和Element DIFF，执行时按顺序依次执行，它们的差异仅仅因为DIFF粒度不同、执行先后顺序不同。       
1. ### Tree DIFF
![](https://img-blog.csdn.net/2018071718264787)
 Tree DIFF是对树的每一层进行遍历，如果某组件不存在了，则会直接销毁。如图所示，左边是旧属，右边是新属，第一层是R组件，一模一样，不会发生变化；第二层进入Component DIFF，同一类型组件继续比较下去，发现A组件没有，所以直接删掉A、B、C组件；继续第三层，重新创建A、B、C组件。

2. ### Component DIFF
![](https://img-blog.csdn.net/20180717182802621)
如图所示，第一层遍历完，进行第二层遍历时，D和G组件是不同类型的组件，不同类型组件直接进行替换，将D删掉，再将G重建。 

3. ### Element DIFF
![](https://img-blog.csdn.net/20180717182842111)  
Element DIFF紧接着以上统一类型组件继续比较下去，常见类型就是列表。同一个列表由旧变新有三种行为，插入、移动和删除，它的比较策略是对于每一个列表指定key，先将所有列表遍历一遍，确定要新增和删除的，再确定需要移动的。如图所示，第一步将D删掉，第二步增加E，再次执行时A和B只需要移动位置即可。

4. ### 总结
![](https://images2018.cnblogs.com/blog/998023/201805/998023-20180519213134497-676744027.png)