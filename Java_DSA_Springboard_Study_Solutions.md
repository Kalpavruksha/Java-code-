# Java Data Structures & Algorithms — Study Solutions

> **Purpose:** Copy-paste study notes for the Springboard / Infosys-style Java exercises shown in the screenshots.
>
> The solutions below focus on the **logic and Java patterns** you should remember. In an assessment, keep the method signature and class name supplied by the platform exactly as given.

---

# 0. Quick Pattern Map

| Problem | Main Pattern | Java Structure |
|---|---|---|
| First non-repeating character | Frequency + second scan | `int[26]` |
| Sort characters by frequency + first appearance | Frequency + first index + sorting | `HashMap` / array |
| Employee presence | Toggle membership | `HashSet` |
| Employee access verification | Lookup + temporary list + reverse | `HashMap`, `ArrayList` |
| Second frequent word | Frequency + first occurrence | `LinkedHashMap` / `HashMap` |
| Reverse vowels | Two pointers | `char[]` |
| Smart notification filter | Filter + formula | `ArrayList<Integer>` |
| Robot spiral path | Four boundaries | Matrix |
| Shortest route grid | Dynamic programming | `int[][]` |
| Reverse workflow | Linked-list reversal / array reversal | `Node` |
| Parcel handling | Map lookup + temporary reverse | `HashMap`, `ArrayList` |
| Transaction validation | Queue + set + temporary reverse | `Queue`, `HashSet` |
| Log analyzer | Filtering + grouping + counting | `List`, `Set`, `Map` |
| Tug of War Captain | Simulation / greedy choice | `ArrayList`, totals |
| Decode Ways | Dynamic programming | `int[]` |

---

# 1. First Non-Repeating Character

## Problem

Given a lowercase string, return the **0-based index** of the first character that appears exactly once.

If every character repeats, return `-1`.

### Example

```text
Input:  "swiss"
Output: 1

Input:  "aabb"
Output: -1
```

## Logic

1. Count every character.
2. Scan the string again from left to right.
3. The first character with frequency `1` is the answer.

## Java

```java
public static int firstNonRepeating(String str) {

    int[] freq = new int[26];

    // Count
    for (char ch : str.toCharArray()) {
        freq[ch - 'a']++;
    }

    // Find first unique character
    for (int i = 0; i < str.length(); i++) {
        if (freq[str.charAt(i) - 'a'] == 1) {
            return i;
        }
    }

    return -1;
}
```

### Remember

```text
COUNT -> SCAN AGAIN
```

Time: `O(n)`  
Space: `O(1)` because there are only 26 lowercase letters.

---

# 2. Sort Characters by Frequency + First Appearance

## Problem

Rearrange a string so:

1. Higher frequency comes first.
2. If two characters have the same frequency, the character appearing earlier in the original string comes first.
3. Every character must appear exactly as many times as in the input.

### Example

```text
Input:  "tree"
Output: "eert"
```

`e` appears twice, while `t` and `r` appear once.  
`t` appeared before `r`, so:

```text
ee + t + r
```

## Easy Java Solution

```java
public static String sortCharacters(String str) {

    int[] freq = new int[26];
    int[] first = new int[26];

    // -1 means character has not appeared yet
    for (int i = 0; i < 26; i++) {
        first[i] = -1;
    }

    // Count frequency and first occurrence
    for (int i = 0; i < str.length(); i++) {

        char ch = str.charAt(i);
        int index = ch - 'a';

        freq[index]++;

        if (first[index] == -1) {
            first[index] = i;
        }
    }

    // Store characters that actually exist
    ArrayList<Character> chars = new ArrayList<>();

    for (int i = 0; i < 26; i++) {
        if (freq[i] > 0) {
            chars.add((char) ('a' + i));
        }
    }

    // Sort:
    // 1. frequency descending
    // 2. first occurrence ascending
    Collections.sort(chars, (a, b) -> {

        int fa = freq[a - 'a'];
        int fb = freq[b - 'a'];

        if (fa != fb) {
            return Integer.compare(fb, fa);
        }

        return Integer.compare(first[a - 'a'], first[b - 'a']);
    });

    StringBuilder result = new StringBuilder();

    for (char ch : chars) {

        int count = freq[ch - 'a'];

        for (int i = 0; i < count; i++) {
            result.append(ch);
        }
    }

    return result.toString();
}
```

### Imports

```java
import java.util.ArrayList;
import java.util.Collections;
```

### Remember

```text
frequency -> first index -> sort -> repeat
```

---

# 3. Employee Presence / Maximum Employees Present

## Problem

A string represents employees entering and leaving.

- First occurrence of an employee = employee enters.
- Next occurrence = employee leaves.
- Next occurrence = employee enters again.
- Find the required current/final/maximum count depending on the question.

The important pattern is **membership toggling**.

## Java

```java
public static int employeePresence(String str) {

    HashSet<Character> present = new HashSet<>();

    int count = 0;
    int max = 0;

    for (char employee : str.toCharArray()) {

        if (present.contains(employee)) {

            // Employee leaves
            present.remove(employee);
            count--;

        } else {

            // Employee enters
            present.add(employee);
            count++;

            max = Math.max(max, count);
        }
    }

    // If question asks final count:
    return count;

    // If question asks maximum count:
    // return max;
}
```

### If the question asks maximum employees

Use:

```java
return max;
```

### If it asks employees currently present at the end

Use:

```java
return count;
```

### Remember

```text
HashSet = "Who is currently inside?"
```

---

# 4. Employee Access Verification System

## Rules from the exercise

Given:

```java
ArrayList<String> employeeRequestList
HashMap<String, String> employeeAccessMap
```

For every requested employee:

- If employee exists in the map:
  - Add `employeeId + "_" + accessLevel` directly to `outList`.
- Otherwise:
  - Add `employeeId + "_INVALID"` to a temporary list.
- Finally append temporary invalid entries in **reverse order**.

### Example

```text
Requests:
[E101, E501, E202]

Map:
E101 -> ADMIN
E202 -> USER
```

Output:

```text
[E101_ADMIN, E202_USER, E501_INVALID]
```

## Java

```java
public static ArrayList<String> verifyEmployees(
        ArrayList<String> employeeRequestList,
        HashMap<String, String> employeeAccessMap) {

    ArrayList<String> outList = new ArrayList<>();
    ArrayList<String> tempDataStructure = new ArrayList<>();

    for (String employeeId : employeeRequestList) {

        if (employeeAccessMap.containsKey(employeeId)) {

            String accessLevel = employeeAccessMap.get(employeeId);

            outList.add(employeeId + "_" + accessLevel);

        } else {

            tempDataStructure.add(employeeId + "_INVALID");
        }
    }

    // Add invalid entries in reverse order
    for (int i = tempDataStructure.size() - 1; i >= 0; i--) {
        outList.add(tempDataStructure.get(i));
    }

    return outList;
}
```

### Important pattern

```text
valid -> output immediately
invalid -> temporary list
end -> reverse temporary list -> output
```

---

# 5. Second Most Frequent Word

## Problem

Given a sentence:

- Count frequency of every word.
- Find the word with the **second-highest frequency**.
- If multiple words have the same frequency, the word appearing earlier in the sentence wins.
- If there is no second distinct frequency, return `"NONE"`.

## Java

```java
public static String secondFrequentWord(String sentence) {

    String[] words = sentence.split(" ");

    HashMap<String, Integer> freq = new HashMap<>();
    ArrayList<String> order = new ArrayList<>();

    // Count while remembering first appearance
    for (String word : words) {

        if (!freq.containsKey(word)) {
            freq.put(word, 1);
            order.add(word);
        } else {
            freq.put(word, freq.get(word) + 1);
        }
    }

    // Sort unique words:
    // frequency descending
    // first appearance ascending
    Collections.sort(order, (a, b) -> {

        int fa = freq.get(a);
        int fb = freq.get(b);

        if (fa != fb) {
            return Integer.compare(fb, fa);
        }

        return Integer.compare(
                firstIndex(words, a),
                firstIndex(words, b)
        );
    });

    if (order.size() < 2) {
        return "NONE";
    }

    // Need a SECOND DISTINCT FREQUENCY
    int highest = freq.get(order.get(0));

    for (int i = 1; i < order.size(); i++) {

        String word = order.get(i);

        if (freq.get(word) < highest) {
            return word;
        }
    }

    return "NONE";
}

private static int firstIndex(String[] words, String target) {

    for (int i = 0; i < words.length; i++) {

        if (words[i].equals(target)) {
            return i;
        }
    }

    return Integer.MAX_VALUE;
}
```

### Better version using first occurrence map

For exams, this version is cleaner:

```java
public static String secondFrequentWord(String sentence) {

    String[] words = sentence.split(" ");

    HashMap<String, Integer> freq = new HashMap<>();
    HashMap<String, Integer> first = new HashMap<>();

    for (int i = 0; i < words.length; i++) {

        String word = words[i];

        freq.put(word, freq.getOrDefault(word, 0) + 1);

        if (!first.containsKey(word)) {
            first.put(word, i);
        }
    }

    ArrayList<String> list = new ArrayList<>(freq.keySet());

    Collections.sort(list, (a, b) -> {

        if (!freq.get(a).equals(freq.get(b))) {
            return Integer.compare(freq.get(b), freq.get(a));
        }

        return Integer.compare(first.get(a), first.get(b));
    });

    int highest = freq.get(list.get(0));

    for (int i = 1; i < list.size(); i++) {

        if (freq.get(list.get(i)) < highest) {
            return list.get(i);
        }
    }

    return "NONE";
}
```

