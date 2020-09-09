---
title: インクルード ファイル
description: インクルード ファイル
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/14/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: d7ac494edfb9b1deeceafcac523ac58bccde96b3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85365277"
---
ここでは、[Azure portal](https://portal.azure.com) を使用して IoT ハブを作成する方法について説明します。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure ホームページから **[+ リソースの作成]** ボタンを選択し、 **[Marketplace を検索]** フィールドに「*IoT Hub*」と入力します。

1. 検索結果の **[IoT Hub]** を選択し、 **[作成]** を選択します。

1. **[基本]** タブで、次のように各フィールドに入力します。

   - **サブスクリプション**:ハブで使用するサブスクリプションを選択します。

   - **リソース グループ**:リソース グループを選択するか、新しく作成します。 新たに作成するには、 **[新規作成]** を選択して、使用する名前を入力します。 既存のリソース グループを使用するには、そのリソース グループを選択します。 詳しくは、[「Manage Azure Resource Manager resource groups (Azure Resource Manager のリソース グループの管理)](../articles/azure-resource-manager/management/manage-resource-groups-portal.md)」をご覧ください。

   - **[リージョン]** :ハブを配置するリージョンを選択します。 ユーザーに最も近い場所を選択します。 一部の機能 ([IoT Hub デバイス ストリーム](../articles/iot-hub/iot-hub-device-streams-overview.md)など) は、特定のリージョンでのみご利用いただけます。 これらの制限のある機能については、サポート対象のいずれかのリージョンを選択する必要があります。

   - **[IoT Hub 名]** : ハブの名前を入力します。 この名前はグローバルに一意である必要があります。 入力した名前が使用可能な場合は、緑色のチェック マークが表示されます。

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Azure portal でハブを作成する](./media/iot-hub-include-create-hub/iot-hub-create-screen-basics.png)

1. **次へ:Size and scale\(次へ: サイズとスケール\)** を選択して、ハブの作成を続けます。

   ![Azure portal を使用して新しいハブのサイズとスケールを設定する](./media/iot-hub-include-create-hub/iot-hub-create-screen-size-scale.png)

   ここでは、既定の設定をそのまま使用できます。 必要に応じて、次のフィールドに変更を加えることができます。 

    - **[価格とスケールティア]** : 選択したレベル。 必要な機能およびソリューションで 1 日に送信するメッセージの数に応じて、複数のレベルから適切なものを選びます。 無料レベルは、テストおよび評価用です。 ハブに接続できるデバイスは 500 個で、1 日に許可されるメッセージ数は最大 8,000 件です。 Azure サブスクリプションごとに、Free レベルの IoT ハブを 1 つ作成できます。 

      IoT Hub デバイス ストリームのクイックスタートに取り組んでいる場合は、Free レベルを選択してください。

    - **[IoT Hub ユニット]** : ユニットごとに許可される 1 日あたりのメッセージの数は、ハブの価格レベルによって決まります。 たとえば、ハブで 700,000 件のイングレス メッセージをサポートする場合は、S1 レベルのユニットを 2 つ選択します。
    他のレベルのオプションについて詳しくは、[適切な IoT Hub レベルの選択](../articles/iot-hub/iot-hub-scaling.md)に関するページをご覧ください。

    - **Azure Security Center**:IoT およびお使いのデバイスに、脅威に対する保護のレイヤーを別途追加するには、これをオンにします。 このオプションは、Free レベルのハブでは使用できません。 この機能の詳細については、[Azure Security Center for IoT](https://docs.microsoft.com/azure/asc-for-iot/) に関するページを参照してください。

    - **[詳細設定]**  >  **[Device-to-cloud パーティション]** : このプロパティでは、device-to-cloud メッセージがそのメッセージの同時閲覧者数に関連付けられます。 ほとんどのハブでは、4 つのパーティションのみが必要となります。

1.  **次へ:[Next]\(次へ\)** を選択して、次の画面に進みます。

    タグは、名前と値の組です。 複数のリソースおよびリソース グループに同じタグを割り当てることで、リソースを分類したり、課金情報を統合したりすることができます。 詳細については、[タグを使用した Azure リソースの整理](../articles/azure-resource-manager/management/tag-resources.md)に関するページを参照してください。

    ![Azure portal を使用してハブにタグを割り当てる](./media/iot-hub-include-create-hub/iot-hub-create-tabs.png)

1.  **次へ:次へ: レビューと作成** をクリックして、選択内容を確認します。 次の画面のようになります。ただし、表示されるのはハブの作成時に選択した値です。 

    ![新しいハブを作成するための情報を確認する](./media/iot-hub-include-create-hub/iot-hub-create-review.png)

1.  **[作成]** を選択して、新しいハブを作成します。 ハブの作成には数分かかります。
