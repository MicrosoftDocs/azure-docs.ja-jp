---
title: 環境イメージのトラブルシューティング
titleSuffix: Azure Machine Learning
description: 環境イメージのビルドとパッケージのインストールに関する問題のトラブルシューティングを行う方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: saachigopal
ms.author: sagopal
ms.date: 12/3/2020
ms.topic: troubleshooting
ms.custom: devx-track-python
ms.openlocfilehash: ec0c7d64f2145cdaf594cb903c072984f4d376a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519131"
---
# <a name="troubleshoot-environment-image-builds"></a>環境イメージのビルドのトラブルシューティング

Docker 環境イメージのビルドとパッケージのインストールに関する問題のトラブルシューティングを行う方法について説明します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) をお試しください。
* [Azure Machine Learning SDK](/python/api/overview/azure/ml/install)。
* [Azure CLI](/cli/azure/install-azure-cli)。
* [Azure Machine Learning 用 CLI 拡張機能](reference-azure-machine-learning-cli.md)。
* ローカルでデバッグするには、ローカル システム上に機能する Docker のインストールが必要です。

## <a name="docker-image-build-failures"></a>Docker イメージのビルド エラー
 
ほとんどのイメージ ビルド エラーについては、イメージ ビルド ログで根本原因を見つけることができます。
イメージ ビルド ログは、Azure Machine Learning ポータル (20\_image\_build\_log.txt)、または Azure Container Registry タスク実行ログで見つけます。
 
通常、エラーをローカルで再現する方が簡単です。 エラーの種類を確認し、次の `setuptools` のいずれかを試してください。

- conda 依存関係をローカルにインストールする: `conda install suspicious-dependency==X.Y.Z`。
- pip 依存関係をローカルにインストールする: `pip install suspicious-dependency==X.Y.Z`。
- 環境全体を具体化する: `conda create -f conda-specification.yml`。

> [!IMPORTANT]
> ローカル コンピューティング クラスターのプラットフォームとインタープリターが、リモート コンピューティング クラスターのものと一致していることを確認します。 

### <a name="timeout"></a>タイムアウト 
 
次のネットワークの問題により、タイムアウト エラーが発生する可能性があります。

- インターネット帯域幅が少ない
- サーバーの問題
- 依存関係が大規模で、指定した conda または pip のタイムアウト設定ではダウンロードできない
 
次の例のようなメッセージは、この問題を示しています。
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

エラー メッセージが表示された場合は、次の考えられる解決策のいずれかを試してください。
 
- ミラー、Azure Blob Storage、その他の Python フィードなど、依存関係の別のソースを試してください。
- conda または pip を更新します。 カスタム Docker ファイルを使用している場合は、タイムアウト設定を更新します。
- 一部の pip バージョンには既知の問題があります。 pip の特定のバージョンを環境の依存関係に追加することを検討してください。

### <a name="package-not-found"></a>パッケージが見つからない

次のエラーは、イメージのビルド エラーの最も一般的なものです。

- conda パッケージが見つからなかった。

   ```
   ResolvePackageNotFound: 
   - not-existing-conda-package
   ```

- 指定した pip パッケージまたはバージョンが見つからなかった。

   ```
   ERROR: Could not find a version that satisfies the requirement invalid-pip-package (from versions: none)
   ERROR: No matching distribution found for invalid-pip-package
   ```

- 入れ子になった pip 依存関係が無効。

   ```
   ERROR: No matching distribution found for bad-package==0.0 (from good-package==1.0)
   ```

