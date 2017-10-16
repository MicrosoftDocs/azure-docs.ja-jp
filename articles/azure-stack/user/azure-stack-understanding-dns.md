---
title: "Azure Stack の DNS について | Microsoft Docs"
description: "Azure Stack の DNS の機能と Azure について"
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: darmour
editor: 
ms.assetid: 60f5ac85-be19-49ac-a7c1-f290d682b5de
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: scottnap
ms.openlocfilehash: 8c023eda179ace41a082bf4a4fadc281c14db7ba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="introducing-idns-for-azure-stack"></a>Azure Stack 用の iDNS の概要

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

iDNS は、外部 DNS 名 (http://www.bing.com など) を解決できる Azure Stack の機能です。
内部仮想ネットワーク名を登録することもできます。 これを行うことで、同じ仮想ネットワーク内の VM を IP アドレスではなく名前で解決することができ、カスタム DNS サーバー エントリを用意する必要はありません。

これは、Azure では常に使用できるものですが、Windows Server 2016 と Azure Stack でも同じように使用できます。

## <a name="what-does-idns-do"></a>iDNS が実行すること
Azure Stack の iDNS を使用して、カスタム DNS サーバー エントリの指定なしで次の機能を取得できます。

* テナントのワークロードに対する共有 DNS 名前解決サービス。
* テナントの仮想ネットワーク内で名前解決と DNS 登録を行う権限がある DNS サービス。
* テナントの VM からインターネット名を解決する再帰的な DNS サービス。 テナントは、インターネット名 (例: www.bing.com) を解決するためにカスタム DNS エントリを指定する必要はもうありません。

必要に応じて、引き続き独自の DNS とカスタム DNS サーバーを使用できます。 ただし、インターネット の DNS 名の解決と同じ仮想ネットワーク内の他の仮想マシンへの接続のみが目的の場合は、何の指定もなしで問題なく機能するようになっています。

## <a name="what-does-idns-not-do"></a>iDNS が実行しないこと
iDNS で実行できないことは、仮想ネットワークの外部から解決できる名前の DNS レコードを作成することです。

Azure には、パブリック IP アドレスに関連付けることができる DNS の名前ラベルを指定するオプションがあります。 ラベル (プレフィックス) は自分で選択できますが、サフィックスは、パブリック IP アドレスを作成するリージョンに基づいて Azure が選択します。

![DNS の名前ラベルのスクリーンショット](media/azure-stack-understanding-dns-in-tp2/image3.png)

上の図では、Azure は、指定された DNS の名前ラベルを持つ **westus.cloudapp.azure.com** ゾーンの DNS に "A" レコードを作成します。プレフィックスとサフィックスは、まとまって、パブリック インターネットの任意の場所で解決できる完全修飾ドメイン名 (FQDN) を構成します。

Azure Stack では、内部名の登録でのみ iDNS をサポートするため、以下を行うことはできません。

* 既存のホストされている DNS ゾーン (例: local.azurestack.external) に DNS レコードを作成する。
* DNS ゾーン (Contoso.com など) を作成する。
* 独自のカスタム DNS ゾーンにレコードを作成する。
* ドメイン名の購入をサポートする。

