## The Office

**Here's the task:**

Your colleagues have been looking over your shoulder. They've noticed you using the work computers to practice coding during work hours instead of completing your actual tasks.

In a team meeting, someone accuses you of not working. You're in trouble!  You need to quickly assess the mood in the room to decide whether to leave or not.

**The Challenge:**

Given an object (`meet`) containing team member names as keys and their happiness rating out of 10 as values, you need to determine the overall happiness rating of the group. If the average happiness rating is less than or equal to 5, return "Get Out Now!". Otherwise, return "Nice Work Champ!".

**Details:**

* The happiness rating is calculated by dividing the total score by the number of people in the room.
* **Boss Factor:**  Your boss is also in the room, and their happiness score is worth double its face value (but they're still just one person!).

**Original Task Link:**

https://www.codewars.com/kata/57ecf6efc7fe13eb070000e1/train/javascript

**Solution #1**

```javascript

function outed(meet, boss) {
  let teamHappinessCounter = 0;
  let colleagueArray = Object.keys(meet);

  for (let i = 0; i < colleagueArray.length; i++) {
    if (colleagueArray[i] === boss) {
      meet[colleagueArray[i]] *= 2; // Double boss's score
    }
    teamHappinessCounter += meet[colleagueArray[i]];
  }

  let average = teamHappinessCounter / colleagueArray.length;

  if (average > 5) {
    return 'Nice Work Champ!';
  } else {
    return 'Get Out Now!';
  }
}

```


Here, a running total is set for the happiness score. Then, a for loop is used to iterate through the colleagues; if the colleague is the boss, that colleague's score is doubled before being added to the running total. 

Pros:
- Readable; each step is clearly laid out in logical steps.
- Using a for loop typically has lower space complexity than using .map() or .reduce(). Space complexity means how much additional computer memory an algorithm needs as its the input (in this case, the number of colleagues) grows. In this implementation, the for loop changes the colleagues object in-place, so new copies aren't made and therefore less computer memory is needed. However, here low space complexity comes at the cost of mutating the original meet object, which is not ideal for larger or production systems (see below). 

Cons:
- The original meet object is changed. This mutation of state means that it the meet variable is used elsewhere in the program after being altered, the changes I've made to it will persist in all subsequent uses. In this case, the boss's happiness rating is doubled forever, so it's not possible for me to use the boss's original happiness rating in future calculations if I wanted to. If I try to, this could lead to 'side-effects' (bugs and unexpected behaviour) in my future calculations involving the happiness ratings. 
- Using a for loop can be inefficient for large datasets. If there were 1000s of colleagues, the for loop would take longer to execute, especially if the object is large and complex. 


**Solution #2**

```javascript

function outed(meet, boss) {
  let teamHappinessCounter = 0;
  let colleagueArray = Object.keys(meet);

  for (let i = 0; i < colleagueArray.length; i++) {
    if (colleagueArray[i] === boss) {
      meet[colleagueArray[i]] *= 2; 
    }
    teamHappinessCounter += meet[colleagueArray[i]];
  }

  let average = teamHappinessCounter / colleagueArray.length;

  if (average <= 5) {
    return 'Get Out Now!';
  } else {
    return 'Nice Work Champ!';
  }
}

```
This implementation is similar to solution 1. The only difference is that the conditional at the end if reversed: if the average is lower than or equal to 5, 'Get Out Now!' is returned. This gives the code a potential advantage:

Pros:
- Readable; each step is clearly laid out in logical steps. Readability makes debugging easier.
- Reversing the conditional is more intuitive from a readability standpoint because it checks for the "bad" condition (<= 5) first and returns 'Get Out Now!' early. Early return for bad conditions is often a good pattern for code readability 

Cons:
- The original meet object is changed. This mutation of state means that it the meet variable is used elsewhere in the program after being altered, the changes I've made to it will persist in all subsequent uses. In this case, the boss's happiness rating is doubled forever, so it's not possible for me to use the boss's original happiness rating in future calculations if I wanted to. If I try to, this could lead to bugs and unexpected behaviour in my future code. 


**Solution #3**

```javascript

function outed(meet, boss) {
  const colleagues = Object.keys(meet);
  const totalHappiness = colleagues.reduce((runningHappinessTotal, colleague) => 
    runningHappinessTotal + (colleague === boss ? meet[colleague] * 2 : meet[colleague]), 0);
  return (totalHappiness / colleagues.length > 5 ? 'Nice Work Champ!' : 'Get Out Now!');
}

```

Pros:
- Efficiency; the use of .reduce() here makes this solution more efficient by processing everything in one pass, rather than iterating through the array and then calculating the average afterward.
- The boss’s score is doubled inside the reduce function, but the original meet object remains unchanged. This avoids the mutation of state that we see in solutions 1 and 2, and means that a copy of the meet object has been created with the boss's score doubled, meansing that the original is still available if we want to use it in any future calculations. Immutability is a best practice for large systems or production systems. Avoiding state mutation reduces potential errors and makes your code more predictable. When you avoid modifying objects in place, your functions become "pure" (they don’t change outside state). 
- The use of ternaries instead of if statements in this version makes the code more concise by handling all the logic in-line. However, overusing ternaries can reduce readability. 


Cons:
- Using .reduce() has a slight space-complexity disadvantage compared to using a for loop, since a new version of the data is created and this increases the amount of memory needed to store the data.
- Less readable; the syntax in this implementation is more declarative (ie. it outlines what should be done, not how to do it). For experienced coders, declarative syntax can be preferable because it's more concise, but it can be less legible at a glance. 
- In this implementation, creating the colleagues variable is redundant. This is known as an 'intermediate variable' (one that you create on the way to getting your final solution). Using intermediate variables is a style preference that can make your code more readable by breaking down complex chains of operations. In this case, it is redundant because you could simply chain the reduce function to the array created by Object.keys(meet), and this would make your code more concise. 


**Solution #4**

```javascript

function outed(meet, boss) {
  const totalHappiness = Object.keys(meet).reduce((runningHappinessTotal, colleague) => 
    runningHappinessTotal + (colleague === boss ? meet[colleague] * 2 : meet[colleague]), 0);
  return (totalHappiness / Object.keys(meet).length > 5 ? 'Nice Work Champ!' : 'Get Out Now!');
}

```

Pros:
- Same as solution 3.
- This is the most concise, compact solution. It avoids storing any intermediate variables (like solution 3 with 'colleagues') and handles the logic inline.

Cons:
- This implementation calls Object.keys(meet) twice, which adds a small amount of overhead when handling large datasets. This means the algorithm needs to calculate what this means twice, whereas in solution 3, it was calculated onces and stored in the variable 'colleagues'. In small datasets the overhead would be negligible. However, if you're working with larger datasets, even small inefficiencies can add up.



**Solution #5**

```javascript

function outed(meet, boss){
  let happinessCounter = 0
  Object.keys(meet).forEach((colleague) => colleague === boss ? happinessCounter += (meet[colleague] * 2) : happinessCounter += meet[colleague])
  return (happinessCounter / Object.keys(meet).length <= 5 ? 'Get Out Now!' : 'Nice Work Champ!') 

}

```

This solution uses .forEach() to iterate over the team members, avoiding mutation of the meet object.

Pros:
- Easy to understand, with minimal syntax changes.
- Maintains immutability, avoiding the side effects present in solutions 1 and 2.

Cons:
- Slightly less efficient than .reduce() since it processes data in two steps (accumulation and return).
- Calls Object.keys(meet) twice, similar to Solution #4, adding some overhead.


**Solution #6**

``` javascript
function outed(meet, boss) {
  
  function calculateTotalScore(obj, boss, keys) {
    if (keys.length === 0) return [0, 0]; 
    const person = keys[0]; 
    const score = person === boss ? obj[person] * 2 : obj[person]; 
    const [totalScore, count] = calculateTotalScore(obj, boss, keys.slice(1));
    return [totalScore + score, count + 1]; 
  }

  const teamMembers = Object.keys(meet);
  const [totalScore, totalCount] = calculateTotalScore(meet, boss, teamMembers);
  const averageHappiness = totalScore / totalCount;
  return averageHappiness <= 5 ? 'Get Out Now!' : 'Nice Work Champ!';
}

```

This solution uses recursion to calculate the total score and count of team members, which are then used to compute the average.

Pros:
- Elegant, recursive solution.
- Maintains immutability and does not mutate the meet object.

Cons:
- Recursive functions are generally less performant for larger datasets due to function call overhead.
- Less readable for developers unfamiliar with recursion.