---

# 6. Reverse Vowels — Keep Consonants Fixed

## Problem

Reverse only the vowels.

Consonants must remain in their original positions.

### Example

```text
Input:  hello
Output: holle
```

Vowels:

```text
e o
```

Reverse:

```text
o e
```

Result:

```text
holle
```

## Java — Two Pointer

```java
public static String reverseVowels(String str) {

    char[] arr = str.toCharArray();

    int left = 0;
    int right = arr.length - 1;

    while (left < right) {

        while (left < right && !isVowel(arr[left])) {
            left++;
        }

        while (left < right && !isVowel(arr[right])) {
            right--;
        }

        // Swap vowels
        char temp = arr[left];
        arr[left] = arr[right];
        arr[right] = temp;

        left++;
        right--;
    }

    return new String(arr);
}

private static boolean isVowel(char ch) {

    return ch == 'a' ||
           ch == 'e' ||
           ch == 'i' ||
           ch == 'o' ||
           ch == 'u';
}
```

### Remember

```text
left -> find vowel
right -> find vowel
swap
move both
```

Time: `O(n)`  
Space: `O(n)` because of the character array.

---

# 7. Smart Notification Filter System

## Rules

Each notification has:

```text
APP_CODE + PRIORITY
```

Example:

```text
A3
C7
Z2
```

Rules:

1. Ignore notifications whose priority is greater than `5`.
2. For remaining notifications:

```text
score = alphabet position of APP_CODE × PRIORITY
```

For example:

```text
A3 -> 1 × 3 = 3
C4 -> 3 × 4 = 12
Z2 -> 26 × 2 = 52
```

## Java

```java
public static int[] calculateNotificationScores(
        int n,
        String[] notifications) {

    ArrayList<Integer> scores = new ArrayList<>();

    for (String notification : notifications) {

        char appCode = notification.charAt(0);

        int priority =
                Integer.parseInt(notification.substring(1));

        // Filter priority > 5
        if (priority > 5) {
            continue;
        }

        int position = appCode - 'A' + 1;

        int score = position * priority;

        scores.add(score);
    }

    int[] result = new int[scores.size()];

    for (int i = 0; i < scores.size(); i++) {
        result[i] = scores.get(i);
    }

    return result;
}
```

### Remember

```text
'A' -> 1
'B' -> 2
'C' -> 3
...
'Z' -> 26

position = ch - 'A' + 1
```

---

# 8. Robot Path — Spiral Matrix

## Problem

A robot must visit all matrix elements in clockwise spiral order:

```text
right -> down -> left -> up
```

Example:

```text
1 2 3
4 5 6
7 8 9
```

Traversal:

```text
1 2 3 6 9 8 7 4 5
```

## Java

```java
public static ArrayList<Integer> spiralOrder(int[][] matrix) {

    ArrayList<Integer> result = new ArrayList<>();

    if (matrix == null || matrix.length == 0) {
        return result;
    }

    int top = 0;
    int bottom = matrix.length - 1;

    int left = 0;
    int right = matrix[0].length - 1;

    while (top <= bottom && left <= right) {

        // 1. Left -> Right
        for (int col = left; col <= right; col++) {
            result.add(matrix[top][col]);
        }

        top++;

        // 2. Top -> Bottom
        for (int row = top; row <= bottom; row++) {
            result.add(matrix[row][right]);
        }

        right--;

        // 3. Right -> Left
        if (top <= bottom) {

            for (int col = right; col >= left; col--) {
                result.add(matrix[bottom][col]);
            }

            bottom--;
        }

        // 4. Bottom -> Top
        if (left <= right) {

            for (int row = bottom; row >= top; row--) {
                result.add(matrix[row][left]);
            }

            left++;
        }
    }

    return result;
}
```

### The four boundaries

```text
top
bottom
left
right
```

After completing a side, move the corresponding boundary inward.

---

# 9. Shortest Route Grid

## Problem

A drone starts at the top-left cell and can move only:

```text
RIGHT
DOWN
```

Each cell contains a cost.

Find the minimum total cost to reach the bottom-right cell.

## Example

```text
1 3 1
1 5 1
4 2 1
```

Minimum route:

```text
1 -> 3 -> 1 -> 1 -> 1
```

Total:

```text
7
```

## Dynamic Programming

Let:

```text
dp[i][j] = minimum cost to reach cell (i,j)
```

For every cell:

```text
dp[i][j] =
grid[i][j] + min(dp[i-1][j], dp[i][j-1])
```

## Java

```java
public static int minPathCost(int[][] grid) {

    int rows = grid.length;
    int cols = grid[0].length;

    int[][] dp = new int[rows][cols];

    dp[0][0] = grid[0][0];

    // First row
    for (int j = 1; j < cols; j++) {
        dp[0][j] = dp[0][j - 1] + grid[0][j];
    }

    // First column
    for (int i = 1; i < rows; i++) {
        dp[i][0] = dp[i - 1][0] + grid[i][0];
    }

    // Remaining cells
    for (int i = 1; i < rows; i++) {

        for (int j = 1; j < cols; j++) {

            dp[i][j] =
                    grid[i][j]
                    + Math.min(dp[i - 1][j], dp[i][j - 1]);
        }
    }

    return dp[rows - 1][cols - 1];
}
```

### Remember

For right/down only:

```text
current + MIN(top, left)
```

---

# 10. Reverse Task Workflow / Linked List

## Problem

Tasks are connected in execution order:

```text
1 -> 2 -> 3 -> 4 -> 5
```

Rollback requires:

```text
5 -> 4 -> 3 -> 2 -> 1
```

## Linked List Node

```java
static class Node {

    int data;
    Node next;

    Node(int data) {
        this.data = data;
    }
}
```

## Reverse Linked List

```java
public static Node reverse(Node head) {

    Node prev = null;
    Node current = head;

    while (current != null) {

        Node nextNode = current.next;

        current.next = prev;

        prev = current;
        current = nextNode;
    }

    return prev;
}
```

### Memorize this exact pattern

```java
Node prev = null;
Node current = head;

while (current != null) {

    Node next = current.next;

    current.next = prev;

    prev = current;
    current = next;
}

return prev;
```

### Why it works

Before:

```text
prev <- null

current
   |
   v
  1 -> 2 -> 3 -> null
```

After first iteration:

```text
1 -> null

prev = 1
current = 2
```

Eventually:

```text
5 -> 4 -> 3 -> 2 -> 1 -> null
```

---

# 11. Reverse an Array — Simple Workflow Version

If the assessment gives an `int[]` rather than a linked-list node:

```java
public static void reverseArray(int[] arr) {

    int left = 0;
    int right = arr.length - 1;

    while (left < right) {

        int temp = arr[left];
        arr[left] = arr[right];
        arr[right] = temp;

        left++;
        right--;
    }
}
```

### Example

```text
1 2 3 4 5
```

becomes:

```text
5 4 3 2 1
```

---

# 12. Parcel Handling Classification System

## Pattern from the exercise

The important rule shown in the exercise is:

- Process parcels from the queue.
- If the parcel has a special handling code in the map, create:

```text
parcelId_handlingCode
```

and put it into the temporary structure.
- Otherwise classify it as regular (`RG`) and add it directly to `outList`.
- After all parcels are processed, append temporary items in reverse order.

## Java

```java
public static ArrayList<String> classifyParcels(
        ArrayList<String> parcelQueue,
        HashMap<String, String> handlingMap) {

    ArrayList<String> outList = new ArrayList<>();
    ArrayList<String> tempDataStructure = new ArrayList<>();

    for (String parcel : parcelQueue) {

        if (handlingMap.containsKey(parcel)) {

            String code = handlingMap.get(parcel);

            tempDataStructure.add(parcel + "_" + code);

        } else {

            // Regular parcel
            outList.add(parcel + "_RG");
        }
    }

    // Special-handling parcels are added in reverse order
    for (int i = tempDataStructure.size() - 1; i >= 0; i--) {
        outList.add(tempDataStructure.get(i));
    }

    return outList;
}
```

### Pattern

```text
Map contains key?
        |
   +----+----+
   |         |
  YES        NO
   |         |
 temp       direct output
   |
reverse at end
```

---

# 13. Transaction Validation / Alert Queue

## Pattern from the exercise

There are:

```java
transactionQueue
flaggedTransactions
tempDataStructure
alertQueue
```

For each transaction:

1. If transaction ID is flagged, classify it as:

```text
<ID>_FRAUD
```

and put it in the fraud/temporary queue.
2. Otherwise classify it as `VALID`.
3. Valid transaction amount:
   - `>= 1000`:

```text
<ID>_VALID_ABOVE_MINBAL
```

   - `< 1000`:

```text
<ID>_VALID_BELOW_MINBAL
```

4. After processing, add the temporary results in reverse order to the alert queue.

> **Important:** Keep the exact transaction parsing and Java method signature supplied by the exercise template. The screenshot uses transaction objects/values; the core data-structure logic is the same.

## Generic Java Pattern

