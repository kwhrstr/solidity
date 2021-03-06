############
Contributing
############

貢献はいつでも歓迎します

始めるには、まず、Solidityのコンポーネントとビルドプロセスを理解するため、:ref:`building-from-source` を確認してください。
また、Solidityでスマートコントラクトを書くことに精通することも役立つでしょう。

以下の領域が特に手助けが必要です。

* ドキュメントを改善する
* `StackExchange <https://ethereum.stackexchange.com>`_ や `Solidity Gitter <https://gitter.im/ethereum/solidity>`_ に投稿されたユーザーからの質問に答える
* `Solidity's GitHub issues <https://github.com/ethereum/solidity/issues>`_ に投稿されたissueに回答し修正する。特にこのタグがついた `good first issue <https://github.com/ethereum/solidity/labels/good%20first%20issue>`_ ものは外部貢献者のための入門的なissueになります。

このプロジェクト(issues, pull requests, Gitter channels含む) は `Contributor Code of Conduct <https://raw.githubusercontent.com/ethereum/solidity/develop/CODE_OF_CONDUCT.md>`_ でリリースされていることに注意してください。
あなたはこの規約に同意することになります。

How to Report Issues
====================

issueの報告には、
`GitHub issues tracker <https://github.com/ethereum/solidity/issues>`_
を使って、以下の項目を含めるようにしてください。

* どのSolidityバージョンをつかっているのか
* どんなソースコードだったのか(可能であれば)
* どのプラットフォームで実行していたか
* 再現方法
* issueの結果はどのようなものか
* 期待する動作はどのようなものか

issueを再現するために最低限のソースコードにするのは有用で、誤解を明らかにします。

Workflow for Pull Requests
==========================

貢献するために、``develop`` ブランチをフォークし、あなたの変更をそこにいれてください。
コミットメッセージには、あなたの変更(小さな変更でなければ)の *what* に加えて *why* を詳細にいれるべきです。

もしあなたが変更を加えた後に ``develop`` からpullする必要がある場合(例えばコンフリクトのマージする)、
私たちがあなたの変更のレビューをするのを簡単にするため、
``git merge`` を使うことは避けて、``git rebase`` を使ってください。

また、追加の機能を実装している場合、適切なテストケースを ``test/`` に追加してください。(下記参照)

しかし、もし大きな変更をする場合、まず、
`Solidity Development Gitter channel
<https://gitter.im/ethereum/solidity-dev>`_
に相談してください。
(上記で参照したものとは異なり、こちらはコンパイラと言語開発用です)

新機能とバグ修正は ``Changelog.md`` に追加されるべきです。
前回のエントリーのスタイルにしたがってください。

最後に、このプロジェクトでは、`coding style
<https://github.com/ethereum/solidity/blob/develop/CODING_STYLE.md>`_
を尊重してください。
私たちはCIテストを実行してはいますが、pull requestsを送る前にはローカルでテストしビルドするようにしてください。

あなたの貢献に感謝いたします。

Running the compiler tests
==========================

``./scripts/tests.sh`` スクリプトはほとんどのSolidityテストを実行し、``aleth`` を走らせます。
``aleth`` がパスに見つかる場合は実行しますが、ダウンロードは行いませんので、最初にインストールしておく必要があります。
詳細を確認してください。

Solidityは様々なテストを含んでおり、ほとんどが ``soltest`` アプリケーションに同包されています。
それらのいくつかはテストモードで　``aleth`` クライアントを必要とし、``libz3`` を必要とするものもあります。

``aleth`` も ``libz3`` も必要としない基本的なテストセットを実行するには、
``./scripts/soltest.sh --no-ipc --no-smt`` を実行します。
このスクリプトは ``./build/test/soltest`` を内部的に走らせます。

.. note ::

    Windows環境のこれらの作業で、上記の基本的なテストセットをGit Bash上でalethやlibz3なしで実行したい場合、
    ``./build/test/Release/soltest.exe -- --no-ipc --no-smt`` このコマンドを実行する必要があるでしょう。もし標準のコマンドプロンプトで実行する場合は、``.\build\test\Release\soltest.exe -- --no-ipc --no-smt`` を実行します。

``--no-smt`` オプションは ``libz3`` を必要とするテストを無効にし、``--no-ipc`` オプションは ``aleth`` を必要とするテストを無効にします。

ipcテスト(生成されたコードのセマンティックをテストするもの)を実行したい場合、
`aleth <https://github.com/ethereum/aleth/releases/download/v1.5.0-alpha.7/aleth-1.5.0-alpha.7-linux-x86_64.tar.gz>`_
をインストールします。
その後、テストモードで実行します。
``aleth --db memorydb --test -d /tmp/testeth``

実際のテストを実行するには、``./scripts/soltest.sh --ipcpath /tmp/testeth/geth.ipc`` を使います。

テストの一部を実行する場合は、フィルタを使います。
``./scripts/soltest.sh -t TestSuite/TestName --ipcpath /tmp/testeth/geth.ipc``
``TestName`` はワイルドカードを指定できます ``*``。

