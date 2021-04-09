---
title: Azure HDInsight アプリケーションの発行
description: HDInsight アプリケーションを作成して、Azure Marketplace に発行する方法を説明します。
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 05/14/2018
ms.openlocfilehash: 73d291fd76d9adbe2a095406b56bd879d497d3b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98942658"
---
# <a name="publish-an-hdinsight-application-in-the-azure-marketplace"></a>Azure Marketplace への HDInsight アプリケーションの発行
Azure HDInsight アプリケーションは、Linux ベースの HDInsight クラスターにインストールできます。 この記事では、HDInsight アプリケーションを Azure Marketplace に発行する方法について説明します。 Azure Marketplace への発行全般に関する情報については、[Azure Marketplace へのプランの発行方法](../marketplace/overview.md)に関する記事を参照してください。

HDInsight アプリケーションは、"*ライセンス持ち込み (BYOL)*" モデルを使用します。 BYOL シナリオでは、アプリ ユーザーに対するアプリケーションの使用許諾はアプリケーション提供者が行います。 また、アプリ ユーザーへの課金はアプリ ユーザーが作成した Azure リソース (HDInsight クラスターとクラスターの VM/ノードなど) についてのみ行われます。 現在、アプリケーションそのものに対する課金は Azure で発生しません。

詳しくは、次の HDInsight アプリケーションに関連する記事を参照してください。

* [HDInsight アプリケーションをインストールする](hdinsight-apps-install-applications.md)。 HDInsight アプリケーションをクラスターにインストールする方法を説明します。
* [カスタム HDInsight アプリケーションをインストールする](hdinsight-apps-install-custom-applications.md)。 カスタム HDInsight アプリケーションをインストールしてテストする方法について説明します。

## <a name="prerequisites"></a>前提条件
カスタム アプリケーションを Azure Marketplace に送信するには、まず、[カスタム アプリケーションを作成してテストします](hdinsight-apps-install-custom-applications.md)。

さらに、開発者アカウントの登録が必要となります。 詳しくは、[Azure Marketplace へのプランの発行](../marketplace/overview.md)に関するページおよび「[Microsoft 開発者アカウントの作成](../marketplace/overview.md)」をご覧ください。

## <a name="define-the-application"></a>アプリケーションの定義
Marketplace へのアプリケーションの発行は、2 段階の手順で実行されます。 最初に、*createUiDef.json* ファイルを定義します。 createUiDef.json ファイルは、どのクラスターがアプリケーションと互換性があるかを示します。 次に、Azure Portal からテンプレートを発行します。 以下に createUiDef.json ファイルの例を示します。

```json
{
    "handler": "Microsoft.HDInsight",
    "version": "0.0.1-preview",
    "clusterFilters": {
        "types": ["Hadoop", "HBase", "Storm", "Spark"],
        "versions": ["3.6"]
    }
}
```

| フィールド | 説明 | 設定可能な値 |
| --- | --- | --- |
| types |アプリケーションと適合するクラスターの種類。 |Hadoop、HBase、Storm、Spark のいずれか (またはその任意の組み合わせ) |
| versions |アプリケーションと適合する HDInsight クラスターの種類。 |3.4 |

## <a name="application-installation-script"></a>アプリケーションのインストール スクリプト
アプリケーションがクラスター (既存のクラスターか新しいクラスターのいずれか) にインストールされると、エッジ ノードが作成されます。 アプリケーションのインストール スクリプトが、エッジ ノードで実行されます。

  > [!IMPORTANT]  
  > アプリケーションのインストール スクリプトの名前は、個々のクラスターに一意である必要があります。 スクリプト名の形式は、以下のとおりです。
  > 
  > "name": "[concat('hue-install-v0','-' ,uniquestring(‘applicationName’)]"
  > 
  > スクリプト名は、3 つの部分で構成されます。
  > 
  > * スクリプト名のプレフィックス。アプリケーション名またはアプリケーションに関連する名前のどちらかが含まれている必要があります。
  > * ハイフン (読みやすくするため)。
  > * 一意の文字列の関数 (パラメーターとしてアプリケーション名を指定)。
  > 
  > 保存されたスクリプト アクションの一覧では、上の例は **hue-install-v0-4wkahss55hlas** と表示されます。 [JSON ペイロードのサンプル](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json)をご覧ください。
  > 