指定したソースにパッケージが存在することを確認します。 pip の依存関係を確認するには、[pip search](https://pip.pypa.io/en/stable/reference/pip_search/) を使用します。

- `pip search azureml-core`

conda の依存関係については、[conda search](https://docs.conda.io/projects/conda/en/latest/commands/search.html) を使用します。

- `conda search conda-forge::numpy`

追加のオプションとして、次を試してください。
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>インストーラーに関する注意事項

指定したプラットフォームと Python インタープリターのバージョン用に必要とされるディストリビューションが存在することを確認します。

pip の依存関係については、`https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files` に移動して、必要なバージョンが使用可能かどうかを確認します。 例については、 https://pypi.org/project/azureml-core/1.11.0/#files を参照してください。

conda の依存関係については、チャネル リポジトリでパッケージを確認します。
Anaconda, Inc. によって管理されているチャネルについては、[Anaconda パッケージのページ](https://repo.anaconda.com/pkgs/)を確認してください。

### <a name="pip-package-update"></a>pip パッケージの更新

pip パッケージのインストール時または更新時に、新しい要件を満たすために、既にインストールされているパッケージをリゾルバーで更新することが必要になる場合があります。
pip のバージョンや、依存関係がインストールされた方法に関連するさまざまな理由により、アンインストールが失敗することがあります。
最も一般的なシナリオは、conda によってインストールされた依存関係を pip でアンインストールできなかったというものです。
このシナリオの場合、`conda remove mypackage` を使用して依存関係をアンインストールすることを検討してください。

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>インストーラーの問題

特定のインストーラーのバージョンはパッケージ リゾルバーに問題があり、それがビルドの失敗につながるおそれがあります。

カスタムの基本イメージまたは Dockerfile を使用する場合は、conda バージョン 4.5.4 以降を使用することをお勧めします。

pip の依存関係をインストールするには、pip パッケージが必要です。 環境でバージョンが指定されていない場合は、最新バージョンが使用されます。
最新バージョンのツールが原因で発生する可能性がある一時的な問題や破壊的変更を回避するために、pip の既知のバージョンを使用することをお勧めします。

次のメッセージが表示された場合は、環境内で pip のバージョンを固定することを検討してください。

   ```
   Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.
   ```

pip のサブプロセスのエラー:
   ```
   ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.
   ```

解決できない競合が依存関係に存在する場合は、pip のインストールが無限ループに陥る可能性があります。 ローカルで作業している場合は、pip のバージョンを 20.3 未満にダウングレードします。 YAML ファイルから作成された conda 環境では、この問題が発生するのは、conda-forge が最高優先度のチャネルである場合のみです。 この問題を軽減するには、conda 依存関係として、conda 仕様ファイル内に pip < 20.3 (!=20.3 または =20.2.4 で他のバージョンに固定) を明示的に指定します。

## <a name="service-side-failures"></a>サービス側のエラー

サービス側で発生する可能性のあるエラーをトラブルシューティングするには、次のシナリオを参照してください。

### <a name="youre-unable-to-pull-an-image-from-a-container-registry-or-the-address-couldnt-be-resolved-for-a-container-registry"></a>コンテナー レジストリからイメージをプルできない、またはコンテナー レジストリのアドレスを解決できない

考えられる問題:
- コンテナー レジストリのパス名が正しく解決されていない可能性があります。 イメージ名に二重スラッシュが使用されていること、および Linux と Windows のホストでスラッシュの方向が正しいことを確認してください。
- 仮想ネットワークの背後にあるコンテナー レジストリで、[サポートされていないリージョン](../private-link/private-link-overview.md#availability)内のプライベート エンドポイントを使用している場合は、ポータルからサービス エンドポイント (パブリック アクセス) を使用してコンテナー レジストリを構成し、再試行してください。
- 仮想ネットワークの背後にコンテナー レジストリを配置した後、[Azure Resource Manager テンプレート](./how-to-network-security-overview.md)を実行して、ワークスペースがコンテナー レジストリ インスタンスと通信できるようにします。

### <a name="you-get-a-401-error-from-a-workspace-container-registry"></a>ワークスペース コンテナー レジストリで 401 エラーが発生する

[ws.sync_keys()](/python/api/azureml-core/azureml.core.workspace.workspace#sync-keys--) を使用してストレージ キーを再同期します。

### <a name="the-environment-keeps-throwing-a-waiting-for-other-conda-operations-to-finish-error"></a>環境で "Waiting for other conda operations to finish..." (他の Conda 操作の完了を待機しています...) というエラーが継続してスローされる

イメージのビルドが進行しているときは、conda は SDK クライアントによってロックされます。 プロセスがクラッシュした場合、またはユーザーによって不適切に取り消された場合、conda はロックされた状態のままになります。 この問題を解決するには、ロック ファイルを手動で削除します。 

### <a name="your-custom-docker-image-isnt-in-the-registry"></a>カスタム Docker イメージがレジストリに存在しない

[正しいタグ](./how-to-use-environments.md#create-an-environment)が使用されていて、`user_managed_dependencies = True` であることを確認します。 `Environment.python.user_managed_dependencies = True` を使用すると、conda が無効になり、ユーザーがインストールしたパッケージが使用されます。

### <a name="you-get-one-of-the-following-common-virtual-network-issues"></a>次の一般的な仮想ネットワークの問題のいずれかが発生する

- ストレージ アカウント、コンピューティング クラスター、コンテナー レジストリのすべてが、仮想ネットワークの同じサブネット内に存在することを確認します。
- コンテナー レジストリが仮想ネットワークの背後にある場合、イメージのビルドのために直接使用することはできません。 イメージをビルドするために、コンピューティング クラスターを使用する必要があります。
- 次の場合、ストレージを仮想ネットワークの背後に配置することが必要になる場合があります。
    - 推論またはプライベート ホイールを使用する。
    - サービス エラー 403 (承認されていません) が表示される。
    - Azure Container Registry からイメージの詳細を取得できない。

### <a name="the-image-build-fails-when-youre-trying-to-access-network-protected-storage"></a>ネットワークの保護されたストレージにアクセスしようとしたときにイメージのビルドが失敗する

- Azure Container Registry のタスクは、仮想ネットワークの背後では機能しません。 ユーザーが仮想ネットワークの背後にあるコンテナー レジストリを使用している場合、それらのユーザーはコンピューティング クラスターを使用してイメージをビルドする必要があります。
- ストレージから依存関係をプルするために、ストレージを仮想ネットワークの背後に配置する必要があります。

### <a name="you-cant-run-experiments-when-storage-has-network-security-enabled"></a>ストレージのネットワーク セキュリティが有効になっているときは実験を実行できない

既定の Docker イメージを使用し、ユーザー管理の依存関係を有効にする場合は、MicrosoftContainerRegistry および AzureFrontDoor.FirstParty の[サービス タグ](./how-to-network-security-overview.md)を使用して、Azure Container Registry とその依存関係を許可リストに載せる必要があります。

 詳細については、[仮想ネットワークの有効化](./how-to-network-security-overview.md)に関する記事を参照してください。

### <a name="you-need-to-create-an-icm"></a>ICM を作成する必要がある

メタストアに対して ICM を作成または割り当てる場合は、Microsoft 側で問題を適切に把握できるように、CSS サポート チケットを含めてください。

## <a name="next-steps"></a>次のステップ

- [機械学習モデルをトレーニングして花を分類する](how-to-train-scikit-learn.md)
- [カスタム Docker イメージを使用して機械学習モデルをトレーニングする](how-to-train-with-custom-image.md)
