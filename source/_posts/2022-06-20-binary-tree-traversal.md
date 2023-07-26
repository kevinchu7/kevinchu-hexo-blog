---
title: 二叉树遍历-Java实现
index_img: https://static.kevinchu.top/blog/assets/img/cover_025.jpeg
date: 2022-06-20 10:04:55
updated: 2022-06-20 10:04:55
tags:
    - Java
    - 算法
categories:
    - 学习记录
---

## 1 概念

### 1.1 二叉树
![](https://static.kevinchu.top/blog/public/20220620110640.png)

二叉树是一种树形结构，特点是每个结点至多只有两颗子树，并且子树有左右之分，次序不能颠倒。

二叉树的特征：
- 二叉树的第i层至多有2^(i-1)个节点，其中i>=1；
- 深度为n的二叉树至多有2^n-1个节点，至少有n个节点，其中n>=1;
- 对于任意一棵二叉树而言，其叶子节点数目为N0，度为2的节点数目为N2，则满足以下关系：N0=N2+1；
- 具有n个节点的完全二叉树的深度：[logn]+1


### 1.2 二叉树遍历
二叉树的遍历（traversing binary tree)是指从根结点出发，按照某种次序依次访问二叉树中所有结点，使得每个结点被访问一次且仅被访问一次。二叉树的遍历次序大多是从头至尾、循环、双向等简单的遍历方式。树的结点之间不存在唯一的前驱和后继关系，在访问一个结点后，下一个被访问的结点面临着不同的选择。

三种遍历方式:
- 先序遍历 DLR (依次遍历：根结点，左子树，右子树)
- 中序遍历 LDR (依次遍历：左子树，根结点，右子树)
- 后序遍历 LRD (依次遍历：左子树，右子树，根结点)

## 2 二叉树遍历的实现


### 2.1 二叉树节点构造
```Java
    /**
     * 定义节点类
     */
    private static class BinNode {
        private Object element;
        private BinNode lChild;
        private BinNode rChild;

        public BinNode(Object element, BinNode lChild, BinNode rChild) {
            this.element = element;
            this.lChild = lChild;
            this.rChild = rChild;
        }
    }

    /**
     * 初始化一个二叉树（从叶节点到根节点层层构造）
     * @return
     */
    public static BinNode init() {
        BinNode h = new BinNode("H", null, null);
        BinNode i = new BinNode("I", null, null);
        BinNode d = new BinNode("D", h, null);
        BinNode e = new BinNode("E", null, null);
        BinNode f = new BinNode("F", i, null);
        BinNode g = new BinNode("G", null, null);
        BinNode b = new BinNode("B", d, e);
        BinNode c = new BinNode("C", f, g);
        BinNode a = new BinNode("A", b, c);
        return a;
    }
```


### 2.2 先序遍历

