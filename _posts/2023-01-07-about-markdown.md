---
layout: post
title: About Markdown
subtitle: Markdown dictionary
tags: [study, general, markdown]
comments: true
---

## 1. Italics and Bold

### Italic

기울임(Italic)을 적용하려면 text 양쪽을 \_로 감싸주면 된다.

"\_Of course\_" she said.  
"_Of course_" she said.

### Bold

굵은 글씨(Bold)를 적용하려면, text 양쪽을 \*\*로 감싸주면 된다.

This will be \*\*only one\*\* chance in my life.  
This will be **only one** chance in my life.

## 2. Headers

헤더를 만들고 싶다면, text 앞에 \#을 붙여주면 된다.  
이 때, n번째 level의 Header를 만들고 싶다면 \#을 n개 만큼 붙여주면 된다.  
일반적으로 1번, 6번 Header는 잘 사용하지 않으며, Header에 Bold 적용은 어려우나 Italic 적용이 되는 경우도 있다.

\# Header_1  
\#\# Header_2  
...  
\#\#\#\#\#\# Header_6

## 3. Links

### Inline Link

Link를 다른 text(link text)로 보여주고 싶다면, \[\] 안에 text를 넣고, \(\) 안에 실제 link를 넣으면 된다.  
Link text안에 Bold나 Italic으로 강조하는 것도 가능하며, Header의 일부를 Link text로 만드는 것도 가능하다.

