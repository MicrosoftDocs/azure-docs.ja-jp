---
title: Azure Portal を使用して IoT Hub を作成する | Microsoft Docs
description: Azure Portal で Azure IoT Hub を作成、管理、および削除する方法。 価格レベル、スケーリング、セキュリティ、およびメッセージングの構成に関する情報が含まれています。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: 0a54d0c85902915d2ee62acd8a1d38b8db8b221c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92536047"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Azure Portal を使用して IoT Hub を作成する

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

この記事では、[Azure portal](https://portal.azure.com) を使用して、IoT Hub を作成して管理する方法について説明します。

このチュートリアルの手順を使用するには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="change-the-settings-of-the-iot-hub"></a>IoT Hub の設定変更

IoT Hub ウィンドウから IoT Hub を作成したら、既存の IoT Hub の設定を変更できます。

![IoT Hub の設定を示すスクリーンショット](./media/iot-hub-create-through-portal/iot-hub-settings-panel.png)

IoT Hub に対して設定できるプロパティをいくつか以下に示します。

**価格とスケール**:このプロパティを使用して、別のレベルに移行したり、IoT Hub ユニットの数を設定したりすることができます。 

**操作の監視**:デバイスからクラウドへのメッセージまたはクラウドからデバイスへのメッセージに関連するイベントのログ記録など、さまざまな監視カテゴリをオンまたはオフにします。

**IP フィルター**:IoT Hub で許可または拒否される IP アドレスの範囲を指定します。

**[プロパティ]** :リソース ID、リソース グループ、場所など、コピーして別の場所で使用できるプロパティのリストが提供されます。

### <a name="shared-access-policies"></a>共有アクセス ポリシー

**[設定]** セクションの **[共有アクセス ポリシー]** をクリックして、共有アクセス ポリシーのリストを表示したり、変更したりすることもできます。 これらのポリシーで、IoT Hub に接続するデバイスとサービス用のアクセス許可を定義します。 

**[追加]** をクリックして、 **[共有アクセスポリシーを追加]** ブレードを開きます。  次の図に示すように、新しいポリシーの名前と、このポリシーに関連付けるアクセス許可を入力できます。

![共有アクセス ポリシーの追加を示すスクリーンショット](./media/iot-hub-create-through-portal/iot-hub-add-shared-access-policy.png)

* **レジストリの読み取り** ポリシーと **レジストリの書き込み** ポリシーは、ID レジストリに対する読み取りと書き込みのアクセス権を付与します。 これらのアクセス許可は、バックエンド クラウド サービスがデバイス ID の管理に使用します。 書き込みオプションを選択すると、読み取りオプションが自動的に選択されます。

* **サービス接続** ポリシーは、サービス エンドポイントへのアクセス許可を付与します。 このアクセス許可は、デバイスからのメッセージの送受信やデバイス ツインおよびモジュール ツインのデータの更新および読み取りを行うために、バックエンド クラウド サービスが使用します。

* **デバイス接続** ポリシーは、IoT Hub デバイス側エンドポイントを使用してメッセージを送受信するためのアクセス許可を付与します。 このアクセス許可は、IoT Hub からのメッセージの送受信、デバイス ツインおよびモジュール ツインのデータの更新および読み取り、およびファイルのアップロードを実行するために、デバイスが使用します。

**[作成]** をクリックして、この新しく作成されたポリシーを既存のリストに追加します。

特定のアクセス許可によって付与されるアクセスの詳細については、[IoT Hub のアクセス許可に関するセクション](./iot-hub-devguide-security.md#iot-hub-permissions)を参照してください。

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT ハブに新しいデバイスを登録する

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="message-routing-for-an-iot-hub"></a>IoT Hub のメッセージ ルーティング

**[メッセージング]** で **[メッセージ ルーティング]** をクリックして、メッセージ ルーティング ウィンドウを表示します。ここでハブのルートとカスタム エンドポイントを定義することができます。 [メッセージ ルーティング](iot-hub-devguide-messages-d2c.md) では、デバイスからエンドポイントへのデータの送信方法を管理することができます。 最初の手順では、新しいルートを追加します。 その後、ルートに既存のエンドポイントを追加したり、Blob Storage など、サポートされる新しい種類のいずれかを作成したりすることができます。 

![メッセージ ルーティング ウィンドウ](./media/iot-hub-create-through-portal/iot-hub-message-routing.png)

### <a name="routes"></a>ルート

[ルート] は、メッセージ ルーティング ウィンドウで最初のタブです。 新しいルートを追加するには、[+ **追加]** をクリックします。 次の画面が表示されます。 

![新しいルートの追加を示すスクリーンショット](./media/iot-hub-create-through-portal/iot-hub-add-route-storage-endpoint.png)

ルートに名前を指定します。 ルート名は、そのハブのルートのルート リスト内で一意である必要があります。 

**[エンドポイント]** では、ドロップダウン リストのいずれかを選択したり、新しいものを追加したりすることができます。 この例では、ストレージ アカウントとコンテナーは既に使用可能です。 それらをエンドポイントとして追加するには、エンドポイント ドロップダウンの横にある [+ **追加]** をクリックして、 **[Blob Storage]** を選択します。 次の画面には、ストレージ アカウントとコンテナーが指定されている場所が示されています。

![ルーティング規則用のストレージ エンドポイントの追加を示すスクリーンショット](./media/iot-hub-create-through-portal/iot-hub-routing-add-storage-endpoint.png)

**[コンテナーを選択します]** をクリックして、ストレージ アカウントとコンテナーを選択します。 これらのフィールドを選択すると、エンドポイント ウィンドウに戻ります。 残りのフィールドでは既定値を使用して、 **[作成]** を選択してストレージ アカウントのエンドポイントを作成し、それをルーティング規則に追加します。

**[データ ソース]** では、[Device Telemetry Messages]\(デバイス テレメトリ メッセージ\) を選びます。 

次に、ルーティング クエリを追加します。 この例では、`critical` と同じ値の `level` と呼ばれるアプリケーション プロパティを含むメッセージが、ストレージ アカウントにルーティングされます。

![新しいルーティング規則の保存を示すスクリーンショット](./media/iot-hub-create-through-portal/iot-hub-add-route.png)

**[保存]** をクリックして、ルーティング規則を保存します。 メッセージ ルーティング ウィンドウに戻ると、新しいルーティング規則が表示されます。

### <a name="custom-endpoints"></a>カスタム エンドポイント

**[カスタム エンドポイント]** タブをクリックします。既に作成されているカスタム エンドポイントがすべて表示されます。 ここで、新しいエンドポイントを追加したり、既存のエンドポイントを削除したりすることができます。 

> [!NOTE]
> ルートを削除しても、そのルートに割り当てられているエンドポイントは削除されません。 エンドポイントを削除するには、[カスタム エンドポイント] タブをクリックして、削除するエンドポイントを選択し、[削除] をクリックします。
>

カスタム エンドポイントの詳細については、「[リファレンス - IoT Hub エンドポイント](iot-hub-devguide-endpoints.md)」を参照してください。

IoT Hub に対して最大 10 個のカスタム エンドポイントを定義できます。 

ルーティングでカスタム エンドポイントを使用する方法の完全な例を確認する場合は、[IoT Hub でのメッセージ ルーティング](tutorial-routing.md)に関するページを参照してください。

## <a name="find-a-specific-iot-hub"></a>特定の IoT Hub を見つける

サブスクリプション内の特定の IoT Hub を見つける方法は次の 2 つです。

1. IoT Hub が属しているリソース グループがわかっている場合は、 **[リソース グループ]** をクリックし、リストからリソース グループを選択します。 リソース グループ画面には、IoT Hub を含め、そのグループ内のすべてのリソースが表示されます。 探しているハブをクリックします。

2. **[すべてのリソース]** をクリックします。 **[すべてのリソース]** ウィンドウには、既定で `All types` に設定されているドロップダウン リストがあります。 ドロップダウン リストをクリックして、`Select all` をオフにします。 `IoT Hub` を見つけて、オンにします。 ドロップダウン リスト ボックスをクリックして閉じると、エントリがフィルター処理され、自分の IoT Hub のみが表示されます。

## <a name="delete-the-iot-hub"></a>IoT Hub の削除

IoT Hub を削除するには、削除する IoT Hub を見つけて、IoT Hub 名の下にある **[削除]** ボタンをクリックします。

## <a name="next-steps"></a>次のステップ

Azure IoT Hub の管理についてさらに学習するには、次のリンクを使用してください。

* [IoT Hub でのメッセージ ルーティング](tutorial-routing.md)
* [IoT Hub の監視](monitor-iot-hub.md)
