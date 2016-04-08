title: BST的实现
date: 2014-10-21 15:45:02
tags: 数据结构
---
 平衡二叉树的实现 操作 遍历
<!-- more -->
```java
 * 文件名：BinaryTree.java
 * 时间：2014年10月23日下午8:27:34
 * 作者：修维康
 */
package chapter4;

import java.util.*;

class BinarySearchTree<AnyType extends Comparable<? super AnyType>> {
	private static class Node<AnyType> {
		Node(AnyType data, Node<AnyType> leftChild, Node<AnyType> rightChild) {
			this.data = data;
			this.leftChild = leftChild;
			this.rightChild = rightChild;
		}

		private AnyType data;
		private Node<AnyType> leftChild;
		private Node<AnyType> rightChild;
	}

	private Node<AnyType> root;

	BinarySearchTree(AnyType x) {

		root = new Node<AnyType>(x, null, null);
	}

	BinarySearchTree() {
		root = null;
	}

	public void makeEmpty() {
		root = null;
	}

	public boolean isEmpty() {
		return root == null;
	}

	public boolean contains(AnyType x) {
		return contains(x, root);
	}

	private boolean contains(AnyType x, Node<AnyType> t) {
		if (t == null)
			return false;

		int compareResult = x.compareTo(t.data);
		if (compareResult < 0)
			return contains(x, t.leftChild);
		else if (compareResult > 0)
			return contains(x, t.rightChild);
		else
			return true;
	}

	public AnyType findMin() {
		if (isEmpty())
			return null;
		return findMin(root).data;
	}

	// 递归是实现查找最大最小值
	/*
	 * public Node<AnyType> findMin(Node<AnyType> t){ if(t == null) return null;
	 * else if(t.leftChild == null) return t; return findMin(t.leftChild); }
	 * public Node<AnyType> findMax(Node<AnyType> t){ if(t == null) return null;
	 * else if(t.rightChild == null) return t; else findMax(t.rightChild); }
	 */

	// 非递归实现查找最大最小值
	private Node<AnyType> findMin(Node<AnyType> t) {
		if (t != null)
			while (t.leftChild != null) {
				t = t.leftChild;
			}
		return t;
	}

	private Node<AnyType> findMax(Node<AnyType> t) {
		if (t != null)
			while (t.rightChild != null) {
				t = t.rightChild;
			}
		return t;
	}

	public void insert(AnyType x) {
		root = insert(x, root);
	}

	private Node<AnyType> insert(AnyType x, Node<AnyType> t) {
		if (t == null)
			t = new Node<AnyType>(x, null, null);
		int compareResult = x.compareTo(t.data);

		if (compareResult < 0)
			t.leftChild = insert(x, t.leftChild);
		if (compareResult > 0)
			t.rightChild = insert(x, t.rightChild);

		return t;
	}

	public void remove(AnyType x) {
		root = remove(x, root);
	}

	private Node<AnyType> remove(AnyType x, Node<AnyType> t) {
		if (t == null)
			return t;// 没找到
		int compareResult = x.compareTo(t.data);
		if (compareResult < 0)
			t.leftChild = remove(x, t.leftChild);
		else if (compareResult > 0)
			t.rightChild = remove(x, t.rightChild);
		else if (t.leftChild != null && t.rightChild != null) {
			t.data = findMin(t.rightChild).data;// 如果这个节点有2个子节点则选右子树中元素最小的节点,赋值给它
			t.rightChild = remove(t.data, t.rightChild);// 同时递归右子树那个最小的节点
		} else
			t = (t.leftChild != null) ? t.leftChild : t.rightChild;

		return t;
	}

	public void printTree() {
		printTree5(root);
	}

	// 递归先序遍历
	private void printTree(Node<AnyType> t) {
		if (t != null) {
			System.out.println(t.data + " ");
			printTree(t.leftChild);
			printTree(t.rightChild);
		}
	}

	// 递归中序遍历 private void
	private void printTree1(Node<AnyType> t) {
		if (t != null) {
			printTree(t.leftChild);
			System.out.println(t.data + " ");
			printTree(t.rightChild);
		}
	}

	// 递归后序遍历
	private void printTree3(Node<AnyType> t) {
		if (t != null) {
			printTree(t.leftChild);
			printTree(t.rightChild);
			System.out.println(t.data + " ");
		}
	}

	// 非递归先序遍历

	private void printTree4(Node<AnyType> p) {
		Stack<Node> stack = new Stack<Node>();
		stack.push(p);
		while (!stack.empty()) {
			p = stack.pop();
			System.out.println(p.data); // 先右节点进栈，在左节点进栈，出来的时候顺序相反。
			if (p.rightChild != null)
				stack.push(p.rightChild);
			if (p.leftChild != null)
				stack.push(p.leftChild);
		}
	}

	// 非递归中序遍历
	private void printTree5(Node<AnyType> p) {
		Stack<Node<AnyType>> stack = new Stack<Node<AnyType>>();
		while (p != null || !stack.empty()) {
			// 和递归一样的思路，很好想
			if (p != null) {
				stack.push(p);
				p = p.leftChild;
			} else {
				p = stack.pop();
				System.out.println(p.data);
				p = p.rightChild;
			}
		}
	}

	// 非递归后序遍历为3中遍历中最复杂的一种，也是面试里经常问到的
	// 后序里面每个节点都要进两次栈
	private void printTree6(Node<AnyType> p) {
		Stack<Node<AnyType>> stack = new Stack<Node<AnyType>>();
		HashSet<Node<AnyType>> visited = new HashSet<Node<AnyType>>();// 通过一个容器来标记
		while (p != null || !stack.empty()) {
			if (p != null) {
				stack.push(p);
				visited.add(p);
				p = p.leftChild;
			} else if (!stack.empty()) {
				p = stack.pop();
				if (p != null && !visited.contains(p)) {
					System.out.println(p.data);
					p = null;
				} else {
					visited.remove(p);
					stack.push(p);
					p = p.rightChild;
				}
			}
		}
	}

	// 层序遍历
	private void printTree7(Node<AnyType> p) {
		LinkedList<Node<AnyType>> queue = new LinkedList<Node<AnyType>>();
		queue.push(p);
		while (!queue.isEmpty()) {
			p = queue.pop();
			System.out.println(p.data);
			if (p.leftChild != null)
				queue.add(p.leftChild);
			if (p.rightChild != null)
				queue.add(p.rightChild);
		}
	}

}

/**
 * 类名：BinarySearchTreeTest 说明：查找二叉树
 */
public class BinarySearchTreeTest {

	/**
	 * 方法名：main 说明：二叉查找树数据结构的测试
	 */
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		BinarySearchTree<Integer> st = new BinarySearchTree<Integer>(6);
		st.insert(15);
		st.insert(6);
		st.insert(3);
		st.insert(7);
		st.insert(2);
		st.insert(4);
		st.insert(13);
		st.insert(9);
		st.insert(18);
		st.insert(17);
		st.insert(20);
		// System.out.println(tree.contains(1));
		// System.out.println(tree.contains(5));
		// tree.remove(4);
		st.printTree();

	}

}
```