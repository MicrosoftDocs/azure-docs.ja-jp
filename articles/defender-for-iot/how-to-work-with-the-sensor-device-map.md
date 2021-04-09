---
title: センサー デバイス マップを操作する
description: デバイス マップには、検出されたネットワーク デバイスがグラフィカルに表示されます。 このマップを使用して、デバイス情報とネットワーク スライスの分析や管理、およびレポートの生成を行います。
ms.date: 1/7/2021
ms.topic: how-to
ms.openlocfilehash: f7579cbca618baef404236556993c9831dd84bdf
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784596"
---
# <a name="investigate-sensor-detections-in-the-device-map"></a>デバイス マップのセンサー検出を調査する

デバイス マップには、検出されたネットワーク デバイスがグラフィカルに表示されます。 マップを使用して次のことを実行します。

  - デバイス情報を取得、分析、および管理する。

  - ネットワーク スライス (関心のある特定のグループや Purdue レイヤーなど) を分析する。

  - レポートを生成する (デバイスの詳細や概要のエクスポートなど)。

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="デバイス マップのスクリーンショット。":::

マップにアクセスするには、次のようにします。

  - コンソールのメイン画面から **[デバイス マップ]** を選択します。

## <a name="map-search-and-layout-tools"></a>マップの検索とレイアウトのツール

マップでの作業には、次のツールを使用します。

[デバイス マップ] ウィンドウでどのツールを使用できるかは、ユーザー ロールによって決まります。 ユーザー ロールの詳細については、[ユーザーの作成および管理](how-to-create-and-manage-users.md)に関する記事を参照してください。

| シンボル | 説明 |
|---|---|
| :::image type="icon" source="media/how-to-work-with-maps/search-bar-icon-v2.png" border="false":::| IP アドレスまたは MAC アドレスで特定のデバイスを検索します。 テキスト ボックスに IP アドレスまたは MAC アドレスを入力します。 検索したデバイスが、それに接続されているデバイスと共にマップに表示されます。 |
| グループの強調表示とフィルター <br /> :::image type="content" source="media/how-to-work-with-maps/group-highlight-and-filters-v2.png" alt-text="グループの強調表示とフィルターのスクリーンショット。"::: | 既定とカスタムのデバイス グループに基づいてマップのフィルター処理や強調表示を行います。 |
| :::image type="icon" source="media/how-to-work-with-maps/collapse-view-icon.png" border="false"::: | IT 折りたたみビュー。OT デバイスに対するフォーカス ビューを有効にし、IT デバイスをグループ化します。  |
| :::image type="icon" source="media/how-to-work-with-maps/device-management-icon.png" border="false"::: | マップ内で現在のデバイスの配置を維持します。 たとえば、デバイスをマップ上の新しい位置にドラッグすると、マップを終了するときにそのデバイスはこれらの位置にとどまります。 |
| :::image type="icon" source="media/how-to-work-with-maps/fit-to-screen-icon.png" border="false"::: | 画面に合わせる |
| :::image type="icon" source="media/how-to-work-with-maps/layer-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/layouts-icon-v2.png" border="false"::: | - このデバイスで識別された Purdue レイヤー (自動、プロセス制御、監督、エンタープライズなど) を表示します <br /> - デバイス間の接続を表示します。|
| :::image type="icon" source="media/how-to-work-with-maps/broadcast-icon.png" border="false"::: | ブロードキャストとマルチキャスト間で表示または非表示を実行します。 |
| :::image type="icon" source="media/how-to-work-with-maps/time-icon.png" border="false"::: | マップ上のデバイスを、他のデバイスと最後に通信した時刻に従ってフィルター処理します。 |
| :::image type="icon" source="media/how-to-work-with-maps/notifications-icon.png" alt-text="通知" border="false"::: | デバイスに関する通知を表示します。 たとえば、既存の MAC アドレスを使用しているデバイスに対して新しい IP アドレスが検出された場合など |
| :::image type="icon" source="media/how-to-work-with-maps/export-import.png" alt-text="エクスポート" border="false"::: | デバイス情報をエクスポートまたはインポートします。 |
| :::image type="icon" source="media/how-to-work-with-maps/properties-icon.png" alt-text="properties" border="false"::: | 選択したデバイスの基本的なデバイス プロパティを表示します。 |
| :::image type="icon" source="media/how-to-work-with-maps/zoom-in-icon-v2.png" alt-text="拡大" border="false":::または:::image type="icon" source="media/how-to-work-with-maps/zoom-out-icon-v2.png" alt-text="縮小" border="false"::: | マップ内のデバイスを拡大または縮小します。 |

## <a name="view-ot-elements-only"></a>OT 要素のみを表示する

既定では、IT デバイスは自動的にサブネットごとに集められるので、マップ ビューは OT および ICS ネットワークにフォーカスされます。 IT ネットワーク要素の表示は最小になるように折りたたまれます。これにより、マップ上に表示されるデバイスの総数が減って、OT および ICS ネットワーク要素が見やすくなります。

各サブネットはデバイス マップ上に 1 つのエンティティとして表示され、IT サブネットの詳細を表示したり元に戻したりするための対話式折りたたみおよび展開機能が含まれています。

下の図は、27 個の IT ネットワーク要素を含む折りたたまれた IT サブネットを示しています。

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="27 個の IT ネットワーク要素を含む折りたたまれた IT サブネット":::

IT ネットワークの折りたたみ機能を有効にするには、次のようにします。

- **[システム設定]** ウィンドウで、IT ネットワーク グループ化の切り替え機能が有効になっていることを確認します。

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="[システム設定] ウィンドウ":::

IT サブネットを展開するには、次のようにします。