\[View on Github\]\(https://www.github.com\)  
[View on Github](https://www.github.com)

### Reference Link

Link에 바로 주소를 기입하는 것이 아니라, Reference를 달아놓을 수 있다.  
이렇게 하면 Link를 업데이트할 때 여러 Link를 따로따로 업데이트하지 않아도 된다는 장점이 있다.

\[View on Github\]\[GitHub\]  
[View on Github][GitHub]

\[GitHub\]: https://www.github.com \(이 Reference는 실제로는 안 보인다. 예시를 위해 적어 놓은 것이다.\)  

[GitHub]: https://www.github.com

## 4. Images

Image와 Link는 기본적으로 비슷하다. Link의 표현법 앞에 \!만 붙여주면 된다.

### Image Link

Alt text를 꼭 넣을 필요는 없지만, 시각 장애인 등 보기 어려운 사람들에게 해당 내용을 설명해줄 수 있는 자료가 된다.

\!\[A pretty tiger\]\(https://upload.wikimedia.org/wikipedia/commons/5/56/Tiger.50.jpg\)  
![A pretty tiger](https://upload.wikimedia.org/wikipedia/commons/5/56/Tiger.50.jpg)

### Reference Image

위의 Reference Link와 만드는 방식은 동일하다. 앞에 \!를 붙인다는 점을 빼고 말이다.
\!\[Black cat\]\[Black\]  
\!\[Orange cat\]\[Orange\]  
\[Black\]: https://upload.wikimedia.org/wikipedia/commons/a/a3/81_INF_DIV_SSI.jpg  
\[Orange\]: http://icons.iconarchive.com/icons/google/noto-emoji-animals-nature/256/22221-cat-icon.png  
(위의 Reference Definition 역시 실제로는 나타나지 않는다.)

![Black cat][Black]

![Orange cat][Orange]

[Black]: https://upload.wikimedia.org/wikipedia/commons/a/a3/81_INF_DIV_SSI.jpg
[Orange]: http://icons.iconarchive.com/icons/google/noto-emoji-animals-nature/256/22221-cat-icon.png

## 5. Blockquotes

때로는 인용문에 특별한 표식을 해줘야 할 때가 있다. 그럴 때에는 Blockquote를 사용한다.  
Line의 앞에 \> 표시를 해주는 것으로 끝이다.  
Blockquote 안에는 위의 다른 문법들도 들어갈 수 있다.  

I read this interesting quote the other day:  
\> "Her eyes had called him and his soul had leaped at the call. To live, to err, to fall, to triumph, to recreate life out of life!"

I read this interesting quote the other day:  
> "Her eyes had called him and his soul had leaped at the call. To live, to err, to fall, to triumph, to recreate life out of life!"

### Grouping Blockquotes

Multiline에 걸쳐 Blockquote를 생성하고 싶을 때에는, 해당하는 모든 line의 맨 앞에 \> 표시를 해주면 된다.

\> Once upon a time and a very good time it was there was a moocow coming down along the road and this moocow that was coming down along the road met a nicens little boy named baby tuckoo...  
\>  
\> His father told him that story: his father looked at him through a glass: he had a hairy face.  
\>  
\> He was baby tuckoo. The moocow came down the road where Betty Byrne lived: she sold lemon platt.  

> Once upon a time and a very good time it was there was a moocow coming down along the road and this moocow that was coming down along the road met a nicens little boy named baby tuckoo...
>
> His father told him that story: his father looked at him through a glass: he had a hairy face.
>
> He was baby tuckoo. The moocow came down the road where Betty Byrne lived: she sold lemon platt.

## 6. Lists

### Unordered List

Unordered List는 Element들 앞에 \*를 적어줌으로써 표현한다.

\* Flour  
\* Cheeze  
\* Tomatoes  

* Flour
* Cheese
* Tomatoes

### Ordered List

Ordered List는 Element들 앞에 숫자와 \.을 적어줌으로써 표현한다.

\1. Cut the cheese  
\2. Slice the tomatoes  
\3. Rub the tomatoes in flour

1. Cut the cheese
2. Slice the tomatoes
3. Rub the tomatoes in flour

### Nested List

Nested List를 표현하고 싶을 때에는, List를 나타내는 표시를 상위 표시보다 2 space 이상 들여쓰기하면 된다.

\* Calculus  
(space)(space)\* A professor  
...

* Calculus
  * A professor
  * Has no hair
  * Often wears green
* Castafiore
  * An opera singer
  * Has white hair
  * Is possibly mentally unwell

### Add Paragraph In List Element

List Element의 text가 길어져서 Paragraph를 만들어야 할 경우, 해당 List Element에 해당하는 Indentation을 모든 Line마다 해주면 된다.
2 space면 충분하지만, Align을 위해 indentation을 3 space를 주었다.

\1. First Element  
(space)(space)(space)First Paragraph  
...

1. First Element  
   First Paragraph  
   And it's text  
   1. First-First Element  
      First-First Paragraph  
      And it's text  
   2. First-Second Element  
      First-Second Paragraph  
2. Second Element

## 7. Paragraphs

그냥 Enter로 새로운 줄을 표시하면, Markdown 언어에서는 두 줄을 한 줄로써 표현한다.  
Enter를 두 번 치면 두 줄을 서로 다르게 표시하긴 하지만, 줄 간격이 너무 넓어 둘이 마치 다른 구문(paragraph)처럼 보인다.  
이를 Hard break라고 하는데, soft break, 그러니까 같은 Paragraph 안에 Multi line을 표시하려면, line의 끝에 space를 두 개 이상 넣어야 한다.
(이미 이 post에 해당 방법이 다 적용되어 있다.)  
Soft break는 일반 paragrph를 표시할 때에도 쓰이지만, List를 표시할 때 많이 쓰인다.
(같은 Element 내에서는 Soft break, 다른 Element에 대해서는 Hard break)

We pictured the meek mild creatures where  
They dwelt in their strawy pen,  
Nor did it occur to one of us there  
To doubt they were kneeling then.

## 8. Congratulations!

여기까지가 Tutorial의 끝이다.  
이것 외에도 Table, Definition list, Footnotes 등 추가적인 활용법들이 있다고 한다. 이들을 블로그를 작성하다가 추가적으로 배우면 좋을 것 같다.

[Tutorial Link](https://www.markdowntutorial.com/conclusion/)