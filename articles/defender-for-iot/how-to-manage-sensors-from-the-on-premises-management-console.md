---
title: オンプレミスの管理コンソールからセンサーを管理する
description: センサーのバージョンの更新、センサーへのシステム設定のプッシュ、センサーでのエンジンの有効化と無効化など、管理コンソールからセンサーを管理する方法について説明します。
ms.date: 12/07/2020
ms.topic: how-to
ms.openlocfilehash: 4706fbcf58b8e5f5eed6532f9a08f65c7716e07e
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781927"
---
# <a name="manage-sensors-from-the-management-console"></a>管理コンソールからセンサーを管理する

この記事では、以下を含めて、管理コンソールからセンサーを管理する方法について説明します。

- システム設定をセンサーにプッシュする

- センサーのエンジンの有効と無効を切り替える

- センサーのバージョンを更新する

## <a name="push-configurations"></a>構成をプッシュする

さまざまなシステム設定を定義し、それらを自動的に、管理コンソールに接続されているセンサーに適用できます。 これは時間の節約になり、エンタープライズ センサー全体にわたる設定を確実に簡素化するのに役立ちます。

管理コンソールからは、以下のセンサー システム設定を定義できます。

- メール サーバー

- SNMP MIB の監視

- Active Directory

- DNS の設定

- サブネット

- ポート エイリアス

システム設定を適用するには:

1. コンソールの左側ペインで、 **[システム設定]** を選択します。

2. **[センサーの構成]** ペインで、いずれかのオプションを選択します。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-system-setting-options.png" alt-text="センサーのシステム設定オプション。":::

   次の例では、エンタープライズ センサーのメール サーバー パラメーターを定義する方法について説明します。

3. **[メール サーバー]** を選択します。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="[システム設定] 画面からメール サーバーを選択します。":::

4. 左側でセンサーを選択します。

5. メール サーバー パラメーターを設定し、 **[複製]** を選択します。 センサー ツリー内の各項目は、横にチェック ボックスが付いた状態で表示されます。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/check-off-each-sensor.png" alt-text="目的のセンサーのチェック ボックスがオンになっていることを確認します。":::

6. センサー ツリーで、構成を適用する項目を選択します。

7. **[保存]** を選択します。

## <a name="update-versions"></a>バージョンを更新する

オンプレミスの管理コンソールから複数のセンサーを同時に更新できます。

複数のセンサーを更新するには:

