title: java类库LinkedList的实现
date: 2014-10-12 15:43:37
tags: 数据结构
---
LinkedList实现
<!-- more -->
```java
import java.util.Iterator;

/**
 * 类名：MyLinkedList 说明：LinkedList的基本实现
 */
public class MyLinkedList<AnyType> implements Iterable {
	private int theSize = 0;
	private int modCount = 0;
	private Node<AnyType> beginMarker;
	private Node<AnyType> endMarker;

	private static class Node<AnyType> {
		public Node(AnyType data, Node<AnyType> prev, Node<AnyType> next) {
			this.data = data;
			this.prev = prev;
			this.next = next;
		}

		public AnyType data;
		public Node<AnyType> prev;
		public Node<AnyType> next;
	}

	public MyLinkedList() {
		clear();
	}

	public void clear() {
		beginMarker = new Node<AnyType>(null, null, null);
		endMarker = new Node<AnyType>(null, beginMarker, null);
		beginMarker.next = endMarker;

		theSize = 0;
		modCount++;
	}

	public boolean isEmpty() {
		return theSize == 0;
	}

	public int size() {
		return theSize;
	}

	public boolean add(AnyType x) {
		add(theSize, x);
		return true;
	}

	public void add(int index, AnyType x) {
		addBefore(getNode(index), x);
	}

	public AnyType get(int index) {
		return getNode(index).data;
	}

	private Node<AnyType> getNode(int index) {
		if (index < 0 || index > theSize)
			throw new IndexOutOfBoundsException();
		Node<AnyType> p;
		if (index < size() / 2) {
			p = beginMarker.next;
			for (int i = 0; i < index; i++) {
				p = p.next;
			}
		} else {
			p = endMarker;
			for (int i = theSize; i > index; i--)
				p = p.prev;
		}
		return p;
	}

	private void addBefore(Node<AnyType> p, AnyType x) {
		Node<AnyType> newNode = new Node(x, p.prev, p);
		newNode.prev.next = newNode;
		p.prev = newNode;
		theSize++;
		modCount++;
	}

	private AnyType remove(Node<AnyType> p) {
		AnyType removed = p.data;
		p.prev.next = p.next;
		p.next.prev = p.prev;
		theSize--;
		modCount++;
		return removed;
	}

	public AnyType set(int index, AnyType x) {
		AnyType old = getNode(index).data;
		getNode(index).data = x;
		return old;
	}

	public AnyType remove(int index) {
		return remove(getNode(index));
	}

	@Override
	public Iterator iterator() {
		// TODO Auto-generated method stub
		return new Iterator() {//匿名内部类实现，jdk里是返回重新写的一个private类。
			private Node<AnyType> current = beginMarker.next;
			private int expectedModCount = modCount;
			private boolean okToRemove = false;

			public boolean hasNext() {
				return current != endMarker;
			}

			public AnyType next() {
				if (modCount != expectedModCount) {
					throw new java.util.ConcurrentModificationException();
				}
				if (!hasNext())
					throw new java.util.NoSuchElementException();
				AnyType d = current.data;
				current = current.next;
				okToRemove = true;
				return d;
			}

			public void remove() {
				if (modCount != expectedModCount) {
					throw new java.util.ConcurrentModificationException();
				}
				if (!okToRemove)
					throw new IllegalStateException();
				MyLinkedList.this.remove(current.prev);
				okToRemove = false;
				expectedModCount++;
			}

		};

	}

	public String toString() {
		String s = new String();
		for (int i = 0; i < theSize; i++)
			s += get(i) + " ";
		return s;

	}

	/**
	 * 方法名：MyLinkedList.java 说明：测试
	 */
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		MyLinkedList<Integer> list = new MyLinkedList();
		list.add(1);
		list.add(2);
		list.add(3);
		list.add(4);
		list.add(5);
		System.out.println(list);
		list.remove(1);
		System.out.println(list);
		list.set(1, 2);
		System.out.println(list);
		Iterator ite = list.iterator();
		while (ite.hasNext()) {
			System.out.println(ite.next() + " ");
		}
	}
}
```