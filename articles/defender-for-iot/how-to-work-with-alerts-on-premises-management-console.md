---
title: オンプレミス管理コンソールでアラートを操作する
description: オンプレミス管理コンソールを使用して、ネットワーク内の最近の脅威のエンタープライズ ビューを取得し、センサー ユーザーがそれらをどのように処理しているかをよりよく理解します。
ms.date: 12/06/2020
ms.topic: how-to
ms.openlocfilehash: a99c489c54b2671a463c0c3dad1909c74b4fa203
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781468"
---
# <a name="work-with-alerts-on-the-on-premises-management-console"></a>オンプレミス管理コンソールでアラートを操作する 

管理コンソールの **[アラート]** ウィンドウで、次の操作を行うことができます。

- アラート フィルターを使用する

- アラート カウンターを使用する

- アラート情報を表示する

- アラート イベントを管理する

- アラートの除外ルールを作成する

- 外部システムからアラート除外ルールをトリガーする

- アラート グループを使用してインシデント ワークフローを高速化する

## <a name="view-alerts-in-the-on-premises-management-console"></a>オンプレミス管理コンソールにアラートを表示する

オンプレミス管理コンソールでは、接続されているすべてのセンサーからのアラートが集約されます。 これにより、ネットワーク内の最近の脅威のエンタープライズ ビューが提供され、センサー ユーザーがそれらをどのように処理しているかをよりよく理解できます。

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alerts-with-samples.png" alt-text="[アラート] ウィンドウのスクリーンショット。":::

### <a name="work-with-alert-filters"></a>アラート フィルターを使用する

**[アラート]** ウィンドウには、オンプレミス管理コンソールに接続されているセンサーによって生成されたアラートが表示されます。 接続されているセンサーのすべてのアラートを表示することも、特定のものから送信されたアラートを表示することもできます。

- サイト

- ゾーン

- Device

- Sensor

すべてのアラートを表示するには **[フィルターのクリア]** を選択します。

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/clear-filters.png" alt-text="[フィルターのクリア] ボタンを選択してフィルターをクリアします。":::

### <a name="work-with-alert-counters"></a>アラート カウンターを使用する

アラート カウンターでは、アラートが重要度と確認済みの状態別に分類されます。

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/number-of-alerts.png" alt-text="アラート カウンターには、受信したアラートの数が表示されます。":::

アラート カウンターには、次の重要度レベルが表示されます。

- **[高]** :直ちに処理する必要がある悪意のある攻撃を示します。

- **メジャー**:対処することが重要なセキュリティ上の脅威を示します。

- **マイナー**:セキュリティ上の脅威を含む可能性のあるベースライン動作からの逸脱を示します。

- **警告**:セキュリティ上の脅威がないベースライン動作からの逸脱を示します。

重要度レベルは事前定義されています。

カウンターを調整して、確認済みおよび未確認のアラートに基づく数値を提供できます。 未確認アラートは、Defender for IoT センサーでトリガーされましたが、センサーのオペレーターによってまだ確認されていません。

**[Show Acknowledged Alerts]\(確認済みのアラートを表示する\)** オプションが選択されている場合、すべての確認済みアラートが **[アラート]** ウィンドウに表示されます。

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/show-acknowledged-alerts.png" alt-text="確認済みのアラートを表示します。":::

### <a name="view-alert-information"></a>アラート情報を表示する

アラートでは次の情報が提示されます。

- アラート イベントの概要。

- アラートの重要度。

- 検出されたセンサーのアラートへのリンク。

- アラートの UUID。 UUID は、センサーで検出されたアラート イベントに関連付けられているアラート ID (ハイフンで区切られ、その後に一意のシステム ID 番号が続く) で構成されます。

**オンプレミス管理コンソール アラートの UUID**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/unauthorized-internet-connectivity.png" alt-text="デバイスは接続されていますが、承認されていません。":::

**センサー アラート ID**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/internet-connectivity-alert.png" alt-text="センサー アラート ID。":::

UUID を使用すると、オンプレミス管理コンソールに表示される各アラートが検索可能になり、一意の番号で識別できるようになります。 これは、複数のセンサーから生成されたアラートによって同じアラート ID が生成される可能性があるために必要です。

