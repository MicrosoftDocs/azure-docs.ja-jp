---
title: Microsoft Azure Data Box Gateway のユース ケース | Microsoft Docs
description: Azure にデータを転送できる仮想アプライアンス ストレージ ソリューションである、Azure Data Box Gateway のユース ケースについて説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: 3bf137f968082e677f45c20947793232b9181220
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98786614"
---
# <a name="use-cases-for-azure-data-box-gateway"></a>Azure Data Box Gateway のユース ケース

Azure Data Box Gateway は、お客様のオンプレミスに存在し、お客様のイメージ、メディア、およびその他のデータを Azure に送信するクラウド ストレージ ゲートウェイ デバイスです。 このクラウド ストレージ ゲートウェイは、お客様のハイパーバイザー内にプロビジョニングされた仮想マシンです。 NFS プロトコルおよび SMB プロトコルを使用して仮想デバイスにデータを記述します。そして、それが Azure に送信されます。 この記事では、このデバイスをデプロイできるシナリオについて詳しく説明します。

Data Box Gateway は次のシナリオに使用します。

- 大量のデータを継続的に取り込むため。
- データの安全で効率的なクラウド アーカイブのため。
- Data Box を使用した初期一括転送の完了後における、ネットワーク経由の増分データ転送のため。

これらの各シナリオについて、以降のセクションで詳しく説明します。


## <a name="continuous-data-ingestion"></a>継続的なデータ インジェスト

Data Box Gateway の主な利点の 1 つは、データ サイズに関係なく、データをデバイスに継続的に取り込んで、クラウドにコピーできることです。

