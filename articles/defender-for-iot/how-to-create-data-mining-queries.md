---
title: データ マイニング レポートの作成
description: さまざまなレイヤーのネットワーク デバイスに関する包括的で詳細な情報 (プロトコル、ファームウェア バージョン、プログラミング コマンドなど) を生成します。
ms.date: 01/20/2021
ms.topic: how-to
ms.openlocfilehash: 99754959e7a3a08b4d763b85b0b9315476969774
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779071"
---
# <a name="sensor-data-mining-queries"></a>センサーのデータ マイニング クエリ

## <a name="about-sensor-data-mining-queries"></a>センサーのデータ マイニング クエリについて

データ マイニング ツールを使用すると、さまざまなレイヤーのネットワーク デバイスに関する包括的で詳細な情報を生成できます。 たとえば、次に基づいてクエリを作成できます。

- [時間間隔]

- インターネットへの接続

- Port

- プロトコル

- ファームウェアのバージョン

- プログラミング コマンド

- デバイスの非アクティブ状態

フィルターに基づいてレポートを微調整できます。 たとえば、ファームウェアが更新された特定のサブネットに対してクエリを実行できます。

:::image type="content" source="media/how-to-generate-reports/active-device-list-v2.png" alt-text="アクティブ デバイスの一覧。":::

クエリを管理するために、さまざまなツールを使用できます。 たとえば、エクスポートして保存することができます。

> [!NOTE]
> 管理者およびセキュリティ アナリストは、データ マイニング オプションにアクセスできます。

### <a name="dynamic-updates"></a>動的更新

作成したデータ マイニング クエリは、開くたびに動的に更新されます。 例:

- 6 月 1 日にデバイスのファームウェアのバージョンに関するレポートを作成し、6 月 10 日に再度レポートを開くと、このレポートは 6 月 10 日の正確な情報で更新されます。

- 6 月 1 日に過去 30 日間に検出された新しいデバイスを検出するレポートを作成し、6 月 30 日にレポートを開くと、過去 30 日間の結果が表示されます。

### <a name="data-mining-use-cases"></a>データ マイニングのユース ケース

クエリを使用して、さまざまなセキュリティ チームの幅広いセキュリティ ニーズに対処できます。

- **SOC インシデント対応**: リアルタイムでレポートを生成して、迅速なインシデント対応を支援します。 たとえば、データ マイニングでは、修正プログラムの適用が必要な可能性があるデバイスの一覧を示すレポートを生成できます。

- **フォレンジクス**: 調査レポートの履歴データに基づくレポートを生成します。

- **IT ネットワークの整合性**: 全体的なネットワーク セキュリティの向上に役立つレポートを生成します。 たとえば、認証資格情報が弱いデバイスを一覧表示するレポートを生成できます。

- **可視性**: すべてのクエリ項目を対象として、ネットワークのすべてのベースライン パラメーターが表示されるレポートを生成します。

## <a name="data-mining-storage"></a>データ マイニングのストレージ

データ マイニング情報は、デバイスが削除された場合を除き、継続して保存および格納されます。 データ マイニングの結果は、セキュリティで保護された外部サーバーにエクスポートしたり格納したりできます。 さらに、システムの継続性とデータの保持のために、センサーによって毎日の自動バックアップが実行されます。


## <a name="predefined-data-mining-queries"></a>定義済みのデータ マイニング クエリ

次の定義済みクエリを使用できます。 これらのクエリはリアルタイムで生成されます。

- **CVE**: 過去 24 時間以内に既知の脆弱性が検出されたデバイスの一覧。

- **Excluded CVEs\(除外された CVE\)** : 手動で除外されたすべての CVE の一覧。 VA レポートと攻撃ベクトルでより正確な結果を得るために、CVE を含めたり除外したりして CVE 一覧を手動でカスタマイズできます。

- **Internet activity \(インターネット アクティビティ\)** : インターネットに接続されているデバイス。

- **Nonactive devices \(非アクティブ デバイス\)** : 過去 7 日間通信していないデバイス。