```java
public static ArrayList<String> processTransactions(
        ArrayList<Transaction> transactionQueue,
        HashSet<String> flaggedTransactions) {

    ArrayList<String> tempDataStructure = new ArrayList<>();
    ArrayList<String> alertQueue = new ArrayList<>();

    for (Transaction t : transactionQueue) {

        String id = t.getId();
        double amount = t.getAmount();

        // Fraud
        if (flaggedTransactions.contains(id)) {

            tempDataStructure.add(id + "_FRAUD");

        } else {

            // Valid transaction
            if (amount >= 1000) {

                tempDataStructure.add(
                        id + "_VALID_ABOVE_MINBAL"
                );

            } else {

                tempDataStructure.add(
                        id + "_VALID_BELOW_MINBAL"
                );
            }
        }
    }

    // Reverse order into alertQueue
    for (int i = tempDataStructure.size() - 1; i >= 0; i--) {
        alertQueue.add(tempDataStructure.get(i));
    }

    return alertQueue;
}
```

### If fraud and valid items are required in separate structures

Follow the exact template's required order. The key idea is:

```text
flagged -> FRAUD
not flagged -> VALID
amount >= 1000 -> ABOVE
amount < 1000 -> BELOW
reverse temporary structure
```

---

# 14. Service Log Analyzer

## Problem

A server log contains records with information such as:

```text
timestamp
level
service
errorCode
message
userId
ip
path
```

Methods required:

```java
getRecordsByErrorCode(String errorCode)
getUniqueErrorCodesByUserId(String userId)
getErrorOccurrences()
```

A method:

```java
getAllRecords()
```

is already provided.

---

## 14.1 Filter Records by Error Code

Comparison is case-insensitive.

```java
public List<LogRecord> getRecordsByErrorCode(String errorCode) {

    List<LogRecord> result = new ArrayList<>();

    for (LogRecord record : getAllRecords()) {

        if (record.getErrorCode().equalsIgnoreCase(errorCode)) {
            result.add(record);
        }
    }

    return result;
}
```

---

## 14.2 Unique Error Codes for a User

Use a `HashSet` because duplicate error codes must be removed.

```java
public Set<String> getUniqueErrorCodesByUserId(String userId) {

    Set<String> result = new HashSet<>();

    for (LogRecord record : getAllRecords()) {

        if (record.getUserId().equals(userId)) {
            result.add(record.getErrorCode());
        }
    }

    return result;
}
```

### Why HashSet?

If the user has:

```text
ERR101
ERR101
ERR202
ERR101
```

the result becomes:

```text
ERR101
ERR202
```

---

## 14.3 Count Occurrences of Every Error Code

Use a `HashMap`.

```java
public HashMap<String, Integer> getErrorOccurrences() {

    HashMap<String, Integer> result = new HashMap<>();

    for (LogRecord record : getAllRecords()) {

        String errorCode = record.getErrorCode();

        result.put(
                errorCode,
                result.getOrDefault(errorCode, 0) + 1
        );
    }

    return result;
}
```

### Remember

```text
Filter        -> List
Unique values -> Set
Count values  -> Map
```

---

# 15. Tug of War Captain — Earlier Practice Problem

## Problem

There are `N` kids with strengths.

One kid is selected as captain and that kid's strength is doubled.

Kids are then placed one at a time on the side with the smaller current total. If both sides are equal, either side can be chosen.

Goal:

```text
minimum absolute difference between the two sides
```

## Important Observation

The difficult part is that **captain selection and order can affect the result**.

For a small `N`, the safest brute-force strategy is:

1. Try every kid as captain.
2. Double that strength.
3. Simulate every meaningful ordering / placement if required by the exact problem.
4. Track the minimum difference.

### Simple simulation idea

```java
public static int tugOfWar(int[] strength) {

    int n = strength.length;
    int answer = Integer.MAX_VALUE;

    for (int captain = 0; captain < n; captain++) {

        int left = 0;
        int right = 0;

        /*
         * For the exact assessment problem, follow the
         * specified ordering rule.
         *
         * The captain's value is doubled.
         */

        // Example of captain value:
        int value = strength[captain] * 2;

        // Apply the exact placement rule here.
    }

    return answer;
}
```

### Study point

This is a **simulation / greedy-rule** problem, not a normal sorting problem.

---

# 16. Decode Ways — Earlier Practice Problem

## Classic version

A numeric string represents letters:

```text
1 -> A
2 -> B
...
26 -> Z
```

Find the number of ways to decode it.

Example:

```text
Input: 12

12
1,2

Output: 2
```

## Dynamic Programming

```java
public static int numDecodings(String s) {

    if (s == null || s.length() == 0) {
        return 0;
    }

    if (s.charAt(0) == '0') {
        return 0;
    }

    int n = s.length();

    int[] dp = new int[n + 1];

    dp[0] = 1;
    dp[1] = 1;

    for (int i = 2; i <= n; i++) {

        // One digit
        char one = s.charAt(i - 1);

        if (one != '0') {
            dp[i] += dp[i - 1];
        }

        // Two digits
        int two =
                (s.charAt(i - 2) - '0') * 10
                + (s.charAt(i - 1) - '0');

        if (two >= 10 && two <= 26) {
            dp[i] += dp[i - 2];
        }
    }

    return dp[n];
}
```

### Remember

At each position ask:

```text
Can I take 1 digit?
Can I take 2 digits?
```

---

# 17. Common Array Patterns to Memorize

## 17.1 Two Sum

```java
public static int[] twoSum(int[] nums, int target) {

    HashMap<Integer, Integer> map = new HashMap<>();

    for (int i = 0; i < nums.length; i++) {

        int need = target - nums[i];

        if (map.containsKey(need)) {
            return new int[]{map.get(need), i};
        }

        map.put(nums[i], i);
    }

    return new int[]{-1, -1};
}
```

---

# 18. Maximum Subarray — Kadane's Algorithm

```java
public static int maxSubArray(int[] nums) {

    int current = nums[0];
    int best = nums[0];

    for (int i = 1; i < nums.length; i++) {

        current = Math.max(
                nums[i],
                current + nums[i]
        );

        best = Math.max(best, current);
    }

    return best;
}
```

### Memorize

```text
current = max(current + x, x)
best = max(best, current)
```

---

# 19. Move Zeroes

Move all zeroes to the end while maintaining the order of non-zero elements.

```java
public static void moveZeroes(int[] nums) {

    int index = 0;

    for (int num : nums) {

        if (num != 0) {
            nums[index++] = num;
        }
    }

    while (index < nums.length) {
        nums[index++] = 0;
    }
}
```

Example:

```text
0 1 0 3 12

1 3 12 0 0
```

---

# 20. Rotate Array

Rotate right by `k`.

```java
public static void rotate(int[] nums, int k) {

    int n = nums.length;

    k = k % n;

    reverse(nums, 0, n - 1);
    reverse(nums, 0, k - 1);
    reverse(nums, k, n - 1);
}

private static void reverse(int[] nums, int left, int right) {

    while (left < right) {

        int temp = nums[left];
        nums[left] = nums[right];
        nums[right] = temp;

        left++;
        right--;
    }
}
```

---

# 21. Majority Element

The majority element occurs more than `n/2` times.

## Boyer-Moore

```java
public static int majorityElement(int[] nums) {

    int candidate = 0;
    int count = 0;

    for (int num : nums) {

        if (count == 0) {
            candidate = num;
        }

        if (num == candidate) {
            count++;
        } else {
            count--;
        }
    }

    return candidate;
}
```

### Remember

```text
same -> +1
different -> -1
count 0 -> new candidate
```

---

# 22. Sliding Window Pattern

For questions like:

> Find the longest/shortest subarray or substring satisfying a condition.

Use two pointers:

```java
int left = 0;

for (int right = 0; right < n; right++) {

    // Add right element

    while (conditionIsInvalid()) {

        // Remove left element
        left++;
    }

    // Update answer
}
```

### Example — longest substring without repeating characters

```java
public static int lengthOfLongestSubstring(String s) {

    HashSet<Character> set = new HashSet<>();

    int left = 0;
    int best = 0;

    for (int right = 0; right < s.length(); right++) {

        char ch = s.charAt(right);

        while (set.contains(ch)) {
            set.remove(s.charAt(left));
            left++;
        }

        set.add(ch);

        best = Math.max(
                best,
                right - left + 1
        );
    }

    return best;
}
```

---

# 23. Java Collections Cheat Sheet

## ArrayList

Use when:

```text
Need ordered collection
Need duplicates
Need index access
```

```java
ArrayList<String> list = new ArrayList<>();

list.add("A");
list.add("B");

String x = list.get(0);

list.remove(0);
```

---

## HashSet

Use when:

```text
Need unique values
Need fast contains()
```

```java
HashSet<String> set = new HashSet<>();

set.add("A");
set.add("A");

System.out.println(set.size()); // 1
```

---

## HashMap

Use when:

```text
key -> value
frequency counting
lookup
```

```java
HashMap<String, Integer> map = new HashMap<>();

map.put("apple", 1);

map.put(
    "apple",
    map.getOrDefault("apple", 0) + 1
);
```

---

## Queue

First in, first out:

```text
FIFO
```

```java
Queue<String> q = new LinkedList<>();

q.offer("A");
q.offer("B");

String x = q.poll();
```

---

## Stack / Deque

Last in, first out:

```text
LIFO
```

Prefer `Deque`:

```java
Deque<Integer> stack = new ArrayDeque<>();

stack.push(10);
stack.push(20);

int x = stack.pop();
```

---

# 24. Most Important Exam Templates

## Frequency

```java
HashMap<Character, Integer> map = new HashMap<>();

for (char ch : str.toCharArray()) {
    map.put(ch, map.getOrDefault(ch, 0) + 1);
}
```

For lowercase letters:

```java
int[] freq = new int[26];

for (char ch : str.toCharArray()) {
    freq[ch - 'a']++;
}
```

---

