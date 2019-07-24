---
title: テンプレートを使用して Apache Hadoop クラスターを作成する - Azure HDInsight
description: Resource Manager テンプレートを使用して HDInsight 用のクラスターを作成する方法について説明します
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: e3757e94dcd8ccc39613b9a6fff8bf491812866a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59044982"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Resource Manager テンプレートを使用して HDInsight で Apache Hadoop クラスターを作成する
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

この記事では、Azure Resource Manager テンプレートを使用して Azure HDInsight クラスターを作成するさまざまな方法について説明します。 詳細については、「 [Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](../azure-resource-manager/resource-group-template-deploy.md)」を参照してください。 その他のクラスター作成ツールと機能を確認するには、このページの上部にあるタブ セレクターをクリックするか、「[クラスターの作成方法](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods)」を参照してください。

## <a name="prerequisites"></a>前提条件
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

この記事の手順に従うには、次のものが必要です。

* [Azure サブスクリプション](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* Azure PowerShell または Azure Classic CLI。

### <a name="resource-manager-templates"></a>Resource Manager テンプレート
Resource Manager テンプレートを使用すると、1 つの調整された操作で、アプリケーションのために以下のリソースを簡単に作成できます。
* HDInsight クラスターとそれらの依存リソース (既定のストレージ アカウントなど)。
* その他のリソース ([Apache Sqoop](https://sqoop.apache.org/) を使用する Azure SQL Database など)。

テンプレートには、アプリケーションで必要なリソースを定義します。 異なる環境の値を入力するためのデプロイパラメーターも指定します。 テンプレートは、デプロイ用の値を構築するために使用する JSON と式で構成されます。

HDInsight テンプレートのサンプルは、「[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/?term=hdinsight)」で見つけることができます。 [Resource Manager 拡張機能](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)が付属しているクロスプラットフォームの [Visual Studio Code](https://code.visualstudio.com/#alt-downloads) またはテキスト エディターを使用して、テンプレートをワークステーションのファイルに保存します。 

Resource Manager テンプレートの詳細については、次の記事と例を参照してください。

* [Azure リソース マネージャーのテンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)
* [Azure Resource Manager テンプレートを使用したアプリケーションのデプロイ](../azure-resource-manager/resource-group-template-deploy.md)
* [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/allversions) テンプレート リファレンス
* [Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="generate-templates"></a>テンプレートを生成する

Resource Manager を使用すると、サブスクリプション内の既存のリソースから Resource Manager テンプレートをさまざまなツールでエクスポートできます。 この生成されたテンプレートを使用すると、テンプレートの構文を学習したり、必要に応じてソリューションの再デプロイを自動化したりすることができます。 詳細については、[テンプレートのエクスポート](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)に関する記事を参照してください。

## <a name="deploy-using-the-portal"></a>ポータルを使用したデプロイ

Resource Manager テンプレートは、Azure Portal を使用してデプロイすることができます。 詳細については、「[カスタム テンプレートからリソースをデプロイする](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)」を参照してください。

## <a name="deploy-using-powershell"></a>PowerShell を使用したデプロイ

Resource Manager テンプレートは、Azure PowerShell を使用してデプロイすることができます。 詳細については、「[Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](../azure-resource-manager/resource-group-template-deploy.md)」と「[SAS トークンと Azure PowerShell を使用してプライベートの Resource Manager テンプレートをデプロイする](../azure-resource-manager/resource-manager-powershell-sas-token.md)」を参照してください。

## <a name="deploy-using-azure-cli"></a>Azure CLI を使用したデプロイ

Resource Manager テンプレートは、クラシック CLI を使用してデプロイすることができます。 詳細については、「[Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../azure-resource-manager/resource-group-template-deploy-cli.md)」と「[SAS トークンと Azure CLI を使用してプライベートの Resource Manager テンプレートをデプロイする](../azure-resource-manager/resource-manager-cli-sas-token.md)」を参照してください。

## <a name="deploy-using-the-rest-api"></a>REST API を使用したデプロイ
Resource Manager テンプレートは、REST API を使用してデプロイすることができます。 詳細については、「[Resource Manager テンプレートと Resource Manager REST API を使用したリソースのデプロイ](../azure-resource-manager/resource-group-template-deploy-rest.md)」を参照してください。

## <a name="deploy-with-visual-studio"></a>Visual Studio でのデプロイ
 Visual Studio を使用してリソース グループ プロジェクトを作成し、それをユーザー インターフェイスを通して Azure にデプロイします。 プロジェクトに含めるリソースの種類を選択します。 これらのリソースは、Resource Manager テンプレートに自動的に追加されます。 プロジェクトでは、テンプレートをデプロイするための PowerShell スクリプトも提供されます。

Visual Studio とリソース グループの使用の概要については、「 [Visual Studio での Azure リソース グループの作成とデプロイ](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)」を参照してください。

## <a name="troubleshoot"></a>トラブルシューティング

HDInsight クラスターの作成で問題が発生した場合は、「[アクセス制御の要件](hdinsight-hadoop-create-linux-clusters-portal.md)」を参照してください。

## <a name="next-steps"></a>次の手順
この記事では、HDInsight クラスターを作成する方法をいくつか説明しました。 詳細については、次の記事を参照してください。

* HDInsight 関連のその他のテンプレートについては、「[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/?term=hdinsight)」を参照してください。
* .NET クライアント ライブラリを使用したリソースのデプロイの例については、[.NET ライブラリとテンプレートを使用したリソースのデプロイ](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページを参照してください。
* アプリケーションのデプロイの詳細な例については、「 [Azure でマイクロサービスを予測どおりにデプロイする](../app-service/deploy-complex-application-predictably.md)」を参照してください。
* ソリューションを別の環境にデプロイする方法については、「 [Microsoft Azure の開発環境とテスト環境](../solution-dev-test-environments.md)」を参照してください。
* Azure Resource Manager のテンプレートのセクションについては、「 [Azure Resource Manager のテンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)」を参照してください。
* Azure Resource Manager のテンプレートで使用できる関数の一覧については、「 [Azure Resource Manager のテンプレートの関数](../azure-resource-manager/resource-group-template-functions.md)」を参照してください。
