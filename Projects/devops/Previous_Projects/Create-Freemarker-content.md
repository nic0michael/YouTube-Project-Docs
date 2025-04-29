---
title: "Create Freemarker Content"
date: 2024-03-01T06:41:39Z
draft: false
---

## 1. Introduction

We will use this site to test our freemarker templates: \
[https://try.freemarker.apache.org/](https://try.freemarker.apache.org/)
## 2. Simple Templates and Data Models working with Strings
### 2.1 Hello 
In this example we use : ${name1} to get the value of name1 and replace the placeholder : ${name1} with : Jack the Ripper 

We use this as Template
```
Hello ${name1}
Hello ${name2}

```
We use this as Data Model
```
name1 = "Jack the Ripper"
name2 = "Elvis Presley"

```

We get this result:
```
Hello Jack the Ripper
Hello Elvis Presley

```

### 2.2 Fettuccine Alfredo
In the example	we use : ${fettuccineAlfredo} to get the value of fettuccineAlfredo \
and replace the placeholder : ${fettuccineAlfredo} with	: Ingredients: Pasta, Alfredo sauce, Parmesan cheese
We use this as Template
```
${fettuccineAlfredo}
```
We use this as Data Model
```
fettuccineAlfredo = "Ingredients: Pasta, Alfredo sauce, Parmesan cheese"
spaghettiBolognese = "Ingredients: Spaghetti, Bolognese sauce, Ground beef, Parmesan cheese"
fettuccineCarbonara = "Ingredients: Pasta, Carbonara sauce, Bacon, Parmesan cheese"

```

We get this result:
```
Ingredients: Pasta, Alfredo sauce, Parmesan cheese
```

### 2.3 Simple Web page
In this example we use : ${name1} to get the value of name1 and replace the placeholder : ${name1} with : Jack the Ripper

We use this as Template
```
<!DOCTYPE html>
<html>
<head>
    <title>Greetings</title>
</head>
<body>
    <h2>Hello ${name1}</h2>
</body>
</html>

```
We use this as Data Model
```
name1 = "Jack the Ripper"
name2 = "Elvis Presley"

```

We get this result:
```
<!DOCTYPE html>
<html>
<head>
    <title>Greetings</title>
</head>
<body>
    <h2>Hello Jack the Ripper</h2>
</body>
</html>
```
### 2.4 HTML page with Header and Footer
In this example we use : ${name1} to get the value of name1 and replace the placeholder : ${name1} with : Jack the Ripper \
We have added two more place holders ${header} and ${footer} to add header and footers to the web page

We use this as Template
```
<!DOCTYPE html>
<html>
<head>
    <title>Greetings</title>
</head>
<body>
    <div>${header}</div>
    <div>
        <h2>Hello ${name1}</h2>
    </div>
    <div>${footer}</div>
</body>
</html>

```
We use this as Data Model
```
name1 = "Jack the Ripper"
name2 = "Elvis Presley"
header = "This is our header"
footer = "This is our footer"

```

We get this result:
```
<!DOCTYPE html>
<html>
<head>
    <title>Greetings</title>
</head>
<body>
    <div>This is our header</div>
    <div>
        <h2>Hello Jack the Ripper</h2>
    </div>
    <div>This is our footer</div>
</body>
</html>
```
### 2.5 Adding Style to the previous web page
We add a placeholders ${style1} ${style2} $style3} and ${style4} to the previous page and add Style Classes to that web page \
We use this as Template
```
<!DOCTYPE html>
<html>
<head>
    <title>Greetings</title>
    <style>
        ${style1}
        ${style2}
        ${style3}
        ${style4}
    </style>
</head>
<body class="page-background">
    <div class="header">
        ${header}
    </div>
    <div class="page-border" style="background-color: #ffffff; padding: 20px;">
        <h2>Hello ${name1}</h2>
    </div>
    <div class="footer">
        ${footer}
    </div>
</body>
</html>
```
We use this as Data Model
```
name1 = "Jack the Ripper"
name2 = "Elvis Presley"
header = "This is our header"
footer = "This is our footer"

style1 = ".page-background { background-color: #f4f4f4; } "
style2 = ".page-border { border: 1px solid #ccc; margin: 10px; border-radius: 5px; } "
style3 = ".header { color: white; background-color: #020202; font-weight: bold; padding: 10px; } "
style4 = ".footer { color: orange; background-color: #333333; font-weight: bold; padding: 10px; } "

```

