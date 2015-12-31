title: KMP algorithm
date: 2015-12-07 03:04:32
description:
categories: Algorithm
tags:
- Code
- Dynamic Programming
---

KMP algorithm is a classic algorithm to search a pattern inside a text. I perosnally think this algorithm is diffiuclt to understand, it's simple but quite abstract.

# Naive Approach
At each character of text, compare with pattern letter by letter from its begining. When a mismatch is found, move the character position by one and start all over again.

Running time: `O(n * k)`
where n is the length of text, k is the length of pattern

{% codeblock lang:cpp %}
#include <stdio.h>
#include <string.h>

int contain(char *t, char *p){
	int i, j;
	int result = 0;

	// start comparsion at each character
	for (i = 0; i < strlen(t) - strlen(p) + 1; i++){
		result = 1;

		// compare each character at text and each character at pattern
		// all letters have to be the same
		for (j = 0; j < strlen(p); j++){
			result = result && (t[i + j] == p[j]);
		}

		if (result) 
			return result;
	}

	return result;
}
{% endcodeblock %}

----

# KMP Algorithm

## Searching principle
It looks a little bit magic. When a mismatch is found, the words itself tell you where you should restart you comparsion and keep the text position. Instead of movining the character position in text one by one, it can move much more quickly and without repeating to compare those character in texts which are proved to be matched. The letters before the mismtached character in the pattern could be possiblily matched with previous characters in the text.

When a mismatch is found at `[i + 1]` character, the letters from `[1...i]` are already matched. Suffix of `[1...i]` could be also a prefix of `[1...i]`. Suffix itself is matched, but its next character is not matched. When Suffix is equal to prefix, it can act likes a prefix, how about trying the next character after the prefix? You only need to roll back the comparsion at the character just behind the prefix. 

`[Prefix][t]...[Suffix][m]`
m is wrong, let's try t

----

Considering the following example, ababc is the pattern, ababababc is the text. And i is the text pointer, j is the pattern pointer. 

**Step 1:**
Start at the begining, when a match is found, keep moving both pointers.
<pre class="sample">
i
ababababc
j
ababc
</pre>
<pre class="sample">
    i
ababababc
    j
ababc
</pre>

**Step 2:**
Look at the above figure, a mistached is found at the 5-th character, a and c. `i` stays at that position, the letters befroe c is `abab`, its longest suffix and prefix is `ab`, so we need to rollback `j` to character just behind `ab`, which is `a`. You can now see that the eariler suffix: `ab` acts like prefix now
<pre class="sample">
    i
ababababc
    j
  ababc
</pre>

**Step 3:**
Keep moving until and mismatched is found.
<pre class="sample">
      i
ababababc
      j
  ababc
</pre>


**Step 4:**  
Look at the above figure, a and c is mismatched again. `i` stays at that position, the letters befroe c is `abab`, its longest suffix and prefix is `ab`, so we need to rollback `j` to character just behind `ab`, which is a. You can now see that the eariler suffix: `ab` acts like prefix now
<pre class="sample">
      i
ababababc
      j
    ababc
</pre>
	

**Step 5:**
Keep moving until `j` reach the end.
<pre class="sample">
        i
ababababc
        j
    ababc
</pre>

## Failure function
The remaining quesition is: how can we find the the position of longest prefix which is also a suffix (LPS) ? It is done by dynamic programming. By examing the previous LPS, the letter at i-th position can get its LPS position easily. 


Consider a pattern `[1...i-1][i]`, can be seen as `[suffix...prefix][a]` where a is the mismatched character. It could probabily look like:

**Case 1: No LPS of previous letters**
`[....no any suffix at all][a]` 
=> LPS of [a] = -1

**Case 2: the next character of the LPS of previous letters is equal to the mismatched letter**
`[suffix[a]..prefix][a]` : LPS of [1...i-1] + 1 is equal to a ?
=> LPS of [a] = LPS of [1..i-1] + 1

