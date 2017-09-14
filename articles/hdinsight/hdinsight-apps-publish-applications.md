---
title: "HDInsight アプリケーションの発行 - Azure | Microsoft Docs"
description: "HDInsight アプリケーションを作成して発行する方法について説明します。"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 14aef891-7a37-4cf1-8f7d-ca923565c783
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/25/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 6aa66cac35bc317fc87003e6c3d824544c53de88
ms.contentlocale: ja-jp
ms.lasthandoff: 06/09/2017


---
# <a name="publish-hdinsight-applications-into-the-azure-marketplace"></a>Azure Marketplace への HDInsight アプリケーションの発行
HDInsight アプリケーションは、ユーザーが Linux ベースの HDInsight クラスターにインストールすることのできるアプリケーションです。 マイクロソフトや独立系ソフトウェア ベンダー (ISV) によって作成されるほか、ユーザーが独自に作成することもできます。 この記事では、HDInsight アプリケーションを Azure Marketplace に発行する方法について説明します。  Azure Marketplace への発行全般に関する情報については、「 [Azure Marketplace へのプランの発行方法](../marketplace-publishing/marketplace-publishing-getting-started.md)」を参照してください。

HDInsight アプリケーションには*ライセンス持ち込み (BYOL)* モデルが適用され、エンド ユーザーに対するアプリケーションの使用許諾は、そのアプリケーションの提供者が担います。また、Azure からエンド ユーザーへの課金は、そのエンド ユーザーによって作成されたリソース (HDInsight クラスターとその VM/ノードなど) についてのみ行われます。 現在、アプリケーションそのものに対する課金は Azure を介さず行われます。

HDInsight アプリケーションに関連したその他の記事もご覧ください。

* [HDInsight アプリケーションをインストールする](hdinsight-apps-install-applications.md): HDInsight アプリケーションをクラスターにインストールする方法について確認します。
* [カスタム HDInsight アプリケーションをインストールする](hdinsight-apps-install-custom-applications.md): カスタム HDInsight アプリケーションをインストールしてテストする方法について説明します。

## <a name="prerequisites"></a>前提条件
カスタム アプリケーションを Azure Marketplace に送信するには、あらかじめカスタム アプリケーションの作成とテストを済ませておく必要があります。 次の記事を参照してください。

* [カスタム HDInsight アプリケーションをインストールする](hdinsight-apps-install-custom-applications.md): カスタム HDInsight アプリケーションをインストールしてテストする方法について説明します。

さらに、開発者アカウントの登録が必要です。 [Azure Marketplace へのプランの発行](../marketplace-publishing/marketplace-publishing-getting-started.md)に関するページおよび「[Microsoft 開発者アカウントの作成](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md)」をご覧ください。

## <a name="define-application"></a>アプリケーションの定義
Azure Marketplace へのアプリケーションの発行は 2 つの段階を経て行います。  まず、 **createUiDef.json** ファイルを定義して、アプリケーションに適合するクラスターを指定し、次に Azure ポータルからテンプレートを発行します。 次のセクションは、サンプルの createUiDef.json ファイルです。

    {
        "handler": "Microsoft.HDInsight",
        "version": "0.0.1-preview",
        "clusterFilters": {
            "types": ["Hadoop", "HBase", "Storm", "Spark"],
            "tiers": ["Standard", "Premium"],
            "versions": ["3.4"]
        }
    }


| フィールド | Description | 指定できる値 |
| --- | --- | --- |
| types |アプリケーションと適合するクラスターの種類。 |Hadoop、HBase、Storm、Spark のいずれか (またはその任意の組み合わせ) |
| tiers |アプリケーションと適合するクラスターのプラン。 |Standard または Premium (またはその両方) |
| versions |アプリケーションと適合する HDInsight クラスターの種類。 |3.4 |

