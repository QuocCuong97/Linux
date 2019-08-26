# So sánh số học trong Bash
- So sánh biến số :
    - `-eq` ( `==` ) : bằng ( *equal* )
    - `-ne` ( `!=` ) : khác ( *not equal* )
    - `-lt` : nhỏ hơn ( *less than* )
    - `-le` : nhỏ hơn hoặc bằng ( *less or equal* )
    - `-ge` : lớn hơn hoặc bằng ( *greater of equal* )
    - `-gt` : lớn hơn ( *greater than* )
- Toán tử logic :
    - `! expression` : NOT
    - `expression1 , -a , expression2` : AND
    - `expression1 && expression2` : AND
    - `expression1 , -0 , expression2` : OR
    - `expression1 || expression2` : OR
- **VD :**
    ```bash
    ((n1 == n2))    ## n1 is equals to n2
    ((n1 != n2))    ## n1 is not equals to n2
    ((n1 > n2))     ## n1 is greater than n2
    ((n1 >= n2))    ## n1 is greater or equals to n2
    ((n1 < n2))     ## n1 is smaller than n2
    ((n1 <= n2))    ## n1 is smaller than or equals to n2
    ```