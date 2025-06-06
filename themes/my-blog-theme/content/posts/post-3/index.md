+++
title = 'Hugo で SyntaxHighlight が適用されない。これはテストです。これはテストです。'
date = 2023-03-15T11:00:00-07:00
draft = false
tags = ['red','green','blue','a','android','emacs','nix','iOS','hello','commom','emacs-lisp']
+++

# 見出し1

これは見出し1です。

## 見出し2

これは見出し2です。

### 見出し3

これは見出し3です。

#### 見出し4

これは見出し4です。

##### 見出し5

これは見出し5です。

###### 見出し6

これは見出し6です。

## 段落

これは1つ目の段落の1行目です。  
これは1つ目の段落の2行目です。

これは2つ目の段落の1行目です。  
これは2つ目の段落の2行目です。

## 順序無しリスト

これは順序無しリストです。

- 1
- 2
    - 2-1
    - 2-2
        - 2-2-1
        - 2-2-2
- 3

これは順序無しリストです。

## 順序付きリスト

これは順序付きリストです。

1. 1
1. 2
    1. 2-1
    1. 2-2
        1. 2-2-1
        1. 2-2-2
1. 3

これは順序付きリストです。

## 引用

これは引用です。

> 引用
> 引用

これは引用です。

## コードブロック

これはコードブロックです。

```ruby {name="foo.rb"}
puts 1 + 1
```

```bash {class="my-class" id="my-codeblock" lineNos=inline tabWidth=2}
declare a=1
echo "$a"
exit
```

これはコードブロックです。

## テーブル

これはテーブルです。

| Left align | Right align | Center align |
|:-----------|------------:|:------------:|
| This       |        This |     This     |
| column     |      column |    column    |
| will       |        will |     will     |
| be         |          be |      be      |
| left       |       right |    center    |
| aligned    |     aligned |   aligned    |

これはテーブルです。

## リンク

これは [リンク](http://example.com) です。

## コード

これは `puts 1 + 1` コードです。

## 強い強調

これは **強い強調** です。

## 強調

これは *強調* です。

## 削除済みテキスト

これは ~~削除済みテキスト~~ です。

### 定義リスト

これは定義リストです。

<dl>
  <dt>リンゴ</dt>
  <dd>赤いフルーツ</dd>
  <dt>オレンジ</dt>
  <dd>橙色のフルーツ</dd>
</dl>

これは定義リストです。

## 水平線

これは水平線です。

---

これは水平線です。

## 画像

これは ![バナナ](https://upload.wikimedia.org/wikipedia/commons/thumb/d/de/Bananavarieties.jpg/220px-Bananavarieties.jpg) 画像です。
