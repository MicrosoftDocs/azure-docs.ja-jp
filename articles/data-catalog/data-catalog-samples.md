---
title: Azure Data Catalog 開発者向けサンプル
description: この記事では、Data Catarog REST API で使用可能な開発者向けサンプルの概要を説明します。
ms.service: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 15b48bc41e230ca5b9003675e2caab25741bcbfd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674769"
---
# <a name="azure-data-catalog-developer-samples"></a>Azure Data Catalog 開発者向けサンプル

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Data Catalog REST API を使用して Azure Data Catalog アプリの開発を開始します。 Data Catalog REST API は、データ資産をプログラムで登録、注釈付けおよび検索するために Data Catalog のリソースにプログラムでアクセスできるようにする、REST ベースの API です。

## <a name="samples-available-on-githubcom"></a>GitHub.com で入手できるサンプル

* [Azure Data Catalog の概要](https://github.com/Azure-Samples/data-catalog-dotnet-get-started/)
  
   この入門サンプルでは、Data Catalog REST API を使用してデータ資産を登録、検索、削除するために Azure AD で認証を行う方法を紹介しています。
   
* [サービス プリンシパルを使用した Azure Data Catalog の概要](https://github.com/Azure-Samples/data-catalog-dotnet-service-principal-get-started/)

   このサンプルでは、Data Catalog REST API を使用してデータ資産を登録、検索、削除する方法を紹介しています。 このサンプルでは、サービス プリンシパルの認証を使用しています。

* [Azure Data Catalog のインポート/エクスポート ツール](https://github.com/Azure-Samples/data-catalog-dotnet-import-export/)

   このサンプルでは、Data Catalog REST API を使用して Azure Data Catalog からアセットを取得し、ファイルにシリアル化する方法を紹介しています。 また、JSON としてシリアル化されたアセットのセットを取得して、それらをカタログにプッシュする方法も示します。 検索クエリを使用したカタログのサブセットのエクスポートをサポートします。

* [Azure Data Catalog での一括登録および注釈付け](https://github.com/Azure-Samples/data-catalog-dotnet-excel-register-data-assets/)
  
   このサンプルでは、Data Catalog REST API と Open XML を使用して、Excel ブックからデータ資産を一括で登録する方法を紹介しています。
  
* [用語集から Azure Data Catalog への用語の一括インポート](https://github.com/Azure-Samples/data-catalog-bulk-import-glossary/)

   このサンプルでは、CSV ファイルから ADC 用語集に用語集の用語をインポートする方法を示します。

* [Azure Data Catalog へのリレーションシップの一括インポート](https://github.com/Azure-Samples/data-catalog-bulk-import-relationship/)

   このサンプルでは、プログラムによって CSV ファイルからデータ カタログにリレーションシップ情報をインポートする方法を紹介しています。

* [Azure Data Catalog へのリレーションシップの発行](https://github.com/Azure-Samples/data-catalog-dotnet-publish-relationships/)

   このサンプルでは、プログラムによってデータ カタログにリレーションシップ情報を発行する方法を紹介しています。
   
## <a name="next-steps"></a>次のステップ
[Azure Data Catalog REST API リファレンス](/rest/api/datacatalog/)
