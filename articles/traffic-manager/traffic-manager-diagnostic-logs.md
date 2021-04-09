---
title: Azure Traffic Manager でリソースログを有効にする
description: Traffic Manager プロファイルのリソース ログを有効にし、その結果作成されるログ ファイルにアクセスする方法について説明します。
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: duau
ms.openlocfilehash: 4cf3709574e2055f40759fd2d7026c93ac9db098
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608021"
---
# <a name="enable-resource-logging-in-azure-traffic-manager"></a>Azure Traffic Manager でリソースログを有効にする

この記事では、Traffic Manager プロファイルの診断リソース ログの収集を有効にしてログ データにアクセスする方法について説明します。

Azure Traffic Manager のリソース ログからは、Traffic Manager プロファイル リソースの動作に関する分析情報が提供されます。 たとえば、プロファイルのログ データを使用すると、個々のプローブがエンドポイントに対してタイムアウトした理由を判断できます。

## <a name="enable-resource-logging"></a>リソース ログの有効化

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

以下のコマンドは、[Azure Cloud Shell](https://shell.azure.com/powershell) で、またはコンピューターから PowerShell を実行することで実行できます。 Azure Cloud Shell は無料の対話型シェルです。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 お使いのコンピューターから PowerShell を実行する場合は、Azure PowerShell モジュール 1.0.0 以降が必要です。 `Get-Module -ListAvailable Az` を実行して、インストールされているバージョンを確認できます。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカル環境で実行している場合は、`Login-AzAccount` を実行して Azure にサインインする必要もあります。

1. **Traffic Manager プロファイルを取得する:**

    リソース ログを有効にするには、Traffic Manager プロファイルの ID が必要です。 [Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile) を使用して、リソース ログを有効にする Traffic Manager プロファイルを取得します。 出力には、Traffic Manager プロファイルの ID 情報が含まれます。

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **Traffic Manager プロファイルのリソース ログを有効にする:**

    前のステップで取得した ID を使用して Traffic Manager プロファイルのリソース ログを有効にするには、[Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) を使用します。 次のコマンドでは、指定した Azure ストレージ アカウントに、Traffic Manager プロファイルの詳細ログが格納されます。 

      ```azurepowershell-interactive
    Set-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **診断の設定を検証する:**

      [Get-AzDiagnosticSetting](/powershell/module/az.monitor/get-azdiagnosticsetting) を使用して、Traffic Manager プロファイルの診断の設定を検証します。 次のコマンドでは、リソースのログに記録されるカテゴリが表示されます。

     ```azurepowershell-interactive
     Get-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      Traffic Manager プロファイル リソースに関連付けられているすべてのログ カテゴリが有効と表示されることを確認します。 また、ストレージ アカウントが正しく設定されていることを検証します。

## <a name="access-log-files"></a>アクセス ログ ファイル
1. [Azure portal](https://portal.azure.com) にサインインします。 
1. ポータルで Azure ストレージ アカウントに移動します。
2. お使いの Azure ストレージ アカウントの **[概要]** ページで、**[サービス]** の **[BLOB]** を選択します。
3. **[コンテナー]** で **insights-logs-probehealthstatusevents** を選択し、PT1H.json ファイルまで下に移動した後、**[ダウンロード]** をクリックしてこのログ ファイルのコピーをダウンロードして保存します。

    ![BLOB ストレージから Traffic Manager プロファイルのログ ファイルにアクセスする](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>Traffic Manager のログのスキーマ

Azure Monitor を通じて使用できるすべてのリソース ログには、共通の上位スキーマが共有されます。各サービスが、独自のイベントに固有のプロパティを出力するための柔軟性も備わっています。 最上位のリソース ログのスキーマについては、「[Azure リソース ログでサポートされているサービス、スキーマ、カテゴリ](../azure-monitor/essentials/resource-logs-schema.md)」をご覧ください。

次の表には、Azure Traffic Manager プロファイル リソースに固有のログ スキーマが含まれています。

|フィールド名|フィールドの型|定義|例|
|----|----|---|---|
|EndpointName|String|正常性の状態が記録されている Traffic Manager エンドポイントの名前。|*myPrimaryEndpoint*|
|Status|String|プローブされた Traffic Manager エンドポイントの正常性の状態。 状態は **Up** または **Down** のいずれかです。|**Up**|
|||||

## <a name="next-steps"></a>次のステップ

* [Traffic Manager の監視](traffic-manager-monitoring.md)の詳細を確認する