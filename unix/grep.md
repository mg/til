# grep & find

 - ``grep "#" **/*.md``: Search for all headers in all *markdown* files in current directory and all its subdirectories.

 - ``find . -name "*.md"``: Find all *markdown* files in current directory and all its subdirectories.

 - ``find . -name "*.md" | xargs``: Run ``xargs echo`` on every file (``echo`` is default).

 - ``grep -r --include="STRING1" "STRING1" .``: Search files matching **STRING1** for occurrences of **STRING2**

 - ``git grep "STRING"``: Searches for occurrences in files tracked by git.

 - ``grep -n``: Show line number where match is found

 - ``grep -A N``: Show match and next **N** lines for context

 - ``grep -B N``: Show match and previous **N** lines for context

 - ``grep -C N``: Show match and previous AND next **N** lines for context

 - ``grep -v``: Inverse mode. Show what **doesn't** match.

 ### grep pattern matching characters

 - ``.``: Matches any **one** character.

 - ``*``: Match zero or more of the previous character in the pattern.

 - ``\?``: Match the previous character in the pattern zero or one time.

 - ``\+``: Match the previous character in the pattern one or more times.

 - ``\|``: Join to expressions together to search for **either** of them.

 - ``^``: Anchor the expression to the beginning of the line.

 - ``$``: Anchor the expression to the end of the line.

 - ``[]``: Create a character class to search for. Use ``-`` to create a range.

 - ``()``: Create a group of matches.

 - Use ``-E`` to enable **extended** regular expressions and skip the ``\``.
