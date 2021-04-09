---
title: インクルード ファイル
description: インクルード ファイル
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/19/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9151a84aa03e9b87b02278672622d458fbc5281e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99538713"
---
Azure portal を使用して IoT ハブを作成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure ホームページで **[リソースの作成]** を選択し、 **[Marketplace を検索]** に「*IoT Hub*」と入力します。

1. 検索結果の **[IoT Hub]** を選択し、 **[作成]** を選択します。

1. **[基本]** タブで、次のように各フィールドに入力します。

   - **サブスクリプション**:ハブで使用するサブスクリプションを選択します。

   - **リソース グループ**:リソース グループを選択するか、新しく作成します。 新たに作成するには、 **[新規作成]** を選択して、使用する名前を入力します。 既存のリソース グループを使用するには、そのリソース グループを選択します。 詳しくは、[「Manage Azure Resource Manager resource groups (Azure Resource Manager のリソース グループの管理)](../articles/azure-resource-manager/management/manage-resource-groups-portal.md)」をご覧ください。 このチュートリアルでは、**tutorials-iot-hub-rg** という名前を使います。

   - **[リージョン]** :ハブを配置するリージョンを選択します。 ユーザーに最も近い場所を選択します。 一部の機能 ([IoT Hub デバイス ストリーム](../articles/iot-hub/iot-hub-device-streams-overview.md)など) は、特定のリージョンでのみご利用いただけます。 これらの制限のある機能については、サポート対象のいずれかのリージョンを選択する必要があります。 このチュートリアルでは、**米国西部** リージョンを使用します。

   - **[IoT Hub 名]** : ハブの名前を入力します。 この名前はグローバルに一意である必要があります。 このチュートリアルでは、**tutorials-iot-hub** を使用します。 ハブを作成するときは、独自の一意名を選ぶ必要があります。

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-basics.png" alt-text="Azure portal でハブを作成する":::

1. **Next:Networking\(次へ: ネットワーク\)** を選択して、ハブの作成を続けます。

   IoT Hub に接続できるエンドポイントを選択します。 既定の設定である **[パブリック エンドポイント (すべてのネットワーク)]** を選択できるほか、 **[Public endpoint (selected IP ranges)]\(パブリック エンドポイント (選択された IP 範囲)\)** または **[プライベート エンドポイント]** を選択できます。 このチュートリアルでは、既定の設定をそのまま使用します。

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-networking.png" alt-text="接続できるエンドポイントを選択する。":::

1. **Next:Management\(次へ: 管理\)** を選択して、ハブの作成を続けます。

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-management.png" alt-text="Azure portal を使用して新しいハブのサイズとスケールを設定する。":::

   ここでは、既定の設定をそのまま使用できます。 必要に応じて、次のフィールドに変更を加えることができます。

   - **[価格とスケールティア]** : 選択したレベル。 Free レベルを選択します。 無料レベルは、テストおよび評価用です。 ハブに接続できるデバイスは 500 個で、1 日に許可されるメッセージ数は最大 8,000 件です。 Azure サブスクリプションごとに、Free レベルの IoT ハブを 1 つ作成できます。

   - **[IoT Hub ユニット]** : ユニットごとに許可される 1 日あたりのメッセージの数は、ハブの価格レベルによって決まります。 たとえば、ハブで 700,000 件のイングレス メッセージをサポートする場合は、S1 レベルのユニットを 2 つ選択します。
   Azure サブスクリプションごとに、Free レベルの IoT ハブを 1 つ作成できます。 他のレベルのオプションについて詳しくは、[適切な IoT Hub レベルの選択](../articles/iot-hub/iot-hub-scaling.md)に関するページをご覧ください。

   - **[Defender for IoT]** : IoT およびお使いのデバイスに、脅威に対する保護のレイヤーを別途追加するには、これをオンにします。 このオプションは、Free レベルのハブでは使用できません。 この機能の詳細については、[Azure Security Center for IoT](/azure/asc-for-iot/) に関するページを参照してください。

   - **[詳細設定]**  >  **[Device-to-cloud パーティション]** : このプロパティでは、device-to-cloud メッセージがそのメッセージの同時閲覧者数に関連付けられます。 ほとんどのハブでは、4 つのパーティションのみが必要となります。 Free レベルのハブは、2 つのパーティションに制限されています。

1. **次へ:[Next]\(次へ\)** を選択して、次の画面に進みます。

   タグは、名前と値の組です。 複数のリソースおよびリソース グループに同じタグを割り当てることで、リソースを分類したり、課金情報を統合したりすることができます。 詳細については、[タグを使用した Azure リソースの整理](../articles/azure-resource-manager/management/tag-resources.md)に関するページを参照してください。

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-tags.png" alt-text="Azure portal を使用してハブにタグを割り当てる。":::

1. **次へ:次へ: レビューと作成** をクリックして、選択内容を確認します。 次の画面のようになります。ただし、表示されるのはハブの作成時に選択した値です。

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-create.png" alt-text="新しいハブを作成するための情報を確認する。":::

1. 選択した IoT ハブの名前を書き留めておきます。 この値は、後ほどチュートリアルの中で使用します。
