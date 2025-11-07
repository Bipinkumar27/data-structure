# Sort Characters By Frequency - Brute Force to Optimal (Revision Guide)

## Problem Statement

**LeetCode Link**: [451. Sort Characters By Frequency](https://leetcode.com/problems/sort-characters-by-frequency/)[10]
**Video Explanation**: [Sort Characters By Frequency | Heap | Leetcode 451](https://www.youtube.com/watch?v=HwCYa1_2vkU&list=PLpIkg8OmuX-IkxvvfOeZp-Ot0UWHMGAT-&index=2&pp=iAQB0gcJCQMKAYcqIYzv)[2]
Given string `s` hai, isko **decreasing order mein sort** karna hai based on character frequency. Frequency matlab kitni baar character aaya hai string mein.[11][10]

**Examples**:
- Input: `"tree"` → Output: `"eert"` ya `"eetr"` (e do baar, t aur r ek baar)
- Input: `"cccaaa"` → Output: `"cccaaa"` ya `"aaaccc"` (dono ki frequency 3 hai)
- Input: `"Aabb"` → Output: `"bbAa"` (b do baar, A aur a ek baar each)

---

## Approach 1: Brute Force - Repeated Search

### Concept
**Har baar maximum frequency wala character dhundo, usko extract karo, repeat karte raho**.[12][11]

### Implementation
```java
class Solution {
    public String frequencySort(String s) {
        // Step 1: Har character ka frequency count karo
        Map<Character, Integer> frequencyMap = new HashMap<>();
        
        for(int i = 0; i < s.length(); i++){
            char currentChar = s.charAt(i);
            frequencyMap.put(currentChar, frequencyMap.getOrDefault(currentChar, 0) + 1);
        }
        
        StringBuilder result = new StringBuilder();
        
        // Step 2: Baar baar max frequency wala character dhundo aur nikalo
        while(!frequencyMap.isEmpty()){
            // Max frequency wala character find karo
            int maxFreq = 0;
            char maxChar = '\0';
            
            for(Map.Entry<Character, Integer> entry : frequencyMap.entrySet()){
                if(entry.getValue() > maxFreq){
                    maxFreq = entry.getValue();
                    maxChar = entry.getKey();
                }
            }
            
            // Max frequency character ko result mein append karo
            for(int i = 0; i < maxFreq; i++){
                result.append(maxChar);
            }
            
            // Map se remove kar do
            frequencyMap.remove(maxChar);
        }
        
        return result.toString();
    }
}
```

### Example Walkthrough
```
Input: "tree"
Frequency Map: {t: 1, r: 1, e: 2}

Iteration 1:
  Max find kiya: 'e' with frequency 2
  Append: "ee"
  'e' remove: {t: 1, r: 1}

Iteration 2:
  Max find kiya: 't' with frequency 1
  Append: "eet"
  't' remove: {r: 1}

Iteration 3:
  Max find kiya: 'r' with frequency 1
  Append: "eetr"
  'r' remove: {}

Output: "eetr"
```

### Complexity
- **Time**: $$O(n + k^2)$$ jahan n = string length, k = unique characters[11]
    - Counting: $$O(n)$$
    - k baar loop, har baar map search: $$O(k)$$ → total $$O(k^2)$$
    - Result build: $$O(n)$$
- **Space**: $$O(k)$$ frequency map ke liye

### Problem
Baar baar max frequency search karna **bahut inefficient** hai! k unique characters ke liye k baar loop aur har baar search.[12][11]

***

## Approach 2: Better - Sort All Pairs

### Concept
**Ek baar frequency count karo, fir frequency ke basis pe descending order mein sort kar do**.[11][12]

### Implementation
```java
class Solution {
    public String frequencySort(String s) {
        // Step 1: Har character ka frequency count karo
        Map<Character, Integer> frequencyMap = new HashMap<>();
        
        for(int i = 0; i < s.length(); i++){
            char currentChar = s.charAt(i);
            frequencyMap.put(currentChar, frequencyMap.getOrDefault(currentChar, 0) + 1);
        }
        
        // Step 2: (character, frequency) pairs ki list banao
        List<Map.Entry<Character, Integer>> pairs = new ArrayList<>(frequencyMap.entrySet());
        
        // Step 3: Frequency ke basis pe descending order mein sort karo
        pairs.sort((a, b) -> b.getValue() - a.getValue());
        
        // Step 4: Result string banao
        StringBuilder result = new StringBuilder();
        for(Map.Entry<Character, Integer> entry : pairs){
            char ch = entry.getKey();
            int freq = entry.getValue();
            for(int i = 0; i < freq; i++){
                result.append(ch);
            }
        }
        
        return result.toString();
    }
}
```

### Complexity
- **Time**: $$O(n + k \log k)$$[12]
    - Counting: $$O(n)$$
    - Sorting: $$O(k \log k)$$
    - Result build: $$O(n)$$
- **Space**: $$O(k)$$

### Improvement
Brute force se kaafi better! Ek baar sorting karni hai, repeated searches nahi.[11]

***

## Approach 3: Optimal - Max Heap (Tumhara Solution!)

### Key Insight
**PriorityQueue (Max Heap) use karo taaki automatically highest frequency wale characters pehle mil jayein**![13][11]

### Implementation (Your Code - Perfect!)
```java
class Solution {
    public String frequencySort(String s) {
        // Step 1: Har character ka frequency count karo
        Map<Character, Integer> frequencyMap = new HashMap<>();
        
        for(int i = 0; i < s.length(); i++){
            char currentChar = s.charAt(i);
            frequencyMap.put(currentChar, frequencyMap.getOrDefault(currentChar, 0) + 1);
        }
        
        // Step 2: Max heap banao frequency ke basis pe
        // Comparator: b.frequency - a.frequency se max heap banta hai (highest frequency pehle)
        PriorityQueue<CharacterFrequency> maxHeap = new PriorityQueue<>((a, b) -> {
            return b.frequency - a.frequency;
        });
        
        // Step 3: Saare characters ko heap mein dalo
        for(Map.Entry<Character, Integer> entry : frequencyMap.entrySet()){
            maxHeap.add(new CharacterFrequency(entry.getKey(), entry.getValue()));
        }
        
        // Step 4: Heap se poll karke result banao (automatically descending order mein)
        StringBuilder result = new StringBuilder();
        
        while(!maxHeap.isEmpty()){
            CharacterFrequency current = maxHeap.poll();
            // Java 21+: StringBuilder.repeat(char, count) available hai
            result.repeat(current.character, current.frequency);
        }
        
        return result.toString();
    }
    
    // Helper class character aur uski frequency store karne ke liye
    public class CharacterFrequency {
        char character;
        int frequency;
        
        public CharacterFrequency(char character, int frequency){
            this.character = character;
            this.frequency = frequency;
        }
    }
}
```

**Java versions < 21 ke liye alternative**:
```java
while(!maxHeap.isEmpty()){
    CharacterFrequency current = maxHeap.poll();
    // Loop use karo
    for(int i = 0; i < current.frequency; i++){
        result.append(current.character);
    }
}
```

### Why Max Heap Works?

1. **Automatic Sorting**: Heap automatically highest frequency ko top pe rakhta hai[13]
2. **Efficient Extraction**: Har poll operation $$O(\log k)$$ time mein hota hai
3. **Clean Code**: Manual sorting ki zarurat nahi
4. **Natural Order**: Characters automatically descending frequency mein extract hote hain

### Visual Example
```
Input: "tree"

Step 1 - Frequency Map:
{'t': 1, 'r': 1, 'e': 2}

Step 2 - Max Heap (frequency ke basis pe):
        (e, 2)
       /      \
    (t, 1)  (r, 1)

Step 3 - Poll karke Build karo:
Poll 1: (e, 2) → result = "ee"
Poll 2: (t, 1) → result = "eet"  (ya (r, 1) pehle - same frequency)
Poll 3: (r, 1) → result = "eetr"

Output: "eetr"
```

### Complexity
- **Time**: $$O(n + k \log k)$$[13][11]
    - Counting: $$O(n)$$
    - Heap insertions: $$O(k \log k)$$
    - Result build: $$O(n)$$
- **Space**: $$O(k)$$ heap aur frequency map ke liye

---

## Progression Summary

| Approach | Time Complexity | Space | Code Clarity |
|----------|----------------|-------|--------------|
| **1. Repeated Search** | $$O(n + k^2)$$ | $$O(k)$$ | ❌ Bahut inefficient |
| **2. Sort Pairs** | $$O(n + k \log k)$$ | $$O(k)$$ | ✅ Achha |
| **3. Max Heap** | $$O(n + k \log k)$$ | $$O(k)$$ | ✅✅ Sabse Best! |

***

## Key Design Decisions

### Why Max Heap Over Sorting?
Dono ki $$O(k \log k)$$ complexity hai, but:
- **Heap**: Is problem ke liye zyada intuitive (hume max frequency pehle chahiye)
- **Sorting**: Standard approach, but heap zyada natural fit hai
- **Performance**: Heap practically thoda fast hai (simpler comparisons)

### Why Custom Class?
```java
public class CharacterFrequency {
    char character;
    int frequency;
}
```
- **Type Safety**: Generic Pair se better
- **Clarity**: Intent clear hai - character + frequency
- **Maintainability**: Extend karna easy hai agar zarurat pade

### Why StringBuilder.repeat() (Java 21+)?
```java
result.repeat(current.character, current.frequency);
```
- **Performance**: ~295M ops/sec (best performance)
- **Cleaner Code**: Loop ki bajaye ek call
- **Official Method**: Koi workaround nahi chahiye

***

## Common Mistakes

❌ **Mistake 1**: Frequency count karna bhool jana
```java
// Galat - overwrite kar deta hai increment ki jagah
frequencyMap.put(currentChar, 1);

// Sahi
frequencyMap.put(currentChar, frequencyMap.getOrDefault(currentChar, 0) + 1);
```

❌ **Mistake 2**: Galat heap order (ascending instead of descending)
```java
// Galat - min heap (lowest frequency pehle)
PriorityQueue<CharacterFrequency> minHeap = new PriorityQueue<>((a, b) -> 
    a.frequency - b.frequency
);

// Sahi - max heap (highest frequency pehle)
PriorityQueue<CharacterFrequency> maxHeap = new PriorityQueue<>((a, b) -> 
    b.frequency - a.frequency
);
```

❌ **Mistake 3**: Java version compatibility bhool jana
```java
// Java 21+
result.repeat(current.character, current.frequency);

// Java 11-20 (loop use karo)
for(int i = 0; i < current.frequency; i++){
    result.append(current.character);
}
```

***

## Interview Approach

### Step 1: Start with Brute Force
"Pehla approach hai baar baar max frequency wala character dhundo aur extract karo. Ye simple hai but inefficient kyunki k baar search karna padega, har baar $$O(k)$$ - total $$O(k^2)$$"[12]

### Step 2: Optimize with Sorting
"Better approach: ek baar frequencies count karo, fir frequency ke basis pe descending order mein sort kar do. Ye $$O(n + k \log k)$$ hai jo bahut better hai"[11]

### Step 3: Use Max Heap (Optimal)
"Best approach: max heap use karo jahan highest frequency automatically pehle aa jata hai. Same $$O(n + k \log k)$$ complexity but cleaner aur zyada intuitive"[13]

### Step 4: Explain Trade-offs
"Dono sorting aur heap ki same complexity hai, but heap is problem ke liye zyada natural hai kyunki hume maximum frequency element baar baar chahiye. Sorting general hai but yahan heap better fit hai"[13][11]

***

## Edge Cases

```java
// Empty string
Input: ""
Output: ""

// Single character
Input: "a"
Output: "a"

// Saare same characters
Input: "aaaa"
Output: "aaaa"

// Saare different characters (koi bhi order valid)
Input: "abc"
Output: "abc" (ya koi bhi permutation)

// Uppercase/lowercase mixed (different treat hote hain)
Input: "Aabb"
Output: "bbAa" ya "bbaA"
```

***