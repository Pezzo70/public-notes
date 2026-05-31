---
title: "Symbol Tables: Complete Implementation Guide in C#"
date: 2025-08-20
tags:
  - post
  - DSA
  - data-structures
  - algorithms
  - csharp
description: "Explore symbol tables from basic implementations to advanced data structures with complete C# code examples and performance analysis."
draft: false
---

A **Symbol Table** is a data structure that associates keys (symbols) with values. It's fundamental in compilers, interpreters, and many other applications where you need to store and retrieve information efficiently. This post covers various implementations from simple arrays to advanced tree-based structures.

## Core Operations

Every symbol table implementation must support these basic operations:

- **Insert**: Add a key-value pair
- **Search/Get**: Retrieve the value associated with a key
- **Delete**: Remove a key-value pair
- **Contains**: Check if a key exists

## Implementation Options

### 1. Unordered Array Implementation

The simplest implementation using arrays, suitable for small datasets.

```csharp
public class UnorderedArraySymbolTable<TKey, TValue>
{
    private TKey[] keys;
    private TValue[] values;
    private int size;
    private readonly int capacity;

    public UnorderedArraySymbolTable(int capacity = 100)
    {
        this.capacity = capacity;
        keys = new TKey[capacity];
        values = new TValue[capacity];
        size = 0;
    }

    public void Put(TKey key, TValue value)
    {
        // Check if key already exists
        for (int i = 0; i < size; i++)
        {
            if (EqualityComparer<TKey>.Default.Equals(keys[i], key))
            {
                values[i] = value;
                return;
            }
        }

        // Add new key-value pair
        if (size < capacity)
        {
            keys[size] = key;
            values[size] = value;
            size++;
        }
        else
        {
            throw new InvalidOperationException("Symbol table is full");
        }
    }

    public TValue Get(TKey key)
    {
        for (int i = 0; i < size; i++)
        {
            if (EqualityComparer<TKey>.Default.Equals(keys[i], key))
            {
                return values[i];
            }
        }
        throw new KeyNotFoundException($"Key '{key}' not found");
    }

    public bool Contains(TKey key)
    {
        for (int i = 0; i < size; i++)
        {
            if (EqualityComparer<TKey>.Default.Equals(keys[i], key))
            {
                return true;
            }
        }
        return false;
    }

    public void Delete(TKey key)
    {
        for (int i = 0; i < size; i++)
        {
            if (EqualityComparer<TKey>.Default.Equals(keys[i], key))
            {
                // Move last element to current position
                keys[i] = keys[size - 1];
                values[i] = values[size - 1];
                size--;
                return;
            }
        }
        throw new KeyNotFoundException($"Key '{key}' not found");
    }

    public int Size => size;
    public bool IsEmpty => size == 0;
}
```

**Time Complexity**: O(n) for all operations

### 2. Ordered Array with Binary Search

More efficient for search operations when data is kept sorted.

```csharp
public class OrderedArraySymbolTable<TKey, TValue> where TKey : IComparable<TKey>
{
    private TKey[] keys;
    private TValue[] values;
    private int size;
    private readonly int capacity;

    public OrderedArraySymbolTable(int capacity = 100)
    {
        this.capacity = capacity;
        keys = new TKey[capacity];
        values = new TValue[capacity];
        size = 0;
    }

    public void Put(TKey key, TValue value)
    {
        int rank = Rank(key);
        
        // Key already exists
        if (rank < size && EqualityComparer<TKey>.Default.Equals(keys[rank], key))
        {
            values[rank] = value;
            return;
        }

        // Insert new key-value pair
        if (size < capacity)
        {
            // Shift elements to make room
            for (int i = size; i > rank; i--)
            {
                keys[i] = keys[i - 1];
                values[i] = values[i - 1];
            }
            
            keys[rank] = key;
            values[rank] = value;
            size++;
        }
        else
        {
            throw new InvalidOperationException("Symbol table is full");
        }
    }

    public TValue Get(TKey key)
    {
        if (IsEmpty)
            throw new KeyNotFoundException("Symbol table is empty");

        int rank = Rank(key);
        if (rank < size && EqualityComparer<TKey>.Default.Equals(keys[rank], key))
        {
            return values[rank];
        }
        throw new KeyNotFoundException($"Key '{key}' not found");
    }

    public bool Contains(TKey key)
    {
        int rank = Rank(key);
        return rank < size && EqualityComparer<TKey>.Default.Equals(keys[rank], key);
    }

    public void Delete(TKey key)
    {
        int rank = Rank(key);
        if (rank < size && EqualityComparer<TKey>.Default.Equals(keys[rank], key))
        {
            // Shift elements to fill the gap
            for (int i = rank; i < size - 1; i++)
            {
                keys[i] = keys[i + 1];
                values[i] = values[i + 1];
            }
            size--;
        }
        else
        {
            throw new KeyNotFoundException($"Key '{key}' not found");
        }
    }

    private int Rank(TKey key)
    {
        int low = 0;
        int high = size - 1;

        while (low <= high)
        {
            int mid = low + (high - low) / 2;
            int comparison = key.CompareTo(keys[mid]);

            if (comparison < 0)
                high = mid - 1;
            else if (comparison > 0)
                low = mid + 1;
            else
                return mid;
        }

        return low;
    }

    public int Size => size;
    public bool IsEmpty => size == 0;
}
```

