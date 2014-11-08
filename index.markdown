# kamiyama.rb 1

### Author

* か (ka)
* [kaosfield](http://www.kaosfield.net)


神山.rb開催おめでとうございます


## reduce

Rubyistの皆さんこんなコード書いてませんか

```ruby
sum = 0
[1, 2, 3].each do |i|
  sum += i
end
```


今すぐそんなコードは投げ捨てよう


他にも以下のような例

```ruby
str = ""
["a", "b", "c"].each do |i|
  str += (i + ",")
end
```


`reduce`を使おう

同じ名前の`inject`でも良い


## 書き換え

```ruby
sum = [1, 2, 3].reduce(0) { |s, i| s + i }

str = ["a", "b", "c"].inject("") { |s, i| s + i + "," }
```


## 動作説明

メソッドの引数を初期値として，  
リストの全要素に対して，  
ブロックの第一引数が初期値に，  
第二引数が各要素になりながら，  
戻り値を次のイテレーションの第一引数にし，  
ループする．  

最終的な戻り値は最後のブロックの戻り値．


なにいってだこいつ


## コードで説明

```ruby
[1, 2, 3].reduce(0) { |s, i| s + i }
```

まず `0` がブロックの `s` に  
`1` が `i` に渡る

`s + i` は `0 + 1` なのでブロックの戻り値は `1`

それが次の `s` になる


## つまり

```ruby
[1, 2, 3].reduce(0) { |s, i| s + i }
```

が

```ruby
[2, 3].reduce(1) { |s, i| s + i }
```

という状態になる


## 繰り返すと

```ruby
[1, 2, 3].reduce(0) { |s, i| s + i }
[2, 3].reduce(1) { |s, i| s + i }
[3].reduce(3) { |s, i| s + i }
```

そして最後の `s + i` が `6` になりそれが結果になる


## 一つの式で表現

```ruby
(((0 + 1) + 2) + 3)
```

これが計算されるわけである

これは所謂 `foldl` である


## 要素が空の時

```ruby
[].reduce(10) { |s, i| s + i } #=> 10
```

初期値がそのまま返る


前のページで「最後のブロックの戻り値が結果になる」と言ったな

あれは嘘だ


## 正しい繰り返しの形

```ruby
[1, 2, 3].reduce(0) { |s, i| s + i }
[2, 3].reduce(1) { |s, i| s + i }
[3].reduce(3) { |s, i| s + i }
[].reduce(6) { |s, i| s + i } #=> 6
```


## メソッドに初期値渡さない

例外的にリストの第一要素が初期値になる

```ruby
[1, 2, 3].reduce { |s, i| s + i }
[2, 3].reduce(1) { |s, i| s + i }
[3].reduce(3) { |s, i| s + i }
[].reduce(6) { |s, i| s + i } #=> 6
```


## ブロックじゃなくてメソッドを渡す

```ruby
[1, 2, 3].reduce(0, :+) #=> 6
```

これでも大丈夫


## 何が起こるか

`method`が指定されたとすると…

```ruby
[1, 2, 3].reduce(0) { |s, i| s.method(i) }
```


## 分かる人向け

`:method` が指定された場合

```ruby
[1, 2, 3].reduce(0) { |s, i| s.send(:method, i) }
```


## 例

```ruby
class Fixnum
  def add10(x)
    10 * self + x
  end
end

[1, 2, 3].reduce(0, :add10) #=> 123
```


## 嫌な臭いは早めに消そう

```ruby
s = 'shokichi'
# ...
for i in ['youso', 'element', 1, 2]
  # ...
  s.ni_nanika_suru(i)
end
nantoka_variable = brushup(s)
```

ペロ…これは…クソコード！


## 実際は

```ruby
["a", "b", "c"].join(",") #=> "a,b,c"
```

メソッドが用意されていることも多い

多分さっきの例で本当にやりたかったことはこうだと思う


## まとめ

初期値設定してループ回して書き換えしていくのはやめよう

面倒・間違えやすい・コードの行数も増える

色々なメソッドがあるのでちゃんと調べよう
