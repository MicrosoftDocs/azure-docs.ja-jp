---
title: Azure Government Storage | Microsoft Docs
description: "Azure Government アプリケーションの機能の比較と開発におけるガイダンスを示します。"
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: 83df022b-d791-4efb-9fdf-8afe47a885d5
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/22/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 831334f2b835d00aa3fd22292764b69e85d735a6
ms.openlocfilehash: a6b61df5884031eb5b53f983b834357bd23a622e
ms.lasthandoff: 02/08/2017


---
# <a name="azure-government-storage"></a>Azure Government ストレージ
## <a name="azure-storage"></a>Azure Storage (Azure Storage)
このサービスとその使用方法について詳しくは、[Azure Storage のパブリック ドキュメント](../storage/index.md)をご覧ください。

### <a name="storage-service-availability-by-azure-government-region"></a>Azure Government リージョン別ストレージ サービスの可用性

| サービス | 米国政府バージニア州 | 米国政府アイオワ州 | メモ
| --- | --- | --- | --- |
| [Blob Storage] (../storage/storage-introduction.md#blob-storage) |一般公開 |一般公開 |
| [Table Storage] (../storage/storage-introduction.md#table-storage) |一般公開  |一般公開 |
| [Queue Storage] (../storage/storage-introduction.md#queue-storage) |一般公開 | 一般公開 |
| [File Storage] (../storage/storage-introduction.md#file-storage) |一般公開 |一般公開 |
| [Hot/Cool Blob Storage] (../storage/storage-blob-storage-tiers.md) |該当なし |該当なし |
| [ストレージ サービスの暗号化] (../storage/storage-service-encryption.md) |一般公開 |一般公開 |
| [Premium Storage] (../storage/storage-premium-storage.md) |一般公開 |該当なし | DS シリーズの Virtual Machines を含みます。 |
| [BLOB のインポート/エクスポート] (../storage/storage-import-export-service.md) |一般公開 |一般公開 |
| [StorSimple] (../storsimple/storsimple-ova-overview.md) |一般公開 |一般公開 |

### <a name="variations"></a>バリエーション
Azure Government では、ストレージ アカウントの URL が異なります。

| サービスの種類 | Azure Public | Azure Government |
| --- | --- | --- |
| BLOB ストレージ |*.blob.core.windows.net |*.blob.core.usgovcloudapi.net |
| キュー ストレージ |*.queue.core.windows.net |*.queue.core.usgovcloudapi.net |
| Table Storage |*.table.core.windows.net |*.table.core.usgovcloudapi.net |
| File Storage |*.file.core.windows.net |*.file.core.usgovcloudapi.net | 

> [!NOTE]
> スクリプトやコードでは必ず、適切なエンドポイントを考慮する必要があります。  「[Azure Storage の接続文字列を構成する](../storage/storage-configure-connection-string.md)」を参照してください。 
>
>

API の詳細については、<a href="https://msdn.microsoft.com/en-us/library/azure/mt616540.aspx">クラウド ストレージ アカウント コンストラクター</a>に関するページを参照してください。

これらのオーバーロードに使用するエンドポイント サフィックスは、core.usgovcloudapi.net です。

> [!NOTE]
> [Microsoft Azure Storage Explorer] (../vs-azure-tools-storage-manage-with-storage-explorer.md) は、現時点では、Azure Government へのアカウントの追加による [サブスクリプションへの接続] (../vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-an-azure-subscription) はサポートしていません。 その他の [ストレージ アカウントへの接続] (../vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service) 方法を使用してください。
[外部ストレージ アカウントに接続する] (../vs-azure-tools-storage-manage-with-storage-explorer.md#attach-to-an-external-storage-account) 場合は、**手順 3** で、ストレージ エンドポイント ドメインとして **[その他 (以下に指定してください)]** を選択し、Azure Government 用の「**core.usgovcloudapi.net**」を指定します。
>
>

> [!NOTE]
> エラー 53 "ネットワーク パスが見つかりませんでした。" が返された ([ファイル共有のマウント中] (../storage/storage-dotnet-how-to-use-files.md#mount-the-file-share)) 場合は、 ファイアウォールによって送信ポートがブロックされている可能性があります。 ストレージ アカウントと同じ Azure サブスクリプション内の VM でのファイル共有のマウントを試してください。
>
>

> [!NOTE]
> StorSimple Manager Service をデプロイするときは、https://portal.azure.us/ と https://manage.windowsazure.us/ を使用してください。前者は Azure Portal の、後者はクラシック ポータルの URL です。 StorSimple Virtual Array のデプロイについては、[StorSimple Virtual Array のシステム要件] (../storsimple/storsimple-ova-system-requirements.md) を、StorSimple 8000 シリーズについては、[StorSimple ソフトウェアの高可用性とネットワーク要件] (../storsimple/storsimple-system-requirements.md) を参照し、左側のナビゲーションから「デプロイ」セクションに移動してください。 StorSimple の全般的なドキュメントについては、[StorSimple とは何か?] (../storsimple/index.md) を参照してください。
>
>

### <a name="considerations"></a>考慮事項
以下の情報は、Azure Storage に関する Azure Government の機能領域について記述したものです。

| 許可される規制対象データ | 許可されない規制対象データ |
| --- | --- |
| Azure Storage 製品内で入力、保存、処理されるデータには、輸出規制対象データが含まれていてもかまいません。 静的な認証コード (Azure のプラットフォーム コンポーネントにアクセスするためのパスワード、スマートカードの PIN など)。 Azure のプラットフォーム コンポーネントを管理する際に使用する証明書の秘密キー。 その他、Azure サービスに格納される各種セキュリティ情報/シークレット (証明書、暗号化キー、マスター キー、ストレージ キーなど)。 |Azure Storage のメタデータに、輸出規制対象データを含めることは許可されません。 このメタデータには、ストレージ製品を作成したり管理したりする際に入力するあらゆる構成データが含まれます。  リソース グループ、デプロイ名、リソース名、リソース タグの各フィールドには規制対象データを入力しないでください。 |

## <a name="next-steps"></a>次のステップ
補足情報と最新情報については、<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government ブログ</a>を講読してください。

