# ビルド

!!! info "説明に使用する環境"
    Windows の MSYS2 環境は構成が複雑なため、説明では Linux 環境を使用します。

ソースファイルから実行ファイルを生成する処理をビルドといいます。
ビルドは次の手順で行われます。

1. プリプロセス
1. コンパイル
1. リンク

## プリプロセス

プリプロセスはコンパイル前に行われる前処理のことです。
コメントの削除と [プリプロセッサ司令] の実行を行います。

[プリプロセッサ司令]: appendix-preprocessor-directives.md

`-E` を指定するとプリプロセスだけを行うことができます。

```bash
$ g++ -std=c++11 -E main.cc
```

## コンパイル

人間が扱うソースファイルをコンピュータで扱うために0と1の表現 (機械語) に変換することをコンパイルといいます。
コンパイルはソースファイル単位で行います。

厳密にはコンパイルを行うプログラムのことをコンパイラといいますが、
コンパイル以外のビルド処理もコンパイラによって提供されるのが一般的です。

ソースファイルを機械語に変換した結果はオブジェクトファイルと呼ばれます。
コンパイルだけ実行するには `-c` を指定します。

```bash
$ g++ -std=c++11 -c main.cc
$ g++ -std=c++11 -c util.cc
```

このコマンドを実行すると
`main.cc` から `main.o`、 `util.cc` から `util.o` へコンパイルされます。

コンパイルだけでは実行ファイルは生成されません。

## リンク

オブジェクトファイルなどをまとめて実行ファイルを生成することをリンクといいます。
リンクを行うプログラムはリンカといいます。

`main.o` と `util.o` をリンクして実行ファイルを生成するには次のようにします。

```bash
$ g++ -std=c++11 main.o util.o
```

コンパイル時点ではソースファイルを1つずつ処理するため、
ソースファイル内で定義されていない関数や変数に対しては未定義であるという情報がオブジェクトファイルに格納されます。
リンクではこうした未定義の関数や変数の定義を他のオブジェクトファイルなどから探して対応付けを行います。

たとえば以下のコードの関数 `Sum` は `main.cc` ではプロトタイプ宣言だけで定義がないため、
`main.o` には `Sum` が未定義であるという情報が格納されます。

=== "main.cc"

    ```cpp
    int Sum(int a, int b);

    int main() {
        Sum(1, 2);
        return 0;
    }
    ```

=== "util.cc"

    ```cpp
    int Sum(int a, int b) {
        return a + b;
    }
    ```

`Sum` は `util.cc` で定義が行われているため、
`main.o` から実行ファイルを生成するには `util.o` が必要となります。
次のように `main.o` だけでリンクを行うと `main.o` で未定義の `Sum` を定義と対応付けできずエラーとなります。

```bash
$ g++ -std=c++11 main.o
main.o: 関数 `main' 内:
main.cc:(.text+0xf): `Sum(int, int)' に対する定義されていない参照です
collect2: error: ld returned 1 exit status
```