ゲートウェイ デバイスにデータが書き込まれると、デバイスによってデータが Azure Storage にアップロードされます。 デバイスでは、特定のしきい値に達するとファイルがローカルで削除され、メタデータが保持されることで、ストレージが自動的に管理されます。 メタデータのローカル コピーが保持されるため、ファイルがアップデートされた際に変更のみがゲートウェイ デバイスによってアップロードされます。 お客様のゲートウェイ デバイスにアップロードされたデータには、「[データのアップロードに関する注意事項](data-box-gateway-limits.md#data-upload-caveats)」にあるガイドラインが適用されます。

デバイスのデータがいっぱいになると、データがクラウドにアップロードされるレートに一致するように、(必要に応じて) 受信レートの調整が開始されます。 デバイス上の継続的なインジェストを監視するには、アラートを使用します。 これらのアラートは、調整が開始されると発生し、調整が停止されるとクリアされます。

## <a name="cloud-archival-of-data"></a>データのクラウド アーカイブ

お客様のデータをクラウドで長期的に保持したい場合、Data Box Gateway を使用します。 長期保有にはストレージのアーカイブ層を使用できます。

アーカイブ層は、ほとんどアクセスされることのないデータを 180 日間以上格納するために最適化されています。 アーカイブ層は、ストレージ コストが最も低くなりますが、アクセス コストが最も高くなります。 詳細については、「[アーカイブ アクセス層](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier)」を参照してください。

### <a name="move-data-to-the-archive-tier"></a>データをアーカイブ層に移動する

開始する前に、Data Box Gateway デバイスが動作していることを確認します。 [Azure Data Box Gateway のデプロイの準備に関するチュートリアル](data-box-gateway-deploy-prep.md)で詳しく説明されている手順に従って、運用デバイスが用意できるまで次のチュートリアルに進みます。

- [Data Box Gateway 経由のデータ転送](data-box-gateway-deploy-add-shares.md)に関するページで説明されている通常の転送手順を通じて、Data Box Gateway デバイスを使用してデータを Azure にアップロードします。
- データがアップロードされた後、それをアーカイブ層に移動する必要があります。 BLOB 層の設定には 2 つの方法があり、Azure PowerShell スクリプトまたは Azure Storage ライフサイクル管理ポリシーを使用します。  
    - Azure PowerShell を使用する場合、次の[手順](../databox/data-box-how-to-set-data-tier.md#use-azure-powershell-to-set-the-blob-tier)に従って、データをアーカイブ層に移動します。
    - Azure ライフサイクル管理を使用する場合、次の手順に従って、データをアーカイブ層に移動します。
        - アーカイブ層を使用するには、BLOB ライフサイクル管理サービスのプレビューに[登録](../storage/blobs/storage-lifecycle-management-concepts.md)します。
        - 次のポリシーを使用して、[取り込み時にデータをアーカイブ](../storage/blobs/storage-lifecycle-management-concepts.md#archive-data-after-ingest)します。
- BLOB は、アーカイブとしてマークされると、ホット層またはコールド層に移動されない限り、ゲートウェイによる変更ができなくなります。 ファイルがローカル ストレージにある場合、ローカル コピーに対して行われる変更 (削除を含む) は、アーカイブ層にアップロードされません。
- アーカイブ ストレージ内のデータを読み取るには、BLOB 層をホットかクールに変更して、データをリハイドレートする必要があります。 ゲートウェイ上の[共有を更新](data-box-gateway-manage-shares.md#refresh-shares)しても、BLOB はリハイドレートされません。

詳細については、[Azure Blob Storage のライフサイクルを管理する方法](../storage/blobs/storage-lifecycle-management-concepts.md)をご確認ください。

## <a name="initial-bulk-transfer-followed-by-incremental-transfer"></a>増分転送に先立つ初期一括転送

増分転送に先立って大量のデータの一括アップロードを行いたい場合には、Data Box と Data Box Gateway を一緒に使用します。 オフライン モードでの一括転送 (初期シード) に Data Box を使用し、ネットワーク経由の増分転送 (継続的なフィード) に Data Box Gateway を使用します。

### <a name="seed-the-data-with-data-box"></a>Data Box を使用してデータをシードする

以下の手順に従って、データを Data Box にコピーして Azure Storage にアップロードします。

1. [お客様の Data Box を注文します](../databox/data-box-deploy-ordered.md)。
2. [お客様の Data Box を設定します](../databox/data-box-deploy-set-up.md)。
3. [SMB 経由でデータを Data Box にコピーします](../databox/data-box-deploy-copy-data.md)。
4. [Data Box を返送し、Azure へのデータ アップロードを確認します](../databox/data-box-deploy-picked-up.md)。
5. Azure へのデータ アップロードが完了したら、すべてのデータは Azure ストレージ コンテナー内にあるはずです。 Data Box 用のストレージ アカウントで、BLOB (およびファイル) コンテナーに移動して、すべてのデータがコピーされたことを確認します。 コンテナー名をメモしておきます。これは、後でこの名前を使用するためです。 たとえば、次のスクリーンショットでは、`databox` コンテナーが増分転送に使用されます。

    ![Data Box 上のデータが含まれたコンテナー](media/data-box-gateway-use-cases/data-container.png)

この一括転送により、初期シード段階が完了します。

### <a name="ongoing-feed-with-data-box-gateway"></a>Data Box Gateway を使用した継続的なフィード

Data Box Gateway を使用して継続的なインジェストを行うには、以下の手順に従います。 

1. Data Box Gateway 上にクラウド共有を作成します。 この共有から、データが Azure ストレージ アカウントに自動的にアップロードされます。 お客様の Data Box Gateway リソースの **[共有]** に移動して、 **+ [共有の追加]** をクリックします。

    ![\+ [共有の追加] のクリック](media/data-box-gateway-use-cases/add-share.png)

2. この共有が、シード済みのデータを含むコンテナーにマップされるようにします。 **[BLOB コンテナーの選択]** で、 **[既存のものを使用]** を選択し、Data Box からのデータが転送されたコンテナーを参照します。

    ![共有の設定](media/data-box-gateway-use-cases/share-settings-select-existing-container.png)

3. 共有が作成された後、共有を更新します。 この操作により、オンプレミスの共有が Azure からの内容で更新されます。

    ![共有の更新](media/data-box-gateway-use-cases/refresh-share.png)

    共有が同期されると、ファイルがクライアント上で変更された場合に、Data Box Gateway によって増分変更がアップロードされます。

## <a name="next-steps"></a>次のステップ

- [Data Box Gateway のシステム要件](data-box-gateway-system-requirements.md)を確認する。
- [Data Box Gateway の制限事項](data-box-gateway-limits.md)を理解する。
- Azure portal で [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) を配置する。