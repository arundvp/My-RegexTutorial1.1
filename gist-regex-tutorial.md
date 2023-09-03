# Regex Tutorial: Validating Credit Card Numbers

Credit card numbers, often referred to as PAN (Primary Account Number), have a structure defined by the ISO/IEC 7812 standard. Validating them using regex can be useful for format checks. However, for full validation, you'll also need algorithms like the Luhn algorithm.

This tutorial will guide you through crafting a regex for basic credit card format validation.

## Summary

Regex provides robust tools for validating, extracting, and formatting credit card numbers, ensuring that they fit the expected patterns and aiding in tasks like form validation.

Here's our regex pattern for credit card validation:
```
^(?:4[0-9]{12}(?:[0-9]{3})?|5[1-5][0-9]{14}|3[47][0-9]{13}|3(?:0[0-5]|[68][0-9])[0-9]{11}|6(?:011|5[0-9]{2})[0-9]{12}|(?:2131|1800|35\d{3})\d{11})$
```
## Table of Contents

- [Anchors](#anchors)
- [Quantifiers](#quantifiers)
- [OR Operator](#or-operator)
- [Character Classes](#character-classes)
- [Flags](#flags)
- [Grouping and Capturing](#grouping-and-capturing)
- [Bracket Expressions](#bracket-expressions)
- [Greedy and Lazy Match](#greedy-and-lazy-match)
- [Boundaries](#boundaries)
- [Back-references](#back-references)

## Regex Components

### Anchors

Anchors can be utilized to ensure that the pattern matches from the beginning (^) to the end ($) of the string, preventing any unwanted characters before or after the expected pattern.

***Visa Card***: Typically starts with 4 and can be 13 or 16 digits long.

`
^4[0-9]{12}(?:[0-9]{3})?$
`

***MasterCard***: Starts with numbers 51 through 55 or 2221 through 2720, and is always 16 digits long.

`
^(5[1-5][0-9]{14}|2[2-7][0-9]{14})$
`
    
***American Express***: Starts with 34 or 37, and is always 15 digits long.

`
^3[47][0-9]{13}$
`

Notice how the ^ and $ anchors ensure the pattern matches the entire string. Without these anchors, the pattern might match a substring within a larger number or text, potentially allowing invalid card numbers.

### Quantifiers

Quantifiers in regular expressions dictate how many instances of the previous element (which could be a character, a group, or a character class) are allowed for a match. 

Here are the commonly used quantifiers:

- `*`: 0 or more times
- `+`: 1 or more times
- `?`: 0 or 1 time (essentially makes the previous element optional)
- `{n}`: Exactly n times
- `{n,}`: n or more times
- `{n,m}`: Between n and m times, inclusive

Let's look at credit card numbers as an example:

1. **Visa**: Starts with `4` and can be 13 or 16 digits long. 
   
   Regex:
   ```regex
   ^4[0-9]{12}(?:[0-9]{3})?$
   ```
   In this pattern:
   - `[0-9]{12}`: `{12}` is a quantifier that matches exactly 12 digits.
   - `(?:[0-9]{3})?`: `{3}` matches exactly 3 digits, and `?` makes the entire group optional, meaning it can appear 0 or 1 time.

2. **MasterCard**: Starts with numbers `51` through `55` or `2221` through `2720`, and is always 16 digits long.
   
   Regex:
   ```regex
   ^(5[1-5][0-9]{14}|2[2-7][0-9]{14})$
   ```
   Here:
   - `[0-9]{14}`: `{14}` ensures we match exactly 14 digits.

3. **American Express (AMEX)**: Starts with `34` or `37`, and is always 15 digits long.
   
   Regex:
   ```regex
   ^3[47][0-9]{13}$
   ```
   In this example:
   - `[0-9]{13}`: `{13}` matches exactly 13 digits.

4. **Cards that start with `3`**: These could be AMEX or other cards like Diners Club. If we were unsure about the total length but knew it was between 13 and 19 digits (a common range for credit cards), we could use:
   
   Regex:
   ```regex
   ^3[0-9]{12,18}$
   ```
   Here:
   - `{12,18}`: This quantifier matches between 12 and 18 digits.

5. **Generic credit card number**: A generic regex to match credit card numbers between 13 to 19 digits without checking for specific starting digits could be:
   
   Regex:
   ```regex
   ^[0-9]{13,19}$
   ```
   - `{13,19}`: Matches a sequence of digits that's at least 13 and at most 19 characters long.

Always remember that while regex can help validate the format of a credit card, it doesn't verify the authenticity, expiry, or available funds on the card. Real-world validation requires integration with payment gateways or processors. And always handle credit card information with utmost care, ensuring compliance with standards like PCI-DSS.

### OR Operator

The `OR` operator in regular expressions is represented by the pipe symbol `|`. It allows you to match one of multiple patterns, making it useful for situations where you have several valid formats or options.

Let's see how this works with credit card numbers:

1. **Visa OR MasterCard**:
   - Visa starts with `4` and can be 13 or 16 digits long.
   - MasterCard starts with numbers `51` through `55` or `2221` through `2720` and is always 16 digits long.
   
   Regex:
   ```regex
   ^(4[0-9]{12}(?:[0-9]{3})?|5[1-5][0-9]{14}|2[2-7][0-9]{14})$
   ```

2. **AMEX OR Diners Club**:
   - AMEX starts with `34` or `37` and is 15 digits long.
   - Diners Club starts with `36` and is 14 digits long.
   
   Regex:
   ```regex
   ^(3[47][0-9]{13}|36[0-9]{12})$
   ```

3. **Visa OR MasterCard OR Discover**:
   - Visa starts with `4` and can be 13 or 16 digits long.
   - MasterCard starts with numbers `51` through `55` or `2221` through `2720` and is always 16 digits long.
   - Discover starts with `6011`, `644` through `649`, or `65` and is 16 digits long.
   
   Regex:
   ```regex
   ^(4[0-9]{12}(?:[0-9]{3})?|5[1-5][0-9]{14}|2[2-7][0-9]{14}|6011[0-9]{12}|64[4-9][0-9]{13}|65[0-9]{14})$
   ```

4. **AMEX OR JCB**:
   - AMEX starts with `34` or `37` and is 15 digits long.
   - JCB starts with `2131` or `1800` and is 15 digits long, or it can start with `35` and be 16 to 19 digits long.
   
   Regex:
   ```regex
   ^(3[47][0-9]{13}|(2131|1800)[0-9]{11}|35[0-9]{13,16})$
   ```

5. **Visa OR MasterCard OR AMEX**:
   Merging the patterns for Visa, MasterCard, and AMEX:
   
   Regex:
   ```regex
   ^(4[0-9]{12}(?:[0-9]{3})?|5[1-5][0-9]{14}|2[2-7][0-9]{14}|3[47][0-9]{13})$
   ```

The `OR` operator provides flexibility when you want to match multiple possible patterns within a single regular expression. Just be sure to encapsulate each pattern within parentheses to ensure the `OR` operator is being applied correctly, especially when combining multiple patterns with different quantifiers and characters.

### Character Classes

Character classes are used to match specific sets of characters, and they can be quite handy when dealing with patterns like credit card numbers.

1. **Matching Any Credit Card Number Starting with 4, 5, or 6**:
   Credit cards often start with certain numbers based on the issuer (e.g., Visa starts with 4, MasterCard with 5, Discover with 6). If you want to match any credit card starting with 4, 5, or 6 and having 16 digits, you could use:
   
   Regex:
   ```regex
   ^[456][0-9]{15}$
   ```

   In this pattern, `[456]` is the character class, which matches any single digit, 4, 5, or 6.

2. **Matching Only Visa or MasterCard Numbers**:
   
   Regex:
   ```regex
   ^[45][0-9]{15}$
   ```
   
   Here, `[45]` matches cards that start with either 4 (Visa) or 5 (MasterCard).

3. **Matching Credit Card Numbers Without 0s or 1s**:
   Suppose you have a scenario where for some reason, you want to match cards that don't have the numbers 0 or 1 anywhere in them (very hypothetical!):

   Regex:
   ```regex
   ^[2-9]{16}$
   ```

   The character class `[2-9]` ensures that each digit is between 2 and 9, inclusive.

4. **Matching Visa, MasterCard, or AMEX**:
   AMEX numbers start with 34 or 37 and are 15 digits long. To create a pattern that matches Visa, MasterCard, or AMEX:

   Regex:
   ```regex
   ^([45][0-9]{15}|3[47][0-9]{13})$
   ```

   Here, `3[47]` is the character class for the AMEX, ensuring it starts with either 34 or 37.

5. **Matching Credit Cards but Not Those Starting with 9**:
   If, for some peculiar reason, you want to exclude credit card numbers that start with 9:

   Regex:
   ```regex
   ^[0-8][0-9]{15}$
   ```

   The character class `[0-8]` ensures that the first digit is any number from 0 to 8.

Remember, these regex patterns just illustrate how character classes work. They will match the given patterns, but in real-world scenarios, there are other factors to consider (like the actual range of valid credit card numbers, checksum validations, etc.). Always be sure your validations are accurate for your use case, especially with sensitive data like credit card numbers.

### Flags

In regular expressions, flags are used to modify the behavior of the pattern matching. The availability of specific flags might differ depending on the programming language or platform, but some common flags include:

1. **Global (g)**: Causes the regex to match all instances in the input, rather than stopping at the first match.

2. **Case-insensitive (i)**: Makes the regex match case-insensitive.

3. **Multiline (m)**: Causes `^` and `$` to match the start and end of lines, respectively, not just the start and end of the entire input.

4. **Dotall (s)**: Makes the `.` (dot) match newline characters as well. In some regex flavors, this flag might be represented differently.

5. **Extended (x)**: Allows for spaces and comments within the regex for clarity. Literal spaces then need to be escaped or included in a character class.

Here's how you could theoretically apply flags to regex patterns involving credit card numbers:

1. **Case-insensitive Search for Card Type**:
   Suppose we want to search for mentions of card types in a text, where the word "VISA" might be in any case combination.
   
   Regex:
   ```regex
   /visa/i
   ```

   With the `i` flag, this regex will match "VISA", "Visa", "visa", etc.

2. **Find All Mentions of a Specific Card**:
   If you're searching a text for all mentions of a specific card number, you might use the `g` flag.
   
   Regex:
   ```regex
   /4567[\s-]?1234[\s-]?5678[\s-]?9012/g
   ```

   This would find all instances of the number "4567-1234-5678-9012" with or without dashes or spaces between sets of digits.

3. **Match Card Numbers at the Start of Lines**:
   If you have a multiline text and you want to find lines that start with a credit card number:
   
   Regex:
   ```regex
   /^(4[0-9]{15}|5[1-5][0-9]{14})/m
   ```

   The `m` flag ensures that the `^` matches the start of each line, not just the start of the entire input.

4. **Using Dotall for Multi-line Records**:
   Let's say you have records that sometimes span multiple lines, and you want to capture the entire record, including new lines:

   Example record:
   ```
   BEGIN
   Card: 4111-1111-1111-1111
   END
   ```

   Regex:
   ```regex
   /BEGIN.*Card:.*END/s
   ```

   The `s` flag ensures that the `.` matches newline characters, so the entire multi-line record is matched.

5. **Commenting a Complex Regex**:
   For complicated regex patterns, using the `x` flag can help improve readability:

   Regex:
   ```regex
   /
   ^           # Start of the line
   (4[0-9]{15})  # Visa, 16 digits starting with 4
   |          # OR
   (5[1-5][0-9]{14})  # MasterCard, 16 digits starting with 51 through 55
   /x
   ```

   This regex, thanks to the `x` flag, is clearer because of the spaces and comments. However, this exact syntax may vary depending on the regex engine and language in use.

When using flags, be aware of the specifics and capabilities of the regex engine you're working with, as some nuances may exist between different platforms.

### Grouping and Capturing

Grouping and capturing are fundamental concepts in regular expressions. They allow you to treat multiple characters as a single unit (grouping), and they let you retrieve the part of the text matched by the group (capturing).

**Grouping** is done using parentheses `()`. Anything inside the parentheses is treated as a single unit by the regex engine.

**Capturing** refers to how most regex engines will "remember" the content matched by the groups, allowing you to extract or refer back to it later.

Here are few examples involving credit card numbers:

1. **Extracting the Last Four Digits**:
   Suppose you want to extract the last four digits of a credit card number:

   Regex:
   ```regex
   ^\d{12}(\d{4})$
   ```

   If you match this against the string `4111111111111234`, the captured group would be `1234`.

2. **Extracting Type and Last Four Digits**:
   Suppose you want to identify the card type based on the first digit and also capture the last four digits:

   Regex:
   ```regex
   ^(4|5)\d{11}(\d{4})$
   ```

   For `4111111111111234`, the first captured group is `4` (indicating Visa), and the second captured group is `1234`.

3. **Matching and Capturing Separated Segments**:
   Some card numbers are written in a segmented format, like `4111-1111-1111-1234`. If you want to match this format and capture each segment:

   Regex:
   ```regex
   ^(\d{4})-(\d{4})-(\d{4})-(\d{4})$
   ```

   This would capture `4111`, `1111`, `1111`, and `1234` as separate groups.

4. **Using Backreferences to Find Repeated Segments**:
   A backreference allows you to refer back to content captured by a group and match the same content again. Suppose you want to find credit card numbers where two consecutive segments are the same:

   Regex:
   ```regex
   ^\d{4}-(\d{4})-\1-\d{4}$
   ```

   Here, the `\1` refers back to the first captured group. It will match strings like `4111-1111-1111-1234`.

5. **Non-capturing Groups**:
   Sometimes, you want to group content without capturing it. This is done using `?:` at the start of the group. For example, to match Visa and MasterCard numbers but only capture the last four digits:

   Regex:
   ```regex
   ^(?:4|5)\d{11}(\d{4})$
   ```

   The `(?:4|5)` part is a non-capturing group. It groups the `4` and `5` alternatives without capturing them.

Grouping and capturing are essential tools when using regular expressions for more advanced pattern matching and extraction tasks. They add flexibility and power to your regex patterns.

### Bracket Expressions

Bracket expressions, often referred to as character classes in regular expressions, allow you to specify a set of characters, any single one of which can match the next character in the input. They are denoted using square brackets (`[]`).

Here are few examples using credit card numbers:

1. **Matching Cards Starting with 4, 5, or 6**:
   Different credit card providers have numbers starting with different digits. For example, Visa cards usually start with a 4, MasterCard with a 5, and Discover with a 6.

   Regex:
   ```regex
   ^[456]\d{15}$
   ```

   The bracket expression `[456]` matches any single character that is either `4`, `5`, or `6`.

2. **Matching Cards that Don't Start with 9**:
   Suppose, hypothetically, you don't want to match cards starting with 9.

   Regex:
   ```regex
   ^[^9]\d{15}$
   ```

   Here, the `[^9]` is a bracket expression that matches any character that isn't `9`.

3. **Matching Cards with Specific 2nd and 3rd Digits**:
   Let's say you have a hypothetical scenario where you only want to match cards where the 2nd digit is 1, 2, or 3 and the 3rd digit is 7, 8, or 9.

   Regex:
   ```regex
   ^\d[123][789]\d{13}$
   ```

   The bracket expressions `[123]` and `[789]` ensure that only the specified digits are matched in the 2nd and 3rd positions, respectively.

4. **Matching Cards with Allowed Separators**:
   Some credit card numbers are written with separators, either spaces or dashes. You can use a bracket expression to match either.

   Regex:
   ```regex
   ^\d{4}[-\s]\d{4}[-\s]\d{4}[-\s]\d{4}$
   ```

   Here, the `[-\s]` bracket expression matches either a dash `-` or a space.

5. **Matching Only Even Starting Digits**:
   Suppose you only want to match cards that start with an even digit.

   Regex:
   ```regex
   ^[02468]\d{15}$
   ```

   The bracket expression `[02468]` matches any of the even digits.

In essence, bracket expressions provide a way to match a character from a specific set, enhancing the flexibility of your regex patterns. It's important to note that, within a bracket expression, most special regex symbols lose their special meaning and represent themselves.

### Greedy and Lazy Match

In the realm of regular expressions, "greedy" and "lazy" (also known as "non-greedy" or "reluctant") describe two modes of quantifier matching behavior. Quantifiers, like `*`, `+`, `?`, and `{n,m}`, determine how many times the preceding element is allowed to occur.

1. **Greedy Matching**: 
   By default, quantifiers are greedy. This means they will try to match as much of the input as possible while still allowing the overall pattern to match.

2. **Lazy Matching**: 
   Lazy matching can be triggered by following a quantifier with a `?`. In this mode, the quantifier will try to match as little of the input as possible.

Let's illustrate the concept with a credit card number example:

### Greedy Match:
Imagine you want to extract anything that looks like a credit card number from a string but surrounded by angle brackets. Let's use the following pattern:

```regex
<(\d+)>  # this pattern is greedy
```

Given the string:
```
<4111111111111111><5555555555554444>
```

Using the pattern above will capture `4111111111111111><5555555555554444` (notice that it captured the maximum possible match by including the middle `><`).

### Lazy Match:
Now, if we want our pattern to be lazy to correctly capture each credit card number, we'd adjust the pattern like this:

```regex
<(\d+?)>  # note the '?' making the '+' lazy
```

Given the same string:
```
<4111111111111111><5555555555554444>
```

Using the lazy pattern will correctly capture `4111111111111111` and `5555555555554444` separately.

More examples:

1. **Greedy `.*` vs Lazy `.*?`**:
   Consider extracting content inside curly braces:

   String: `{4111-1111-1111-1234} and {5555-5555-5555-5678}`

   - Greedy: `\{.*\}` captures `{4111-1111-1111-1234} and {5555-5555-5555-5678}`
   - Lazy: `\{.*?\}` captures `{4111-1111-1111-1234}` and `{5555-5555-5555-5678}` separately.

2. **Greedy with Multiple Digits**:
   Consider extracting numbers in a string like: `4111xxxx1234`

   - Greedy: `\d+` captures `4111`
   - Lazy: `\d+?` captures just `4` (the shortest possible match).

Remember, greedy patterns will try to "consume" as much of the string as possible, while lazy patterns will consume as little as possible. The decision to use one over the other often depends on the specific task and the nature of the input data.

### Boundaries

In regular expressions, boundaries are metacharacters that match positions rather than actual characters in the string. They are crucial when you need to distinguish where a match should specifically start or end relative to surrounding characters. The most common boundary metacharacters are `^` (start of a line or string), `$` (end of a line or string), and `\b` (word boundary).

Let's dive into the concept using credit card numbers:

1. **Start and End of a String (`^` and `$`)**:
   Suppose you want to validate a credit card number input, ensuring the entire input is a credit card number without any extra characters.

   Regex:
   ```regex
   ^\d{16}$
   ```

   This pattern ensures that the entire string (from start to end) is a 16-digit number.

   For the string `4111111111111111`, the pattern matches. But for the string `4111111111111111extra`, it won't match because of the extra characters.

2. **Matching Only Full Words with Word Boundaries (`\b`)**:
   Imagine you want to search a text for the Visa card number `4111-1111-1111-1111`, but you don't want it to match if it appears as part of a longer sequence like `a4111-1111-1111-1111b`.

   Regex:
   ```regex
   \b4111-1111-1111-1111\b
   ```

   Using the `\b` metacharacter ensures that the card number is seen as a distinct "word" or sequence and is not surrounded by other alphanumeric characters.

   For the string `My card is 4111-1111-1111-1111!`, the pattern matches. However, for the string `X4111-1111-1111-1111Y`, it won't match because of the surrounding characters.

3. **Matching At the Start of a Line**:
   Consider a multiline string where you want to match credit card numbers only if they appear at the start of a line:

   Regex:
   ```regex
   ^\d{4}-\d{4}-\d{4}-\d{4}
   ```

   Given the text:
   ```
   My cards are:
   4111-1111-1111-1111
   Also, 5555-5555-5555-5555
   ```

   This regex will only match `4111-1111-1111-1111` because it starts at the beginning of a line, while the other card number is preceded by the word "Also".

4. **Matching At the End of a Line**:
   Using the `$` metacharacter, you can find credit card numbers that appear at the end of a line:

   Regex:
   ```regex
   \d{4}-\d{4}-\d{4}-\d{4}$
   ```

   Given the text:
   ```
   The cards 4111-1111-1111-1111 and
   5555-5555-5555-5555 are mine.
   ```

   This regex will only match `5555-5555-5555-5555` because it's at the end of its line.

5. **Matching Card Numbers between Parentheses**:
   Let's say you want to match card numbers only when they are fully enclosed in parentheses:

   Regex:
   ```regex
   \((\d{4}-\d{4}-\d{4}-\d{4})\)
   ```

   For the string `My card is (4111-1111-1111-1111)`, the pattern matches, capturing the number. But for `4111-1111-1111-1111)` or `(4111-1111-1111-1111`, it won't match.

Boundaries ensure that your matches are contextually accurate by considering the position of a pattern relative to its surroundings.

### Back-references

Back-references in regular expressions allow you to refer back to a portion of a string that was already matched by a previous capturing group in the regex pattern. Essentially, they let you say "match this pattern, and then later match the exact same text again."

Capturing groups are created in regex by placing part of the pattern inside parentheses `( )`. These groups can then be referred back to using a backslash `\` followed by the group number, such as `\1` for the first group, `\2` for the second, and so on.

### Examples with credit card numbers:

1. **Matching Paired Delimiters**:
    Suppose you want to match a credit card number that uses the same delimiter character between the groups of numbers, but you don't know in advance which delimiter will be used. This is where a back-reference can be helpful.
    
    Regex Pattern:
    ```
    ^(\d{4})([ -])(\d{4})\2(\d{4})\2(\d{4})$
    ```
    This pattern will match:
    ```
    1234-5678-9012-3456
    1234 5678 9012 3456
    ```
    But not:
    ```
    1234-5678 9012-3456
    ```

2. **Matching Mirrored Sequences**:
    While this isn't necessarily related to credit card numbers, it's a fun example that demonstrates the concept of back-references. If you want to match strings where the second half is the reverse of the first half, you can use a pattern like this:
    ```
    ^(....)\4$
    ```

### Common Uses:
Back-references are commonly used in situations where:

- **Find & Replace Operations**: When you're trying to reformat strings, you can use back-references in the replacement string to refer to portions of the original string.
- **Validation**: To ensure that certain parts of a string match other parts, as in the delimiter example above.
- **Advanced Pattern Recognition**: For tasks that require matching repeating or mirrored patterns within a string.

### Important Notes:
- The power of back-references comes at a cost: they can make regex operations slower, especially when used in complex patterns.
- Not all regex flavors or tools support back-references, but most of the popular ones like PCRE (used in languages like PHP), Java, .NET, and Python do.
- When using back-references in a string replacement operation, some environments or tools might require `$1` instead of `\1` to reference captured groups.

Remember, while back-references are powerful, they should be used judiciously to keep regex patterns readable and maintainable.

## Author

Follow me on Github at [Arun Ayyasamy](https://github.com/arundvp). Additional questions or concerns? feel free to contact me.

GitHub Repository:[GitHub Repository: Click Here](https://github.com/arundvp/My-RegexTutorial1.1)

GitHub Gist Reference:[GitHub Gist Reference: Click Here](https://gist.github.com/arundvp/188d92fefda9bb7546ee52a9ecf7aad6)

© 2023 [Arun Ayyasamy](https://github.com/arundvp). Confidential and Proprietary. All Rights Reserved.