## First occurrence

```java
HashMap<Character, Integer> first = new HashMap<>();

for (int i = 0; i < str.length(); i++) {

    char ch = str.charAt(i);

    if (!first.containsKey(ch)) {
        first.put(ch, i);
    }
}
```

---

## Reverse Array

```java
int left = 0;
int right = arr.length - 1;

while (left < right) {

    int temp = arr[left];
    arr[left] = arr[right];
    arr[right] = temp;

    left++;
    right--;
}
```

---

## Reverse Linked List

```java
Node prev = null;
Node current = head;

while (current != null) {

    Node next = current.next;

    current.next = prev;

    prev = current;
    current = next;
}

return prev;
```

---

## Map Lookup

```java
if (map.containsKey(key)) {

    String value = map.get(key);

} else {

    // not found
}
```

---

## Reverse Temporary List

```java
for (int i = temp.size() - 1; i >= 0; i--) {
    out.add(temp.get(i));
}
```

This pattern appears repeatedly in the Springboard exercises.

---

# 25. How to Decide the Data Structure Quickly

When you see the question, ask:

### "I need frequency."

Use:

```text
HashMap
```

or for lowercase characters:

```text
int[26]
```

### "I need unique values."

Use:

```text
HashSet
```

### "I need key -> value lookup."

Use:

```text
HashMap
```

### "I need first-in-first-out."

Use:

```text
Queue
```

### "I need last-in-first-out."

Use:

```text
Stack / Deque
```

### "I need to preserve insertion order."

Use:

```text
LinkedHashMap
LinkedHashSet
ArrayList
```

### "I need reverse order."

Use:

```text
two pointers
```

or:

```java
for (int i = list.size() - 1; i >= 0; i--)
```

### "I can only move right/down in a grid."

Think:

```text
Dynamic Programming
```

### "I need spiral matrix traversal."

Think:

```text
top
bottom
left
right
```

---

# 26. One-Minute Revision Sheet

```text
FIRST UNIQUE
count -> scan

FREQUENCY SORT
count -> first index -> sort

EMPLOYEE PRESENCE
HashSet toggle add/remove

ACCESS VERIFICATION
valid -> output
invalid -> temp
reverse temp

SECOND FREQUENT WORD
count words -> first index -> sort by frequency

REVERSE VOWELS
left/right -> find vowels -> swap

NOTIFICATION
filter -> score = (ch-'A'+1) * priority

SPIRAL MATRIX
top -> right -> bottom -> left

SHORTEST GRID
dp[i][j] = grid[i][j] + min(top,left)

LINKED LIST REVERSE
prev/current/next

PARCEL
map lookup -> special temp -> reverse temp

TRANSACTION
flagged -> fraud
else amount >= 1000 -> above
else -> below
reverse required temp

LOG ANALYZER
filter = List
unique = Set
count = Map

TWO SUM
HashMap complement

MAX SUBARRAY
Kadane

MOVE ZEROES
write non-zero -> fill zeroes

ROTATE
reverse all -> reverse parts

MAJORITY
Boyer-Moore

SLIDING WINDOW
left/right + condition
```

---

# 27. Java Imports to Keep Ready

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.HashMap;
import java.util.HashSet;
import java.util.LinkedHashMap;
import java.util.LinkedHashSet;
import java.util.List;
import java.util.Map;
import java.util.Queue;
import java.util.Set;
import java.util.LinkedList;
import java.util.ArrayDeque;
import java.util.Deque;
```

---

# 28. Exam Strategy

When the platform gives a long problem statement:

### Step 1 — Ignore the story

Convert:

```text
employee security system
parcel system
notification system
transaction system
```

into the actual operation:

```text
lookup
count
filter
reverse
sort
traverse
```

### Step 2 — Identify the structure

```text
count       -> HashMap / int[]
unique      -> HashSet
lookup      -> HashMap
order       -> ArrayList
FIFO        -> Queue
LIFO        -> Stack/Deque
matrix      -> DP / boundaries
linked list -> pointers
```

### Step 3 — Write the simplest loop

Do not try to solve everything at once.

### Step 4 — Check the edge cases

```text
empty
one element
all repeated
all unique
not found
first/last element
single row
single column
```

### Step 5 — Preserve the platform's method signature

Do **not** change:

```java
public static ...
```

or the class name if the assessment provides it.

Only fill in the method body unless the platform explicitly asks for a complete program.

---

# Final Memory Rule

Most of these exercises are combinations of only a few patterns:

```text
COUNT
LOOKUP
UNIQUE
FILTER
REVERSE
SORT
TWO POINTERS
SLIDING WINDOW
STACK / QUEUE
LINKED LIST POINTERS
MATRIX BOUNDARIES
DYNAMIC PROGRAMMING
```

If you can recognize these 12 patterns quickly, the long business story in the question becomes much easier.




# Java Programming Solutions

## Table of Contents

1. [Smart City Toll Booth System](#1-smart-city-toll-booth-system)
2. [Bank Account Hierarchy](#2-bank-account-hierarchy)
3. [E-Commerce Payment Gateway](#3-e-commerce-payment-gateway)
4. [Library Item Management System](#4-library-item-management-system)
5. [Optimal Region Merge](#5-optimal-region-merge)
6. [Reverse Workflow](#6-reverse-workflow)
7. [Shortest Route Grid](#7-shortest-route-grid)
8. [Robot Path - Spiral Traversal](#8-robot-path---spiral-traversal)
9. [Reverse Vowel Swapper](#9-reverse-vowel-swapper)
10. [Smart Notification Filter System](#10-smart-notification-filter-system)

---

# 1. Smart City Toll Booth System

## Problem

Create an abstract `Vehicle` class containing:

- `licensePlate`
- `baseFee`
- abstract method `calculateToll()`

Create:

- `Car` → pays 100% of base fee
- `Truck` → pays base fee + 50 for every axle beyond 2

The `TollBooth` class should:

- Maintain total revenue
- Maintain the number of unique vehicles
- Use a `HashSet` for unique license plates
- Give a 20% discount when the same vehicle passes again on the same day

## Solution

```java
import java.util.HashSet;
import java.util.Set;

abstract class Vehicle {
    protected String licensePlate;
    protected double baseFee;

    Vehicle(String licensePlate, double baseFee) {
        this.licensePlate = licensePlate;
        this.baseFee = baseFee;
    }

    public abstract double calculateToll();
}

class Car extends Vehicle {

    Car(String licensePlate, double baseFee) {
        super(licensePlate, baseFee);
    }

    @Override
    public double calculateToll() {
        return baseFee;
    }
}

class Truck extends Vehicle {
    private int axles;

    Truck(String licensePlate, double baseFee, int axles) {
        super(licensePlate, baseFee);
        this.axles = axles;
    }

    @Override
    public double calculateToll() {
        return baseFee + (axles > 2 ? (axles - 2) * 50 : 0);
    }
}

class TollBooth {
    private double totalRevenue = 0;
    private int totalVehiclesProcessed = 0;

    private Set<String> uniqueVehicles = new HashSet<>();

    public double processVehicle(Vehicle v) {

        double toll = v.calculateToll();

        // Repeat vehicle gets 20% discount
        if (uniqueVehicles.contains(v.licensePlate)) {
            toll = toll * 0.80;
        } else {
            uniqueVehicles.add(v.licensePlate);
            totalVehiclesProcessed++;
        }

        totalRevenue += toll;

        return toll;
    }

    public double getTotalRevenue() {
        return totalRevenue;
    }

    public int getUniqueVehicles() {
        return uniqueVehicles.size();
    }
}

public class Main {
    public static void main(String[] args) {

        TollBooth booth = new TollBooth();

        Vehicle v1 = new Car("KA-01-1234", 100.0);
        Vehicle v2 = new Truck("KA-02-5678", 100.0, 4);
        Vehicle v3 = new Car("KA-01-1234", 100.0);

        System.out.println("Vehicle 1 Toll: " + booth.processVehicle(v1));
        System.out.println("Vehicle 2 Toll: " + booth.processVehicle(v2));
        System.out.println("Vehicle 3 Toll: " + booth.processVehicle(v3));

        System.out.println("Total Revenue: " + booth.getTotalRevenue());
        System.out.println("Unique Vehicles: " + booth.getUniqueVehicles());
    }
}

Output
Vehicle 1 Toll: 100.0
Vehicle 2 Toll: 200.0
Vehicle 3 Toll: 80.0
Total Revenue: 380.0
Unique Vehicles: 2

Concepts Used
- Abstraction
- Inheritance
- Method overriding
- Encapsulation
- Polymorphism
- HashSet
2. Bank Account Hierarchy
Problem
Create an InterestBearing interface with:
applyInterest()

Create a base BankAccount containing:
- account number
- balance
- deposit()
- withdraw()
Create:
- SavingsAccount
  - 4% annual interest
  - Minimum balance = 1000
- CurrentAccount
  - Overdraft allowed up to 5000
If withdrawal violates the rules, throw a checked:
InsufficientFundsException

Solution
interface InterestBearing {
    void applyInterest();
}

class InsufficientFundsException extends Exception {

    public InsufficientFundsException(String message) {
        super(message);
    }
}

class BankAccount {
    protected String accountNumber;
    protected double balance;

    BankAccount(String accountNumber, double balance) {
        this.accountNumber = accountNumber;
        this.balance = balance;
    }

    public void deposit(double amount) {
        balance += amount;
    }

