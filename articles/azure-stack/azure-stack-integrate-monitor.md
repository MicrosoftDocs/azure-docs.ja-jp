---
title: Azure Stack と外部の監視ソリューションとの統合 | Microsoft Docs
description: Azure Stack とデータセンターの外部の監視ソリューションを統合する方法を説明します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 02/06/2019
ms.author: jeffgilb
ms.reviewer: thoroet
ms.lastreviewed: 02/06/2019
ms.openlocfilehash: 520319fb21dce3cf4f3cc1b36c52657cf9eb24e7
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58904000"
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Azure Stack と外部の監視ソリューションとの統合

Azure Stack インフラストラクチャの外部の監視のためには、Azure Stack ソフトウェア、物理コンピューター、物理ネットワーク スイッチを監視する必要があります。 これらにはそれぞれ、正常性とアラートの情報を取得する方法があります。

- Azure Stack ソフトウェアでは、REST ベースの API を使用して正常性とアラートの情報を取得します。 記憶域スペース ダイレクトのようなソフトウェアによるテクノロジを使用すると、ストレージの正常性およびアラートはソフトウェアの監視に含まれます。
- 物理コンピューターでは、ベースボード管理コントローラー (BMC) を通して、正常性とアラートの情報を取得できます。
- 物理ネットワーク デバイスでは、SNMP プロトコルを通して、正常性とアラートの情報を取得できます。

各 Azure Stack ソリューションは、ハードウェア ライフサイクル ホストに含まれています。 このホストは、物理サーバーとネットワーク デバイス用に OEM (Original Equipment Manufacturer) ハードウェア ベンダーの監視ソフトウェアを実行します。 必要に応じて、これらの監視ソリューションを使用せずに、データセンター内の既存の監視ソリューションと直接統合することもできます。

> [!IMPORTANT]
> 使用する外部の監視ソリューションは、エージェントレスである必要があります。 Azure Stack コンポーネント内にサード パーティ製エージェントをインストールすることはできません。

次の図は、Azure Stack 統合システム、ハードウェア ライフサイクル ホスト、外部の監視ソリューション、外部のチケット発行/データ収集システムの間のトラフィック フローを示しています。

