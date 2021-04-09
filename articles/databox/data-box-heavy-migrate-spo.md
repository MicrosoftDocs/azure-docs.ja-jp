---
title: Azure Data Box Heavy を使用してファイル共有のコンテンツを SharePoint Online に移動する
description: このチュートリアルでは、Azure Data Box Heavy を使用してファイル共有のコンテンツを Share Point Online に移行する方法について説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: how-to
ms.date: 07/18/2019
ms.author: alkohli
ms.openlocfilehash: b00a944b69b8492d4aa00258154bf00f1c66ad33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92127116"
---
# <a name="use-the-azure-data-box-heavy-to-migrate-your-file-share-content-to-sharepoint-online"></a>Azure Data Box Heavy を使用してファイル共有のコンテンツを SharePoint Online に移行する

Azure Data Box Heavy と SharePoint 移行ツール (SPMT) を使用すると、ファイル共有のコンテンツを簡単に SharePoint Online と OneDrive に移行できます。 Data Box Heavy を使用することで、ワイド エリア ネットワーク (WAN) リンク経由でデータを転送する必要がなくなります。

Microsoft Azure Data Box は、Microsoft Azure portal からデバイスを注文できるサービスです。 お客様は、サーバーからデバイスにテラバイトのデータをコピーできます。 それを Microsoft に返送いただくと、お客様のデータが Azure にコピーされます。 転送するデータのサイズに応じて、以下から選択できます。

- [Data Box Disk](./data-box-disk-overview.md) は小規模から中規模のデータセット向けで、注文 1 件につき 35 TB の容量を使用できます。
- [Data Box](./data-box-overview.md) は中規模から大規模のデータセット向けで、デバイス 1 台につき 80 TB の容量を使用できます。
- [Data Box Heavy](./data-box-heavy-overview.md) は大規模のデータセット向けで、デバイス 1 台につき 770 TB の容量を使用できます。

この記事では、特に Data Box Heavy を使用してファイル共有のコンテンツを SharePoint Online に移行する方法について説明します。

## <a name="requirements-and-costs"></a>要件と費用

### <a name="for-data-box-heavy"></a>Data Box Heavy

- Data Box Heavy は、マイクロソフト エンタープライズ契約 (EA)、クラウド ソリューション プロバイダー (CSP)、または Azure スポンサー プランでのみ利用できます。 サブスクリプションが上記のいずれのタイプにも該当しない場合は、Microsoft サポートに連絡してサブスクリプションをアップグレードするか、[Azure サブスクリプションの価格](https://azure.microsoft.com/pricing/)をご確認ください。
- Data Box Heavy のご利用には料金がかかります。 必ず [Data Box Heavy の価格](https://azure.microsoft.com/pricing/details/databox/heavy/)をご確認ください。


### <a name="for-sharepoint-online"></a>SharePoint Online

- [SharePoint 移行ツール (SPMT) の最小要件](/sharepointmigration/how-to-use-the-sharepoint-migration-tool)をご確認ください。

## <a name="workflow-overview"></a>ワークフローの概要

このワークフローでは、Azure Data Box Heavy と SharePoint Online の両方で手順を行う必要があります。
次の手順は Azure Data Box Heavy に関連しています。

1. Azure Data Box Heavy を注文します。
2. デバイスを受け取って設定します。
3. オンプレミスのファイル共有から、デバイス上の Azure Files 用のフォルダーにデータをコピーします。
4. コピーが完了したら、指示に従ってデバイスを返送します。
5. データが完全に Azure にアップロードされるまで待ちます。

次の手順は SharePoint Online に関連しています。

6. Azure portal で VM を作成し、そこに Azure ファイル共有をマウントします。
7. Azure VM に SPMT ツールをインストールします。
8. Azure ファイル共有を "*ソース*" として使用して SPMT ツールを実行します。
9. ツールの最後の手順を完了します。
10. データを確認します。

## <a name="use-data-box-heavy-to-copy-data"></a>Data Box Heavy を使用してデータをコピーする

Data Box Heavy にデータをコピーするには、次の手順を実行します。

1. [Data Box Heavy を注文します](data-box-heavy-deploy-ordered.md)。
2. Data Box Heavy を受け取ったら、[Data Box Heavy を設定](data-box-heavy-deploy-set-up.md)します。 ケーブルを接続し、デバイス上の両方のノードを構成します。
3. [Azure Data Box Heavy にデータをコピーします](data-box-heavy-deploy-copy-data.md)。 コピーの際は、以下の点に留意してください。

    - データをコピーする際は、Data Box Heavy の *StorageAccountName_AzFile* フォルダーのみを使用します。 これは、データの最終的な場所がブロック BLOB やページ BLOB ではなく Azure ファイル共有だからです。
    - *StorageAccountName_AzFile* フォルダー内のフォルダーにファイルをコピーします。 *StorageAccountName_AzFile* フォルダー内のサブフォルダーでファイル共有が作成されます。 *StorageAccountName_AzFile* フォルダーに直接コピーされたファイルは失敗し、ブロック BLOB としてアップロードされます。 これが、次の手順で VM にマウントするファイル共有です。
    - Data Box Heavy の両方のノードにデータをコピーします。
3. デバイスで [[発送準備]](data-box-heavy-deploy-picked-up.md#prepare-to-ship) を実行します。 発送準備が成功すれば、Azure へのファイルのアップロードも成功します。
4. [デバイスを返送します](data-box-heavy-deploy-picked-up.md#ship-data-box-heavy-back)。
5. [Azure にデータがアップロードされたことを確認します](data-box-heavy-deploy-picked-up.md#verify-data-upload-to-azure)。

## <a name="use-spmt-to-migrate-data"></a>SPMT を使用してデータを移行する

データのコピーが完了したことを知らせる通知が Azure データ チームから届いたら、SharePoint Online へのデータの移行に進みます。

最適なパフォーマンスと接続性を確保するため、Azure 仮想マシン (VM) を作成することをお勧めします。

1. Azure portal にサインインし、[仮想マシンを作成](../virtual-machines/windows/quick-create-portal.md)します。
2. [Azure ファイル共有を VM にマウントします](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share-with-file-explorer)。
3. [SharePoint 移行ツールをダウンロード](https://spmtreleasescus.blob.core.windows.net/install/default.htm)し、Azure VM にインストールします。
4. SharePoint 移行ツールを起動します。 **[サインイン]** をクリックし、職場または学校のアカウントのユーザー名とパスワードを入力します。
5. **[データはどこにありますか?]** が表示されたら、 **[ファイル共有]** を選択します。 データが置かれている Azure ファイル共有のパスを入力します。
6. 通常どおり残りの指示に従います (ターゲットの場所など)。 詳細については、「[SharePoint 移行ツールの使用](/sharepointmigration/how-to-use-the-sharepoint-migration-tool)」をご覧ください。

> [!IMPORTANT]
> - SharePoint Online にデータが取り込まれる速度は、データが既に Azure にあるかどうかに関係なく、さまざまな要因の影響を受けます。 これらの要因を理解することが、移行を計画し、その効率を最大化するのに役立ちます。  詳細については、[SharePoint Online と OneDrive の移行速度](/sharepointmigration/sharepoint-online-and-onedrive-migration-speed)に関する記事をご覧ください。
> - SharePoint Online にデータを移行すると、ファイルの既存のアクセス許可が失われるおそれがあります。 また、"*作成者*" や "*変更日*" などの特定のメタデータが失われる可能性もあります。

## <a name="next-steps"></a>次のステップ

[Data Box Heavy を注文する](./data-box-heavy-deploy-ordered.md)