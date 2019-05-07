---
title: DOM diff算法
sidebar: auto
sidebarDepth: 2
---
# DOM diff算法
![](https://img-blog.csdn.net/20180717182348969)
DIFF算法在执行时有三个维度，分别是Tree DIFF、Component DIFF和Element DIFF，执行时按顺序依次执行，它们的差异仅仅因为DIFF粒度不同、执行先后顺序不同。 
## Tree DIFF
![](https://img-blog.csdn.net/2018071718264787)
        Tree DIFF是对树的每一层进行遍历，如果某组件不存在了，则会直接销毁。如图所示，左边是旧属，右边是新属，第一层是R组件，一模一样，不会发生变化；第二层进入Component DIFF，同一类型组件继续比较下去，发现A组件没有，所以直接删掉A、B、C组件；继续第三层，重新创建A、B、C组件。
## Component DIFF
![](https://img-blog.csdn.net/20180717182802621)
如图所示，第一层遍历完，进行第二层遍历时，D和G组件是不同类型的组件，不同类型组件直接进行替换，将D删掉，再将G重建。 
## Element DIFF
![](https://img-blog.csdn.net/20180717182842111)
Element DIFF紧接着以上统一类型组件继续比较下去，常见类型就是列表。同一个列表由旧变新有三种行为，插入、移动和删除，它的比较策略是对于每一个列表指定key，先将所有列表遍历一遍，确定要新增和删除的，再确定需要移动的。如图所示，第一步将D删掉，第二步增加E，再次执行时A和B只需要移动位置即可。
