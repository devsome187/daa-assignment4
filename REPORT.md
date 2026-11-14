### DAA Assignment4 - Report and Defense (50 points)

---

##### Problem 1 - Counting Bits **(5 points)**.

For every `i` from `0` to `n` we need the number of `1` bits. Let `dp[i]` be that number. Shifting `i` right by one bit drops its last bit, so `i` has the same bits as `i >> 1` plus its lowest bit `i & 1`:

`dp[0] = 0`, `dp[i] = dp[i >> 1] + (i & 1)`.

*Why it is correct:* `i >> 1 < i` for `i ≥ 1`, so `dp[i >> 1]` is already computed when we reach `i` (the subproblem is smaller). The binary representation of `i` is the representation of `i >> 1` followed by one more bit, so the count is the count of `i >> 1` plus that bit.

- Time complexity: O(n), one step per number (no `O(log n)` popcount per number).
- Space complexity: O(1) besides the output array of size n + 1.

---

##### Problem 2 - Jump Game **(5 points)**.

(The linked problem is Jump Game II: find the minimum number of jumps to reach the last index; it is guaranteed to be reachable.)

Let `dp[i]` be the minimum number of jumps needed to reach index `i`. `dp[0] = 0`, all others start at infinity. I go from left to right; from every reachable index `i` I can jump to any `j` in `(i, i + nums[i]]`, so I relax `dp[j] = min(dp[j], dp[i] + 1)`. The answer is `dp[n − 1]`.

*Why it is correct:* jumps only go forward, so any path to `j` ends with a jump from some `i < j`. When I process `i`, every index before it has already been relaxed from all its predecessors, so `dp[i]` is final (optimal). Then `dp[j] = min over i < j with i + nums[i] ≥ j of dp[i] + 1`, which is exactly the optimal-substructure recurrence.

- Time complexity: O(n · k), where k = max(nums[i]) ≤ 1000; O(n²) in the worst case.
- Space complexity: O(n).

*Remark:* the same DP can be compressed into a greedy BFS by levels (keep the farthest index reachable with the current number of jumps), which gives O(n) time and O(1) space. The DP version is used here because the assignment asks for dynamic programming, and it shows the recurrence explicitly.

---

##### Problem 3 - Min Cost Climbing Stairs **(5 points)**.

Let `dp[i]` be the minimum cost to stand on step `i` (the top is step `n`). We may start on step 0 or 1 for free, so `dp[0] = dp[1] = 0`. We reach step `i` either from `i − 1` (paying `cost[i − 1]`) or from `i − 2` (paying `cost[i − 2]`):

`dp[i] = min(dp[i − 1] + cost[i − 1], dp[i − 2] + cost[i − 2])`, answer `dp[n]`.

*Why it is correct:* the last move into step `i` is a 1-step or a 2-step, and these two cases cover every way to arrive. Whatever happened before that move, it should be the cheapest way to reach the previous step (optimal substructure), so taking the minimum of the two cases gives the optimum.

- Time complexity: O(n).
- Space complexity: O(n) for the table (can be reduced to O(1) with two variables, since only `dp[i − 1]` and `dp[i − 2]` are used).

---

##### Problem 4 - Longest Increasing Subsequence **(5 points)**.

Let `dp[i]` be the length of the longest strictly increasing subsequence that **ends at** index `i`. Every element alone is a subsequence of length 1. An increasing subsequence ending at `i` with more than one element has a previous element `j < i` with `nums[j] < nums[i]`:

`dp[i] = 1 + max(dp[j])` over `j < i` with `nums[j] < nums[i]` (or `1` if there is no such `j`). The answer is `max(dp[i])`, because the LIS can end anywhere.

*Why it is correct:* removing the last element of an optimal subsequence ending at `i` leaves an increasing subsequence ending at some `j`, and it must be the longest one ending at `j`, otherwise we could swap in a longer one. The recurrence tries every possible `j`.

- Time complexity: O(n²), fine for n ≤ 2500.
- Space complexity: O(n).

*Remark:* an O(n log n) version keeps `tails[k]`, the smallest possible tail of an increasing subsequence of length `k + 1`, and updates it with binary search.

---

##### Problem 5 - Longest Common Subsequence **(5 points)**.

Let `dp[i][j]` be the LCS length of the prefixes `text1[0..i)` and `text2[0..j)`. Empty prefixes give 0 (row and column 0).

- If `text1[i − 1] == text2[j − 1]`: `dp[i][j] = dp[i − 1][j − 1] + 1`, the matching characters extend the LCS.
- Otherwise: `dp[i][j] = max(dp[i − 1][j], dp[i][j − 1])`, at least one of the two last characters is not in the LCS, so we drop it.

The answer is `dp[m][n]`.

*Why it is correct:* if the last characters are equal, some LCS uses both of them (exchange argument: any LCS that does not can be changed to end with this pair without becoming shorter). If they differ, they cannot both be the last matched pair, so the LCS lies in one of the two smaller subproblems.

