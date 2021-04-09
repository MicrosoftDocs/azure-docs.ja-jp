---
title: Azure で機械学習に Python と TensorFlow を使用する
description: 機械学習モデルと共に Python、TensorFlow、Azure Functions を使用して、内容に基づいて画像を分類します。
author: anthonychu
ms.topic: tutorial
ms.date: 01/15/2020
ms.author: antchu
ms.custom: mvc, devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: ca9ce27583168dfee1a597fce559afad38a3a8c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994606"
---
# <a name="tutorial-apply-machine-learning-models-in-azure-functions-with-python-and-tensorflow"></a>チュートリアル:Python および TensorFlow を使用して Azure Functions で機械学習モデルを適用する

この記事では、機械学習モデルと共に Python、TensorFlow、Azure Functions を使用して、内容に基づいて画像を分類する方法について学習します。 すべての作業をローカルで行い、クラウドで Azure リソースを作成しないため、このチュートリアルを完了するのにコストは一切かかりません。

> [!div class="checklist"]
> * Python で Azure Functions を開発するためにローカル環境を初期化する。
> * カスタムの TensorFlow 機械学習モデルを関数アプリにインポートする。
> * 犬または猫が含まれる画像を分類するためのサーバーレス HTTP API を構築する。
> * Web アプリから API を使用する。