1. IT と OT の各ネットワークを区別するために、[システム設定] 画面で **[サブネット]** を選択します。

   > [!NOTE]
   > ユーザーが IT と OT の各ネットワークを区別するために、各サブネットには簡単に識別できるようにわかりやすい名前を付けることをお勧めします。

   :::image type="content" source="media/how-to-work-with-maps/subnet-list.png" alt-text="サブネット構成":::

2. **[Edit Subnets Configuration]\(サブネット構成の編集\)** ウィンドウで、IT サブネットとして定義する各サブネットの **[ICS Subnet]\(ICS サブネット\)** チェックボックスをオフにします。 IT サブネットは、IT ネットワーク内のコントローラーや PLC などの ICS デバイスの通知と共に、デバイス マップに折りたたまれて表示されます。

   :::image type="content" source="media/how-to-work-with-maps/edit-config.png" alt-text="サブネット構成の編集":::

3. マップ上で IT ネットワークを展開するには、[デバイス] ウィンドウ内でそれを右クリックし、 **[Expand Network]\(ネットワークを展開\)** を選択します。

   :::image type="content" source="media/how-to-work-with-maps/expand-network.png" alt-text="ネットワークの表示を展開する。":::

4. レイアウト変更はやり直せないことを通知する確認ボックスが表示されます。

5. **[OK]** を選択します。 IT サブネットの要素がマップに表示されます。

   :::image type="content" source="media/how-to-work-with-maps/fixed-map.png" alt-text="[OK]":::

IT サブネットを折りたたむには、次のようにします。

1.  左側のペインで **[デバイス]** を選択します。

2. [デバイス] ウィンドウで、折りたたみアイコンを選択します。 赤の数値は、マップに現在表示されている展開済みの IT サブネットの数を示しています。

   :::image type="content" source="media/how-to-work-with-maps/devices-notifications.png" alt-text="デバイス ウィンドウ":::

3. 折りたたむサブネットを選択するか、 **[Collapse All]\(すべて折りたたむ\)** を選択します。 選択したサブネットがマップ上に折りたたまれて表示されます。

   :::image type="content" source="media/how-to-work-with-maps/close-all-subnets.png" alt-text="すべて折りたたむ":::

折りたたみアイコンが、更新された展開済み IT サブネット数で更新されます。

## <a name="view-or-highlight-device-groups"></a>デバイス グループを表示または強調表示する

デバイス グループに基づいて、マップの表示をカスタマイズできます。 たとえば、特定の OT プロトコル、VLAN、またはサブネットに関連付けられているデバイスのグループなどです。 定義済みグループを使用でき、カスタム グループを作成することもできます。

次の方法でグループを表示します。

  - **強調表示する:** 特定のグループに属するデバイスを青で強調表示します。

  - **フィルター処理する:** 特定のグループに属するデバイスのみを表示します。

:::image type="content" source="media/how-to-work-with-maps/port-standard.png" alt-text="標準ポート ビュー":::

次の定義済みグループを使用できます。

| グループ名 | 説明 |
|--|--|
| **Known applications (既知のアプリケーション)** | TCP などの予約済みポートを使用するデバイス。  |
| **non-standard ports (標準以外のポート) (既定)** | 別名が割り当てられていない標準以外のポートを使用するデバイス。 |
| **OT protocols (OT プロトコル) (既定)** | 既知の OT トラフィックを処理するデバイス。 |
| **Authorization (認可) (既定)** | 学習プロセス中にネットワークで検出された、またはネットワーク上で正式に認可されたデバイス。 |
| **Device inventory filters (デバイス インベントリ フィルター)** | デバイス インベントリ テーブルに保存されたフィルターに従ってグループ化されたデバイス。 |
| **Polling intervals (ポーリング間隔)** | ポーリング間隔によってグループ化されたデバイス。 ポーリング間隔は、循環チャネルまたは期間に従って自動的に生成されます。 たとえば、15.0 秒、3.0 秒、1.5 秒、または任意の間隔。 この情報を確認すると、システムのポーリングが早すぎるか遅すぎるかを知ることができます。 |
| **プログラミング** | エンジニアリング ステーションとプログラミング マシン。 |
| **サブネット** | 特定のサブネットに属するデバイス。 |
| **VLAN** | 特定の VLAN ID に関連付けられているデバイス。 |
| **Cross subnet connections (サブネット間の接続)** | あるサブネットから別のサブネットへ通信するデバイス。 |
| **Pinned alerts (固定されたアラート)** | ユーザーがアラートを固定しているデバイス。 |
| **Attack vector simulations (攻撃ベクトルのシミュレーション)** | 攻撃ベクトルのレポートで検出された脆弱なデバイス。 これらのデバイスをマップ上に表示するには、攻撃ベクトルを生成するときに **[Display on Device Map]\(デバイス マップに表示する\)** チェックボックスを選択します。 :::image type="content" source="media/how-to-work-with-maps/add-attack-v2.png" alt-text="攻撃ベクトルのシミュレーションの追加"::: |
| **最終表示日** | 最後に表示された時間枠でグループ化されたデバイス。たとえば、1 時間、6 時間、1 日、7 日など。 |
| **Not In Active Directory (Active Directory にない)** | Active Directory と通信していないすべての PLC 以外のデバイス。 |

デバイスを強調表示またはフィルター処理するには、次のようにします。

1. サイド メニューから **[デバイス マップ]** を選択します。

2. フィルター アイコンを選択します。 :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="Menu":::

3. [グループ] ペインで、デバイスを強調表示またはフィルター処理するグループを選択します。

4. **[強調表示]** または **[Filter]\(フィルター\)** を選択します。 同じ選択内容を切り替えると、強調表示またはフィルターが解除されます。

## <a name="define-custom-groups"></a>カスタム グループを定義する

定義済みのグループを表示するだけでなく、カスタム グループを定義することもできます。 これらのグループは、デバイス マップ、デバイス インベントリ、およびデータ マイニング レポートに表示されます。

> [!NOTE]
> また、デバイス インベントリからグループを作成することもできます。

