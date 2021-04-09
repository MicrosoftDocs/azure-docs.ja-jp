---
title: DPM を使用した SharePoint ファームの Azure へのバックアップ
description: この記事では、Azure への SharePoint ファームの DPM/Azure Backup サーバー保護の概要について説明します
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 7661d64e487c8b8badca240852d17bcf736ba8cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91254433"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-dpm"></a>DPM を使用した SharePoint ファームの Azure へのバックアップ

System Center Data Protection Manager (DPM) を使用して SharePoint ファームを Microsoft Azure にバックアップする方法は、他のデータ ソースのバックアップとよく似ています。 Azure Backup ではバックアップのスケジュールを柔軟に設定して日、週、月、年の単位でバックアップ ポイントを作成でき、さまざまなバックアップ ポイントに対応する保有ポリシー オプションがあります。 DPM では、目標復旧時間 (RTO) 短縮のためにはローカル ディスク コピーを保存でき、コスト効率に優れた長期リテンション期間のためには Azure にコピーできます。

DPM を使用して SharePoint を Azure にバックアップするプロセスは、SharePoint をローカルで DPM にバックアップする場合とよく似ています。 この記事では、Azure に関する特定の考慮事項について説明します。

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>SharePoint のサポートされるバージョンと関連する保護シナリオ

サポートされている SharePoint のバージョンと、それらのバックアップに必要な DPM のバージョンの一覧については、「[DPM でバックアップできるものは何か](/system-center/dpm/dpm-protection-matrix#applications-backup)」を参照してください。

## <a name="before-you-start"></a>開始する前に

SharePoint ファームを Azure にバックアップする前に、確認する必要がある点がいくつかあります。

### <a name="prerequisites"></a>前提条件

続行する前に、 [Microsoft Azure Backup を使用してワークロードを保護するための前提条件](backup-azure-dpm-introduction.md#prerequisites-and-limitations) がすべて満たされていることを確認します。 前提条件を満たすための作業として、バックアップ コンテナーの作成、コンテナー資格情報のダウンロード、Azure Backup エージェントのインストール、コンテナーへの DPM/Azure Backup Server の登録などのがあります。

追加の前提条件と制限事項については、[DPM を使用した SharePoint のバックアップ](/system-center/dpm/back-up-sharepoint#prerequisites-and-limitations)に関する記事を参照してください。

## <a name="configure-backup"></a>バックアップの構成

SharePoint ファームをバックアップするには、ConfigureSharePoint.exe を使用して SharePoint の保護を構成し、DPM で保護グループを作成します。 手順については、DPM ドキュメントの「[バックアップの構成](/system-center/dpm/back-up-sharepoint#configure-backup)」を参照してください。

## <a name="monitoring"></a>監視

バックアップジョブを監視するには、[DPM バックアップの監視](/system-center/dpm/back-up-sharepoint#monitoring)に関する記事の手順に従います

## <a name="restore-sharepoint-data"></a>SharePoint データの復元

DPM を使用してディスクから SharePoint 項目を復元する方法については、「[SharePoint データの復元](/system-center/dpm/back-up-sharepoint#restore-sharepoint-data)」を参照してください。

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>DPM を使用した Azure からの SharePoint データベースの復元

1. SharePoint コンテンツ データベースを回復するには、さまざまな回復ポイントを参照して (前述したように)、復元する回復ポイントを選択します。

    ![DPM の SharePoint 保護 8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. SharePoint の回復ポイントをダブルクリックして、使用可能な SharePoint カタログ情報を表示します。

   > [!NOTE]
   > SharePoint ファームは Azure では長期保有期間用に保護されているので、DPM サーバーには使用可能なカタログ情報 (メタデータ) がありません。 その結果、特定時点の SharePoint コンテンツ データベースを回復する必要があるときは常に、SharePoint ファームを再カタログ化する必要があります。
   >
   >
3. **[再カタログ化]** を選択します。

    ![DPM の SharePoint 保護 10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    **[クラウドの再カタログ化]** ステータス ウィンドウが開きます。

    ![DPM の SharePoint 保護 11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    カタログ化が完了すると、ステータスが *[成功]* に変わります。 **[閉じる]** を選択します。

    ![DPM の SharePoint 保護 12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. DPM の **[回復]** タブに表示される SharePoint オブジェクトを選択して、コンテンツ データベースの構造を取得します。 項目を右クリックし、 **[回復]** を選択します。

    ![DPM の SharePoint 保護 13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. この時点で、この記事で前述した回復手順に従ってディスクから SharePoint コンテンツ データベースを回復します。

## <a name="switching-the-front-end-web-server"></a>フロントエンド Web サーバーの切り替え

複数のフロントエンド Web サーバーがあり、DPM でファームの保護に使用するサーバーを切り替えたい場合は、「[フロントエンド Web サーバーの切り替え](/system-center/dpm/back-up-sharepoint#switching-the-front-end-web-server)」の手順に従ってください。

## <a name="next-steps"></a>次のステップ

* [Azure Backup Server と DPM - FAQ](backup-azure-dpm-azure-server-faq.md)
* [System Center Data Protection Manager のトラブルシューティング](backup-azure-scdpm-troubleshooting.md)
