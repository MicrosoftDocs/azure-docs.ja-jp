---
title: ネットワーク ルールでの Azure Firewall Manager のフィルタリング (プレビュー)
description: ネットワーク ルールで FQDN フィルタリングを使用する方法
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 07/30/2020
ms.author: victorh
ms.openlocfilehash: 28cd26532ca5bdf83902854b7910f7d6c18a4eab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87460152"
---
# <a name="fqdn-filtering-in-network-rules-preview"></a>ネットワーク ルールでの FQDN フィルタリング (プレビュー)

> [!IMPORTANT]
> ネットワーク ルールでの FQDN フィルタリングは現在、パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

完全修飾ドメイン名 (FQDN) は、ホストまたは IP アドレスのドメイン名を表します。 Azure Firewall とファイアウォール ポリシーの DNS 解決に基づいて、ネットワーク ルールで FQDN を使用できます。 この機能を使用すると、任意の TCP/UDP プロトコル (NTP、SSH、RDP など) を使用して送信トラフィックをフィルター処理できます。 ネットワーク ルールで FQDN を使用するには、DNS プロキシを有効にする必要があります。 詳細については、[Azure Firewall ポリシーの DNS 設定 (プレビュー)](dns-settings.md) に関するページを参照してください。

## <a name="how-it-works"></a>しくみ

組織で必要な DNS サーバー (Azure DNS または独自のカスタム DNS) を定義すると、選択した DNS サーバーに基づいて Azure Firewall によって FQDN が IP アドレスに変換されます。 この変換は、アプリケーションとネットワーク ルールの両方の処理で行われます。

ネットワーク ルールでの場合と比較して、アプリケーション ルールでドメイン名を使用する場合との違いは何ですか? 

- HTTP/S および MSSQL のアプリケーション ルールでの FQDN フィルタリングは、アプリケーション レベルの透過プロキシと SNI ヘッダーに基づいています。 そのため、同じ IP アドレスに解決される 2 つの FQDN を区別できます。 これは、ネットワーク ルールでの FQDN フィルタリングには当てはまりません。 可能な場合は、常にアプリケーション ルールを使用してください。
- アプリケーション ルールでは、選択したプロトコルとして HTTP/S および MSSQL を使用できます。 ネットワーク ルールでは、宛先の FQDN と共に任意の TCP/UDP プロトコルを使用できます。

## <a name="next-steps"></a>次のステップ

[Azure Firewall の DNS 設定](dns-settings.md)