## <a name="application-install-script"></a>アプリケーションのインストール スクリプト
アプリケーションが (既存または新規の) クラスターにインストールされると必ず、エッジ ノードが作成され、アプリケーション インストール スクリプトはそのノードで実行されます。
  > [!IMPORTANT]
  > アプリケーションのインストール スクリプトの名前は次の形式を使用し、特定のクラスターにおいて一意である必要があります。
  > 
  > name": "[concat('hue-install-v0','-' ,uniquestring(‘applicationName’)]"
  > 
  > スクリプト名は、次の 3 つの部分で構成されていることに注意してください。
  > 
  > 1. スクリプト名のプレフィックス。アプリケーション名またはアプリケーションに関連する名前のどちらかが含まれている必要があります。
  > 2. "-" (読みやすくするため)。
  > 3. 一意の文字列の関数 (パラメーターとしてアプリケーション名を指定)。
  > 
  > 上記の例の場合、保存されたスクリプト アクションの一覧では hue-install-v0-4wkahss55hlas となります。 JSON ペイロードのサンプルについては、[https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json) を参照してください。
  > 
インストール スクリプトに必要な特性を次に示します。
1. べき等スクリプトにします。 スクリプトを複数回呼び出すと、同じ結果が生成されます。
2. スクリプトのバージョンを適切に管理します。 アップグレードや変更をテストするときは、アプリケーションをインストールするお客様が影響を受けないように、別の場所にあるスクリプトを使用します。 
3. ポイントごとに適切なログ記録をスクリプトに追加します。 通常、スクリプト ログは、アプリケーション インストールに関する問題をデバッグする唯一の方法です。
4. インストールがネットワークの一時的な問題に影響されないように、外部サービスやリソースへの呼び出しの再試行数は十分に確保します。
5. スクリプトがノードでサービスを開始する場合は、サービスを確実に監視し、ノードの再起動時にサービスが自動的に開始されるように構成します。

## <a name="package-application"></a>アプリケーションのパッケージ化
HDInsight アプリケーションのインストールに必要なファイルをすべて含んだ zip ファイルを作成します。 この zip ファイルは、「[アプリケーションの発行](#publish-application)」で使用します。

* [createUiDefinition.json](#define-application)。
* mainTemplate.json。 「 [カスタム HDInsight アプリケーションのインストール](hdinsight-apps-install-custom-applications.md)」でサンプルを参照してください。
* 必要なすべてのスクリプト。

> [!NOTE]
> アプリケーション ファイル (もしあれば Web アプリケーション ファイルも含む) は、パブリックにアクセスできる任意のエンドポイントに配置できます。
> 

## <a name="publish-application"></a>アプリケーションの発行
次の手順に従って HDInsight アプリケーションを発行します。

1. [Azure 発行ポータル](https://publish.windowsazure.com/)にサインオンします。
2. 左側の **[ソリューション テンプレート]** をクリックして、新しいソリューション テンプレートを作成します。
3. タイトルを入力し、**[Create a new solution template (新しいソリューション テンプレートを作成する)]** をクリックします。
4. **[Create Dev Center account and join the Azure program (デベロッパー センター アカウントを作成して Azure プログラムに参加する)]** をクリックして勤務先の会社を登録します (未登録の場合)。  [Microsoft 開発者アカウントの作成](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md)」を参照してください。
5. **[Define some Topologies to get Started (いくつかのトポロジを定義して開始する)]**をクリックします。 ソリューション テンプレートは、作成したすべてのトポロジの「親」となります。 1 つのプランまたはソリューション テンプレートでは、複数のトポロジを定義できます。 プランをステージングにプッシュすると、すべてのトポロジも一緒にプッシュされます。 
6. トポロジ名を入力し、プラス記号をクリックします。
7. 新しいバージョンを入力し、プラス記号をクリックします。
8. 「 [アプリケーションのパッケージ化](#package-application)」で作成した zip ファイルをアップロードします。  
9. **[Request Certification (証明書の要求)]**をクリックします。 Microsoft の認定チームがファイルを確認し、トポロジを認定します。

## <a name="next-steps"></a>次のステップ
* [HDInsight アプリケーションをインストールする](hdinsight-apps-install-applications.md): HDInsight アプリケーションをクラスターにインストールする方法について確認します。
* [カスタム HDInsight アプリケーションをインストールする](hdinsight-apps-install-custom-applications.md): 未発行の HDInsight アプリケーションを HDInsight にデプロイする方法について確認します。
* [スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md): スクリプト アクションを使用してアプリケーションを追加インストールする方法を確認します。
* [Azure Resource Manager テンプレートを使用して HDInsight で Linux ベースの Hadoop クラスターを作成する](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Resource Manager テンプレートを呼び出して HDInsight クラスターを作成する方法を確認します。
* [HDInsight で空のエッジ ノードを使用する](hdinsight-apps-use-edge-node.md): HDInsight クラスター、テスト HDInsight アプリケーション、およびホスティング HDInsight アプリケーションにアクセスするために空のエッジ ノードを使用する方法を確認します。


