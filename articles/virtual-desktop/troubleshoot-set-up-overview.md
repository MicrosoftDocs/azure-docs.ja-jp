---
title: Windows Virtual Desktop トラブルシューティングの概要、フィードバック、サポート - Azure
description: Windows Virtual Desktop テナント環境の設定時の問題の解決方法の概要
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: b349d7f384c801e95d745f7a18535f9f5d13cf86
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605209"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>トラブルシューティングの概要、フィードバック、サポート

この記事では、Windows Virtual Desktop テナント環境の設定中に発生することがある問題の概要とその問題の解決方法について説明しています。

## <a name="provide-feedback"></a>フィードバックの提供

Windows Virtual Desktop がプレビューの間は、サポート ケースを受け付けていません。 Windows Virtual Desktop サービスに関して製品チームや活発なコミュニティのメンバーと話をしたいときは、[Windows Virtual Desktop の技術コミュニティ](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)にアクセスしてください。

## <a name="escalation-tracks"></a>エスカレーション トラック

リモート デスクトップ クライアントを使用してテナント環境を設定しているときに発生することがある問題を特定し、解決するために次の表をご利用ください。 テナントが設定されたら、新しい[診断サービス](https://docs.microsoft.com/azure/virtual-desktop/diagnostics-role-service)を使用して、一般的なシナリオに関する問題を識別できます。

>[!NOTE]
>Windows Virtual Desktop がプレビューの間は、サポート ケースを受け付けていません。 Windows Virtual Desktop サポートに言及している箇所については、当分の間、Tech Community フォーラムをご覧ください。 [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) にアクセスすると、製品チームや活発に参加しているコミュニティ メンバーと問題について話し合うことができます。 サポート問題を解決する必要がある場合、アクティビティ ID と問題が発生したおおよその時間枠を含めてください。

| **問題**                                                            | **推奨されている解決方法**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| テナントの作成                                                    | Azure が停止した場合、[Azure サポート](https://azure.microsoft.com/support/options/)か**リモート デスクトップ サービス/Windows Virtual Desktop サポート**にご連絡ください。|
| Azure portal で Marketplace テンプレートにアクセスする       | Azure が停止した場合、[Azure サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。 <br> <br> Azure Marketplace Windows Virtual Desktop テンプレートを無料で入手できます。|
| GitHub から Azure Resource Manager テンプレートにアクセスする                                  | 「[テナントとホスト プールの作成](troubleshoot-set-up-issues.md)」の「Windows Virtual Desktop セッション ホスト VM の作成」セクションをご覧ください。 問題が解決されない場合、[GitHub サポート チーム](https://github.com/contact)にお問い合わせください。 <br> <br> GitHub でテンプレートにアクセスした後にエラーが発生した場合、[Azure サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。|
| セッション ホスト プール Azure Virtual Network (VNET) と ExpressRoute 設定               | **Azure サポート (ネットワーク)** にお問い合わせください。 |
| Windows Virtual Desktop で提供される Azure Resource Manager テンプレートが使用されないときのセッション ホスト プール仮想マシン (VM) 作成 | **Azure サポート (コンピューティング)** にお問い合わせください。 <br> <br> Windows Virtual Desktop で提供される Azure Resource Manager テンプレートの問題については、「[テナントとホスト プールの作成](troubleshoot-set-up-issues.md)」の「Windows Virtual Desktop テナントの作成」セクションを参照してください。 |
| Azure portal から Windows Virtual Desktop セッション ホスト環境を管理する    | **Azure サポート**にお問い合わせください。 <br> <br> リモート デスクトップ サービス/Windows Virtual Desktop PowerShell 使用時の管理問題については、「[Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)」を参照するか、**リモート デスクトップ サービス/Windows Virtual Desktop サポート チーム**にお問い合わせください。 |
| ホスト プールとアプリケーション グループ (アプリ グループ) に関連付けられている Windows Virtual Desktop 構成を管理する      | 「[Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)」を参照するか、**リモート デスクトップ サービス/Windows Virtual Desktop サポート チーム**にお問い合わせください。 <br> <br> 問題がサンプルの GUI (グラフィカル ユーザー インターフェイス) に関連する場合、Yammer コミュニティにアクセスしてください。|
| 起動時のリモート デスクトップ クライアントの誤作動                                                 | 「[Windows Virtual Desktop PowerShell](troubleshoot-client-connection.md)」を参照してください。それでも問題が解決されない場合、**リモート デスクトップ サービス/Windows Virtual Desktop サポート チーム**にお問い合わせください。  <br> <br> ネットワーク問題の場合、ユーザーはネットワーク管理者に問い合わせる必要があります。 |
| 接続されたがフィードがない                                                                 | 「[リモート デスクトップ クライアントの接続](troubleshoot-client-connection.md)」の "User connects but nothing is displayed (no feed)" (ユーザーが接続したが何も表示されない (フィードがない)) を参照して問題を解決してください。 <br> <br> ユーザーがアプリ グループに割り当てられていない場合、**リモート デスクトップ サービス/Windows Virtual Desktop サポート チーム**に指示を求めてください。 |
| ネットワークに起因するフィード検出問題                                            | ユーザーはネットワーク管理者に問い合わせる必要があります。 |
| クライアントの接続                                                                    | 「[リモート デスクトップ クライアントの接続](troubleshoot-client-connection.md)」を参照してください。それでも問題が解決されない場合、「[セッション ホスト仮想マシンの構成](troubleshoot-vm-configuration.md)」を参照してください。 |
| リモート アプリケーションまたはデスクトップの応答性                                      | 問題が特定のアプリケーションまたは製品に関連する場合、その製品の担当チームにお問い合わせください。 |
| ライセンスに関するメッセージまたはエラー                                                          | 問題が特定のアプリケーションまたは製品に関連する場合、その製品の担当チームにお問い合わせください。 |

## <a name="next-steps"></a>次の手順

- Windows Virtual Desktop 環境でテナント/ホスト プールを作成しているときに発生した問題を解決するには、「[テナントとホスト プールの作成](troubleshoot-set-up-issues.md)」を参照してください。
- Windows Virtual Desktop で仮想マシン (VM) の構成中に発生した問題を解決するには、「[セッション ホスト仮想マシンの構成](troubleshoot-vm-configuration.md)」を参照してください。
- Windows Virtual Desktop クライアント接続の問題を解決するには、「[リモート デスクトップ クライアントの接続](troubleshoot-client-connection.md)」を参照してください。
- Windows Virtual Desktop で PowerShell を使用しているときに発生した問題を解決するには、「[Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)」を参照してください。
- プレビュー サービスの詳細については、「[Windows Virtual Desktop プレビュー環境](https://docs.microsoft.com/azure/virtual-desktop/environment-setup)」を参照してください。
- トラブルシューティング チュートリアルについては、「[Tutorial:Resource Manager テンプレート デプロイのトラブルシューティング](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)」を参照してください。
- 監査アクションについては、「 [リソース マネージャーの監査操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)」をご覧ください。
- デプロイ時にエラーが発生した場合の対応については、 [デプロイ操作の確認](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)に関するページを参照してください。