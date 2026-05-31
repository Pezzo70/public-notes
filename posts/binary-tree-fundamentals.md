---
title: "Binary Tree Fundamentals: A Complete Guide with C# Implementation"
date: 2025-08-20
tags:
  - post
  - DSA
  - data-structures
  - algorithms
  - csharp
description: "Explore binary trees from fundamentals to advanced operations with complete C# implementations and practical examples."
draft: false
---

A **Binary Tree** is a hierarchical data structure where each node has at most two children, typically referred to as the left child and right child. This fundamental data structure serves as the foundation for many advanced tree-based algorithms and data structures.

## Structure

```
     Root
    /    \
   A      B
  / \    / \
 C   D  E   F
```

## Key Concepts

- **Root**: The topmost node of the tree
- **Parent**: A node that has child nodes
- **Child**: A node directly connected to a parent
- **Leaf**: A node with no children
- **Internal Node**: A node with at least one child
- **Height**: The length of the longest path from root to leaf
- **Depth**: The length of the path from root to a specific node

## Basic Implementation

```csharp
public class TreeNode<T>
{
    public T Data { get; set; }
    public TreeNode<T> Left { get; set; }
    public TreeNode<T> Right { get; set; }

    public TreeNode(T data)
    {
        Data = data;
        Left = null;
        Right = null;
    }
}

public class BinaryTree<T>
{
    public TreeNode<T> Root { get; private set; }

    public BinaryTree()
    {
        Root = null;
    }

    public void Insert(T data)
    {
        Root = InsertRecursive(Root, data);
    }

    private TreeNode<T> InsertRecursive(TreeNode<T> node, T data)
    {
        if (node == null)
        {
            return new TreeNode<T>(data);
        }

        // For simplicity, we'll use a simple comparison
        // In practice, you might want to implement IComparable<T>
        if (data.GetHashCode() < node.Data.GetHashCode())
        {
            node.Left = InsertRecursive(node.Left, data);
        }
        else
        {
            node.Right = InsertRecursive(node.Right, data);
        }

        return node;
    }
}
```

## Types of Binary Trees

### 1. Full Binary Tree
Every node has either 0 or 2 children.

```csharp
public bool IsFullBinaryTree(TreeNode<T> node)
{
    if (node == null)
        return true;

    // If leaf node
    if (node.Left == null && node.Right == null)
        return true;

    // If both children exist
    if (node.Left != null && node.Right != null)
        return IsFullBinaryTree(node.Left) && IsFullBinaryTree(node.Right);

    return false;
}
```

### 2. Complete Binary Tree
All levels are filled except possibly the last level, which is filled from left to right.

```csharp
public bool IsCompleteBinaryTree(TreeNode<T> root)
{
    if (root == null)
        return true;

    Queue<TreeNode<T>> queue = new Queue<TreeNode<T>>();
    queue.Enqueue(root);
    bool flag = false;

    while (queue.Count > 0)
    {
        TreeNode<T> temp = queue.Dequeue();

        if (temp.Left != null)
        {
            if (flag)
                return false;
            queue.Enqueue(temp.Left);
        }
        else
        {
            flag = true;
        }

        if (temp.Right != null)
        {
            if (flag)
                return false;
            queue.Enqueue(temp.Right);
        }
        else
        {
            flag = true;
        }
    }

    return true;
}
```

### 3. Perfect Binary Tree
All internal nodes have 2 children and all leaves are at the same level.

```csharp
public bool IsPerfectBinaryTree(TreeNode<T> root)
{
    int depth = GetDepth(root);
    return IsPerfectBinaryTreeHelper(root, depth, 0);
}

private bool IsPerfectBinaryTreeHelper(TreeNode<T> node, int depth, int level)
{
    if (node == null)
        return true;

    if (node.Left == null && node.Right == null)
        return depth == level + 1;

    if (node.Left == null || node.Right == null)
        return false;

    return IsPerfectBinaryTreeHelper(node.Left, depth, level + 1) &&
           IsPerfectBinaryTreeHelper(node.Right, depth, level + 1);
}

private int GetDepth(TreeNode<T> node)
{
    int depth = 0;
    while (node != null)
    {
        depth++;
        node = node.Left;
    }
    return depth;
}
```

### 4. Balanced Binary Tree
The height difference between left and right subtrees is at most 1.

```csharp
public bool IsBalanced(TreeNode<T> root)
{
    return CheckHeight(root) != -1;
}

private int CheckHeight(TreeNode<T> node)
{
    if (node == null)
        return 0;

    int leftHeight = CheckHeight(node.Left);
    if (leftHeight == -1)
        return -1;

    int rightHeight = CheckHeight(node.Right);
    if (rightHeight == -1)
        return -1;

    if (Math.Abs(leftHeight - rightHeight) > 1)
        return -1;

    return Math.Max(leftHeight, rightHeight) + 1;
}
```

## Traversal Methods