We get this result:
```
<!DOCTYPE html>
<html>
<head>
    <title>Greetings</title>
    <style>
        .page-background { background-color: #f4f4f4; } 
        .page-border { border: 1px solid #ccc; margin: 10px; border-radius: 5px; } 
        .header { color: white; background-color: #020202; font-weight: bold; padding: 10px; } 
        .footer { color: orange; background-color: #333333; font-weight: bold; padding: 10px; } 
    </style>
</head>
<body class="page-background">
    <div class="header">
        This is our header
    </div>
    <div class="page-border" style="background-color: #ffffff; padding: 20px;">
        <h2>Hello Jack the Ripper</h2>
    </div>
    <div class="footer">
        This is our footer
    </div>
</body>
</html>
```


### 2.6 HTML Email Template
When creating an HTML email message template, it's important to consider that some email clients may have limited support for CSS and certain HTML elements. Here's a simplified HTML email template with an <h2> element for the greeting: \
In this example we use : ${name1} to get the value of name1 and replace the placeholder : ${name1} with : Jack the Ripper
We use this as Template
```
<!DOCTYPE html>
<html>
<head>
    <title>Greetings</title>
    <style>
        /* Inline styles for compatibility */
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
            margin: 0;
            padding: 0;
        }
        .container {
            max-width: 600px;
            margin: 0 auto;
            padding: 20px;
            background-color: #fff;
            border-radius: 5px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }
        h2 {
            color: #333;
            margin-top: 0;
        }
    </style>
</head>
<body>
    <div class="container">
        <h2>Hello ${name1}</h2>
    </div>
</body>
</html>

```
We use this as Data Model
```
name1 = "Jack the Ripper"
name2 = "Elvis Presley"

```

We get this result:
```
<!DOCTYPE html>
<html>
<head>
    <title>Greetings</title>
    <style>
        /* Inline styles for compatibility */
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
            margin: 0;
            padding: 0;
        }
        .container {
            max-width: 600px;
            margin: 0 auto;
            padding: 20px;
            background-color: #fff;
            border-radius: 5px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }
        h2 {
            color: #333;
            margin-top: 0;
        }
    </style>
</head>
<body>
    <div class="container">
        <h2>Hello Jack the Ripper</h2>
    </div>
</body>
</html>

```
## 3. Arrays lists and Map 
### 3.1 Lists
In this example we use : ${name1} to get the value of name1 and replace the placeholder : ${name1} with : Jack the Ripper

We use this as Template
```
List of Names:
<#list names as name>
  - ${name}
</#list>
```
We use this as Data Model
```
names = ["Jack the Ripper", "Elvis Presley", "Tom Jones", "Tina Turner"]
```

We get this result:
```
List of Names:
  - Jack the Ripper
  - Elvis Presley
  - Tom Jones
  - Tina Turner

```

### 3.2 Arrays and Lists
In practice, FreeMarker treats arrays and lists very similarly. Both can be iterated over using <#list> directive, and both support similar operations such as accessing elements by index and determining their size.


We use this as Template
```
Name Array:
<#list nameArray as name>
  - ${name}
</#list>

Name List:
<#list nameList as name>
  - ${name}
</#list>

```
We use this as Data Model
```
nameArray = ["Jack the Ripper", "Elvis Presley", "Tom Jones", "Tina Turner"]
nameList = ["Jack the Ripper", "Elvis Presley", "Tom Jones", "Tina Turner"]

```

We get this result:
```
Name Array:
  - Jack the Ripper
  - Elvis Presley
  - Tom Jones
  - Tina Turner

Name List:
  - Jack the Ripper
  - Elvis Presley
  - Tom Jones
  - Tina Turner

```