    public void withdraw(double amount) throws InsufficientFundsException {
        if (amount > balance) {
            throw new InsufficientFundsException(
                "Insufficient balance"
            );
        }

        balance -= amount;
    }

    public double getBalance() {
        return balance;
    }
}

class SavingsAccount extends BankAccount
        implements InterestBearing {

    private final double MIN_BALANCE = 1000;

    SavingsAccount(String accountNumber, double balance) {
        super(accountNumber, balance);
    }

    @Override
    public void applyInterest() {
        balance += balance * 0.04;
    }

    @Override
    public void withdraw(double amount)
            throws InsufficientFundsException {

        if (balance - amount < MIN_BALANCE) {

            double available = balance - MIN_BALANCE;

            throw new InsufficientFundsException(
                "Minimum balance of 1000 must be maintained. " +
                "Available to withdraw: " + available
            );
        }

        balance -= amount;
    }
}

class CurrentAccount extends BankAccount {

    private final double OVERDRAFT_LIMIT = 5000;

    CurrentAccount(String accountNumber, double balance) {
        super(accountNumber, balance);
    }

    @Override
    public void withdraw(double amount)
            throws InsufficientFundsException {

        if (balance - amount < -OVERDRAFT_LIMIT) {

            throw new InsufficientFundsException(
                "Overdraft limit of 5000 exceeded"
            );
        }

        balance -= amount;
    }
}

public class Main {

    public static void main(String[] args) {

        // Case 1
        SavingsAccount savings =
            new SavingsAccount("S001", 1500);

        try {
            savings.withdraw(700);

            System.out.println(
                "Withdrawal successful. New Balance: "
                + savings.getBalance()
            );

        } catch (InsufficientFundsException e) {

            System.out.println(
                "InsufficientFundsException: "
                + e.getMessage()
            );
        }

        // Case 2
        CurrentAccount current =
            new CurrentAccount("C001", 500);

        try {
            current.withdraw(2000);

            System.out.println(
                "Withdrawal successful. New Balance: "
                + current.getBalance()
            );

        } catch (InsufficientFundsException e) {

            System.out.println(
                "InsufficientFundsException: "
                + e.getMessage()
            );
        }
    }
}

Expected Output
InsufficientFundsException: Minimum balance of 1000 must be maintained. Available to withdraw: 500.0
Withdrawal successful. New Balance: -1500.0

Concepts Used
- Interface
- Inheritance
- Exception handling
- Custom checked exception
- Method overriding
- Encapsulation
3. E-Commerce Payment Gateway
Problem
Create an interface:
PaymentMethod

with:
validateDetails()
pay(double amount)

Payment methods:
Credit Card
- Card number must contain exactly 16 digits
- 2% convenience fee
UPI
- ID must contain @
- No processing fee
Create CheckoutService that accepts any PaymentMethod.
This demonstrates runtime polymorphism.
Solution
interface PaymentMethod {

    boolean validateDetails();

    boolean pay(double amount);
}

class CreditCard implements PaymentMethod {

    private String cardNumber;

    CreditCard(String cardNumber) {
        this.cardNumber = cardNumber;
    }

    @Override
    public boolean validateDetails() {
        return cardNumber.matches("\\d{16}");
    }

    @Override
    public boolean pay(double amount) {

        if (!validateDetails()) {
            return false;
        }

        double finalAmount = amount + (amount * 0.02);

        System.out.println(
            "Payment Validated. Charged: " + finalAmount
        );

        return true;
    }
}

class UPI implements PaymentMethod {

    private String upiId;

    UPI(String upiId) {
        this.upiId = upiId;
    }

    @Override
    public boolean validateDetails() {
        return upiId.contains("@");
    }

    @Override
    public boolean pay(double amount) {

        if (!validateDetails()) {
            return false;
        }

        System.out.println(
            "Payment Validated. Charged: " + amount
        );

        return true;
    }
}

class CheckoutService {

    public void checkout(
            PaymentMethod paymentMethod,
            double amount) {

        if (paymentMethod.validateDetails()) {

            boolean success =
                paymentMethod.pay(amount);

            if (success) {
                System.out.println("Status: SUCCESS");
            } else {
                System.out.println("Status: FAILED");
            }

        } else {

            System.out.println(
                "Invalid Payment Details. Status: FAILED"
            );
        }
    }
}

public class Main {

    public static void main(String[] args) {

        CheckoutService service =
            new CheckoutService();

        // UPI
        PaymentMethod upi =
            new UPI("user@okbank");

        service.checkout(upi, 500.0);

        System.out.println();

        // Invalid Credit Card
        PaymentMethod card =
            new CreditCard("1234567890");

        service.checkout(card, 1000.0);
    }
}

Output
Payment Validated. Charged: 500.0
Status: SUCCESS

Invalid Payment Details. Status: FAILED

Concepts Used
- Interface
- Abstraction
- Runtime polymorphism
- Method overriding
- Encapsulation
4. Library Item Management System
Problem
Create a LibraryItem class containing:
- itemId
- title
- isBorrowed
Implement method overloading:
checkout()
checkout(int customDays)
checkout(String patronRole)

Rules:
- Normal checkout = 14 days
- Custom checkout = specified number of days
- Student = 14 days
- Faculty = 30 days
Create DigitalMedia derived from LibraryItem.
For digital media:
- checkout() should grant streaming
- isBorrowed should remain unchanged
Solution
class LibraryItem {

    protected String itemId;
    protected String title;
    protected boolean isBorrowed;

    LibraryItem(String itemId, String title) {
        this.itemId = itemId;
        this.title = title;
        this.isBorrowed = false;
    }

    // Method overloading
    public void checkout() {

        isBorrowed = true;

        System.out.println(
            "Item " + itemId +
            " checked out for 14 days."
        );
    }

    // Method overloading
    public void checkout(int customDays) {

        isBorrowed = true;

        System.out.println(
            "Item " + itemId +
            " checked out for " +
            customDays + " days."
        );
    }

    // Method overloading
    public void checkout(String patronRole) {

        int days;

        if (patronRole.equalsIgnoreCase("FACULTY")) {
            days = 30;
        } else {
            days = 14;
        }

        isBorrowed = true;

        System.out.println(
            "Item " + itemId +
            " checked out for " +
            days + " days."
        );
    }
}

class Book extends LibraryItem {

    Book(String itemId, String title) {
        super(itemId, title);
    }
}

class DigitalMedia extends LibraryItem {

    DigitalMedia(String itemId, String title) {
        super(itemId, title);
    }

    // Method overriding
    @Override
    public void checkout() {

        System.out.println(
            "Item " + itemId +
            " streaming granted. Item remains available"
        );

        // isBorrowed is intentionally NOT changed
    }
}

public class Main {

    public static void main(String[] args) {

        Book book =
            new Book("B101", "Java Core");

        book.checkout("FACULTY");

        DigitalMedia media =
            new DigitalMedia("D201", "DSA Lecture");

        media.checkout();
    }
}

Output
Item B101 checked out for 30 days.
Item D201 streaming granted. Item remains available

Concepts Used
- Method overloading
- Method overriding
- Inheritance
- Polymorphism
5. Optimal Region Merge
Problem
Given intervals such as:
[1,3]
[2,6]
[8,10]
[15,18]

Merge overlapping or touching intervals.
For example:
[1,3] + [2,6] -> [1,6]

Approach
1. Sort intervals by starting time.
2. Compare the current interval with the last merged interval.
3. If they overlap, extend the ending time.
4. Otherwise, add a new interval.
Solution
import java.util.*;

public class Main {

    public static int[][] mergeIntervals(int[][] intervals) {

        if (intervals.length == 0) {
            return new int[0][0];
        }

        Arrays.sort(
            intervals,
            (a, b) -> Integer.compare(a[0], b[0])
        );

        List<int[]> result = new ArrayList<>();

        int start = intervals[0][0];
        int end = intervals[0][1];

        for (int i = 1; i < intervals.length; i++) {

            int currentStart = intervals[i][0];
            int currentEnd = intervals[i][1];

            if (currentStart <= end) {

                end = Math.max(end, currentEnd);

            } else {

                result.add(new int[]{start, end});

                start = currentStart;
                end = currentEnd;
            }
        }

        result.add(new int[]{start, end});

        return result.toArray(new int[result.size()][]);
    }

    public static void main(String[] args) {

        int[][] intervals = {
            {1, 3},
            {2, 6},
            {8, 10},
            {15, 18}
        };

        int[][] result = mergeIntervals(intervals);

        for (int[] interval : result) {
            System.out.println(
                interval[0] + " " + interval[1]
            );
        }
    }
}

Output
1 6
8 10
15 18

Time Complexity
O(n log n)

because of sorting.
6. Reverse Workflow
Problem
Given an array representing a workflow:
1 2 3 4 5

Reverse the workflow:
5 4 3 2 1

Simple Array Solution
public class Main {

    public static void reverse(int[] arr) {

        int left = 0;
        int right = arr.length - 1;

        while (left < right) {

            int temp = arr[left];
            arr[left] = arr[right];
            arr[right] = temp;

            left++;
            right--;
        }
    }

    public static void main(String[] args) {

        int[] arr = {1, 2, 3, 4, 5};

        reverse(arr);

        for (int value : arr) {
            System.out.print(value + " ");
        }
    }
}

Output
5 4 3 2 1

Linked List Version
If the question specifically expects linked-list pointer manipulation:
class Node {

    int data;
    Node next;

    Node(int data) {
        this.data = data;
    }
}

public class Main {

