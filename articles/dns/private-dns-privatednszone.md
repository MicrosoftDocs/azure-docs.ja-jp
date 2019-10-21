---
title: Azure DNS プライベート ゾーンとは
description: プライベート DNS ゾーンの概要
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 462c873221a4bdc622a9b118b6699a9719a5f88d
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2019
ms.locfileid: "71963735"
---
# <a name="what-is-a-private-azure-dns-zone"></a>プライベート Azure DNS ゾーンとは

Azure プライベート DNS は、信頼性が高くセキュリティで保護された DNS サービスを提供し、カスタムの DNS ソリューションの追加を必要とせずに、仮想ネットワークでドメイン名を管理および解決します。 プライベート DNS ゾーンを使用すると、現在利用可能な Azure で提供される名前ではなく、独自のカスタム ドメイン名を使用できます。 

プライベート DNS ゾーンに含まれるレコードは、インターネットから解決できません。 プライベート DNS ゾーンに対する DNS 解決は、それにリンクされている仮想ネットワークからのみ機能します。

[仮想ネットワーク リンク](./private-dns-virtual-network-links.md)を作成することによって、プライベート DNS ゾーンを 1 つ以上の仮想ネットワークにリンクできます。
また、[自動登録](./private-dns-autoregistration.md)機能を有効にして、仮想ネットワークにデプロイされている仮想マシンの DNS レコードのライフサイクルを自動的に管理することもできます。

## <a name="limits"></a>制限

サブスクリプションで作成できるプライベート DNS ゾーンの数と、プライベート DNS ゾーンでサポートされるレコードセットの数については、「[Azure DNS の制限](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-dns-limits)」を参照してください。

## <a name="restrictions"></a>制限

* 単一ラベルのプライベート DNS ゾーンはサポートされていません。 プライベート DNS ゾーンには、2 つ以上のラベルが必要です。 たとえば、contoso.com にはドットで区切られた 2 つのラベルがあります。 プライベート DNS ゾーンは、最大 34 個のラベルを持つことができます。
* プライベート DNS ゾーンにゾーンの委任 (NS レコード) を作成することはできません。 子ドメインを使用する場合は、ドメインをプライベート DNS ゾーンとして直接作成し、親ゾーンからネームサーバーの委任を設定せずに、仮想ネットワークにリンクすることができます。

## <a name="next-steps"></a>次の手順

* [Azure PowerShell](./private-dns-getstarted-powershell.md) または [Azure CLI](./private-dns-getstarted-cli.md) を使用して Azure DNS のプライベート ゾーンを作成する方法を確認します。

* [プライベート ゾーンのシナリオ](./private-dns-scenarios.md)に関しては、Azure DNS のプライベート ゾーンで実現できるいくつかの一般的なシナリオを参照してください。

* Azure DNS のプライベート ゾーンに関する一般的な質問と回答については、[プライベート DNS の FAQ](./dns-faq-private.md) に関する記事をご覧ください。特定の種類の操作で期待できる固有の動作についても説明があります。
