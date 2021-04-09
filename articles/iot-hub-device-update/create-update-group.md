---
title: Device Update for Azure IoT Hub でデバイス グループを作成する | Microsoft Docs
description: Device Update for Azure IoT Hub でデバイス グループを作成します
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: a0894047db1ed7687a1a0f5f87fc4020ddf7c694
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101678635"
---
# <a name="create-device-groups-in-device-update-for-iot-hub"></a>Device Update for IoT Hub でデバイス グループを作成する
Device Update for IoT Hub を使用すると、IoT デバイスのグループに更新プログラムをデプロイできます。

## <a name="prerequisites"></a>前提条件

* [Device Update for IoT Hub が有効になっている IoT ハブにアクセスできること](create-device-update-account.md)。 IoT ハブには、S1 (Standard) レベル以上を使用することをお勧めします。 
* Device Update 用にプロビジョニングされている、IoT ハブ内の IoT デバイス (またはシミュレーター)。
* [少なくとも 1 つの更新プログラムが、プロビジョニング済みのデバイスに正常にインポートされていること。](import-update.md)

## <a name="add-a-tag-to-your-devices"></a>デバイスにタグを追加する  

Device Update for IoT Hub を使用すると、IoT デバイスのグループに更新プログラムをデプロイできます。 グループを作成するには、最初の手順として、IoT ハブ内の対象デバイス セットにタグを追加します。 タグは、デバイスを Device Update に接続した後にのみ、デバイスに正常に追加できます。

次のドキュメントでは、タグを追加および更新する方法について説明します。

### <a name="programmatically-update-device-twin"></a>プログラムを使用してデバイス ツインを更新する

Device Update にデバイスを登録したら、RegistryManager を使用して、適切なタグでデバイス ツインを更新できます。 
[サンプル .NET アプリを使用してタグを追加する方法をご確認ください。](../iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)  
[タグのプロパティについてご確認ください](../iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format)。

#### <a name="device-update-tag-format"></a>Device Update のタグの形式

```markdown
     "tags": {
              "ADUGroup": "<CustomTagValue>"
             }
```

### <a name="using-jobs"></a>ジョブの使用

