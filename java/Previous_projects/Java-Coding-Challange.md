---
title: "Java Coding Challange"
date: 2023-08-15T07:24:07Z
draft: false
---



## Write Program in Java to create threads using the Runnable interface
```
public class RunnableInterface implements Runnable {

//Declaring a count variable
private int count;

	//run() method this method tells what the thread will do  
     public void run() {
         for (int i = 1; i <= 5; i++) {
             System.out.println("Ninja " + count + " count: " + i);
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
     }

	//Setter for count variable
     public void setCount(int count) {
        this.count = count;
    }
}
```

```
public class Main {

public static void main(String[] args) {

                //Creating object of the interfaces and setting the count
         RunnableInterface runnableInterface1 = new RunnableInterface();
         runnableInterface1.setCount(1);


         RunnableInterface runnableInterface2 = new RunnableInterface();
         runnableInterface2.setCount(2);

		//Creating object for the thread to use interface’s object
         Thread myThread1 = new Thread(runnableInterface1);
         Thread myThread2 = new Thread(runnableInterface2);


		// Now starting the thread by using start() method
        myThread1.start();
         myThread2.start();

	}
}
```
Expected output
```
Ninja 1 count: 1
Ninja 2 count: 1
Ninja 1 count: 2
Ninja 2 count: 2
Ninja 1 count: 3
Ninja 2 count: 3
Ninja 1 count: 4
Ninja 2 count: 4
Ninja 1 count: 5
Ninja 2 count: 5
```

## Write Program in Java to create threads using the Callable interface


```

import java.util.concurrent.Callable;  
import java.util.Random;  
import java.util.concurrent.FutureTask;  

class JavaCallable implements Callable {

  @Override  
  public Object call() throws Exception {

    // Creating an object of the  Random class   
    Random randObj = new Random();  
  
    // generating a random number between 0 to 9  
    Integer randNo = randObj.nextInt(10);  
  
    // the thread is delayed for some random time  
    Thread.sleep(randNo * 1000);  
  
    // return the object that contains the   
    // generated random number  
    return randNo;  
  }  
}  

```

```
public class Main {

  public static void main(String argvs[]) throws Exception  
  {  
    
    // creating an array of 10 objects of the FutureTask class  
    FutureTask[] randomNoTasks = new FutureTask[10];  
    
    // loop for spawning 10 threads  
    for (int j = 0; j < 10; j++) {
      // Creating a new object of the JavaCallable class  
      Callable callable = new JavaCallable();  
    
      // Creating the FutureTask with Callable  
      randomNoTasks[j] = new FutureTask(callable);  
    
      // Since FutureTask implements Runnable,   
      // one can create a Thread  
      // with a FutureTask object  
      Thread th = new Thread(randomNoTasks[j]);  
      th.start();  
    }  
    
    // loop for receiving the random numbers  
    for (int j = 0; j < 10; j++) {
  
      // invoking the get() method  
      Object o = randomNoTasks[j].get();  
      Integer nr = (Integer) randomNoTasks[j].get();

      // The get method holds the control until the result is received  
      // The get method may throw the checked exceptions  
      // like when the method is interrupted. Because of this reason  
      // we have to add the throws clause to the main method  

       // printing the generated random number  
//      System.out.println("The random number is: " + o);
      System.out.println("The random number is: " + nr);  
  
    }  
  }  
}  
```

The expected output is
```
The random number is: 5
The random number is: 0
The random number is: 1
The random number is: 3
The random number is: 9
The random number is: 0
The random number is: 7
The random number is: 9
The random number is: 3
The random number is: 5
```


## How do you remove all occurrences of a given character from an input string in Java?
```
String str1 = "abcdABCDabcdABCD";
		
str1 = str1.replace("a", ""); 

System.out.println(str1); // bcdABCDbcdABCD
```

## How do you get distinct characters and their count in a string in Java?
```
String str1 = "abcdABCDabcd";

char[] chars = str1.toCharArray();

Map<Character, Integer> charsCount = new HashMap<>();

for (char c : chars) {
	if (charsCount.containsKey(c)) {
		charsCount.put(c, charsCount.get(c) + 1);
	} else
		charsCount.put(c, 1);
}

System.out.println(charsCount); // {a=2, A=1, b=2, B=1, c=2, C=1, d=2, D=1}
```


## Reverse only the letters in a string
```

```


## How do you reverse a string in Java?
```
public class StringPrograms {

	public static void main(String[] args) {
		String str = "123";

		System.out.println(reverse(str));
	}

	public static String reverse(String in) {
		if (in == null)
			throw new IllegalArgumentException("Null is not valid input");

		StringBuilder out = new StringBuilder();

		char[] chars = in.toCharArray();

		for (int i = chars.length - 1; i >= 0; i--)
			out.append(chars[i]);

		return out.toString();
	}

}
```

