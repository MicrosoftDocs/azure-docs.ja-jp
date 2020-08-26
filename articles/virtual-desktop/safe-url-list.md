---
title: Windows Virtual Desktop の安全な URL の一覧 - Azure
description: Windows Virtual Desktop のデプロイを意図したとおりに機能させるためにブロックを解除する必要がある URL の一覧。
author: Heidilohr
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d75d6ecf73dece6dad43367a7f869a1b8ec3d86b
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2020
ms.locfileid: "88135863"
---
# <a name="safe-url-list"></a>安全な URL リスト

Windows Virtual Desktop のデプロイが正しく機能するように、特定の URL のブロックを解除する必要があります。 この記事では、どの URL が安全であるか確認できるよう、これらの URL の一覧を示します。

## <a name="virtual-machines"></a>仮想マシン

Windows Virtual Desktop 用に作成する Azure 仮想マシンには、次の URL に対するアクセスが必要です。

|Address|アウトバウンド TCP ポート|目的|サービス タグ|
|---|---|---|---|
|*.wvd.microsoft.com|443|サービス トラフィック|WindowsVirtualDesktop|
|mrsglobalsteus2prod.blob.core.windows.net|443|エージェントと SXS スタックの更新|AzureCloud|
|*.core.windows.net|443|エージェント トラフィック|AzureCloud|
|*.servicebus.windows.net|443|エージェント トラフィック|AzureCloud|
|gcs.prod.monitoring.core.windows.net|443|エージェント トラフィック|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Windows のライセンス認証|インターネット|
|wvdportalstorageblob.blob.core.windows.net|443|Azure portal のサポート|AzureCloud|
| 169.254.169.254 | 80 | [Azure Instance Metadata Service エンドポイント](../virtual-machines/windows/instance-metadata-service.md) | 該当なし |
| 168.63.129.16 | 80 | [セッション ホストの正常性の監視](../virtual-network/security-overview.md#azure-platform-considerations) | 該当なし |

>[!IMPORTANT]
>Windows Virtual Desktop で FQDN タグがサポートされるようになりました。 詳細については、「[Azure Firewall を使用して Windows Virtual Desktop のデプロイを保護する](../firewall/protect-windows-virtual-desktop.md)」を参照してください。
>
>サービスの問題を防ぐために、URL ではなく FQDN タグまたはサービス タグを使用することをお勧めします。 記載した URL とタグは、Windows Virtual Desktop のサイトとリソースにのみ対応します。 他のサービス (Azure Active Directory など) の URL は含まれません。

次の表は、Azure 仮想マシンからアクセスできるオプションの URL の一覧です。

|Address|アウトバウンド TCP ポート|目的|サービス タグ|
|---|---|---|---|
|*.microsoftonline.com|443|Microsoft オンライン サービスへの認証|なし|
|*.events.data.microsoft.com|443|テレメトリ サービス|なし|
|www.msftconnecttest.com|443|OS がインターネットに接続されているかどうかの検出|なし|
|*.prod.do.dsp.mp.microsoft.com|443|Windows Update|なし|
|login.windows.net|443|Microsoft Online Services、Microsoft 365 へのサインイン|なし|
|*.sfx.ms|443|OneDrive クライアント ソフトウェアの更新|なし|
|*.digicert.com|443|証明書失効の確認|なし|

>[!NOTE]
>現在、Windows Virtual Desktop には、ネットワーク トラフィックを許可するためにブロックを解除できる IP アドレス範囲の一覧がありません。 現時点では、特定の URL のブロック解除のみがサポートされます。
>
>Office に関連した安全な URL (必須の Azure Active Directory 関連の URL など) の一覧については、「[Office 365 の URL と IP アドレスの範囲](/office365/enterprise/urls-and-ip-address-ranges)」を参照してください。
>
>サービス トラフィックに関係した URL にはワイルドカード文字 (*) を使用する必要があります。 エージェント関連のトラフィックに * を使用したくない場合、ワイルドカードを使わずに URL を見つける方法は次のとおりです。
>
>1. Windows Virtual Desktop ホスト プールに仮想マシンを登録します。
>2. **イベント ビューアー**を開き、 **[Windows ログ]**  >  **[アプリケーション]**  >  **[WVD-Agent]** に移動して、イベント ID 3701 を探します。
>3. イベント ID 3701 に記載されている URL をホワイトリストに登録します。 イベント ID 3701 に記載されている URL はリージョン固有です。 仮想マシンのデプロイ先となるリージョンごとに、適切な URL を使用して、ブロック解除プロセスを繰り返す必要があります。

## <a name="remote-desktop-clients"></a>リモート デスクトップ クライアント

使用するリモート デスクトップ クライアントから、次の URL にアクセスできる必要があります。

|Address|アウトバウンド TCP ポート|目的|クライアント|
|---|---|---|---|
|*.wvd.microsoft.com|443|サービス トラフィック|All|
|*.servicebus.windows.net|443|データのトラブルシューティング|All|
|go.microsoft.com|443|Microsoft の FWLink|All|
|aka.ms|443|Microsoft URL 短縮ツール|All|
|docs.microsoft.com|443|ドキュメント|All|
|privacy.microsoft.com|443|プライバシー ステートメント|All|
|query.prod.cms.rt.microsoft.com|443|クライアント更新|Windows デスクトップ|

>[!IMPORTANT]
>信頼できるクライアント エクスペリエンスを実現するには、これらの URL を開くことが不可欠です。 これらの URL へのアクセスをブロックすることはサポート対象外であり、サービスの機能にも支障が生じます。
>
>これらの URL はクライアントのサイトとリソースにのみ対応します。 この一覧には、他のサービス (Azure Active Directory など) の URL は含まれません。 Azure Active Directory URL は「[Office 365 URL および IP アドレス範囲](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)」の ID 56 にあります。
