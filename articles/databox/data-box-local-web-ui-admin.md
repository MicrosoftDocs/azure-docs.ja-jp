---
title: Microsoft Azure Data Box ローカルWeb UI 管理 | データ内の Microsoft Docs
description: ローカル Web UI を使用して Data Box デバイスを管理する方法について説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 02/01/2019
ms.author: alkohli
ms.openlocfilehash: d5b6949930161f78c6edf4213df98471e96b1d42
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404044"
---
# <a name="use-the-local-web-ui-to-administer-your-data-box"></a>ローカル Web UI を使用して Data Box を管理する

この記事では、Data Box で実行できる一部の構成と管理タスクについて説明します。 Data Box の管理は、Azure portal UI またはデバイスのローカル Web UI から行うことができます。 この記事では、ローカル Web UI を使用して実行できるタスクについて説明します。

Data Box のローカル Web UI は、デバイスの初期構成に使用されます。 ローカル Web UI を使用して、Data Box のシャットダウンまたは再起動、診断テストの実行、ソフトウェアの更新、コピー ログの表示、および Microsoft サポートのログ パッケージの生成を行うこともできます。

この記事には次のチュートリアルが含まれています。

- サポート パッケージの生成
- デバイスのシャットダウンと再起動
- BOM またはマニフェスト ファイルのダウンロード
- デバイスの使用可能な容量の表示
- チェックサム検証のスキップ 

## <a name="generate-support-package"></a>サポート パッケージの生成

デバイスの問題が発生した場合は、システム ログからサポート パッケージを作成できます。 Microsoft サポートでは、このパッケージを使用して問題のトラブルシューティングを行います。 サポート パッケージを作成するには、次の手順を実行します。

1. ローカル Web UI で、**[サポートにお問い合わせ]** に移動し、**[サポート パッケージの作成]** をクリックします。

    ![サポート パッケージの作成 1](media/data-box-local-web-ui-admin/create-support-package-1.png)

2. サポート パッケージが収集されます。 この操作は、数分かかります。

    ![サポート パッケージの作成 2](media/data-box-local-web-ui-admin/create-support-package-2.png)

3. サポート パッケージの作成が完了したら、**[サポート パッケージのダウンロード]** をクリックします。 

    ![サポート パッケージの作成 4](media/data-box-local-web-ui-admin/create-support-package-4.png)

4. ダウンロードする場所を参照して選択します。 フォルダを開いて内容を表示します。

    ![サポート パッケージの作成 5](media/data-box-local-web-ui-admin/create-support-package-5.png)


## <a name="shut-down-or-restart-your-device"></a>デバイスのシャットダウンと再起動

ローカル Web UI を使用して、Data Box をシャットダウンまたは再起動できます。 再起動する前に、ホストの共有をオフラインにしてから、デバイスをオフラインにすることをお勧めします。 これにより、データ破損の可能性を最小限に抑えられます。 デバイスをシャットダウンするときに、データのコピーが進行中でないことを確認してください。

Data Box をシャットダウンするには、次の手順を実行します。

1. ローカル Web UI で、**[シャット ダウンまたは再起動]** に移動します。
2. **[シャットダウン]** をクリックします。

    ![Data Box のシャット ダウン 1](media/data-box-local-web-ui-admin/shut-down-local-web-ui-1.png)

3. 確認を求められたら、**[OK]** をクリックして続行します。

    ![Data Box のシャット ダウン 2](media/data-box-local-web-ui-admin/shut-down-local-web-ui-2.png)

デバイスがシャットダウンしたら、フロントパネルの電源ボタンを使用して、デバイスをオンにします。

Data Box を再起動するには、次の手順を実行します。

1. ローカル Web UI で、**[シャット ダウンまたは再起動]** に移動します。
2. **[Restart (再開)]** をクリックします。

    ![Data Box の再起動 1](media/data-box-local-web-ui-admin/restart-local-web-ui-1.png)

