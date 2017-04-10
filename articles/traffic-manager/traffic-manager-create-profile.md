---
title: "Azure での Traffic Manager プロファイルの作成 | Microsoft Docs"
description: "この記事では、Traffic Manager プロファイルの作成方法について説明します。"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 58194b71b22b63f7d4a2a6bf0f4e66f456a96d03
ms.lasthandoff: 03/22/2017

---

# <a name="create-a-traffic-manager-profile"></a>Traffic Manager プロファイルの作成

この記事では、ルーティングの種類に**優先順位**を使用するプロファイルを作成して、ユーザーを Azure Web Apps の 2 つのエンドポイントにルーティングする方法について説明します。 ルーティングの種類に**優先順位**を使用することで、すべてのトラフィックが 1 番目のエンドポイントにルーティングされ、2 番目のエンドポイントはバックアップとして保持されます。 その結果、1 番目のエンドポイントに異常が発生した場合は、2 番目のエンドポイントにユーザーをルーティングできます。

この記事では、あらかじめ作成した 2 つの Azure Web Apps エンドポイントを、新たに作成した Traffic Manager プロファイルに関連付けます。 Azure Web Apps のエンドポイントを作成する方法の詳細については、[Azure Web Apps のドキュメント ページ](https://docs.microsoft.com/azure/app-service-web/)にアクセスしてください。 DNS 名を持ち、パブリック インターネット経由で到達可能な任意のエンドポイントを追加することができます。ここでは、例として Azure Web Apps エンドポイントを使用します。

### <a name="create-a-traffic-manager-profile"></a>Traffic Manager プロファイルの作成
1. ブラウザーから [Azure Portal](http://portal.azure.com) にサインインします。 まだアカウントを持っていない場合は、[1 か月間の無料試用版](https://azure.microsoft.com/free/)にサインアップできます。 
2. **[ハブ]** メニューで、**[新規]** > **[ネットワーク]** > **[すべて表示]** の順にクリックし、**[Traffic Manager プロファイル]** をクリックして **[Traffic Manager プロファイルの作成]** ブレードを開き、**[作成]** をクリックします。
3. **[Traffic Manager プロファイルの作成]** ブレードで、以下の操作を実行します。
    1. **[名前]** に、プロファイルの名前を入力します。 この名前は trafficmanager.net ゾーン内で一意である必要があります。結果的に、Traffic Manager プロファイルへのアクセスに使用される DNS 名 <name>.trafficmanager.net になるためです。
    2. **[ルーティング方法]** で、**[優先順位]** ルーティング方法を選びます。
    3. **[サブスクリプション]** で、このプロファイルを作成するサブスクリプションを選択します。
    4. **[リソース グループ]** で、このプロファイルを配置する新しいリソース グループを作成します。
    5. **[リソース グループの場所]** で、リソース グループの場所を選択します。 これはリソース グループの場所を指定する設定であり、グローバルにデプロイされる Traffic Manager プロファイルには影響しません。
    6. **[作成]**をクリックします。
    7. Traffic Manager プロファイルは、グローバルなデプロイが完了すると、それぞれのリソース グループ内にリソースの 1 つとして表示されます。

![Traffic Manager プロファイルの作成](./media/traffic-manager-create-profile/Create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager エンドポイントの追加

1. ポータルの検索バーで、前のセクションで作成した **Traffic Manager プロファイル**の名前を検索し、表示された結果内で Traffic Manager プロファイルをクリックします。
2. **[Traffic Manager プロファイル]** ブレードの **[設定]** セクションで、**[エンドポイント]** をクリックします。
3. 表示された **[エンドポイント]** ブレードで、**[追加]** をクリックします。
4. **[エンドポイントの追加]** ブレードで、以下の操作を実行します。
    1. **[種類]** で、**[Azure エンドポイント]** をクリックします。
    2. このエンドポイントを識別するための**名前**を指定します。
    3. **[ターゲット リソースの種類]** で、**[App Service]** をクリックします。
    4. **[ターゲット リソース]** で、**[Choose an app service (アプリ サービスの選択)]** をクリックし、同じサブスクリプションにある Web Apps の一覧を表示します。 表示された **[リソース]** ブレードで、最初のエンドポイントとして追加する App Service を選択します。
    5. **[優先順位]** で **[1]** を選択します。 この結果、このエンドポイント正常な状態である場合、すべてのトラフィックがここに送信されます。
    6. **[Add as disabled (無効として追加)]** はオフのままにします。
    7. **[OK]**
5.    次の Azure Web Apps エンドポイントに対して手順 3. と 4. を繰り返します。 必ず **[優先順位]** の値を **[2]** に設定してこのエンドポイント追加してください。
6.    両方のエンドポイントは、追加が完了すると、**[Traffic Manager プロファイル]** ブレードに、監視ステータスが **[オンライン]** の状態で表示されます。

![Traffic Manager エンドポイントの追加](./media/traffic-manager-create-profile/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Traffic Manager プロファイルの使用
1.    ポータルの検索バーで、前のセクションで作成した **Traffic Manager プロファイル**の名前を検索します。 表示された結果で、Traffic Manager プロファイルをクリックします。
2. **[Traffic Manager プロファイル]** ブレードで、**[概要]** をクリックします。
3. **[Traffic Manager プロファイル]** ブレードに、新しく作成した Traffic Manager プロファイルの DNS 名が表示されます。 これを任意のクライアントで使用して (たとえば、Web ブラウザーを使用して移動します)、ルーティングの種類によって決まる適切なエンドポイントにルーティングすることができます。 この場合、すべての要求が最初のエンドポイントにルーティングされますが、Traffic Manager によって異常状態が検出された場合は、トラフィックが自動的に次のエンドポイントにフェールオーバーされます。

## <a name="delete-the-traffic-manager-profile"></a>Traffic Manager プロファイルの削除
リソース グループと、作成した Traffic Manager プロファイルは、不要になったときに削除します。 そのためには、**[Traffic Manager プロファイル]** ブレードでリソース グループを選択し、**[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

- [ルーティングの種類](traffic-manager-routing-methods.md)の詳細について学習します。
- [エンドポイントの種類](traffic-manager-endpoint-types.md)の詳細について学習します。
- [エンドポイントの監視](traffic-manager-monitoring.md)の詳細について学習します。




