---
title: Windows Virtual Desktop に関する FAQ - Azure
description: Windows Virtual Desktop のよく寄せられる質問とベスト プラクティス。
author: Heidilohr
ms.topic: conceptual
ms.date: 10/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3bdb38b8a9590cf6191c75fdef024543c2b1c190
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720275"
---
# <a name="windows-virtual-desktop-faq"></a>Windows Virtual Desktop の FAQ

この記事では、よく寄せられる質問に回答し、Windows Virtual Desktop のベスト プラクティスについて説明します。

## <a name="what-are-the-minimum-admin-permissions-i-need-to-manage-objects"></a>オブジェクトを管理するために必要な、最少管理者権限は何ですか。

ホスト プールとその他のオブジェクトを作成する場合は、使用しているサブスクリプションまたはリソース グループの共同作成者ロールが割り当てられている必要があります。

アプリ グループをユーザーまたはユーザー グループに公開するには、アプリ グループのユーザー アクセス管理者ロールが割り当てられている必要があります。

ユーザーへのメッセージの送信、ユーザーのサインアウトなど、管理者がユーザー セッションの管理のみを行えるよう制限するには、カスタム ロールを作成します。 次に例を示します。

```powershell
"actions": [
"Microsoft.Resources/deployments/operations/read",
"Microsoft.Resources/tags/read",
"Microsoft.Authorization/roleAssignments/read",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/*",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/write"
],
"notActions": [],
"dataActions": [],
"notDataActions": []
}
```

## <a name="does-windows-virtual-desktop-support-split-azure-active-directory-models"></a>Windows Virtual Desktop では Azure Active Directory モデルの分割はサポートされていますか。

ユーザーがアプリ グループに割り当てられると、サービスにより単純な Azure ロールの割り当てが行われます。 そのため、ユーザーの Azure Active Directory (AD) とアプリ グループの Azure AD は同じ場所にある必要があります。 ホスト プール、アプリ グループ、ワークスペースなどのすべてのサービス オブジェクトも、ユーザーと同じ Azure AD に含まれている必要があります。

同じ仮想ネットワーク (VNET) 内のユーザーの Azure AD と Active Directory が同期されている限り、異なる Azure AD に仮想マシン (VM) を作成できます。

## <a name="what-are-location-restrictions"></a>場所の制限とは何ですか。

すべてのサービス リソースには、場所が関連付けられています。 ホスト プールの場所によって、ホスト プールのサービス メタデータが格納される地理的な場所が決まります。 ホスト プールなしで、アプリ グループは存在できません。 アプリを RemoteApp アプリ グループに追加する場合は、スタート メニュー アプリを決定するためのセッション ホストも必要になります。 アプリ グループ アクションの場合は、ホスト プール上の関連データ アクセスも必要になります。 データが複数の場所に転送されないようにするには、アプリ グループの場所をホスト プールと同じ場所にする必要があります。

また、ワークスペースもそのアプリ グループと同じ場所にある必要があります。 ワークスペースが更新されるたびに、関連するアプリ グループが更新されます。 アプリ グループと同様に、サービスでは、すべてのワークスペースが同じ場所で作成されたアプリ グループに関連付けられている必要があります。

## <a name="how-do-you-expand-an-objects-properties-in-powershell"></a>PowerShell でオブジェクトのプロパティを展開するにはどうすればよいですか。

PowerShell コマンドレットを実行すると、リソース名と場所のみが表示されます。

次に例を示します。

```powershell
Get-AzWvdHostPool -Name 0224hp -ResourceGroupName 0224rg

Location Name   Type
-------- ----   ----
westus   0224hp Microsoft.DesktopVirtualization/hostpools
```

リソースのすべてのプロパティを表示するには、`format-list` または `fl` をコマンドレットの最後に追加します。

次に例を示します。

```powershell
Get-AzWvdHostPool -Name 0224hp -ResourceGroupName 0224rg |fl
```

特定のプロパティを表示するには `format-list` または `fl` の後に、その特定のプロパティ名を追加します。

次に例を示します。

```powershell
Get-AzWvdHostPool -Name demohp -ResourceGroupName 0414rg |fl CustomRdpProperty

CustomRdpProperty : audiocapturemode:i:0;audiomode:i:0;drivestoredirect:s:;redirectclipboard:i:1;redirectcomports:i:0;redirectprinters:i:1;redirectsmartcards:i:1;screen modeid:i:2;
```

## <a name="does-windows-virtual-desktop-support-guest-users"></a>Windows Virtual Desktop ではゲスト ユーザーはサポートされていますか。

Windows Virtual Desktop では Azure AD ゲスト ユーザー アカウントはサポートされていません。 たとえば、ゲスト ユーザーのグループが Microsoft 365 E3 (ユーザーごと)、Windows E3 (ユーザーごと) または WIN VDA ライセンスを会社で使用しているにも関わらず、別の会社の Azure AD のゲスト ユーザーであるとします。 もう一方の会社では、Azure AD と Active Directory の両方で、ゲスト ユーザーのユーザー オブジェクトをローカル アカウントのように管理します。

サード パーティのベネフィットになるように、独自のライセンスを使用することはできません。 また、Windows Virtual Desktop では、現在 Microsoft アカウント (MSA) はサポートされていません。

