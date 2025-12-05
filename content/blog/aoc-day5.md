---
title: "AOC Day 5 [Part 2] - Greedy Algorithms"
date: 2025-12-05T21:49:18+11:00
draft: false
---

[Advent of Code](https://adventofcode.com) is a famous website that releases one programming puzzle each day, starting from the 1st and usually ending on the 25th (like an advent calendar) of December. This year, only 12 puzzles will be released in the first 12 days. While this is sad news for many Advent of Code enjoyers like me, I think this gives me an opportunity to finish every day's puzzle for the first time. Anyway, I want to write about how greedy algorithm can be used to solve part 2 of today's problem (Day 5 [Part 2]).

Part 2 of today's puzzle is basically given a list of ranges,
```
3-5
10-14
16-20
12-18
```
count how many distinct numbers there are in all these ranges. In the above example, there are 14 distinct numbers: `3, 4, 5, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20`.

Here's my solution to the problem. First read in and store all the ranges in a vector. Then sort the ranges in ascending order of starting points, then ascending order of ending point. Then apply a greedy algorithm where we take the leftmost range, if this range intersects with another range (`this.end >= another.start`), increase the endpoint of this range if it's greater (`this.end = max(this.end, another.end)`). Repeat until there are no more ranges to the right that intersects with our range. At the end, we will have a new range that merged as many ranges as possible. We know that this new range does not intersect with any other range in our collection and so we can count the number of distinct numbers by calculating `range.end - range.start + 1`.

Below is the C++ code used to solve this problem.
```c++
using ll = long long;

// Range struct
typedef struct {
    ll start;
    ll end;
} Range;

// Comparison function for std::sort()
bool comp_range(const Range &a, const Range &b) {
    if (a.start != b.start) {
        return a.start < b.start;
    }
    return a.end < b.end;
}

void part_two() {
    std::ifstream f("./puzzle_input/day5.txt");
    std::vector<Range> ranges;

    // Read in ranges
    for (;;) {
        std::string range;
        getline(f, range);
        if (range.empty()) break;
        Range r;
        int i = range.find('-');
        r.start = stoll(range.substr(0, i));
        r.end = stoll(range.substr(i + 1));
        ranges.push_back(r);
    }

    // Sort ranges
    std::sort(ranges.begin(), ranges.end(), &comp_range);

    // Greedy algorithm to merge ranges
    ll count = 0;
    size_t i = 0;
    while (i < ranges.size()) {
        Range r = ranges[i];
        while (i + 1 < ranges.size() && ranges[i + 1].start <= r.end) {
            r.end = std::max(ranges[i + 1].end, r.end);
            i++;
        }
        count += r.end - r.start + 1;
        i++;
    }
    std::cout << count << '\n';
}

```

---
## Things I've learnt

I would also like to use this blog to document knowledge about C++ that I've learnt while doing AOC.

### Reading from a file in C++
Include the `<fstream>` header and initialize an `ifstream` with the path to the file that you want to open. Then you can read the file using the `>>` operator or `getline()`. You can read the file line by line using a for loop!
```c++
#include <fstream>

int main() {
    std::ifstream f("./path/to/file");
    for (std::string line; getline(f, line);) {
        // do something with line
    }
    return 0;
}
```

### Modulo in C++
The `%` operator in many programming languages is not actually the modulo operation, it's a return the remainder operation. It doesn't work correctly when you do `-a % b` where `-a` is a negative number. For example `-4 mod 3 = -2` but `-4 % 3 = -1`. To get actual modulo operation, you have to do this
```c++
(a % b + b) % b;
```