> [!NOTE]
> 既定では、転送ルールが定義されている場合、UUID は次のパートナー システムに表示されます:ArcSight、syslog サーバー、QRadar、Sentinel、および NetWitness。 設定は必要ありません。

アラート情報を表示するには:

- [アラート] リストからアラートを選択します。

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-information.png" alt-text="アラート情報のスクリーンショット。":::

センサーのアラートを表示するには:

- アラートから **[OPEN SENSOR]\(オープン センサー\)** を選択します。

デバイスをゾーン マップに表示するには:

- アラートを受け取ったデバイスとそれに接続されているすべてのデバイスにフォーカスがあるデバイス マップを表示するには、 **[デバイスの表示]** を選択します。

## <a name="manage-alert-events"></a>アラート イベントを管理する

オンプレミス管理コンソールからアラート イベントを管理するために使用できるオプションがいくつかあります。

- アラート イベントの学習または確認。 承認可能なすべてのアラート イベントを学習し、現在確認されていないすべてのアラート イベントを確認するには、 **[Learn & Acknowledge]\(学習と確認\)** を選択します。

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/learn-and-acknowledge.png" alt-text="[Learn & Acknowledge]\(学習と確認\) を選択してすべてを学習します。":::

- アラート イベントをミュートまたはミュート解除します。

アラート イベントの学習、確認、ミュートの詳細については、センサーの「[アラート イベントの管理](how-to-manage-the-alert-event.md)」を参照してください。

## <a name="export-alert-information"></a>アラート情報をエクスポートする

アラート情報を .csv ファイルにエクスポートします。 検出されたすべてのアラートの情報をエクスポートしたり、フィルター表示に基づいて情報をエクスポートしたりできます。次の情報がエクスポートされます。

- ソースの住所
- Destination Address (宛先アドレス)
- アラートのタイトル
- アラートの重大度
- アラート メッセージ
- 関連情報
- 確認済み状態
- PCAP の可用性

エクスポートするには、次のようにします。

1. サイド メニューから、 [アラート] を選択します。
1. [エクスポート] を選択します。
1. 複数のデバイスを対象とするアラートごとに個別の行にアラート情報をエクスポートするには、[拡張アラートのエクスポート] を選択します。 [拡張アラートのエクスポート] を選択すると、.csv ファイルにアラートの重複行が作成され、行ごとに一意の項目が含まれます。 このオプションを使用すると、エクスポートされたアラート イベントを簡単に調査できます。  

## <a name="create-alert-exclusion-rules"></a>アラートの除外ルールを作成する

次に基づくアラート トリガーを無視するよう Defender for IoT に指示します。

- タイムゾーンと期間

- デバイス アドレス (IP、MAC、サブネット)

- アラート名

- 特定のセンサー

アラートをトリガーするアクティビティが Defender for IoT によって無視されるようにする場合は、アラートの除外ルールを作成します。

たとえば、特定のセンサーによって監視されているすべての OT デバイスで 2 日間にわたりメンテナンス手順が実行されることがわかっている場合は、事前に定義された期間中、このセンサーによって検出されるアラートを非表示にするよう Defender for IoT に指示する除外ルールを定義できます。

### <a name="alert-exclusion-logic"></a>アラートの除外ロジック

アラート ルールのロジックは `AND` に基づいています。 つまり、ルールの条件がすべて満たされた場合にのみアラートがトリガーされます。

ルールの条件が定義されていない場合、条件にはすべてのオプションが含まれます。 たとえば、ルールにセンサーの名前を含めない場合は、すべてのセンサーに適用されます。

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-v2.png" alt-text="[Create Exclusion Rule]\(除外ルールの作成\) ビューのスクリーンショット。":::

ルールの概要は、 **[除外ルール]** ウィンドウに表示されます。

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/exclusion-summary-v2.png" alt-text="除外ルールの概要ビューのスクリーンショット。":::

除外ルールを使用するだけでなく、アラートをミュートして非表示にすることもできます。

### <a name="create-exclusion-rules"></a>除外ルールの作成

除外ルールを作成するには:

1. オンプレミス管理コンソールの左ペインで、 **[Alert Exclusion]\(アラートの除外\)** を選択します。 開いたウィンドウの右上隅にある **[追加]** アイコン :::image type="icon" source="media/how-to-work-with-alerts-on-premises-management-console/add-icon.png" border="false"::: を選択して、新しい除外ルールを定義します。 **[Create Exclusion Rule]\(除外ルールの作成\)** ダイアログ ボックスが開きます。

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-view.png" alt-text="ここに情報を入力して、アラートの除外を作成します。":::

