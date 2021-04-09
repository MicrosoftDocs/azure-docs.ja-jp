---
title: Azure Firewall ポリシーの DNS 設定
description: DNS サーバーと DNS プロキシ設定を使用して Azure Firewall ポリシーを構成できます。
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 02/17/2021
ms.author: victorh
ms.openlocfilehash: a02879c5322add16f89f77e2da39daec7d2b5f31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633650"
---
# <a name="azure-firewall-policy-dns-settings"></a>Azure Firewall ポリシーの DNS 設定

カスタム DNS サーバーを構成して、Azure Firewall ポリシーの DNS プロキシを有効にすることができます。 ファイアウォール以降を展開するときに、 **[DNS 設定]** ページからこれらの設定を構成できます。

## <a name="dns-servers"></a>DNS サーバー

DNS サーバーでは、ドメイン名から IP アドレスに管理と解決が行われます。 Azure Firewall では、名前解決に Azure DNS が既定で使用されます。 **DNS サーバー** 設定を使用すると、Azure Firewall の名前解決に対して独自の DNS サーバーを構成できます。 1 つまたは複数のサーバーを構成することができます。

### <a name="configure-custom-dns-servers"></a>カスタム DNS サーバーを構成する

1. ファイアウォール ポリシーを選択します。
2. **[設定]** で、 **[DNS 設定]** を選択します。
3. **[DNS サーバー]** では、Virtual Network で以前に指定された既存の DNS サーバーを入力または追加できます。
4. **[保存]** を選択します。
5. ファイアウォールによって、名前解決のために、指定された DNS サーバーに DNS トラフィックが送信されるようになりました。

## <a name="dns-proxy"></a>DNS プロキシ

Azure Firewall が DNS プロキシとして機能するように構成できます。 DNS プロキシは、クライアント仮想マシンから DNS サーバーへの DNS 要求の仲介役として機能します。 カスタム DNS サーバーを構成する場合は、DNS 解決の不一致を回避するために DNS プロキシを有効にして、ネットワーク ルールで FQDN フィルタリングを有効にする必要があります。

DNS プロキシを有効にしない場合は、クライアントからの DNS 要求は、別の時刻に DNS サーバーに移動するか、ファイアウォールの応答とは異なる応答を返します。 DNS プロキシでは、不整合を回避するために、クライアント要求のパスに Azure Firewall が配置されます。

DNS プロキシの構成には、次の 3 つの手順が必要です。

1. Azure Firewall の [DNS 設定] で DNS プロキシを有効にします。
2. 必要に応じて、カスタム DNS サーバーを構成するか、指定された既定のものを使用します。
3. 最後に、仮想ネットワークの DNS サーバーの設定で、Azure Firewall のプライベート IP アドレスをカスタム DNS アドレスとして構成する必要があります。 これにより、DNS トラフィックが Azure Firewall に送信されるようになります。

### <a name="configure-dns-proxy"></a>DNS プロキシを構成する

DNS プロキシを構成するには、ファイアウォールのプライベート IP アドレスを使用するように、仮想ネットワークの DNS サーバー設定を構成する必要があります。 次に、Azure Firewall ポリシーの **[DNS 設定]** で DNS プロキシを有効にします。

#### <a name="configure-virtual-network-dns-servers"></a>仮想ネットワークの DNS サーバーを構成する

1. Azure Firewall 経由で DNS トラフィックがルーティングされる仮想ネットワークを選択します。
2. **[設定]** で、 **[DNS サーバー]** を選択します。
3. **[DNS サーバー]** で、 **[カスタム]** を選択します。
4. ファイアウォールのプライベート IP アドレスを入力します。
5. **[保存]** を選択します。

#### <a name="enable-dns-proxy"></a>DNS プロキシを有効にする

1. Azure Firewall ポリシーを選択します。
2. **[設定]** で、 **[DNS 設定]** を選択します。
3. 既定では、**DNS プロキシ** は無効になっています。 有効にすると、ファイアウォールはポート 53 でリッスンし、構成済みの DNS サーバーに DNS 要求を転送します。
4. **DNS サーバー** の構成を確認して、設定が環境に適していることを確認します。
5. **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

[ネットワーク ルールでの FQDN フィルタリング](fqdn-filtering-network-rules.md)