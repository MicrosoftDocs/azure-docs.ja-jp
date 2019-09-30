---
title: HDInsight クラスターを削除する方法 - Azure
description: Azure HDInsight クラスターを削除できるさまざまな方法についての情報
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/17/2019
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: a28d59cb35004fac6b069f2aa41042b4d46e443f
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091536"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>ブラウザー、PowerShell、または Azure CLI を使用して HDInsight クラスターを削除する

HDInsight クラスターの課金は、クラスターが作成されると開始し、クラスターが削除されると停止します。 課金は分単位なので、クラスターを使わなくなったら必ず削除してください。 このドキュメントでは、[Azure portal](https://portal.azure.com)、[Azure PowerShell Az モジュール](https://docs.microsoft.com/powershell/azure/overview)、および [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) を使ってクラスターを削除する方法について説明します。

> [!IMPORTANT]  
> HDInsight クラスターを削除しても、そのクラスターに関連付けられている Azure Storage アカウントまたは Data Lake Storage は削除されません。 これらのサービスに格納されているデータは、将来再利用できます。

## <a name="azure-portal"></a>Azure ポータル

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. 左側メニューから、 **[すべてのサービス]**  >  **[分析]**  >  **[HDInsight クラスター]** に移動し、クラスターを選択します。

3. 既定のビューから、 **[削除]** アイコンを選択します。 クラスターを削除するプロンプトに従います。

    ![HDInsight クラスター削除ボタン](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

## <a name="azure-powershell-az-module"></a>Azure PowerShell Az モジュール

`CLUSTERNAME` を、以下のコード内の HDInsight クラスターの名前に置き換えます。 クラスターを削除するには、PowerShell プロンプトで次のコマンドを実行します。

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI

`CLUSTERNAME`を HDInsight クラスターの名前、および`RESOURCEGROUP`次のコードでのリソース グループの名前に置き換えます。  クラスターを削除するには、コマンド プロンプトで以下を実行します。

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
