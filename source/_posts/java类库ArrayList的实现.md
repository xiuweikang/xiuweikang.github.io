title: java类库ArrayList的实现
date: 2014-10-11 15:43:21
tags: 数据结构
---

ArrayList实现
<!-- more -->
```java
import java.util.Iterator;

/**
 * 类名：MyArrayList
 * 说明：和ArrayList源码基本差不多
 */
public class MyArrayList<AnyType> implements Iterable<AnyType>{
	private static final int DEFAULT_CAPACITY = 10;
	private AnyType[] theItems;
	private int theSize;
	public MyArrayList(){
		clear();
	}
	public void clear(){
		theSize = 0;
		ensureCapacity(DEFAULT_CAPACITY);
	}
	public int size(){
		return theSize;
	}
	public void trimToSize(){
		ensureCapacity(theSize);
	}
	public boolean isEmpty(){
		return theSize == 0;
	}
	public AnyType get(int index){
		if(index < 0||index >= theSize)
			throw new ArrayIndexOutOfBoundsException();
		return theItems[index];
	}
	public AnyType set(int index,AnyType newVal){
		if(index < 0||index >= theSize)
			throw new ArrayIndexOutOfBoundsException();
		AnyType old = theItems[index];
		theItems[index] = newVal;
		return old;
	}
	public void add(int index,AnyType x){
		if(theItems.length == index)
			ensureCapacity(2 * size() + 1);
		for(int i = theSize; i > index; i--)
			theItems[i] = theItems[i - 1];
		theItems[index] = x;
		theSize++;
	}
	public boolean add(AnyType x){
		add(theSize,x);
		return true;
	}
	public AnyType remove(int index){
		if(index < 0||index >= theSize)
			throw new ArrayIndexOutOfBoundsException();
		AnyType old = theItems[index];
		for(int i = index; i < theSize;i++)
			theItems[i] = theItems[i+1];	
		theSize--;
		return old;
	}
	public void ensureCapacity(int newCapacity){
		if(newCapacity < theSize)
			return;
		AnyType[] old = theItems;
		theItems = (AnyType[]) new Object[newCapacity];
		for(int i = 0;i < theSize;i++)
			theItems[i] = old[i];
	}
	public Iterator<AnyType> iterator() {
		// TODO Auto-generated method stub
		return new Iterator(){
			private int current = 0;
			@Override
			public boolean hasNext() {
				return current < theSize;
			}
			@Override
			public AnyType next() {
				return theItems[current++];
			}

			@Override
			public void remove() {
				MyArrayList.this.remove(--current);
			}
			
		};
		
	}
	public String toString(){
		String s = new String();
		for(int i = 0;i < theSize;i++)
			s += get(i)+" ";
		return s;
	}
	
	/**
	 * 方法名：MyArrayList.java
	 * 说明：
	 */
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		MyArrayList<Integer> list = new MyArrayList<Integer>();
		list.add(1);
		list.add(2);
		list.add(3);
		list.add(4);
		list.add(5);
		System.out.println(list);
		list.remove(1);
		System.out.println(list);
		list.set(1,2);
		System.out.println(list);
		Iterator ite = list.iterator();
		while(ite.hasNext()){
			System.out.println(ite.next()+" ");
		}
	}
}
```