1. [Azure ポータル](https://portal.azure.com/)にアクセスします。

2. Azure Defender for IoT に移動します。

3. **[更新プログラム]** ページに移動します。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/update-screen.png" alt-text="[更新プログラム] ダッシュボード ビューのスクリーンショット。":::

4. **[センサー]** セクションの **[ダウンロード]** を選択し、ファイルを保存します。

5. 管理コンソールにサインインして、 **[システム設定]** を選択します。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/admin-system-settings.png" alt-text="[システム設定] を選択する [管理] メニューのスクリーンショット。":::

6. **[センサー エンジンの構成]** セクションで、更新するセンサーにマークを付けてから、 **[自動更新]** を選択します。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensors-select.png" alt-text="学習モードと自動更新を示している 2 つのセンサー。":::

7. **[変更の保存]** を選択します。

8. **[センサー バージョンのアップグレード]** ペインで、:::image type="icon" source="media/how-to-manage-sensors-from-the-on-premises-management-console/plus-icon.png" border="false"::: を選択します。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/display-files.png" alt-text="ファイルを表示する [センサー バージョンのアップグレード] 画面。":::

9. **[ファイルのアップロード]** ダイアログ ボックスが開きます。 **[更新プログラム]** ページからダウンロードしたファイルをアップロードします。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/upload-file.png" alt-text="[参照] ボタンを選択し、ファイルをアップロードします。":::

10. 更新プロセス中には、 **[サイト管理]** ウィンドウに各センサーの更新状態が表示されます。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/progress.png" alt-text="更新の進行状況を観察します。":::

## <a name="update-threat-intelligence-packages"></a>脅威インテリジェンス パッケージを更新する 

脅威インテリジェンス用のデータ パッケージは、新しい Defender for IoT バージョンごとに、またはリリース間で必要に応じて提供されます。 パッケージには、シグネチャ (マルウェア シグネチャを含む)、CVE、その他のセキュリティ コンテンツが含まれています。 

このファイルは、Defender for IoT ポータルの **[更新プログラム]** ページから手動でアップロードし、センサーに対して自動更新することができます。 

脅威インテリジェンスのデータを更新するには: 

1. Defender for IoT の **[更新プログラム]** ページに移動します。 

2. ファイルをダウンロードして保存します。

3. 管理コンソールにサインインします。 

4. サイド メニューで、 **[システム設定]** を選択します。 

5. **[センサー エンジンの構成]** セクションで、更新プログラムを受信する必要のあるセンサーを選択します。  

6. **[脅威インテリジェンス データの選択]** セクションで、プラス記号 ( **+** ) を選択します。 

7. Defender for IoT の **[更新プログラム]** ページから、ダウンロードしたパッケージをアップロードします。

## <a name="understand-sensor-disconnection-events"></a>センサー切断イベントについて

センサーが、割り当てられているオンプレミスの管理コンソールから切断されると、 **[Site Manager]** ウィンドウに、切断情報が表示されます。 以下のようなセンサー切断情報が提供されます。

- "オンプレミスの管理コンソールでは、センサーから受信したデータを処理できません。"

- "時間のずれが検出されました。 オンプレミスの管理コンソールはセンサーから切断されています。"

- "センサーがオンプレミスの管理コンソールと通信していません。 ネットワーク接続または証明書の検証を調べてください。"

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="ゾーン 1 ビューのスクリーンショット。":::

切断されたセンサーに関する情報を使用して、サード パーティにアラートを送信できます。 詳細については、「[センサーの障害アラートの転送](how-to-manage-individual-sensors.md#forward-sensor-failure-alerts)」を参照してください。

## <a name="enable-or-disable-sensors"></a>センサーを有効または無効にする

センサーは、5 つの Defender for IoT エンジンによって保護されます。 接続されているセンサーのエンジンは、有効または無効にすることができます。

| エンジン | [説明] | シナリオ例 |
|--|--|--|
| プロトコル違反エンジン | プロトコル違反は、パケット構造またはフィールド値がプロトコル仕様に準拠していない場合に発生します。 | "無効な MODBUS 操作 (関数コード ゼロ)" アラート。 このアラートは、プライマリ デバイスが、セカンダリ デバイスに対して関数コード 0 の要求を送信したことを示します。 プロトコルの仕様に従い、これは許可されません。セカンダリ デバイスでは入力が正しく処理されない可能性があります。 |
| ポリシー違反エンジン | ポリシー違反は、学習または構成されたポリシーで定義されているベースライン動作からの逸脱によって発生します。 | "認可されていない HTTP ユーザー エージェント" アラート。 このアラートは、デバイスで、学習されていないか、ポリシーによって承認されていないアプリケーションが HTTP クライアントとして使用されていることを示します。 これは、そのデバイス上の新しい Web ブラウザーまたはアプリケーションである可能性があります。 |
| マルウェア対策エンジン | マルウェア対策エンジンでは、悪意のあるネットワーク アクティビティが検出されます。 | "悪意のあるアクティビティ (Stuxnet) の疑い" アラート。 このアラートは、センサーで、Stuxnet マルウェアに関連することが判明している疑わしいネットワーク アクティビティが検出されたことを示します。これは、産業用制御ネットワークや SCADA ネットワークを狙う高度で永続的な脅威です。 |
| 異常エンジン | マルウェア対策エンジンでは、ネットワーク動作の異常を検出します。 | "通信チャネルにおける周期的動作" アラート。 これは、ネットワーク接続を検査し、データ転送の反復的動作や周期的な動作を検出するコンポーネントで、産業用ネットワークでは一般的です。 |
| 運用エンジン | このエンジンでは、運用上のインシデントや正常に機能していないエンティティを検出します。 | `Device is Suspected to be Disconnected (Unresponsive)` アラート。 このアラートがトリガーされるのは、事前に定義された期間に、デバイスがどの要求にも応答していないときです。 これは、デバイスのシャットダウン、切断、誤動作を示している可能性があります。
|

接続されているセンサーのエンジンを有効または無効にするには:

1. コンソールの左側ペインで、 **[システム設定]** を選択します。

2. **[センサー エンジンの構成]** セクションで、エンジンに対して **[有効]** または **[無効]** を選択します。
         
3. **[変更の保存]** を選択します。

   いずれかのエンタープライズ センサーについて、有効にされたエンジンの不一致があると、赤色の感嘆符が表示されます。 こうしたエンジンは、センサーから直接無効にされた可能性があります。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/red-exclamation-example.png" alt-text="有効にされたエンジンの不一致。"::: 

## <a name="define-sensor-backup-schedules"></a>センサーのバックアップ スケジュールを定義する

オンプレミスの管理コンソールから、センサーのバックアップをスケジュールし、オンデマンドのセンサーのバックアップを実行できます。 これは、ハード ドライブの障害やデータの損失からの保護に役立ちます。

- バックアップされるもの:構成とデータ。

- バックアップされないもの:PCAP ファイルとログ。 PCAP とログのバックアップと復元は手動で行うことができます。

センサーは、既定では毎日午前 3:00 に自動的にバックアップされます。 センサーのバックアップ スケジュール機能を使用すると、これらのバックアップを収集し、オンプレミスの管理コンソールまたは外部バックアップ サーバーに保存できます。 センサーからオンプレミスの管理コンソールへのファイル コピーは、暗号化されたチャネルを介して行われます。

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-backup-schedule-screen.png" alt-text="センサーのバックアップ画面のビュー。":::

既定のセンサーのバックアップ場所が変更されると、コンソールに場所へのアクセス許可がある場合は、オンプレミスの管理コンソールによって自動的に、センサーまたは外部の場所にある新しい場所からファイルが取得されます。 

センサーがオンプレミスの管理コンソールに登録されていないと、 **[センサーのバックアップ スケジュール]** ダイアログ ボックスに、管理されているセンサーがないことが示されます。  

復元プロセスは、ファイルが格納されている場所を問わず同一です。

### <a name="backup-storage-for-sensors"></a>センサー用のバックアップ ストレージ

バックアップ ファイルが、割り当てられている最大バックアップ領域を超過しなければ、オンプレミスの管理コンソールを使用して、管理センサーごとに最大 9 個のバックアップを管理できます。 

使用できる領域は、使用している管理コンソール モデルに基づいて計算されます。 

- **実稼働モード**:既定のストレージは 40 GB で、制限は 100 GB です。 

- **中規模モデル**:既定のストレージは 20 GB で、制限は 50 GB です。 

- **ラップトップ モデル**:既定のストレージは 10 GB で、制限は 25 GB です。 

- **シン モデル**:既定のストレージは 2 GB で、制限は 4 GB です。 

- **ラグド モデル**:既定のストレージは 10 GB で、制限は 25 GB です。 

既定の割り当ては、 **[センサーのバックアップ スケジュール]** ダイアログ ボックスに表示されます。 

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-mail-server-configuration.png" alt-text="[メール サーバーの構成の編集] 画面。":::

外部サーバーにバックアップする場合、ストレージの制限はありません。 ただし、 **[センサーのバックアップ スケジュール]**  >  **[カスタム パス]** フィールドで、割り当ての上限を定義する必要があります。 次の数値と文字がサポートされています: `/, a-z, A-Z, 0-9, and _`。 

以下に、割り当てストレージの制限を超えた場合に関する情報を示します。

- 割り当てられたストレージ スペースを超えた場合、センサーはバックアップされません。 

- 複数のセンサーをバックアップしようとしている場合、管理コンソールでは、管理対象センサーのセンサー ファイルの取得が試みられます。  

- 1 つのセンサーからの取得が制限を超えた場合、管理コンソールでは、次のセンサーからのバックアップ情報の取得が試みられます。 

定義されている、保持するバックアップの数を超えた場合、新しいファイルを格納するために、最も古いバックアップ ファイルが削除されます。

センサーのバックアップ ファイルには、次の形式で自動的に名前が付けられます: `<sensor name>-backup-version-<version>-<date>.tar`。 (例: `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`)。 

センサーをバックアップするには:

1. **[システム設定]** ウィンドウで、 **[センサー バックアップのスケジュール]** を選択します。 オンプレミスの管理コンソールで管理しているセンサーが、 **[センサーのバックアップ スケジュール]** ダイアログ ボックスに表示されます。  

2. **[バックアップの収集]** トグルを有効にします。  

3. カレンダー上での間隔、日付、タイム ゾーンを選択します。 時刻の形式は、24 時間制に基づきます。 たとえば、午後の 6:00 は **18:00** として入力します。 

4. **[バックアップ ストレージの割り当て]** フィールドに、バックアップに割り当てるストレージを入力します。 最大容量を超えた場合は通知が行われます。

5. **[最後の保持]** フィールドで、センサーごとに保持するバックアップの数を指定します。 制限を超えると、最も古いバックアップが削除されます。  

6. バックアップの場所を選択します。  

   - オンプレミスの管理コンソールにバックアップするには、 **[カスタム パス]** トグルを無効にします。 既定の場所は `/var/cyberx/sensor-backups` です。  

   - 外部サーバーにバックアップするには、 **[カスタム パス]** トグルを有効にして、場所を入力します。 次の数値と文字がサポートされています: `/, a-z, A-Z, 0-9, and, _`。 

7. **[保存]** を選択します。 

すぐにバックアップするには: 

- **[今すぐバックアップ]** を選択します。 オンプレミスの管理コンソールで、センサーのバックアップ ファイルの作成と収集が行われます。 

### <a name="receiving-backup-notifications-for-sensors"></a>センサーのバックアップ通知の受信 

**[センサーのバックアップ スケジュール]** ダイアログ ボックスとバックアップ ログの一覧に、バックアップの成功と失敗に関する情報が自動的に示されます。  

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-location.png" alt-text="センサーと、それらが配置されている場所、すべての関連情報を表示します。":::

失敗は、以下の理由で発生する可能性があります。    

- バックアップ ファイルが見つからない。 

- ファイルは見つかったが取得できない。  

- ネットワーク接続の障害が起きている。 

- バックアップを完了するために、オンプレミスの管理コンソールに割り当てられた領域が十分ではない。  

失敗が発生したときには、メール通知、syslog の更新、システム通知を送信できます。 これを行うには、 **[システム通知]** で転送ルールを作成します。 

### <a name="restoring-sensors"></a>センサーの復元 

バックアップの復元は、オンプレミスの管理コンソールから、または CLI を使用して行えます。  

コンソールから復元するには: 

- **[センサー システム]** 設定ウィンドウから **[イメージの復元]** を選択します。

  :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/restore.png" alt-text="イメージのバックアップを復元します。":::

  その後、復元の失敗はコンソールに表示されます。  

CLI を使用して復元するには: 

- 管理者アカウントにサインインし、「`$ sudo cyberx-xsense-system-restore`」と入力します。 

### <a name="save-a-sensor-backup-to-an-external-smb-server"></a>外部の SMB サーバーにセンサーのバックアップを保存する

センサーのバックアップを外部ドライブに保存できるように SMB サーバーを設定するには: 

1. 外部の SMB サーバーに共有フォルダーを作成します。 

2. SMB サーバーへのアクセスに必要なフォルダー パス、ユーザー名、パスワードを取得します。 

3. Defender for IoT で、バックアップ用のディレクトリを作成します。 

   `sudo mkdir /<backup_folder_name_on_server>` 

   `sudo chmod 777 /<backup_folder_name_on_server>/` 

4. fstab を編集します。  

   `sudo nano /etc/fstab` 

   `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

5. 共有する資格情報を編集または作成します。 これらは SMB サーバーの資格情報です。 

   `sudo nano /etc/samba/user` 

6. 次の項目を追加します。  

   `username=<user name>` 

   `password=<password>` 

7. ディレクトリをマウントします。 

   `sudo mount -a` 

8. Defender for IoT センサー上の共有フォルダーに、バックアップ ディレクトリを構成します。  

   `sudo nano /var/cyberx/properties/backup.properties` 

9. `Backup.shared_location` を `<backup_folder_name_on_cyberx_server>` に設定します。

## <a name="see-also"></a>関連項目

[個々のセンサーの管理](how-to-manage-individual-sensors.md)
