---
title: Azure Site Recovery を使用した Azure 間のディザスター リカバリーの接続のトラブルシューティング
description: Azure VM ディザスター リカバリーでの接続に関する問題のトラブルシューティング
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/06/2020
ms.openlocfilehash: 24ffce1528aa5c82fec9666fa0cb7b8717107f54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97652264"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Azure 間の VM ネットワーク接続の問題のトラブルシューティング

この記事では、あるリージョンから別のリージョンに Azure 仮想マシン (VM) をレプリケートして復旧するときのネットワーク接続に関連する一般的な問題について説明します。 ネットワーク要件の詳細については、[Azure VM をレプリケートするための接続要件](azure-to-azure-about-networking.md)に関するページを参照してください。

Site Recovery レプリケーションを動作させるには、VM から特定の URL または IP 範囲への送信接続が必要です。 VM がファイアウォールの内側にあるか、ネットワーク セキュリティ グループ (NSG) ルールを使用して送信接続を制御している場合は、次のいずれかの問題に直面することがあります。

| **名前**                  | **商用**                               | **政府**                                 | **説明** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| ストレージ                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | VM からソース リージョンのキャッシュ ストレージ アカウントにデータを書き込むことができるようにするために必要です。 VM のすべてのキャッシュ ストレージ アカウントを把握している場合は、特定のストレージ アカウント URL に対して許可リストを使用できます。 たとえば、`*.blob.core.windows.net` ではなく、`cache1.blob.core.windows.net` や `cache2.blob.core.windows.net` などです。 |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Site Recovery サービス URL に対する承認と認証に必要です。 |
| レプリケーション               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | VM から Site Recovery サービス通信を実行できるようにするために必要です。 ファイアウォール プロキシによって IP がサポートされている場合は、該当する _Site Recovery IP_ を使用できます。 |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | VM から Site Recovery の監視および診断データを書き込むことができるようにするために必要です。 ファイアウォール プロキシによって IP がサポートされている場合は、該当する _Site Recovery Monitoring IP_ を使用できます。 |

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL または IP 範囲の送信接続 (エラー コード 151037 または 151072)

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>問題 1:Azure 仮想マシンを Site Recovery に登録できませんでした (151195)

#### <a name="possible-cause"></a>考えられる原因

ドメイン ネーム システム (DNS) を解決できないため、Site Recovery エンドポイントとの接続を確立できません。 この問題が頻繁に発生するのは、VM をフェールオーバーして再保護を行っているときに、ディザスター リカバリー (DR) リージョンから DNS サーバーに到達できない場合です。

#### <a name="resolution"></a>解像度

カスタム DNS を使っている場合は、ディザスター リカバリー リージョンから DNS サーバーにアクセスできることを確認します。

VM がカスタム DNS 設定を使用するかどうかを確認するには、次を実行します。

1. **[仮想マシン]** を開いて、VM を選択します。
1. VM の **[設定]** に移動し、 **[ネットワーク]** を選択します。
1. **[仮想ネットワーク/サブネット]** で、仮想ネットワークのリソース ページを開くためのリンクを選択します。
1. **[設定]** に移動して、 **[DNS サーバー]** を選択します。

仮想マシンから DNS サーバーにアクセスを試みます。 DNS サーバーにアクセスできない場合は、DNS サーバーをフェールオーバーするか、または DR ネットワークと DNS の間にサイトのラインを作成して、アクセスできるようにします。

  :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-error":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>問題 2:Site Recovery の構成に失敗しました (151196)

