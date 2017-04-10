---
title: "Azure Portal を使用して IoT Hub を作成する | Microsoft Docs"
description: "Azure Portal で Azure IoT Hub を作成、管理、および削除する方法。 価格レベル、スケーリング、セキュリティ、およびメッセージングの構成に関する情報が含まれています。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0909cd2b-4c1e-49e0-b68a-75532caf0a6a
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 1915044f252984f6d68498837e13c817242542cf
ms.openlocfilehash: d88c6e8d4c0c5aecbdbcf6262da8d919ad3b325d
ms.lasthandoff: 01/31/2017


---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Azure Portal を使用して IoT Hub を作成する
[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>はじめに
この記事では、Azure Portal で IoT Hub サービスを検索する方法と、IoT hub を作成して管理する方法について説明します。

## <a name="where-to-find-iot-hubs"></a>IoT Hub の検索場所
さまざまな場所で IoT hub を見つけることができます。

1. **[+ 新規]**: **Azure IoT Hub** は IoT サービスであり、他のサービスと同じように、**[+ 新規]** にある **[モノのインターネット]** カテゴリで見つけることができます。
2. また、IoT Hub には、 **[モノのインターネット]**のヒーロー サービスである Marketplace からアクセスできます。

## <a name="create-an-iot-hub"></a>IoT Hub の作成
IoT Hub の作成には、次の方法を使用できます。

* **[+ 新規]** オプションを使用して IoT Hub を作成すると、次のスクリーン ショットに示すブレードが表示されます。 この方法および Marketplace を使用して IoT Hub を作成する手順は同じです。
* Marketplace を使用した IoT Hub の作成: **[作成]** をクリックすると、ブレードが開きます。これは、**[+ 新規]** の場合と同じものです。 次のセクションでは、IoT Hub の作成に関連するいくつかの手順を示します。

### <a name="choose-the-name-of-the-iot-hub"></a>IoT Hub の名前の選択
IoT Hub を作成するには、IoT ハブに名前を付ける必要があります。 この名前は、IoT ハブ全体で一意にする必要があります。 ソリューション バックエンドではハブの重複は許可されないため、このハブの名前はできるだけ一意にすることをお勧めします。

### <a name="choose-the-pricing-tier"></a>価格レベルの選択
**Free**、**Standard 1**、**Standard 2**、**Standard S3** の 4 つのレベルから選択することができます。 Free レベルでは、IoT Hub に接続できるデバイスは 500 個のみで、1 日に許可されるメッセージ数は最大 8,000 件です。

**Standard S1**: IoT Hub S1 エディションは、デバイスごとの生成データが比較的少量であるデバイスが多数存在する IoT ソリューション用に設計されています。 S1 エディションのユニットごとに、すべての接続デバイスでの合計で 1 日に最大 400,000 メッセージを送信できます。

**Standard S2**: IoT Hub S2 エディションは、デバイスが大量のデータを生成する IoT ソリューション用に設計されています。 S2 エディションのユニットごとに、すべての接続デバイスでの合計で 1 日に最大 600 万メッセージを送信できます。

**Standard S3**: IoT Hub S3 エディションは、大量のデータが生成される IoT ソリューションに適しています。 S3 エディションのユニットごとに、すべての接続デバイスでの合計で 1 日に最大 3 億件のメッセージを送信できます。

![][4]

> [!NOTE]
> IoT Hub で許可される無料ハブは Azure サブスクリプションごとに&1; つのみです。
> 
> 

### <a name="iot-hub-units"></a>IoT Hub ユニット
ユニットごとに許可される&1; 日あたりのメッセージの数は、ハブの価格レベルによって決まります。 たとえば、IoT Hub で 700,000 件の受信メッセージをサポートする場合は、S1 レベルのユニットを 2 つ選択します。

### <a name="device-to-cloud-partitions-and-resource-group"></a>デバイスとクラウド間のパーティションおよびリソース グループ
IoT Hub のパーティションの数を変更することができます。 既定のパーティションは 4 に設定されますが、ドロップダウン リストからパーティションの別の数を選択できます。

リソース グループについては、明示的に空のリソース グループを作成する必要はありません。 リソースを作成するときは、新しいリソース グループを作成することも、既存のリソース グループを使用することもできます。

![][5]

### <a name="choose-subscriptions"></a>サブスクリプションの選択
Azure IoT Hub は、ユーザー アカウントがリンクされている Azure サブスクリプションのリストを自動的に表示します。 IoT Hub をその Azure サブスクリプションに関連付ける場合は、ここでオプションのいずれかを選択できます。

### <a name="choose-the-location"></a>場所の選択
場所のオプションでは、IoT Hub が提供されるリージョンのリストが提供されます。 IoT Hub は、オーストラリア東部、オーストラリア南東部、アジア東部、アジア南東部、北ヨーロッパ、西ヨーロッパ、東日本、西日本、米国東部、米国西部にデプロイできるようになっています。

### <a name="create-the-iot-hub"></a>IoT Hub の作成
前の手順がすべて完了したら、IoT Hub を作成することができます。 **[作成]** をクリックし、特定のオプションでこの IoT Hub を作成するバックエンド プロセスを開始して、指定した場所にデプロイします。

適切な場所のサーバーでのバックエンド デプロイメントには時間がかかるため、IoT Hub の作成には数分かかる場合があります。

## <a name="change-the-settings-of-the-iot-hub"></a>IoT Hub の設定変更
IoT Hub ブレードから IoT Hub を作成したら、既存の設定を変更できます。

![][8]

**共有アクセス ポリシー**: これらのポリシーは、IoT Hub に接続するデバイスとサービスのアクセス許可を定義します。 これらのポリシーには、**[一般]** にある **[共有アクセス ポリシー]** をクリックすることでアクセスできます。 このブレードでは、既存のポリシーを変更したり、新しいポリシーを追加したりすることができます。

### <a name="create-a-policy"></a>ポリシーの作成
* **[追加]** をクリックしてブレードを開きます。 このブレードで、次の図に示すように、新しいポリシーの名前と、このポリシーに関連付けるアクセス許可を入力できます。
  
    これらの共有ポリシーに関連付けることができるアクセス許可はいくつかあります。 最初の&2; つのポリシーの **[レジストリの読み取り]** と **[レジストリの書き込み]** は、デバイス ID ストアまたは ID レジストリに対する読み取りと書き込みのアクセス権を付与するためのものです。 書き込みオプションを選択すると、読み取りオプションも自動的に選択されます。
  
     **サービス接続** ポリシーは、IoT Hub に接続するサービスのコンシューマー グループなどのクラウド側エンドポイントへのアクセス許可を付与します。 **デバイス接続** ポリシーは、IoT Hub のデバイス側のエンドポイントにメッセージを送受信するためのアクセス許可を付与します。
* **[作成]** をクリックして、この新しく作成されたポリシーを既存のリストに追加します。

![][10]

## <a name="endpoints"></a>エンドポイント
**[エンドポイント]** をクリックして、変更対象となる IoT Hub のエンドポイントの一覧を表示します。 エンドポイントには、IoT Hub に組み込まれているエンドポイントと、IoT Hub を作成した後に追加したエンドポイントの&2; 種類があります。

![][11]

### <a name="built-in-endpoints"></a>組み込みのエンドポイント
組み込みのエンドポイントには、**クラウドからデバイスへのフィードバック**と**イベント**の&2; つがあります。

* **クラウドからデバイスへのフィードバック**設定: この設定には、メッセージに関する **[クラウドからデバイスの TTL]** (有効期限) と **[保存期間]** (時間単位) の&2; つのサブ設定があります。 IoT Hub を最初に作成したときは、どちらも既定値の&1; 時間に設定されます。 これらの設定を調整するには、スライダーを使用するか、値を入力します。
* **イベント**設定: この設定には複数のサブ設定があり、その中には読み取り専用のものもあります。 これらの設定を以下に示します。

    * **パーティション**: IoT Hub の作成時に既定値が設定されます。 この設定を使用してパーティションの数を変更できます。

    * **イベント ハブと互換性のある名前とエンドポイント**: IoT Hub の作成時に、イベント ハブが内部的に作成され、ユーザーは特定の状況ではアクセスが必要な場合があります。 イベント ハブと互換性のある名前とエンドポイントの値をカスタマイズすることはできませんが、**[コピー]** をクリックすることで値をコピーできます。

    * **保存期間**: 既定で&1; 日に設定されますが、ドロップダウン リストを使用して値を変更できます。 デバイスからクラウド設定では、この値は日数です。

    * **コンシューマー グループ**: コンシューマー グループは、他のアプリケーションやサービスを IoT Hub に接続する特定の方法でデータをプルするために使用できる他のメッセージング システムと同様の設定です。 すべての IoT Hub は、既定のコンシューマー グループを使用して作成されます。 ただし、この設定を使用して、コンシューマー グループを IoT Hub に追加したり、削除したりすることができます。

    > [!NOTE]
    > 既定のコンシューマー グループを編集したり削除したりすることはできません。
    > 
    > 

### <a name="custom-endpoints"></a>カスタム エンドポイント
ポータルを使用して、IoT Hub にカスタム エンドポイントを追加できます。 **[エンドポイント]** ブレードで、上部にある **[追加]** をクリックして **[エンドポイントの追加]** ブレードを開きます。 必要な情報を入力し、**[OK]** をクリックします。 メインの **[エンドポイント]** ブレードに、カスタム エンドポイントが表示されます。

![][13]

カスタム エンドポイントの詳細については、「[リファレンス - IoT Hub エンドポイント][lnk-devguide-endpoints]」を参照してください。

## <a name="routes"></a>ルート
IoT Hub がデバイスからクラウドへのメッセージをディスパッチする方法を管理するには、**[ルート]** をクリックします。

![][14]

IoT Hub にルートを追加するには、**[ルート]*** ブレードの上部にある **[追加]** をクリックし、必要な情報を入力して、**[OK]** をクリックします。メインの **[ルート]** ブレードにルートが表示されます。ルートを編集するには、一覧でルートをクリックします。ルートを有効にするには、一覧でルートをクリックし、**有効/無効**の切り替えを **[オフ]** に設定します。ブレード下部の **[OK]** をクリックして変更を保存します。

![][15]

## <a name="pricing-and-scale"></a>価格とスケール
既存の IoT Hub の価格は **[価格]** 設定を介して変更できますが、次の例外があります。

* 現在の実装では、無料の SKU の IoT Hub のレベルを、有料の SKU のいずれかに変更することはできません。その逆も同じです。
* Azure サブスクリプションで許可される Free レベルの IoT Hub は&1; つのみです。

![][12]

高レベル (S2 または S3) から低レベル (S1 または S2) への移行は、当該日に送信されるメッセージの数が競合していない場合にのみ許可されます。 たとえば、1 日あたりのメッセージの数が 400,000 件を超える場合、IoT Hub のレベルを変更できます。 ただし、S1 レベルに変更すると、その IoT ハブは当該日に制限を受けます。

## <a name="delete-the-iot-hub"></a>IoT Hub の削除
削除する IoT Hub は、 **[参照]**をクリックしてから、削除対象の適切なハブを選択することで参照できます。 IoT ハブ名の下の **[削除]** をクリックすると、その IoT ハブが削除されます。

## <a name="next-steps"></a>次のステップ
Azure IoT Hub の管理についてさらに学習するには、次のリンクを使用してください。

* [IoT デバイスの一括管理][lnk-bulk]
* [IoT Hub メトリック][lnk-metrics]
* [操作の監視][lnk-monitor]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Hub 開発者ガイド][lnk-devguide]
* [IoT Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]
* [IoT ソリューションの徹底的なセキュリティ保護][lnk-securing]

[4]: ./media/iot-hub-create-through-portal/create-iothub.png
[5]: ./media/iot-hub-create-through-portal/location1.png
[8]: ./media/iot-hub-create-through-portal/portal-settings.png
[10]: ./media/iot-hub-create-through-portal/shared-access-policies.png
[11]: ./media/iot-hub-create-through-portal/messaging-settings.png
[12]: ./media/iot-hub-create-through-portal/pricing-error.png
[13]: ./media/iot-hub-create-through-portal/endpoint-creation.png
[14]: ./media/iot-hub-create-through-portal/routes-list.png
[15]: ./media/iot-hub-create-through-portal/route-edit.png

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md

