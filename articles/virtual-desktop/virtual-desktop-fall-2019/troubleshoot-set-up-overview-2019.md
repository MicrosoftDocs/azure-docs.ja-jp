---
title: Windows Virtual Desktop (クラシック) トラブルシューティングの概要 - Azure
description: Windows Virtual Desktop (クラシック) テナント環境の設定時の問題の解決方法の概要。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7b2220ffe92446338e1b9db9b329847d93830334
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88005433"
---
#  <a name="windows-virtual-desktop-classic-troubleshooting-overview-feedback-and-support"></a>Windows Virtual Desktop (クラシック) トラブルシューティングの概要、フィードバック、サポート

>[!IMPORTANT]
>この内容は、Azure Resource Manager Windows Virtual Desktop オブジェクトをサポートしていない Windows Virtual Desktop (クラシック) に適用されます。 Azure Resource Manager Windows Virtual Desktop オブジェクトを管理しようとしている場合は、[こちらの記事](../troubleshoot-set-up-overview.md)を参照してください。

この記事では、Windows Virtual Desktop テナント環境の設定中に発生することがある問題の概要とその問題の解決方法について説明しています。

## <a name="provide-feedback"></a>フィードバックの提供

Windows Virtual Desktop サービスに関して製品チームや活発なコミュニティのメンバーと話をしたいときは、[Windows Virtual Desktop の技術コミュニティ](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)にアクセスしてください。

## <a name="escalation-tracks"></a>エスカレーション トラック

リモート デスクトップ クライアントを使用してテナント環境を設定しているときに発生することがある問題を特定し、解決するために次の表をご利用ください。 テナントが設定されたら、新しい[診断サービス](diagnostics-role-service-2019.md)を使用して、一般的なシナリオに関する問題を識別できます。

>[!NOTE]
> 弊社の Tech Community フォーラムにアクセスすると、製品チームやアクティブなコミュニティ メンバーと問題について話し合うことができます。 [Windows Virtual Desktop の Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) にアクセスして、話し合いを始めてください。

