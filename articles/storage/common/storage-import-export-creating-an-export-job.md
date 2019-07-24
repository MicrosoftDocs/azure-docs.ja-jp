---
title: Azure Import/Export のエクスポート ジョブを作成する | Microsoft Docs
description: Microsoft Azure Import/Export サービスのエクスポート ジョブを作成する方法について説明します。
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: e0513bc18f1cf14beb4c1becfc1835235a5ddc96
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58092227"
---
# <a name="creating-an-export-job-for-the-azure-importexport-service"></a>Azure Import/Export サービスのエクスポート ジョブの作成
REST API を使用して Microsoft Azure Import/Export サービスのエクスポート ジョブを作成するには、次の手順を実行します。

- エクスポートする BLOB の選択。

- 送付先の取得。

- エクスポート ジョブの作成。

- サポートされている運送サービスを経由して、Microsoft に空のドライブを送付する。

- エクスポート ジョブのパッケージ情報を更新する。

- Microsoft から返送されたドライブを受け取る。

  Import/Export サービスの概要については、「[Using the Windows Azure Import/Export service to Transfer Data to Blob Storage (Windows Azure Import/Export サービスを使用した Blob Storage へのデータの転送)](storage-import-export-service.md)」を参照してください。[Azure ポータル](https://portal.azure.com/)を使用してインポート ジョブやエクスポート ジョブを作成および管理する方法も説明されています。

## <a name="selecting-blobs-to-export"></a>エクスポートする BLOB を選択する
 エクスポート ジョブを作成するには、ストレージ アカウントからエクスポートする BLOB の一覧を提供する必要があります。 エクスポートする BLOB の選択方法は複数あります。

- BLOB の相対パスを使用して、1 つの BLOB とそのすべてのスナップショットを選択する。

- BLOB の相対パスを使用して、1 つの BLOB (スナップショットを除く) を選択する。

- BLOB の相対パスとスナップショット時刻を使用して、1 つのスナップショットを選択する。

- BLOB のプレフィックスを使用して、特定のプレフィックスを持つすべての BLOB とスナップショットを選択する。

- ストレージ アカウント内のすべての BLOB とスナップショットをエクスポートする。

  エクスポートする BLOB の指定方法について詳しくは、[Put Job](/rest/api/storageimportexport/jobs) をご覧ください。

## <a name="obtaining-your-shipping-location"></a>送付先を取得する
エクスポート ジョブを作成する前に、[Get Location](https://portal.azure.com) または [List Locations](https://docs.microsoft.com/rest/api/storageimportexport/locations/list) 操作を呼び出して、送付先の名前と住所を取得する必要があります。 `List Locations` は、送付先と住所の一覧とを返します。 返された一覧から送付先を選択し、その住所宛てにハードドライブを発送します。 `Get Location` 操作を使用して、特定の送付先の住所を取得することもできます。

次の手順に従って、送付先を取得します。

-   ストレージ アカウントの場所名を特定します。 この値は、Azure Portal でストレージ アカウントの**ダッシュボード**の **[場所]** フィールドを見て確認するか、サービス管理 API の [Get Storage Account Properties](/rest/api/storagerp/storageaccounts) 操作を使用して照会することができます。

-   `Get Location` 操作を呼び出して、このストレージ アカウントを処理できる場所を取得します。

-   場所の `AlternateLocations` プロパティにその場所自体が含まれていれば、その場所を使用できます。 そうでない場合は、別の場所を使用して `Get Location` 操作をもう一度呼び出します。 保守作業のために、元の場所が一時的に閉鎖されている場合もあります。

## <a name="creating-the-export-job"></a>エクスポート ジョブを作成する
 エクスポート ジョブを作成するには、[Put Job](/rest/api/storageimportexport/jobs) 操作を呼び出します。 次の情報を指定する必要があります。

-   ジョブの名前。

-   ストレージ アカウント名。

-   発送先名 (前の手順で取得します)。

-   ジョブ タイプ (エクスポート)。

-   エクスポート ジョブの完了後にドライブが返送される際の返送先住所。

-   エクスポートする BLOB (または BLOB プレフィックス) の一覧。

## <a name="shipping-your-drives"></a>ドライブを発送する
 次に、Azure Import/Export ツールを使用して、送付する必要があるドライブの数を決定します。これは、エクスポートの対象として選択した BLOB と、ドライブのサイズに基づいて決定します。 詳しくは、「[Azure Import-Export ツールの参照](storage-import-export-tool-how-to-v1.md)」をご覧ください。

 ドライブを 1 つのパッケージに梱包し、前の手順で取得した住所に発送します。 次の手順のために、パッケージの追跡番号をメモしておきます。

> [!NOTE]
>  ドライブは、サポートされている配送サービスを通じて発送する必要があります。そうすることで、パッケージの追跡番号が提供されます。

## <a name="updating-the-export-job-with-your-package-information"></a>エクスポート ジョブのパッケージ情報を更新する
 追跡番号を入手したら、[Update Job Properties](/rest/api/storageimportexport/jobs) 操作を呼び出して、ジョブの配送業者名や追跡番号を更新します。 オプションで、ドライブ数、返送先住所、および発送日を指定することもできます。

## <a name="receiving-the-package"></a>パッケージを受け取る
 エクスポート ジョブの処理が完了したら、データが暗号化された状態で、ドライブが返送されます。 お客様は、[Get Job](/rest/api/storageimportexport/jobs) 操作呼び出すことで各ドライブの BitLocker キーを取得できます。 その後、そのキーを使用してドライブのロックを解除できます。 各ドライブのドライブ マニフェスト ファイルには、ドライブ上のファイルの一覧と、各ファイルの元の BLOB アドレスが含まれています。

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>次の手順

* [Import/Export サービス REST API の使用](storage-import-export-using-the-rest-api.md)