## <a name="prerequisites"></a>前提条件 

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Python 3.7.4](https://www.python.org/downloads/release/python-374/) (Python 3.7.4 および Python 3.6. x は Azure Functions で確認されています。Python 3.8 以降のバージョンは、まだサポートされていません)。
- [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)
- コード エディター ([Visual Studio Code](https://code.visualstudio.com/) など)

### <a name="prerequisite-check"></a>前提条件のチェック

1. ターミナルまたはコマンド ウィンドウで `func --version` を実行して、Azure Functions Core Tools のバージョンが 2.7.1846 以降であることを確認します。
1. `python --version` (Linux と macOS の場合) または `py --version` (Windows の場合) を実行して、Python のバージョンが 3.7. x であることを確認します。

## <a name="clone-the-tutorial-repository"></a>チュートリアル リポジトリをクローンする

1. ターミナルまたはコマンド ウィンドウで、Git を使用して次のリポジトリをクローンします。

    ```
    git clone https://github.com/Azure-Samples/functions-python-tensorflow-tutorial.git
    ```

1. フォルダーに移動し、その内容を確認します。

    ```
    cd functions-python-tensorflow-tutorial
    ```

    - *start* はチュートリアル用の作業フォルダーです。
    - *end* は、参照用の最終結果と完全な実装です。
    - *resources* には、機械学習モデルとヘルパー ライブラリが含まれています。
    - *frontend* は、関数アプリを呼び出す Web サイトです。
    
## <a name="create-and-activate-a-python-virtual-environment"></a>Python 仮想環境を作成してアクティブ化する

*start* フォルダーに移動して、次のコマンドを実行し、`.venv` という名前の仮想環境を作成してアクティブにします。 必ず、Azure Functions でサポートされている Python 3.7 を使用してください。


# <a name="bash"></a>[bash](#tab/bash)

```bash
cd start
```

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

お使いの Linux ディストリビューションに Python の venv パッケージがインストールされていなかった場合は、次のコマンドを実行します。

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
cd start
```

```powershell
py -3.7 -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd start
```

```cmd
py -3.7 -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

以降のコマンドはすべて、このアクティブ化された仮想環境で実行します (仮想環境を終了するには、`deactivate` を実行します)。


## <a name="create-a-local-functions-project"></a>ローカル関数プロジェクトを作成する

Azure Functions における関数プロジェクトとは、それぞれが特定のトリガーに応答する個別の関数を 1 つまたは複数含んだコンテナーです。 プロジェクト内のすべての関数は、同じローカル構成とホスティング構成を共有します。 このセクションでは、HTTP エンドポイントを提供する `classify` という名前の 1 つの定型関数が含まれる関数プロジェクトを作成します。 後のセクションで、より具体的なコードを追加します。

1. *start* フォルダーで、Azure Functions Core Tools を使用して Python 関数アプリを初期化します。

    ```
    func init --worker-runtime python
    ```

    初期化後、*start* フォルダーにはプロジェクト用の各種ファイルが格納されます。たとえば、[local.settings.json](functions-run-local.md#local-settings-file) や [host.json](functions-host-json.md) といった名前の構成ファイルです。 *local.settings.json* には Azure からダウンロードしたシークレットを含めることができるため、このファイルは既定で *.gitignore* ファイルによってソース管理から除外されます。

    > [!TIP]
    > 関数プロジェクトは特定のランタイムに関連付けられているので、プロジェクト内の関数はすべて同じ言語で記述する必要があります。

1. 次のコマンドを使用して、関数を自分のプロジェクトに追加します。ここで、`--name` 引数は関数の一意の名前で、`--template` 引数は関数のトリガーを指定するものです。 `func new` によって、関数と同じ名前のサブフォルダーが作成されます。ここには、プロジェクト用に選択した言語に適したコード ファイルと、*function.json* という名前の構成ファイルが含まれます。

    ```
    func new --name classify --template "HTTP trigger"
    ```

    このコマンドによって、関数の名前 (*classify*) と同じ名前のフォルダーが作成されます。 このフォルダーには 2 つのファイルが格納されています。1 つは関数コードが含まれている *\_\_init\_\_.py* で、もう 1 つは関数のトリガーとその入出力バインドを記述した *function.json* です。 これらのファイルのコンテンツの詳細については、Python クイックスタートの「[ファイルの内容を確認する](./create-first-function-cli-python.md#optional-examine-the-file-contents)」を参照してください。


## <a name="run-the-function-locally"></a>関数をローカルで実行する

1. *start* フォルダーで、ローカルの Azure Functions ランタイム ホストを起動して、関数を開始します。

    ```
    func start
    ```
    
1. 出力に `classify` エンドポイントが表示されるのを確認したら、URL ```http://localhost:7071/api/classify?name=Azure``` に移動します。 "Hello Azure!" というメッセージが、 出力として表示されます。

1. **Ctrl** - **C** キーを使用してホストを停止します。


## <a name="import-the-tensorflow-model-and-add-helper-code"></a>TensorFlow モデルをインポートしてヘルパー コードを追加する

内容に基づいて画像を分類するように `classify` 関数を変更するには、Azure Custom Vision Service でトレーニングされ、このサービスからエクスポートされた、既製の TensorFlow モデルを使用します。 先ほどクローンしたサンプルの *resources* フォルダーに格納されているこのモデルは、犬と猫のどちらが含まれているのかに基づいて画像を分類します。 次に、いくつかのヘルパー コードと依存関係を自分のプロジェクトに追加します。

Free レベルの Custom Vision Service を使用して独自のモデルを作成する場合は、[サンプル プロジェクト リポジトリ](https://github.com/Azure-Samples/functions-python-tensorflow-tutorial/blob/master/train-custom-vision-model.md)の手順に従ってください。

> [!TIP]
> 関数アプリには一切依存せずに TensorFlow モデルをホストしたい場合は、対象のモデルがあるファイル共有を Linux 関数アプリにマウントすることができます。 詳細については、「[Azure CLI を使用してファイル共有を Python 関数アプリにマウントする](./scripts/functions-cli-mount-files-storage-linux.md)」を参照してください。

1. *start* フォルダーで次のコマンドを実行して、モデル ファイルを *classify* フォルダーにコピーします。 コマンドには、必ず `\*` を含めてください。 

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    cp ../resources/model/* classify
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    copy ..\resources\model\* classify
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    copy ..\resources\model\* classify
    ```
    
    ---
    
1. *classify* フォルダーに *model.pb* と *labels.txt* という名前のファイルが含まれていることを確認します。 含まれていない場合は、*start* フォルダーでコマンドを実行したことを確認してください。

1. *start* フォルダーで次のコマンドを実行して、ヘルパー コードが含まれるファイルを *classify* フォルダーにコピーします。

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    cp ../resources/predict.py classify
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    copy ..\resources\predict.py classify
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    copy ..\resources\predict.py classify
    ```
    
    ---

1. *classify* フォルダーに *predict.py* という名前のファイルが含まれていることを確認します。

1. テキスト エディターで *start/requirements.txt* を開き、ヘルパー コードで必要とされる次の依存関係を追加します。

    ```txt
    tensorflow==1.14
    Pillow
    requests
    ```
    
1. *requirements.txt* を保存します。

1. *start* フォルダーで次のコマンドを実行して、依存関係をインストールします。 インストールには数分かかる場合があります。その間に、次のセクションに進んで関数の変更を行うことができます。

    ```
    pip install --no-cache-dir -r requirements.txt
    ```
    
    Windows では、"Could not install packages due to an EnvironmentError: [Errno 2] No such file or directory: (EnvironmentError: [Errno 2] が原因でパッケージをインストールできませんでした。ファイルまたはディレクトリがありません:)" の後に、*sharded_mutable_dense_hashtable.cpython-37.pyc* のようなファイルへの長いパス名が続くエラーが表示されることがあります。 通常、このエラーはフォルダー パスが長くなりすぎることが原因で発生します。 この場合は、レジストリ キー `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem@LongPathsEnabled` を `1` に設定して、長いパスを有効にします。 または、Python インタープリターがインストールされている場所を確認します。 その場所へのパスが長い場合は、短いパスのフォルダーに再インストールしてみてください。

> [!TIP]
> 最初の予測を行うために *predict.py* を呼び出すと、`_initialize` という名前の関数によって TensorFlow モデルがディスクから読み込まれ、グローバル変数内にキャッシュされます。 このキャッシュにより、後続の予測が高速化されます。 グローバル変数の使用法の詳細については、[Azure Functions の Python 開発者向けガイド](functions-reference-python.md#global-variables)を参照してください。

## <a name="update-the-function-to-run-predictions"></a>予測を実行するよう関数を更新する

1. テキスト エディターで *classify/\_\_init\_\_.py* を開き、既存の `import` ステートメントの後に次の行を追加して、標準の JSON ライブラリと *predict* ヘルパーをインポートします。

    :::code language="python" source="~/functions-python-tensorflow-tutorial/end/classify/__init__.py" range="1-6" highlight="5-6":::

1. `main` 関数の内容全体を次のコードで置き換えます。

    :::code language="python" source="~/functions-python-tensorflow-tutorial/end/classify/__init__.py" range="8-19":::

    この関数は、`img` という名前のクエリ文字列パラメーターで画像 URL を受け取ります。 次に、ヘルパー ライブラリにある `predict_image_from_url` を呼び出し、TensorFlow モデルを使用して画像をダウンロードし、分類します。 次に、この関数は、結果と共に HTTP 応答を返します。 

    > [!IMPORTANT]
    > この HTTP エンドポイントは、別のドメインでホストされている Web ページによって呼び出されるため、応答には、ブラウザーのクロス オリジン リソース共有 (CORS) 要件を満たすための `Access-Control-Allow-Origin` ヘッダーが含まれます。
    >
    > 実稼働アプリケーションでは、セキュリティを強化するために、`*` を Web ページの特定のオリジンに変更します。

1. 変更内容を保存し、依存関係のインストールが完了していることを前提に、`func start` を使用してローカル関数ホストを再度起動します。 必ず、仮想環境をアクティブにして *start* フォルダーでホストを実行します。 そうしないと、ホストは起動しますが、関数の呼び出し時にエラーが発生します。

    ```
    func start
    ```

1. ブラウザーで次の URL を開き、猫画像の URL を使用して関数を呼び出して、返された JSON で画像が猫として分類されることを確認します。

    ```
    http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png
    ```
    
1. 次の手順で使用するので、ホストは実行したままにしておいてください。 

### <a name="run-the-local-web-app-front-end-to-test-the-function"></a>ローカル Web アプリのフロントエンドを実行して関数をテストする

別の Web アプリから関数エンドポイントの呼び出しをテストするために、リポジトリの *frontend* フォルダーに簡単なアプリが用意されています。

1. 新しいターミナルまたはコマンド プロンプトを開き、仮想環境をアクティブにします (前の「[Python 仮想環境を作成してアクティブ化する](#create-and-activate-a-python-virtual-environment)」にある説明を参照)。

1. リポジトリの *frontend* フォルダーに移動します。

1. Python で HTTP サーバーを起動します。

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash 
    python -m http.server
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    py -m http.server
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    py -m http.server
    ```

1. ブラウザーで `localhost:8000` に移動し、次の写真の URL のいずれかをテキスト ボックスに入力するか、パブリックにアクセスできる画像の URL を使用します。

    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat2.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog1.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog2.png`
    
1. **[Submit]\(送信\)** を選択して関数エンドポイントを呼び出し、画像を分類します。

    ![完成したプロジェクトのスクリーンショット](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png)

    画像の URL を送信したときにブラウザーからエラーがレポートされる場合は、関数アプリを実行しているターミナルを確認します。 "No module found 'PIL' (モジュールが見つかりません 'PIL')" のようなエラーが表示される場合は、前に作成した仮想環境をアクティブにする前に、*start* フォルダーで関数アプリを起動した可能性があります。 引き続きエラーが発生する場合は、仮想環境をアクティブにして `pip install -r requirements.txt` を再度実行し、エラーを探します。

> [!NOTE]
> このモデルでは、画像の内容が常に猫または犬に分類されます。画像に両方含まれている場合やどちらも含まれていない場合は、既定で犬に分類されます。 たとえば、虎や豹の画像であれば通常猫に分類されますが、象、人参、または飛行機の画像は犬に分類されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルの全作業は、お使いのマシン上でローカルに実行されるため、クリーンアップする Azure リソースやサービスはありません。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Functions を使用して HTTP API エンドポイントをビルドしてカスタマイズし、TensorFlow モデルを使用して画像を分類する方法について学習しました。 また、Web アプリから API を呼び出す方法についても学習しました。 このチュートリアルの手法を使用することで、Azure Functions のサーバーレス コンピューティング モデルで API を実行しながら、複雑な API もビルドできます。

> [!div class="nextstepaction"]
> [Azure CLI ガイドを使用して関数を Azure Functions にデプロイする](./functions-run-local.md#publish)

関連項目:

- [Visual Studio Code を使用して関数を Azure にデプロイする](https://code.visualstudio.com/docs/python/tutorial-azure-functions)。
- [Azure Functions の Python 開発者向けガイド](./functions-reference-python.md)
- [Azure CLI を使用してファイル共有を Python 関数アプリにマウントする](./scripts/functions-cli-mount-files-storage-linux.md)
