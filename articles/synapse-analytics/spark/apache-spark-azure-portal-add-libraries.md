---
title: Azure Synapse Analytics で Apache Spark 用のライブラリを管理する
description: Azure Synapse Analytics で、Apache Spark によって使用されるライブラリを追加および管理する方法について説明します。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.subservice: spark
ms.openlocfilehash: b7aea6565e8301e2aeb96263b8e7b1d2ea64995d
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589558"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Azure Synapse Analytics で Apache Spark 用のライブラリを管理する

ライブラリでは、プログラムまたはプロジェクトに含めることができる再利用可能なコードが提供されます。 サードパーティのコードまたはローカル環境でビルドされたコードをアプリケーションで使用できるようにするには、いずれかの Spark プール (プレビュー) にライブラリをインストールします。 Spark プールにインストールされたライブラリは、同じプールを使用するすべてのセッションで使用できるようになります。 

## <a name="default-installation"></a>既定のインストール
Azure Synapse Analytics の Apache Spark には、Anacondas の完全インストールと追加のライブラリがあります。 完全なライブラリの一覧については、[Apache Spark のバージョンのサポート](apache-spark-version-support.md)に関するページを参照してください。 

これらのライブラリは、Spark インスタンスが起動されるときに自動的に組み込まれます。 追加の Python パッケージおよびカスタム ビルド パッケージを、Spark プール (プレビュー) レベルで追加できます。


## <a name="manage-python-packages"></a>Python パッケージの管理
Spark アプリケーションで使用したいライブラリが見つかったら、それらを Spark プール (プレビュー) にインストールすることができます。 

 *requirements.txt* ファイル (`pip freeze` コマンドからの出力) を使用して、仮想環境をアップグレードできます。 このファイルにリストされているインストールまたはアップグレード用のパッケージは、プールの開始時に PyPi からダウンロードされます。 この要件ファイルは、その Spark プールから Spark インスタンスが作成されるたびに使用されます。

> [!IMPORTANT]
> - インストールするパッケージが大きいか、インストールに時間がかかる場合、これは Spark インスタンスのアップタイムに影響します。
> - インストール時にコンパイラのサポートを必要とするパッケージ (GCC など) はサポートされていません。
> - パッケージのダウングレードはできません。追加またはアップグレードのみが可能です。

### <a name="requirements-format"></a>要件の形式