3. 確認を求められたら、**[OK]** をクリックして続行します。

   デバイスは、シャットダウンしてから再起動します。

## <a name="download-bom-or-manifest-files"></a>BOM またはマニフェスト ファイルのダウンロード

部品表 (BOM) またはマニフェスト ファイルには、Data Box にコピーされるファイルの一覧が含まれています。 これらのファイルは、発送する Data Box を準備するときに生成されます。

開始する前に、Data Box の**発送準備**手順が完了していることを確認します。 次の手順に従って、BOM またはマニフェスト ファイルをダウンロードします。

1. Data Box のローカル Web UI に移動します。 Data Box の発送準備が完了したことが表示されます。 デバイスの準備が完了すると、デバイスの状態が **[発送する準備ができました]** と表示されます。

    ![デバイスの発送準備完了](media/data-box-portal-admin/ready-to-ship.png)

2. **[ファイルの一覧のダウンロード]** をクリックして、自分の Data Box にコピーされたファイルの一覧をダウンロードします。

    ![[ファイルの一覧のダウンロード] をクリックする](media/data-box-portal-admin/download-list-of-files.png)

3. エクスプローラーで、デバイスへの接続に使用されているプロトコルと使用されている Azure Storage の種類に応じて別個のファイルの一覧が生成されたことを確認できます。

    ![ストレージの種類と接続プロトコル別のファイル](media/data-box-portal-admin/files-storage-connection-type.png)

   ファイル名と Azure Storage の種類および使用されている接続プロトコルのマッピングを次の表に示します。

    |ファイル名  |Azure Storage の種類  |使用される接続プロトコル |
    |---------|---------|---------|
    |databoxe2etest_BlockBlob.txt     |ブロック blob         |SMB/NFS         |
    |databoxe2etest_PageBlob.txt     |ページ blob         |SMB/NFS         |
    |databoxe2etest_AzFile-BOM.txt    |Azure Files         |SMB/NFS         |
    |databoxe2etest_PageBlock_Rest-BOM.txt     |ページ blob         |REST        |
    |databoxe2etest_BlockBlock_Rest-BOM.txt    |ブロック blob         |REST         |
    |mydbmdrg1_MDisk-BOM.txt    |マネージド ディスク         |SMB/NFS         |
    |mydbmdrg2_MDisk-BOM.txt     |マネージド ディスク         |SMB/NFS         |

この一覧を使用して、Data Box が Azure データセンターに返送された後に Azure Storage アカウントにアップロードされたファイルを確認します。 マニフェスト ファイルの例を次に示します。

