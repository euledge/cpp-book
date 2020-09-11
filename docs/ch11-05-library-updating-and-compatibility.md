# ライブラリ更新と互換性

!!! info "説明に使用する環境"
    Windows の MSYS2 環境は構成が複雑なため、説明では Linux 環境を使用します。

ライブラリの更新があった場合に、
ライブラリを使用するプログラムで必要な作業は以下の項目で決まります。

- 静的ライブラリと動的ライブラリのどちらであるか
- 更新前後での API 互換性
- 更新前後での ABI 互換性

![ライブラリ更新で必要な手順を表した図][library_updating]
[library_updating]: img/library_updating.svg

## API 互換性

API はアプリケーションプログラミングインタフェースの略で、
ソースコードレベルでのインターフェースのことをいいます。
更新前のライブラリに存在する API を
更新後のライブラリでそのまま使用できる場合に API 互換性があるといいます。

API 互換性があれば
ライブラリを使用するプログラムのコード修正は必要ありません。

API の変化を伴わない以下のような変更は API 互換性があります。

- コメントの変更 (誤字修正など)
- ソースファイルに記述されている実装の変更 (リファクタリングやバグ修正など)

API の変化を伴う変更は、
次のように API 互換性のあるものとないものがあります。

- API 互換性のある変更
    - API の新規追加
    - 更新前の仕様を維持して API を変更 (拡張)
- API 互換性のない変更
    - API の削除
    - 更新前の仕様を維持せず API を変更

## 静的ライブラリと動的ライブラリ

!!! info "静的ライブラリ"
    ![静的ライブラリ][static_library]

静的ライブラリはライブラリを使用するプログラムに取り込まれるため、
ライブラリを使用するプログラムの再ビルドが必要です。

!!! info "動的ライブラリ"
    ![動的ライブラリ][dynamic_library]

動的ライブラリはライブラリを使用するプログラムに取り込まれないため、
ライブラリを使用するプログラムの再ビルドは不要です。
ただし、後述の ABI 互換性がない場合には再ビルドが必要となります。

[static_library]: img/static_library.svg
[dynamic_library]: img/dynamic_library.svg

## ABI 互換性

ABI はアプリケーションバイナリインタフェースの略で、
バイナリレベルでのインターフェースのことをいいます。
更新前のライブラリに存在する ABI を
更新後のライブラリでそのまま使用できる場合に ABI 互換性があるといいます。

ABI 互換性があれば
実行環境の動的ライブラリを変更するだけで動作します。

通常は
API の変更がなければ ABI 互換性があり、
API 互換性がなければ ABI 互換性はありません。

??? question "ABI のコンパイラ依存"
    ABI はコンパイラ依存です。

    同じコンパイラであってもバージョンが異なれば ABI 互換性はないことがあります。
    たとえば GCC 4.x と GCC 5.x ではデフォルトの ABI は互換性がありません。
    GCC 5.x では `_GLIBCXX_USE_CXX11_ABI` マクロを `0` に定義することで
    GCC 4.x と ABI 互換性があるようにビルドすることができます。
    詳細は [Dual ABI - The GNU C++ Library][gcc_dual_abi] を参照してください。

    一方で、異なるコンパイラ間でも ABI 互換性がある場合もあります。
    たとえば Clang でビルドしたライブラリを GCC で使用できることがあります。

[gcc_dual_abi]: https://gcc.gnu.org/onlinedocs/libstdc++/manual/using_dual_abi.html

API 互換性のある API の変更は、
次のように ABI 互換性のあるものとないものがあります。

- ABI 互換性のある変更
    - クラスに属さない関数や変数の追加
- ABI 互換性のない変更
    - クラスにメンバ変数を追加 (オブジェクトのサイズ変更)
    - クラスのメンバ変数の宣言順序を変更 (オブジェクトのメモリ配置変更)
    - 仮想関数の追加 (vtable のサイズ変更)

詳細は
[Policies/Binary Compatibility Issues With C++ - KDE Community Wiki][kde_abi]
を参照してください。

[kde_abi]: https://community.kde.org/Policies/Binary_Compatibility_Issues_With_C%2B%2B