---
title:      Some regular expressions for Japanese
layout:     post
category:   tutorial-tips
tags:       [regex, php, japanese]
---
In working with Japanese customers, i found useful regular expressions.

<!--more-->

Regex for matching ALL Japanese common & uncommon Kanji (`4e00` – `9fcf`) ~ The Big Kahuna!

```javascript
/[一-龯]/
```

Regex for matching Hirgana or Katakana [^1]

```javascript
/[ぁ-んァ-ン]/
```

Regex for matching Non-Hirgana or Non-Katakana

```javascript
/[^ぁ-んァ-ン]/
```

Regex for matching Hirgana or Katakana or basic punctuation (、。’)

```javascript
/[ぁ-んァ-ン\w]/
```

Regex for matching Hirgana or Katakana and random other characters

```javascript
/[ぁ-んァ-ン！：／]/
```

Regex for matching Hirgana

```javascript
/[ぁ-ん]/
```

Regex for matching full-width Katakana (zenkaku 全角)

```javascript
/[ァ-ン]/
```

Regex for matching half-width Katakana (hankaku 半角)

```javascript
/[ｧ-ﾝﾞﾟ]/
```

Regex for matching full-width Numbers (zenkaku 全角)

```javascript
/[０-９]/
```

Regex for matching full-width Letters (zenkaku 全角)

```javascript
/[Ａ-ｚ]/
```

Regex for matching Hiragana codespace characters (includes non phonetic characters)

```javascript
/[ぁ-ゞ]/
```

Regex for matching full-width (zenkaku) Katakana codespace characters (includes non phonetic characters)

```javascript
/[ァ-ヶ]/
```

Regex for matching half-width (hankaku) Katakana codespace characters (this is an old character set so the order is inconsistent with the hiragana)

```javascript
/[ｦ-ﾟ]/
```

Regex for matching Japanese Post Codes

```javascript
/^\d{3}-\d{4}$/
/^\d{3}-\d{4}$|^\d{3}-\d{2}$|^\d{3}$/
```
Regex for matching Japanese mobile phone numbers (keitai bangou)

```javascript
/^\d{3}-\d{4}-\d{4}$|^\d{11}$/
/^0\d0-\d{4}-\d{4}$/
```

Regex for matching Japanese fixed line phone numbers

```javascript
/^[0-9-]{6,9}$|^[0-9-]{12}$/
/^\d{1,4}-\d{4}$|^\d{2,5}-\d{1,4}-\d{4}$/
```

You can try in here: [http://www.regexr.com/][regexr]

[^1]: Katakana without mentioning "full-width" or "half-width" means "full-width katakana".

[regexr]: http://www.regexr.com/