インストール スクリプトに必要な特性を次に示します。
* べき等スクリプトにします。 スクリプトを複数回呼び出すと、同じ結果が生成されます。
* スクリプトのバージョンを適切に管理します。 変更をアップグレードまたはテストするときに、スクリプト用に別の場所を使用します。 これにより、アプリケーションをインストールする顧客は、更新またはテストの影響を受けることがなくなります。 
* スクリプトは、ポイントごとに適切なログ記録を持ちます。 通常、スクリプト ログは、アプリケーション インストールに関する問題をデバッグする唯一の方法です。
* インストールがネットワークの一時的な問題に影響されないように、外部サービスやリソースへの呼び出しの再試行数は十分に確保します。
* スクリプトがノードでサービスを開始する場合は、サービスを確実に監視し、ノードの再起動時にサービスが自動的に開始されるように構成します。

## <a name="package-the-application"></a>アプリケーションをパッケージ化する
HDInsight アプリケーションのインストールに必要なすべてのファイルを含んだ .zip ファイルを作成します。 .zip ファイルを使用してアプリケーションを発行します。 .zip ファイルには、次のファイルが含まれます。

* createUiDefinition.json
* mainTemplate.json (サンプルについては、「[カスタム HDInsight アプリケーションのインストール](hdinsight-apps-install-custom-applications.md)」を参照してください。)
* 必要なすべてのスクリプト

> [!NOTE]  
> パブリックにアクセスできる任意のエンドポイントでアプリケーション ファイル (任意の Web アプリ ファイルを含む) をホストできます。

## <a name="publish-the-application"></a>アプリケーションを発行する
HDInsight アプリケーションを発行するには:

1. [Azure 発行](https://publish.windowsazure.com/)にサインインします。
2. 左側のメニューで、**[ソリューション テンプレート]** を選択します。
3. タイトルを入力し、**[Create a new solution template]\(新しいソリューション テンプレートを作成する\)** を選択します。
4. 組織をまだ登録していない場合は、**[Create Dev Center account and join the Azure program]\(デベロッパー センター アカウントを作成して Azure プログラムに参加する\)** を選択します。  詳しくは、「[Microsoft 開発者アカウントの作成](../marketplace/overview.md)」を参照してください。
5. **[Define some Topologies to get Started]\(いくつかのトポロジを定義して開始する\)** を選択します。 ソリューション テンプレートは、作成したすべてのトポロジの「親」となります。 1 つのプランまたはソリューション テンプレートでは、複数のトポロジを定義できます。 プランをステージングにプッシュすると、すべてのトポロジも一緒にプッシュされます。 
6. トポロジ名を入力し、 **[+]** を選択します。
7. 新しいバージョンを入力し、 **[+]** を選択します。
8. アプリケーションのパッケージ化の際に、作成した .zip ファイルをアップロードします。  
9. **[Request Certification]\(証明書の要求\)** を選択します。 Microsoft の認定チームがファイルを確認し、トポロジを認定します。

## <a name="next-steps"></a>次のステップ
* クラスターに [HDInsight アプリケーションをインストール](hdinsight-apps-install-applications.md)する方法を確認します。
* [カスタム HDInsight アプリケーションをインストール](hdinsight-apps-install-custom-applications.md)し、未発行の HDInsight アプリケーションを HDInsight にデプロイする方法について確認します。
* [Script Action を使って Linux ベースの HDInsight クラスターをカスタマイズ](hdinsight-hadoop-customize-cluster-linux.md)し、アプリケーションをさらに追加する方法について説明します。 
* [Azure Resource Manager テンプレートを使用して、HDInsight での Linux ベースの Apache Hadoop クラスターを作成](hdinsight-hadoop-create-linux-clusters-arm-templates.md)する方法を確認します。
* HDInsight クラスター、テスト HDInsight アプリケーション、およびホスティング HDInsight アプリケーションにアクセスするために [HDInsight で空のエッジ ノードを使用](hdinsight-apps-use-edge-node.md)する方法を確認します。