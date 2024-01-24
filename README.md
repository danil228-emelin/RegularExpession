# Introduction 
1)Analyze different patterns examples written in Java.  <br>
2)Come up with rules to write proper regular expessions.<br>
3)Analyze development of Regular expressions in different JDK versions.<br>

# Source
## Example №1
```
System.out.println (Pattern.compile ("(x+x+)+y").matcher ("xxxxxxxxxxxxxxxxxxxy").matches ());
```
This example works slowly below Jdk 8.
The problem lies in the algorithm itself for working with the template, which is provided as a regular expression.
The backtracking algorithm is an algorithm that builds all possible solutions and selects the appropriate ones from them.
Its complexity depends on the number of characters in the string.O(2^{Number of characters}).<br>

### Outline the problem 
The first x+ will match all the characters of x. The second x+ does not work.
The algorithm abandons the previous state (before analyzing the last x), this x is run through the remaining regular, it mashes with the second x+. y matches y, and a general match is found.(Works fast)

The method takes a long time to execute if we remove the "y” from the line submitted for analysis. Everything works as in the first case, before the analysis of “y". When “y” fails, the regular expression engine rolls back. The group has one iteration to which it can return. The second x+ matched only one x, so no return is possible. But the first x+ can give up one x. The second x+ immediately corresponds to xx.
The group goes through one iteration again, fails the next, and y fails. Going back again, the second x+ now has one return position, reducing itself to match x. The group is trying the second iteration. The first x+ matches, but the second one gets stuck at the end of the line. Going back again, the first x+ in the first iteration of the group is reduced to 7 characters. The second x+ corresponds to xxx and so on. The difficulty will be O(2^n).

### Solution 
It is better to replace this regular with xx+y or x{2,}y
### Conclusion
If you put quantifiers on the same group, it will lead to catastrophic backtracking.

```
System.out.println (Pattern.compile ("(a+)+").matcher ( "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa" +
"aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa" + "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa" + 
```
it even doesn't work (JAVA<=8) 

### RULE 1
Don't use repetitive groups.

## Example №2
```
System.out.println (Pattern.compile ("(a|aa)+")
.matcher ("aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaab")
.matches ());
```
Works for ages.
### Outline the problem
By default, the expression OR (A|B) considers that the analysis was successful when a character or set of characters fully satisfies one of the options specified in parentheses. In our case
The first character in the string is taken and compared with the first option in the template,if first template isn't satisfied,we move futher  until the last template. When it comes to b, there are a number of problems.<br>
1) Engine compares with all templates, although it is enough to check for equality of only one option.<br>
2) When b cannot approach any of the options, the regular expression mechanism is rolled back, and it will be like in the first example.All possible solutions will be built.<br>
### Solution
It is better to replace wuth "(a|b)+"
### Conclusion
If a subexpression can match with the beginning of the another template, then this will lead to catastrophic backtracking.
### RULE 2
Try to avoid such situations


## Example № 3
```
System.out.println (java.util.regex.Pattern.compile	
("	(h|h|ih(((i|a|c|c|a|i|i|j|b|a|i|b|a|a|j))+h)ahbfhba|c|i)*	")
.matcher ( "hchcchicihcchciiicichhcichcihcchiihichiciiiihhcchi" +
"cchhcihchcihiihciichhccciccichcichiihcchcihhicchcciicchcccihiiihhihihihi" + "chicihhcciccchihhhcchichchciihiicihciihcccciciccicciiiiiiiiicihhhiiiihchccch" + "chhhhiiihchihcccchhhiiiiiiiicicichicihcciciihichhhhchihciiihhiccccccciciihh" + "ichiccchhicchicihihccichicciihcichccihhiciccccccccichhhhihihhcchchihih" + "iihhihihihicichihiiiihhhhihhhchhichiicihhiiiiihchccccchichci").matches ());
}
```

### RULE 3
Don't write too big patterns.

### Example № 4

```
System.out.println (Pattern.compile ("\\d*?").matcher ("").matches ());

```
### Outline the problem

You have to be careful with greedy quantifiers. 
If you do not specify anything after them, then an empty string will be considered as appropriate

### Solution
if you need empty string use "^$"

### Rule 4
Don't use  greedy quantifiers with empty strings.
