---
title: Windows Virtual Desktop の RDP Shortpath (プレビュー)
titleSuffix: Azure
description: Windows Virtual Desktop の RDP Shortpath (プレビュー) を設定する方法。
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: 295a46f6d1074ddf8422233ea3ccfa4d65c28fd8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571583"
---
# <a name="windows-virtual-desktop-rdp-shortpath-preview"></a>Windows Virtual Desktop の RDP Shortpath (プレビュー)

> [!IMPORTANT]
> RDP Shortpath は、現在パブリック プレビュー段階にあります。
> このプレビューはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

RDP Shortpath は、リモート デスクトップ クライアントとセッション ホストとの間に直接的な UDP ベースのトランスポートを確立する Windows Virtual Desktop の機能です。 RDP では、このトランスポートを使用して、より高い信頼性と一貫した待機時間で、リモート デスクトップと RemoteApp を提供します。

## <a name="key-benefits"></a>主な利点

* RDP Shortpath トランスポートは、非常に効率的な [Universal Rate Control Protocol (URCP)](https://www.microsoft.com/en-us/research/publication/urcp-universal-rate-control-protocol-for-real-time-communication-applications/) に基づいています。 URCP では、ネットワークの状態をアクティブに監視することで UDP を強化し、公平で完全なリンク使用率を実現します。 URCP は、リモート デスクトップの必要に応じて、低遅延および低損失レベルで動作します。 URCP を使用すると、ネットワーク パラメーターを動的に学習し、レート制御メカニズムを備えたプロトコルを提供することにより、最高のパフォーマンスが達成されます。
* RDP Shortpath は、リモート デスクトップ クライアントとセッション ホストとの間に直接接続を確立します。 直接接続により、Windows Virtual Desktop ゲートウェイ上の依存関係が軽減され、接続の信頼性が向上し、各ユーザー セッションに使用できる帯域幅が増加します。
* 追加のリレーを削除すると、ラウンド トリップ時間が短縮され、待機時間の影響を受けやすいアプリケーションと入力方式のユーザー エクスペリエンスが向上します。
* RDP Shortpath では、DSCP (Differentiated Services Code Point) マークを使用した RDP 接続の [サービスの品質 (QoS) 優先度の構成](./rdp-quality-of-service-qos.md)がサポートされます
* RDP Shortpath トランスポートを使用することにより、各セッションのスロットル率を指定することで、[送信ネットワーク トラフィックを制限する](./rdp-bandwidth.md#limit-network-bandwidth-use-with-throttle-rate)ことができます。

## <a name="connection-security"></a>接続のセキュリティ

RDP Shortpath により、RDP マルチトランスポート機能が拡張されます。 逆方向接続トランスポートは置き換えられませんが、補完されます。 初期セッション ブローカリングはすべて、Windows Virtual Desktop インフラストラクチャを介して管理されます。

UDP ポート 3390 は、逆方向接続トランスポートを介して認証される受信 Shortpath トラフィックにのみ使用されます。 RDP Shortpath リスナーでは、逆方向接続セッションと一致しない限り、リスナーへの接続試行はすべて無視されます。

RDP Shortpath では、セッション ホストの証明書を使用して、クライアントとセッション ホスト間の TLS 接続を使用します。 既定では、RDP 暗号化に使用される証明書は、デプロイ中に OS によって自己生成されます。 必要に応じて、顧客は企業の証明機関によって発行され、一元管理された証明書を展開できます。 証明書の構成の詳細については、[Windows Server ドキュメント](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations)を参照してください。

## <a name="rdp-shortpath-connection-sequence"></a>RDP Shortpath の接続シーケンス

[逆方向接続トランスポート](./network-connectivity.md)をインストールすると、クライアントとセッション ホストにより、RDP 接続が確立され、マルチトランスポート機能がネゴシエートされます。 以下では、追加の手順について説明します。

1. セッション ホストにより、プライベートおよびパブリックの IPv4 と IPv6 のアドレスの一覧がクライアントに送信されます。
2. クライアントでは、バックグラウンド スレッドが開始され、ホストの IP アドレスの 1 つに対して、UDP ベースの並列トランスポートが直接確立されます。
3. クライアントは、指定された IP アドレスを調査している間、逆方向接続トランスポートを介して初期接続の確立を続行し、ユーザー接続の遅延が発生しないようにします。
4. クライアントに直接の通信経路があり、ファイアウォールの構成が正しい場合、クライアントによって、セッション ホストとの安全な TLS 接続が確立されます。
5. RDP は、Shortpath トランスポートを確立した後、リモート グラフィックス、入力、デバイス リダイレクトを含むすべての動的仮想チャネル (DVC) を新しいトランスポートに移動します。
6. ファイアウォールまたはネットワーク トポロジにより、クライアントが直接 UDP 接続を確立できない場合、RDP は逆方向接続トランスポートを続行します。

次の図は、RDP Shortpath ネットワーク接続の概要を示しています。

:::image type="content" source="media/rdp-shortpath-connections.svg" alt-text="RDP Shortpath ネットワーク接続の図" lightbox="media/rdp-shortpath-connections.svg":::

## <a name="requirements"></a>要件

RDP Shortpath をサポートするには、Windows Virtual Desktop クライアントに、セッション ホストへの直接の通信経路が必要です。 直接の通信経路を取得するには、次のテクノロジのいずれかを使用します。

* [ExpressRoute プライベート ピアリング](../expressroute/expressroute-circuit-peerings.md)
* [サイト間 VPN (IPsec ベース)](../vpn-gateway/tutorial-site-to-site-portal.md)
* [ポイント間 VPN (IPsec ベース)](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [パブリック IP アドレスの割り当て](../virtual-network/virtual-network-public-ip-address.md)

他の VPN の種類を使用して Azure 仮想ネットワークに接続する場合、最善の結果を得るには、UDP ベースの VPN を使用することをお勧めします。 多くの TCP ベースの VPN ソリューションでは、UDP を含むすべての IP パケットがカプセル化されますが、TCP 輻輳制御の継承されたオーバーヘッドが追加され、RDP のパフォーマンスを低下させます。

直接の通信経路とは、ファイアウォールによって UDP ポート 3390 がブロックされず、クライアントがセッション ホストに直接接続できることを意味します。

## <a name="enabling-rdp-shortpath-preview"></a>RDP Shortpath プレビューの有効化

RDP Shortpath のプレビューに参加するには、セッション ホストで RDP Shortpath リスナーを有効にする必要があります。 ご利用の環境内で使用されている任意の数のセッション ホストで RDP Shortpath を有効にすることができます。 プール内のすべてのホストで RDP Shortpath を有効にする必要はありません。
Shortpath リスナーを有効にするには、次のレジストリ値を構成する必要があります。

> [!WARNING]
> レジストリ エディターまたは別の方法を使用してレジストリを誤って変更すると、重大な問題が発生する場合があります。 このような問題が発生した場合、オペレーティング システムの再インストールが必要になることがあります。 Microsoft はそのような問題が解決できることを保証できません。 レジストリを変更する場合は、ご自分の責任で行ってください。

1. セッション ホストでは、Regedit.exe を起動し、次の場所に移動します。

    ```cmd
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations
    ```

2. **fUseUdpPortRedirector** という名前の新しい **DWORD** 値を作成し、**1** (10 進) に設定します
3. **UdpPortNumber** という名前の新しい **DWORD** 値を作成し、**3390** (10 進) に設定します。
4. レジストリ エディターを終了します。
5. セッション ホストを再起動します

また、管理者特権での PowerShell ウィンドウで次のコマンドレットを実行して、これらのレジストリ値を設定できます。

```powershell
$WinstationsKey = 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations'
New-ItemProperty -Path $WinstationsKey -Name 'fUseUdpPortRedirector' -ErrorAction:SilentlyContinue -PropertyType:dword -Value 1 -Force
New-ItemProperty -Path $WinstationsKey -Name 'UdpPortNumber' -ErrorAction:SilentlyContinue -PropertyType:dword -Value 3390 -Force
```

さらに、PowerShell を使用してグループ ポリシーを構成することもできます

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "RDP Shortpath Policy"
Set-GPPrefRegistryValue -Key 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations' -ValueName 'fUseUdpPortRedirector' -Value 1 -Type:DWord  -Action:Create -Context:Computer -Name $policyName -Domain $domainName
Set-GPPrefRegistryValue -Key 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations' -ValueName 'UdpPortNumber' -Value 3390 -Type:DWord  -Action:Create -Context:Computer -Name $policyName -Domain $domainName
```

## <a name="configure-windows-defender-firewall-with-advanced-security"></a>[セキュリティが強化された Windows ファイアウォール] を構成する

RDP Shortpath の受信ネットワーク トラフィックを許可するには、グループ ポリシー管理 MMC スナップインの [セキュリティが強化された Windows Defender ファイアウォール] ノードを使用して、ファイアウォール規則を作成します。

1. [セキュリティが強化された Windows Defender ファイアウォール](/windows/security/threat-protection/windows-firewall/open-the-group-policy-management-console-to-windows-firewall-with-advanced-security)のグループ ポリシー管理コンソールを開きます。
2. ナビゲーション ウィンドウで、 **[受信規則]** をクリックします。
3. **[操作]** 、 **[新しい規則]** の順に選択します。
4. 新規の受信の規則ウィザードの **[規則の種類]** ページで、 **[カスタム]** 、 **[次へ]** の順に選択します。
5. **[プログラム]** ページで、 **[このプログラムのパス]** を選択し、「%SystemRoot%\system32\svchost.exe」と入力して、 **[次へ]** を選択します。
6. **[プロトコルおよびポート]** ページで、UDP プロトコルの種類を選択します。 **[ローカル ポート]** で、[特定のポート] を選択し、「3390」と入力します。
7. [**スコープ**] ページでは、このページで入力した IP アドレスに対する送受信ネットワーク トラフィックにのみ規則が適用されるよう指定できます。 設計に適した構成を行い、 **[次へ]** を選択します。
8. **[操作]** ページで、 **[接続を許可する]** を選択し、 **[次へ]** を選択します。
9. **[プロファイル]** ページで、この規則を適用するネットワークの場所の種類を選択し、 **[次へ]** をクリックします。
10. **[名前]** ページで、規則の名前と説明を入力して、 **[完了]** をクリックします。

新しい規則が次のスクリーンショットと一致していることを確認できます。:::image type="content" source="media/rdp-shortpath-firewall-general-tab.png" alt-text="RDP Shortpath ネットワーク接続のファイアウォールを構成するための [全般] タブのスクリーンショット" lightbox="media/rdp-shortpath-firewall-general-tab.png":::

:::image type="content" source="media/rdp-shortpath-firewall-service-settings.png" alt-text="RDP Shortpath ネットワーク接続のファイアウォールを構成するための [プログラムおよびサービス] タブのスクリーンショット" lightbox="media/rdp-shortpath-firewall-service-settings.png":::

:::image type="content" source="media/rdp-shortpath-firewall-protocol-and-ports.png" alt-text="RDP Shortpath ネットワーク接続のファイアウォールを構成するための [プロトコルおよびポート] タブのスクリーンショット" lightbox="media/rdp-shortpath-firewall-protocol-and-ports.png":::

また、PowerShell を使用して Windows ファイアウォールを構成することができます。

```powershell
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP'  -PolicyStore PersistentStore -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True
```

### <a name="using-powershell-to-configure-windows-defender-firewall"></a>PowerShell を使用した Windows Defender ファイアウォールの構成

さらに、PowerShell を使用してグループ ポリシーを構成することもできます

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "RDP Shortpath Policy"
$gpoSession = Open-NetGPO -PolicyStore "$domainName\$policyName"
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP' -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True -GPOSession $gpoSession
Save-NetGPO -GPOSession $gpoSession
```

## <a name="configuring-azure-network-security-group"></a>Azure ネットワーク セキュリティ グループの構成

ネットワーク セキュリティ境界を越えて RDP Shortpath リスナーへのアクセスを許可するには、Azure ネットワーク セキュリティ グループを構成して受信 UDP ポート 3390 を許可する必要があります。
[ネットワーク セキュリティ グループのドキュメント](../virtual-machines/windows/nsg-quickstart-portal.md)に従い、次のパラメーターを使用して、トラフィックを許可する受信セキュリティ規則を作成します。

* **Source** - **Any** またはクライアントが存在する IP 範囲
* **[ソース ポート範囲]**  -  **\***
* **[宛先]**  -  **[Any]**
* **Destination port ranges** - **3390**
* **Protocol** - **UDP**
* **Action** - **Allow**
* 必要に応じて、**Priority** を変更します。 優先度は、ルールが適用される順序に影響します。数値が小さいほど、ルールが早く適用されます。
* **Name** - - **RDP Shortpath**

### <a name="disabling-rdp-shortpath-for-a-specific-subnet"></a>特定のサブネットに対して RDP Shortpath を無効にする

特定のサブネットが RDP Shortpath トランスポートを使用できないようにする必要がある場合、発信元 IP 範囲を指定して追加のネットワーク セキュリティ グループを構成できます。

## <a name="verifying-the-connectivity"></a>接続の検証

### <a name="using-connection-information-dialog"></a>[接続情報] ダイアログの使用

接続で RDP Shortpath が使用されていることを確認するには、接続ツールバーのアンテナ アイコンをクリックして、[接続情報] ダイアログを開きます。

:::image type="content" source="media/rdp-shortpath-connection-bar.png" alt-text="リモート デスクトップ接続バーの画像":::

:::image type="content" source="media/rdp-shortpath-connection-info.png" alt-text="[リモート デスクトップ接続情報] ダイアログの画像":::

### <a name="using-event-logs"></a>イベント ログの使用

セッションで RDP Shortpath トランスポートが使用されていることを確認するには、次の手順を実行します。

1. Windows Virtual Desktop クライアントを使って VM のデスクトップに接続します。
2. イベント ビューアーを起動し、次のノードに移動します。 **[アプリケーションとサービス ログ] > Microsoft > Windows > RemoteDesktopServices-RdpCoreCDV > Microsoft-Windows-RemoteDesktopServices-RdpCoreCDV/Operational**
3. RDP Shortpath トランスポートが使用されているかどうかを判断するには、イベント ID 131 を探します。

### <a name="using-log-analytics-to-verify-shortpath-connectivity"></a>Log Analytics を使用した Shortpath 接続の検証

[Azure Log Analytics](./diagnostics-log-analytics.md) を使用する場合、[WVDConnections テーブル](/azure/azure-monitor/reference/tables/wvdconnections)に対してクエリを実行することで接続を監視できます。 UdpUse という名前の列は、Windows Virtual Desktop RDP スタックにより、現在のユーザー接続で UDP プロトコルが使用されているかどうかを示します。
次の値を指定できます。

* **0** - ユーザー接続で RDP Shortpath は使用されていない
* **1** - ユーザー接続で RDP Shortpath が使用されている
  
次のクエリ一覧では、接続情報を確認できます。 このクエリは、[Log Analytics クエリ エディター](../azure-monitor/logs/log-analytics-tutorial.md#write-a-query)で実行できます。 クエリごとに、`userupn` を検索するユーザーの UPN に置き換えます。

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated, UdpUse, SessionHostName, SessionHostSxSStackVersion
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId, UdpUse)
on CorrelationId
| project StartTime, Duration = EndTime - StartTime, ResourceAlias, UdpUse,  SessionHostName, SessionHostSxSStackVersion
| sort by StartTime asc
```

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="verify-shortpath-listener"></a>Shortpath リスナーを確認する

UDP リスナーが有効であることを確認するには、セッション ホストで次の PowerShell コマンドを使用します。

```powershell
Get-NetUDPEndpoint -OwningProcess ((Get-WmiObject win32_service -Filter "name = 'TermService'").ProcessId)  -LocalPort 3390
```

有効な場合、次のような出力が表示されます

```dos
LocalAddress                             LocalPort
------------                             ---------
::                                       3390
0.0.0.0                                  3390
```

競合が発生した場合、次のコマンドを使用して、ポートを専有しているプロセスを特定できます

```powershell
Get-Process -id (Get-NetUDPEndpoint  -LocalPort 3390 -LocalAddress 0.0.0.0).OwningProcess
```

## <a name="disabling-rdp-shortpath"></a>RDP Shortpath を無効にする

場合によっては、RDP Shortpath トランスポートを無効にする必要があります。 RDP Shortpath は、グループ ポリシーを使用して無効にすることができます。

### <a name="disabling-rdp-shortpath-on-the-client"></a>クライアントで RDP Shortpath を無効にする

特定のクライアントに対して RDP Shortpath を無効にするには、次のグループ ポリシーを使用して UDP のサポートを無効にすることができます。

1. クライアントで、**gpedit.msc** を実行します。
2. **[コンピューターの構成] > [管理テンプレート] > [Windows コンポーネント] > [リモート デスクトップ サービス] > [リモート デスクトップ接続のクライアント]** の順に移動します。
3. **[クライアントの UDP を無効にする]** 設定を **[有効]** に設定します

### <a name="disabling-rdp-shortpath-on-the-session-host"></a>セッション ホストで RDP Shortpath を無効にする

特定のセッション ホストに対して RDP Shortpath を無効にするには、次のグループ ポリシーを使用して UDP のサポートを無効にすることができます。

1. セッション ホストで **gpedit.msc** を実行します。
2. **[コンピューターの構成] > [管理テンプレート] > [Windows コンポーネント] > [リモート デスクトップ サービス] > [リモート デスクトップ接続ホスト] > [接続]** の順に移動します。
3. **[RDP トランスポート プロトコルの選択]** 設定を **[TCP のみ]** に設定します

## <a name="public-preview-feedback"></a>パブリック プレビューのフィードバック

このパブリック プレビューを使用したお客様からのご意見をお待ちしております。
* 質問、要望、コメント、その他のフィードバックについては、[このフィードバック フォームを使用してください](https://aka.ms/RDPShortpathFeedback)。

## <a name="next-steps"></a>次のステップ

* Windows Virtual Desktop のネットワーク接続の詳細については、「[Windows Virtual Desktop のネットワーク接続について](network-connectivity.md)」を参照してください。
* Windows Virtual Desktop のサービス品質 (QoS) の使用を開始するには、「[Windows Virtual Desktop のサービス品質 (QoS) を実装する](rdp-quality-of-service-qos.md)」を参照してください。