---
title: "サービスを使用する場合やアプリを作成する場合の Azure と Azure Stack の主な違い | Microsoft Docs"
description: "Azure Stack でサービスを使用する場合やアプリを作成する場合に知っておくべき内容。"
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: c81f551d-c13e-47d9-a5c2-eb1ea4806228
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 1e170f320292e3dbe920907a4ed81ab0d1eb388b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="key-considerations-using-services-or-building-apps-for-azure-stack"></a>主な考慮事項: Azure Stack でのサービスの使用またはアプリの作成

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack でサービスを使用する場合やアプリを作成する場合、Azure Stack と Azure では違いがあることを理解する必要があります。 この記事では、ハイブリッド クラウド開発環境として Azure Stack をターゲットとする場合の主な考慮事項の概要を示します。

## <a name="overview"></a>概要

Azure Stack は、会社またはサービス プロバイダーのデータセンターからの Azure サービスの使用を可能にするハイブリッド クラウド プラットフォームです。 開発者は、Azure Stack で実行するアプリを作成できます。 その後、そのアプリを Azure Stack と Azure にデプロイできます。また、Azure Stack クラウドと Azure 間の接続を利用するハイブリッド アプリを実際に作成することもできます。

Azure Stack オペレーターは、ユーザーに使用可能なサービスと、サポートを受ける方法を知らせます。 これらのサービスはカスタマイズされたプランやオファーを通じて提供されます。

Azure に関する技術的な内容は、Azure Stack ではなく、Azure サービス用にアプリが開発されていることを前提としています。 アプリを作成して Azure Stack にデプロイする場合は、次のようないくつかの主な違いを理解する必要があります。

* Azure Stack では、Azure で利用可能なサービスと機能のサブセットが提供されます。
* 会社またはサービス プロバイダーは、提供するサービスを選択できます。 これには、カスタマイズされたサービスやアプリケーションが含まれます。 独自のカスタマイズされたドキュメントが提供される場合もあります。
* 適切な Azure Stack 固有のエンドポイント (ポータル アドレスや Azure Resource Manager エンドポイントの URL など) を使用する必要があります。
* Azure Stack でサポートされている PowerShell および API のバージョンを使用する必要があります。 これにより、確実に Azure Stack と Azure の両方でアプリが動作するようになります。

## <a name="cheat-sheet-high-level-differences"></a>チート シート: 違いの概要

次の表では、Azure Stack と Azure の違いの概要を示します。 Azure Stack 向けに開発する場合や、Azure Stack サービスを使用する場合は以下の点に注意してください。

