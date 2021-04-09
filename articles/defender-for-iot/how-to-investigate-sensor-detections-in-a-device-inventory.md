---
title: 特定のセンサーによって検出されたデバイスに関する分析情報を得る
description: デバイス インベントリには、センサーで検出された広範なデバイス属性が表示されます。
ms.date: 12/06/2020
ms.topic: how-to
ms.openlocfilehash: 4daec83f44a545d7837a7e73e847f56b1f5770e7
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782097"
---
# <a name="investigate-sensor-detections-in-a-device-inventory"></a>デバイス インベントリのセンサー検出を調査する

デバイス インベントリには、センサーで検出された広範なデバイス属性が表示されます。 次のオプションを使用できます。

 - 情報を簡単にフィルター処理します。

 - CSV ファイルに情報をエクスポートします。

 - Windows レジストリの詳細をインポートします。

 - デバイス マップに表示するグループを作成します。

## <a name="view-device-attributes-in-the-device-inventory"></a>デバイス インベントリのデバイス属性を表示する

デバイス インベントリ テーブルには、次の属性が表示されます。

| パラメーター | 説明 |
|--|--|
| 名前 | センサーによって検出された、またはユーザーが入力したデバイスの名前。 |
| Type | センサーによって決定された、またはユーザーが入力したデバイスの種類。 |
| ベンダー | MAC アドレスで定義されている、デバイスの製造元の名前。 |
| オペレーティング システム | デバイスの OS (検出された場合)。 |
| Firmware version | デバイスのファームウェア (検出された場合)。 |
| IP アドレス | 定義されているデバイスの IP アドレス。 |
| VLAN | デバイスの VLAN。 センサーに VLAN を検出するように指示する方法の詳細については、[VLAN 名の定義](how-to-manage-the-on-premises-management-console.md#define-vlan-names)に関するページを参照してください。(how-to-define-management-console-network-settings.md#define-vlan-names)。 |
| MAC アドレス | デバイスの MAC アドレス。 |
| プロトコル | デバイスで使用するプロトコル。 |
| 未確認アラート | このデバイスに関連する未確認のアラートの数。 |
| 認可済みである | ユーザーによって定義された認可の状態:<br />- **True**:デバイスは認可されています。<br />- **False**:デバイスは認可されていません。 |
| スキャナーとして認識されている | ユーザーによってネットワーク スキャン デバイスとして定義されています。 |
| プログラミング デバイスである | ユーザーによって認可されたプログラミング デバイスとして定義されています。 <br />- **True**:デバイスは、エンジニアリング ステーションに関連する PLC、RTU、およびコントローラーのプログラミング アクティビティを実行します。 <br />- **False**:デバイスはプログラミング デバイスではありません。 |
| グループ | このデバイスが参加するグループ。 |
| 最終アクティビティ | デバイスで最後に実行されたアクティビティ。 |
| Discovered | このデバイスがネットワークに最初に表示された日時。 |

デバイス インベントリを表示するには、次の操作を行います。

1. 左側のウィンドウで **[デバイス]** を選択します。 右側に **[デバイス]** ウィンドウが開きます。

2. **[デバイス]** ウィンドウで、 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/device-pane-icon.png" border="false"::: を選択します。

列を非表示にしたり表示したりするには、デバイス インベントリ テーブルをカスタマイズします。

1. デバイス インベントリの右上のメニューで、:::image type="icon" source="media/how-to-work-with-asset-inventory-information/settings-icon.png" border="false"::: を選択します。

    :::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-settings-screens-v2.png" alt-text="デバイス インベントリの設定画面。":::

2. **[デバイス インベントリ設定]** ウィンドウで、デバイス インベントリ テーブルに表示する列を選択します。

3. 矢印を使用して、テーブル内の列の位置を変更します。

4. **[保存]** を選択します。 **[デバイス インベントリの設定]** ウィンドウが閉じて、テーブルに新しい設定が表示されます。

### <a name="create-temporary-device-inventory-filters"></a>一時的なデバイス インベントリのフィルターを作成する

テーブルに表示される情報を定義するフィルターを設定できます。 たとえば、PLC デバイスの情報のみを表示するように決定できます。

:::image type="content" source="media/how-to-work-with-asset-inventory-information/devices-learning-v2.png" alt-text="デバイスの知識。":::

このフィルターは、インベントリを離れるときに保存されません。

### <a name="save-device-inventory-filters"></a>デバイス インベントリのフィルターを保存する

必要なフィルターまたはフィルターの組み合わせを保存し、デバイス インベントリに再適用することができます。 特定のデバイスの種類に基づいて幅広いフィルターを、または特定の種類と特定のプロトコルに基づいてより狭いフィルターを作成します。

保存するフィルターは、デバイス マップ グループとしても保存されます。 この機能により、マップ上のネットワーク デバイスを表示する際の精度がさらに向上します。

フィルターを作成するには、次の操作を行います。

1. フィルター処理する列で、:::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-icon.png" border="false"::: を選択します。

2. **[フィルター]** ダイアログ ボックスで、フィルターの種類を選択します。

   - **Equals**:正確な値。これに従って、列をフィルター処理します。 たとえば、**Equals** と `value=ICMP` に従ってプロトコル列をフィルター処理すると、この列には ICMP プロトコルのみを使用するデバイスが表示されます。

   - **Contains**:列の他の値の中に含まれる値。 たとえば、**Contains** と `value=ICMP` に従ってプロトコル列をフィルター処理すると、この列には、デバイスで使用されるプロトコルの一覧の一部として ICMP プロトコルを使用するデバイスが表示されます。

3. アルファベット順に従って列情報を整理するには、:::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false"::: を選択します。 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: と :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: の矢印を選択して、順序を変更します。

4. 新しいフィルターを保存するには、フィルターを定義し、 **[名前を付けて保存]** を選択します。

5. フィルター定義を変更するには、定義を変更し、 **[変更の保存]** を選択します。

フィルターを表示するには、次の操作を行います。

- 左側のウィンドウを開いて、保存したフィルターを表示します。

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-from-left-pane-v2.png" alt-text="左側のウィンドウからフィルターを表示します。":::

### <a name="view-filtered-information-as-a-map-group"></a>フィルター処理された情報をマップ グループとして表示する

マップ ビューに切り替えると、フィルター処理されたデバイスが強調表示され、フィルター処理されます。 保存したフィルター グループは、 **[デバイス インベントリのフィルター]** グループの下のサイド メニューに表示されます。

:::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-in-the-map-view-v2.png" alt-text="マップ ビューでフィルターを表示します。":::

## <a name="learn-windows-registry-details"></a>Windows レジストリの詳細を知る

OT デバイスについて知るだけでなく、Microsoft Windows ワークステーションやサーバーを見つけることができます。 これらのデバイスも、デバイス インベントリに表示されます。 デバイスについて把握した後、次のような詳細な Windows 情報を使用してデバイス インベントリを強化できます。

- インストールされている Windows のバージョン

- インストールされているアプリケーション

- パッチ レベル情報

- ポートを開く

- OS バージョンに関するより確かな情報

この情報を取得するには、次の 2 つのオプションを使用できます。

- スケジュールされた WMI スキャンを使用したアクティブなポーリング。 

- デバイスでスクリプトを配布して実行することによる、ローカルでの調査。 ローカル スクリプトを使用すると、エンドポイントで WMI ポーリングを実行するリスクを回避できます。 また、ウォーターフォールと一方向の要素を持つ規制対象ネットワークにも役立ちます。

この記事では、スクリプトを使用して Windows エンドポイント レジストリをローカルで調査する方法について説明します。 この情報は、アラート、通知、データ マイニング レポート、リスク評価、および攻撃ベクトル レポートの生成に使用されます。

:::image type="content" source="media/how-to-work-with-asset-inventory-information/data-mining-screen.png" alt-text="データ マイニングのスクリーンショット。":::

次の Windows オペレーティング システムを調査できます。

- Windows XP

- Windows 2000

- Windows NT

- Windows 7

- Windows 10

- Windows Server 2003/2008/2012/2016

### <a name="before-you-begin"></a>開始する前に

スクリプトを使用するには、次の要件を満たす必要があります。

- デバイスでスクリプトを実行するには、管理者アクセス許可が必要である。

- センサーが既に Windows デバイスについて把握している。 これは、デバイスが既に存在する場合は、スクリプトがその情報を取得することを意味します。

- センサーが、Windows PC が接続されているネットワークを監視している。

### <a name="acquire-the-script"></a>スクリプトを取得する

このスクリプトを受信するには、[カスタマー サポートにお問い合わせください](mailto:support.microsoft.com)。

### <a name="deploy-the-script"></a>スクリプトをデプロイする

標準の自動デプロイの方法とツールを使用して、スクリプトを 1 回デプロイしたり、実行中のクエリをスケジュールしたりすることができます。

### <a name="about-the-script"></a>スクリプトについて

- スクリプトは、インストールされているプログラムではなく、ユーティリティとして実行されます。 スクリプトを実行しても、エンドポイントには影響しません。

- スクリプトによって生成されるファイルは、削除するまでローカル ドライブに残ります。

- スクリプトによって生成されるファイルは、互いに隣り合って配置されています。 これらをばらばらにしないでください。

- スクリプトを同じ場所で再度実行すると、これらのファイルは上書きされます。

スクリプトを実行するには、次の手順を実行します。  

1. スクリプトをローカル ドライブにコピーして解凍します。 次のファイルが表示されます。

    - start.bat

    - settings.json

    - data.bin

    - run.bat

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/files-in-file-explorer.png" alt-text="ファイル エクスプローラー内のファイルのビュー。":::

2. `run.bat` ファイルを実行します。

3. レジストリが調査されると、レジストリ情報と共に CX スナップショット ファイルが表示されます。

4. ファイル名は、スナップショットのシステム名と日時を示します。 ファイル名の例としては、`CX-snaphot_SystemName_Month_Year_Time` があります。

### <a name="import-device-details"></a>デバイスの詳細をインポートする

各エンドポイントで把握した情報は、センサーにインポートする必要があります。

クエリから生成されたファイルは、センサーからアクセスできる 1 つのフォルダーに配置できます。 標準的な自動化された方法とツールを使用して、各 Windows エンドポイントからセンサーにインポートする場所にファイルを移動します。

ファイル名は更新しないでください。

インポートするには、次の操作を行います。

1. **[Windows 構成のインポート]** ダイアログ ボックスで **[設定のインポート]** を選択します。

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/import-windows-configuration-v2.png" alt-text="Windows 構成をインポートします。":::

2. **[追加]** を選択し、すべてのファイルを選択します (Ctrl+A)。

3. **[閉じる]** を選択します。 デバイスのレジストリ情報がインポートされます。 いずれかのファイルのアップロードで問題が発生した場合は、ファイルのアップロードに失敗したことが通知されます。

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-new-file.png" alt-text="追加されたファイルのアップロードに成功しました。":::

## <a name="export-device-inventory-information"></a>デバイスのインベントリ情報をエクスポートする

デバイス インベントリ情報を Excel ファイルにエクスポートできます。

CSV ファイルをエクスポートするには、次の操作を行います。

- デバイス インベントリの右上のメニューで、:::image type="icon" source="media/how-to-work-with-asset-inventory-information/csv-excel-export-icon.png" border="false"::: を選択します。 CSV レポートが生成され、ダウンロードされます。

## <a name="see-also"></a>関連項目

[デバイス インベントリのすべてのエンタープライズ センサー検出を調査する](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)

[サイト マップ ビューの使用](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)
