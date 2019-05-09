---
title: Azure Traffic Manager を使用した地理的トラフィック ルーティング方法の構成
description: この記事では、Azure Traffic Manager を使用して地理的トラフィック ルーティング方法を構成する方法について説明します。
services: traffic-manager
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: kumud
ms.openlocfilehash: 30c4a6817272f7b5f1810873a93280b234ff6cc9
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59524312"
---
# <a name="configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Traffic Manager を使用した地理的トラフィック ルーティング方法の構成

地理的トラフィック ルーティング方法を使用すると、要求の送信元の地理的な場所に基づいて、特定のエンドポイントにトラフィックを転送することができます。 このチュートリアルでは、このルーティング方法を使用して Traffic Manager プロファイルを作成し、特定の地域からのトラフィックを受信するようにエンドポイントを構成する方法を説明します。

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager プロファイルの作成

1. ブラウザーから [Azure Portal](https://portal.azure.com) にサインインします。 まだアカウントを持っていない場合は、[1 か月間の無料試用版](https://azure.microsoft.com/free/)にサインアップできます。
2. **[リソースの作成]** > **[ネットワーク]** > **[Traffic Manager プロファイル]** > **[作成]** の順にクリックします。
4. **[Traffic Manager プロファイルの作成]** で、以下の手順に従います。
    1. プロファイルの名前を指定します。 この名前は、trafficmanager.net zone 内で一意である必要があります。 Traffic Manager プロファイルにアクセスするには、ご自分の DNS 名 `<profilename>.trafficmanager.net` を使用します。
    2. **地理的**ルーティング方法を選択します。
    3. このプロファイルを作成するサブスクリプションを選択します。
    4. 既存のリソース グループを使用するか、新しいリソース グループを作成して、その下にこのプロファイルを配置します。 新しいリソース グループを作成する場合は、**[リソース グループの場所]** ドロップダウンを使用して、リソース グループの場所を指定します。 これはリソース グループの場所を指定する設定であり、グローバルにデプロイされる Traffic Manager プロファイルには影響しません。
    5. **[作成]** をクリックした後、Traffic Manager プロファイルが作成され、グローバルにデプロイされます。

![Traffic Manager プロファイルの作成](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>エンドポイントの追加

1. 作成した Traffic Manager プロファイル名をポータルの検索バーで検索し、結果が表示されたらクリックします。
2. Traffic Manager で **[設定]** -> **[エンドポイント]** の順に移動します。
3. **[追加]** をクリックして、**[エンドポイントの追加]** を表示します。
3. **[追加]** をクリックし、表示された **[エンドポイントの追加]** で、以下の操作を実行します。
4. 追加するエンドポイントの種類に応じて **[種類]** を選択します。 実稼働環境で使用する地理的ルーティング プロファイルの場合、1 つ以上のエンドポイントを持つ子プロファイルを含む入れ子になったエンドポイントの種類を使用することを強くお勧めします。 詳細については、[地理的トラフィック ルーティング方法に関するよく寄せられる質問](traffic-manager-FAQs.md)を参照してください。
5. このエンドポイントを識別するための**名前**を指定します。
6. このページ内の特定のフィールドは、追加するエンドポイントの種類によって決まります。
    1. Azure エンドポイントを追加する場合は、**[ターゲット リソースの種類]** と、トラフィックの転送先のリソースに基づいて **[ターゲット]** を選択します。
    2. **外部**エンドポイントを追加する場合、エンドポイントの**完全修飾ドメイン名 (FQDN)** を指定します。
    3. **入れ子になったエンドポイント**を追加する場合、使用する子プロファイルに対応する**ターゲット リソース**を選択し、**[Minimum child endpoints count (最小子エンドポイント数)]** を指定します。
7. [Geo-mapping (geo マッピング)] セクションで、ドロップダウンを使用して、このエンドポイントにトラフィックを送信するリージョンを追加します。 少なくとも 1 つのリージョンを追加する必要があり、複数のリージョンをマッピングすることができます。
8. このプロファイルの下に追加するすべてのエンドポイントに対してこの手順を繰り返します。

![Traffic Manager エンドポイントの追加](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Traffic Manager プロファイルの使用
1.  ポータルの検索バーで、前のセクションで作成した **Traffic Manager プロファイル**の名前を検索し、表示された結果内で Traffic Manager プロファイルをクリックします。
2. **[Overview]** をクリックします。
3. **[Traffic Manager プロファイル]** に、新しく作成した Traffic Manager プロファイルの DNS 名が表示されます。 これを任意のクライアントで使用して (たとえば、Web ブラウザーを使用して移動します)、ルーティングの種類によって決まる適切なエンドポイントにルーティングすることができます。  地理的ルーティングの場合、Traffic Manager は、受信要求の発信元 IP アドレスを確認して、発信元のリージョンを判断します。 そのリージョンがエンドポイントにマッピングされている場合、トラフィックはそこにルーティングされます。 このリージョンがエンドポイントにマッピングされていない場合、Traffic Manager は NODATA クエリ応答を返します。

## <a name="next-steps"></a>次の手順

- [地理的トラフィック ルーティング方法](traffic-manager-routing-methods.md#geographic)の詳細を確認します。
- [Traffic Manager の設定のテスト](traffic-manager-testing-settings.md)方法について学習します。