    public static Node reverse(Node head) {

        Node previous = null;
        Node current = head;

        while (current != null) {

            Node nextNode = current.next;

            current.next = previous;

            previous = current;
            current = nextNode;
        }

        return previous;
    }

    public static void printList(Node head) {

        Node current = head;

        while (current != null) {

            System.out.print(current.data + " ");

            current = current.next;
        }
    }

    public static void main(String[] args) {

        Node head = new Node(1);
        head.next = new Node(2);
        head.next.next = new Node(3);
        head.next.next.next = new Node(4);
        head.next.next.next.next = new Node(5);

        head = reverse(head);

        printList(head);
    }
}

Output
5 4 3 2 1

Complexity
Time: O(n)
Space: O(1)

7. Shortest Route Grid
Problem
A drone starts at the top-left corner of a grid and must reach the bottom-right corner.
It can move only:
- Right
- Down
Each cell has a cost.
Find the minimum total cost.
Example
1 3 1
1 5 1
4 2 1

Minimum cost:
7

Dynamic Programming Solution
public class Main {

    public static int minPathCost(int[][] grid) {

        int rows = grid.length;
        int cols = grid[0].length;

        int[][] dp = new int[rows][cols];

        dp[0][0] = grid[0][0];

        // First row
        for (int j = 1; j < cols; j++) {
            dp[0][j] =
                dp[0][j - 1] + grid[0][j];
        }

        // First column
        for (int i = 1; i < rows; i++) {
            dp[i][0] =
                dp[i - 1][0] + grid[i][0];
        }

        // Remaining cells
        for (int i = 1; i < rows; i++) {

            for (int j = 1; j < cols; j++) {

                dp[i][j] =
                    Math.min(
                        dp[i - 1][j],
                        dp[i][j - 1]
                    ) + grid[i][j];
            }
        }

        return dp[rows - 1][cols - 1];
    }

    public static void main(String[] args) {

        int[][] grid = {
            {1, 3, 1},
            {1, 5, 1},
            {4, 2, 1}
        };

        System.out.println(
            minPathCost(grid)
        );
    }
}

Output
7

Complexity
Time: O(rows × cols)
Space: O(rows × cols)

8. Robot Path - Spiral Traversal
Problem
A robot starts at the top-left corner and scans the matrix in clockwise spiral order:
1. Move right
2. Move down
3. Move left
4. Move up
5. Continue inward
Example:
1 2 3
4 5 6
7 8 9

Output:
1 2 3 6 9 8 7 4 5

Solution
import java.util.*;

public class Main {

    public static List<Integer> spiralOrder(
            int[][] matrix) {

        List<Integer> result =
            new ArrayList<>();

        if (matrix == null ||
            matrix.length == 0) {
            return result;
        }

        int top = 0;
        int bottom = matrix.length - 1;
        int left = 0;
        int right = matrix[0].length - 1;

        while (top <= bottom &&
               left <= right) {

            // Move right
            for (int j = left; j <= right; j++) {
                result.add(matrix[top][j]);
            }

            top++;

            // Move down
            for (int i = top; i <= bottom; i++) {
                result.add(matrix[i][right]);
            }

            right--;

            // Move left
            if (top <= bottom) {

                for (int j = right; j >= left; j--) {
                    result.add(matrix[bottom][j]);
                }

                bottom--;
            }

            // Move up
            if (left <= right) {

                for (int i = bottom; i >= top; i--) {
                    result.add(matrix[i][left]);
                }

                left++;
            }
        }

        return result;
    }

    public static void main(String[] args) {

        int[][] matrix = {
            {1, 2, 3},
            {4, 5, 6},
            {7, 8, 9}
        };

        List<Integer> result =
            spiralOrder(matrix);

        for (int value : result) {
            System.out.print(value + " ");
        }
    }
}

Output
1 2 3 6 9 8 7 4 5

Complexity
Time: O(rows × cols)
Space: O(rows × cols)

9. Reverse Vowel Swapper
Problem
Given a lowercase string, reverse only its vowels.
Consonants must remain in their original positions.
Example:
hello

Vowels:
e o

Reverse:
o e

Result:
holle

Solution
public class Main {

    public static String reverseVowels(String str) {

        char[] arr = str.toCharArray();

        int left = 0;
        int right = arr.length - 1;

        while (left < right) {

            while (left < right &&
                   !isVowel(arr[left])) {
                left++;
            }

            while (left < right &&
                   !isVowel(arr[right])) {
                right--;
            }

            if (left < right) {

                char temp = arr[left];
                arr[left] = arr[right];
                arr[right] = temp;

                left++;
                right--;
            }
        }

        return new String(arr);
    }

    public static boolean isVowel(char c) {

        return c == 'a' ||
               c == 'e' ||
               c == 'i' ||
               c == 'o' ||
               c == 'u';
    }

    public static void main(String[] args) {

        System.out.println(
            reverseVowels("hello")
        );

        System.out.println(
            reverseVowels("aeiou")
        );
    }
}

Output
holle
uoiea

Complexity
Time: O(n)
Space: O(n)

10. Smart Notification Filter System
Problem
Each notification has the format:
<APP_CODE><PRIORITY>

Example:
A1
B3
Z5

Rules:
1. Discard notifications with priority greater than 5.
2. Calculate score:
score = alphabet position of APP_CODE × PRIORITY

For example:
A1

means:
A = 1
priority = 1

score = 1 × 1 = 1

For:
C4

C = 3
priority = 4

score = 3 × 4 = 12

Solution
import java.util.*;

public class Main {

    public static int[] calculateScores(
            String[] notifications) {

        List<Integer> scores =
            new ArrayList<>();

        for (String notification : notifications) {

            if (notification == null ||
                notification.length() < 2) {
                continue;
            }

            char appCode =
                notification.charAt(0);

            int priority =
                Integer.parseInt(
                    notification.substring(1)
                );

            // Discard priority > 5
            if (priority > 5) {
                continue;
            }

            int position =
                appCode - 'A' + 1;

            int score =
                position * priority;

            scores.add(score);
        }

        int[] result =
            new int[scores.size()];

        for (int i = 0; i < scores.size(); i++) {
            result[i] = scores.get(i);
        }

        return result;
    }

    public static void main(String[] args) {

        String[] notifications = {
            "A1",
            "C4",
            "B6",
            "D2"
        };

        int[] result =
            calculateScores(notifications);

        for (int value : result) {
            System.out.print(value + " ");
        }
    }
}

Output
1 12 8

B6 is ignored because its priority is greater than 5.
Quick Pattern Revision
Problem	Main Concept	Pattern
Toll Booth	OOP	Abstract class + HashSet
Bank Account	OOP	Interface + Custom Exception
Payment Gateway	OOP	Interface + Runtime Polymorphism
Library System	OOP	Overloading + Overriding
Optimal Region Merge	Arrays	Sorting + Intervals
Reverse Workflow	Linked List / Array	Two pointers
Shortest Route Grid	Dynamic Programming	Grid DP
Robot Path	Matrix	Spiral Traversal
Reverse Vowels	String	Two Pointers
Smart Notification	String + Array	Parsing + ASCII/Alphabet Position


Most Important Patterns to Remember
1. Two Pointers
Used in:
- Reverse array
- Reverse linked list
- Reverse vowels
Basic pattern:
int left = 0;
int right = arr.length - 1;

while (left < right) {

    // process

    left++;
    right--;
}

2. HashSet
Used when the question asks for:
- Unique elements
- Duplicate detection
- Already processed items
HashSet<String> set = new HashSet<>();

if (set.contains(value)) {
    // duplicate
} else {
    set.add(value);
}

3. Dynamic Programming Grid
For movement only right/down:
dp[i][j] =
    Math.min(
        dp[i - 1][j],
        dp[i][j - 1]
    ) + grid[i][j];

4. Spiral Matrix
Remember the four boundaries:
top
bottom
left
right

Then repeatedly:
right
down
left
up

5. Custom Checked Exception
class MyException extends Exception {

    MyException(String message) {
        super(message);
    }
}

Use:
try {
    // risky operation
}
catch (MyException e) {
    System.out.println(e.getMessage());
}

6. Interface + Polymorphism
interface PaymentMethod {
    boolean validateDetails();
    boolean pay(double amount);
}

Then:
PaymentMethod payment =
    new UPI("user@bank");

The same reference can point to different implementations.
Exam Strategy
For these questions, identify the keyword first:
Keyword in Question	Think Of
abstract	Abstract class
interface	Interface
extends	Inheritance
implements	Interface
override	@Override
overloading	Same method name, different parameters
custom exception	extends Exception
unique	HashSet
overlap intervals	Sort + merge
reverse	Two pointers
minimum path	Dynamic Programming
spiral	Four matrix boundaries
reverse vowels	Two pointers
priority + parsing	String processing
Yes. I’ll give you only the MCQs from the screenshots, with:
1. Question
2. Correct option
3. Step-by-step solution
4. Why the correct option is correct
5. Why the other options are wrong
6. Exam shortcut/concept
Q3 — Queue
Question
What will be the output of the following code?
int[] numbers = {12, 18, 17, 16, 28, 34, 36};
int count = 6;

for (int number : numbers) {
    if (count == 0) {
        break;
    }

    if (number % count == 0) {
        queue.enqueue(number);
    }

    --count;
}

Answer: 12 28 34
Solution
Start with count = 6.
Number	count	number % count	Enqueue?
12	6	12 % 6 = 0	✅ 12
18	5	18 % 5 = 3	❌
17	4	17 % 4 = 1	❌
16	3	16 % 3 = 1	❌
28	2	28 % 2 = 0	✅ 28
34	1	34 % 1 = 0	✅ 34
36	0	break	❌


