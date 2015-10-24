title: 'Exception: Prelude.read: no parse'
date: 2013-08-29 08:59:42
description: I got Exception, Prelude.read no parse when I tested this function with floating number and integer number
categories: 經驗分享
tags: Haskell
---

{% codeblock %}
    solveRPN' :: (Num a, Read a) => String -> a  
    solveRPN' = head . foldl foldingFunction [] . words  
        where   foldingFunction (x:y:ys) "*" = (x * y):ys  
                foldingFunction (x:y:ys) "+" = (x + y):ys  
                foldingFunction (x:y:ys) "-" = (y - x):ys  
                foldingFunction xs numberString = read numberString:xs 

    solveRPN' "10 4.0 3 + 2 * -"
    *** Exception: Prelude.read: no parse
	
{% endcodeblock %}

By default, the ghc will deduce this function as `(Num Int, Read Int)` . The error is casued by the Prelude.read function which cannot parse a "Floating number string" into Int. However, we can explicity specific the type of expression is Float. Integer string can be parsed into Floating number but Floating number cannot be parsed into Int.

{% codeblock %}
    read "4.0" :: Int
    *** Exception: Prelude.read: no parse

    read "4" :: Float
    4.0
    
    solveRPN' "10.0 4 3 + 2 * -" :: Float
    -4.0
{% endcodeblock %}