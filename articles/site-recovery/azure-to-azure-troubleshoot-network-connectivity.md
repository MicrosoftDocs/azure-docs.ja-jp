---
title: Azure 間のネットワーク接続の問題とエラーに関する Azure Site Recovery のトラブルシューティング | Microsoft Docs
description: ディザスター リカバリーのために Azure 仮想マシンをレプリケートするとき発生するエラーや問題に関するトラブルシューティング
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/05/2019
ms.author: asgang
ms.openlocfilehash: 8e1350a22554bab257e8c99954c2beaa357de2ff
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934520"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Azure 間の VM ネットワーク接続の問題のトラブルシューティング

この記事では、あるリージョンから別のリージョンに Azure 仮想マシンをレプリケートして復旧するときのネットワーク接続に関連する一般的な問題について説明します。 ネットワーク要件の詳細については、[Azure VM をレプリケートするための接続要件](azure-to-azure-about-networking.md)に関するページを参照してください。

Site Recovery レプリケーションを動作させるには、VM から特定の URL または IP 範囲への送信接続が必要です。 VM がファイアウォールの内側にあるか、ネットワーク セキュリティ グループ (NSG) ルールを使用して送信接続を制御している場合は、次のいずれかの問題に直面することがあります。

**URL** | **詳細**  
--- | ---
*.blob.core.windows.net | VM からソース リージョンのキャッシュ ストレージ アカウントにデータを書き込むことができるようにするために必要です。 お使いの VM のすべてのキャッシュ ストレージ アカウントを把握している場合、*.blob.core.windows.net の代わりに、特定のストレージ アカウントの URL (たとえば、cache1.blob.core.windows.net および cache2.blob.core.windows.net) を許可リストに登録できます。
login.microsoftonline.com | Site Recovery サービス URL に対する承認と認証に必要です。
*.hypervrecoverymanager.windowsazure.com | VM から Site Recovery サービス通信を実行できるようにするために必要です。 ファイアウォール プロキシによって IP がサポートされている場合は、該当する 'Site Recovery IP' を使用できます。
*.servicebus.windows.net | VM から Site Recovery の監視および診断データを書き込むことができるようにするために必要です。 ファイアウォール プロキシによって IP がサポートされている場合は、該当する 'Site Recovery Monitoring IP' を使用できます。

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL または IP 範囲の送信接続 (エラー コード 151037 または 151072)

## <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>問題 1: Azure 仮想マシンを Site Recovery に登録できませんでした (151195) </br>
- **考えられる原因** </br>
  - DNS を解決できないため、Site Recovery エンドポイントへの接続を確立できません。
  - これが頻繁に発生するのは、仮想マシンをフェールオーバーして再保護を行っているときに、DR リージョンから DNS サーバーに到達できない場合です。