So:
Front → 12 28 34 ← Rear

Why other possible options are wrong
- 12 16 28 34 ❌ — 16 % 3 = 1, so 16 is not inserted.
- 12 28 34 36 ❌ — after processing 34, count becomes 0, so the loop breaks before 36.
- Any option containing 18 or 17 ❌ — neither is divisible by the current count.
Shortcut
Whenever you see:
number % count == 0

just make a small table of current number + current count. Remember that count-- happens after the check.
Q4 — TreeSet
Question
What will be the output?
The code adds:
"A"
"B"
"C"
"C"
"E"
"D"
"a"
"F"

to a TreeSet<String> and prints it.
Correct Answer: A B C D E F a
Why?
TreeSet:
- Removes duplicates.
- Stores elements in sorted/natural order.
- For strings, uppercase letters come before lowercase letters.
Therefore:
A B C D E F a

The second "C" is ignored because sets cannot contain duplicates.
Why other options are wrong
- A a B C D E F ❌ — TreeSet doesn't put lowercase a between A and B.
- A B C C E D a F ❌ — TreeSet sorts the values and removes duplicate C.
- A B C D E F a ✅ — correct natural ordering.
Shortcut
TreeSet = Sorted + No duplicates

Think:
HashSet       → unique, no guaranteed order
LinkedHashSet → unique + insertion order
TreeSet       → unique + sorted order

Q6 — Time Complexity
Question
int number1 = 0, counter = 10;

while (counter > 0) {
    number1 += counter;
    counter /= 2;
}

What is the time complexity?
Options:
- O(n)
- O(sqrt(n))
- O(n/2)
- O(log n)
Correct Answer: O(log n)
Why?
counter is repeatedly divided by 2:
n
n/2
n/4
n/8
n/16
...
1
0

The number of iterations is approximately:
log₂(n)

Therefore:
Time = O(log n)

Why others are wrong
- O(n) ❌ — counter isn't decreasing by 1.
- O(sqrt(n)) ❌ — there is no square-root pattern.
- O(n/2) ❌ — division by 2 each iteration gives logarithmic complexity.
- O(log n) ✅
Shortcut
If you see:
n /= 2;

or
n *= 2;

inside a loop → usually O(log n).
Q7 — LinkedHashSet
Question
The code adds approximately:
A
B
C
C
E
D
E
null
E

to a LinkedHashSet.
What is printed?
Correct Answer: A B C E D null
Step-by-step
LinkedHashSet has two important properties:
1. No duplicates.
2. Maintains insertion order.
Insertion:
A → add
B → add
C → add
C → duplicate ❌
E → add
D → add
E → duplicate ❌
null → add
E → duplicate ❌

Final:
A B C E D null

Why others are wrong
- A B C D null E ❌ — that would imply sorting or changing insertion order.
- A B C C E D E null E ❌ — duplicates are not allowed.
- Compilation error because null cannot be added ❌ — HashSet/LinkedHashSet allows one null.
- A B C E D null ✅
Shortcut
LinkedHashSet = HashSet + insertion order

Q8 — LinkedList + Digit Sum
Question
The list contains:
10, 12, 33, 44, 75, 67

For every number, the code calculates the sum of its digits and adds it to a cumulative sum.
Whenever:
sum % 2 == 0

it prints "Infosys".
How many times is "Infosys" displayed?
Correct Answer: 4 times
Step-by-step
Digit sums:
10 → 1 + 0 = 1
12 → 1 + 2 = 3
33 → 3 + 3 = 6
44 → 4 + 4 = 8
75 → 7 + 5 = 12
67 → 6 + 7 = 13

But sum is cumulative:
Number	Digit sum	Cumulative sum	Even?
10	1	1	❌
12	3	4	✅ Infosys
33	6	10	✅ Infosys
44	8	18	✅ Infosys
75	12	30	✅ Infosys
67	13	43	❌


Therefore:
Infosys
Infosys
Infosys
Infosys

Answer: 4 times
Important trick
Don't check whether each number's digit sum is even.
You must check:
cumulative sum % 2

That's the trap in this question.
Q9 — HashMap replace()
Question
The code essentially does:
studentDetails.put("Max", 337);
studentDetails.put("Stocks", 480);
studentDetails.put("Malinda", 570);
studentDetails.put("Mathew", 640);

studentDetails.put("Max", 340);

if (studentDetails.replace("Stocks", 480, 650)) {
    studentDetails.remove("Max");
} else {
    studentDetails.put("Sam", 490);
}

How many key-value pairs will remain?
Correct answer: 3 key-value pairs
⚠️ Important: the screenshot's listed options appear not to contain 3. So the MCQ itself appears to have an incorrect/missing option.
Step 1 — Understand duplicate Max
Initially:
Max → 337
Stocks → 480
Malinda → 570
Mathew → 640

Then:
put("Max", 340);

This doesn't create another Max.
It updates:
Max → 340

So there are still 4 keys.
Step 2 — Understand replace()
replace("Stocks", 480, 650)

This means:
If "Stocks" currently has value 480, change it to 650.

It does:
Stocks → 650

and returns:
true

Therefore the if executes:
studentDetails.remove("Max");

Now:
Stocks → 650
Malinda → 570
Mathew → 640

Final count:
3 key-value pairs.
Why the common options are wrong
- 6 ❌ — put() with an existing key does not add another key.
- 4 ❌ — Max is removed after replace() returns true.
- 5 ❌ — Sam is added only in the else, but replace() succeeds.
- Compilation error ❌ — duplicate keys are completely legal in a HashMap; the old value is replaced.
Very important HashMap rule
map.put("A", 10);
map.put("A", 20);

does NOT give two A's.
It gives:
A → 20

Q10 — Best Case Analysis
Question
Which asymptotic notation is used to represent the best-case analysis of an algorithm?
Options:
- Big Theta
- Big Omega
- Big Oh
- There is no asymptotic notation
Correct Answer: Big Omega (Ω)
Why?
The common association is:
Analysis	Notation
Best case / lower bound	Ω
Average/tight bound	Θ
Worst case / upper bound	O


So the expected MCQ answer is:
Big Omega (Ω).
Why others are wrong
- Big Theta ❌ — Θ represents a tight asymptotic bound, not specifically "best case."
- Big O ❌ — O represents an upper bound and is commonly associated with worst-case analysis.
- No notation ❌ — best case can absolutely be expressed asymptotically.
Shortcut
Remember:
Ω → Minimum / Best
Θ → Tight / Exact order
O → Maximum / Worst

Q14 — Stack
Question
The stack receives:
stack.push(18);
stack.push(10);
stack.push(24);
stack.push(56);
stack.push(27);

So:
Bottom → 18  10  24  56  27 ← Top

Then:
for (int i = 0; i < 2; i++) {
    if (stack.pop() % 3 == 0) {
        int temp = stack.pop();
        stack.push(++temp);
        stack.push(++temp);
    }
}

What is the final stack from top to bottom?
Correct Answer: 57 24 10 18
Step 1
Top is:
27

First:
stack.pop()

removes 27.
Check:
27 % 3 = 0

So condition is true.
Now:
temp = stack.pop();

removes 56.
Then:
stack.push(++temp);

temp = 57, push 57.
Then:
stack.push(++temp);

temp = 58, push 58.
Stack:
Bottom → 18 10 24 57 58 ← Top

Step 2
Second loop:
stack.pop()

removes:
58

Check:
58 % 3 != 0

So nothing else happens.
Final stack:
Bottom → 18 10 24 57

Therefore top → bottom:
57 24 10 18

Why other answers are wrong
Any answer containing 58 is wrong because the second iteration pops 58.
Any answer containing 27 is wrong because 27 was popped during the first iteration.
Any answer containing 56 is wrong because 56 was also popped and replaced by 57 and 58.
Shortcut
For stack questions:
LIFO = Last In, First Out

Always write the stack vertically or explicitly mark TOP.
Q16 — Bubble Sort
Question
What is the time complexity of Bubble Sort?
Options:
- O(n × n)
- O(n × log n)
- O(n)
- O(1)
Correct Answer: O(n²)
The option written as:
O(n*n)

is the same as:
O(n²)

Why?
Bubble sort repeatedly compares adjacent elements.
For n elements, roughly:
n comparisons
n-1 comparisons
n-2 comparisons
...

Total is approximately:
n(n-1)/2

which simplifies to:
O(n²)

Why others are wrong
- O(n log n) ❌ — typical of Merge Sort/Heap Sort, not ordinary Bubble Sort.
- O(n) ❌ — only possible for an optimized Bubble Sort's best case when already sorted, not the general complexity.
- O(1) ❌ — would mean constant time regardless of input size.
- O(n × n) = O(n²) ✅
Shortcut
Bubble Sort → O(n²)
Selection Sort → O(n²)
Insertion Sort → O(n²) average/worst
Merge Sort → O(n log n)
Quick Sort → O(n log n) average

Final Answers — Quick Revision
Q	Topic	Correct Answer
3	Queue	12 28 34
4	TreeSet	A B C D E F a
6	Complexity	O(log n)
7	LinkedHashSet	A B C E D null
8	Digit sum + LinkedList	Infosys 4 times
9	HashMap	3 pairs — screenshot options appear faulty
10	Best-case notation	Big Omega (Ω)
14	Stack	57 24 10 18
16	Bubble Sort	O(n²)


