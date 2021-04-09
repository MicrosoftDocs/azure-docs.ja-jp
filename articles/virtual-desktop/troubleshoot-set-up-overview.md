---
title: Windows Virtual Desktop トラブルシューティングの概要 - Azure
description: Windows Virtual Desktop 環境の設定時の問題の解決方法の概要。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/04/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4d4bdc4fa15f634b36f12a650b70b9ffd89b40e0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98539118"
---
# <a name="troubleshooting-overview-feedback-and-support-for-windows-virtual-desktop"></a>Windows Virtual Desktop のトラブルシューティングの概要、フィードバック、およびサポート

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager Windows Virtual Desktop オブジェクトを含む Windows Virtual Desktop に適用されます。 Azure Resource Manager オブジェクトなしで Windows Virtual Desktop (classic) を使用している場合は、[この記事](./virtual-desktop-fall-2019/troubleshoot-set-up-overview-2019.md)を参照してください。

この記事では、Windows Virtual Desktop 環境の設定中に発生することがある問題の概要とその問題の解決方法について説明しています。

## <a name="report-issues"></a>レポートに関する問題

Azure Resource Manager 統合を使用して Windows Virtual Desktop の問題を報告したり、機能を提案したりするには、[Windows Virtual Desktop の Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) にアクセスしてください。 Tech Community を使用して、ベスト プラクティスについて話し合ったり、新機能を提案したり投票したりすることができます。

投稿でヘルプを要求したり、新しい機能を提案したりするときは、できるだけ詳細にトピックを記述してください。 詳細情報は、他のユーザーが質問に回答したり、投票を提案している機能を理解したりするのに役立ちます。

## <a name="escalation-tracks"></a>エスカレーション トラック

