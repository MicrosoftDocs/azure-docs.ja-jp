---
title: Azure API for FHIR でデータベース設定を構成する
description: この記事では、Azure API for FHIR でデータベース設定を構成する方法について説明します
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/15/2019
ms.author: matjazl
ms.openlocfilehash: 3ce51141f20d2e032cb255f53efe3cb63882f1b2
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018712"
---
# <a name="configure-database-settings"></a>データベース設定を構成する 

Azure API for FHIR では、データベースを使用してデータを格納します。 基になるデータベースのパフォーマンスは、サービスのプロビジョニング中に選択された要求ユニット (RU) の数、またはサービスがプロビジョニングされた後のデータベース設定によって異なります。

Azure API for FHIR では、基になるデータベースのパフォーマンスを設定する際、Cosmos DB の RU の概念が採用されています (「[Azure Cosmos DB の要求ユニット](../../cosmos-db/request-units.md)」を参照)。 

データベースで常に十分なシステム リソースを確実に使用できるようにするには、スループットをプロビジョニングする必要があります。 アプリケーションに必要な RU の数は、実行する操作によって異なります。 操作の範囲は、単純な読み取りと書き込みから、より複雑なクエリにまで及びます。 

> [!NOTE]
> 消費される RU 数は操作によって異なるため、各 API 呼び出しで、実際に消費された RU 数が応答ヘッダーで返されます。 このようにして、アプリケーションによって消費された RU 数をプロファイリングすることができます。

## <a name="update-throughput"></a>スループットを更新する

この設定を Azure portal で変更するには、Azure API for FHIR に移動して [データベース] ブレードを開きます。 次に、パフォーマンス要件に応じて [Provisioned throughput]\(プロビジョニング スループット\) を適切な値に設定します。 設定できる値の上限は 10,000 RU/秒です。 それよりも大きな値が必要な場合は、Azure サポートにお問い合わせください。

データベースのスループットが 10,000 RU/秒を超える場合、またはデータベースの格納データが 50 GB を超えている場合は、お使いのクライアント アプリケーションで継続トークンを処理できる必要があります。 スループットが 10,000 RU/秒増加するたび、または格納データ量が 50 GB を超えた場合に、データベースに新しいパーティションが作成されます。 パーティションが複数ある場合、継続トークンを使用して改ページされるマルチページ応答が作成されます。

> [!NOTE] 
> 値が高いほど、Azure API for FHIR のスループットが高く、サービスのコストが高いことを意味します。

![Cosmos DB を構成する](media/database/database-settings.png)

## <a name="next-steps"></a>次のステップ

この記事では、Azure API for FHIR の RU 数を更新する方法について説明しました。 顧客管理キーをデータベース設定として構成する方法の詳細については、次を参照してください。

>[!div class="nextstepaction"]
>[顧客管理キーの構成](customer-managed-key.md)

または、フル マネージド Azure API for FHIR をデプロイすることもできます。
 
>[!div class="nextstepaction"]
>[Azure API for FHIR をデプロイする](fhir-paas-portal-quickstart.md)