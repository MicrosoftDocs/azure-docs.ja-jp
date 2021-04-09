---
title: Azure Traffic Manager プロファイルの管理 | Microsoft Docs
description: この記事は、Azure Traffic Manager プロファイルの作成、無効化、有効化、削除に役立ちます。
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
manager: twooley
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: duau
ms.openlocfilehash: a39120b1305022739aaef3407aa6c2621a97e842
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98184155"
---
# <a name="manage-an-azure-traffic-manager-profile"></a>Azure Traffic Manager プロファイルの管理

Traffic Manager プロファイルでは、クラウド サービスまたは Web サイト エンドポイントへのトラフィックの分散を制御するためにトラフィック ルーティング方法が使用されます。 この記事では、これらのプロファイルの作成と管理の方法について説明します。

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager プロファイルの作成

Azure Portal を使用して Traffic Manager プロファイルを作成できます。 プロファイルの作成後に、Azure Portal でエンドポイント、監視、その他の設定を構成できます。 Traffic Manager では、1 プロファイル当たり、最大 200 のエンドポイントをサポートしますが、 ほとんどの使用シナリオに必要なエンドポイントは少数にすぎません。

### <a name="to-create-a-traffic-manager-profile"></a>Traffic Manager プロファイルを作成するには

1. ブラウザーから [Azure Portal](https://portal.azure.com) にサインインします。 まだアカウントを持っていない場合は、[1 か月間の無料試用版](https://azure.microsoft.com/free/)にサインアップできます。 
2. **[リソースの作成]**  >  **[ネットワーク]**  >  **[Traffic Manager プロファイル]**  >  **[作成]** の順にクリックします。
4. **[Traffic Manager プロファイルの作成]** で、以下を実行します。
    1. **[名前]** に、プロファイルの名前を入力します。 この名前は trafficmanager.net ゾーン内で一意である必要があります。結果的に、Traffic Manager プロファイルへのアクセスに使用される、DNS 名 `<name>`.trafficmanager.net になるためです。
    2. **[ルーティング方法]** で、**[優先順位]** ルーティング方法を選びます。
    3. **[サブスクリプション]** で、このプロファイルを作成するサブスクリプションを選択します。
    4. **[リソース グループ]** で、このプロファイルを配置する新しいリソース グループを作成します。
    5. **[リソース グループの場所]** で、リソース グループの場所を選択します。 これはリソース グループの場所を指定する設定であり、グローバルにデプロイされる Traffic Manager プロファイルには影響しません。
    6. **Create** をクリックしてください。
    7. Traffic Manager プロファイルは、グローバルなデプロイが完了すると、それぞれのリソース グループ内にリソースの 1 つとして表示されます。

## <a name="disable-enable-or-delete-a-profile"></a>プロファイルの無効化、有効化、または削除

Traffic Manager が構成済みのエンドポイントに対するユーザーの要求を参照しないように、既存のプロファイルを無効にすることができます。 Traffic Manager プロファイルを無効にすると、プロファイルとプロファイルに含まれる情報は元のまま残ります。このため、Traffic Manager インターフェイスで編集することもできます。  プロファイルをもう一度有効にすると、参照が再開されます。 Traffic Manager プロファイルは、Azure ポータルで作成すると自動的に有効になります。 プロファイルが不要になった場合は、削除できます。

### <a name="to-disable-a-profile"></a>プロファイルを無効にするには

1. カスタム ドメイン名を使用している場合は、インターネット DNS サーバー上の CNAME レコードを変更して、Traffic Manager プロファイルをポイントしないようにします。
2. Traffic Manager プロファイルをこのように設定すると、トラフィックがエンドポイントに送信されなくなります。
3. ブラウザーから [Azure Portal](https://portal.azure.com) にサインインします。
2. ポータルの検索バーで、変更の対象となる **Traffic Manager プロファイル** 名を検索し、表示された結果内で Traffic Manager プロファイルをクリックします。
3. **[概要]**  >  **[無効化]** の順にクリックします。
4. 確認して Traffic Manager プロファイルを無効にします。

### <a name="to-enable-a-profile"></a>プロファイルを有効にするには

1. ブラウザーから [Azure Portal](https://portal.azure.com) にサインインします。
2. ポータルの検索バーで、変更の対象となる **Traffic Manager プロファイル** 名を検索し、表示された結果内で Traffic Manager プロファイルをクリックします。
3. **[概要]**  >  **[有効化]** の順にクリックします。
1. カスタム ドメイン名を使用している場合は、インターネット DNS サーバー上の CNAME レコードを作成して、Traffic Manager プロファイルのドメイン名をポイントするようにします。
2. トラフィックは再度、エンドポイントに送信されるようになります。

### <a name="to-delete-a-profile"></a>プロファイルを削除するには

1. インターネット DNS サーバー上の DNS リソース レコードが、Traffic Manager プロファイルのドメイン名を指す CNAME リソース レコードを使用していないことを確認します。
2. ポータルの検索バーで、変更の対象となる **Traffic Manager プロファイル** 名を検索し、表示された結果内で Traffic Manager プロファイルをクリックします。
3. **[概要]**  >  **[削除]** の順にクリックします。
4. 確認して Traffic Manager プロファイルを削除します。

## <a name="next-steps"></a>次のステップ

* [エンドポイントの追加](./traffic-manager-manage-endpoints.md)
* [優先順位によるルーティング方法の構成](traffic-manager-configure-priority-routing-method.md)
* [地理的なルーティング方法の構成](traffic-manager-configure-geographic-routing-method.md) 
* [重み付けによるルーティング方法の構成](traffic-manager-configure-weighted-routing-method.md)
* [パフォーマンスによるルーティング方法の構成](traffic-manager-configure-performance-routing-method.md)