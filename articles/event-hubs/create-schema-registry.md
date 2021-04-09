---
title: Azure Event Hubs スキーマ レジストリを作成する
description: この記事では、Azure Event Hubs 名前空間にスキーマ レジストリを作成する方法について説明します。
ms.topic: how-to
ms.date: 12/03/2020
ms.custom: references_regions
ms.openlocfilehash: 45791cd69772be97ca6768184ed17179e04ad9dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576810"
---
# <a name="create-an-azure-event-hubs-schema-registry-preview"></a>Azure Event Hubs スキーマ レジストリを作成する (プレビュー)
この記事では、Azure Event Hubs によってホストされるスキーマ レジストリに、スキーマを含むスキーマ グループを作成する方法について説明します。 Azure Event Hubs のスキーマ レジストリ機能の概要については、「[Event Hubs の Azure スキーマ レジストリ](schema-registry-overview.md)」を参照してください。

> [!NOTE]
> - 現在、**Schema Registry** 機能は **プレビュー** 段階にあります。運用環境のワークロードにはお勧めできません。
> - この機能は **Standard** と **Dedicated** レベルでのみ使用でき、**Basic** レベルでは使用できません。
> - イベント ハブが **仮想ネットワーク** にある場合、同じ仮想ネットワーク内の VM からポータルにアクセスしない限り、Azure portal にスキーマを作成することはできません。 

## <a name="prerequisites"></a>前提条件
[Event Hubs 名前空間を作成します](event-hubs-create.md#create-an-event-hubs-namespace)。 既存の名前空間を使用することもできます。 

## <a name="create-a-schema-group"></a>スキーマ グループを作成する
1. **[Event Hubs 名前空間]** ページに移動します。 
1. 左側のメニューの **[スキーマ レジストリ]** を選択します。 スキーマ グループを作成するには、ツールバーの **[+ スキーマ グループ]** を選択します。 

    :::image type="content" source="./media/create-schema-registry/namespace-page.png" alt-text="[スキーマ レジストリ] ページ":::
1. **[スキーマ グループの作成]** ページで、次の手順を実行します。
    1. スキーマ グループの **名前** を入力します。
    1. **[シリアル化の種類]** については、スキーマ グループ内のすべてのスキーマに適用されるシリアル化形式を選択します。 現時点でサポートされている種類は **Avro** のみであるため、 **[Avro]** を選択します。 
    1. グループ内のすべてのスキーマに対して、**互換モード** を選択します。 **[Avro]** の場合、上位および下位の各互換性モードがサポートされています。 
    1. 次に、 **[作成]** を選択して、スキーマ グループを作成します。 
1. スキーマ グループの一覧で、**スキーマ グループ** の名前を選択します。

    :::image type="content" source="./media/create-schema-registry/select-schema-group.png" alt-text="一覧でスキーマ グループを選択する":::    
1. グループの **[スキーマ グループ]** ページが表示されます。

    :::image type="content" source="./media/create-schema-registry/schema-group-page.png" alt-text="[スキーマ グループ] ページ":::
    

## <a name="add-a-schema-to-the-schema-group"></a>スキーマ グループにスキーマを追加する
このセクションでは、Azure portal を使用してスキーマ グループにスキーマを追加します。 

1. **[スキーマ グループ]** ページで、ツールバーの **[+ スキーマ]** を選択します。 
1. **[スキーマの作成]** ページで、こちらの手順を実行します。
    1. スキーマの **名前** を入力します。
    1. 次の **スキーマ** をテキスト ボックスにコピーします。 スキーマを含むファイルを選択することもできます。
    
        ```json
        {
            "type": "record",
            "name": "AvroUser",
            "namespace": "com.azure.schemaregistry.samples",
            "fields": [
                {
                    "name": "name",
                    "type": "string"
                },
                {
                    "name": "favoriteNumber",
                    "type": "int"
                }
            ]
        }
        ```
    1. **［作成］** を選択します 
1. スキーマの一覧から **スキーマ** を選択します。 

    :::image type="content" source="./media/create-schema-registry/select-schema.png" alt-text="スキーマの選択":::
1. スキーマの **[スキーマの概要]** ページが表示されます。 

    :::image type="content" source="./media/create-schema-registry/schema-overview-page.png" alt-text="[スキーマの概要] ページ":::    
1. 複数のバージョンのスキーマがある場合、これらは **[バージョン]** ドロップダウン リストに表示されます。 バージョンを選択して、そのバージョン スキーマに切り替えます。 

## <a name="create-a-new-version-of-schema"></a>新しいバージョンのスキーマを作成する

1. テキスト ボックス内のスキーマを更新し、 **[検証]** を選択します。 次の例では、新しいフィールド `id` がスキーマに追加されています。 

    :::image type="content" source="./media/create-schema-registry/update-schema.png" alt-text="スキーマを更新する":::    
    
1. 検証の状態と変更を確認し、 **[保存]** を選択します。 

    :::image type="content" source="./media/create-schema-registry/compare-save-schema.png" alt-text="検証の状態と変更を確認して保存する":::     
1. **[スキーマの概要]** ページの **[バージョン]** で [`2`] が選択されていることがわかります。 

    :::image type="content" source="./media/create-schema-registry/new-version.png" alt-text="新しいバージョンのスキーマ":::    
1. [`1`] を選択すると、スキーマのバージョン 1 が表示されます。 

    :::image type="content" source="./media/create-schema-registry/select-version.png" alt-text="バージョンを選択する":::    


## <a name="next-steps"></a>次のステップ
スキーマレジストリの詳細については、「[Event Hubs の Azure スキーマ レジストリ](schema-registry-overview.md)」を参照してください。