次のスニペットは、要件ファイルの形式を示しています。 PyPi パッケージ名が正確なバージョンと共にリストされます。 このファイルは、[pip freeze](https://pip.pypa.io/en/stable/reference/pip_freeze/) のリファレンス ドキュメントで説明されている形式に従います。 この例では、特定のバージョンを固定しています。 

```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```

### <a name="install-python-packages"></a>Python パッケージのインストール
Spark アプリケーションを開発するときに、既存のライブラリの更新または新しいライブラリのインストールが必要であることがわかる場合があります。 ライブラリは、プールの作成中または作成後に更新できます。

#### <a name="install-packages-during-pool-creation"></a>プールの作成中にパッケージをインストールする
プールを作成している間にライブラリを Spark プール (プレビュー) にインストールするには:
   
1. Azure portal から Azure Synapse Analytics ワークスペースに移動します。
   
2. **[Create Apache Spark pool]\(Apache Spark プールの作成\)** を選択し、 **[追加設定]** タブを選択します。 
   
3. ページの **[パッケージ]** セクションにあるファイル セレクターを使用して、環境構成ファイルをアップロードします。 
   
![プールの作成時に Python ライブラリを追加する](./media/apache-spark-azure-portal-add-libraries/apache-spark-azure-portal-add-library-python.png "Python ライブラリを追加する")
 

#### <a name="install-packages-from-the-synapse-workspace"></a>Synapse ワークスペースからパッケージをインストールする
Azure Synapse Analytics ポータルから Spark プール (プレビュー) に追加のライブラリを更新または追加するには:

1.  Azure portal から Azure Synapse Analytics ワークスペースに移動します。
   
2.  Azure portal から Azure Synapse Analytics ワークスペースを起動します。

3.  メイン ナビゲーション パネルから **[管理]** を選択し、 **[Apache Spark プール]** を選択します。
   
4. Spark プールを 1 つ選択し、ページの **[パッケージ]** セクションにあるファイル セレクターを使用して、環境構成ファイルをアップロードします。

![Synapse で Python ライブラリを追加する](./media/apache-spark-azure-portal-add-libraries/apache-spark-azure-portal-update.png "Python ライブラリを追加する")
   
#### <a name="install-packages-from-the-azure-portal"></a>Azure portal からパッケージをインストールする
Azure portal から Spark プール (プレビュー) にライブラリを直接インストールするには:
   
 1. Azure portal から Azure Synapse Analytics ワークスペースに移動します。
   
 2. **[Synapse resources]\(Synapse リソース\)** セクションで **[Apache Spark プール]** タブを選択し、一覧から Spark プールを選択します。
   
 3. Spark プールの **[設定]** セクションから **[パッケージ]** を選択します。 

 4. ファイル セレクターを使用して、環境構成ファイルをアップロードします。

![Python ライブラリを追加する](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Python ライブラリを追加する")

### <a name="verify-installed-libraries"></a>インストールされているライブラリの検証

正しいライブラリの正しいバージョンがインストールされているかどうかを検証するには、次のコードを実行します。

```python
import pip #needed to use the pip functions
for i in pip.get_installed_distributions(local_only=True):
    print(i)
```
### <a name="update-python-packages"></a>Python パッケージを更新する
セッションとセッションの間のいつでも、パッケージを追加または変更できます。 新しいパッケージ構成ファイルをアップロードすると、既存のパッケージとバージョンが上書きされます。  

ライブラリを更新またはアンインストールするには:
1. Azure Synapse Analytics ワークスペースに移動します。 

2. Azure portal または Azure Synapse ワークスペースを使用して、更新する **Apache Spark プール**を選択します。

3. **[パッケージ]** セクションに移動し、新しい環境構成ファイルをアップロードします
   
4. 変更を保存したら、アクティブなセッションを終了し、プールを再起動する必要があります。 必要に応じて、 **[Force new settings]\(新しい設定を強制\)** チェック ボックスをオンにして、アクティブなセッションを強制的に終了できます。

![Python ライブラリを追加する](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Python ライブラリを追加する")
   

> [!IMPORTANT]
> **[Force new settings]\(新しい設定を強制\)** オプションを選択すると、選択した Spark プールの現在のすべてのセッションが終了します。 セッションが終了したら、プールが再起動するまで待機する必要があります。 
>
> この設定をオフにした場合は、現在の Spark セッションが終了するまで待つか、手動で停止する必要があります。 セッションが終了したら、プールが再起動されるようにする必要があります。 


## <a name="manage-a-python-wheel"></a>Python Wheel を管理する

### <a name="install-a-custom-wheel-file"></a>カスタム Wheel ファイルをインストールする
Synapse ワークスペースにリンクされている Azure Data Lake Storage (Gen2) アカウントにすべての wheel ファイルをアップロードすることで、カスタム ビルドの wheel パッケージを Apache Spark プールにインストールできます。 

ストレージ アカウントの既定のコンテナーの次のパスに、ファイルをアップロードする必要があります。 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>sparkpools/<pool_name>libraries/python/
```

>[!IMPORTANT]
>セッションとセッションの間に、カスタム パッケージを追加または変更できます。 ただし、更新されたパッケージを表示するには、プールとセッションが再起動するまで待つ必要があります。

## <a name="next-steps"></a>次のステップ
- 既定のライブラリを確認します: [Apache Spark バージョンのサポート](apache-spark-version-support.md)
