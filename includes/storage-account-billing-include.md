---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/19/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6ad5bafe66e6e39503d3987aa6ddd9159c532fa0
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72237390"
---
Azure Storage は、ストレージ アカウントに基づいた使用量によって課金されます。 ストレージ アカウント内のすべてのオブジェクトは、グループとしてまとめて課金されます。 

ストレージのコストは、リージョン/場所、アカウントの種類、アクセス層、ストレージ容量、レプリケーション スキーム、ストレージ トランザクション、および送信データ転送に従って計算されます。

* **リージョン**とは、自分のアカウントがベースとしている地理的領域を指します。
* **アカウントの種類**とは、使用するストレージ アカウントの種類を指します。 
* **アクセス層**とは、汎用 v2 または BLOB ストレージ アカウント用に指定したデータの使用状況パターンを指します。
* **ストレージ容量**とは、データの保存に使用するためにストレージ アカウントに割り当てられた容量を指します。
* **レプリケーション**は、1 回に保持するデータのコピー数とデータの場所を決定します。
* **トランザクション**とは、Azure Storage に対するすべての読み取り操作と書き込み操作を指します。
* **データ転送**とは、Azure リージョンから転送されるデータを指します。 他のリージョンで動作しているアプリケーションがストレージ アカウント内のデータにアクセスした場合は、送信データ転送として課金されます リソース グループを使用してデータとサービスを同じリージョンにグループ化してエグレス料金を制限する方法については、「[Azure リソース グループとは](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group)」を参照してください。 

アカウントの種類、ストレージ容量、レプリケーション、およびトランザクションに基づく詳細な料金情報については、「 [Azure Storage 料金](https://azure.microsoft.com/pricing/details/storage/) 」ページを参照してください。 送信データ転送の価格の詳細については、「 [データ転送の価格の詳細](https://azure.microsoft.com/pricing/details/data-transfers/) 」を参照してください。 コストの見積には、 [計算ツール](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) をご利用ください。

