---
title: Windows Virtual Desktop の必要な URL リスト - Azure
description: Windows Virtual Desktop のデプロイを意図したとおりに機能させるためにブロックを解除する必要がある URL の一覧。
author: Heidilohr
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 872a67d8d9f41c0c809df54304352b2a5f58e011
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98251762"
---
# <a name="required-url-list"></a>必要な URL リスト

Windows Virtual Desktop をデプロイし、使用するには、仮想マシン (VM) で特定の URL にいつでもアクセスできるよう、URL のブロックを解除する必要があります。 この記事では、Windows Virtual Desktop を正しく機能させるためにブロックを解除する必要がある、必要な URL の一覧を示します。 

>[!IMPORTANT]
>Windows Virtual Desktop では、この記事に含まれていない URL をブロックするデプロイはサポートされていません。

## <a name="virtual-machines"></a>仮想マシン

Windows Virtual Desktop 用に作成する Azure 仮想マシンには、Azure コマーシャル クラウドで次の URL に対するアクセスが必要です。

|Address|アウトバウンド TCP ポート|目的|サービス タグ|
|---|---|---|---|
|*.wvd.microsoft.com|443|サービス トラフィック|WindowsVirtualDesktop|
|gcs.prod.monitoring.core.windows.net|443|エージェント トラフィック|AzureCloud|
|production.diagnostics.monitoring.core.windows.net|443|エージェント トラフィック|AzureCloud|
|*xt.blob.core.windows.net|443|エージェント トラフィック|AzureCloud|
|*eh.servicebus.windows.net|443|エージェント トラフィック|AzureCloud|
|*xt.table.core.windows.net|443|エージェント トラフィック|AzureCloud|
|*xt.queue.core.windows.net|443|エージェント トラフィック|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Windows のライセンス認証|インターネット|
|mrsglobalsteus2prod.blob.core.windows.net|443|エージェントと SXS スタックの更新|AzureCloud|
|wvdportalstorageblob.blob.core.windows.net|443|Azure portal のサポート|AzureCloud|
| 169.254.169.254 | 80 | [Azure Instance Metadata Service エンドポイント](../virtual-machines/windows/instance-metadata-service.md) | 該当なし |
| 168.63.129.16 | 80 | [セッション ホストの正常性の監視](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | 該当なし |

>[!IMPORTANT]
>Windows Virtual Desktop で FQDN タグがサポートされるようになりました。 詳細については、「[Azure Firewall を使用して Windows Virtual Desktop のデプロイを保護する](../firewall/protect-windows-virtual-desktop.md)」を参照してください。
>
>サービスの問題を防ぐために、URL ではなく FQDN タグまたはサービス タグを使用することをお勧めします。 記載した URL とタグは、Windows Virtual Desktop のサイトとリソースにのみ対応します。 他のサービス (Azure Active Directory など) の URL は含まれません。

Windows Virtual Desktop 用に作成する Azure 仮想マシンには、Azure Government クラウドで次の URL に対するアクセスが必要です。

|Address|アウトバウンド TCP ポート|目的|サービス タグ|
|---|---|---|---|
|*.wvd.microsoft.us|443|サービス トラフィック|WindowsVirtualDesktop|
|gcs.monitoring.core.usgovcloudapi.net|443|エージェント トラフィック|AzureCloud|
|monitoring.core.usgovcloudapi.net|443|エージェント トラフィック|AzureCloud|
|fairfax.warmpath.usgovcloudapi.net|443|エージェント トラフィック|AzureCloud|
|*xt.blob.core.usgovcloudapi.net|443|エージェント トラフィック|AzureCloud|
|*.servicebus.usgovcloudapi.net|443|エージェント トラフィック|AzureCloud|
|*xt.table.core.usgovcloudapi.net|443|エージェント トラフィック|AzureCloud|
|Kms.core.usgovcloudapi.net|1688|Windows のライセンス認証|インターネット|
|mrsglobalstugviffx.blob.core.usgovcloudapi.net|443|エージェントと SXS スタックの更新|AzureCloud|
|wvdportalstorageblob.blob.core.usgovcloudapi.net|443|Azure portal のサポート|AzureCloud|
| 169.254.169.254 | 80 | [Azure Instance Metadata Service エンドポイント](../virtual-machines/windows/instance-metadata-service.md) | 該当なし |
| 168.63.129.16 | 80 | [セッション ホストの正常性の監視](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | 該当なし |

次の表は、Azure 仮想マシンからアクセスできるオプションの URL の一覧です。

|Address|アウトバウンド TCP ポート|目的|Azure Gov|
|---|---|---|---|
|*.microsoftonline.com|443|Microsoft オンライン サービスへの認証|login.microsoftonline.us|
|*.events.data.microsoft.com|443|テレメトリ サービス|なし|
|www.msftconnecttest.com|443|OS がインターネットに接続されているかどうかの検出|なし|
|*.prod.do.dsp.mp.microsoft.com|443|Windows Update|なし|
|login.windows.net|443|Microsoft Online Services、Microsoft 365 へのサインイン|login.microsoftonline.us|
|*.sfx.ms|443|OneDrive クライアント ソフトウェアの更新|oneclient.sfx.ms|
|*.digicert.com|443|証明書失効の確認|なし|
|*.azure-dns.com|443|Azure DNS 解決|なし|
|*.azure-dns.net|443|Azure DNS 解決|なし|

>[!NOTE]
>現在、Windows Virtual Desktop には、ネットワーク トラフィックを許可するためにブロックを解除できる IP アドレス範囲の一覧がありません。 現時点では、特定の URL のブロック解除のみがサポートされます。
>
>Next Generation Firewall (NGFW) を使用している場合、確実に接続できるよう、Azure IP のために特別に作られた動的リストを使用する必要があります。
>
>Office に関連した安全な URL (必須の Azure Active Directory 関連の URL など) の一覧については、「[Office 365 の URL と IP アドレスの範囲](/office365/enterprise/urls-and-ip-address-ranges)」を参照してください。
>
>サービス トラフィックに関係した URL にはワイルドカード文字 (*) を使用する必要があります。 エージェント関連のトラフィックに * を使用したくない場合、ワイルドカードを使わずに URL を見つける方法は次のとおりです。
>
>1. Windows Virtual Desktop ホスト プールに仮想マシンを登録します。
>2. **イベント ビューアー** を開き、 **[Windows ログ]**  >  **[アプリケーション]**  >  **[WVD-Agent]** に移動して、イベント ID 3701 を探します。
>3. イベント ID 3701 に記載されている URL をブロック解除します。 イベント ID 3701 に記載されている URL はリージョン固有です。 仮想マシンのデプロイ先となるリージョンごとに、適切な URL を使用して、ブロック解除プロセスを繰り返す必要があります。

## <a name="remote-desktop-clients"></a>リモート デスクトップ クライアント

使用するリモート デスクトップ クライアントから、次の URL にアクセスできる必要があります。

|Address|アウトバウンド TCP ポート|目的|クライアント|Azure Gov|
|---|---|---|---|---|
|*.wvd.microsoft.com|443|サービス トラフィック|All|*.wvd.microsoft.us|
|*.servicebus.windows.net|443|データのトラブルシューティング|All|*.servicebus.usgovcloudapi.net|
|go.microsoft.com|443|Microsoft の FWLink|All|なし|
|aka.ms|443|Microsoft URL 短縮ツール|All|なし|
|docs.microsoft.com|443|ドキュメント|All|なし|
|privacy.microsoft.com|443|プライバシー ステートメント|All|なし|
|query.prod.cms.rt.microsoft.com|443|クライアント更新|Windows デスクトップ|None|

>[!IMPORTANT]
>信頼できるクライアント エクスペリエンスを実現するには、これらの URL を開くことが不可欠です。 これらの URL へのアクセスをブロックすることはサポート対象外であり、サービスの機能にも支障が生じます。
>
>これらの URL はクライアントのサイトとリソースにのみ対応します。 この一覧には、他のサービス (Azure Active Directory など) の URL は含まれません。 Azure Active Directory URL は「[Office 365 URL および IP アドレス範囲](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)」の ID 56 にあります。