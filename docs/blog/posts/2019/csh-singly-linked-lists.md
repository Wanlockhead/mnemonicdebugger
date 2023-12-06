---
date: 2019-03-08
categories:
    - c#
    - puzzles
    - singlylinkedlist
---

# C# – Checking for Loops in a Singly Linked List

I was asked a question about how to check for loops in a singly linked list (a loosely linked collection of objects in which each object references only the next object in the sequence, but not the object prior).

After some research I learned that this could be done easily and quickly using pointers and a hash table in C or C++. However, this is a more interesting question in C#, because accessing pointers is easier said than done with managed value types.

Ultimately, the solution (posted on GitHub now: https://github.com/SerdoSchofield/SinglyLinkedListTest/tree/master) is to take advantage of the fact that C# passes Objects around as reference types, rather than copying their exact values all over the place. Using this, the solution becomes pretty simple:

1. Create a HashSet for your object class (A custom Node in my case).
2. Make your way from object to object in your LinkedList, adding each object to the HashSet as you go (Remember: this is not copying their values everywhere, but instead only using references so it is not a memory hog)
3. Because HashSet must have entirely unique values, you can check using HashSet.Contains() to see if the next object is already found. If it has been, congratulations! You found the loop!

```c#
static void CheckForLoops(LinkedList linkedList){
     var curr = linkedList.First;
     HashSet hashCodes = new HashSet();
     while (Next(curr, linkedList.First) != null)     {
         hashCodes.Add(curr);
         if (hashCodes.Contains(curr.next))
             Console.WriteLine($"{curr.data.ToString()}: 
               {curr.next.data.ToString()} --- We've seen this before!! 
               {curr.GetHashCode()} {curr.next.GetHashCode()}");
         curr = Next(curr, linkedList.First);
     }
}
```

Never mind that this scenario would likely never arise in modern C#, it was a pretty fun puzzle to solve.