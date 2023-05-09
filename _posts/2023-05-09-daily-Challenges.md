I will keep this post updated with my solutions to daily challenges shared from [*The Daily Coding Problem](https://www.dailycodingproblem.com/). 

## Problem: May 9, 2023: 
```plaintext
Given a list of numbers and a number k, return whether any two numbers from the list add up to k.

For example, given [10, 15, 3, 7] and k of 17, return true since 10 + 7 is 17.

Bonus: Can you do this in one pass?
```

Thought Process: The communitaive property of should allow me to return the appropriate TRUE/FALSE characterization without removing any numbers. I want my function to  move through the list left to right where it starts with the first value and subsequently repeats the following process:
1. Starts with the first value in the list
2. Adds the subsequent number
3. Checks the sum condition if n<sub>0</sub> +n<sub>n</sub> is equal to k
4. The loop can break if the sum condition returned True, otherwise it needs to move to the 2nd item in the list and continue through the loop   

## Solution: May 9, 2023:
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