2. **[名前]** フィールドにルール名を入力します。 名前に引用付 (`"`) を含めることはできません。

3. **[By Time Zone/Period]\(タイム ゾーン/期間を指定\)** セクションで、特定のタイム ゾーン内の期間を入力します。 この機能は、1 つのタイム ゾーンで特定の期間に対して除外ルールを作成し、他のタイム ゾーンで同時に実装する必要がある場合に使用します。 たとえば、3 つの異なるタイム ゾーンで午前 8 時と午前 10 時の間に除外ルールを適用することが必要になる場合があります。 この場合、同じ期間と関連するタイム ゾーンを使用する 3 つの個別の除外ルールを作成します。

4. **[追加]** を選択します。 除外期間中は、接続されているセンサーにアラートが作成されません。

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/by-the-time-period.png" alt-text="[By Time Period]\(期間を指定\) ビューのスクリーンショット。":::

5. **[By Device Address]\(デバイス アドレスを指定\)** セクションで、次のように定義します。

  - 除外するデバイスの IP アドレス、MAC アドレス、またはサブネット アドレス。

  - 除外されるデバイスのトラフィックの方向 (ソースおよび宛先)。

6. **[追加]** を選択します。

7. **[By Alert Title]\(アラート タイトルを指定\)** セクションで、アラート タイトルの入力を開始します。 ドロップダウン リストから、除外する 1 つまたは複数のアラート タイトルを選択します。

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-title.png" alt-text="[By Alert Title]\(アラート タイトルを指定\) ビューのスクリーンショット。":::

8. **[追加]** を選択します。

9. **[By Sensor Name]\(センサー名を指定\)** セクションで、センサー名の入力を開始します。 ドロップダウン リストから、実行する 1 つまたは複数のセンサーを選択します。

10. **[追加]** を選択します。

11. **[SAVE]\(保存\)** を選択します。 ルールの一覧に新しいルールが表示されます。

アラートをミュートするか、除外ルールを作成して、アラートを非表示にすることができます。 このセクションでは、両方の機能について考えられるユース ケースについて説明します。

- **除外ルール**。 次の場合に除外ルールを作成します。

  - データベースからイベントを除外する必要があることが事前にわかっている。 たとえば、特定のセンサーで検出されたシナリオにより、関連性のないアラートがトリガーされることがわかっている場合などです。 たとえば、特定のサイトで組織の PLC のメンテナンス作業を行い、このサイトの PLC に関連するアラートを非表示にする必要がある場合などです。

  - Defender for IoT で (システム メンテナンス タスクのために) 特定の期間にわたりイベントを無視する。

  - 特定のサブネット内のイベントを無視する。

  - 1 つのルールを使用して複数のセンサーから生成されたアラート イベントを制御する。

  - イベント ログ内で、アラートの除外をイベントとして追跡しない。

- **ミュート**。 次の場合にアラートをミュートします。

  - ミュートする必要がある項目が計画されていない。 どのイベントに関連性がないかが事前にわかっていない。

  - アラートを **[アラート]** ウィンドウから非表示にしたいが、イベント ログでそれを追跡する必要がある。

  - 特定のチャネル上のイベントを無視する。

### <a name="trigger-alert-exclusion-rules-from-external-systems"></a>外部システムからアラート除外ルールをトリガーする

外部システムからアラート除外ルールをトリガーします。 たとえば、企業のチケット発行システムや、ネットワーク メンテナンス プロセスを管理するシステムから除外ルールを管理します。

ルールを適用するセンサー、エンジン、開始時刻、終了時刻を定義します。 詳細については、「[Defender for IoT API センサーおよび管理コンソール API](references-work-with-defender-for-iot-apis.md)」を参照してください。

API を使用して作成したルールは、 **[除外ルール]** ウィンドウに RO として表示されます。

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/edit-exclusion-rule-screen.png" alt-text="[Edit Exclusion Rule]\(除外ルールの編集\) ビューのスクリーンショット。":::

## <a name="see-also"></a>関連項目

[センサー上でのアラートの操作](how-to-work-with-alerts-on-your-sensor.md)