- Time complexity: O(m · n).
- Space complexity: O(m · n) (can be reduced to O(min(m, n)) by keeping two rows).

---

##### Problem 6 - Edit Distance **(5 points)**.

Let `dp[i][j]` be the minimum number of operations to convert `word1[0..i)` into `word2[0..j)`.

Base cases: `dp[i][0] = i` (delete all `i` characters), `dp[0][j] = j` (insert all `j` characters).

- If `word1[i − 1] == word2[j − 1]`: `dp[i][j] = dp[i − 1][j − 1]`, no operation is needed for the last character.
- Otherwise: `dp[i][j] = 1 + min(dp[i − 1][j − 1]` (replace), `dp[i − 1][j]` (delete from word1), `dp[i][j − 1]` (insert into word1)`)`.

The answer is `dp[m][n]`.

*Why it is correct:* look at the last character of both prefixes in an optimal edit script. Either it is kept (only possible when the characters are equal), replaced, deleted, or a new character is inserted at the end. These cases cover every possibility, and each leaves a smaller subproblem that must itself be solved optimally.

- Time complexity: O(m · n).
- Space complexity: O(m · n) (two rows are enough for O(n)).

---

##### Problem 7 - Longest Palindromic Subsequence **(5 points)**.

Let `dp[i][j]` be the length of the longest palindromic subsequence of `s[i..j]`.

- `dp[i][i] = 1` (one character is a palindrome).
- If `s[i] == s[j]`: `dp[i][j] = dp[i + 1][j − 1] + 2`, both ends wrap the inner palindrome (for `j = i + 1` the inner part is empty and the table value is 0).
- Otherwise: `dp[i][j] = max(dp[i + 1][j], dp[i][j − 1])`, one of the ends is not used.

The table is filled with `i` going from `n − 1` down to `0` and `j` from `i + 1` up, so `dp[i + 1][*]` and `dp[i][j − 1]` are always ready. The answer is `dp[0][n − 1]`.

*Why it is correct:* if the ends are equal, some optimal palindrome uses both of them as its outer pair (exchange argument). If they differ, they cannot be the outer pair together, so at least one of them is dropped. This is the same idea as LCS; in fact LPS(s) = LCS(s, reverse(s)).

- Time complexity: O(n²).
- Space complexity: O(n²).

---

##### Problem 8 - Palindromic Substrings **(5 points)**.

Let `dp[i][j]` be `true` if the substring `s[i..j]` is a palindrome. A substring is a palindrome when its ends are equal and the inside is a palindrome:

`dp[i][j] = (s[i] == s[j]) && (j − i < 2 || dp[i + 1][j − 1])`.

The case `j − i < 2` covers lengths 1 and 2, where the inside is empty. I fill the table with `i` from `n − 1` down to `0` (so `dp[i + 1][j − 1]` is already known) and count every `true` cell.

*Why it is correct:* each substring `(i, j)` is checked exactly once, and the recurrence is the definition of a palindrome (read from both ends). So the counter equals the number of palindromic substrings, counting equal substrings at different positions separately, as the problem requires.

- Time complexity: O(n²).
- Space complexity: O(n²) (the "expand around center" method gives O(1) space with the same time).

---

##### Problem 9 - Maximum Subarray **(5 points)**.

This is Kadane's algorithm written as DP. Let `cur` (`dp[i]`) be the maximum sum of a subarray that **ends at** index `i`. Such a subarray either is `nums[i]` alone or extends the best subarray ending at `i − 1`:

`dp[i] = max(nums[i], dp[i − 1] + nums[i])`, answer `max(dp[i])`.

Only `dp[i − 1]` is needed, so one variable `cur` replaces the table, and `best` stores the maximum seen so far.

*Why it is correct:* if `dp[i − 1]` is negative, adding it only decreases the sum, so it is better to start a new subarray at `i`; otherwise extending is better. Every subarray ends at some index, so the maximum over all `dp[i]` is the answer. Starting from `nums[0]` (not 0) handles arrays where all numbers are negative.

- Time complexity: O(n).
- Space complexity: O(1).

---

##### Problem 10 - House Robber **(5 points)**.

Let `dp[i]` be the maximum money we can rob from the first `i` houses. For house `i` there are two choices: skip it (`dp[i − 1]`), or rob it, and then house `i − 1` must be skipped (`dp[i − 2] + nums[i]`):

`dp[i] = max(dp[i − 1], dp[i − 2] + nums[i])`, with `dp` of an empty prefix = 0.

Only the two previous values are needed, so I keep them in `prev1` (`dp[i − 1]`) and `prev2` (`dp[i − 2]`).

*Why it is correct:* in an optimal plan the last house is either robbed or not. If it is robbed, its neighbour is not, and the rest must be an optimal plan for the first `i − 2` houses; if not, the rest is an optimal plan for the first `i − 1`. The maximum of the two cases is optimal.

- Time complexity: O(n).
- Space complexity: O(1).