![](https://static.kevinchu.top/blog/public/20220620112345.png)
依次遍历：根结点，左子树，右子树
图中先序遍历结果：A B D H E C F I G

Java实现：
```Java
    /**
     * 先序遍历
     * @param node
     */
    public static void preOrder(BinNode node) {
        list.add(node); // 先将根节点存入list
        // 如果左子树不为空继续往左找，在递归调用方法的时候一直会将子树的根存入list，这就做到了先遍历根节点
        if (node.lChild != null) {
            preOrder(node.lChild);
        }
        // 无论走到哪一层，只要当前节点左子树为空，那么就可以在右子树上遍历，保证了根左右的遍历顺序
        if (node.rChild != null) {
            preOrder(node.rChild);
        }
    }
```

### 2.3 中序遍历

![](https://static.kevinchu.top/blog/public/20220620135006.png)
依次遍历：左子树，根结点，右子树
图中中序遍历结果：H D B E A I F C G

Java实现：
```Java
    /**
     * 中序遍历
     * @param node
     */
    public static void inOrder(BinNode node) {
        if (node.lChild != null) {
            inOrder(node.lChild);
        }
        list.add(node);
        if (node.rChild != null) {
            inOrder(node.rChild);
        }
    }
```


### 2.4 后序遍历

![](https://static.kevinchu.top/blog/public/20220620140506.png)
依次遍历：左子树，右子树，根节点
图中后序遍历结果：H D E B I F G C A


Java实现：
```Java
    /**
     * 后序遍历
     * @param node
     */
    public static void postOrder(BinNode node) {
        if (node.lChild != null) {
            postOrder(node.lChild);
        }
        if (node.rChild != null) {
            postOrder(node.rChild);
        }
        list.add(node);
    }
```

### 2.5 完整测试代码

```Java
package com.zhu.common.test;


import java.util.ArrayList;
import java.util.List;

public class BinaryTreeTest {

    private static List<BinNode> list = new ArrayList<>();

    /**
     * 定义节点类
     */
    private static class BinNode {
        private Object element;
        private BinNode lChild;
        private BinNode rChild;

        public BinNode(Object element, BinNode lChild, BinNode rChild) {
            this.element = element;
            this.lChild = lChild;
            this.rChild = rChild;
        }
    }

    /**
     * 初始化一个二叉树（从叶节点到根节点层层构造）
     *
     * @return
     */
    public static BinNode init() {
        BinNode h = new BinNode("H", null, null);
        BinNode i = new BinNode("I", null, null);
        BinNode d = new BinNode("D", h, null);
        BinNode e = new BinNode("E", null, null);
        BinNode f = new BinNode("F", i, null);
        BinNode g = new BinNode("G", null, null);
        BinNode b = new BinNode("B", d, e);
        BinNode c = new BinNode("C", f, g);
        BinNode a = new BinNode("A", b, c);
        return a;
    }

    /**
     * 先序遍历
     *
     * @param node
     */
    public static void preOrder(BinNode node) {
        list.add(node); // 先将根节点存入list
        // 如果左子树不为空继续往左找，在递归调用方法的时候一直会将子树的根存入list，这就做到了先遍历根节点
        if (node.lChild != null) {
            preOrder(node.lChild);
        }
        // 无论走到哪一层，只要当前节点左子树为空，那么就可以在右子树上遍历，保证了根左右的遍历顺序
        if (node.rChild != null) {
            preOrder(node.rChild);
        }
    }

    /**
     * 中序遍历
     *
     * @param node
     */
    public static void inOrder(BinNode node) {
        if (node.lChild != null) {
            inOrder(node.lChild);
        }
        list.add(node);
        if (node.rChild != null) {
            inOrder(node.rChild);
        }
    }

    /**
     * 后序遍历
     *
     * @param node
     */
    public static void postOrder(BinNode node) {
        if (node.lChild != null) {
            postOrder(node.lChild);
        }
        if (node.rChild != null) {
            postOrder(node.rChild);
        }
        list.add(node);
    }


    public static void main(String[] args) {
        BinNode tree = init();
        preOrder(tree);

        System.out.println("先序遍历结果：");
        list.forEach(n -> {
            System.out.print(n.element + " ");
        });
        list.clear();
        System.out.println();

        inOrder(tree);
        System.out.println("中序遍历结果：");
        list.forEach(n -> {
            System.out.print(n.element + " ");
        });
        list.clear();
        System.out.println();

        postOrder(tree);
        System.out.println("后序遍历结果：");
        list.forEach(n -> {
            System.out.print(n.element + " ");
        });
    }
}

```

```Java
先序遍历结果：
A B D H E C F I G 
中序遍历结果：
H D B E A I F C G 
后序遍历结果：
H D E B I F G C A 
```

## 3 扩展

- 已知前序遍历序列和中序遍历序列，可以唯一确定一棵二叉树

- 已知后序遍历序列和中序遍历序列，可以唯一确定一棵二叉树

- 但是已知前序遍历序列和后序遍历序列，不能确定一棵二叉树

- 即：没有中序遍历序列的情况下无法确定一颗二叉树