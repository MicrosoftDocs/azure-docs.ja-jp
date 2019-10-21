---
title: Azure DNS プライベート ゾーンの自動登録機能とは
description: Azure DNS プライベート ゾーンの自動登録機能の概要
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9d1854b459e799d5cbb401de9ac717dd7d0fde1d
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2019
ms.locfileid: "71963715"
---
# <a name="what-is-the-autoregistration-feature-of-azure-dns-private-zones"></a>Azure DNS プライベート ゾーンの自動登録機能とは

Azure DNS プライベート ゾーンの自動登録機能を使用すると、仮想ネットワークにデプロイされている仮想マシンの DNS レコード管理の問題が解消されます。 プライベート DNS ゾーンに[仮想ネットワークをリンク](./private-dns-virtual-network-links.md)し、すべての仮想マシンの自動登録を有効にすると、仮想ネットワークにデプロイされている仮想マシンの DNS レコードがプライベート DNS ゾーンに自動的に作成されます。 それらの仮想マシンの前方参照レコード (A レコード) に加えて、逆引き参照レコード (PTR レコード) も自動的に作成されます。
仮想ネットワークに仮想マシンを追加すると、リンクされたプライベート DNS ゾーンにそれらの仮想マシンの DNS レコードも自動的に作成されます。

仮想マシンを削除すると、その仮想マシンの DNS レコードはプライベート DNS ゾーンから自動的に削除されます。

自動登録は、仮想ネットワーク リンクの作成中に [Enable auto registration]\(自動登録を有効にする\) オプションを選択することで有効にできます。

![自動登録を有効にする](./media/privatedns-concepts/enable-autoregistration.png)

## <a name="restrictions"></a>制限

* 自動登録は、仮想マシンに対してのみ機能します。 内部ロード バランサーなどの他のすべてのリソースについては、仮想ネットワークにリンクされているプライベート DNS ゾーンに DNS レコードを手動で作成できます。
* DNS レコードは、プライマリ仮想マシンの NIC に対してのみ自動的に作成されます。 仮想マシンに複数の NIC がある場合は、他のネットワーク インターフェイスの DNS レコードを手動で作成できます。
* IPv6 (AAAA レコード) の自動登録はサポートされていません。

## <a name="next-steps"></a>次の手順

* [Azure PowerShell](./private-dns-getstarted-powershell.md) または [Azure CLI](./private-dns-getstarted-cli.md) を使用して Azure DNS のプライベート ゾーンを作成する方法を確認します。

* [プライベート ゾーンのシナリオ](./private-dns-scenarios.md)に関しては、Azure DNS のプライベート ゾーンで実現できるいくつかの一般的なシナリオを参照してください。

* Azure DNS のプライベート ゾーンに関する一般的な質問と回答については、[プライベート DNS の FAQ](./dns-faq-private.md) に関する記事をご覧ください。特定の種類の操作で期待できる固有の動作についても説明があります。