グループを作成するには:

1. サイド メニューから **[デバイス]** を選択します。 デバイス マップが表示されます。

1. :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="[グループ設定]"::: を選択して、グループの設定を表示します。

1. 新しいカスタム グループを作成するには、:::image type="content" source="media/how-to-work-with-maps/create-group-v2.png" alt-text="[グループ]"::: を選択します。

:::image type="content" source="media/how-to-work-with-maps/custom-group-v2.png" alt-text="カスタム グループの作成画面":::

1. グループの名前を追加します。最大 30 文字を使用します。

1. 次のようにして、関連するデバイスを選択します。

   - 一覧から選択 (矢印ボタンで選択) して、このメニューからデバイスを追加します。<br /> または、 <br /> 
   - 選択したグループからコピー (矢印ボタンで選択) して、このメニューからデバイスを追加します。

1. **[グループの追加]** を選択して、既存のグループをカスタム グループに追加します。

### <a name="add-devices-to-a-custom-group"></a>カスタム グループにデバイスを追加する

カスタム グループにデバイスを追加したり、新しいカスタム グループとデバイスを作成したりすることができます。

1. マップ上のデバイスを右クリックします。

1. **[Add to Group]\(グループに追加\)** を選択します。

1. [グループ] フィールドにグループ名を入力し、[+] を選択します。 新しいグループが表示されます。 グループが既に存在する場合は、既存のカスタム グループに追加されます。

   :::image type="content" source="media/how-to-work-with-maps/groups-section-v2.png" alt-text="グループ名":::

1. 手順 1 から 3 を繰り返して、デバイスをグループに追加します。

## <a name="map-zoom-views"></a>マップの拡大ビュー

マップ ビューを操作すると、大規模ネットワークを分析する際のフォレンジクスを迅速に処理することができます。