**Time Complexity**: O(log n) for search, O(n) for insert/delete

### 3. Binary Search Tree Implementation

A more balanced approach with good average-case performance.

```csharp
public class BinarySearchTreeSymbolTable<TKey, TValue> where TKey : IComparable<TKey>
{
    private class Node
    {
        public TKey Key { get; set; }
        public TValue Value { get; set; }
        public Node Left { get; set; }
        public Node Right { get; set; }
        public int Count { get; set; }

        public Node(TKey key, TValue value, int count)
        {
            Key = key;
            Value = value;
            Count = count;
        }
    }

    private Node root;

    public void Put(TKey key, TValue value)
    {
        root = Put(root, key, value);
    }

    private Node Put(Node node, TKey key, TValue value)
    {
        if (node == null)
            return new Node(key, value, 1);

        int comparison = key.CompareTo(node.Key);
        if (comparison < 0)
            node.Left = Put(node.Left, key, value);
        else if (comparison > 0)
            node.Right = Put(node.Right, key, value);
        else
            node.Value = value;

        node.Count = 1 + Size(node.Left) + Size(node.Right);
        return node;
    }

    public TValue Get(TKey key)
    {
        Node node = Get(root, key);
        if (node == null)
            throw new KeyNotFoundException($"Key '{key}' not found");
        return node.Value;
    }

    private Node Get(Node node, TKey key)
    {
        if (node == null)
            return null;

        int comparison = key.CompareTo(node.Key);
        if (comparison < 0)
            return Get(node.Left, key);
        else if (comparison > 0)
            return Get(node.Right, key);
        else
            return node;
    }

    public bool Contains(TKey key)
    {
        return Get(root, key) != null;
    }

    public void Delete(TKey key)
    {
        root = Delete(root, key);
    }

    private Node Delete(Node node, TKey key)
    {
        if (node == null)
            return null;

        int comparison = key.CompareTo(node.Key);
        if (comparison < 0)
            node.Left = Delete(node.Left, key);
        else if (comparison > 0)
            node.Right = Delete(node.Right, key);
        else
        {
            if (node.Right == null)
                return node.Left;
            if (node.Left == null)
                return node.Right;

            Node temp = node;
            node = Min(temp.Right);
            node.Right = DeleteMin(temp.Right);
            node.Left = temp.Left;
        }

        node.Count = 1 + Size(node.Left) + Size(node.Right);
        return node;
    }

    private Node Min(Node node)
    {
        if (node.Left == null)
            return node;
        return Min(node.Left);
    }

    private Node DeleteMin(Node node)
    {
        if (node.Left == null)
            return node.Right;
        node.Left = DeleteMin(node.Left);
        node.Count = 1 + Size(node.Left) + Size(node.Right);
        return node;
    }

    private int Size(Node node)
    {
        return node?.Count ?? 0;
    }

    public int Size => Size(root);
    public bool IsEmpty => root == null;
}
```

**Time Complexity**: O(log n) average, O(n) worst case

### 4. Hash Table Implementation

Fastest average-case performance for most operations.