Most important concepts these questions are testing
Memorize these for the assessment:
HashSet       → unique, unordered
LinkedHashSet → unique + insertion order
TreeSet       → unique + sorted order

Stack → LIFO
Queue → FIFO

HashMap duplicate key → updates old value

n /= 2 → O(log n)

Bubble Sort → O(n²)

O   → upper bound
Ω   → lower bound
Θ   → tight bound

Q9 is the only one where the screenshot's answer choices don't match the actual Java execution. The mathematically/programmatically correct result is 3 pairs.
Q2 — Encapsulation, Getters & Arithmetic
Question
An Employee object has:
- basicSalary = 2150
- employeeId = 101
- bonusPercent = 8
Calculate and display the total salary.
✅ Correct Answer
this.totalSalary =
    this.basicSalary + (this.basicSalary * bonusPercent / 100.0);

and:
System.out.println(
    "Employee Id: " + employee.getEmployeeId() +
    ", Total salary: " + employee.getTotalSalary()
);

Why?
Bonus:
2150 × 8 / 100 = 172

Total:
2150 + 172 = 2322

Therefore:
Employee Id: 101, Total salary: 2322.0

🧠 Remember
Percentage calculation:
amount + (amount × percentage / 100)

Using 100.0 ensures floating-point calculation.
Q9 — Aggregation + Access Modifiers
Question
Customer contains a public Address object:
class Customer {
    public Address address;
}

Address contains:
private int zipCode;

but provides:
public int getZipCode()

How should zipCode be accessed from main()?
✅ Correct Answer
System.out.println(customer.address.getZipCode());

Why?
There are two levels:
customer
   ↓
address
   ↓
getZipCode()
   ↓
zipCode

address is public, so we can access it.
But zipCode is private, so we cannot directly do:
customer.address.zipCode ❌

We must use the public getter:
customer.address.getZipCode() ✅

Why other options are wrong
- address.getZipCode() ❌ — address is an instance variable, so inside static main() you need the object customer.
- customer.zipCode ❌ — zipCode belongs to Address, not Customer.
- customer.address.zipCode ❌ — zipCode is private.
🧠 Exam Shortcut
private variable → getter
private data
     ↓
public get method

Q11 — do-while + Integer Division + break
Question
The program starts with:
num1 = 2
num2 = 20

and uses:
do {
    num2 = num2 / num1;

    if (num1 > num2) {
        break;
    }

    num2--;
} while (++num1 < 5);

✅ Correct Answer: num1 = 4, num2 = 0
Step-by-step
Iteration 1
num1 = 2
num2 = 20 / 2 = 10

Check:
2 > 10 → false

So:
num2-- → 9

Condition:
++num1 → 3
3 < 5 → true

Iteration 2
num1 = 3
num2 = 9 / 3 = 3

3 > 3 → false

Then:
num2-- → 2

Condition:
++num1 → 4
4 < 5 → true

Iteration 3
num1 = 4
num2 = 2 / 4

Java integer division:
2 / 4 = 0

Now:
4 > 0 → true

So break executes.
Final:
num1 = 4
num2 = 0

🧠 Exam Shortcut
For int / int:
2 / 4 = 0
5 / 2 = 2
9 / 4 = 2

The decimal part is discarded.
Q8 — Nested if-else + Negative Numbers
Given
num1 = -20
num2 = -30
num3 = 10
num4 = -40

✅ Correct Answer: 3
Step 1
num1 + num2 >= num4

Substitute:
-20 + (-30) >= -40
-50 >= -40

False.
So execution goes to else.
Step 2
num2 / num1 > 0

-30 / -20 = 1

Because Java integer division gives 1.
1 > 0 → true

So evaluate:
num1 < num2 || num4 % num3 == 0

First:
-20 < -30 → false

Second:
-40 % 10 == 0 → true

Therefore:
false || true
= true

So it prints:
3

🧠 Exam Shortcut
Remember:
negative ÷ negative = positive
negative % positive can be negative/zero

And for ||:
true || anything = true

Q6 — Recursion
Question
The method behaves like:
demo(x, y)

if (...) ...
else if (y > x)
    return 0;
else
    return y + demo(x - 1, y + 1);

Called with:
demo(5, 1)

✅ Correct Answer: 6
Trace
demo(5,1)
= 1 + demo(4,2)

= 1 + 2 + demo(3,3)

= 1 + 2 + 3 + demo(2,4)

Now:
y > x
4 > 2

True.
Therefore:
demo(2,4) = 0

So:
1 + 2 + 3 + 0
= 6

🧠 Exam Shortcut
For recursion, write every call on a separate line.
Don't try to calculate everything mentally.
Q7 — Static vs Instance Variables
Question
Suppose:
private int employeeId;
private static int counter = 1000;

Three objects are created:
employeeId = ++counter;

Then details are printed.
✅ Correct Answer
1001 1003
1002 1003
1003 1003

Why?
counter is static.
That means there is only one shared counter.
Initially:
counter = 1000

First object:
++counter → 1001
employeeId = 1001

Second:
++counter → 1002
employeeId = 1002

Third:
++counter → 1003
employeeId = 1003

After all three objects are created:
counter = 1003

So every object sees the same static value:
Object 1 → 1001 1003
Object 2 → 1002 1003
Object 3 → 1003 1003

🧠 Key Difference
Instance variable:
Each object has its own copy.

Static variable:
All objects share one copy.

Q4 — try-catch-finally + Exception Propagation
Question
validateStudent() loops using:
index <= studentId.length

and has a finally block.
main() calls it inside:
try {
    ...
}
catch (ArrayIndexOutOfBoundsException e) {
    ...
}
finally {
    ...
}

✅ Correct Answer: P Q S T
Why?
The array length is 3, so valid indexes are:
0
1
2

But:
index <= studentId.length

eventually allows:
index = 3

studentId[3] is invalid.
Therefore:
ArrayIndexOutOfBoundsException

occurs.
Execution order
At the valid matching index:
P

Then exception occurs.
Before leaving validateStudent(), its finally executes:
Q

The exception reaches main().
The normal statement after the method call is skipped, so R is not printed.
The catch executes:
S

Then main()'s finally executes:
T

Final:
P Q S T

🧠 Golden Rule
When an exception occurs:
try → finally → catch → finally

More precisely here:
inner try
   ↓
inner finally
   ↓
outer catch
   ↓
outer finally

Q3 — Private Method
Question
Calculator has:
private int add(int num1, int num2)

Tester tries to call add().
✅ Correct Answer
Compilation error because add() is private.
Why?
private means the method can only be accessed inside the same class.
So:
class Calculator {
    private int add(...) { }
}

can be called from inside Calculator:
add(10, 20);       // ✅

But not from:
class Tester {
    ...
}

calculator.add(10,20);   // ❌

🧠 Access modifier shortcut
private  → same class only
default  → same package
protected → package + subclasses
public   → everywhere

Q5 — Type Casting + Integer Division
Given
noOfItems = 10
pricePerItem = 255.6f
discountPercentage = 7
taxAmount = 135.50f

The calculation contains:
(noOfItems * (int) pricePerItem)
*
(1 - discountPercentage / 100)

✅ Correct Answer: 2685.5
Step 1 — Explicit casting
(int) 255.6f

becomes:
255

The .6 is discarded.
So:
10 × 255 = 2550

Step 2 — Discount calculation
Important trap:
discountPercentage / 100

Both are integers.
Therefore:
7 / 100 = 0

NOT:
0.07

So:
1 - 0 = 1

Discounted amount:
2550 × 1 = 2550

Add tax:
2550 + 135.50
= 2685.50

✅ Final Answer
2685.5

🧠 Biggest Trap
In Java:
7 / 100

is:
0

But:
7 / 100.0

is:
0.07

Q1 — Method Overriding + Variable Shadowing
Given
Class A has:
count = 10;

Its method1() contains a local variable:
int count = 20;

and returns:
this.count;

Class B overrides method1():
return this.count = 15;

Class C overrides method2():
return 40;

The expression is:
obj1.method1() + obj3.method1() + obj3.method2()

✅ Correct Answer: 65
Part 1
obj1 belongs to A.
Inside:
int count = 20;
return this.count;

There are two counts:
local count       = 20
instance count    = 10

this.count specifically means the instance variable.
Therefore:
obj1.method1() = 10

Part 2
obj3.method1() uses B's overridden method:
return this.count = 15;

This means:
this.count = 15

and then returns:
15

Therefore:
obj3.method1() = 15

Part 3
C overrides method2():
return 40;

Therefore:
obj3.method2() = 40

Final:
10 + 15 + 40
= 65

🧠 Important Trap
These are different:
return count;

and
return this.count;

If a local variable has the same name, this.count means:
the object's instance variable

Also:
this.count = 15;

does two things:
1. Changes the instance variable to 15
2. Returns 15 because assignment itself is an expression
🔥 Most Important Rules From This Set
Concept	Rule to remember
private	Same class only
Getter	Used to access private fields
static	Shared by all objects
Instance variable	Separate copy for each object
this.x	Object's instance variable
Local variable	Can shadow an instance variable
++x	Increment first, then use
x++	Use first, then increment
--x	Decrement first, then use
int / int	Integer division
(int) 255.6	255
7 / 100	0
finally	Executes even when exception occurs
break	Immediately exits the loop/switch
do-while	Executes at least once
`	
&&	Both sides must be true
String	Immutable
final method	Cannot be overridden


These are exactly the kinds of small Java traps that tend to appear in DSA/Java objective assessments.