まず初めに、[Azure の状態のページ](https://status.azure.com/status)と [Azure Service Health](https://azure.microsoft.com/features/service-health/) を確認して、Azure サービスが正常に実行されていることを確認してください。

リモート デスクトップ クライアントを使用して環境を設定しているときに発生することがある問題を特定し、解決するために次の表をご利用ください。 環境が設定されたら、新しい[診断サービス](diagnostics-role-service.md)を使用して、一般的なシナリオに関する問題を識別できます。

| **問題点**                                                            | **推奨されている解決方法**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| セッション ホスト プール Azure Virtual Network (VNET) と ExpressRoute 設定               | [Azure サポート リクエストを開いて](https://azure.microsoft.com/support/create-ticket/)、適切なサービスを選択します ([ネットワーク] カテゴリの下で)。 |
| Windows Virtual Desktop で提供される Azure Resource Manager テンプレートが使用されないときのセッション ホスト プール仮想マシン (VM) 作成 | [Azure サポート リクエストを開いて](https://azure.microsoft.com/support/create-ticket/)、サービスとして **[Windows Virtual Desktop]** を選択します。 <br> <br> Windows Virtual Desktop で提供される Azure Resource Manager テンプレートの問題については、「[ホスト プールの作成](troubleshoot-set-up-issues.md)」の「Azure Resource Manager テンプレート エラー」セクションを参照してください。 |
| Azure portal から Windows Virtual Desktop セッション ホスト環境を管理する    | [Azure サポート リクエストを開きます](https://azure.microsoft.com/support/create-ticket/)。 <br> <br> リモート デスクトップ サービスまたは Windows Virtual Desktop PowerShell を使用する際の管理上の問題については、「[Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)」を参照するか、または、[Azure サポート リクエストを開き](https://azure.microsoft.com/support/create-ticket/)、サービスとして **[Windows Virtual Desktop]** を選択し、問題の種類として **[構成と管理]** を選択し、さらに問題のサブタイプとして **[Issues configuring environment using PowerShell]\(PowerShell を使用して環境を構成する際の問題\)** を選択してください。 |
| ホスト プールとアプリケーション グループ (アプリ グループ) に関連付けられている Windows Virtual Desktop 構成を管理する      | 「[Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)」を参照するか、または [Azure サポート リクエストを開き](https://azure.microsoft.com/support/create-ticket/)、サービスとして **[Windows Virtual Desktop]** を選択してから、該当する問題の種類を選択してください。|
| FSLogix プロファイル コンテナーのデプロイと管理 | 「[FSLogix 製品のトラブルシューティング ガイド](/fslogix/fslogix-trouble-shooting-ht/)」を参照してください。それでも問題が解決しない場合は、[Azure サポート リクエストを開き](https://azure.microsoft.com/support/create-ticket/)、サービスとして **[Windows Virtual Desktop]** を選択し、問題の種類として **[FSLogix]** を選択してから、該当する問題のサブタイプを選択してください。 |
| 起動時のリモート デスクトップ クライアントの誤作動                                                 | 「[リモート デスクトップ クライアントのトラブルシューティング](troubleshoot-client.md)」を参照してください。それでも問題が解決しない場合は、[Azure サポート リクエストを開き](https://azure.microsoft.com/support/create-ticket/)、サービスとして **[Windows Virtual Desktop]** を選択してから、問題の種類として **[リモート デスクトップ クライアント]** を選択してください。  <br> <br> ネットワーク問題の場合、ユーザーはネットワーク管理者に問い合わせる必要があります。 |
| 接続されたがフィードがない                                                                 | 「[Windows Virtual Desktop サービスの接続](troubleshoot-service-connection.md)」の「[ユーザーが接続しても何も表示されない (フィードなし)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed)」セクションに従ってトラブルシューティングを行います。 <br> <br> ユーザーがアプリ グループに割り当てられている場合は、[Azure サポート リクエストを開き](https://azure.microsoft.com/support/create-ticket/)、サービスとして **[Windows Virtual Desktop]** を選択してから、問題の種類として **[リモート デスクトップ クライアント]** を選択します。 |
| ネットワークに起因するフィード検出問題                                            | ユーザーはネットワーク管理者に問い合わせる必要があります。 |
| クライアントの接続                                                                    | [Windows Virtual Desktop サービス接続](troubleshoot-service-connection.md)に関するページを参照してください。それでも問題が解決されない場合は、「[セッション ホスト仮想マシンの構成](troubleshoot-vm-configuration.md)」を参照してください。 |
| リモート アプリケーションまたはデスクトップの応答性                                      | 問題が特定のアプリケーションまたは製品に関連する場合、その製品の担当チームにお問い合わせください。 |
| ライセンスに関するメッセージまたはエラー                                                          | 問題が特定のアプリケーションまたは製品に関連する場合、その製品の担当チームにお問い合わせください。 |
| サードパーティの認証方法またはツールに関する問題 | サードパーティのプロバイダーが Windows Virtual Desktop のシナリオをサポートしていることを確認し、既知の問題があれば、それらに対処します。 |
| Windows Virtual Desktop で Log Analytics を使用する場合の問題 | 診断スキーマに関する問題については、[Azure サポート リクエストをオープン](https://azure.microsoft.com/support/create-ticket/)してください。<br><br>Log Analytics のクエリ、視覚化、またはその他の問題については、Log Analytics の下で適切な問題の種類を選択してください。 |
| M365 アプリを使用する場合の問題 | [M365 管理センターのヘルプ オプション](/microsoft-365/admin/contact-support-for-business-products/)のいずれかを使用して、M365 管理センターに連絡してください。 |

## <a name="next-steps"></a>次のステップ

- Windows Virtual Desktop 環境でホスト プールを作成しているときに発生した問題のトラブルシューティングを行うには、「[ホスト プールの作成](troubleshoot-set-up-issues.md)」を参照してください。
- Windows Virtual Desktop で仮想マシン (VM) の構成中に発生した問題を解決するには、[Session host virtual machine configuration (セッション ホスト仮想マシンの構成)](troubleshoot-vm-configuration.md) に関する記事を参照してください。
- Windows Virtual Desktop エージェントまたはセッション接続に関連した問題をトラブルシューティングするには、「[Windows Virtual Desktop エージェントに関する一般的な問題をトラブルシューティングする](troubleshoot-agent.md)」を参照してください。
- Windows Virtual Desktop クライアント接続の問題をトラブルシューティングするには、[Windows Virtual Desktop サービスの接続](troubleshoot-service-connection.md)に関するページを参照してください。
- リモート デスクトップ クライアントの問題をトラブルシューティングするには、[リモート デスクトップ クライアントのトラブルシューティング](troubleshoot-client.md) に関するページを参照してください
- Windows Virtual Desktop で PowerShell を使用しているときに発生した問題を解決するには、「[Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)」を参照してください。
- サービスの詳細については、[Windows Virtual Desktop 環境](environment-setup.md)に関するページを参照してください。
- トラブルシューティング チュートリアルについては、「[Tutorial:Resource Manager テンプレート デプロイのトラブルシューティング](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)」を参照してください。
- 監査アクションについては、「 [リソース マネージャーの監査操作](../azure-resource-manager/management/view-activity-logs.md)」をご覧ください。
- デプロイ時にエラーが発生した場合の対応については、[デプロイ操作の確認](../azure-resource-manager/templates/deployment-history.md)に関するページを参照してください。
