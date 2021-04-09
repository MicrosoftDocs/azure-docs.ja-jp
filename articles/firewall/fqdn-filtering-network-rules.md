---
title: ネットワーク ルールでの Azure Firewall の FQDN フィルタリング
description: ネットワーク ルールで Azure Firewall の FQDN フィルタリングを使用する方法
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/06/2020
ms.author: victorh
ms.openlocfilehash: f7196c7715ad5d2c02759040b780b96218e1655e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94695947"
---
# <a name="use-fqdn-filtering-in-network-rules"></a>ネットワーク ルールでの FQDN フィルタリング

完全修飾ドメイン名 (FQDN) は、ホストまたは IP アドレスのドメイン名を表します。 Azure Firewall とファイアウォール ポリシーの DNS 解決に基づいて、ネットワーク ルールで FQDN を使用できます。 この機能を使用すると、任意の TCP/UDP プロトコル (NTP、SSH、RDP など) を使用して送信トラフィックをフィルター処理できます。 ネットワーク ルールで FQDN を使用するには、DNS プロキシを有効にする必要があります。 詳細については、「[Azure Firewall の DNS 設定](dns-settings.md)」を参照してください。

> [!NOTE]
> 仕様上、FQDN フィルターではワイルドカードはサポートされていません。

## <a name="how-it-works"></a>しくみ

組織で必要な DNS サーバー (Azure DNS または独自のカスタム DNS) を定義すると、選択した DNS サーバーに基づいて Azure Firewall によって FQDN が IP アドレスに変換されます。 この変換は、アプリケーションとネットワーク ルールの両方の処理で行われます。

新しい DNS 解決が行われると、新しい IP アドレスがファイアウォール規則に追加されます。 DNS サーバーによって返されなくなった古い IP アドレスは、15 分後に有効期限が切れます。 Azure Firewall 規則は、ネットワーク ルールの FQDN の DNS 解決から 15 秒ごとに更新されます。

### <a name="differences-in-application-rules-vs-network-rules"></a>アプリケーション ルールとネットワーク ルールの違い

- HTTP/S および MSSQL のアプリケーション ルールでの FQDN フィルタリングは、アプリケーション レベルの透過プロキシと SNI ヘッダーに基づいています。 そのため、同じ IP アドレスに解決される 2 つの FQDN を区別できます。 これは、ネットワーク ルールでの FQDN フィルタリングには当てはまりません。 

   可能な場合は、常にアプリケーション ルールを使用してください。
     - プロトコルが HTTP/S または MSSQL の場合は、FQDN フィルタリングにアプリケーション ルールを使用します。
   - HTTP/S または MSSQL 以外のその他のプロトコルの場合は、FQDN フィルタリングにアプリケーション ルールまたはネットワーク ルールを使用できます。

## <a name="next-steps"></a>次のステップ

[Azure Firewall の DNS 設定](dns-settings.md)