| 領域 | Azure (グローバル) | Azure Stack |
| -------- | ------------- | ----------|
| 担当 | Microsoft | 組織またはサービス プロバイダー。|
| サポートに関する連絡先 | Microsoft | 統合システムについては、(組織またはサービス プロバイダーの) Azure Stack オペレーターにお問い合わせください。<br><br>Azure Stack Development Kit のサポートについては、[Microsoft フォーラム](https://social.msdn.microsoft.com/Forums/home?forum=azurestack)にアクセスして確認してください。 開発キットは評価環境として提供されるものであるため、Microsoft カスケード スタイル シート (CSS) を通じて公式なサポートは提供されません。
| 利用可能なサービス | [Azuer 製品](https://azure.microsoft.com/services/?b=17.04b)の一覧を参照してください。 利用可能なサービスは Azure リージョンによって異なります。 | Azure Stack では Azure サービスのサブセットがサポートされます。 提供される実際のサービスは、組織またはサービス プロバイダーによる選択内容によって異なります。
| Azure Resource Manager のエンドポイント* | https://management.azure.com | Azure Stack 統合システムの場合は、Azure Stack オペレーターによって提供されたエンドポイントを使用します。<br><br>開発キットの場合は、https://management.local.azurestack.external を使用します。
| ポータル URL* | [https://portal.azure.com](https://portal.azure.com) | Azure Stack 統合システムの場合は、Azure Stack オペレーターによって提供された URL にアクセスします。<br><br>開発キットの場合は、https://portal.local.azurestack.external を使用します。
| リージョン | デプロイ先のリージョンを選択することができます。 | Azure Stack 統合システムの場合は、システムで利用可能なリージョンを使用します。<br><br>開発キットの場合、リージョンは常に**ローカル**になります。
| リソース グループ | リソース グループは複数のリージョンにまたがることができます。 | 統合システムと開発キットのいずれも、リージョンは 1 つのみです。
|サポートされている名前空間、リソースの種類、および API のバージョン | 最新 (またはまだ非推奨ではない以前のバージョン)。 | Azure Stack では特定のバージョンがサポートされます。 この記事の「バージョンの要件」セクションを参照してください。
| | |

*Azure Stack オペレーターの場合、詳細については、[管理者ポータルの使用](../azure-stack-manage-portals.md)に関する記事および[管理の基本](../azure-stack-manage-basics.md)に関する記事をご覧ください。

## <a name="helpful-tools-and-best-practices"></a>便利なツールとベスト プラクティス
 
 Microsoft は、Azure Stack 向けの開発に役立ついくつかのツールとガイダンスを提供しています。

| 推奨 | 参照 | 
| -------- | ------------- | 
| 開発者用ワークステーションに適切なツールをインストールします。 | - [PowerShell のインストール](azure-stack-powershell-install.md)<br>- [ツールのダウンロード](azure-stack-powershell-download.md)<br>- [PowerShell の構成](azure-stack-powershell-configure-user.md)<br>- [Visual Studio のインストール](azure-stack-install-visual-studio.md) 
| 以下の情報を確認します。<br>- Azure Resource Manager テンプレートに関する考慮事項<br>- クイックスタート テンプレートを見つける方法<br>- Azure Stack 向けの開発で Azure を使用する場合に役立つポリシー モジュールの使用 | [Azure Stack 向けの開発](azure-stack-developer.md) | 
| テンプレートのベスト プラクティスを確認して、それに従います。 | [Resource Manager のクイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#best-practices)
| | |

## <a name="version-requirements"></a>バージョンの要件

Azure Stack では、特定のバージョンの Azure PowerShell と Azure サービス API がサポートされます。 アプリが Azure Stack と Azure の両方にデプロイできるように、サポートされるバージョンを使用する必要があります。

正しいバージョンの Azure PowerShell を使用していることを確認するには、[API バージョン プロファイル](azure-stack-version-profiles.md)を使用します。 使用可能な最新の API バージョン プロファイルを確認するには、使用している Azure Stack のビルドを知る必要があります。 この情報は、Azure Stack 管理者から取得できます。

>[!NOTE]
 Azure Stack Development Kit を使用しており、管理アクセス権がある場合は、[更新の管理](https://docs.microsoft.com/azure/azure-stack/azure-stack-updates#determine-the-current-version)に関するページの "現在のバージョンの確認" セクションを参照して、Azure Stack のビルドを確認してください。

その他の API の場合は、次の PowerShell コマンドを実行し、名前空間、リソースの種類、および Azure Stack サブスクリプションでサポートされている API のバージョンを出力します。 プロパティ レベルでも違いがある可能性があることに注意してください  (このコマンドを機能させるには、Azure Stack 環境用に PowerShell が既に[インストール](azure-stack-powershell-install.md)され、[構成](azure-stack-powershell-configure-user.md)されている必要があります。 Azure Stack オファーのサブスクリプションも必要です)。

 ```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

出力例 (一部): ![Get-AzureRmResourceProvider コマンドの出力例](media/azure-stack-considerations/image1.png)
 
## <a name="next-steps"></a>次のステップ

サービス レベルでの違いの詳細については、以下を参照してください。

* [Azure Stack の仮想マシンに関する考慮事項](azure-stack-vm-considerations.md)
* [Azure Stack のストレージに関する考慮事項](azure-stack-acs-differences.md)
* [Azure Stack ネットワークに関する考慮事項](azure-stack-network-differences.md)
