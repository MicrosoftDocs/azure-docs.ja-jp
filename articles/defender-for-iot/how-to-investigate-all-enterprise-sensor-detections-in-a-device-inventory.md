---
title: すべてのエンタープライズ センサーによって検出されたデバイスについて
description: オンプレミスの管理コンソールのデバイス インベントリを使用して、接続されているセンサーからのデバイス情報の包括的なビューを取得します。 この情報を管理するには、インポート、エクスポート、およびフィルター処理のツールを使用します。
ms.date: 12/02/2020
ms.topic: how-to
ms.openlocfilehash: 0ae59123b59cfb54cba2a2ee9bdeefb411c8793b
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782182"
---
# <a name="investigate-all-enterprise-sensor-detections-in-the-device-inventory"></a>デバイス インベントリのすべてのエンタープライズ センサー検出を調査する

オンプレミスの管理コンソールで *デバイス インベントリ* を使用して、接続されているセンサーからデバイス情報を表示できます。 この機能により、すべてのネットワーク情報を包括的に表示できます。 この情報を管理するには、インポート、エクスポート、およびフィルター処理のツールを使用します。 接続されているセンサーのバージョンに関するステータス情報も表示されます。

:::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-data-table.png" alt-text="デバイス インベントリのデータ テーブルのスクリーンショット。":::

次の表では、デバイス インベントリのテーブルの列について説明します。

| パラメーター | [説明] |
|--|--|
| **未確認アラート** | このデバイスに関連する未処理のアラートの数。 |
| **事業単位** | このデバイスが含まれている事業単位。 |
| **リージョン** | このデバイスが含まれているリージョン。 |
| **サイト** | このデバイスが含まれているサイト。 |
| **ゾーン** | このデバイスが含まれているゾーン。 |
| **アプライアンス** | このデバイスを保護する Azure Defender for IoT センサー。 |
| **名前** | Defender for IoT によって検出された、このデバイスの名前。 |
| **Type** | デバイスの種類 (PLC や HMI など)。 |
| **ベンダー名** | MAC アドレスで定義されている、デバイスの製造元の名前。 |
| **オペレーティング システム** | デバイスの OS。 |
| **ファームウェア** | デバイスのファームウェア。 |
| **IP アドレス** | デバイスの IP アドレス。 |
| **VLAN** | デバイスの VLAN。 |
| **MAC アドレス** | デバイスの MAC アドレス。 |
| **プロトコル** | デバイスで使用するプロトコル。 |
| **未確認アラート** | このデバイスに関連する未処理のアラートの数。 |
| **認可済みである** | デバイスの認可の状態:<br />- **True**:デバイスは認可されています。<br />- **False**:デバイスは認可されていません。 |
| **スキャナーとして認識されている** | このデバイスがネットワーク内でスキャンのようなアクティビティを実行するかどうか。 |
| **プログラミング デバイスである** | これがプログラミング デバイスであるかどうか:<br />- **True**:デバイスは、エンジニアリング ステーションに関連する PLC、RTU、およびコントローラーのプログラミング アクティビティを実行します。<br />- **False**:デバイスはプログラミング デバイスではありません。 |
| **グループ** | このデバイスが参加するグループ。 |
| **最終アクティビティ** | デバイスで最後に実行されたアクティビティ。 |
| **Discovered** | このデバイスがネットワークに最初に表示された日時。 |

## <a name="integrate-data-into-the-enterprise-device-inventory"></a>エンタープライズ デバイス インベントリにデータを統合する

データ統合機能を使用すると、他のエンタープライズ リソースからの情報でデバイス インベントリのデータを強化できます。 これらのリソースには、CMDB、DNS、ファイアウォール、Web API などがあります。

この情報を使用して学習できます。 例:

- デバイスの購入日と保証終了日

- 各デバイスを担当するユーザー

- デバイスのオープンになっているチケット

- ファームウェアがアップグレードされた最後の日付

- インターネットへのアクセスが許可されているデバイス

- アクティブなウイルス対策アプリケーションを実行しているデバイス

- デバイスにサインインしているユーザー

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-screen-with-items-highlighted-v2.png" alt-text="デバイス インベントリ画面のデータ テーブル。":::

次のいずれかによってデータを統合できます。

- 手動で追加する

- Defender for IoT で提供されるカスタマイズされたスクリプトを実行する

:::image type="content" source="media/how-to-work-with-asset-inventory-information/enterprise-data-integrator-graph.png" alt-text="エンタープライズ データ インテグレーターの図。":::

Defender for IoT テクニカル サポートと連携して、Web API クエリを受信するようにシステムを設定することができます。

データを手動で追加するには、次の操作を行います。

1. サイド メニューで、 **[デバイス インベントリ]** を選択し、:::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon.png" border="false"::: を選択します。

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-settings-v2.png" alt-text="デバイスのインベントリ設定を編集します。":::