### 1. Inorder (Left → Root → Right)
```csharp
public void InorderTraversal(TreeNode<T> node)
{
    if (node != null)
    {
        InorderTraversal(node.Left);
        Console.Write(node.Data + " ");
        InorderTraversal(node.Right);
    }
}

// Iterative version
public void InorderTraversalIterative(TreeNode<T> root)
{
    if (root == null)
        return;

    Stack<TreeNode<T>> stack = new Stack<TreeNode<T>>();
    TreeNode<T> current = root;

    while (current != null || stack.Count > 0)
    {
        while (current != null)
        {
            stack.Push(current);
            current = current.Left;
        }

        current = stack.Pop();
        Console.Write(current.Data + " ");
        current = current.Right;
    }
}
```

### 2. Preorder (Root → Left → Right)
```csharp
public void PreorderTraversal(TreeNode<T> node)
{
    if (node != null)
    {
        Console.Write(node.Data + " ");
        PreorderTraversal(node.Left);
        PreorderTraversal(node.Right);
    }
}

// Iterative version
public void PreorderTraversalIterative(TreeNode<T> root)
{
    if (root == null)
        return;

    Stack<TreeNode<T>> stack = new Stack<TreeNode<T>>();
    stack.Push(root);

    while (stack.Count > 0)
    {
        TreeNode<T> current = stack.Pop();
        Console.Write(current.Data + " ");

        if (current.Right != null)
            stack.Push(current.Right);
        if (current.Left != null)
            stack.Push(current.Left);
    }
}
```

### 3. Postorder (Left → Right → Root)
```csharp
public void PostorderTraversal(TreeNode<T> node)
{
    if (node != null)
    {
        PostorderTraversal(node.Left);
        PostorderTraversal(node.Right);
        Console.Write(node.Data + " ");
    }
}

// Iterative version
public void PostorderTraversalIterative(TreeNode<T> root)
{
    if (root == null)
        return;

    Stack<TreeNode<T>> stack1 = new Stack<TreeNode<T>>();
    Stack<TreeNode<T>> stack2 = new Stack<TreeNode<T>>();
    stack1.Push(root);

    while (stack1.Count > 0)
    {
        TreeNode<T> current = stack1.Pop();
        stack2.Push(current);

        if (current.Left != null)
            stack1.Push(current.Left);
        if (current.Right != null)
            stack1.Push(current.Right);
    }

    while (stack2.Count > 0)
    {
        Console.Write(stack2.Pop().Data + " ");
    }
}
```

### 4. Level Order (Breadth-First)
```csharp
public void LevelOrderTraversal(TreeNode<T> root)
{
    if (root == null)
        return;

    Queue<TreeNode<T>> queue = new Queue<TreeNode<T>>();
    queue.Enqueue(root);

    while (queue.Count > 0)
    {
        TreeNode<T> current = queue.Dequeue();
        Console.Write(current.Data + " ");

        if (current.Left != null)
            queue.Enqueue(current.Left);
        if (current.Right != null)
            queue.Enqueue(current.Right);
    }
}
```

## Common Operations

### Height of Tree
```csharp
public int GetHeight(TreeNode<T> node)
{
    if (node == null)
        return -1;

    int leftHeight = GetHeight(node.Left);
    int rightHeight = GetHeight(node.Right);

    return Math.Max(leftHeight, rightHeight) + 1;
}
```

### Count Nodes
```csharp
public int CountNodes(TreeNode<T> node)
{
    if (node == null)
        return 0;

    return 1 + CountNodes(node.Left) + CountNodes(node.Right);
}
```

### Count Leaf Nodes
```csharp
public int CountLeafNodes(TreeNode<T> node)
{
    if (node == null)
        return 0;

    if (node.Left == null && node.Right == null)
        return 1;

    return CountLeafNodes(node.Left) + CountLeafNodes(node.Right);
}
```

### Find Maximum Value
```csharp
public T FindMax(TreeNode<T> root)
{
    if (root == null)
        throw new InvalidOperationException("Tree is empty");

    T max = root.Data;
    T leftMax = root.Left != null ? FindMax(root.Left) : default(T);
    T rightMax = root.Right != null ? FindMax(root.Right) : default(T);

    if (Comparer<T>.Default.Compare(leftMax, max) > 0)
        max = leftMax;
    if (Comparer<T>.Default.Compare(rightMax, max) > 0)
        max = rightMax;

    return max;
}
```

## Applications

- **Binary Search Trees**: Efficient searching and sorting
- **Expression Trees**: Representing mathematical expressions
- **Huffman Trees**: Data compression
- **Decision Trees**: Machine learning and decision making
- **AVL Trees**: Self-balancing binary search trees
- **Red-Black Trees**: Another type of self-balancing tree

## Related Concepts

- [[trie-data-structure]] - A tree-like data structure for string operations
- [[symbol-tables]] - Data structures for symbol tables

## Conclusion

Binary trees are fundamental data structures that form the basis for many advanced algorithms and data structures. Understanding their implementation and traversal methods is crucial for any software engineer working with data structures and algorithms.

The C# implementations provided here demonstrate both recursive and iterative approaches, giving you flexibility in choosing the most appropriate method for your specific use case.

---

*This post covers the fundamentals of binary trees with complete C# implementations. For more advanced topics like AVL trees, Red-Black trees, and B-trees, stay tuned for future posts.*