- **解決策**
   - カスタム DNS を使っている場合は、ディザスター リカバリー リージョンから DNS サーバーにアクセスできることを確認します。 カスタム DNS かどうかを確認するには、VM > ディザスター リカバリー ネットワーク > DNS サーバーに移動します。 仮想マシンから DNS サーバーにアクセスしてみます。 アクセスできない場合は、DNS サーバーをフェールオーバーするか、または DR ネットワークと DNS の間にサイトのラインを作成して、アクセスできるようにします。

    ![com-error](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


## <a name="issue-2-site-recovery-configuration-failed-151196"></a>問題 2:Site Recovery の構成に失敗しました (151196)

> [!NOTE]
> 仮想マシンが **Standard** 内部ロード バランサーの背後にある場合、既定では O365 IP (つまり login.microsoftonline.com) にアクセスできません。 これを **Basic** 内部ロード バランサーの種類に変更するか、この[記事](https://aka.ms/lboutboundrulescli)の説明に従ってアウトバインド アクセスを作成します。

- **考えられる原因** </br>
  - Office 365 認証と ID IP4 エンドポイントへの接続を確立できません。

- **解決策**
  - Azure Site Recovery では、認証のために Office 365 の IP 範囲にアクセスする必要がありました。
    VM での発信ネットワーク接続の制御に Azure ネットワーク セキュリティ グループ (NSG) のルール/ファイアウォール プロキシを使っている場合は、確実に O365 の IP 範囲への通信を許可します。 Azure Active Directory (Azure AD) に対応するすべての IP アドレスへのアクセスを許可するには、[Azure AD サービス タグ](../virtual-network/security-overview.md#service-tags) ベースの NSG 規則を作成します。
      - 今後、新しいアドレスが Azure AD に追加された場合は、新しい NSG 規則を作成する必要があります。

### <a name="example-nsg-configuration"></a>NSG 構成の例

この例は、レプリケートする VM に対して NSG ルールを構成する方法を示しています。

- NSG ルールを使用して送信接続を制御している場合は、必要なすべての IP アドレス範囲のポート 443 に対して "HTTPS 送信を許可" ルールを使用します。
- この例では、VM ソースの場所は "米国東部" で、ターゲットの場所は "米国中央部" であると仮定します。

### <a name="nsg-rules---east-us"></a>NSG ルール - 米国東部

1. 次のスクリーンショットで示すように、NSG 上の "Storage.EastUS" に対して送信方向の HTTPS (443) セキュリティ規則を作成します。

      ![storage-tag](./media/azure-to-azure-about-networking/storage-tag.png)

2. 次のスクリーンショットで示すように、NSG 上の "AzureActiveDirectory" に対して送信方向の HTTPS (443) セキュリティ規則を作成します。

      ![aad-tag](./media/azure-to-azure-about-networking/aad-tag.png)

3. ターゲットの場所に対応する Site Recovery IP に対して、送信方向の HTTPS (443) ルールを作成します。

   **Location** | **Site Recovery IP アドレス** |  **Site Recovery 監視 IP アドレス**
    --- | --- | ---
   米国中部 | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>NSG ルール - 米国中央部

フェールオーバー後にターゲット リージョンからソース リージョンへのレプリケーションが有効になるようにするには、次のルールが必要です。

1. NSG 上の "Storage.CentralUS" に対して、送信方向の HTTPS (443) セキュリティ ルールを作成します。

2. NSG 上の "AzureActiveDirectory" に対して送信方向の HTTPS (443) セキュリティ規則を作成します。

3. ソースの場所に対応する Site Recovery IP に対して、送信方向の HTTPS (443) ルールを作成します。

   **Location** | **Site Recovery IP アドレス** |  **Site Recovery 監視 IP アドレス**
    --- | --- | ---
   米国中部 | 13.82.88.226 | 104.45.147.24
## <a name="issue-3-site-recovery-configuration-failed-151197"></a>問題 3:Site Recovery の構成に失敗しました (151197)
- **考えられる原因** </br>
  - Azure Site Recovery サービスのエンドポイントに対する接続を確立できません。

- **解決策**
  - Azure Site Recovery では、リージョンに基づいて [Site Recovery の IP 範囲](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)にアクセスする必要がありました。 必要な IP 範囲に仮想マシンからアクセスできることを確認します。


## <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>問題 4:ネットワーク トラフィックがオンプレミス プロキシ サーバーを経由するときに A2A レプリケーションが失敗しました (151072)
- **考えられる原因** </br>
  - カスタム プロキシ設定が無効であり、Azure Site Recovery Mobility Service エージェントが IE からプロキシ設定を自動検出しませんでした


- **解決策**
  1. モビリティ サービス エージェントは、Windows では IE から、Linux では /etc/environment からプロキシ設定を検出します。
  2. プロキシを Azure Site Recovery Mobility Service にのみ設定する場合は、次の場所にある ProxyInfo.conf 内にプロキシの詳細を指定できます。</br>
     - ***Linux*** の場合は ``/usr/local/InMage/config/``
     - ***Windows*** の場合は ``C:\ProgramData\Microsoft Azure Site Recovery\Config``
  3. ProxyInfo.conf 内のプロキシ設定は、次の INI 形式になっている必要があります。</br>
                *[proxy]*</br>
                *Address=http://1.2.3.4*</br>
                *Port=567*</br>
  4. Azure Site Recovery Mobility Service エージェントは、"***認証されていないプロキシ***" のみをサポートします。

### <a name="fix-the-problem"></a>問題の解決
[必要な URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) または[必要な IP 範囲](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)を許可するには、[ネットワーク ガイダンスのドキュメント](site-recovery-azure-to-azure-networking-guidance.md)の次の手順に従ってください。


## <a name="next-steps"></a>次の手順
[Azure 仮想マシンのレプリケート](site-recovery-replicate-azure-to-azure.md)