## 3.3 Maps and HashMaps
In FreeMarker, both maps and hashes are treated similarly in terms of how you access their values and iterate over their elements. The syntax for accessing values from both maps and hashes is the same: variableName[key]

We use this as Template
```
<#if myMap["Jack"]??>
  Value for key "Jack" in Map: ${myMap["Jack"]}
<#else>
  Key "Jack" not found in the Map.
</#if>

<#if myHash["Jack"]??>
  Value for key "Jack" in Hash: ${myHash["Jack"]}
<#else>
  Key "Jack" not found in the Hash.
</#if>

```
We use this as Data Model
```
myMap = {
  "Jack": "Jack the Ripper",
  "Elvis": "Elvis Presley",
  "Tom": "Tom Jones",
  "Tina": "Tina Turner"
}

myHash = {
  "Jack": "Jack the Ripper",
  "Elvis": "Elvis Presley",
  "Tom": "Tom Jones",
  "Tina": "Tina Turner"
}

```

We get this result:
```
  Value for key "Jack" in Map: Jack the Ripper

  Value for key "Jack" in Hash: Jack the Ripper

```
## 3.4 The Assign Directive
<#assign> Directive: Assign a value to a variable.

use this template :
```
<#assign message = "Hello, World!">

<!DOCTYPE html>
<html>
<head>
    <title>Greetings</title>
</head>
<body>
    <h1>${message}</h1>
</body>
</html>
```

We get this result:
```
<!DOCTYPE html>
<html>
<head>
    <title>Greetings</title>
</head>
<body>
    <h1>Hello, World!</h1>
</body>
</html>

```
## 3.5 The Makro Directive to define reusable blocks of template code.
The <#macro> directive in FreeMarker allows you to define reusable blocks of template code, similar to functions or procedures in other programming languages. \
Here's an overview of how it works:
```
<#macro macroName param1 param2 ...>
    Template code here
</#macro>

```

The <#macro> directive in FreeMarker allows you to define reusable blocks of template code, similar to functions or procedures in other programming languages. Here's an overview of how it works:

<#macro macroName param1 param2 ...>
    Template code here
</#macro>
1. **macroName**: The name of the macro.
2. **param1, param2, ...**: Optional parameters that the macro can accept.
3. **Template code here**: The code block that defines the behavior of the macro.

#### 3.5.1 a Simple Makro
use this template :
```
<#macro greet name>
    Hello, ${name}!
</#macro>

<!DOCTYPE html>
<html>
<head>
    <title>Greetings</title>
</head>
<body>
    <h1>
        <@greet name="John" />
    </h1>
</body>
</html>

```

use this Model
```
We dont need a model
```

Result :
```
<!DOCTYPE html>
<html>
<head>
    <title>Greetings</title>
</head>
<body>
    <h1>
    Hello, John!
    </h1>
</body>
</html>
```
#### 3.5.2 a Calculator Makro example
use this template :
```
<#macro calculateTotalPrice items>
    <#assign totalPrice = 0>
    <#list items as item>
        <#assign totalPrice = totalPrice + item.price>
    </#list>
    Total Price: ${totalPrice}
</#macro>

<!DOCTYPE html>
<html>
<head>
    <title>Shopping Cart</title>
</head>
<body>
    <h1>Shopping Cart</h1>
    <ul>
        <li>Item 1 - Price: $10</li>
        <li>Item 2 - Price: $20</li>
        <li>Item 3 - Price: $30</li>
    </ul>
    <p>
        <#assign items = [{ "name": "Item 1", "price": 10 }, { "name": "Item 2", "price": 20 }, { "name": "Item 3", "price": 30 }]>
        <@calculateTotalPrice items=items />
    </p>
</body>
</html>

```

use this Model
```
We dont need a model
```