- **Active devices \(アクティブ デバイス\)** : 過去 24 時間以内にアクティブなネットワーク デバイス。

- **Remote access \(リモート アクセス\)** : リモート セッション プロトコルを介して通信するデバイス。

- **Programming commands \(プログラミング コマンド\)** : 産業用プログラミングを送信するデバイス。

これらのレポートには、 **[Reports]\(レポート\)** 画面から自動的にアクセスできます。RO ユーザーやその他のユーザーはそこでそれらを閲覧できます。 RO ユーザーはデータ マイニング レポートにはアクセスできません。

:::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="データ マイニング画面。":::

## <a name="create-a-data-mining-query"></a>データ マイニング クエリを作成する

データ マイニング レポートを作成するには、次のようにします。

1. サイド メニューから、 **[Data Mining]\(データ マイニング\)** を選択します。 定義済みの推奨レポートが自動的に表示されます。

 :::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="サイド ペインからデータ マイニングを選択する。":::

2. [:::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false":::] を選択します。

3. **[New Report]\(新しいレポート\)** を選択し、レポートを定義します。

   :::image type="content" source="media/how-to-generate-reports/create-new-report-screen.png" alt-text="この画面に入力して、新しいレポートを作成する。":::

   次のパラメーターを使用できます。

   - レポートの名前と説明を指定します。

   - カテゴリについては、次のいずれかを選択します。

      - ネットワーク内の全デバイスに関するすべてのレポート結果を表示するには、 **[Categories (All)]\(カテゴリ (すべて)\)** 。

      - 標準のカテゴリを選択するには、 **[Generic]\(汎用\)** 。

   - 関心のある特定のレポート パラメーターを選択します。

   - 並べ替え順序 ( **[Order by]\(並べ替え\)** ) を選択します。 アクティビティまたはカテゴリに基づいて結果を並べ替えます。

   - **[Save to Report Pages]\(レポート ページに保存\)** を選択すると、レポート結果が、 **[Reports]\(レポート\)** ページからアクセスできるレポートとして保存されます。 これにより、作成されたレポートを RO ユーザーが実行できるようになります。

   - フィルターをさらに追加するには、 **[Filters (Add)]\(フィルター (追加)\)** を選択します。 ワイルドカード要求がサポートされています。

   - (デバイス マップに定義されている) デバイス グループを指定します。

   - IP アドレスを指定します。

   - ポートを指定します。

   - MAC アドレスを指定します。

4. **[保存]** を選択します。 **[Data Mining]\(データ マイニング\)** ページでレポートの結果が開きます。

:::image type="content" source="media/how-to-generate-reports/data-mining-page.png" alt-text="[Data Mining]\(データ マイニング\) ページに表示されているレポートの結果。":::

### <a name="manage-data-mining-reports"></a>データ マイニング レポートを管理する

次の表では、データ マイニングの管理オプションについて説明します。

| アイコンの画像 | 説明 |
|--|--|
| :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: | レポートのパラメーターを編集します。 |
| :::image type="icon" source="media/how-to-generate-reports/export-as-pdf-icon.png" border="false"::: | PDF としてエクスポートします。 |
| :::image type="icon" source="media/how-to-generate-reports/csv-export-icon.png" border="false"::: |CSV としてエクスポートします。 |
| :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false"::: | 最終更新日などの追加情報を表示します。 この機能を使用して、クエリ結果のスナップショットを作成します。 たとえば、チーム リーダーや SOC アナリストと共に詳細な調査を行うために、この操作が必要になる場合があります。 |
| :::image type="icon" source="media/how-to-generate-reports/pin-icon.png" border="false"::: | **[Reports]\(レポート\)** ページに表示するか、 **[Reports]\(レポート\)** ページで非表示にします。 :::image type="content" source="media/how-to-generate-reports/hide-reports-page.png" alt-text="レポートを表示または非表示にする。"::: |
| :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: | レポートを削除します。 |

#### <a name="create-customized-directories"></a>カスタマイズされたディレクトリを作成する 

カテゴリのディレクトリを作成することにより、データ マイニング クエリの広範な情報を整理できます。 たとえば、プロトコル用または場所用のディレクトリを作成できます。

新しいディレクトリを作成するには、次のようにします。

1. :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false"::: を選択して、新しいディレクトリを追加します。

2. **[New Directory]\(新しいディレクトリ\)** を選択して、新しいディレクトリのフォームを表示します。

3. 新しいディレクトリに名前を付けます。

4. 必要なレポートを新しいディレクトリにドラッグします。 いつでも、レポートをメイン ビューにドラッグして戻すことができます。

5. 新しいディレクトリを右クリックして、開いたり、編集したり、削除したりします。

#### <a name="create-snapshots-of-report-results"></a>レポート結果のスナップショットを作成する

詳細な調査を行うために特定のクエリ結果を保存する必要がある場合があります。 たとえば、さまざまなセキュリティ チームと結果を共有することが必要になる場合があります。

スナップショットはレポート結果内に保存され、動的クエリは生成されません。

:::image type="content" source="media/how-to-generate-reports/report-results-report.png" alt-text="スナップショット。":::

スナップショットを作成するには、次のようにします。

1. 必要なレポートを開きます。

2. 情報アイコン :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false"::: を選択します。

3. **[Take New]\(新規作成\)** を選択します。

4. スナップショットの名前を入力し、 **[保存]** を選択します。

:::image type="content" source="media/how-to-generate-reports/take-a-snapshot.png" alt-text="スナップショットを作成する。":::

#### <a name="customize-the-cve-list"></a>CVE 一覧をカスタマイズする

次のように、CVE 一覧を手動でカスタマイズできます。

  - CVE を含める、または除外する

  - CVE スコアを変更する

CVE レポートで手動の変更を行うには、次のようにします。

1.  サイド メニューから、 **[Data Mining]\(データ マイニング\)** を選択します。

2. **[Data Mining]\(データ マイニング\)** ウィンドウの左上隅にある :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false"::: を選択します。 次に、 **[New Report]\(新しいレポート\)** を選択します。

   :::image type="content" source="media/how-to-generate-reports/create-a-new-report-screen.png" alt-text="新しいレポートを作成する。":::

3. 左側のペインから、次のいずれかのオプションを選択します。

   - **Known Vulnerabilities \(既知の脆弱性\)** : 両方のオプションを選択し、レポートの 2 つのテーブル (1つは CVE が含まれたもので、もう 1 つは CVE が除外されたもの) に結果を表示します。

   - **CVE**: すべての CVE の一覧を表示するには、このオプションを選択します。

   - **Excluded CVEs\(除外された CVE\)** : 除外されたすべての CVE の一覧を表示するには、このオプションを選択します。

4. **[名前]** と **[説明]** の情報を入力し、 **[保存]** を選択します。 新しいレポートが **[Data Mining]\(データ マイニング\)** ウィンドウに表示されます。

5. CVE を除外するには、CVE のデータ マイニング レポートを開きます。 すべての CVE の一覧が表示されます。

   :::image type="content" source="media/how-to-generate-reports/cves.png" alt-text="CVE レポート。":::

6. 一覧から項目を選択できるようにするには、:::image type="icon" source="media/how-to-generate-reports/enable-selecting-icon.png" border="false"::: を選択し、カスタマイズする CVE を選択します。 **[操作]** バーが下部に表示されます。

   :::image type="content" source="media/how-to-generate-reports/operations-bar-appears.png" alt-text="データ マイニングの操作バーのスクリーンショット。":::

7. 除外する CVE を選択してから、 **[Delete Records]\(レコードの削除\)** を選択します。 選択した CVE は、CVE の一覧に表示されず、生成時に除外される CVE の一覧に表示されます。

8. 除外された CVE を CVE の一覧に含めるには、除外された CVE のレポートを生成し、その一覧から、CVE の一覧に再び含める項目を削除します。

9. スコアを変更する CVE を選択し、 **[Update CVE Score]\(CVE スコアの更新\)** を選択します。

   :::image type="content" source="media/how-to-generate-reports/set-new-score-screen.png" alt-text="CVE スコアを更新する。":::

10. 新しいスコアを入力し、 **[OK]** を選択します。 選択した CVE に、更新されたスコアが表示されます。



## <a name="sensor-reports-based-on-data-mining"></a>データ マイニングに基づくセンサー レポート

**[レポート]** オプションからアクセスする通常のレポートは、事前に定義されたデータ マイニング レポートです。 これらは、データ マイニングで使用できる動的クエリではなく、データ マイニング クエリの結果を静的に表したものです。

読み取り専用ユーザーは、データ マイニング クエリの結果を使用できません。 データ マイニング クエリによって生成された情報に読み取り専用ユーザーがアクセスできるようにするには、管理者およびセキュリティ アナリストが、この情報をレポートとして保存する必要があります。

レポートには、データ マイニング クエリの結果によって生成された情報が反映されます。 これには、[Reports]\(レポート\) ビューで使用できる既定のデータ マイニング レポートが含まれます。 管理者とセキュリティ アナリストは、カスタムのデータ マイニング クエリを生成し、レポートとして保存することもできます。 これらのレポートは、RO ユーザーも使用できます。

レポートを生成するには:

1. サイド メニューから **[Reports]\(レポート\)** を選択します。

2. 表示する必要があるレポートを選択します。 **カスタム** または **自動生成** レポート (**プログラミング コマンド** や **リモート アクセス** など) を選択できます。

3. レポートをエクスポートするには、画面の右上にあるアイコンのいずれかを選択します。

   :::image type="icon" source="media/how-to-generate-reports/export-to-pdf-icon.png" border="false"::: PDF ファイルにエクスポートします。

   :::image type="icon" source="media/how-to-generate-reports/export-to-csv-icon.png" border="false"::: CSV ファイルにエクスポートします。

> [!NOTE] 
> RO ユーザーは、自分たちのために作成されたレポートのみを表示できます。

:::image type="content" source="media/how-to-generate-reports/select-a-report-screen.png" alt-text="生成するレポートを選択する。":::

:::image type="content" source="media/how-to-generate-reports/remote-access-report.png" alt-text="リモート アクセス レポートが生成された。":::

## <a name="on-premises-management-console-reports-based-on-data-mining-reports"></a>データ マイニング レポートに基づくオンプレミス管理コンソールのレポート

オンプレミス管理コンソールを使用すると、接続されているセンサーごとにレポートを生成できます。 レポートは、実行されるセンサーのデータ マイニング クエリに基づきます。

次のレポートを生成できます。

- **アクティブ デバイス (過去 24 時間)** : 24 時間以内にネットワーク アクティビティが示されているデバイスの一覧を表示します。

- **非アクティブ デバイス (過去 7 日間)** : 過去 7 日間以内にネットワーク アクティビティが示されていないデバイスの一覧を表示します。

- **プログラミング コマンド**: 過去 24 時間以内にプログラミング コマンドが送信されたデバイスの一覧を表示します。

- **リモート アクセス**: 過去 24 時間以内にリモート リソースによってアクセスされたデバイスの一覧を表示します。

:::image type="content" source="media/how-to-generate-reports/reports-view.png" alt-text="レポート ビューのスクリーンショット。":::

オンプレミス管理コンソールからセンサーを選択すると、そのセンサーに構成されているすべてのカスタム レポートがレポートの一覧に表示されます。 センサーごとに、既定のレポート、またはそのセンサーに構成されているカスタム レポートを生成できます。

レポートを生成するには:

1. 左側のペインで、 **[Reports]\(レポート\)** を選択します。 **[Reports]\(レポート\)** ウィンドウが表示されます。

2. **[Sensors]\(センサー\)** ドロップダウン リストから、レポートを生成するセンサーを選択します。

   :::image type="content" source="media/how-to-generate-reports/sensor-drop-down-list.png" alt-text="センサー ビューのスクリーンショット。":::

3. 右側のドロップダウン リストから、生成するレポートを選択します。

4. レポート結果の PDF を作成するには、:::image type="icon" source="media/how-to-generate-reports/pdf-report-icon.png" border="false"::: を選択します。
