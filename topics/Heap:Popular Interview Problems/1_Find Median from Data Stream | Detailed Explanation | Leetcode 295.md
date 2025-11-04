Perfect! Here's your updated markdown with the YouTube video link added. There are two ways to add it - I'll show you both:

# MedianFinder - Brute Force to Optimal (Revision Guide)

## Problem Statement

**LeetCode Link**: [295. Find Median from Data Stream](https://leetcode.com/problems/find-median-from-data-stream/)[1]

**Video Explanation**: [Find Median from Data Stream - codestorywithMIK](https://www.youtube.com/watch?v=jnj87BSi9Is)[2]

Stream of numbers aa rahe hain continuously, aur tumhe **real-time median** nikalna hai efficiently.[3][2]

**Median kya hai?**
- Odd count: Middle element
- Even count: Average of two middle elements

***

## Video Tutorial

**Hindi Explanation** by codestorywithMIK - Complete walkthrough with examples:

[![Find Median from Data Stream](https://img.youtube.com/vi/jnj87BSi9Iswww.youtube.com/watch?v=jnj87BSiClick the thumbnail above to watch the detailed explanation*

***

## Approach 1: Brute Force - Sort Every Time

### Concept
Sabse simple idea: **Har baar puri list sort kar do** jab median chahiye.[6][7]

### Implementation
```java
class MedianFinderBruteForce {
    List<Integer> nums;
    
    public MedianFinderBruteForce() {
        this.nums = new ArrayList<>();
    }
    
    public void addNum(int num) {
        nums.add(num);  // Bas list mein append kar do
    }
    
    public double findMedian() {
        Collections.sort(nums);  // Har baar puri list sort karo
        int n = nums.size();
        
        if (n % 2 == 0) {
            // Even: dono middle ka average
            return (nums.get(n/2 - 1) + nums.get(n/2)) / 2.0;
        }
        return nums.get(n/2);  // Odd: middle element
    }
}
```

### Example
```
addNum(5)  → [5]         → median = 5
addNum(15) → [5,15]      → sort → [5,15]  → median = 10.0
addNum(1)  → [5,15,1]    → sort → [1,5,15] → median = 5
addNum(3)  → [5,15,1,3]  → sort → [1,3,5,15] → median = 4.0
```

### Complexity
- **addNum()**: $$O(1)$$ - simple append
- **findMedian()**: $$O(n \log n)$$ - sorting dominates[7][2]
- **Space**: $$O(n)$$

### Problem
Agar 1000 numbers hain aur 100 baar median nikalna hai, toh **100 baar 1000 elements sort** karoge - bahut wasteful![3][7]

---

## Approach 2: Better - Keep List Sorted

### Concept
Agar list ko **sorted state mein maintain** karo insertion ke time, toh median find karna instant ho jayega.[2]

### Implementation
```java
class MedianFinderSorted {
    List<Integer> nums;
    
    public MedianFinderSorted() {
        this.nums = new ArrayList<>();
    }
    
    public void addNum(int num) {
        // Binary search se sahi position dhundo
        int insertPos = Collections.binarySearch(nums, num);
        if (insertPos < 0) {
            insertPos = -(insertPos + 1);  // Insertion point
        }
        nums.add(insertPos, num);  // Sahi jagah insert karo
    }
    
    public double findMedian() {
        int n = nums.size();
        // Already sorted hai, direct access
        if (n % 2 == 0) {
            return (nums.get(n/2 - 1) + nums.get(n/2)) / 2.0;
        }
        return nums.get(n/2);
    }
}
```

### Complexity
- **addNum()**: $$O(n)$$ - binary search $$O(\log n)$$ + insertion $$O(n)$$
- **findMedian()**: $$O(1)$$ - direct access
- **Space**: $$O(n)$$

### Improvement
findMedian() instant ho gaya, but addNum() still slow hai because list ke beech mein insert karna expensive hai.[2]

***

## Approach 3: Optimal - Two Heaps (Tumhara Solution!)

### Key Insight
Median find karne ke liye **puri sorted list ki zarurat nahi**, sirf **middle elements** chahiye![8][3]

**Brilliant Idea**: Numbers ko do parts mein divide karo:
- **Left half (Max Heap)**: Chhote numbers, top pe largest of smaller half
- **Right half (Min Heap)**: Bade numbers, top pe smallest of larger half

### Visual Representation
```
Sorted Array: [1, 3, 5, 8, 15, 20]
              -------  --------
              Left      Right
              (Max)     (Min)
              
Left heap top = 5 (max of smaller half)
Right heap top = 8 (min of larger half)
Median = (5 + 8) / 2 = 6.5
```

### Implementation
```java
class MedianFinder {
    PriorityQueue<Integer> left_max_heap;   // Chhote numbers
    PriorityQueue<Integer> right_min_heap;  // Bade numbers
    
    public MedianFinder() {
        this.left_max_heap = new PriorityQueue<>((a,b) -> b-a);  // Max heap
        this.right_min_heap = new PriorityQueue<>();             // Min heap
    }
    
    public void addNum(int num) {
        // Step 1: Sahi heap mein insert karo
        if(left_max_heap.isEmpty() || num < left_max_heap.peek()){
            left_max_heap.add(num);
        }
        else{
            right_min_heap.add(num);
        }
        
        // Step 2: Balance maintain karo
        if(left_max_heap.size() - right_min_heap.size() > 1){
            right_min_heap.add(left_max_heap.peek());
            left_max_heap.poll();
        }
        else if(left_max_heap.size() < right_min_heap.size()){
            left_max_heap.add(right_min_heap.peek());
            right_min_heap.poll();
        }
    }
    
    public double findMedian() {
        if(left_max_heap.size() == right_min_heap.size()){
            return (double)(left_max_heap.peek() + right_min_heap.peek())/2;
        } 
        return left_max_heap.peek();
    }
}
```

### Why Two Heaps Work?

1. **Max Heap (Left)**: Top element = largest among smaller numbers
2. **Min Heap (Right)**: Top element = smallest among larger numbers
3. **Balance Rule**: Left size ≥ Right size, difference ≤ 1

Ye ensure karta hai ki **median hamesha heaps ke top pe available hai**![8][3]

### Example Walkthrough

```
add(5):
Left: [5]          Right: []
Median = 5

add(15):
Left: [5]          Right: [15]
Median = (5+15)/2 = 10.0

add(1):
Left: [5,1]        Right: [15]
Median = 5 (left top)

add(3):
Before balance: Left: [5,3,1]  Right: [15]  ← Imbalanced!
After balance:  Left: [3,1]    Right: [5,15]
Median = (3+5)/2 = 4.0

add(8):
Before balance: Left: [3,1]    Right: [5,8,15]  ← Imbalanced!
After balance:  Left: [5,3,1]  Right: [8,15]
Median = 5
```

### Complexity
- **addNum()**: $$O(\log n)$$ - heap operations[3][8]
- **findMedian()**: $$O(1)$$ - peek operation[8][3]
- **Space**: $$O(n)$$

***

## Progression Summary

| Approach | addNum() | findMedian() | Why Improve? |
|----------|----------|--------------|--------------|
| **1. Brute Force** | $$O(1)$$ | $$O(n \log n)$$ | Repeated sorting bahut slow[7] |
| **2. Sorted List** | $$O(n)$$ | $$O(1)$$ | List insertion expensive[2] |
| **3. Two Heaps** | $$O(\log n)$$ | $$O(1)$$ | ✅ Optimal! Best of both[3] |

---

## Key Design Decisions

### Why Max Heap for Left?
Taaki left half ka **maximum** easily accessible ho - ye median ka left part hai.[3][8]

### Why Balance is Critical?
```
Bad: Left: [1,2,3,4,5]  Right: [10]  ← Wrong median!
Good: Left: [1,2,3]     Right: [4,5,10]  ← Correct!
```

Balance ensures middle elements hamesha accessible hain.

### Why Left Size ≥ Right?
Simplicity! Odd count mein direct left ka top return kar sakte ho.

***

## Interview Approach

### Step 1: Start with Brute Force
"Pehla approach hai har baar list ko sort karna. addNum() $$O(1)$$ hoga but findMedian() $$O(n \log n)$$ - agar frequently median chahiye toh bahut slow"[7]

### Step 2: Discuss Improvement
"Agar list ko sorted maintain karein toh findMedian() instant ho jayega, but insertion $$O(n)$$ ho jayega"[2]

### Step 3: Optimal Solution
"Actual insight ye hai ki median ke liye puri sorted list ki zarurat nahi - sirf middle elements chahiye. Two heaps use karke hum $$O(\log n)$$ insertion aur $$O(1)$$ median achieve kar sakte hain"[8][3]

### Step 4: Explain Why It Works
"Max heap left half ke max ko track karta hai, min heap right half ke min ko. Balance maintain karke hum ensure karte hain ki ye dono elements hamesha middle ke paas hain"[3][8]

---

## Common Mistakes

❌ Brute force se seedha two heaps pe jump - progression nahi dikhaya  
❌ Balance logic galat - right size > left size allow kar liya  
❌ Max heap banana bhool gaye (default comparator use kiya)  
❌ Integer division use kar liya median calculation mein

✅ Pehle simple approaches discuss karo  
✅ Har approach ka trade-off explain karo  
✅ Balance invariant clearly state karo  
✅ Double return type for median

***

## Final Takeaway

**Interview Strategy**:
1. Brute force se start karo (confidence + clear thinking)
2. Optimization steps discuss karo (problem-solving skills)
3. Optimal solution pe pahuncho (technical depth)

Tumhara two-heap solution **P50-level expected answer** hai - ye optimal complexity achieve karta hai aur scalable hai millions of numbers ke liye![8][3]

---

## Additional Resources

- **LeetCode Problem**: [295. Find Median from Data Stream](https://leetcode.com/problems/find-median-from-data-stream/)
- **Video Tutorial (Hindi)**: [codestorywithMIK Explanation](https://www.youtube.com/watch?v=jnj87BSi9Is)

Keep practicing! 🚀