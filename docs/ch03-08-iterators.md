# イテレータ

イテレータとはコンテナ内での要素の位置を指すもので、
ポインタのように扱うことができます。
イテレータを使用することで
コンテナの種類に依存しないで処理を共通化できます。

```cpp
std::vector<int> x = {0, 1, 2, 3, 4};

// begin() でコンテナ内の先頭要素を指すイテレータを取得
auto it = x.begin();

// イテレータを使用して要素を出力
std::cout << *it << std::endl;  // 0

// イテレータを1つ進める
++it;

// イテレータを使用して要素を出力
std::cout << *it << std::endl;  // 1
```

イテレータが指す要素を参照するには
ポインタのデリファレンス同様に `*` をつけます。

イテレータはインクリメントで1つ進めることができます。

```cpp
std::vector<int> x = {0, 1, 2, 3, 4};

// end() でコンテナ内の最終要素の1つ先を指すイテレータを取得
for (auto it = x.begin(); it != x.end(); ++it) {
    std::cout << *it << std::endl;
}
```

`end()` で取得するイテレータは最終要素ではなく、
最終要素の1つ先であるためループの終了条件として使用できます。

イテレータはコンテナの種類に依存しないで処理を行えるため、
次のように `std::set` に変更してもそのまま動作します。

```cpp
std::set<int> x = {0, 1, 2, 3, 4};

for (auto it = x.begin(); it != x.end(); ++it) {
    std::cout << *it << std::endl;
}
```

## マップのイテレータ

`std::map` や `std::unordered_map` のイテレータが指す要素はペアとなっています。
このペアは `first` がキーで `second` が値です。

```cpp hl_lines="7 8 9"
std::map<std::string, int> persons = {
    {"Alice", 18},
    {"Bob", 20}
};

for (auto it = persons.begin(); it != persons.end(); ++it) {
    const auto& person = *it;  // std::pair<std::string, int>
    const std::string& name = person.first;
    const int age = person.second;
    std::cout << name << ": " << age << std::endl;
}
```

## イテレータの種類

標準ライブラリのイテレータは5種類ありますが、
本書ではそのうち以下3種類だけを紹介します。

* 前方向イテレータ (Forward Iterator)
* 双方向イテレータ (Bidirectional Iterator)
* ランダムアクセスイテレータ (Random Access Iterator)

この3種類は次のような is-a 関係があります。

![is-a関係を表した図][iterator-is-a-diagram]

[iterator-is-a-diagram]: img/iterator.svg

### 前方向イテレータ

イテレータを動かす場合に前に進めることだけができます。

`std::unordered_map` や `std::unordered_set` のイテレータが該当します。

```cpp
std::unordered_set<int> x = {0, 1, 2, 3, 4};

auto it = x.begin();
std::cout << *it << std::endl;

++it;  // 前に進める
std::cout << *it << std::endl;
```

### 双方向イテレータ

イテレータを動かす場合に前に進めるだけでなく、後ろへ戻すことができます。

`std::map` や `std::set` のイテレータが該当します。

```cpp
std::set<int> x = {0, 1, 2, 3, 4};

auto it = x.begin();
std::cout << *it << std::endl;  // 0

++it;  // 前に進める
std::cout << *it << std::endl;  // 1

--it;  // 後ろへ戻す
std::cout << *it << std::endl;  // 0
```

### ランダムアクセスイテレータ

イテレータを動かす場合に任意の位置へ動かすことができます。

`std::array` や `std::vector` のイテレータが該当します。

```cpp
std::vector<int> x = {0, 1, 2, 3, 4};

auto it = x.begin();
std::cout << *it << std::endl;  // 0

++it;  // 前に進める
std::cout << *it << std::endl;  // 1

--it;  // 後ろへ戻す
std::cout << *it << std::endl;  // 0

it = it + 3;  // 3つ前に進める
std::cout << *it << std::endl;  // 3

// it は変更せずに it から2つ後ろへ戻した要素を参照
std::cout << it[-2] << std::endl;  // 1 (it から2つ後ろへ戻した要素)
std::cout << *it << std::endl;  // 3 (it は変更されていない)
```

<!-- TODO: 範囲for文の説明 -->