```xml
<file size="52689" crc64="0x95a62e3f2095181e">\databox\media\data-box-deploy-copy-data\prepare-to-ship2.png</file>
<file size="22117" crc64="0x9b160c2c43ab6869">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer2.png</file>
<file size="57159" crc64="0x1caa82004e0053a4">\databox\media\data-box-deploy-copy-data\verify-used-space-dashboard.png</file>
<file size="24777" crc64="0x3e0db0cd1ad438e0">\databox\media\data-box-deploy-copy-data\prepare-to-ship5.png</file>
<file size="162006" crc64="0x9ceacb612ecb59d6">\databox\media\data-box-cable-options\cabling-dhcp-data-only.png</file>
<file size="155066" crc64="0x051a08d36980f5bc">\databox\media\data-box-cable-options\cabling-2-port-setup.png</file>
<file size="150399" crc64="0x66c5894ff328c0b1">\databox\media\data-box-cable-options\cabling-with-switch-static-ip.png</file>
<file size="158082" crc64="0xbd4b4c5103a783ea">\databox\media\data-box-cable-options\cabling-mgmt-only.png</file>
<file size="148456" crc64="0xa461ad24c8e4344a">\databox\media\data-box-cable-options\cabling-with-static-ip.png</file>
<file size="40417" crc64="0x637f59dd10d032b3">\databox\media\data-box-portal-admin\delete-order1.png</file>
<file size="33704" crc64="0x388546569ea9a29f">\databox\media\data-box-portal-admin\clone-order1.png</file>
<file size="5757" crc64="0x9979df75ee9be91e">\databox\media\data-box-safety\japan.png</file>
<file size="998" crc64="0xc10c5a1863c5f88f">\databox\media\data-box-safety\overload_tip_hazard_icon.png</file>
<file size="5870" crc64="0x4aec2377bb16136d">\databox\media\data-box-safety\south-korea.png</file>
<file size="16572" crc64="0x05b13500a1385a87">\databox\media\data-box-safety\taiwan.png</file>
<file size="999" crc64="0x3f3f1c5c596a4920">\databox\media\data-box-safety\warning_icon.png</file>
<file size="1054" crc64="0x24911140d7487311">\databox\media\data-box-safety\read_safety_and_health_information_icon.png</file>
<file size="1258" crc64="0xc00a2d5480f4fcec">\databox\media\data-box-safety\heavy_weight_hazard_icon.png</file>
<file size="1672" crc64="0x4ae5cfa67c0e895a">\databox\media\data-box-safety\no_user_serviceable_parts_icon.png</file>
<file size="3577" crc64="0x99e3d9df341b62eb">\databox\media\data-box-safety\battery_disposal_icon.png</file>
<file size="993" crc64="0x5a1a78a399840a17">\databox\media\data-box-safety\tip_hazard_icon.png</file>
<file size="1028" crc64="0xffe332400278f013">\databox\media\data-box-safety\electrical_shock_hazard_icon.png</file>
<file size="58699" crc64="0x2c411d5202c78a95">\databox\media\data-box-deploy-ordered\data-box-ordered.png</file>
<file size="46816" crc64="0x31e48aa9ca76bd05">\databox\media\data-box-deploy-ordered\search-azure-data-box1.png</file>
<file size="24160" crc64="0x978fc0c6e0c4c16d">\databox\media\data-box-deploy-ordered\select-data-box-option1.png</file>
<file size="115954" crc64="0x0b42449312086227">\databox\media\data-box-disk-deploy-copy-data\data-box-disk-validation-tool-output.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
<file size="2823" crc64="0x63db1ada6fcdc672">\databox\index.yml</file>
<file size="4364" crc64="0x62b5710f58f00b8b">\databox\data-box-local-web-ui-admin.md</file>
<file size="3603" crc64="0x7e34c25d5606693f">\databox\TOC.yml</file>
```

このファイルには、Data Box にコピーされたすべてのファイルの一覧が含まれています。 このファイルの *crc64* 値は、対応するファイルに対して生成されたチェックサムに関連付けられています。

## <a name="view-available-capacity-of-the-device"></a>デバイスの使用可能な容量の表示

デバイス ダッシュボードを使用して、デバイスの使用可能な容量と使用済み容量を表示できます。 

1. ローカル Web UI で、**[ダッシュボードの表示]** に移動します。
2. **[接続とコピー]** の下に、デバイスの空き容量と使用済み容量が表示されます。

    ![使用可能な容量の表示](media/data-box-local-web-ui-admin/verify-used-space-dashboard.png)


## <a name="skip-checksum-validation"></a>チェックサム検証のスキップ

既定では、発送の準備をするときにデータのチェックサムが生成されます。 特定のまれなケースにおいて、データ型 (小さいファイル サイズ) によっては、パフォーマンスが低下することがあります。 このような場合は、チェックサムをスキップできます。 

パフォーマンスに重大な影響がない限り、チェックサムを無効にしないことを強くお勧めします。

1. デバイスのローカル Web UI の右上隅にある [設定] に移動します。

    ![チェックサムを無効にする](media/data-box-local-web-ui-admin/disable-checksum.png)

2. **[無効]** をクリックしてチェックサム検証を無効にします
3. **[Apply]** をクリックします。

## <a name="next-steps"></a>次の手順

- [Azure portal から Data Box を管理する](data-box-portal-admin.md)方法について説明します。

