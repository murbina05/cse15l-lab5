# Lab Report 5

## Debugging scenario

Student's help post:

### Max: help I have not idea why this is not working. 

I'm trying to run the bash script in `git clone https://github.com/ucsd-cse15l-s23/lab7` however whenever I run it a get an error. When I run it I get the following error message. 

```
JUnit version 4.13.2
..E
Time: 0.588
There was 1 failure:
1) testMerge2(ListExamplesTests)
org.junit.runners.model.TestTimedOutException: test timed out after 500 milliseconds
        at java.base@17.0.10/java.util.Arrays.copyOf(Arrays.java:3512)
        at java.base@17.0.10/java.util.Arrays.copyOf(Arrays.java:3481)
        at java.base@17.0.10/java.util.ArrayList.grow(ArrayList.java:237)
        at java.base@17.0.10/java.util.ArrayList.grow(ArrayList.java:244)
        at java.base@17.0.10/java.util.ArrayList.add(ArrayList.java:454)
        at java.base@17.0.10/java.util.ArrayList.add(ArrayList.java:467)
        at app//ListExamples.merge(ListExamples.java:42)
        at app//ListExamplesTests.testMerge2(ListExamplesTests.java:19)

FAILURES!!!
Tests run: 2,  Failures: 1

```

I though it had something to do with the merge method so I tried different input but I still get teh timed out error message when I run the script. I try running the script with `bash test.sh` is this the right way of calling the script? Also I'll add a picture of what the test script looks I wonder if I did something that made this brocken.

```
javac -cp .:lib/hamcrest-core-1.3.jar:lib/junit-4.13.2.jar *.java
java -cp .:lib/hamcrest-core-1.3.jar:lib/junit-4.13.2.jar org.junit.runner.JUnitCore ListExamplesTests

```

here is how my test list methods looks like 

```
import static org.junit.Assert.*;
import org.junit.*;
import java.util.*;
import java.util.ArrayList;


public class ListExamplesTests {
        @Test(timeout = 500)
        public void testMerge1() {
                List<String> l1 = new ArrayList<String>(Arrays.asList("x", "y"));
                List<String> l2 = new ArrayList<String>(Arrays.asList("a", "b"));
                assertArrayEquals(new String[]{ "a", "b", "x", "y"}, ListExamples.merge(l1, l2).toArray());
        }

        @Test(timeout = 500)
        public void testMerge2() {
                List<String> l1 = new ArrayList<String>(Arrays.asList("a", "b", "c"));
                List<String> l2 = new ArrayList<String>(Arrays.asList("c", "d", "e"));
                assertArrayEquals(new String[]{ "a", "b", "c", "c", "d", "e" }, ListExamples.merge(l1, l2).toArray());
        }

}
```


### TA Respons 

Hi Max, thats an interesting error message. By the looks of it you run two tests and one passes and the other does not. The first thing I would look into is what is differnt about the two methods. Furthermore the error message you get is a timeout error so that suggests taht something is preventing the test method from running in an appropriate time. I would suggest you look for some infinite loops. Make sure that for every loop you have an exit condition. Hope this helps and good luck!





### Max: help I have not idea why this is not working.

Wow I fixed it! Thank you for the advice. Turns out that my bug was a simple one. When I was coding I misstyped one of the variables and as a results I was updating the wrong one. I changed it and now the code runs! I guess in the future I need to be more careful when working with loops. 

When I run the scrit it executes with no issues!

```
murbina@Max:~/cse15/lab7$ bash test.sh
JUnit version 4.13.2
..
Time: 0.008

OK (2 tests)
```

Here is the code that I changed

```
import java.util.ArrayList;
import java.util.List;

interface StringChecker { boolean checkString(String s); }

class ListExamples {

  // Returns a new list that has all the elements of the input list for which
  // the StringChecker returns true, and not the elements that return false, in
  // the same order they appeared in the input list;
  static List<String> filter(List<String> list, StringChecker sc) {
    List<String> result = new ArrayList<>();
    for(String s: list) {
      if(sc.checkString(s)) {
        result.add(0, s);
      }
    }
    return result;
  }


  // Takes two sorted list of strings (so "a" appears before "b" and so on),
  // and return a new list that has all the strings in both list in sorted order.
  static List<String> merge(List<String> list1, List<String> list2) {
    List<String> result = new ArrayList<>();
    int index1 = 0, index2 = 0;
    while(index1 < list1.size() && index2 < list2.size()) {
      if(list1.get(index1).compareTo(list2.get(index2)) < 0) {
        result.add(list1.get(index1));
        index1 += 1;
      }
      else {
        result.add(list2.get(index2));
        index2 += 1;
      }
    }
    while(index1 < list1.size()) {
      result.add(list1.get(index1));
      index1 += 1;
    }
    while(index2 < list2.size()) {
      result.add(list2.get(index2));
      // change index1 below to index2 to fix test
      index2 += 1;
    }
    return result;
  }


```