Example : `aabaab`
To calculate the position of LPS of the last b, F(5), it can be viewd as `[aabaa]b`
LPS of `[aabaa]` is `aa` with index 2, `aa` + 1 = `b` ? 
YES !! therefore LPS of the last `b` = 2 + 1 = 3

**Case 3: the next character of the LPS of the LPS... of the LPS of previous letters is equal to the mismatched letter**
`[su[a]fix[b]..prefix][a]` : ( LPS of LPS...of LPS of [1...i-1] ) + 1 is equal to a ?
=> LPS of [a] = ( LPS of LPS...of LPS of [1..i-1] ) + 1

Example: `adcaadcad`
To calculate the position of LPS of the last c, F(8), it can be viewd as `[adcaadca]d`
LPS of `[adcaadca]` is `adca` with index 3, `adca` + 1 = `d` ? 
NO !! find the LPS of the LPS recursively.
LPS of `adca` is `a` with index 0, is `a` + 1 = `d` ? 
YES !!! therefore F(8) = 0 + 1

## Running time analysis:
Best Case:
the text pointer always keep moving forward, and the pattern pointer always keep staying at the original position. n times will be executed

Worst Case: (in theory, althoguh it is not a possible case)
Condition 1: the text pointer can move forward for n time
Condition 2: the pattern pointer rolls back and keeps follow the text pointer, the pattern pointer can only walk at most at text pointer walks
If two conditions happen evenly, at most 2n times will be executed.

**Overall Running time: O(n + k)**
where n is the length of text
where k is the length of pattern

From wikipedia
{% quote %}
Here is another way to think about the runtime: Let us say we begin to match W and S at position i and p. If W exists as a substring of S at p, then W[0..m] = S[p..p+m]. Upon success, that is, the word and the text matched at the positions (W[i] = S[p+i]), we increase i by 1. Upon failure, that is, the word and the text does not match at the positions (W[i] ≠ S[p+i]), the text pointer is kept still, while the word pointer is rolled back a certain amount (i = T[i], where T is the jump table), and we attempt to match W[T[i]] with S[p+i]. The maximum number of roll-back of i is bounded by i, that is to say, for any failure, we can only roll back as much as we have progressed up to the failure. Then it is clear the runtime is 2n.
{% endquote %}

# Code Sample in C
The failure function starts with 0 index
-1 means no LPS

{% codeblock lang:cpp %}
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

// f is expected enough to have capacity as big as "p"
// like: int f[strlen(p)]
void failure(char *p, int *f){
	
	f[0] = -1;
	for (int i = 1; i < strlen(p); i++){
		int r = f[i - 1];
		// [1....i-1][a]
		// [suffix...prefix][a]
		// is it looks like [suffix[a]..prefix][a] ?
		if (p[i] == p[r + 1] ){
			f[i] = r + 1;
		} else if (r < 0){
			f[i] = -1;
		} else {
			// is it looks like [su[a]fix[b]..prefix][a] ? 
			// recursivly find the suffix of suffix
			int k = f[r]; // the is the last index of "suffix of the suffix"
			while ( k > -1 && (p[i] != p[k + 1]) ){
				k = f[k];
			}
			if (p[i] == p[k + 1]) k++;
				
			f[i] = k;
		}
	}
}

// LPS = longest prefix and suffix
// it uses two integer variable to "simulate a for loop" 
int contain(char *t, char *p){
	int i, j;
	i = 0;
	j = 0;

	// compute f(n), the latest index of the suffix
	int *f = (int*)malloc(strlen(p) * sizeof(int));
	failure(p, f);

	// scan though the text with i as index variable
	while (i < strlen(t)){
		// letter is matched, continue to the next one please
		if (t[i] == p[j]){
			i++;
			j++;

			// yes, you has done it!
			if (j == strlen(p)){
				return 1;
			}
		}
		// not mtached, how many I should move for i and j?
		else if (t[i] != p[j]){
			// no avaiable LPS, just move ahead
			if (j == 0){
				i++;
			} else {
				// start over at the last index of suffix before j-th letter + 1
				j = f[j - 1] + 1;
			}
		} 
	}

	return 0;
}
{% endcodeblock %}