[これら](../iot-hub/iot-hub-devguide-jobs.md)の例に従って、Device Update のタグを追加または更新するジョブを、複数のデバイスに対してスケジュールできます。 [詳細については、こちらを参照してください](../iot-hub/iot-hub-csharp-csharp-schedule-jobs.md)。

  > [!NOTE] 
  > このアクションは、IOT Hub の現在のメッセージ クォータに反しているため、一度に変更するデバイス ツイン タグの数は 50,000 以下にすることをお勧めします。そうしないと、IOT Hub の 1 日のメッセージ クォータを超過した場合に IOT Hub のユニットをさらに購入する必要が生じる場合があります。 詳細については、「[クォータと調整](../iot-hub/iot-hub-devguide-quotas-throttling.md#quotas-and-throttling)」を参照してください。

### <a name="direct-twin-updates"></a>ツインの直接更新

タグは、デバイス ツインに直接追加したり更新したりすることもできます。

1. [Azure portal](https://portal.azure.com) にログインし、自分の IoT ハブに移動します。

2. 左側のナビゲーション ウィンドウの [IoT デバイス] または [IoT Edge] から、対象の IoT デバイスを見つけて、デバイス ツインに移動します。

3. デバイス ツインで、Device Update の既存のタグ値を null に設定することで削除します。

4. 次に示すように、Device Update の新しいタグ値を追加します。 [タグを含むデバイス ツイン JSON ドキュメントの例。](../iot-hub/iot-hub-devguide-device-twins.md#device-twins)

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

### <a name="limitations"></a>制限事項

* 予約済みの値である "Uncategorized" を除き、タグには任意の値を追加できます。
* 255 文字を超えるタグ値は指定できません。
* タグ値には、英数字と特殊文字の "."、"-"、"_"、"~" を含めることができます。
* タグ名およびグループ名では、大文字と小文字が区別されます。
* デバイスには、ADUGroup という名前のタグを 1 つだけ付けることができます。その後、その名前のタグを追加するたびに、タグ名 ADUGroup の既存の値がオーバーライドされます。
* 各デバイスは 1 つのグループにのみ属することができます。

## <a name="create-a-device-group-by-selecting-an-existing-iot-hub-tag"></a>既存の IoT Hub タグを選択してデバイス グループを作成する

1. [Azure ポータル](https://portal.azure.com)にアクセスします。

2. 以前に Device Update インスタンスに接続した IoT ハブを選択します。

3. 左側のナビゲーション バーの [自動デバイス管理] にある [デバイスの更新] オプションを選択します。

4. ページの上部にある [グループ] タブを選択します。 Device Update に接続されているものの、まだグループ化されていないデバイスの数を確認できます。
   :::image type="content" source="media/create-update-group/ungrouped-devices.png" alt-text="グループ化されていないデバイスのスクリーンショット。" lightbox="media/create-update-group/ungrouped-devices.png":::

5. [追加] ボタンを選択して、新しいグループを作成します。
   :::image type="content" source="media/create-update-group/add-group.png" alt-text="デバイス グループの追加のスクリーンショット。" lightbox="media/create-update-group/add-group.png":::

6. 一覧から IoT Hub タグを選択し、[Create update group]\(更新プログラム グループの作成\) を選択します。
   :::image type="content" source="media/create-update-group/select-tag.png" alt-text="タグの選択のスクリーンショット。" lightbox="media/create-update-group/select-tag.png":::

7. グループを作成すると、更新プログラムのコンプライアンス チャートとグループの一覧が更新されていることを確認できます。  更新プログラムのコンプライアンス チャートには、さまざまなコンプライアンス対応状態 (最新の更新状態、利用可能な新しい更新プログラムがある、更新プログラムのインストールが進行中、デバイスがまだグループ化されていない) にあるデバイスの数が表示されます。 [更新プログラムのコンプライアンスについてご確認ください。](device-update-compliance.md)
   :::image type="content" source="media/create-update-group/updated-view.png" alt-text="更新プログラムのコンプライアンス ビューのスクリーンショット。" lightbox="media/create-update-group/updated-view.png":::

8. 新しく作成したグループと、新しいグループ内のデバイスで利用可能な更新プログラムが表示されます。 このビューで更新プログラムの名前をクリックすると、新しいグループに更新プログラムをデプロイできます。 詳細については、「次のステップ」の「更新プログラムをデプロイする」を参照してください。

## <a name="view-device-details-for-the-group-you-created"></a>作成したグループのデバイスの詳細を表示する

1. 新しく作成したグループに移動し、グループ名をクリックします。

2. グループに含まれるデバイスの一覧が、デバイスの更新プログラムのプロパティと共に表示されます。 このビューには、グループのメンバーであるすべてのデバイスについて、更新プログラムのコンプライアンス対応情報も表示されます。 更新プログラムのコンプライアンス チャートには、さまざまなコンプライアンス対応状態 (最新の更新状態、利用可能な新しい更新プログラムがある、更新プログラムのインストールが進行中) にあるデバイスの数が表示されます。
   :::image type="content" source="media/create-update-group/group-details.png" alt-text="デバイス グループの詳細ビューのスクリーンショット。" lightbox="media/create-update-group/group-details.png":::

3. また、グループ内の個々のデバイスをクリックして、IoT ハブ内のデバイスの詳細ページに移動することもできます。
   :::image type="content" source="media/create-update-group/device-details.png" alt-text="デバイスの詳細ビューのスクリーンショット。" lightbox="media/create-update-group/device-details.png":::

## <a name="next-steps"></a>次の手順 

[更新プログラムをデプロイする](deploy-update.md)

[デバイス グループの詳細情報](device-update-groups.md)

[更新プログラムのコンプライアンスの詳細情報。](device-update-compliance.md)
