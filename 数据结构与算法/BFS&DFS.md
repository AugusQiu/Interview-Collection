https://segmentfault.com/a/1190000018706578
# 图的基本算法(BFS和DFS)
图作为一种灵活的的数据结构，一般用V表示顶点，E表示边  
图可以分为有向图和无向图，G=(V,E)来表示图，经常用邻接矩阵或者邻接表来描述一副图  
在图的基本算法中，最初需要接触的就是图的**遍历**算法，根据访问节点的顺序，可分为**广度优先搜索(BFS)**和**深度优先搜索(DFS)**

## 广度优先搜索(Breadth-First-Search)
BFS并不考虑结果的可能位置，而彻底地搜索整张图，检查所有节点，直到找到结果为止。**它是一种盲目搜寻法，从一个点四面八方地进行扩散测试(每走一步，就要把下一步的所有可能都遍历一下，然后把错误的路口淘汰掉)**   
Dijkstra单源最短路径算法和Prim最小生成树算法都采用了和BFS类似的思想.ACM中，BFS常常被用于解决迷宫问题
````
//非递归
function wideTraversal(node){
    var nodes = [];
    if(node != null){
        var queue = [];
        queue.unshift(node);
        while(queue.length!=0){
            var item = queue.shift();
            nodes.push(item);
            var children = item.children;  
            for (var i = 0; i < children.length; i++)  
                queue.push(children[i]);  
        }
    }
    return nodes;
}
````
## 深度优先遍历
````
//递归
function deepTraversal(node,nodeList){
  if(node){
      nodeList.push(node);
      var children = node.children;
      for(var i=0;i<children.length;i++){
         deepTraversal(children[i],nodeList);   
      }
  }
  return nodeList;
}


//非递归
function deepTraversal(node){
    var nodeList = [];
    if(node){
        var stack = [];
        stack.push(node);
        while (stack.length != 0) {  
            var childrenItem = stack.pop();  
            nodeList.push(childrenItem);  
            var childrenList = childrenItem.children;  
            for (var i = childrenList.length - 1; i >= 0; i--)  
                stack.push(childrenList[i]);  
        }  
    }
    return nodeList;  
}
````

