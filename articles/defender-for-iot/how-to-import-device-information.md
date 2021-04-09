---
title: デバイス情報をインポートする
description: Defender for IoT センサーでは、ミラー化されたトラフィックの監視と分析を行います。 これらの場合は、データをインポートして、検出済みのデバイスに関する情報をエンリッチすることができます。
ms.date: 12/06/2020
ms.topic: how-to
ms.openlocfilehash: eb82a3e5d360913acfead06adc03194d80f7fccb
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785632"
---
# <a name="import-device-information-to-a-sensor"></a>デバイス情報をセンサーにインポートする

Azure Defender for IoT センサーでは、ミラー化されたトラフィックの監視と分析を行います。 場合によっては、組織固有のネットワーク構成ポリシーが原因で、一部の情報が送信されないことがあります。

これらの場合は、データをインポートして、既に検出されているデバイスに関する情報をエンリッチすることができます。 センサーに情報をインポートするために、次の 2 つのオプションを使用できます。

- **[マップからインポートする]** :マップに対して、デバイスの名前、種類、グループ、Purdue レイヤーを更新します。

- **[インポート設定からインポートする]** :デバイスの OS、IP アドレス、パッチ レベル、認可の状態をインポートします。

## <a name="import-from-the-map"></a>マップからインポートする

このセクションでは、デバイスの名前、種類、グループ、Purdue レイヤーをデバイス マップにインポートする方法について説明します。 これはマップから行います。

以下にインポートの要件を示します。

- **[名前]** :最大 30 文字までです。

- **[種類]** または **[Purdue レイヤー]** : **[デバイスのプロパティ]** ダイアログ ボックスに表示されるこれらのオプションを使用します。 (デバイスを右クリックし、 **[プロパティの表示]** を選択します。)

- **[デバイス グループ]** :最大 30 文字の新しいグループを作成します。 

> [!NOTE]
> 競合を回避するため、1 つのセンサーから別のセンサーにエクスポートしたデータをインポートしないでください。

インポートするには:

1. サイド メニューの **[デバイス]** を選択します。

2. **[デバイス]** ウィンドウの右上隅で :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false"::: を選択します。

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="デバイス ウィンドウのスクリーンショット。":::

3. **[デバイスのエクスポート]** を選択します。 エクスポートされたファイルには、広範囲にわたる情報が記載されています。 この情報には、デバイスで使用するプロトコルと、デバイスの認可の状態が含まれます。

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="エクスポートされたファイルの情報。":::

4. CSV ファイル内で、デバイスの名前、種類、グループ、Purdue レイヤーのみを変更します。 そのうえでファイルを保存します。 

   エクスポートされたファイルで示された大文字と小文字の表記を使用します。 たとえば、Purdue レイヤーの場合、最初の文字にはすべて、大文字を使用します。

5. **[デバイス]** ウィンドウの **[インポート/エクスポート]** ドロップダウン メニューから、 **[デバイスのインポート]** を選択します。

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="デバイス ウィンドウを通してデバイスをインポートします。":::

6. **[デバイスのインポート]** を選択し、インポートする CSV ファイルを選択します。 インポートの状態に関するメッセージは、 **[デバイスのインポート]** ダイアログ ボックスが閉じられるまで画面に表示されます。

## <a name="import-from-import-settings"></a>インポート設定からインポートする

このセクションでは、デバイスの IP アドレス、OS、パッチ レベル、認可の状態をデバイス マップにインポートする方法について説明します。 これは **[設定のインポート]** ダイアログ ボックスから行います。

IP アドレス、OS、パッチ レベルをインポートするには:

