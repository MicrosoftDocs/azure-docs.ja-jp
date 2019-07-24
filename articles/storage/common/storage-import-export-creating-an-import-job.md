---
title: Azure Import/Export のインポート ジョブを作成する | Microsoft Docs
description: Microsoft Azure Import/Export サービスのインポート ジョブを作成する方法について説明します。
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: fa76f4fb5d4da5fd00bb9fa4ed862c6977a47e90
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58102181"
---
# <a name="creating-an-import-job-for-the-azure-importexport-service"></a>Azure Import/Export サービスのインポート ジョブの作成

REST API を使用して Microsoft Azure Import/Export サービスのインポート ジョブを作成するには、次の手順を実行します。

- Azure Import/Export ツールを使ってドライブを準備する。

- ドライブの発送先となる場所を取得する。

- インポート ジョブを作成する。

- サポートされている運送サービスを経由して、Microsoft にドライブを送付する。

- インポート ジョブの発送詳細を更新する。

  Import/Export サービスの概要については、「[Microsoft Azure Import/Export サービスを使用した Blob Storage へのデータの転送](storage-import-export-service.md)」を参照してください。[Azure ポータル](https://portal.azure.com/)を使用してインポート ジョブやエクスポート ジョブを作成および管理する方法も説明されています。

## <a name="preparing-drives-with-the-azure-importexport-tool"></a>Azure Import/Export ツールを使用してドライブを準備する

インポート ジョブのドライブを準備する手順は、ジョブの作成にポータルを使用する場合でも、REST API を使用する場合でも同じです。

次に示すのは、ドライブ準備作業の概要です。 詳しい手順については、「[Azure Import-Export ツールの参照](storage-import-export-tool-how-to-v1.md)」をご覧ください。 Azure Import/Export ツールは、[こちら](https://go.microsoft.com/fwlink/?LinkID=301900)からダウンロードできます。

ドライブの準備には次の手順が含まれます。

- インポートするデータを特定する。

- Windows Azure Storage 内のインポート先 BLOB を特定する。

- Azure Import/Export ツールを使用して、1 台または複数のハード ドライブにデータをコピーする。

  Azure Import/Export ツールでは、ドライブの準備中に各ドライブのマニフェスト ファイルも生成されます。 マニフェスト ファイルには次の情報が含まれています。

- アップロードするすべてのファイルの列挙体と、それらのファイルから BLOB へのマッピング。

- 各ファイルのセグメントのチェックサム。

- 各 BLOB に関連付けるメタデータとプロパティについての情報。

- アップロードされる BLOB の名前がコンテナー内の既存の BLOB と同じであった場合に実行されるアクションの一覧。 可能なオプションは次のとおりです: a) 当該のファイルで BLOB を上書きする、b) 既存の BLOB を維持し、ファイルのアップロードをスキップする、c) 名前にサフィックスを追加して、他のファイルとの競合を回避する。

## <a name="obtaining-your-shipping-location"></a>送付先を取得する

インポート ジョブを作成する前に、[List Locations](https://docs.microsoft.com/rest/api/storageimportexport/locations/list) 操作を呼び出して、送付先の名前と住所を取得する必要があります。 `List Locations` は、送付先と住所の一覧とを返します。 返された一覧から送付先を選択し、その住所宛てにハードドライブを発送します。 `Get Location` 操作を使用して、特定の送付先の住所を取得することもできます。

 次の手順に従って、送付先を取得します。

-   ストレージ アカウントの場所名を特定します。 この値は、Azure Portal でストレージ アカウントの**ダッシュボード**の **[場所]** フィールドを見て確認するか、サービス管理 API の [Get Storage Account Properties](/rest/api/storagerp/storageaccounts) 操作を使用して照会することができます。

-   `Get Location` 操作を呼び出して、このストレージ アカウントを処理できる場所を取得します。

-   場所の `AlternateLocations` プロパティにその場所自体が含まれていれば、その場所を使用できます。 そうでない場合は、別の場所を使用して `Get Location` 操作をもう一度呼び出します。 保守作業のために、元の場所が一時的に閉鎖されている場合もあります。

## <a name="creating-the-import-job"></a>インポート ジョブを作成する
インポート ジョブを作成するには、[Put Job](/rest/api/storageimportexport/jobs) 操作を呼び出します。 次の情報を指定する必要があります。

-   ジョブの名前。

-   ストレージ アカウント名。

-   発送先名 (前の手順で取得します)。

-   ジョブ タイプ (インポート)。

-   インポート ジョブの完了後にドライブが返送される際の返送先住所。

-   ジョブに含まれるドライブの一覧。 各ドライブについて、ドライブの準備中に取得した次の情報を含める必要があります。

    -   ドライブ ID

    -   BitLocker キー

    -   ハード ドライブ上のマニフェスト ファイルの相対パス

    -   Base16 でエンコードされたマニフェスト ファイルの MD5 ハッシュ

## <a name="shipping-your-drives"></a>ドライブを発送する
前の手順で取得した住所にドライブを発送し、パッケージの追跡番号を Import/Export サービスに通知する必要があります。

> [!NOTE]
>  ドライブは、サポートされている配送サービスを通じて発送する必要があります。そうすることで、パッケージの追跡番号が提供されます。

## <a name="updating-the-import-job-with-your-shipping-information"></a>インポート ジョブの発送情報を更新する
追跡番号を入手したら、[Update Job Properties](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Update) 操作を呼び出して、ジョブの配送業者名、追跡番号、および返送時の配送業者アカウント番号を更新します。 オプションで、ドライブ数、および発送日を指定することもできます。

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>次の手順

* [Import/Export サービス REST API の使用](storage-import-export-using-the-rest-api.md)