![Azure Stack 監視とチケット発行ソリューションとの間のトラフィックを示す図。](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

> [!NOTE]
> 物理サーバーやネットワーク デバイスとの直接の外部監視統合は許可されておらず、アクセス制御リスト (ACL) によって積極的にブロックされています。 

この記事では、Azure Stack を System Center Operations Manager や Nagios などの外部の監視ソリューションと統合する方法を説明します。 PowerShell を使用して、または REST API 呼び出しを介して、アラートをプログラムで操作する方法も説明します。

## <a name="integrate-with-operations-manager"></a>Operations Manager との統合

Azure Stack の外部の監視に Operations Manager を使用できます。 Microsoft Azure Stack 用 System Center 管理パックを使用すれば、複数の Azure Stack デプロイを 1 つの Operations Manager インスタンスで監視できます。 この管理パックは、正常性リソースプロバイダーと更新リソースプロバイダーの REST API を使用して Azure Stack と通信します。 ハードウェア ライフサイクル ホストで実行されている OEM 監視ソフトウェアを使用しない場合は、ベンダー管理パックをインストールして物理サーバーを監視できます。 Operations Manager のネットワーク デバイス検出機能を使用して、ネットワーク スイッチを監視することもできます。

Azure Stack 用管理パックには、次の機能があります。

- 複数の Azure Stack デプロイを監視できます
- Azure Active Directory (Azure AD) と Active Directory フェデレーション サービス (AD FS) をサポートしています
- アラートを取得したり終了したりできます
- 正常性と容量についてのダッシュボードがあります
- パッチおよび更新プログラム (P&U) 進行時の自動メンテナンス モード検出機能を備えています
- デプロイ用とリージョン用の強制更新タスクが含まれます
- リージョンにカスタム情報を追加できます
- 通知とレポートがサポートされています

Microsoft Azure Stack 用 System Center 管理パックと、関連するユーザー ガイドを[こちら](https://www.microsoft.com/en-us/download/details.aspx?id=55184)から、または Operations Manager から直接ダウンロードできます。

チケット発行ソリューションについては、System Center Service Manager と Operations Manager を統合できます。 統合された製品コネクタによって双方向通信が可能になり、それによって、Service Manager のサービス要求を解決したあとに Azure Stack と Operations Manager のアラートを終了できます。

次の図は、既存の System Center デプロイと Azure Stack の統合を示しています。 System Center Orchestrator または Service Management Automation (SMA) を使用してさらに Service Manager を自動化し、Azure Stack 内で操作を実行できます。

![OM、Service Manager、SMA との統合を示す図。](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Nagios との統合

Nagios 監視プラグインは、制約のない無料ソフトウェア ライセンスである MIT (Massachusetts Institute of Technology) ライセンスの下で使用できるパートナー クラウドベース ソリューションと合わせて開発されました。

このプラグインは Python で書かれており、正常性リソースプロバイダーの REST API を使用します。 また、Azure Stack でアラートを取得したり終了したりする基本的な機能を提供します。 System Center 管理パックと同じように、このプラグインで複数の Azure Stack デプロイを追加したり、通知を送信したりすることが可能になります。

このプラグインは、Nagios Enterprise と Nagios Core で使用可能です。 [こちら](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)からダウンロードできます。 このダウンロード サイトでインストールと詳細な構成も行えます。

### <a name="plugin-parameters"></a>プラグイン パラメーター

プラグイン ファイルの "Azurestack_plugin.py" を次のパラメーターで構成します。

| パラメーター | 説明 | 例 |
|---------|---------|---------|
| *arm_endpoint* | Azure Resource Manager (管理者) エンドポイント | https://adminmanagement.local.azurestack.external |
| *api_endpoint* | Azure Resource Manager (管理者) エンドポイント  | https://adminmanagement.local.azurestack.external |
| *Tenant_id* | 管理者のサブスクリプション ID | 管理者ポータルまたは PowerShell で取得します |
| *User_name* | オペレーターのサブスクリプション ユーザー名 | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | オペレーターのサブスクリプション パスワード | mypassword |
| *Client_id* | クライアント | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417* |
| *region* |  Azure Stack のリージョン名 | local |
|  |  |

* 指定の PowerShell GUID はユニバーサルです。 各デプロイ用に使用できます。

## <a name="use-powershell-to-monitor-health-and-alerts"></a>PowerShell を使用した正常性とアラートの監視

Operations Manager、Nagios、または Nagios ベースのソリューションを使用していない場合は、PowerShell でさまざまな監視ソリューションを使用して Azure Stack と統合できます。

1. PowerShell を使用するには、Azure Stack オペレーター環境用に [PowerShell がインストールされ構成されている](azure-stack-powershell-configure-quickstart.md)必要があります。 Resource Manager (管理者) エンドポイント (https://adminmanagement.[region].[External_FQDN]) にアクセスできるローカル コンピューターに PowerShell をインストールします。

2. 次のコマンドを実行して、Azure Stack オペレーターとして Azure Stack 環境に接続します。

   ```powershell
   Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN]

   Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```

3. 次の例のようなコマンドを使用して、アラートを操作します。
   ```powershell
    #Retrieve all alerts
    $Alerts = Get-AzsAlert
    $Alerts

    #Filter for active alerts
    $Active = $Alerts | Where-Object { $_.State -eq "active" }
    $Active

    #Close alert
    Close-AzsAlert -AlertID "ID"

    #Retrieve resource provider health
    $RPHealth = Get-AzsRPHealth
    $RPHealth

    #Retrieve infrastructure role instance health
    $FRPID = $RPHealth | Where-Object { $_.DisplayName -eq "Capacity" }
    Get-AzsRegistrationHealth -ServiceRegistrationId $FRPID.RegistrationId
    ```

## <a name="learn-more"></a>詳細情報

組み込みの正常性監視の詳細については、「[Azure Stack での正常性およびアラートの監視](azure-stack-monitor-health.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

[セキュリティの統合](azure-stack-integrate-security.md)
