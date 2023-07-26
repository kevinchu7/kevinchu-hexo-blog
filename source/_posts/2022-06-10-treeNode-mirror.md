---
title: 二叉树的镜像-Java实现
index_img: https://static.kevinchu.top/blog/assets/img/cover_023.jpeg
date: 2022-06-10 14:10:21
update: 2022-06-10 14:10:21
tags:
    - Java
    - 算法
categories:
    - 学习记录
---
## 题目描述
给出一棵二叉树，求它的镜像，如下图：右边是二叉树是左边二叉树的镜像。
![](https://static.kevinchu.top/blog/public/treeNode-mirror.png)

## 思路分析
Java递归实现：如果当前节点为空，返回，否则交换该节点的左右节点，递归的对其左右节点进行交换处理。

## 代码实现
```Java
import java.util.*;

/*
 * public class TreeNode {
 *   int val = 0;
 *   TreeNode left = null;
 *   TreeNode right = null;
 *   public TreeNode(int val) {
 *     this.val = val;
 *   }
 * }
 */
public class Solution {
    /**
     *
     * 
     * @param pRoot TreeNode类 
     * @return TreeNode类
     */
    public TreeNode Mirror (TreeNode pRoot) {
        // write code here
        if(pRoot==null||(pRoot.left==null&&pRoot.right==null)){
            return pRoot;
        }
        TreeNode node=pRoot.left;
        pRoot.left=pRoot.right;
        pRoot.right=node;
        if(pRoot.left!=null){
            Mirror(pRoot.left);
        }
        if(pRoot.right!=null){
            Mirror(pRoot.right);
        }
        return pRoot;
    }
}

```