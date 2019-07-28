---
title: Azure Cosmos アカウントの IP ファイアウォール
description: ファイアウォール サポートに IP アクセス制御ポリシーを使用して、Azure Cosmos DB データを保護する方法について説明します。
author: kanshig
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.openlocfilehash: 9398eb4038afcd17788e750fcb5c27c76e9f3f44
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66241072"
---
# <a name="ip-firewall-in-azure-cosmos-db"></a>Azure Cosmos DB の IP ファイアウォール

アカウントに保存されているデータを保護するために、Azure Cosmos DB では、強固なハッシュベースのメッセージ認証コード (HMAC) を利用したシークレット ベースの承認モデルをサポートしています。 さらに、Azure Cosmos DB では、受信ファイアウォールをサポートするための IP ベースのアクセス制御に対応しています。 このモデルは、従来型データベース システムのファイアウォール規則に似ていますが、アカウントのセキュリティ水準がさらに高くなっています。 ファイアウォールを利用して、承認されているコンピューターのグループやクラウド サービスからのみアクセスできるように Azure Cosmos アカウントを構成することができます。 ただし、承認されているコンピューターのグループやサービスから Azure Cosmos データベースに格納されているデータにアクセスするためには、呼び出し側が有効な承認トークンを提示する必要がある点は変わりません。

## <a id="ip-access-control-overview"></a>IP アクセス制御の概要

既定では、有効な承認トークンと共に要求が送信されれば、Azure Cosmos アカウントにインターネットからアクセスすることができます。 IP ポリシー ベースのアクセス制御を構成するためには、特定の Azure Cosmos アカウントにアクセスするクライアント IP の許可リストとして追加する一連の IP アドレスまたは IP アドレス範囲を CIDR (Classless Inter-Domain Routing) 形式でユーザーが指定する必要があります。 この構成を適用すると、この許可リストにないマシンを発信元とするすべての要求で、403 (禁止) 応答が受信されます。 IP ファイアウォールを使用している場合は、Azure portal からアカウントへのアクセスを許可することをお勧めします。 データ エクスプローラーの使用を許可するため、また、Azure portal に表示されるアカウントのメトリックを取得するために、アクセスが必要になります。 データ エクスプローラーを使用する場合は、Azure portal にアカウントへのアクセスを許可するだけでなく、ファイアウォール規則に現在の IP アドレスを追加するようファイアウォール設定を更新する必要もあります。 ファイアウォールの変更が反映されるまでに最大 15 分かかる場合があります。 

IP ベースのファイアウォールと、サブネットおよび VNET のアクセス制御を組み合わせることができます。 これらを組み合わせることで、パブリック IP を保持する任意のソースへのアクセスや、VNET 内の特定のサブネットからのアクセスを制限できます。 サブネットおよび VNET ベースのアクセス制御に関する詳細については、[仮想ネットワークから Azure Cosmos DB リソースへのアクセス](vnet-service-endpoint.md)に関するページを参照してください。

端的に言えば、Azure Cosmos アカウントにアクセスするには、常に認証トークンが必要になります。 IP ファイアウォールおよび VNET アクセス制御リスト (ACL) が設定されていない場合は、認証トークンで Azure Cosmos アカウントにアクセスできます。 IP ファイアウォールまたは VNET ACL、あるいはその両方が Azure Cosmos アカウントに設定された後は、指定したソースから送信された要求 (認証トークンを備えている) のみが、有効な応答を取得します。 

## <a name="next-steps"></a>次の手順

次に、以下のドキュメントを使用して、アカウントの IP ファイアウォールまたは VNET サービス エンドポイントを構成できます。

* [Azure Cosmos アカウントに IP ファイアウォールを構成する方法](how-to-configure-firewall.md)
* [仮想ネットワークから Azure Cosmos DB リソースへのアクセス](vnet-service-endpoint.md)
* [Azure Cosmos アカウントに仮想ネットワーク サービス エンドポイントを構成する方法](how-to-configure-vnet-service-endpoint.md)