## Reverse a string using recursion in Java
```
public class ReverseString {

    public String reverse(String input) {
        if (input.isEmpty()) {  
            System.out.println("String is empty now");
            return input;
        }
        return reverse(input.substring(1)) + input.charAt(0);   
    }
}
```

## Reverse a String using Stack
```
public static String reverse(String str) {
    char[] charArr = str.toCharArray();
    int size = charArr.length;
    Stack stack = new Stack(size);

    int i;
    for (i = 0; i < size; ++i) {
        stack.push(charArr[i]);
    }

    for (i = 0; i < size; ++i) {
        charArr[i] = stack.pop();
    }

    return String.valueOf(charArr);
}
```

## How do you remove spaces from a string in Java?
```
String removeWhiteSpaces(String input) {
	StringBuilder output = new StringBuilder();
	
	char[] charArray = input.toCharArray();
	
	for (char c : charArray) {
		if (!Character.isWhitespace(c))
			output.append(c);
	}
	
	return output.toString();
}
```

##  How do you remove leading and trailing spaces from a string in Java?
```
String s = "  abc  def\t";
		
s = s.strip();
		
System.out.println(s);
```

## How do you swap two numbers without using a third variable in Java?
```
b = b + a; // now b is sum of both the numbers
a = b - a; // b - a = (b + a) - a = b (a is swapped)
b = b - a; // (b + a) - b = a (b is swapped)
```

```
public class SwapNumbers {

public static void main(String[] args) {
	int a = 10;
	int b = 20;

    System.out.println("a is " + a + " and b is " + b);

	a = a + b;
	b = a - b;
	a = a - b;

    System.out.println("After swapping, a is " + a + " and b is " + b);
    }

}
```

##  Write a Java program to check if a vowel is present in a string.
```
public class StringContainsVowels {

	public static void main(String[] args) {
		System.out.println(stringContainsVowels("Hello")); // true
		System.out.println(stringContainsVowels("TV")); // false
	}

	public static boolean stringContainsVowels(String input) {
		return input.toLowerCase().matches(".*[aeiou].*");
	}

}
```

## How do you sort an array in Java?
```
int[] array = {1, 2, 3, -1, -2, 4};

Arrays.sort(array);

System.out.println(Arrays.toString(array));
```

##  Write a Java program to check if the given number is a prime number.
```
public class PrimeNumberCheck {

	public static void main(String[] args) {
		System.out.println(isPrime(19)); // true
		System.out.println(isPrime(49)); // false
	}

	public static boolean isPrime(int n) {
		if (n == 0 || n == 1) {
			return false;
		}
		if (n == 2) {
			return true;
		}
		for (int i = 2; i <= n / 2; i++) {
			if (n % i == 0) {
				return false;
			}
		}

		return true;
	}

}

```
## Prime number checker in Java
```
boolean isPrime(long n) {
    for (int i = 2; i < n; i++) {
        if (n % i == 0)
            return false;
    }
    return true;
}
```

also 
```
boolean isPrimeV2(long n) {
    if (n % 2 == 0) return false; 

    for (int i = 3; i * i <= n; i += 2) { 
        if (n % i == 0)
            return false;
    }
    return true;
}
```

## Write a Java program to print a Fibonacci sequence using recursion.
```
public class PrintFibonacci {

	public static void printFibonacciSequence(int count) {
		int a = 0;
		int b = 1;
		int c = 1;

		for (int i = 1; i <= count; i++) {
			System.out.print(a + ", ");

            a = b;
			b = c;
			c = a + b;
		}
	}

	public static void main(String[] args) {
    	printFibonacciSequence(10);
	}

}
```

## Return the N-th value of the Fibonacci sequence. 
```
function fib(n){
  let arr = [0, 1];
  for (let i = 2; i < n + 1; i++){
    arr.push(arr[i - 2] + arr[i -1])
  }
 return arr[n]
}
```


## Write a Java program to reverse a string without using the reverse method of Java’s String class
```

```

## Java Program to find Factorial of a number
factorial (n) = n x factorial (n-1)
```
public class Factorial {
    public long factorial(long n) {
        if (n == 1)     
            return 1;
        else {
            return n * factorial(n - 1);    
        }
    }
}
```

