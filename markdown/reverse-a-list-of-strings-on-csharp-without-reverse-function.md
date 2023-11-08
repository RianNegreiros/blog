# Reverse a List of Strings on C# without Reverse()

You can create a function in C# to reverse a list of strings without using native functions like `Reverse()`. Here's a simple implementation:

```csharp
using System;
using System.Collections.Generic;

class Program
{
    static void Main()
    {
        List<string> originalList = new List<string> { "apple", "banana", "cherry", "date" };
        
        List<string> reversedList = ReverseList(originalList);

        Console.WriteLine("Original List: " + string.Join(", ", originalList));
        Console.WriteLine("Reversed List: " + string.Join(", ", reversedList));
    }

    static List<string> ReverseList(List<string> inputList)
    {
        int left = 0;
        int right = inputList.Count - 1;

        while (left < right)
        {
            // Swap elements at left and right indices
            string temp = inputList[left];
            inputList[left] = inputList[right];
            inputList[right] = temp;

            // Move indices towards the center
            left++;
            right--;
        }

        return inputList;
    }
}
```

Let me break down the `ReverseList` function:

1. **Input Parameters**: The function takes a `List<string>` as input.
2. **Two Pointers Approach**: We use a two-pointers approach to swap elements from the beginning and end of the list towards the center.
3. **While Loop**: The loop continues until the left pointer is less than the right pointer.
4. **Swap Elements**: Inside the loop, we swap the elements at the left and right indices.
5. **Move Pointers**: After swapping, we increment the left pointer and decrement the right pointer.
6. **Return Reversed List**: The function returns the modified list after reversing it.
