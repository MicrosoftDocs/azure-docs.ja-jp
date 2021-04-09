---
title: Azure Functions での Python Function App のトラブルシューティング
description: Python 関数のトラブルシューティングの方法について説明します。
author: Hazhzeng
ms.topic: article
ms.date: 07/29/2020
ms.author: hazeng
ms.custom: devx-track-python
ms.openlocfilehash: 9b9f5d389eda5d74e7e78cfcfa9a46fba7276cbd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "87846039"
---
# <a name="troubleshoot-python-errors-in-azure-functions"></a>Azure Functions での Python エラーのトラブルシューティング

Python 関数の一般的な問題に関するトラブルシューティング ガイドの一覧を次に示します。

* [ModuleNotFoundError および ImportError](#troubleshoot-modulenotfounderror)
* ["cygrpc" をインポートできない](#troubleshoot-cannot-import-cygrpc)

## <a name="troubleshoot-modulenotfounderror"></a>ModuleNotFoundError のトラブルシューティング

このセクションは、Python Function App でのモジュール関連のエラーのトラブルシューティングに役立ちます。 これらのエラーでは、通常、次のような Azure Functions エラー メッセージが示されます。

> `Exception: ModuleNotFoundError: No module named 'module_name'.`

このエラーの問題は、Python Function App で Python モジュールの読み込みに失敗した場合に発生します。 このエラーの根本原因は、次の問題のいずれかです。

- [パッケージが見つからない](#the-package-cant-be-found)
- [パッケージが適切な Linux ホイールで解決されない](#the-package-isnt-resolved-with-proper-linux-wheel)
- [パッケージが Python インタープリター バージョンと互換性がない](#the-package-is-incompatible-with-the-python-interpreter-version)
- [パッケージが他のパッケージと競合している](#the-package-conflicts-with-other-packages)
- [パッケージで、Windows または macOS プラットフォームのみがサポートされている](#the-package-only-supports-windows-or-macos-platforms)

### <a name="view-project-files"></a>プロジェクト ファイルを表示する

問題の実際の原因を特定するには、Function App で実行される Python プロジェクト ファイルを取得する必要があります。 ローカル コンピューターにプロジェクト ファイルがない場合は、次のいずれかの方法で取得できます。

- Function App のアプリ設定が `WEBSITE_RUN_FROM_PACKAGE` で、その値が URL の場合は、URL をコピーしてブラウザーに貼り付け、ファイルをダウンロードします。
- Function App に `WEBSITE_RUN_FROM_PACKAGE` があり、それが `1` に設定されている場合は、`https://<app-name>.scm.azurewebsites.net/api/vfs/data/SitePackages` に移動し、最新の `href` URL からファイルをダウンロードします。
- Function App に前述のアプリ設定がない場合は、`https://<app-name>.scm.azurewebsites.net/api/settings` に移動し、`SCM_RUN_FROM_PACKAGE` の下の URL を見つけます。 URL をコピーし、ブラウザーに貼り付けて、ファイルをダウンロードします。
- これらのいずれもうまくいかない場合は、`https://<app-name>.scm.azurewebsites.net/DebugConsole` に移動し、`/home/site/wwwroot` の下のコンテンツを表示します。

この記事の残りの部分は、Function App のコンテンツを検査し、根本原因を特定し、特定の問題を解決することによって、このエラーの考えられる原因をトラブルシューティングするのに役立ちます。

### <a name="diagnose-modulenotfounderror"></a>ModuleNotFoundError を診断する

このセクションでは、モジュール関連のエラーの潜在的な根本原因について詳しく説明します。 考えられる根本原因を解明した後、関連する軽減策に進むことができます。

#### <a name="the-package-cant-be-found"></a>パッケージが見つからない

`.python_packages/lib/python3.6/site-packages/<package-name>` または `.python_packages/lib/site-packages/<package-name>` を参照します。 ファイル パスが存在しない場合は、この欠落したパスが根本原因である可能性があります。

デプロイ中にサードパーティまたは古いツールを使用すると、この問題が発生する可能性があります。

軽減策については、「[リモート ビルドを有効にする](#enable-remote-build)」または「[ネイティブの依存関係を構築する](#build-native-dependencies)」を参照してください。

#### <a name="the-package-isnt-resolved-with-proper-linux-wheel"></a>パッケージが適切な Linux ホイールで解決されない

`.python_packages/lib/python3.6/site-packages/<package-name>-<version>-dist-info` または `.python_packages/lib/site-packages/<package-name>-<version>-dist-info` に移動します。 任意のテキスト エディターを使用して、**wheel** ファイルを開き、**Tag:** セクションを確認します。 タグの値に **linux** が含まれていない場合、これが問題である可能性があります。

Python 関数は、Azure の Linux でのみ実行されます。関数ランタイム v2.x は Debian Stretch で、v3.x ランタイムは Debian Buster で実行されます。 成果物には、正しい Linux バイナリを含める必要があります。 Core Tools、サードパーティ、または古いツールで `--build local` フラグを使用すると、古いバイナリが使用される可能性があります。

軽減策については、「[リモート ビルドを有効にする](#enable-remote-build)」または「[ネイティブの依存関係を構築する](#build-native-dependencies)」を参照してください。

#### <a name="the-package-is-incompatible-with-the-python-interpreter-version"></a>パッケージが Python インタープリター バージョンと互換性がない

`.python_packages/lib/python3.6/site-packages/<package-name>-<version>-dist-info` または `.python_packages/lib/site-packages/<package-name>-<version>-dist-info` に移動します。 テキスト エディターを使用して、METADATA ファイルを開き、**Classifiers:** セクションを確認します。 セクションに `Python :: 3`、`Python :: 3.6`、`Python :: 3.7`、または `Python :: 3.8` が含まれていない場合、パッケージのバージョンが古すぎるか、ほとんどの場合、パッケージが既にメンテナンスの対象外となっていることを意味します。

ご利用の Function App の Python バージョンは、[Azure portal](https://portal.azure.com) から確認できます。 Function App に移動し、 **[リソース エクスプローラー]** を選び、 **[移動]** を選択します。

:::image type="content" source="media/recover-module-not-found/resource-explorer.png" alt-text="ポータルで Function App のリソース エクスプローラーを開く":::

エクスプローラーが読み込まれた後に、**LinuxFxVersion** を検索します。これは、Python のバージョンを示しています。

軽減策については、「[パッケージを最新バージョンに更新する](#update-your-package-to-the-latest-version)」または「[パッケージを同等のものに置き換える](#replace-the-package-with-equivalents)」を参照してください。

#### <a name="the-package-conflicts-with-other-packages"></a>パッケージが他のパッケージと競合している

パッケージが適切な Linux ホイールで正しく解決されていることを確認した場合は、他のパッケージと競合している可能性があります。 特定のパッケージでは、PyPi ドキュメントで互換性のないモジュールが明確になる場合があります。 たとえば、[`azure 4.0.0`](https://pypi.org/project/azure/4.0.0/) には、次のようなステートメントがあります。

<pre>This package isn't compatible with azure-storage.
If you installed azure-storage, or if you installed azure 1.x/2.x and didn’t uninstall azure-storage,
you must uninstall azure-storage first.</pre>

ご利用のパッケージ バージョンのドキュメントは、`https://pypi.org/project/<package-name>/<package-version>` で確認できます。

軽減策については、「[パッケージを最新バージョンに更新する](#update-your-package-to-the-latest-version)」または「[パッケージを同等のものに置き換える](#replace-the-package-with-equivalents)」を参照してください。

#### <a name="the-package-only-supports-windows-or-macos-platforms"></a>パッケージで、Windows または macOS プラットフォームのみがサポートされている

テキスト エディターを使用して `requirements.txt` を開き、`https://pypi.org/project/<package-name>` でパッケージを確認します。 一部のパッケージは、Windows または macOS プラットフォームでのみ実行されます。 たとえば、pywin32 は Windows 上でのみ実行されます。

ローカル開発に Windows または macOS を使用している場合は、`Module Not Found` エラーが発生しない可能性があります。 しかし、実行時に Linux を使用する、Azure Functions にパッケージをインポートすることはできません。 これは、プロジェクトの初期化中に Windows または macOS マシンから requirements.txt に仮想環境をエクスポートするために、`pip freeze` を使用することで発生する可能性があります。

軽減策については、「[パッケージを同等のものに置き換える](#replace-the-package-with-equivalents)」または「[requirements.txt を手動で作成する](#handcraft-requirementstxt)」を参照してください。

### <a name="mitigate-modulenotfounderror"></a>ModuleNotFoundError を軽減する

モジュール関連の問題の軽減策として考えられるものを以下に示します。 [上記の診断方法](#diagnose-modulenotfounderror)を使用して、これらの軽減策のうち、どれを試すかを決めます。

#### <a name="enable-remote-build"></a>リモート ビルドを有効にする

リモート ビルドが有効になっていることを確認します。 これを行う方法は、デプロイ方法によって異なります。

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/vscode)
最新バージョンの [Visual Studio Code 用の Azure Functions 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)がインストールされていることを確認してください。 `.vscode/settings.json` が存在し、それに `"azureFunctions.scmDoBuildDuringDeployment": true` 設定が含まれていることを確認します。 そうでない場合は、`azureFunctions.scmDoBuildDuringDeployment` 設定を有効にした状態でこのファイルを作成し、プロジェクトを再デプロイしてください。

## <a name="azure-functions-core-tools"></a>[Azure Functions Core Tools](#tab/coretools)

最新バージョンの [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools/releases) がインストールされていることを確認してください。 ローカル関数プロジェクト フォルダーに移動し、デプロイに `func azure functionapp publish <app-name>` を使用します。

## <a name="manual-publishing"></a>[手動による発行](#tab/manual)

`https://<app-name>.scm.azurewebsites.net/api/zipdeploy` エンドポイントにパッケージを手動で発行する場合は、**SCM_DO_BUILD_DURING_DEPLOYMENT** と **ENABLE_ORYX_BUILD** の両方が **true** に設定されていることを確認してください。 詳細については、[アプリケーション設定の操作方法](functions-how-to-use-azure-function-app-settings.md#settings)に関する記述を参照してください。

---

#### <a name="build-native-dependencies"></a>ネイティブの依存関係を構築する

最新バージョンの **docker** と [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools/releases) の両方がインストールされていることを確認してください。 ローカル関数プロジェクト フォルダーに移動し、デプロイに `func azure functionapp publish <app-name> --build-native-deps` を使用します。

#### <a name="update-your-package-to-the-latest-version"></a>パッケージを最新バージョンに更新する

`https://pypi.org/project/<package-name>` で最新のパッケージ バージョンを参照し、**Classifiers:** セクションを確認します。 パッケージは `OS Independent` であるか、**Operating System** の `POSIX` または `POSIX :: Linux` と互換性がある必要があります。 また、プログラミング言語には、`Python :: 3`、`Python :: 3.6`、`Python :: 3.7`、または `Python :: 3.8` が含まれている必要があります。

これらが正しい場合は、requirements.txt の `<package-name>~=<latest-version>` 行を変更することで、パッケージを最新バージョンに更新できます。

#### <a name="handcraft-requirementstxt"></a>requirements.txt を手動で作成する

一部の開発者は `pip freeze > requirements.txt` を使用して、開発環境用の Python パッケージの一覧を生成します。 ほとんどの場合、この利便性が機能するはずですが、関数は Windows または macOS でローカルに開発しても、Linux で実行される Function App に発行する場合など、クロスプラットフォームのデプロイ シナリオで問題が発生する可能性があります。 このシナリオでは、`pip freeze` によって、ローカル開発環境に対して予期しないオペレーティング システム固有の依存関係が生じる場合があります。 これらの依存関係により、Python Function App が Linux で実行されている場合に中断される可能性があります。

プロジェクト ソース コード内の各 .py ファイルの import ステートメントを確認し、requirements.txt ファイルでそれらのモジュールのみをチェックインすることをお勧めします。 これにより、さまざまなオペレーティング システムでパッケージの解決を適切に扱えることが保証されます。

#### <a name="replace-the-package-with-equivalents"></a>パッケージを同等のものに置き換える

まず、`https://pypi.org/project/<package-name>` でパッケージの最新バージョンを確認する必要があります。 通常、このパッケージには独自の GitHub ページがあり、GitHub の **Issues** セクションに移動して、問題が解決されたかどうかを検索します。 そうである場合は、パッケージを最新バージョンに更新します。

場合によっては、パッケージが [Python Standard Library](https://docs.python.org/3/library/) (pathlib など) に統合されていることがあります。 そのような場合は、Azure Functions で特定の Python ディストリビューション (Python 3.6、Python 3.7、および Python 3.8) が提供されるため、requirements.txt のパッケージを削除する必要があります。

しかし、問題が解決されておらず、期限が迫っている場合があります。 ある程度調査し、プロジェクトに対して同様のパッケージを見つけることをお勧めします。 通常、Python コミュニティでは、使用可能な同様のさまざまなライブラリが提供されます。

---

## <a name="troubleshoot-cannot-import-cygrpc"></a>トラブルシューティングで "cygrpc" をインポートできない

このセクションは、Python Function App での "cygrpc" 関連のエラーのトラブルシューティングに役立ちます。 これらのエラーでは、通常、次のような Azure Functions エラー メッセージが示されます。

> `Cannot import name 'cygrpc' from 'grpc._cython'`

このエラーは、Python Function App で適切な Python インタープリターの使用を開始できない場合に発生します。 このエラーの根本原因は、次の問題のいずれかです。

- [Python インタープリターが OS アーキテクチャに一致しない](#the-python-interpreter-mismatches-os-architecture)
- [Python インタープリターが Azure Functions の Python Worker でサポートされない](#the-python-interpreter-is-not-supported-by-azure-functions-python-worker)

### <a name="diagnose-cygrpc-reference-error"></a>"cygrpc" リファレンス エラーの診断

#### <a name="the-python-interpreter-mismatches-os-architecture"></a>Python インタープリターが OS アーキテクチャに一致しない

これは、32 ビットの Python インタープリターが 64 ビットのオペレーティング システムにインストールされていることが原因だと考えられます。

x64 オペレーティング システムで実行している場合は、Python 3.6、3.7、または 3.8 インタープリターが 64 ビットバージョンであることを確認してください。

次のコマンドを使用して、ご利用の Python インタープリターのビットを確認できます。

Windows で PowerShell の場合:`py -c 'import platform; print(platform.architecture()[0])'`

Unix のようなシェルの場合:`python3 -c 'import platform; print(platform.architecture()[0])'`

Python インタープリターのビットとオペレーティング システムのアーキテクチャが一致しない場合は、[Python Software Foundation](https://python.org/downloads/release) から適切な Python インタープリターをダウンロードしてください。

#### <a name="the-python-interpreter-is-not-supported-by-azure-functions-python-worker"></a>Python インタープリターが Azure Functions の Python Worker でサポートされない

Azure Functions の Python Worker では、Python 3.6、3.7、3.8 のみがサポートされています。
Windows の場合は `py --version`、または Unix 系システムの場合は `python3 --version` で、Python インタープリターが想定されているバージョンと一致しているかどうかを確認してください。 返された結果が Python 3.6.x、Python 3.7.x、または Python 3.8.x であることを確認します。

Python インタープリターが想定されているバージョンでない場合は、[Python Software Foundation](https://python.org/downloads/release) から Python 3.6、3.7、または 3.8 インタープリターをダウンロードしてください。

## <a name="next-steps"></a>次の手順

問題を解決できない場合は、Functions チームに報告してください。

> [!div class="nextstepaction"]
> [未解決の問題を報告する](https://github.com/Azure/azure-functions-python-worker/issues)