## Pangram checker in java
A pangram is a sentence containing every letter in the English Alphabet (a-z). Pangram example: The quick brown fox jumps over the lazy dog
```
public class Pangram {

    public boolean checkPangram(String str) {
        boolean[] mark = new boolean[26];

        int index = 0;
        for (int i = 0; i < str.length(); i++) {
            // If uppercase character, subtract 'A' to find index.
            if ('A' <= str.charAt(i) && str.charAt(i) <= 'Z')
                index = str.charAt(i) - 'A';
            else if ('a' <= str.charAt(i) && str.charAt(i) <= 'z')
                index = str.charAt(i) - 'a';
            mark[index] = true;
        }
        for (int i = 0; i <= 25; i++)
            if (!mark[i])
                return (false);
        return (true);
    }
}
```

## Anagrams string checker
Two strings are called anagrams if they contain same set of characters but in different order. For example,
peek and keep are anagrams
```
import java.util.Arrays;

public class AnagramChecker {

    public boolean isAnagram(String input1, String input2) {
        char[] sortedWord1 = input1.toCharArray();
        Arrays.sort(sortedWord1);

        char[] sortedWord2 = input2.toCharArray();
        Arrays.sort(sortedWord2);

        return Arrays.equals(sortedWord1, sortedWord2);
    }
}
```

## Armstrong Number
In an Armstrong number (also known as narcissistic number), is a number that is the sum of its own digits each raised to the power of the number of digits.
153 = 1^3 + 5^3 + 3^3 
8208 = 8^4 + 2^4 + 0^4 + 8^4
```
public class Armstrong {

    public boolean check(int input) {
        int temp, digit, sumOfPower = 0;
        temp = input;
        int digits = countDigit(input);
        while (temp != 0) {
            digit = temp % 10;
            System.out.println("Current Digit is " + digit);
            sumOfPower = sumOfPower + (int) Math.pow(digit, digits);
            System.out.println("Current sumOfPower is " + sumOfPower);
            temp /= 10;
        }
        return sumOfPower == input;
    }

    static int countDigit(long n) {
        return (int) Math.floor(Math.log10(n) + 1);
    }
}
```

## How do you check whether a string is a palindrome in Java?
```
boolean checkPalindromeString(String input) {
	boolean result = true;
	int length = input.length();

	for (int i = 0; i < length/2; i++) {
		if (input.charAt(i) != input.charAt(length - i - 1)) {
			result = false;
			break;
		}
	}

	return result;
}
```

## Palindrome checker
A palindrome is a word, number, phrase, or other sequence of characters which reads the same backward as forward, such as madam or racecar or the number 10801.
```
public class Palindrome {

  boolean isPalindrome(int input) {
      int reversed = reverse(input);
      return input == reversed;
  }

  private int reverse(int input) {
      int lastDigit, sum = 0, temp;
      temp = input;
      while (temp > 0) {
          lastDigit = temp % 10; 
          sum = (sum * 10) + lastDigit;
          temp = temp / 10;
      }
      return sum; 
  }
}

```

## Check if the given string is palindrome
A palindrome is a word, number, phrase, or other sequence of characters which reads the same backward as forward, such as madam or racecar or the number 10801.
```
public boolean isPalindrome(String input) {
    int length = input.length();
    for (int i = 0; i < length / 2; i++) {
        if ( input.charAt(i) != input.charAt(length - 1 - i)) {
            return false;
        }
    }
    return true;
}
```

## Create a Java Singleton class
```

```

## Write a Java program that returns an MD5 hash
MD5 (Message-Digest algorithm 5) is a cryptographic function for creating 128-bit hashes from strings. It was designed by Professor Ronald Rivest of MIT, and although the function is considered cryptographically broken, it’s still useful as a programming exercise.

Research the algorithm and write a Java class from scratch that accepts an alphanumeric string and returns the MD5 hash for that string.
```

```

## Write three Java lambda expressions
1. One that returns true if a number passed to it is odd and false if it’s even.
2. One that returns true if a number is prime and false if it’s not.
3. One that returns true if the parameter is a palindrome and false if it’s not.

## write a method to provide a sorted array of numbers with no duplicates

```

```


## How do you reverse a linked list in Java?
```
LinkedList<Integer> ll = new LinkedList<>();

ll.add(1);
ll.add(2);
ll.add(3);

System.out.println(ll);

LinkedList<Integer> ll1 = new LinkedList<>();

ll.descendingIterator().forEachRemaining(ll1::add);

System.out.println(ll1);
```

## How do you implement a binary search in Java?
```
public static int binarySearch(int arr[], int low, int high, int key) {
	int mid = (low + high) / 2;

	while (low <= high) {
		if (arr[mid] < key) {
			low = mid + 1;
		} else if (arr[mid] == key) {
			return mid;
		} else {
			high = mid - 1;
		}
		mid = (low + high) / 2;
	}

	if (low > high) {
		return -1;
	}

	return -1;
}
```