Result :
```
<!DOCTYPE html>
<html>
<head>
    <title>Shopping Cart</title>
</head>
<body>
    <h1>Shopping Cart</h1>
    <ul>
        <li>Item 1 - Price: $10</li>
        <li>Item 2 - Price: $20</li>
        <li>Item 3 - Price: $30</li>
    </ul>
    <p>
    Total Price: 60
    </p>
</body>
</html>
```
## 3.6 The Include directive <#include> Directive
The <#include> directive is a feature of FreeMarker that allows including the content of another template file within the current template. 
It's typically used for modularizing templates and reusing common components across multiple templates.

First we create the file useful.ftl with this code:
```
<#macro calculator num1 operator num2>
    <#if operator == "+">
        <#return num1 + num2>
    <#elseif operator == "-">
        <#return num1 - num2>
    <#elseif operator == "*">
        <#return num1 * num2>
    <#elseif operator == "/">
        <#return num1 / num2>
    <#else>
        Unknown operator: ${operator}
    </#if>
</#macro>

<#macro currentDate>
    <#return .now?string("yyyy-MM-dd")>
</#macro>

<#macro welcomeMessage name>
    Hello, ${name}! Welcome to our website.
</#macro>

```

Now we create this template : it can be : main.ftl
```
<!DOCTYPE html>
<html>
<head>
    <title>FreeMarker Demo</title>
</head>
<body>
    <h1>FreeMarker Demo</h1>
    
    <#-- Include useful.ftl from the provided URL -->
    <#include "path/to/useful.ftl">
    
    <p>Today's date: ${currentDate()}</p>
    
    <p>Calculator Example:</p>
    <p>2 + 3 = ${calculator(2, "+", 3)}</p>
    <p>10 - 5 = ${calculator(10, "-", 5)}</p>
    <p>4 * 6 = ${calculator(4, "*", 6)}</p>
    <p>15 / 3 = ${calculator(15, "/", 3)}</p>
    
    <p>${welcomeMessage("John")}</p>
    
    <p>This page is using macros from an included file.</p>
</body>
</html>

```
Unfortunatly the "Online FreeMarker Template Tester" can work with externel files not eve if accessable from the Internet

## 3.7 String Manipulation
We will show examples of the String manipulation in this template
```
<!DOCTYPE html>
<html>
<head>
    <title>String Manipulation Example</title>
</head>
<body>
    <h1>String Manipulation Example</h1>
    
    <p>Original Message: ${message}</p>
    
    <p>1. Uppercase: ${message?upper_case}</p>
    <p>2. Lowercase: ${message?lower_case}</p>
    <p>3. Capitalize First Letter: ${message?cap_first}</p>
    <p>4. Substring (from index 10): ${message?substring(10)}</p>
    <p>5. Substring (from index 16 to 29): ${message?substring(16, 29)}</p>
    <p>6. Replace "fox" with "dog": ${message?replace("fox", "dog")}</p>
    <p>7. Trim whitespace: ${message?trim}</p>
    <p>8. Length: ${message?length}</p>
    <p>9. Index of "lazy": ${message?index_of("lazy")}</p>
    <p>10. Contains "dog"? ${message?contains("dog")?string("yes", "no")}</p>
</body>
</html>
```
Using this Model:
```
message = "The quick brown Fox jumps over the lazy fox "
```

Result:
```
<!DOCTYPE html>
<html>
<head>
    <title>String Manipulation Example</title>
</head>
<body>
    <h1>String Manipulation Example</h1>
    
    <p>Original Message: The quick brown Fox jumps over the lazy fox </p>
    
    <p>1. Uppercase: THE QUICK BROWN FOX JUMPS OVER THE LAZY FOX </p>
    <p>2. Lowercase: the quick brown fox jumps over the lazy fox </p>
    <p>3. Capitalize First Letter: The quick brown Fox jumps over the lazy fox </p>
    <p>4. Substring (from index 10): brown Fox jumps over the lazy fox </p>
    <p>5. Substring (from index 16 to 29): Fox jumps ove</p>
    <p>6. Replace "fox" with "dog": The quick brown Fox jumps over the lazy dog </p>
    <p>7. Trim whitespace: The quick brown Fox jumps over the lazy fox</p>
    <p>8. Length: 44</p>
    <p>9. Index of "lazy": 35</p>
    <p>10. Contains "dog"? no</p>
</body>
</html>
```

