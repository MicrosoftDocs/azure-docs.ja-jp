---
title: ネットワーク ルールでの Azure Firewall の FQDN フィルタリング (プレビュー)
description: ネットワーク ルールで Azure Firewall の FQDN フィルタリングを使用する方法
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 08/25/2020
ms.author: victorh
ms.openlocfilehash: 1a35d9c48dd46d5d220699589f4ed758d21feca8
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854275"
---
# <a name="use-fqdn-filtering-in-network-rules-preview"></a>ネットワーク ルールでの FQDN フィルタリング (プレビュー) を使用する

> [!IMPORTANT]
> ネットワーク ルールでの FQDN フィルタリングは現在、パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

完全修飾ドメイン名 (FQDN) は、ホストまたは IP アドレスのドメイン名を表します。 Azure Firewall とファイアウォール ポリシーの DNS 解決に基づいて、ネットワーク ルールで FQDN を使用できます。 この機能を使用すると、任意の TCP/UDP プロトコル (NTP、SSH、RDP など) を使用して送信トラフィックをフィルター処理できます。 ネットワーク ルールで FQDN を使用するには、DNS プロキシを有効にする必要があります。 詳細については、「[Azure Firewall の DNS 設定 (プレビュー)](dns-settings.md)」を参照してください。

> [!NOTE]
> 仕様上、FQDN フィルターではワイルドカードはサポートされていません。

## <a name="how-it-works"></a>しくみ

組織で必要な DNS サーバー (Azure DNS または独自のカスタム DNS) を定義すると、選択した DNS サーバーに基づいて Azure Firewall によって FQDN が IP アドレスに変換されます。 この変換は、アプリケーションとネットワーク ルールの両方の処理で行われます。

ネットワーク ルールでの場合と比較して、アプリケーション ルールでドメイン名を使用する場合との違いは何ですか? 

- HTTP/S および MSSQL のアプリケーション ルールでの FQDN フィルタリングは、アプリケーション レベルの透過プロキシと SNI ヘッダーに基づいています。 そのため、同じ IP アドレスに解決される 2 つの FQDN を区別できます。 これは、ネットワーク ルールでの FQDN フィルタリングには当てはまりません。 可能な場合は、常にアプリケーション ルールを使用してください。
- アプリケーション ルールでは、選択したプロトコルとして HTTP/S および MSSQL を使用できます。 ネットワーク ルールでは、宛先の FQDN と共に任意の TCP/UDP プロトコルを使用できます。

## <a name="next-steps"></a>次のステップ

[Azure Firewall の DNS 設定](dns-settings.md)