## Write Java program that checks if two arrays contain the same elements.
```
import java.util.Arrays;
import java.util.HashSet;
import java.util.Set;

public class ArraySameElements {

	public static void main(String[] args) {
		Integer[] a1 = {1,2,3,2,1};
		Integer[] a2 = {1,2,3};
		Integer[] a3 = {1,2,3,4};
		
		System.out.println(sameElements(a1, a2));
		System.out.println(sameElements(a1, a3));
	}

	static boolean sameElements(Object[] array1, Object[] array2) {
		Set<Object> uniqueElements1 = new HashSet<>(Arrays.asList(array1));
		Set<Object> uniqueElements2 = new HashSet<>(Arrays.asList(array2));
		
		// if size is different, means there will be a mismatch
		if (uniqueElements1.size() != uniqueElements2.size()) return false;
		
		for (Object obj : uniqueElements1) {
			// element not present in both?
			if (!uniqueElements2.contains(obj)) return false;
		}
		
		return true;
	}

}
```

##  How do you print a date in specific format in Java?
```
yyyy-MMMM-dd 2023-July-04
yyyy-MMM-dd  2023-Jul-04
yyyy-MM-dd   2023-07-04
HH:mm:ss     15:30:55
hh:mm:ss a   3:30:55 PM
```

```
String pattern = "MM-dd-yyyy";
SimpleDateFormat simpleDateFormat = new SimpleDateFormat(pattern);

String date = simpleDateFormat.format(new Date());
System.out.println(date); // 06-23-2020
```

## How do you create a deadlock scenario programmatically in Java?
Deadlock is a scenario in a multi-threaded Java environment where two or more threads are blocked forever. The deadlock situation arises with at two or more threads. The following example code creates a deadlock scenario:
```
public class ThreadDeadlock {

    public static void main(String[] args) throws InterruptedException {
        Object obj1 = new Object();
        Object obj2 = new Object();
        Object obj3 = new Object();
    
        Thread t1 = new Thread(new SyncThread(obj1, obj2), "t1");
        Thread t2 = new Thread(new SyncThread(obj2, obj3), "t2");
        Thread t3 = new Thread(new SyncThread(obj3, obj1), "t3");
        
        t1.start();
        Thread.sleep(5000);
        t2.start();
        Thread.sleep(5000);
        t3.start();        
    }

}

class SyncThread implements Runnable {

    private Object obj1;
    private Object obj2;

    public SyncThread(Object o1, Object o2) {
        this.obj1 = o1;
        this.obj2 = o2;
    }

    @Override
    public void run() {
        String name = Thread.currentThread().getName();

        System.out.println(name + " acquiring lock on " + obj1);
        synchronized (obj1) {
            System.out.println(name + " acquired lock on " + obj1);
            work();
            System.out.println(name + " acquiring lock on " + obj2);
            synchronized (obj2) {
                System.out.println(name + " acquired lock on " + obj2);
                work();
            }
            System.out.println(name + " released lock on " + obj2);
        }
        System.out.println(name + " released lock on " + obj1);
        System.out.println(name + " finished execution.");
    }

    private void work() {
        try {
            Thread.sleep(30000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

}
```
## Avoiding deadlock in Java 
```

```

## Convert a Single Linked List to a Double Linked List
Here we link with Previos node as well as Next node
```
private class Node
{
    Picture data;
    Node pNext;
    Node pPrev;
};
```

## Implement Stack using Two Queues (with efficient push)
```
public Stack<E> {
    private Queue<E> q1 = new Queue<E>();
    private Queue<E> q2 = new Queue<E>();

    public void push(E x) {
        q1.enqueue(x);
    }

    public E pop() {
        while (q1.size() > 1) {
            q2.enqueue(q1.dequeue());
        }
        E pop = q1.dequeue();
        Queue<E> temp = q1;
        q1 = q2;
        q2 = temp;
        return pop;
    }
}

```

## Implement a Queue using two Stacks
```
public class Queue<T> where T : class
{
    private Stack<T> input = new Stack<T>();
    private Stack<T> output = new Stack<T>();

    public void Enqueue(T t)
    {
        input.Push(t);
    }

    public T Dequeue()
    {
        if (output.Count == 0)
        {
            while (input.Count != 0)
            {
                output.Push(input.Pop());
            }
        }

        return output.Pop();
    }
}
```







## Bibliography
https://www.javacodemonk.com/tag/java-coding-challenges \
https://www.codecademy.com/resources/blog/advanced-java-code-challenges/ \
https://www.digitalocean.com/community/tutorials/java-programming-interview-questions \
https://www.fullstack.cafe/blog/coding-challenges-interview-questions \