例えば、実行したテストがあるとして、
``./scripts/soltest.sh -t "yulOptimizerTests/disambiguator/*" --no-ipc --no-smt``
を実行すると、disambiguatorの全てのテストが実行されます。

全てテストを確認するには、
``./build/test/soltest --list_content=HRF -- --ipcpath /tmp/irrelevant``
を使います。

もしGDBでデバッグしたい場合は、通常とは異なる方法でビルドしていることが必要です。
以下のコマンドを ``build`` フォルダで実行します。

::

   cmake -DCMAKE_BUILD_TYPE=Debug ..
   make


これは、あなたが ``--debug`` フラグを使いテストをデバッグする際のシンボルを生成します。
これで、関数や変数をbreakしたりprintで表示したりすることができるようになります。

``./scripts/tests.sh`` スクリプトも、``soltest`` で見つかったテストに加えて、コマンドラインテストを実行しテストをコンパイルします。

CIはEmscriptenのコンパイルを必要とする追加のテスト( ``solc-js`` やサードパーティのSolidityフレームワークのテストを含みます)を実行します。

.. note ::

    いくつかの ``aleth`` のバージョンはテストに使うことはできません。
    SolidityのCIで使っているものと同じバージョンを使うことを推奨します。
    現在CIはバージョン ``aleth`` の ``1.5.0-alpha.7`` を使っています。

Writing and running syntax tests
--------------------------------

構文テストは、コンパイラが無効なコードに正しいエラーメッセージを生成し、適切に有効なコードを受け入れることをチェックします。
それらは ``tests/libsolidity/syntaxTests`` フォルダ内へ個別のファイルに格納されます。
それらのファイルは、個別のテストケースの正しい結果とアノテーションを含んでいます。
テストスイートは、正しい結果に対してチェックしコンパイルします。

例えば、``./test/libsolidity/syntaxTests/double_stateVariable_declaration.sol`` では、

::

    contract test {
        uint256 variable;
        uint128 variable;
    }
    // ----
    // DeclarationError: (36-52): Identifier already declared.

構文テストは、少なくともセパレータ ``// ----`` に続くテスト自身のコントラクトを含まなければいけません。
セパレータに続くコメントは、正しいコンパイラエラーやワーニングを記述するのに使われます。
数字の範囲は、エラーが発生したソースコードの場所を指定しています。
コントラクトにエラーやワーニングなしでコンパイルしたい場合、セパレータとコメントを削除することができます。

上記の例だと、``variable`` 変数は２度宣言されてます。
これは、すでに宣言されていますという識別子の ``DeclarationError`` となります。

``isoltest`` というツールはこれらのテストに使え、``./build/test/tools/`` にあります。
このツールは、対話的に、お好みのエディタを使って、失敗しているコントラクトを編集することができます。
``variable`` を削除して、このテストを突破してみましょう！

::

    contract test {
        uint256 variable;
    }
    // ----
    // DeclarationError: (36-52): Identifier already declared.

再度 ``./build/test/isoltest`` を実行すると失敗します。

::

    syntaxTests/double_stateVariable_declaration.sol: FAIL
        Contract:
            contract test {
                uint256 variable;
            }

        Expected result:
            DeclarationError: (36-52): Identifier already declared.
        Obtained result:
            Success

``isoltest`` は得られた結果の次に期待する結果を表示します。また、現在のコントラクトの編集や更新、スキップする方法や、アプリケーションを終了する方法など方法も提供します。

失敗してるテストのためにいくつかの選択肢を提供します。

- ``edit``: ``isoltest`` はエディタでコントラクトを開こうとし、修正できるます。``isoltest --editor /path/to/editor`` としてコマンドラインで与えられたエディタか、``EDITOR`` 環境変数で設定されいてるものか ``/usr/bin/editor`` を使います。(この順番で設定されます).
- ``update``: テストのコントラクトの期待値を更新します。満たされていない期待値を取り除き、不足している期待値を追加することアノテーションを更新します。このテストは再度実行されます。
- ``skip``: 特定のテストの実行をスキップします。
- ``quit``: ``isoltest`` を終了します。

全てのテスト実行を終了する ``quit`` をのぞいて全てのオプションは現在のコントラクトへ適用します。

自動的、上記のテストは以下に変更され、

::

    contract test {
        uint256 variable;
    }
    // ----

再実行され、テストはパスします。

::

    Re-running test case...
    syntaxTests/double_stateVariable_declaration.sol: OK


.. note::
    そのテストを説明するコントラクトファイルの名前を選んでみてください。例えば、``double_variable_declaration.sol`` などです。
    継承のテストやクロスコントラクトのテスト以外で、1つのファイルに複数のコントラクトを配置していはいけません。
    各ファイルは新しい機能の側面のみテストすべきです。


Running the Fuzzer via AFL
==========================
Fuzzingは例外的な状態(segmentation faults, exceptions, etc)を見つけるための、ランダムインプットでプログラムを実行させるテクニックです。

