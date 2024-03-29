---
layout: post
title: Extended Markdown
subtitle: Markdown dictionary
tags: [study, general, markdown]
comments: true
---

Markdown 언어의 Extended 기능에 대해 알아보는 posting이다.  
공부를 하며 아래 사이트에서 직접 사용해본 기능들에 대해서만 여기에 추가하여 정리 할 예정이다.  
참고: [Markdown Extended Syntax](https://www.markdownguide.org/extended-syntax)

### 03. Fenced Code Blocks

code block 전후로 \`\`\`를 추가하거나, \~\~\~를 추가하면 된다.  
이 때, syntax highlighting을 위하여 첫 번째 backtick 앞에 language를 적어주면 된다.  
(VSCode의 Markdown linter를 사용하면 language를 적어주는 것이 필요하다.)

\`\`\`json  
{  
&nbsp;&nbsp;"firstName": "John",  
&nbsp;&nbsp;"lastName": "Smith",  
&nbsp;&nbsp;"age": 25  
}  
\`\`\`

```json
{
  "firstName": "John",
  "lastName": "Smith",
  "age": 25
}
```

### 04. Fenced code block inside a list

code block의 indentation을 조금 더 넣으면 된다.

* 1 element of unordered list
  * 1-1 element of unordered list

    ```python
    from unittest import mock

    def main():
      print("this code block indented with 4 spaces")
    ```

  * 1-2 element of unordered list

1. 1 element of ordered list
    1. 1-1 element of ordered list

        ```python
        from unittest import mock

        def main():
          print("this code block indented with 8 spaces")
        ```

    2. 1-2 element of ordered list

### 05. Inline code syntax

backtick 을 양쪽에 넣으면 된다.

이런 식이 된다. `code이에요~!`

code syntax highlight의 경우, github이나 특별한 extension이 있어야 하는 것 같다.