## 3.8 Number Formatting
We will show all the examples on number formatting in this template
```
<!DOCTYPE html>
<html>
<head>
    <title>Number Formatting Example</title>
</head>
<body>
    <h1>Number Formatting Example</h1>
    
    <#list numbers as number>
        <p>Original Number: ${number}</p>
        <p>Formatted Number (with two decimal places): ${number?string("#.##")}</p>
        <p>Formatted Number (with comma as thousands separator): ${number?string("#,###")}</p>
        <p>Formatted Number (scientific notation): ${number?string("0.###E0")}</p>
        <p>Formatted Number (percentage): ${number?string("#%")}</p>
        <hr>
    </#list>
</body>
</html>

```

Using this model :
```
numbers=[123.456, 789, 12.34]
```
 
Result:
```
<!DOCTYPE html>
<html>
<head>
    <title>Number Formatting Example</title>
</head>
<body>
    <h1>Number Formatting Example</h1>
    
            <p>Original Number: 123.456</p>
        <p>Formatted Number (with two decimal places): 123.46</p>
        <p>Formatted Number (with comma as thousands separator): 123</p>
        <p>Formatted Number (scientific notation): 1.235E2</p>
        <p>Formatted Number (percentage): 12346%</p>
        <hr>
        <p>Original Number: 789</p>
        <p>Formatted Number (with two decimal places): 789</p>
        <p>Formatted Number (with comma as thousands separator): 789</p>
        <p>Formatted Number (scientific notation): 7.89E2</p>
        <p>Formatted Number (percentage): 78900%</p>
        <hr>
        <p>Original Number: 12.34</p>
        <p>Formatted Number (with two decimal places): 12.34</p>
        <p>Formatted Number (with comma as thousands separator): 12</p>
        <p>Formatted Number (scientific notation): 1.234E1</p>
        <p>Formatted Number (percentage): 1234%</p>
        <hr>
</body>
</html>

```

## 3.9 Logical Comparison 
We will show all the examples on Logical Comparison in this template
```
<!DOCTYPE html>
<html>
<head>
    <title>Logical Comparison Example</title>
</head>
<body>
    <h1>Logical Comparison Example</h1>
    
    <#assign age = 20>
    <#assign score = 85>
    <#assign isStudent = true>
    <#assign username = "John">
    
    <p>Data Model:</p>
    <p>Age: ${age}</p>
    <p>Score: ${score}</p>
    <p>Is Student: ${isStudent?string("yes", "no")}</p>
    <p>Username: ${username}</p>
    
    <!-- Rest of the template -->
</body>
</html>
```

We will use this Data Model:
```
age=20
score=85
isStudent=true
username="John"

```

Result:
```
<!DOCTYPE html>
<html>
<head>
    <title>Logical Comparison Example</title>
</head>
<body>
    <h1>Logical Comparison Example</h1>
    
        
    <p>Data Model:</p>
    <p>Age: 20</p>
    <p>Score: 85</p>
    <p>Is Student: yes</p>
    <p>Username: John</p>
    
    <!-- Rest of the template -->
</body>
</html>
```

## 3.10 Collection Manipulation
FreeMarker provides various directives and functions for working with collections (such as lists and maps) in templates. Here are some key features:

#### 3.10.1 Iterating Over Lists and Sequences:
**<#list>**: Iterates over each element in a list or sequence. \
**item_index**: Special variable holding the index of the current item in the list.
Example:
```
<#list numbers as num>
    Item ${item_index + 1}: ${num}
</#list>
```