## <a name="why-dont-i-see-the-client-ip-address-in-the-wvdconnections-table"></a>WVDConnections テーブルにクライアント IP アドレスが表示されないのはなぜですか。

現在、Web クライアントの IP アドレスを収集する信頼性の高い方法がないため、テーブルにはその値が含まれていません。

## <a name="how-does-windows-virtual-desktop-handle-backups"></a>Windows Virtual Desktop でバックアップはどのように処理されますか。

Azure には、バックアップを処理するための複数のオプションがあります。 Azure Backup、Site Recovery、およびスナップショットを使用できます。

## <a name="does-windows-virtual-desktop-support-third-party-collaboration-apps"></a>Windows Virtual Desktop ではサード パーティのコラボレーション アプリはサポートされていますか。

Windows Virtual Desktop は、現在 Teams 向けに最適化されています。 Microsoft では現在、Zoom のようなサード パーティ コラボレーション アプリはサポートしていません。 サード パーティの組織は、お客様に互換性ガイドラインを提供する責任を負います。 Windows Virtual Desktop では、Skype for Business もサポートされていません。

## <a name="can-i-change-from-pooled-to-personal-host-pools"></a>プールから個人用ホスト プールに変更できますか。

ホスト プールを作成した後は、その種類を変更することはできません。 ただし、ホスト プールに登録したすべての VM を別の種類のホスト プールに移動することはできます。

## <a name="whats-the-largest-profile-size-fslogix-can-handle"></a>FSLogix で処理できる最大のプロファイル サイズは何ですか。

FSLogix の制限事項またはクォータは、ユーザー プロファイルの VHD(X) ファイルの格納に使用される記憶域ファブリックによって異なります。

次の表に、FSLogix プロファイルで各ユーザーをサポートするために必要なリソースの数を示します。 要件は、各プロファイルのユーザー、アプリケーション、およびアクティビティによって大きく異なる場合があります。

| リソース | 要件 |
|---|---|
| 安定状態での IOPS | 10 |
| サインイン/サインアウト時の IOPS | 50 |

この表の例は 1 人のユーザーの場合を示していますが、環境内のユーザーの合計数の要件を見積もるために使用できます。 たとえば、100 ユーザーの場合は 1000 IOPS、サインインとサインアウトの場合は 5000 IOPS が必要になります。

## <a name="is-there-a-scale-limit-for-host-pools-created-in-the-azure-portal"></a>Azure portal で作成されたホスト プールに、スケールの制限はありますか。

以下の要因が、ホスト プールのスケール制限に影響することがあります。

- Azure テンプレートでは、800 オブジェクトに制限されています。 詳しくは、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md#template-limits)」をご覧ください。 また、各 VM では約 6 個のオブジェクトが作成されるため、テンプレートを実行するたびに約 132 台の VM を作成できます。

- リージョンごと、およびサブスクリプションごとに作成できるコアの数には制限があります。 たとえば、Enterprise Agreement サブスクリプションがある場合は、350 コアを作成できます。 テンプレートを実行するたびに作成できる VM の数を決定するには、350 を VM あたりの既定のコア数または独自のコア制限のどちらかで割る必要があります。 詳細については、「[Virtual Machines の制限 - Azure リソース マネージャー](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits---azure-resource-manager)」を参照してください。

- VM のプレフィックス名と VM の数は 15 文字未満です。 詳細については、「[Azure リソースの名前付け規則と制限事項](../azure-resource-manager/management/resource-name-rules.md#microsoftcompute)」を参照してください。

## <a name="can-i-manage-windows-virtual-desktop-environments-with-azure-lighthouse"></a>Azure Lighthouse を使用して Windows Virtual Desktop 環境を管理できますか?

Azure Lighthouse では、Windows Virtual Desktop 環境の管理は完全にはサポートされていません。 Lighthouse では現在、Azure AD テナント間でのユーザー管理がサポートされていないため、Lighthouse のお客様はユーザーを管理するために使用する Azure AD にサインインする必要があります。

また、Windows Virtual Desktop サービスで CSP サンドボックス サブスクリプションを使用することもできません。 詳細については、「[統合サンドボックス アカウント](/partner-center/develop/set-up-api-access-in-partner-center#integration-sandbox-account)」を参照してください。

最後に、CSP の所有者アカウントからリソース プロバイダーを有効にした場合、CSP の顧客アカウントでリソース プロバイダーを変更することはできません。

## <a name="how-often-should-i-turn-my-vms-on-to-prevent-registration-issues"></a>登録の問題を防ぐには、VM をどれくらいの頻度でオンにする必要がありますか?

Windows Virtual Desktop サービス内のホスト プールに VM を登録した後は、VM がアクティブになるたびに、エージェントによって VM のトークンが定期的に更新されます。 登録トークンの証明書の有効期間は 90 日間です。 この 90 日間の期限を考慮し、お客様には、VM を 90 日おきに起動することをお勧めしています。 この期限内に VM をオンにすれば、登録トークンが期限切れになったり、無効になったりするのを回避できます。 90 日を過ぎた後に VM を起動し、登録に関する問題が発生した場合は、[Windows Virtual Desktop エージェントのトラブルシューティング ガイド](troubleshoot-agent.md#your-issue-isnt-listed-here-or-wasnt-resolved)に記載されている手順に従って、ホスト プールから VM を削除し、エージェントを再インストールした後、VM をプールに再登録してください。