次の 3 つのデバイス詳細ビューを表示できます。

  - [概観ビュー](#birds-eye-view)

  - [デバイスの種類と接続ビュー](#device-type-and-connection-view)

  - [詳細ビュー](#detailed-view)

### <a name="birds-eye-view"></a>概観ビュー

このビューには、次のように表されるデバイスの概要が表示されます。

  - 赤のドットは、アラートがあるデバイスを示しています

  - 星型のドットは、重要としてマークされているデバイスを示しています

  - 黒のドットは、アラートがないデバイスを示しています

:::image type="content" source="media/how-to-work-with-maps/colored-dots-v2.png" alt-text="概観ビュー":::

### <a name="device-type-and-connection-view"></a>デバイスの種類と接続ビュー 

このビューでは、アラート、デバイスの種類、および接続されているデバイスでデバイスを強調表示するために、デバイスはマップ上にアイコンとして表示されます。

  - アラートがあるデバイスは赤いリングで表示されます

  - アラートがないデバイスはグレーのリングで表示されます

  - 星で表示されているデバイスは重要としてマークされました

デバイスの種類のアイコンは、接続されているデバイスと共に表示されます。

:::image type="content" source="media/how-to-work-with-maps/colored-rings.png" alt-text="接続ビュー":::

### <a name="detailed-view"></a>詳細ビュー 

詳細ビューには、デバイスとデバイスのラベルとインジケーターが次の情報と共に表示されます。

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="詳細ビュー":::

### <a name="control-the-zoom-view"></a>ズーム ビューを制御する

表示されるマップ ビューは、マップのズームレベルによって異なります。 マップ ビューの切り替えは、ズームレベルを変更することによって行われます。

:::image type="content" source="media/how-to-work-with-maps/zoom-in-out.png" alt-text="ズーム ビューを制御する":::

### <a name="enable-simplified-zoom-views"></a>簡略化されたズーム ビューを有効にする

セキュリティ アナリストや RO ユーザーが概観およびデバイスと種類の接続ビューにアクセスできるようにする場合、管理者は、簡略化されたビュー オプションを有効にする必要があります。

簡略化されたマップ　ビューを有効にするには、次のようにします。

  - **[システム設定]** を選択し、 **[Simplified Map View]\(簡略化されたマップ ビュー\)** オプションを切り替えます。

:::image type="content" source="media/how-to-work-with-maps/simplify-view-v2.png" alt-text="マップ ビューを簡略化する":::

## <a name="learn-more-about-devices"></a>デバイスの詳細情報

デバイスマップを構成するデバイスの詳細を確認するために、さまざまなツールを使用できます。

- [デバイスのラベルとインジケーター](#device-labels-and-indicators)

- [デバイスのクイック ビュー](#device-quick-views)

- [デバイスのプロパティを表示および管理する](#view-and-manage-device-properties)

- [デバイスの種類を表示する](#view-device-types)

- [バックプレーン](#backplane-properties)

- [デバイスのイベントのタイムラインを表示する](#view-a-timeline-of-events-for-the-device)

- [プログラミングの詳細と変更を分析する](#analyze-programming-details-and-changes)

### <a name="device-labels-and-indicators"></a>デバイスのラベルとインジケーター

マップ上のデバイスに、次のラベルとインジケーターが表示される場合があります。

| デバイスのラベル | 説明 |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/host-v2.png" alt-text="IP ホスト名"::: | IP アドレスのホスト名と IP アドレス、またはサブネット アドレス |
| :::image type="content" source="media/how-to-work-with-maps/amount-alerts-v2.png" alt-text="アラートの数"::: | デバイスに関連付けられているアラートの数 |
| :::image type="icon" source="media/how-to-work-with-maps/type-v2.png" border="false"::: | デバイスの種類のアイコン (ストレージ、PLC、ヒストリアンなど)。 |
| :::image type="content" source="media/how-to-work-with-maps/grouped-v2.png" alt-text="グループ化されているデバイス"::: | IT ネットワークのサブネット内でグループ化されているデバイスの数。 この例では、8 です。 |
| :::image type="content" source="media/how-to-work-with-maps/not-authorized-v2.png" alt-text="デバイス学習期間"::: | 学習期間後に検出され、ネットワーク デバイスとして認可されなかったデバイス。 |
| 実線 | デバイス間の論理接続 |
| :::image type="content" source="media/how-to-work-with-maps/new-v2.png" alt-text="新しいデバイス"::: | 学習完了後に検出された新しいデバイス。 |

### <a name="device-quick-views"></a>デバイスのクイック ビュー

マップからデバイスのプロパティと接続にアクセスします。

クイック プロパティ メニューを開くには、次のようにします。

  - クイック プロパティ メニュー :::image type="content" source="media/how-to-work-with-maps/properties.png" alt-text="クイック プロパティ メニュー"::: を選択します。

#### <a name="quick-device-properties"></a>クイック デバイス プロパティ

クイック プロパティ画面が開いている間に 1 つのデバイスまたは複数のデバイスを選択して、それらのデバイスの強調表示を確認します。

:::image type="content" source="media/how-to-work-with-maps/device-information.png" alt-text="クイック デバイス プロパティ":::

#### <a name="quick-connection-properties"></a>クイック接続プロパティ

クイック プロパティ画面が開いている間に接続を選択すると、この接続で使用されているプロトコルと、それらが最後に確認されたのがいつかが表示されます。

:::image type="content" source="media/how-to-work-with-maps/connection-details-v2.png" alt-text="クイック接続プロパティ":::

## <a name="view-and-manage-device-properties"></a>デバイスのプロパティを表示および管理する

マップに表示されている各デバイスのデバイス プロパティを表示できます。 たとえば、デバイスの名前、種類、OS、またはファームウェアやベンダーなど。

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="デバイスのプロパティを表示および管理する":::

次の情報は手動で更新できます。 Defender for IoT によって検出された情報は、手動で入力された情報で上書きされます。

  - 名前

  - Type

  - OS

  - Purdue レイヤー

  - 説明

| Item | 説明 |
|--|--|
| 基本情報 | 必要な基本情報。 |
| 名前 | デバイス名。 <br /> 既定では、ネットワークに定義されているデバイス名がセンサーによって検出されます。 たとえば、DNS サーバーに定義されている名前などです。 <br /> このような名前が定義されていない場合、このフィールドにはデバイスの IP アドレスが表示されます。 <br /> デバイス名は手動で変更できます。 デバイスには、その機能を反映したわかりやすい名前を付けてください。 |
| Type | センサーによって検出されたデバイスの種類。 <br /> 詳細については、「[デバイスの種類を表示する](#view-device-types)」を参照してください。 |
| ベンダー | デバイスのベンダー。 これは、デバイスの MAC アドレスの先頭文字によって特定されます。 このフィールドは読み取り専用です。 |
| オペレーティング システム | センサーによって検出されたデバイスの OS。 |
| Purdue レイヤー | センサーによって識別されたこのデバイスの Purdue レイヤー。次のものが含まれます。 <br /> - 自動 <br /> - プロセス制御 <br /> - 監督 <br /> - Enterprise |
| 説明 | フリー テキスト フィールド。 <br /> デバイスに関する情報を追加します。 |
| 属性 | 学習期間中に検出されたデバイスに関する追加情報で、他のカテゴリに属していないものは、[属性] セクションに表示されます。 <br /> この情報は RO です。 |
| 設定 | 擬陽性を防止するためにデバイス設定を手動で変更できます。 <br /> - **Authorized Device (認可済みデバイス)** : 学習期間中は、ネットワーク内で検出されたすべてのデバイスは認可済みデバイスとして識別されます。 学習期間後にデバイスが検出された場合は、既定で、未認可デバイスとして表示されます。 この定義は手動で変更できます。 <br /> - **Known as Scanner (スキャナーとして認識済み)** : このデバイスがスキャナーとして認識されていることがわかっていて、それについて警告する必要がない場合は、このオプションを有効にします。 <br /> - **Programming Device (プログラミング デバイス)** : このデバイスがプログラミング デバイスとして認識されており、プログラミング変更を行うために使用されていることがわかっている場合は、このオプションを有効にします。 プログラミング デバイスとして識別すると、この資産から生じたプログラミング変更に関するアラートが防止されます。 |
| カスタム グループ | このデバイスが参加しているデバイス マップ内のカスタム グループ。 |
| State | デバイスのセキュリティと認可の状態: <br /> - アラートがない場合、状態は `Secured` です <br /> - デバイスに関するアラートがある場合は、アラートの数が表示されます <br /> - 学習期間後にネットワークに追加されたデバイスについては、状態 `Unauthorized` が表示されます。 設定でデバイスを `Authorized Device` として手動で定義できます。 <br /> - このデバイスのアドレスが動的アドレスとして定義されている場合は、`DHCP` が状態に追加されます。 |


| ネットワーク | 説明 |
|--|--|
| インターフェイス | デバイスのインターフェイス。 RO フィールド。 |
| プロトコル | デバイスによって使用されるプロトコル。 RO フィールド。 |
| ファームウェア | バックプレーン情報が使用可能な場合、ファームウェア情報は表示されません。 |
| Address | デバイスの IP アドレス。 |
| シリアル | デバイスのシリアル番号。 |
| モジュール アドレス | デバイス モデルとスロットの番号または ID。 |
| モデル | デバイス モデルの番号。 |
| Firmware Version | ファームウェアのバージョン番号。 |

デバイス情報を表示するには、次のようにします。

1. サイド メニューから **[デバイス]** を選択します。

2. デバイスを右クリックし、 **[View Properties]\(プロパティの表示\)** を選択します。 [Device Properties]\(デバイス プロパティ\) ウィンドウが表示されます。

3. このウィンドウの下部にある必要なアラートを選択すると、このデバイスのアラートに関する詳細情報が表示されます。

### <a name="view-device-types"></a>デバイスの種類を表示する

デバイスの種類は、デバイスの検出プロセス中にセンサーによって自動的に識別されます。 種類は手動で変更できます。

:::image type="content" source="media/how-to-work-with-maps/type-of-device.png" alt-text="デバイスの種類を表示する":::

次の表に、システム内のすべての種類を示します。

| カテゴリ | デバイスの種類 |
|--|--|
| ICS | エンジニアリング ステーション <br /> PLC <br />ヒストリアン <br />HMI <br />IED <br />DCS コントローラー <br />RTU <br />産業用パッケージ システム <br />産業用スケール <br />産業用ロボット <br />スロット <br />測定 <br />可変周波数ドライブ  <br />ロボット コントローラー <br />サーボ ドライブ <br />空気圧デバイス <br />Marquee |
| IT | ドメイン コントローラー <br />DB サーバー <br />ワークステーション <br />サーバー <br />ターミナル ステーション <br />ストレージ <br />スマート フォン <br />Tablet <br />バックアップ サーバー |
| IoT | IP カメラ <br />プリンター  <br />タイム レコーダー <br />ATM <br />スマート TV <br />ゲーム コンソール <br />DVR <br />ドア コントロール パネル <br />HVAC <br />Thermostat <br />火災報知器 <br />スマート ライト <br />スマート スイッチ <br />火災検知器 <br />IP 電話 <br />警報システム <br />警報サイレン <br />Motion Detector <br />エレベーター <br />湿度センサー <br />バーコード スキャナー <br />無停電電源装置 <br />人数カウンターシステム <br />Intercom <br />ターンスタイル |
| ネットワーク | ワイヤレス アクセス ポイント <br />ルーター <br />Switch <br />ファイアウォール <br />VPN Gateway <br />NTP サーバー <br />Wifi Pineapple <br />物理的な場所 <br />I/O アダプター <br /> プロトコル コンバーター |

デバイス情報を表示するには、次のようにします。

1.  サイド メニューから **[デバイス]** を選択します。

2. デバイスを右クリックし、 **[View Properties]\(プロパティの表示\)** を選択します。 [Device Properties]\(デバイス プロパティ\) ウィンドウが表示されます。

3. 必要なアラートを選択すると、このデバイスのアラートに関する詳細情報が表示されます。

### <a name="backplane-properties"></a>バックプレーンのプロパティ

PLC に、複数のラックとスロットに分けられた複数のモジュールが含まれている場合は、モジュール カードによって特性が異なる可能性があります。 たとえば、IP アドレスと MAC アドレスが同じである場合、ファームウェアが異なる可能性があります。

バックプレーン オプションを使用すると、複数のコントローラーまたはカードとその入れ子になったデバイスを、さまざまな定義を含む 1 つのエンティティとして確認できます。 バックプレーン ビューの各スロットは、基になるデバイス (その背後で検出されたデバイス) を表しています。

:::image type="content" source="media/how-to-work-with-maps/backplane-image-v2.png" alt-text="バックプレーンのプロパティ":::

:::image type="content" source="media/how-to-work-with-maps/backplane-details-v2.png" alt-text="バックプレーン デバイス プロパティ":::

1 つのバックプレーンには、最大 30 枚のコントローラー カードと最大 30 個のラック ユニットを含めることができます。 複数のレベルに含まれているデバイスの合計数は、最大 200 デバイスまでです。

バックプレーンの詳細が検出されると、[Device Properties]\(デバイス プロパティ\) ウィンドウに [バックプレーン] ペインが表示されます。

各スロットは、基になるデバイスの数と、モジュールの種類を示すアイコンと共に表示されます。

| アイコン | モジュールの型 |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/power.png" alt-text="電源"::: | 電源 |
| :::image type="content" source="media/how-to-work-with-maps/analog.png" alt-text="アナログ I/O"::: | アナログ I/O |
| :::image type="content" source="media/how-to-work-with-maps/comms.png" alt-text="通信アダプター"::: | 通信アダプター |
| :::image type="content" source="media/how-to-work-with-maps/digital.png" alt-text="デジタル I/O"::: | デジタル I/O |
| :::image type="content" source="media/how-to-work-with-maps/computer-processor.png" alt-text="CPU"::: | CPU |
| :::image type="content" source="media/how-to-work-with-maps/HMI-icon.png" alt-text="HMI"::: | HMI |
| :::image type="content" source="media/how-to-work-with-maps/average.png" alt-text="全般"::: | ジェネリック |

スロットを選択すると、スロットの詳細が表示されます。

:::image type="content" source="media/how-to-work-with-maps/slot-selection-v2.png" alt-text="スロットを選択する":::

スロットの背後にある基になるデバイスを表示するには、 **[VIEW ON MAP]\(マップに表示\)** を選択します。 スロットは、すべての基になるモジュールおよび接続されているデバイスと共にデバイス マップに表示されます。

:::image type="content" source="media/how-to-work-with-maps/map-appearance-v2.png" alt-text="マップに表示":::

## <a name="view-a-timeline-of-events-for-the-device"></a>デバイスのイベントのタイムラインを表示する

デバイスに関連付けられているイベントのタイムラインを表示します。

タイムラインを表示するには、次のようにします。

1. マップからデバイスを右クリックします。

2. **[Show Events]\(イベントの表示\)** を選択します。 [Event Timeline]\(イベントのタイムライン\) ウィンドウが開き、選択したデバイスで検出されたイベントに関する情報が表示されます。

詳細については、「[イベント タイムライン](#event-timeline)」を参照してください。

## <a name="analyze-programming-details-and-changes"></a>プログラミングの詳細と変更を分析する

ネットワーク デバイスで実行されたプログラミング イベントを表示し、コード変更を分析することによってフォレンジクスを強化します。 この情報は、次のような疑わしいプログラミング アクティビティを検出するのに役立ちます。

  - 人的エラー: エンジニアが間違ったデバイスをプログラミングしている。

  - 破損したプログラミング オートメーション: オートメーション エラーが原因で、プログラミングが誤って実行されている。

  - ハッキングされたシステム: 認可されていないユーザーがプログラミング デバイスにログインした。

プログラミングされたデバイスを表示し、それに対して他のデバイスによって実行されたさまざまなプログラミング変更をスクロールできます。

プログラミング デバイスによって追加、変更、削除、または再読み込みされたコードを表示します。 関心のあるファイルの種類、日付、または時間に基づいて、プログラミング変更を検索します。

### <a name="when-to-review-programming-activity"></a>プログラミング アクティビティを確認するタイミング 

プログラミング アクティビティの確認が必要となるのは、次のような場合です。

  - 認可されていないプログラミングに関するアラートが表示された後

  - コントローラーに対する予定された更新の後

  - プロセスまたはマシンが正常に動作していないとき (最後の更新がいつ誰によって行われたかを確認するため)

:::image type="content" source="media/how-to-work-with-maps/differences.png" alt-text="プログラミングの変更ログ":::

その他のオプションを使用すると、次のことを実行できます。

  - 関心のあるイベントを星印でマークする。

  - 現在のコードで *.txt ファイルをダウンロードする。

### <a name="about-authorized-vs-unauthorized-programming-events"></a>認可済みと未認可のプログラミング イベントについて 

未認可のプログラミング イベントは、プログラミング デバイスとして学習されていないデバイス、または手動で定義されていないデバイスによって実行されます。 認可済みのプログラミング イベントは、プログラミング デバイスとして解決された、または手動で定義されたデバイスによって実行されます。

[Programming Analysis]\(プログラミングの分析\) ウィンドウには、認可済みと未認可の両方のプログラミング イベントが表示されます。

### <a name="accessing-programming-details-and-changes"></a>プログラミングの詳細と変更へのアクセス

[Programming Analysis]\(プログラミングの分析\) ウィンドウには、次からアクセスします。

- [イベント タイムライン](#event-timeline)

- [未認可プログラミングの警告](#unauthorized-programming-alerts)

### <a name="event-timeline"></a>イベント タイムライン

イベント タイムラインを使用して、プログラミング変更が検出されたイベントのタイムラインを表示します。

:::image type="content" source="media/how-to-work-with-maps/timeline.png" alt-text="イベント タイムラインのビュー。":::

### <a name="unauthorized-programming-alerts"></a>未認可プログラミングの警告

未認可のプログラミング デバイスがプログラミング アクティビティを実行すると、アラートがトリガーされます。

:::image type="content" source="media/how-to-work-with-maps/unauthorized.png" alt-text="未認可プログラミングの警告":::

> [!NOTE]
> [Device Properties]\(デバイス プロパティ\) ウィンドウと [Device Inventory]\(デバイス インベントリ\) で基本的なプログラミング情報を表示することもできます。

### <a name="working-in-the-programming-timeline-window"></a>プログラミングのタイムライン ウィンドウでの作業

このセクションでは、プログラミング ファイルを表示し、バージョンを比較する方法について説明します。 プログラミングされたデバイスに送信された特定のファイルを検索します。 次に基づいてファイルを検索します。

  - Date

  - ファイルの種類

:::image type="content" source="media/how-to-work-with-maps/timeline-view.png" alt-text="プログラミングのタイムライン ウィンドウ":::

|プログラミングのタイムラインの種類 | 説明 |
|--|--|
| プログラミングされたデバイス | ホスト名とファイルなど、プログラミングされたデバイスに関する詳細情報を提供します。 |
| 最近のイベント | センサーによって検出された 50 個の最新イベントが表示されます。 <br />イベントを強調表示するには、その上にマウス ポインターを移動し、星をクリックします。 :::image type="icon" source="media/how-to-work-with-maps/star.png" border="false"::: <br /> 最新の 50 個のイベントを表示できます。 |
| ファイル | プログラミングされたデバイスで、選択された日付とファイル サイズで検出されたファイルを表示します。 <br /> 既定では、デバイスごとに表示できるファイルの最大数は 300 です。 <br /> 既定では、各ファイルの最大ファイル サイズは 15 MB です。 |
| ファイルの状態 :::image type="icon" source="media/how-to-work-with-maps/status-v2.png" border="false"::: | ファイル ラベルは、デバイス上のファイルの状態を示します。これには次のものが含まれます。 <br /> **追加済み**: 選択された日付または時刻にエンドポイントにファイルが追加された。 <br /> **更新済み**: 選択した日付または時刻にファイルが更新された。 <br /> **削除済み**:ファイルが削除された。 <br /> **ラベルなし**: ファイルは変更されなかった。   |
| プログラミング デバイス | プログラミング変更が行われたデバイス。 プログラムされた 1 つのデバイスに対して複数のデバイスによってプログラミング変更が実行されている可能性があります。 変更のホスト名、日付、または時刻とログイン ユーザーが表示されます。 |
| :::image type="icon" source="media/how-to-work-with-maps/current.png" border="false"::: | プログラミングされたデバイスにインストールされている現在のファイルが表示されます。 |
| :::image type="icon" source="media/how-to-work-with-maps/download-text.png" border="false"::: | 表示されているコードのテキスト ファイルをダウンロードします。 |
| :::image type="icon" source="media/how-to-work-with-maps/compare.png" border="false"::: | 現在のファイルと、選択した日付で検出されたファイルを比較します。 |

### <a name="choose-a-file-to-review"></a>確認するファイルを選択する

このセクションでは、確認するファイルを選択する方法について説明します。

確認するファイルを選択するには、次のようにします。

1. **[Recent Events]\(最近のイベント\)** ペインからイベントを選択します。

2. [ファイル] ペインからファイルを選択します。 [Current]\(現在\) ペインにファイルが表示されます。

:::image type="content" source="media/how-to-work-with-maps/choose-file.png" alt-text="操作するファイルを選択する。":::

### <a name="compare-files"></a>ファイルの比較

このセクションでは、プログラミング ファイルを比較する方法について説明します。

比較するには、次のようにします。

1. [Recent Events]\(最近のイベント\) ペインからイベントを選択します。

2. [ファイル] ペインからファイルを選択します。 [Current]\(現在\) ペインにファイルが表示されます。 このファイルを他のファイルと比較することができます。

3. 比較インジケーターを選択します。

   :::image type="content" source="media/how-to-work-with-maps/compare.png" alt-text="比較インジケーター":::

   このウィンドウには、選択したファイルが、プログラミングされたデバイスで検出されたすべての日付が表示されます。 ファイルは、プログラミングされたデバイス上で、複数のプログラミング デバイスによって更新されている可能性があります。

   検出された相違点の数がウィンドウの右上隅に表示されます。 相違点を表示するには、下にスクロールする必要がある場合があります。

   :::image type="content" source="media/how-to-work-with-maps/scroll.png" alt-text="選択対象まで下にスクロールする":::

   数値は、変更されたテキストの隣接する行によって計算されます。 たとえば、8 つの連続したコード行が変更 (削除、更新、または追加) された場合、これは 1 つの相違点として計算されます。

   :::image type="content" source="media/how-to-work-with-maps/program-timeline.png" alt-text="プログラミングのタイムライン ビュー。":::

4. 日付を選択します。 選択した日付で検出されたファイルがウィンドウに表示されます。

5. [Recent Events]\(最近のイベント\) または [Files]\(ファイル\) ペインから選択されたファイルは、常に右側に表示されます。

### <a name="device-programming-information-other-locations"></a>デバイスのプログラミング情報:その他の場所

プログラミングのタイムラインで詳細を確認するだけでなく、[デバイス プロパティ] ウィンドウやデバイス インベントリでプログラミング情報にアクセスすることもできます。

| デバイスの種類 | 説明 |
|--|--|
| デバイスのプロパティ | デバイスのプロパティ ウィンドウには、デバイスで検出された最後のプログラミング イベントに関する情報が表示されます。 :::image type="content" source="media/how-to-work-with-maps/information-from-device-v2.png" alt-text="デバイスのプロパティ"::: |
| デバイス インベントリ | デバイス インベントリには、デバイスがプログラミング デバイスであるかどうかが示されます。 :::image type="content" source="media/how-to-work-with-maps/inventory-v2.png" alt-text="デバイスのインベントリ"::: |

## <a name="manage-device-information-from-the-map"></a>マップからデバイス情報を管理する

センサーは、ネットワーク上のデバイスに直接更新を行ったり影響を与えたりしません。 ここで行われた変更は、デバイスの分析方法にのみ影響します。

### <a name="delete-devices"></a>デバイスの削除

学習された情報に関連性がない場合は、デバイスを削除できます。 たとえば、オブジェクトに適用された

  - 提携企業の契約社員が、エンジニアリング ワークステーションで構成の更新を実行するために一時的に接続します。 タスクが完了すると、デバイスは削除されます。

  - ネットワークの変更により、一部のデバイスはもう接続されていません。

デバイスを削除しないと、センサーによってその監視が続行されます。 60 日後、通知が表示されて、削除が推奨されます。

別のデバイスがアクセスしようとすると、デバイスが応答していないことを示すアラートを受け取る可能性があります。 この場合は、ネットワークが正しく構成されていない可能性があります。

デバイスは、デバイス マップ、デバイス インベントリ、およびデータ マイニング レポートから削除されます。 その他の情報 (たとえば、ウィジェットに格納されている情報) は保持されます。

デバイスを削除するには、それが少なくとも 10 分間非アクティブになっている必要があります。

デバイス マップからデバイスを削除するには、次のようにします。

1. サイド メニューから **[デバイス]** を選択します。

2. デバイスを右クリックし、 **[削除]** を選択します。

### <a name="merge-devices"></a>デバイスをマージする

特定の状況で、デバイスをマージする必要がある場合があります。 これは、1 つの一意のデバイスに関連付けられている別々のネットワーク エンティティがセンサーによって検出された場合に必要になることがあります。 たとえば、オブジェクトに適用された

  - 4 枚のネットワーク カードがある PLC。

  - WIFI と物理カードがあるラップトップ。
  
  - 2 枚以上のネットワーク カードがあるワークステーション。

マージするときに、2 つのデバイスのデバイス プロパティを 1 つに結合するようセンサーに指示します。 これを行うと、[Device Properties]\(デバイスのプロパティ\) ウィンドウとセンサー レポートが新しいデバイス プロパティの詳細で更新されます。

たとえば、それぞれ IP アドレスがある 2 つのデバイスをマージすると、両方の IP アドレスが [Device Properties]\(デバイスのプロパティ\) ウィンドウに別々のインターフェイスとして表示されます。 マージできるのは、認可済みのデバイスのみです。

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="[Device Properties]\(デバイス プロパティ\) ウィンドウ":::

イベントのタイムラインには、マージ イベントが表示されます。

:::image type="content" source="media/how-to-work-with-maps/events-time.png" alt-text="マージされたイベントが含まれたイベントのタイムライン。":::

デバイスのマージを元に戻すことはできません。 2 つのデバイスを誤ってマージした場合は、そのデバイスを削除し、センサーによって両方が再検出されるのを待ちます。

デバイスをマージするには、次のようにします。

1. 2 つのデバイスを選択 (Shift キーを押しながらクリック) し、そのいずれかを右クリックします。

2. **[Merge]\(マージ\)** を選択してデバイスをマージします。 マージが完了するまでには最大で 2 分かかる場合があります。

3. [Set merged device attributes]\(マージされたデバイス属性の設定\) ダイアログ ボックスで、デバイス名を選択します。

   :::image type="content" source="media/how-to-work-with-maps/name-the-device-v2.png" alt-text="属性ダイアログ ボックス":::

4. **[保存]** を選択します。

### <a name="authorize-and-unauthorize-devices"></a>デバイスの認可と認可解除

学習期間中は、ネットワーク内で検出されたすべてのデバイスは認可済みデバイスとして識別されます。 **[Authorized]\(認可済み\)** ラベルは、デバイス マップ内のこれらのデバイスには表示されません。

学習期間後にデバイスが検出された場合は、未認可デバイスとして表示されます。 未認可デバイスはマップに表示されるだけでなく、デバイス インベントリにも表示できます。

:::image type="content" source="media/how-to-work-with-maps/inventory-icon.png" alt-text="デバイス インベントリ":::

**新しいデバイスと未認可**

学習期間後に検出された新しいデバイスは、`New` および `Unauthorized` ラベルと共に表示されます。

マップ上でデバイスを移動したり、デバイスのプロパティを手動で変更したりすると、`New` ラベルがデバイス アイコンから削除されます。

#### <a name="unauthorized-devices---attack-vectors-and-risk-assessment-reports"></a>未認可デバイス - 攻撃ベクトルとリスク評価レポート

未認可デバイスは、リスク評価レポートと攻撃ベクトル レポートに含まれます。

- **攻撃ベクトル レポート:** 未認可としてマークされているデバイスは、攻撃ベクトルで、ネットワークに対する脅威となる可能性がある非認可の疑いのあるデバイスとして解決されます。

   :::image type="content" source="media/how-to-work-with-maps/attack-vector-reports.png" alt-text="攻撃ベクトル レポートを表示する":::

- **リスク評価レポート:** 未認可としてマークされているデバイスは次のとおりです。

  - リスク評価レポートで識別されます

デバイスを手動で認可または認可解除するには、次のようにします。

1. マップ上のデバイスを右クリックし、 **[Unauthorize]\(認可解除\)** を選択します。

### <a name="mark-devices-as-important"></a>デバイスを重要としてマークする

重要なネットワーク デバイスを重要としてマークできます。たとえば、ビジネスに不可欠なサーバーなど。 これらのデバイスは、マップ上で星印を使ってマークされます。 星は、マップのズーム レベルによって異なります。

:::image type="icon" source="media/how-to-work-with-maps/star-one.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-two.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-3.png" border="false":::

### <a name="important-devices---attack-vectors-and-risk-assessment-reports"></a>重要なデバイス - 攻撃ベクトルとリスク評価レポート

重要なデバイスは、リスク評価レポートと攻撃ベクトル レポートを生成するときに計算されます。

  - 重要とマークされている攻撃ベクトル レポートのデバイスは、攻撃ターゲットとして攻撃ベクトルで解決されます。 

  - リスク評価レポート: 重要とマークされているデバイスは、リスク評価レポートでセキュリティ スコアを提供するときに計算されます。

## <a name="generate-activity-reports-from-the-map"></a>マップからアクティビティ レポートを生成する

選択したデバイスについて 1 時間、6 時間、12 時間、または 24 時間にわたるアクティビティ レポートを生成します。 利用可能な情報は以下のとおりです。

  - カテゴリ:トラフィック シナリオに基づいた基本的な検出情報。

  - ソースと宛先のデバイス

  - データ: 検出された追加情報。

  - 最後に確認された日時。

レポートは、Microsoft Excel または Word のファイルとして保存できます。

:::image type="content" source="media/how-to-work-with-maps/historical-information.png" alt-text="最近のアクティビティ":::

デバイスのアクティビティ レポートを生成するには、次のようにします。

1. マップからデバイスを右クリックします。

2. アクティビティ レポートを選択します。

   :::image type="content" source="media/how-to-work-with-maps/activity-report.png" alt-text="アクティビティのレポートを表示する。":::

## <a name="generate-attack-vector-reports-from-the-map"></a>マップから攻撃ベクトル レポートを生成する

攻撃ベクトル レポートをシミュレートして、選択したマップ上のデバイスが脆弱な攻撃対象であるかどうかを確認します。

攻撃ベクトル レポートには、悪用可能なデバイスの脆弱性チェーンがグラフィカルに表示されています。 これらの脆弱性により、攻撃者は重要なネットワーク デバイスにアクセスできます。 攻撃ベクトル シミュレーターでは、攻撃ベクトルをリアルタイムで計算し、特定のターゲットごとにすべての攻撃ベクトルを分析します。

攻撃ベクトル レポートでデバイスを表示するには、次のようにします。

1. マップからデバイスを右クリックします。

2. **[Simulate Attack Vectors]\(攻撃ベクトルのシミュレート\)** を選択します。 攻撃ベクトルのダイアログ ボックスが開き、攻撃対象として選択したデバイスが表示されます。

   :::image type="content" source="media/how-to-work-with-maps/simulation.png" alt-text="攻撃ベクトルのシミュレーションを追加する":::

3. 残りのパラメーターをダイアログ ボックスに追加し、 **[Add Simulation]\(シミュレーションの追加\)** を選択します。

## <a name="export-device-information-from-the-map"></a>マップからデバイス情報をエクスポートする

マップから次のデバイス情報をエクスポートします。

  - デバイスの詳細 (Microsoft Excel)

  - デバイスの概要 (Microsoft Excel)

  - グループを含む Word ファイル (Microsoft Word)

エクスポートするには、次のようにします。

1. マップからエクスポート アイコンを選択します。

1. エクスポート オプションを選択します。

## <a name="see-also"></a>関連項目

[デバイス インベントリのセンサー検出を調査する](how-to-investigate-sensor-detections-in-a-device-inventory.md)