現代のfuzzersはかしこく、インプットの中で有向探索を実行します。
インプットとして、ソースコードをとる ``solfuzzer`` と呼ばれる特別なバイナリがあります。
内部コンパイラエラー、やsegmentation faultや類似のエラーが発生した時は失敗しますが、
コードにエラーが含まれていても失敗しません。こうして、fuzzingツールはコンパイラの中の内部的な問題を見つけることができます。

私たちは主に、`AFL <http://lcamtuf.coredump.cx/afl/>` をfuzzingに使います。リポジトリ(afl, afl-clang)からAFLパッケージをダウンロードしインストールか、手動でビルドする必要があります。
次に、自分用のコンパイラとしてAFLとSolidityをビルドします（もしくは、単に ``solfuzzer`` バイナリを使います）。

::

    cd build
    # if needed
    make clean
    cmake .. -DCMAKE_C_COMPILER=path/to/afl-gcc -DCMAKE_CXX_COMPILER=path/to/afl-g++
    make solfuzzer

この段階では以下のようなメッセージを見ることになるでしょう。

::

    Scanning dependencies of target solfuzzer
    [ 98%] Building CXX object test/tools/CMakeFiles/solfuzzer.dir/fuzzer.cpp.o
    afl-cc 2.52b by <lcamtuf@google.com>
    afl-as 2.52b by <lcamtuf@google.com>
    [+] Instrumented 1949 locations (64-bit, non-hardened mode, ratio 100%).
    [100%] Linking CXX executable solfuzzer

このようなメッセージ表示されない場合、cmakeフラグをAFLのclangバイナリに向けるようにしてみてください。

::

    # if previously failed
    make clean
    cmake .. -DCMAKE_C_COMPILER=path/to/afl-clang -DCMAKE_CXX_COMPILER=path/to/afl-clang++
    make solfuzzer

そうしなければ、直ちにバイナリはインスツルメントされてないというエラーと共にfuzzerは停止します。

::

    afl-fuzz 2.52b by <lcamtuf@google.com>
    ... (truncated messages)
    [*] Validating target binary...

    [-] Looks like the target binary is not instrumented! The fuzzer depends on
        compile-time instrumentation to isolate interesting test cases while
        mutating the input data. For more information, and for tips on how to
        instrument binaries, please see /usr/share/doc/afl-doc/docs/README.

        When source code is not available, you may be able to leverage QEMU
        mode support. Consult the README for tips on how to enable this.
        (It is also possible to use afl-fuzz as a traditional, "dumb" fuzzer.
        For that, you can use the -n option - but expect much worse results.)

    [-] PROGRAM ABORT : No instrumentation detected
             Location : check_binary(), afl-fuzz.c:6920

次に、サンプルソースファイルで、それはfuzzerに簡単にエラーを見つけられるようする実例のソースコードが必要です。
あなたは、文法テストからファイルをコピーするか、ドキュメントや他のテストからテストファイルを抽出することができます。

::

    mkdir /tmp/test_cases
    cd /tmp/test_cases
    # extract from tests:
    path/to/solidity/scripts/isolate_tests.py path/to/solidity/test/libsolidity/SolidityEndToEndTest.cpp
    # extract from documentation:
    path/to/solidity/scripts/isolate_tests.py path/to/solidity/docs docs

AFLドキュメントはコーパス（初期のインプットファイル）が大きすぎないようにしています。これらのファイル自身は1 kBを超えず、機能あたり多くても１ファイルであるべきです。
``afl-cmin`` というインプットファイルをバイナリと似た動作に整えるツールもあります。

fuzzerを実行してください( ``-m`` はメモリサイズを60 MBへ拡張します):

::

    afl-fuzz -m 60 -i /tmp/test_cases -o /tmp/fuzzer_reports -- /path/to/solfuzzer

fuzzerは ``/tmp/fuzzer_reports`` に失敗するソースファイルを生成します。
同じエラーを発生させる似たソースファイルがあることもありますが、``scripts/uniqueErrors.sh`` を使うとユニークなエラーのみ絞り込むことができます。

Whiskers
========

*Whiskers* は `Mustache <https://mustache.github.io>` と似た文字型テンプレートシステムです。
コードの保守性や検証可能性、可読性のため、様々な場所でコンパイラが使います。

シンタックスはMustacheと大きく異なり、``{{`` と ``}}`` は　``<`` と ``>`` に置き換わっています。
それはパースしやすくするのと、:ref:`inline-assembly` とコンフリクトするのを避けるためです(インラインアセンブラでは ``<`` と ``>`` は不正ですが、``{`` と ``}`` はブロックを区切るのに使います)
リストは１階層のみ処理され再帰しない、という制限がありますが、将来は変更されるでしょう。

A rough specification is the following:
大まかな仕様として以下があります。

``<name>`` は全て文字型の ``name`` にエスケープや繰り返しの置換することなしに置き換われます。
領域は ``<#name>...</name>`` で区切ることができます。テンプレートシステムへ与えられた変数のセットとして、内容を連結したものに置きかわりますし、
``<inner>`` は毎回、それぞれの値で置き換わります。
トップレベルの変数はその領域内部でも使うことができます。
