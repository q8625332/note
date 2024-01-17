## 免递归生成树

> 需要后端将 parentCategoryId，categoryId进行asc排序

**例子：**

```java
function buildTree(data){
    let root = []
    let nodes = {}
    for(let i=0; i<data.length; i++){
        let node = data[i]
        if(node.parentCategoryId){
            if(nodes[node.parentCategoryId]){
                nodes[node.parentCategoryId].childs.push(node)
            }else{
                console.log("parent empty, {}", node)
                nodes[node.parentCategoryId] = {childs:[node]}
            }
        }
        else 
        {
            root.push(node)
        }
        if(nodes[node.categoryId]){
            nodes[node.categoryId] = Object.assign(node, {childs:nodes[node.categoryId].childs})
        }else{
            nodes[node.categoryId] = Object.assign(node, {childs:[]})
        }
    }
    return root
}
```