> [!NOTE]
> VM が **Standard** 内部ロード バランサーの背後にある場合、既定では、`login.microsoftonline.com` などの Microsoft 365 IP にアクセスすることはできません。 記事「[Azure CLI を使用して Standard Load Balancer の負荷分散規則とアウトバウンド規則を構成する](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard#create-outbound-rule-configuration)」の説明に従って、内部ロード バランサーの種類を **Basic** に変更するか、発信アクセスを作成します。

#### <a name="possible-cause"></a>考えられる原因

Microsoft 365 認証と ID IP4 エンドポイントへの接続を確立できません。

#### <a name="resolution"></a>解像度

- Azure Site Recovery では、認証のために Microsoft 365 の IP 範囲にアクセスする必要があります。
- VM での発信ネットワーク接続の制御に Azure ネットワーク セキュリティ グループ (NSG) のルールやファイアウォール プロキシを使用している場合は、Microsoft 365 の IP 範囲への通信を確実に許可します。 Azure Active Directory (Azure AD) に対応するすべての IP アドレスへのアクセスを許可するには、[Azure Active Directory (Azure AD) サービス タグ](../virtual-network/network-security-groups-overview.md#service-tags) ベースの NSG 規則を作成します。
- 今後、新しいアドレスが Azure AD に追加された場合は、新しい NSG 規則を作成する必要があります。

### <a name="example-nsg-configuration"></a>NSG 構成の例

この例は、レプリケートする VM に対して NSG ルールを構成する方法を示しています。

- NSG ルールを使用して送信接続を制御している場合は、必要なすべての IP アドレス範囲のポート 443 に対して、"**HTTPS 送信を許可**" ルールを使用します。
- この例では、VM ソースの場所は **米国東部** で、ターゲットの場所は **米国中部** であると仮定します。

#### <a name="nsg-rules---east-us"></a>NSG ルール - 米国東部

1. 次のスクリーンショットで示すように、NSG の HTTPS 送信セキュリティ ルールを作成します。 この例では、次の **[宛先サービス タグ]** を使用します。_Storage.EastUS_ および **[宛先ポート範囲]** : _443_。

     :::image type="content" source="./media/azure-to-azure-about-networking/storage-tag.png" alt-text="Storage.EastUS のセキュリティ規則の [送信セキュリティ規則の追加] ウィンドウを示すスクリーンショット。":::

1. 次のスクリーンショットで示すように、NSG の HTTPS 送信セキュリティ ルールを作成します。 この例では、次の **[宛先サービス タグ]** を使用します。_AzureActiveDirectory_ および **[宛先ポート範囲]** : _443_。

     :::image type="content" source="./media/azure-to-azure-about-networking/aad-tag.png" alt-text="Azure Active Directory のセキュリティ規則の [送信セキュリティ規則の追加] ウィンドウを示すスクリーンショット。":::

1. 上記のセキュリティ規則と同様に、ターゲットの場所に対応する NSG 上の "EventHub.CentralUS" に対して送信方向の HTTPS (443) セキュリティ規則を作成します。 これにより、Site Recovery 監視にアクセスできるようになります。
1. NSG 上の "AzureSiteRecovery" に対して送信方向の HTTPS (443) セキュリティ規則を作成します。 これにより、任意のリージョンの Site Recovery Service にアクセスできます。

#### <a name="nsg-rules---central-us"></a>NSG ルール - 米国中部

この例では、フェールオーバー後にターゲット リージョンからソース リージョンへのレプリケーションが有効になるようにするには、以下の NSG ルールが必要です。

1. _Storage.CentralUS_ に対して、HTTPS 送信セキュリティ ルールを作成します。

   - **[宛先サービス タグ]** : _Storage.CentralUS_
   - **[宛先ポート範囲]** : _443_

1. _AzureActiveDirectory_ に対して、HTTPS 送信セキュリティ ルールを作成します。

   - **[宛先サービス タグ]** : _AzureActiveDirectory_
   - **[宛先ポート範囲]** : _443_

1. 上記のセキュリティ規則と同様に、ソースの場所に対応する NSG 上の "EventHub.EastUS" に対して送信方向の HTTPS (443) セキュリティ規則を作成します。 これにより、Site Recovery 監視にアクセスできるようになります。
1. NSG 上の "AzureSiteRecovery" に対して送信方向の HTTPS (443) セキュリティ規則を作成します。 これにより、任意のリージョンの Site Recovery Service にアクセスできます。

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>問題 3:Site Recovery の構成に失敗しました (151197)

#### <a name="possible-cause"></a>考えられる原因

Azure Site Recovery サービスのエンドポイントに対する接続を確立できません。

#### <a name="resolution"></a>解像度

マシンでの発信ネットワーク接続の制御に Azure ネットワーク セキュリティ グループ (NSG) 規則またはファイアウォール プロキシを使用している場合は、許可が必要なサービス タグがいくつか存在します。 [詳細については、こちらを参照してください](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)。

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>問題 4:ネットワーク トラフィックがオンプレミス プロキシ サーバーを経由するときに Azure 間レプリケーションが失敗しました (151072)

#### <a name="possible-cause"></a>考えられる原因

カスタム プロキシ設定が無効であり、Azure Site Recovery Mobility Service エージェントが Internet Explorer (IE) からプロキシ設定を自動検出しませんでした。

#### <a name="resolution"></a>解像度

1. モビリティ サービス エージェントは、Windows では IE から、Linux では `/etc/environment` からプロキシ設定を検出します。
1. プロキシを Azure Site Recovery Mobility Service にのみ設定する場合は、次の場所にある _ProxyInfo.conf_ 内にプロキシの詳細を指定できます。

   - **Linux**: `/usr/local/InMage/config/`
   - **Windows**: `C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. _ProxyInfo.conf_ 内のプロキシ設定は、次の _INI_ 形式になっている必要があります。

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> Azure Site Recovery Mobility Service エージェントでは、**認証されていないプロキシ** のみがサポートされます

### <a name="fix-the-problem"></a>問題の解決

[必要な URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) または[必要な IP 範囲](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)を許可するには、[ネットワーク ガイダンスのドキュメント](./azure-to-azure-about-networking.md)の次の手順に従ってください。

## <a name="next-steps"></a>次のステップ

[Azure VM を別の Azure リージョンにレプリケートする](azure-to-azure-how-to-enable-replication.md)