1. [[ヘルプ センター]](https://cyberx-labs.zendesk.com/hc/en-us) から [devices_info_2.2.8 and up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) ファイルをダウンロードして、次のように情報を入力します。

   - **IP アドレス**:デバイスの IP アドレスを入力します。

   - **オペレーティング システム**:ドロップダウン リストから選択します。

   - **最終更新日**:YYYY-MM-DD 形式を使用します。

     :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="オプション画面。":::

2. サイド メニューで、 **[設定のインポート]** を選択します。

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="設定をインポートします。":::

3. 必要な構成をアップロードするには、 **[デバイス情報]** セクションで **[追加]** を選択し、準備した CSV ファイルをアップロードします。

認可の状態をインポートするには:

1. Defender for IoT ヘルプ センターから、[authorized_devices.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) ファイルをダウンロードして保存します。 このファイルを CSV として保存したことを確認します。

2. 以下のように情報を入力します。

   - **IP アドレス**:デバイスの IP アドレス。

   - **[名前]** : 認可されているデバイスの名前。 名前が正確であることを確認します。 デバイス マップに表示される名前は、インポートされる一覧でデバイスに対して指定されている名前で上書きされます。

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="インポートされるデバイスの一覧が記載されている Excel ファイル。":::

3. サイド メニューで、 **[設定のインポート]** を選択します。

4. **[認可されたデバイス]** セクションで **[追加]** を選択し、保存した CSV ファイルをアップロードします。

情報をインポートすると、この一覧に記載されていないすべてのデバイスについて、認可されていないデバイスに関するアラートが表示されます。

## <a name="import-device-information-to-the-sensor"></a>デバイス情報をセンサーにインポートする

センサーでは、ミラー化されたトラフィックの監視と分析を行います。 場合によっては、組織固有のネットワーク構成ポリシーが原因で、一部の情報が送信されないことがあります。

これらの場合は、データをインポートして、既に検出されているデバイスに関するデバイス情報をエンリッチすることができます。 センサーに情報をインポートするために、次の 2 つのオプションを使用できます。

- **[マップからインポートする]** :マップに対して、デバイスの名前、種類、グループ、Purdue レイヤーを更新します。

- **[インポート設定からインポートする]** :デバイスの OS、IP アドレス、パッチ レベル、認可の状態をインポートします。

### <a name="import-from-the-map"></a>マップからインポートする

このセクションでは、デバイスの名前、種類、グループ、Purdue レイヤーをデバイス マップにインポートする方法について説明します。 これはマップから行います。

以下にインポートの要件を示します。

- **[名前]** :最大 30 文字までです。

- **[種類]** または **[Purdue レイヤー]** : **[デバイスのプロパティ]** ダイアログ ボックスに表示されるこれらのオプションを使用します。 (デバイスを右クリックし、 **[プロパティの表示]** を選択します。)

- **[デバイス グループ]** :最大 30 文字の新しいグループを作成します。 

> [!NOTE]
> 競合を回避するため、1 つのセンサーから別のセンサーにエクスポートしたデータをインポートしないでください。

インポートするには:

1. サイド メニューの **[デバイス]** を選択します。

2. **[デバイス]** ウィンドウの右上隅で :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false"::: を選択します。

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="デバイスを選択するウィンドウ。":::

3. **[デバイスのエクスポート]** を選択します。 エクスポートされたファイルには、広範囲にわたる情報が記載されています。 例には、デバイスで使用するプロトコルと、デバイスの認可の状態が含まれています。

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="エクスポートされたファイルの情報。":::

4. CSV ファイル内で、デバイスの名前、種類、グループ、Purdue レイヤーのみを変更します。 そのうえでファイルを保存します。 

   エクスポートされたファイルで示された大文字と小文字の表記を使用します。 たとえば、Purdue レイヤーの場合、最初の文字にはすべて、大文字を使用します。

5. **[デバイス]** ウィンドウの **[インポート/エクスポート]** ドロップダウン メニューから、 **[デバイスのインポート]** を選択します。

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="デバイスをインポートします。":::

6. **[デバイスのインポート]** を選択し、インポートする CSV ファイルを選択します。 インポートの状態に関するメッセージは、 **[デバイスのインポート]** ダイアログ ボックスが閉じられるまで画面に表示されます。

### <a name="import-from-import-settings"></a>インポート設定からインポートする 

このセクションでは、デバイスの IP アドレス、OS、パッチ レベル、認可の状態をデバイス マップにインポートする方法について説明します。 これは **[設定のインポート]** ダイアログ ボックスから行います。

IP アドレス、OS、パッチ レベルをインポートするには:

1. [[ヘルプ センター]](https://cyberx-labs.zendesk.com/hc/en-us) から [devices_info_2.2.8 and up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) ファイルをダウンロードして、次のように情報を入力します。

   - **IP アドレス**:デバイスの IP アドレス。

   - **オペレーティング システム**:ドロップダウン リストから選択します。

   - **最終更新日**:YYYY-MM-DD 形式を使用します。

    :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="画面の内容。":::

2. サイド メニューで、 **[設定のインポート]** を選択します。

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="[インポートの設定] 画面に入力します。":::

3. 必要な構成をアップロードするには、 **[デバイス情報]** セクションで **[追加]** を選択し、準備した CSV ファイルをアップロードします。

認可の状態をインポートするには:

1. Defender for IoT ヘルプ センターから、[authorized_devices - examples.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) ファイルをダウンロードして保存します。 このファイルを CSV として保存したことを確認します。

2. 以下のように情報を入力します。

   - **IP アドレス**:デバイスの IP アドレス。

   - **[名前]** : 認可されているデバイスの名前。 名前が正確であることを確認します。 デバイス マップに表示される名前は、インポートされる一覧でデバイスに対して指定されている名前で上書きされます。

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="デバイス マップへのインポートの一覧。":::

3. サイド メニューで、 **[設定のインポート]** を選択します。

4. **[認可されたデバイス]** セクションで **[追加]** を選択し、保存した CSV ファイルをアップロードします。

情報をインポートすると、この一覧に記載されていないすべてのデバイスについて、認可されていないデバイスに関するアラートが表示されます。

## <a name="see-also"></a>関連項目

[監視するトラフィックを制御する](how-to-control-what-traffic-is-monitored.md)

[デバイス インベントリのセンサー検出を調査する](how-to-investigate-sensor-detections-in-a-device-inventory.md)