Below is all the files that were involved in this lab report.
```
murbina@Max:~/cse15/lab7$ ls
ListExamples.class  ListExamplesTests.class  StringChecker.class  test.sh
ListExamples.java   ListExamplesTests.java   lib
murbina@Max:~/cse15/lab7$ cd lib/
murbina@Max:~/cse15/lab7/lib$ ls
hamcrest-core-1.3.jar  junit-4.13.2.jar
murbina@Max:~/cse15/lab7/lib$
```

The contents of **ListExamples.java** before fixing the bug 

```

import java.util.ArrayList;
import java.util.List;

interface StringChecker { boolean checkString(String s); }

class ListExamples {

  // Returns a new list that has all the elements of the input list for which
  // the StringChecker returns true, and not the elements that return false, in
  // the same order they appeared in the input list;
  static List<String> filter(List<String> list, StringChecker sc) {
    List<String> result = new ArrayList<>();
    for(String s: list) {
      if(sc.checkString(s)) {
        result.add(0, s);
      }
    }
    return result;
  }


  // Takes two sorted list of strings (so "a" appears before "b" and so on),
  // and return a new list that has all the strings in both list in sorted order.
  static List<String> merge(List<String> list1, List<String> list2) {
    List<String> result = new ArrayList<>();
    int index1 = 0, index2 = 0;
    while(index1 < list1.size() && index2 < list2.size()) {
      if(list1.get(index1).compareTo(list2.get(index2)) < 0) {
        result.add(list1.get(index1));
        index1 += 1;
      }
      else {
        result.add(list2.get(index2));
        index2 += 1;
      }
    }
    while(index1 < list1.size()) {
      result.add(list1.get(index1));
      index1 += 1;
    }
    while(index2 < list2.size()) {
      result.add(list2.get(index2));
      // change index1 below to index2 to fix test
      index1 += 1;
    }
    return result;
  }
```

**ListExamplesTests.java**:

```
import static org.junit.Assert.*;
import org.junit.*;
import java.util.*;
import java.util.ArrayList;


public class ListExamplesTests {
        @Test(timeout = 500)
        public void testMerge1() {
                List<String> l1 = new ArrayList<String>(Arrays.asList("x", "y"));
                List<String> l2 = new ArrayList<String>(Arrays.asList("a", "b"));
                assertArrayEquals(new String[]{ "a", "b", "x", "y"}, ListExamples.merge(l1, l2).toArray());
        }

        @Test(timeout = 500)
        public void testMerge2() {
                List<String> l1 = new ArrayList<String>(Arrays.asList("a", "b", "c"));
                List<String> l2 = new ArrayList<String>(Arrays.asList("c", "d", "e"));
                assertArrayEquals(new String[]{ "a", "b", "c", "c", "d", "e" }, ListExamples.merge(l1, l2).toArray());
        }

}
```

**test.sh**:

```
javac -cp .:lib/hamcrest-core-1.3.jar:lib/junit-4.13.2.jar *.java
java -cp .:lib/hamcrest-core-1.3.jar:lib/junit-4.13.2.jar org.junit.runner.JUnitCore ListExamplesTests
```


The command that was run to cause the issues was `bash test.sh`

The bug that was happening was there was a loop that never would end becuase there was an infinite loop. This is an error that is easy to fix all you have to do is make sure you are updating the right variable so that you are not stuck in this loop. However if you are not looking at the error message closly it can take a while to figure out what is actually happening and how you can fix this bug. These are usually the type of bugs that I encounter whenever I try to rush something and I'm not careful.


This is the what causes the bug all you have to do is chagne the variable

```
      index1 += 1;
```

As you can see is you are not increasing the index so the loop doesn't know that you are actually moving at all and will never terminate. All you have to do is change it to index2 like below
```
      index2 += 1;

```


## Part 2 

One of the things that I learned and developed in the second half of this quarter was working with vim. Throughout this whole quarter we have been getting more familaiarized with using the command line and all the powerful tools that come with it. However, out of all the things I learned/developed using vim is probably the skill that I will use the most after this course is done. I like that this course has an emphasize learning tools not just concepts. A lot of the other courses that we take teach us concepts that we most likely only use for that class and then forget all about it. 

