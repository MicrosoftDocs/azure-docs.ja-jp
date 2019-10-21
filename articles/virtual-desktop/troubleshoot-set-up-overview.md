---
title: Windows Virtual Desktop トラブルシューティングの概要、フィードバック、サポート - Azure
description: Windows Virtual Desktop テナント環境の設定時の問題の解決方法の概要
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: b16d993717529953da1dc31604e6112f53ed7ac9
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679434"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>トラブルシューティングの概要、フィードバック、サポート

この記事では、Windows Virtual Desktop テナント環境の設定中に発生することがある問題の概要とその問題の解決方法について説明しています。

## <a name="provide-feedback"></a>フィードバックの提供

Windows Virtual Desktop サービスに関して製品チームや活発なコミュニティのメンバーと話をしたいときは、[Windows Virtual Desktop の技術コミュニティ](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)にアクセスしてください。

## <a name="escalation-tracks"></a>エスカレーション トラック

リモート デスクトップ クライアントを使用してテナント環境を設定しているときに発生することがある問題を特定し、解決するために次の表をご利用ください。 テナントが設定されたら、新しい[診断サービス](https://docs.microsoft.com/azure/virtual-desktop/diagnostics-role-service)を使用して、一般的なシナリオに関する問題を識別できます。

>[!NOTE]
> 弊社の Tech Community フォーラムにアクセスすると、製品チームやアクティブなコミュニティ メンバーと問題について話し合うことができます。 [Windows Virtual Desktop の Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) にアクセスしてください 

| **問題**                                                            | **推奨されている解決方法**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| テナントの作成                                                    | Azure が停止した場合は、[Azure サポート](https://azure.microsoft.com/support/options/)にお問い合わせいただくか、**Windows Virtual Desktop (Compute)** のサポート リクエストを開きます。|
| Azure portal で Marketplace テンプレートにアクセスする       | Azure が停止した場合、[Azure サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。 <br> <br> Azure Marketplace Windows Virtual Desktop テンプレートを無料で入手できます。|
| GitHub から Azure Resource Manager テンプレートにアクセスする                                  | 「[テナントとホスト プールの作成](troubleshoot-set-up-issues.md)」の「Windows Virtual Desktop セッション ホスト VM の作成」セクションをご覧ください。 問題が解決されない場合、[GitHub サポート チーム](https://github.com/contact)にお問い合わせください。 <br> <br> GitHub でテンプレートにアクセスした後にエラーが発生した場合、[Azure サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。|
| セッション ホスト プール Azure Virtual Network (VNET) と ExpressRoute 設定               | **Azure サポート (ネットワーク)** にお問い合わせください。 |
| Windows Virtual Desktop で提供される Azure Resource Manager テンプレートが使用されないときのセッション ホスト プール仮想マシン (VM) 作成 | **Azure サポート (コンピューティング)** にお問い合わせください。 <br> <br> Windows Virtual Desktop で提供される Azure Resource Manager テンプレートの問題については、「[テナントとホスト プールの作成](troubleshoot-set-up-issues.md)」の「Windows Virtual Desktop テナントの作成」セクションを参照してください。 |
| Azure portal から Windows Virtual Desktop セッション ホスト環境を管理する    | **Azure サポート**にお問い合わせください。 <br> <br> リモート デスクトップ サービス/Windows Virtual Desktop PowerShell 使用時の管理問題については、「[Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)」を参照するか、**Windows Virtual Desktop (Compute) のサポート リクエストを開きます**。 |
| ホスト プールとアプリケーション グループ (アプリ グループ) に関連付けられている Windows Virtual Desktop 構成を管理する      | 「[Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)」を参照するか、**Windows Virtual Desktop (Compute) のサポート リクエストを開きます**。 <br> <br> 問題がサンプルの GUI (グラフィカル ユーザー インターフェイス) に関連する場合、Yammer コミュニティにアクセスしてください。|
| 起動時のリモート デスクトップ クライアントの誤作動                                                 | 「[Remote Desktop client connections](troubleshoot-client-connection.md)」 (リモート デスクトップ クライアントの接続) を参照してください。それでも問題が解決されない場合は、**Windows Virtual Desktop (Compute) のサポート リクエストを開きます**。  <br> <br> ネットワーク問題の場合、ユーザーはネットワーク管理者に問い合わせる必要があります。 |
| 接続されたがフィードがない                                                                 | 「[リモート デスクトップ クライアントの接続](troubleshoot-client-connection.md)」の "User connects but nothing is displayed (no feed)" (ユーザーが接続したが何も表示されない (フィードがない)) を参照して問題を解決してください。 <br> <br> ユーザーがアプリ グループに割り当てられている場合は、**Windows Virtual Desktop (Compute) のサポート リクエストを開きます**。 |
| ネットワークに起因するフィード検出問題                                            | ユーザーはネットワーク管理者に問い合わせる必要があります。 |
| クライアントの接続                                                                    | 「[リモート デスクトップ クライアントの接続](troubleshoot-client-connection.md)」を参照してください。それでも問題が解決されない場合、「[セッション ホスト仮想マシンの構成](troubleshoot-vm-configuration.md)」を参照してください。 |
| リモート アプリケーションまたはデスクトップの応答性                                      | 問題が特定のアプリケーションまたは製品に関連する場合、その製品の担当チームにお問い合わせください。 |
| ライセンスに関するメッセージまたはエラー                                                          | 問題が特定のアプリケーションまたは製品に関連する場合、その製品の担当チームにお問い合わせください。 |

## <a name="next-steps"></a>次の手順

- Windows Virtual Desktop 環境でテナント/ホスト プールを作成しているときに発生した問題を解決するには、「[テナントとホスト プールの作成](troubleshoot-set-up-issues.md)」を参照してください。
- Windows Virtual Desktop で仮想マシン (VM) の構成中に発生した問題を解決するには、「[セッション ホスト仮想マシンの構成](troubleshoot-vm-configuration.md)」を参照してください。
- Windows Virtual Desktop クライアント接続の問題を解決するには、「[リモート デスクトップ クライアントの接続](troubleshoot-client-connection.md)」を参照してください。
- Windows Virtual Desktop で PowerShell を使用しているときに発生した問題を解決するには、「[Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)」を参照してください。
- サービスの詳細については、[Windows Virtual Desktop 環境](https://docs.microsoft.com/azure/virtual-desktop/environment-setup)に関するページを参照してください。
- トラブルシューティング チュートリアルについては、「[Tutorial:Resource Manager テンプレート デプロイのトラブルシューティング](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)」を参照してください。
- 監査アクションについては、「 [リソース マネージャーの監査操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)」をご覧ください。
- デプロイ時にエラーが発生した場合の対応については、 [デプロイ操作の確認](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)に関するページを参照してください。