#### 3.10.2 Iterating Over Maps:
**<#list map as key, value>**: Iterates over each key-value pair in a map. \
Example:
```
<#list person as key, value>
    ${key}: ${value}
</#list>
```
#### 3.10.3 Sorting Lists:
**?sort**: Sorts a list in ascending order.
**?sort_by("property")**: Sorts a list of objects by a specific property. \
Example:
```
<#list numbers?sort as num>
    ${num}
</#list>
```
#### 3.10.4 Filtering Lists:
**?filter(predicate)**: Filters a list based on a predicate function. \
Example:
```
<#list numbers?filter(num -> num > 10) as num>
    ${num}
</#list>
```
#### 3.10.5 Mapping and Transforming Lists:
**?map(transformer)**: Maps each element in a list to a new value based on a transformation function. \
Example:
```
<#list numbers?map(num -> num * 2) as num>
    ${num}
</#list>
```

#### 3.10.6 Other Operations:
**?size**: Returns the size of a list or map. \
**?contains(value)**: Checks if a list or map contains a specific value.

The following template will demonstrate all of this:
```
<!DOCTYPE html>
<html>
<head>
    <title>Collection Manipulation Example</title>
</head>
<body>
    <h1>Collection Manipulation Example</h1>
    
    <p>Original List of Numbers:</p>
    <ul>
        <#list numbers as num>
            <li>${num}</li>
        </#list>
    </ul>
    
    <hr>
    
    <p>Sorted List of Numbers:</p>
    <ul>
        <#list numbers?sort as num>
            <li>${num}</li>
        </#list>
    </ul>
    
    <hr>
    
    <p>Filtered List (Numbers > 5):</p>
    <ul>
        <#list numbers?filter(num -> num > 5) as num>
            <li>${num}</li>
        </#list>
    </ul>
    
    <hr>
    
    <p>Mapped List (Doubled Numbers):</p>
    <ul>
        <#list numbers?map(num -> num * 2) as num>
            <li>${num}</li>
        </#list>
    </ul>
    
    <hr>
    
    <p>Iterating Over Map (Person Details):</p>
    <ul>
        <#list person as key, value>
            <li>${key}: ${value}</li>
        </#list>
    </ul>
</body>
</html>
```
The required <model:
```
numbers=[5, 3, 8, 2, 7]
person={"name": "John", "age": 30, "city": "New York"}
```
Results:
```
<!DOCTYPE html>
<html>
<head>
    <title>Collection Manipulation Example</title>
</head>
<body>
    <h1>Collection Manipulation Example</h1>
    
    <p>Original List of Numbers:</p>
    <ul>
                    <li>5</li>
            <li>3</li>
            <li>8</li>
            <li>2</li>
            <li>7</li>
    </ul>
    
    <hr>
    
    <p>Sorted List of Numbers:</p>
    <ul>
            <li>2</li>
            <li>3</li>
            <li>5</li>
            <li>7</li>
            <li>8</li>
    </ul>
    
    <hr>
    
    <p>Filtered List (Numbers > 5):</p>
    <ul>
            <li>8</li>
            <li>7</li>
    </ul>
    
    <hr>
    
    <p>Mapped List (Doubled Numbers):</p>
    <ul>
            <li>10</li>
            <li>6</li>
            <li>16</li>
            <li>4</li>
            <li>14</li>
    </ul>
    
    <hr>
    
    <p>Iterating Over Map (Person Details):</p>
    <ul>
            <li>name: John</li>
            <li>age: 30</li>
            <li>city: New York</li>
    </ul>
</body>
</html>
```

## Bibliography
Official Documentation: \
https://try.freemarker.apache.org/ \
https://freemarker.apache.org/docs/ref.html \
https://freemarker.apache.org/docs/xgui_imperative_learn.html \
https://freemarker.apache.org/docs/index.html \
https://usermanual.wiki/buckets/90534/1646465766/freemarker-manual.pdf \
Some Internet resources: \
https://www.vogella.com/tutorials/FreeMarker/article.html \
https://www.baeldung.com/freemarker-operations \
https://suitecorner.com/en/use-freemarker-functions-in-your-reporter-layouts-and-advanced-pdf-html-templates-in-netsuite/ \
https://maurodatamapper.github.io/plugins/rest-api/freemarker/#basic-usage \

