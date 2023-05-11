I will keep this post updated with my solutions to daily challenges shared from [The Daily Coding Problem](https://www.dailycodingproblem.com/). 

## Problem - May 9, 2023: 
```plaintext
Given a list of numbers and a number k, return whether any two numbers from the list add up to k.`

For example, given [10, 15, 3, 7] and k of 17, return true since 10 + 7 is 17.`

Bonus: Can you do this in one pass?
```

Thought Process: The communitaive property of should allow me to return the appropriate TRUE/FALSE characterization without removing any numbers. I want my function to  move through the list left to right where it starts with the first value and subsequently repeats the following process:
1. Starts with the first value in the list
2. Adds the subsequent number
3. Checks the sum condition if n<sub>0</sub> +n<sub>n</sub> is equal to k
4. The loop can break if the sum condition returned True, otherwise it needs to move to the 2nd item in the list and continue through the loop   

## Solution - May 9, 2023:
```python
numbers=[74, 36, 32, 62, 25, 38, 31, 10, 20, 3, 34, 56, 36, 51, 24, 61, 18, 73, 38, 2, 18, 9, 64, 59, 40, 15, 67, 64, 51, 43, 62, 45, 34, 58, 19, 68, 37, 33, 39, 3, 56]

k=59

def evaluate_sumCondition(k,n_list):
    #Set variable for moving through each value in the list starting with first indexx 0
    nth_item=0
    #loop through each item in list
    for i in n_list:
        #Traverse the list adding positional element N to element N+1
        sum=n_list[nth_item]+n_list[nth_item+1]
        #Evaluate Sum Condition
        if sum == k: 
            return True
        else:
            #Stop if the second to last element of the list was evaluated
            if nth_item==len(n_list)-2:
                return False
                break
            #Continue loop if more than one number in the list remains
            else:
                nth_item=nth_item+1

print(evaluate_sumCondition(k, numbers))
```


## Problem - May 10, 2023: 

```plaintext

Given an array of integers, return a new array such that each element at index i of the new array 
is the product of all the numbers in the original array except the one at i.
For example, if our input was [1, 2, 3, 4, 5], the expected output would be [120, 60, 40, 30, 24]. 
If our input was [3, 2, 1], the expected output would be [2, 3, 6].
Follow-up: what if you can't use division?
```
Thought Process: 
1. Initialize a input list
2. Remove value at index 0
3. On each loop disregard i and look at i+1...i+n up to and including the last value in the list
4. Create a new list adding the the results of multiplying the numbers i+n or i-n together

## Solution - May 10, 2023: 

```python
#Intialize Input List
input_list=[1, 2, 3, 4, 5]

def skipMultiply(someList):
    #Two additional lists intialized, one to capture output and one as a working list to store values that get temporarily removed from the input 
    output_list=[]
    working_list=[]
    #Variable to track where which value in the list is be ignored for multiplication
    value_position=0
    #Define Outside of Loop: Maximum position of value in 0-indexed list
    maxPosition=len(input_list)
    #Define Outside of Loop: Index of input list
    index=list(range(maxPosition))

    #Only loop through until the value position is 1 less than the maximum position accounting for 0-indexing
    while value_position <maxPosition:
        maxPosition=len(input_list)
        index=list(range(maxPosition))
        #Before removing i from input list, add it to working list allowing for adding it back later
        working_list.append(input_list[index[0]]) 
        input_list.pop(0)
        # Initialize product as 1 so that the multiplication with the first element of input_list in the for loop will return that element itself
        product=1
        for i in range(len(input_list)):
            product*=input_list[i]
        #Add result to output list
        output_list.append(product)
        #Replace value that was removed from input to be included in subsequent multiplication            
        input_list.append(working_list[value_position])
        value_position+=1
    return output_list

print(skipMultiply(input_list))
```