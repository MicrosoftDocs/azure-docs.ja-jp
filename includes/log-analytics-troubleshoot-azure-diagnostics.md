---
author: mgoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 11/09/2018
ms.author: magoedte
ms.openlocfilehash: 6890c71ac7c265d46cc77751786fea4d0b228588
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/27/2020
ms.locfileid: "67181568"
---
### <a name="troubleshoot-azure-diagnostics"></a>Azure Diagnostics のトラブルシューティング

次のエラー メッセージが表示される場合は、Microsoft.insights リソース プロバイダーが登録されていません。

`Failed to update diagnostics for 'resource'. {"code":"Forbidden","message":"Please register the subscription 'subscription id' with Microsoft.Insights."}`

リソース プロバイダーを登録するには、Azure Portal で次の手順を実行します。

1.  左側のナビゲーション ウィンドウで、 *[サブスクリプション]* をクリックします。
2.  エラー メッセージで示されたサブスクリプションを選択します。
3.  *[リソース プロバイダー]* をクリックします。
4.  *Microsoft.insights* プロバイダーを探します。
5.  *[登録]* リンクをクリックします。

![microsoft.insights リソース プロバイダーの登録](./media/log-analytics-troubleshoot-azure-diagnostics/log-analytics-register-microsoft-diagnostics-resource-provider.png)

*Microsoft.insights* リソース プロバイダーが登録されたら、診断の構成を再試行してください。


PowerShell で次のエラー メッセージが表示された場合は、PowerShell のバージョンを更新する必要があります。

`Set-AzDiagnosticSetting : A parameter cannot be found that matches parameter name 'WorkspaceId'.`

お使いの Azure PowerShell バージョンを更新し、[Azure PowerShell のインストール](/powershell/azure/install-az-ps)に関する記事で説明されている手順に従ってください。
