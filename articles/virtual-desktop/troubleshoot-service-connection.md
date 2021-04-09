---
title: Windows Virtual Desktop サービスの接続に関するトラブルシューティング - Azure
description: Windows Virtual Desktop テナント環境でサービス接続を設定するときの問題を解決する方法です。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 10/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 42502864cfed177adfe487e9c59247579628fec8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98539080"
---
# <a name="windows-virtual-desktop-service-connections"></a>Windows Virtual Desktop サービスの接続

>[!IMPORTANT]
>これは、Azure Resource Manager Windows Virtual Desktop オブジェクトを使用する Windows Virtual Desktop を対象としたコンテンツです。 Azure Resource Manager オブジェクトを使用しない Windows Virtual Desktop (クラシック) を使用している場合は、[この記事](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md)を参照してください。

Windows Virtual Desktop クライアントの接続に関する問題を解決するには、この記事を使います。

## <a name="provide-feedback"></a>フィードバックの提供

フィードバックを行い、製品チームや [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) のその他のアクティブなコミュニティメンバーと Windows Virtual Desktop Service について話し合うことができます。

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>ユーザーが接続しても何も表示されない (フィードなし)

ユーザーは、リモート デスクトップ クライアントを開始して認証を行うことができますが、Web 検出フィードでアイコンが何も表示されません。

1. 次のコマンド ラインを使用し、問題を報告しているユーザーがアプリケーション グループに割り当てられていることを確認します。

     ```powershell
     Get-AzRoleAssignment -SignInName <userupn>
     ```

2. ユーザーが正しい資格情報でサインインしていることを確認します。

3. Web クライアントを使っている場合は、キャッシュされた資格情報の問題がないことを確認します。

4. ユーザーが Azure Active Directory (AD) ユーザー グループのメンバーである場合は、そのユーザー グループが配布グループではなくセキュリティ グループであることを確認します。 Windows Virtual Desktop では Azure AD 配布グループはサポートされません。

## <a name="user-loses-existing-feed-and-no-remote-resource-is-displayed-no-feed"></a>ユーザーの既存のフィードが失われ、リモート リソースが表示されない (フィードなし)

このエラーは通常、ユーザーがサブスクリプションを 1 つの Azure AD テナントから別のテナントに移動した後に表示されます。 その結果、サービスは以前の Azure AD テナントに関連付けられたままになるため、ユーザーの割り当てを追跡できなくなります。

これを解決するために必要なことは、ユーザーをアプリ グループに再割り当てすることだけです。

これは、CSP プロバイダーによってサブスクリプションが作成されて顧客に譲渡された場合にも、発生する可能性があります。 これを解決するには、リソース プロバイダーを再登録します。

1. Azure portal にサインインします。
2. **[サブスクリプション]** に移動し、ご使用のサブスクリプションを選択します。
3. ページの左側にあるメニューで、 **[リソース プロバイダー]** を選択します。
4. **[Microsoft.DesktopVirtualization]** を見つけて選択し、 **[再登録]** を選択します。

## <a name="next-steps"></a>次のステップ

- Windows Virtual Desktop トラブルシューティングの概要とエスカレーション トラックについては、「[トラブルシューティングの概要、フィードバック、サポート](troubleshoot-set-up-overview.md)」を参照してください。
- Windows Virtual Desktop 環境を作成しているときや、Windows Virtual Desktop 環境でホスト プールを作成しているときに発生した問題を解決するには、[環境とホスト プールの作成](troubleshoot-set-up-issues.md)に関するページを参照してください。
- Windows Virtual Desktop で仮想マシン (VM) の構成中に発生した問題を解決するには、[Session host virtual machine configuration (セッション ホスト仮想マシンの構成)](troubleshoot-vm-configuration.md) に関する記事を参照してください。
- Windows Virtual Desktop エージェントまたはセッション接続に関連する問題のトラブルシューティングについては、「[Windows Virtual Desktop エージェントに関する一般的な問題をトラブルシューティングする](troubleshoot-agent.md)」を参照してください。
- Windows Virtual Desktop で PowerShell を使用しているときに発生した問題を解決するには、「[Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)」を参照してください。
- トラブルシューティング チュートリアルについては、「[Tutorial:Resource Manager テンプレート デプロイのトラブルシューティング](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)」を参照してください。