```csharp
public class HashTableSymbolTable<TKey, TValue>
{
    private class Entry
    {
        public TKey Key { get; set; }
        public TValue Value { get; set; }
        public Entry Next { get; set; }

        public Entry(TKey key, TValue value, Entry next)
        {
            Key = key;
            Value = value;
            Next = next;
        }
    }

    private Entry[] buckets;
    private int size;
    private readonly int capacity;
    private const double LoadFactor = 0.75;

    public HashTableSymbolTable(int capacity = 16)
    {
        this.capacity = capacity;
        buckets = new Entry[capacity];
        size = 0;
    }

    public void Put(TKey key, TValue value)
    {
        if (size >= capacity * LoadFactor)
        {
            Resize(2 * capacity);
        }

        int index = Hash(key);
        Entry current = buckets[index];

        // Check if key already exists
        while (current != null)
        {
            if (EqualityComparer<TKey>.Default.Equals(current.Key, key))
            {
                current.Value = value;
                return;
            }
            current = current.Next;
        }

        // Add new entry
        buckets[index] = new Entry(key, value, buckets[index]);
        size++;
    }

    public TValue Get(TKey key)
    {
        int index = Hash(key);
        Entry current = buckets[index];

        while (current != null)
        {
            if (EqualityComparer<TKey>.Default.Equals(current.Key, key))
            {
                return current.Value;
            }
            current = current.Next;
        }

        throw new KeyNotFoundException($"Key '{key}' not found");
    }

    public bool Contains(TKey key)
    {
        int index = Hash(key);
        Entry current = buckets[index];

        while (current != null)
        {
            if (EqualityComparer<TKey>.Default.Equals(current.Key, key))
            {
                return true;
            }
            current = current.Next;
        }

        return false;
    }

    public void Delete(TKey key)
    {
        int index = Hash(key);
        Entry current = buckets[index];
        Entry previous = null;

        while (current != null)
        {
            if (EqualityComparer<TKey>.Default.Equals(current.Key, key))
            {
                if (previous == null)
                {
                    buckets[index] = current.Next;
                }
                else
                {
                    previous.Next = current.Next;
                }
                size--;
                return;
            }
            previous = current;
            current = current.Next;
        }

        throw new KeyNotFoundException($"Key '{key}' not found");
    }

    private int Hash(TKey key)
    {
        return Math.Abs(key.GetHashCode()) % capacity;
    }

    private void Resize(int newCapacity)
    {
        HashTableSymbolTable<TKey, TValue> newTable = new HashTableSymbolTable<TKey, TValue>(newCapacity);
        
        for (int i = 0; i < capacity; i++)
        {
            Entry current = buckets[i];
            while (current != null)
            {
                newTable.Put(current.Key, current.Value);
                current = current.Next;
            }
        }

        buckets = newTable.buckets;
        capacity = newCapacity;
    }

    public int Size => size;
    public bool IsEmpty => size == 0;
}
```

**Time Complexity**: O(1) average, O(n) worst case

## Performance Comparison

| Implementation | Insert | Search | Delete | Space |
|---------------|--------|--------|--------|-------|
| Unordered Array | O(n) | O(n) | O(n) | O(n) |
| Ordered Array | O(n) | O(log n) | O(n) | O(n) |
| Binary Search Tree | O(log n) | O(log n) | O(log n) | O(n) |
| Hash Table | O(1) | O(1) | O(1) | O(n) |

## Applications

### 1. Compilers
```csharp
public class CompilerSymbolTable
{
    private readonly HashTableSymbolTable<string, SymbolInfo> symbols = new();

    public void AddVariable(string name, string type, int scope)
    {
        symbols.Put(name, new SymbolInfo { Type = type, Scope = scope });
    }

    public SymbolInfo LookupVariable(string name)
    {
        return symbols.Get(name);
    }
}

public class SymbolInfo
{
    public string Type { get; set; }
    public int Scope { get; set; }
    public int LineNumber { get; set; }
}
```

### 2. Configuration Systems
```csharp
public class ConfigurationManager
{
    private readonly HashTableSymbolTable<string, object> config = new();

    public void SetValue(string key, object value)
    {
        config.Put(key, value);
    }

    public T GetValue<T>(string key, T defaultValue = default)
    {
        if (config.Contains(key))
        {
            return (T)config.Get(key);
        }
        return defaultValue;
    }
}
```

## Advanced Symbol Tables

### Red-Black Trees
Self-balancing binary search trees that guarantee O(log n) performance.

### B-Trees
Tree data structures optimized for systems that read and write large blocks of data.

### [[trie-data-structure]] (Prefix Trees)
Efficient for string keys and prefix-based operations.

## Related Concepts

- [[binary-tree-fundamentals]] - Fundamental tree structure
- [[trie-data-structure]] - Specialized tree for string operations
- Hash Tables - Fast key-value storage
- Binary Search - Efficient search algorithm

## Conclusion

Symbol tables are fundamental data structures that form the backbone of many applications. The choice of implementation depends on your specific requirements:

- **Hash Tables**: Best for general-purpose use with good average-case performance
- **Binary Search Trees**: Good for ordered operations and range queries
- **Ordered Arrays**: Efficient for small datasets with frequent searches
- **Unordered Arrays**: Simple implementation for small datasets

Understanding these implementations helps you make informed decisions about which data structure to use in your applications.

---

*This post covers the fundamental implementations of symbol tables in C#. For more advanced topics like Red-Black trees, B-trees, and specialized symbol table implementations, stay tuned for future posts.*