2. **[デバイス インベントリの設定]** ダイアログ ボックスで、 **[カスタム列の追加**] を選択します。

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="カスタム列をインベントリに追加します。":::

3. **[カスタム列の追加]** ダイアログ ボックスで、新しい列名 (最大 250 文字) を追加し、 **[手動]** を選択し、 **[保存]** を選択します。 新しい項目が **[デバイス インベントリの設定]** ダイアログ ボックスに表示されます。

4. **[デバイス インベントリ]** ウィンドウの右上隅にある :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: を選択し、 **[すべてのデバイス インベントリのエクスポート]** を選択します。 CSV ファイルが生成されます。

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/sample-exported-csv-file.png" alt-text="エクスポートされた CSV ファイル。":::

5. 新しい列に手動で情報を追加し、ファイルを保存します。

6. **[デバイス インベントリ]** ウィンドウの右上隅にある :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: を選択し、 **[手動入力列のインポート]** を選択して、CSV ファイルを参照します。 新しいデータが **[デバイス インベントリ]** テーブルに表示されます。

他のエンタープライズ エンティティのデータを統合するには、次の操作を行います。

1. **[デバイス インベントリ]** ウィンドウの右上隅にある :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: を選択し、 **[すべてのデバイス インベントリのエクスポート]** を選択します。

2. **[デバイス インベントリの設定]** ダイアログ ボックスで、 **[カスタム列の追加**] を選択します。

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="カスタム列をインベントリに追加します。":::

3. **[カスタム列の追加]** ダイアログ ボックスで、新しい列名 (最大 250 文字) を追加し、 **[自動]** を選択します。 **[スクリプトのアップロード]** および **[スクリプトのテスト]** オプションが表示されます。

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column-automatic.png" alt-text="カスタム列を自動的に追加します。":::

4. [Microsoft サポート](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)から受け取ったスクリプトをアップロードしてテストします。

## <a name="retrieve-information-from-the-device-inventory"></a>デバイス インベントリから情報を取得する

管理センサーによって検出された広範なデバイス情報を取得し、その情報をパートナー システムと統合することができます。 たとえば、センサー、ゾーン、サイト ID、IP アドレス、MAC アドレス、ファームウェア、プロトコル、およびベンダー情報を取得できます。 以下に基づいて取得する情報をフィルター処理します。

- 認可済みおよび未認可のデバイス。

- 特定のサイトに関連付けられているデバイス。

- 特定のゾーンに関連付けられているデバイス。

- 特定のセンサーに関連付けられているデバイス。

この情報を取得して統合するには、Defender for IoT API コマンドを使用します。 詳細については、「[Defender for IoT API センサーおよび管理コンソール API](references-work-with-defender-for-iot-apis.md)」を参照してください。

## <a name="filter-the-device-inventory"></a>デバイス インベントリをフィルター処理する

デバイス インベントリをフィルター処理して、目的の列を表示することができます。 たとえば、PLC デバイス情報を表示できます。

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-view-v2.png" alt-text="デバイス インベントリのスクリーンショット。":::

このフィルターは、このウィンドウを離れるとクリアされます。

同じフィルターを複数回使用する場合は、必要なフィルターまたはフィルターの組み合わせを保存することができます。 左側のウィンドウを開いて、保存したフィルターを表示できます。

:::image type="content" source="media/how-to-work-with-asset-inventory-information/view-your-asset-inventories-v2.png" alt-text="デバイス インベントリの画面。":::

デバイス インベントリをフィルター処理するには、次の操作を行います。

1. フィルター処理する列で、:::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-a-column-icon.png" border="false"::: を選択します。

2. **[フィルター]** ダイアログ ボックスで、フィルターの種類を選択します。

   - **Equals**:正確な値。これに従って、列をフィルター処理します。 たとえば、**Equals** と `value=ICMP` に従ってプロトコル列をフィルター処理すると、この列には ICMP プロトコルのみを使用するデバイスが表示されます。

   - **Contains**:列の他の値の中に含まれる値。 たとえば、**Contains** と `value=ICMP` に従ってプロトコル列をフィルター処理すると、この列には、デバイスで使用されるプロトコルの一覧の一部として ICMP プロトコルを使用するデバイスが表示されます。

3. アルファベット順に従って列情報を整理するには、:::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false"::: を選択します。 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: と :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: の矢印を選択して、順序を変更します。

4. 新しいフィルターを保存するには、フィルターを定義し、 **[名前を付けて保存]** を選択します。

5. フィルター定義を変更するには、定義を変更し、 **[変更の保存]** を選択します。

## <a name="next-steps"></a>次のステップ

[デバイス インベントリのセンサー検出を調査する](how-to-investigate-sensor-detections-in-a-device-inventory.md)