| **問題点**                                                            | **推奨されている解決方法**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Windows Virtual Desktop テナントの作成                                                    | Azure が停止した場合は、[Azure サポート リクエストを開きます](https://azure.microsoft.com/support/create-ticket/)。それ以外の場合は [Azure サポート リクエストを開き](https://azure.microsoft.com/support/create-ticket/)、サービスとして **[Windows Virtual Desktop]** を選択し、問題の種類として **[デプロイ]** を選択し、さらに問題のサブタイプのとして、 **[Issues creating a Windows Virtual Desktop tenant]\(Windows Virtual Desktop テナントを作成する際の問題\)** を選択します。|
| Azure portal で Marketplace テンプレートにアクセスする       | Azure が停止した場合は、[Azure サポート リクエストを開きます](https://azure.microsoft.com/support/create-ticket/)。 <br> <br> Azure Marketplace Windows Virtual Desktop テンプレートを無料で入手できます。|
| GitHub から Azure Resource Manager テンプレートにアクセスする                                  | 「[テナントとホスト プールの作成](troubleshoot-set-up-issues-2019.md)」の「[Windows Virtual Desktop セッション ホスト VM の作成](troubleshoot-set-up-issues-2019.md#creating-windows-virtual-desktop-session-host-vms)」セクションをご覧ください。 問題が解決されない場合、[GitHub サポート チーム](https://github.com/contact)にお問い合わせください。 <br> <br> GitHub でテンプレートにアクセスした後にエラーが発生した場合、[Azure サポート](https://azure.microsoft.com/support/create-ticket/)にお問い合わせください。|
| セッション ホスト プール Azure Virtual Network (VNET) と ExpressRoute 設定               | [Azure サポート リクエストを開いて](https://azure.microsoft.com/support/create-ticket/)、適切なサービスを選択します ([ネットワーク] カテゴリの下で)。 |
| Windows Virtual Desktop で提供される Azure Resource Manager テンプレートが使用されないときのセッション ホスト プール仮想マシン (VM) 作成 | [Azure サポート リクエストを開いて](https://azure.microsoft.com/support/create-ticket/)、サービスとして **[Windows を実行している仮想マシン]** を選択します。 <br> <br> Windows Virtual Desktop で提供される Azure Resource Manager テンプレートの問題については、「[テナントとホスト プールの作成](troubleshoot-set-up-issues-2019.md)」の「Windows Virtual Desktop テナントの作成」セクションを参照してください。 |
| Azure portal から Windows Virtual Desktop セッション ホスト環境を管理する    | [Azure サポート リクエストを開きます](https://azure.microsoft.com/support/create-ticket/)。 <br> <br> リモート デスクトップ サービスまたは Windows Virtual Desktop PowerShell を使用する際の管理上の問題については、「[Windows Virtual Desktop PowerShell](troubleshoot-powershell-2019.md)」を参照するか、または、[Azure サポート リクエストを開き](https://azure.microsoft.com/support/create-ticket/)、サービスとして **[Windows Virtual Desktop]** を選択し、問題の種類として **[構成と管理]** を選択し、さらに問題のサブタイプとして **[Issues configuring tenant using PowerShell]\(PowerShell を使用してテナントを構成する際の問題\)** を選択してください。 |
| ホスト プールとアプリケーション グループ (アプリ グループ) に関連付けられている Windows Virtual Desktop 構成を管理する      | 「[Windows Virtual Desktop PowerShell](troubleshoot-powershell-2019.md)」を参照するか、または [Azure サポート リクエストを開き](https://azure.microsoft.com/support/create-ticket/)、サービスとして **[Windows Virtual Desktop]** を選択してから、該当する問題の種類を選択してください。|
| FSLogix プロファイル コンテナーのデプロイと管理 | 「[FSLogix 製品のトラブルシューティング ガイド](/fslogix/fslogix-trouble-shooting-ht/)」を参照してください。それでも問題が解決しない場合は、[Azure サポート リクエストを開き](https://azure.microsoft.com/support/create-ticket/)、サービスとして **[Windows Virtual Desktop]** を選択し、問題の種類として **[FSLogix]** を選択してから、該当する問題のサブタイプを選択してください。 |
| 起動時のリモート デスクトップ クライアントの誤作動                                                 | 「[リモート デスクトップ クライアントのトラブルシューティング](../troubleshoot-client.md)」を参照してください。それでも問題が解決しない場合は、[Azure サポート リクエストを開き](https://azure.microsoft.com/support/create-ticket/)、サービスとして **[Windows Virtual Desktop]** を選択してから、問題の種類として **[リモート デスクトップ クライアント]** を選択してください。  <br> <br> ネットワーク問題の場合、ユーザーはネットワーク管理者に問い合わせる必要があります。 |
| 接続されたがフィードがない                                                                 | 「[Windows Virtual Desktop サービスの接続](troubleshoot-service-connection-2019.md)」の「[ユーザーが接続しても何も表示されない (フィードなし)](troubleshoot-service-connection-2019.md#user-connects-but-nothing-is-displayed-no-feed)」セクションに従ってトラブルシューティングを行います。 <br> <br> ユーザーがアプリ グループに割り当てられている場合は、[Azure サポート リクエストを開き](https://azure.microsoft.com/support/create-ticket/)、サービスとして **[Windows Virtual Desktop]** を選択してから、問題の種類として **[リモート デスクトップ クライアント]** を選択します。 |
| ネットワークに起因するフィード検出問題                                            | ユーザーはネットワーク管理者に問い合わせる必要があります。 |
| クライアントの接続                                                                    | [Windows Virtual Desktop サービス接続](troubleshoot-service-connection-2019.md)に関するページを参照してください。それでも問題が解決されない場合は、「[セッション ホスト仮想マシンの構成](troubleshoot-vm-configuration-2019.md)」を参照してください。 |
| リモート アプリケーションまたはデスクトップの応答性                                      | 問題が特定のアプリケーションまたは製品に関連する場合、その製品の担当チームにお問い合わせください。 |
| ライセンスに関するメッセージまたはエラー                                                          | 問題が特定のアプリケーションまたは製品に関連する場合、その製品の担当チームにお問い合わせください。 |
| GitHub で Windows Virtual Desktop ツール (Azure Resource Manager テンプレート、診断ツール、管理ツール) を使用するときに発生する問題 | 問題の報告方法については、[リモート デスクトップ サービス向け Azure Resource Manager テンプレート](https://github.com/Azure/RDS-Templates/blob/master/README.md)に関するページを参照してください。 |

## <a name="next-steps"></a>次のステップ

- Windows Virtual Desktop 環境でテナント/ホスト プールを作成しているときに発生した問題を解決するには、「[Tenant and host pool creation](troubleshoot-set-up-issues-2019.md)」 (テナントとホスト プールの作成) を参照してください。
- Windows Virtual Desktop で仮想マシン (VM) の構成中に発生した問題を解決するには、[Session host virtual machine configuration (セッション ホスト仮想マシンの構成)](troubleshoot-vm-configuration-2019.md) に関する記事を参照してください。
- Windows Virtual Desktop クライアント接続の問題をトラブルシューティングするには、[Windows Virtual Desktop サービスの接続](troubleshoot-service-connection-2019.md)に関するページを参照してください。
- リモート デスクトップ クライアントの問題をトラブルシューティングするには、[リモート デスクトップ クライアントのトラブルシューティング](../troubleshoot-client.md) に関するページを参照してください
- Windows Virtual Desktop で PowerShell を使用しているときに発生した問題を解決するには、「[Windows Virtual Desktop PowerShell](troubleshoot-powershell-2019.md)」を参照してください。
- サービスの詳細については、[Windows Virtual Desktop 環境](environment-setup-2019.md)に関するページを参照してください。
- トラブルシューティング チュートリアルについては、「[Tutorial:Resource Manager テンプレート デプロイのトラブルシューティング](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md)」を参照してください。
- 監査アクションについては、「 [リソース マネージャーの監査操作](../../azure-resource-manager/management/view-activity-logs.md)」をご覧ください。
- デプロイ時にエラーが発生した場合の対応については、[デプロイ操作の確認](../../azure-resource-manager/templates/deployment-history.md)に関するページを参照してください。
