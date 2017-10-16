一部のパッケージでは、Azure で実行すると pip を使用するはインストールされません可能性があります。  単にパッケージが Python Package Index を使用できないことがあります。  コンパイラが必要である可能性があります (コンパイラは Azure App service web アプリを実行しているコンピューターで使用できません)。

このセクションでは、この問題に対処する方法を紹介します。

### <a name="request-wheels"></a>要求のホイール
パッケージのインストールには、コンパイラが必要とする場合のホイールがパッケージを可能にすることを要求するパッケージの所有者に連絡してください。

最新の可用性を備えた[Python 2.7 用の Microsoft Visual C コンパイラ][Microsoft Visual C++ Compiler for Python 2.7]、Python 2.7 のネイティブ コードがあるパッケージを作成すると簡単になっています。

### <a name="build-wheels-requires-windows"></a>ホイールのビルド (Windows が必要)
メモ: このオプションを使用するときに確認をバージョンに対応するプラットフォーム/アーキテクチャ/Azure App service web アプリで使用されている Python 環境を使用してパッケージをコンパイルする (Windows/32-bit/2.7 または 3.4)。

コンパイラが必要とするため、パッケージをインストールできない場合、ローカル コンピューターで、コンパイラをインストールしをリポジトリに含めるし、パッケージのホイールをビルドできます。

Mac と Linux のユーザー: Windows コンピューターへのアクセスを持っていない場合に表示[を実行している Windows の仮想マシンを作成する][ Create a Virtual Machine Running Windows] Azure で VM を作成する方法についてです。  これを使用するには、ホイールをビルドし、リポジトリに追加、必要であれば、VM を破棄します。 

インストールできる Python 2.7 [Python 2.7 用の Microsoft Visual C コンパイラ][Microsoft Visual C++ Compiler for Python 2.7]です。

Python 3.4 をインストールする[Microsoft の Visual C 2010 Express][Microsoft Visual C++ 2010 Express]です。

ホイールをビルドするには、ホイール パッケージを必要があります。

    env\scripts\pip install wheel

使用して`pip wheel`依存関係をコンパイルします。

    env\scripts\pip wheel azure==0.8.4

これには、\wheelhouse フォルダーで .whl ファイルが作成されます。  リポジトリに \wheelhouse フォルダーとホイール ファイルを追加します。

追加する、requirements.txt を編集、`--find-links`上部にあるオプション。 これは、python パッケージ インデックスに進む前にローカル フォルダーに完全一致を検索する pip を示しています。

    --find-links wheelhouse
    azure==0.8.4

追加することによって、パッケージのインデックスを無視する pip を強制できます \wheelhouse フォルダーに含めると、すべての依存関係および python パッケージ インデックスがまったく使用されない場合は、 `--no-index` requirements.txt の最上位にします。

    --no-index

### <a name="customize-installation"></a>インストールをカスタマイズします。
インストーラーを使用して代替をなど、簡単な仮想環境にパッケージをインストールする配置スクリプトをカスタマイズする\_をインストールします。  Deploy.cmd コメント アウトされている例を参照してください。  このようなパッケージが pip がそれらをインストールしないようにする、requirements.txt に記載されていないことを確認します。

これは、配置スクリプトを追加します。

    env\scripts\easy_install somepackage

簡単に使用することもできます\_exe インストーラーからインストールするインストール (一部は、互換性のある、簡単に zip\_インストールをサポートしている)。  リポジトリにインストーラーを追加し、簡単に呼び出す\_実行可能ファイルへのパスを渡すことによってインストールされます。

これは、配置スクリプトを追加します。

    env\scripts\easy_install "%DEPLOYMENT_SOURCE%\installers\somepackage.exe"

### <a name="include-the-virtual-environment-in-the-repository-requires-windows"></a>(Windows が必要) リポジトリに仮想環境を含める
メモ: このオプションを使用するときに確認バージョンに対応するプラットフォーム/アーキテクチャ/Azure App service web アプリで使用されている仮想環境を使用する (Windows/32-bit/2.7 または 3.4)。

リポジトリに仮想環境を含める場合は、空のファイルを作成することで Azure の仮想環境の管理を行ってから配置スクリプトをできないようにすることができます。

    .skipPythonDeployment

仮想環境が自動的に管理されたときから残されたファイルを防ぐために、このアプリで既存の仮想環境を削除することをお勧めします。

[Create a Virtual Machine Running Windows]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-hero-tutorial/
[Microsoft Visual C++ Compiler for Python 2.7]: http://aka.ms/vcpython27
[Microsoft Visual C++ 2010 Express]: http://go.microsoft.com/?linkid=9709949
