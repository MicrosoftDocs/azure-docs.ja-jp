---
title: Azure Security Center でのデータ サービスの脅威検出 | Microsoft Docs
description: このトピックでは、Azure Security Center で使用可能なデータ サービスのアラートについて説明します。
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/24/2019
ms.author: v-mohabe
ms.openlocfilehash: 8812ac325e6bfd5ee019f6ddd6bf86c846ed5c10
ms.sourcegitcommit: f7998db5e6ba35cbf2a133174027dc8ccf8ce957
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2019
ms.locfileid: "68782463"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Azure Security Center でのデータ サービスの脅威検出

 Security Center では、データ ストレージ サービスのログが分析され、データ リソースへの脅威が検出されると、アラートがトリガーされます。 このトピックでは、Security Center によって次のサービスに対して生成されるアラートの一覧を示します。

* [Azure SQL Database と SQL Data Warehouse](#data-sql)
* [Azure Storage](#azure-storage)
* [Cosmos DB](#cosmos-db)

## Azure SQL Database と SQL Data Warehouse <a name="data-sql"></a>

SQL の脅威の検出により、データベースへのアクセスやデータベースの悪用を試みる、異常で有害と考えられる不自然な動作が検出されます。 Security Center により SQL 監査ログが分析されます。Security Center は、SQL エンジンでネイティブに実行されます。

|アラート:|説明|
|---|---|
|**Vulnerability to SQL Injection (SQL インジェクションにつながる脆弱性)**|アプリケーションにより、データベースでエラーのある SQL ステートメントが生成されました。 これは、SQL インジェクション攻撃に対する脆弱性があることを示している可能性があります。 エラーのあるステートメントが生成される理由として、次の 2 つが考えられます。アプリケーション コードの欠陥により、エラーのある SQL ステートメントが作成された。 または、SQL インジェクションに悪用される可能性がある、エラーのある SQL ステートメントが作成されるとき、アプリケーション コードまたはストアド プロシージャによって、ユーザー入力がサニタイズされなかった。|
|**SQL インジェクションの可能性**|SQL インジェクションに脆弱な特定されたアプリケーションに対してアクティブな悪用が発生しました。 これは、攻撃者が脆弱なアプリケーション コードまたはストアド プロシージャを使用して、悪意のある SQL ステートメントを挿入しようとしていることを意味します。|
|**Access from unusual location (通常とは異なる場所からのアクセス)**|SQL サーバーへのアクセス パターンに変化がありました。何者かが通常とは異なる地理的な場所から SQL サーバーにログオンしました。 このアラートで正当なアクション (新しいアプリケーションや開発者メンテナンス) が検出されることがあります。 別のケースでは、このアラートによって悪意のあるアクション (元従業員、外部の攻撃者) が検出されます。|
|**Access from unfamiliar principal (通常とは異なるプリンシパルからのアクセス)**|SQL サーバーへのアクセス パターンに変化がありました。何者かが通常とは異なるプリンシパル (SQL ユーザー) を使用して SQL サーバーにログオンしました。 このアラートで正当なアクション (新しいアプリケーションや開発者メンテナンス) が検出されることがあります。 別のケースでは、このアラートによって悪意のあるアクション (元従業員、外部の攻撃者) が検出されます。|
|**Access from a potentially harmful application (潜在的に有害なアプリケーションからのアクセス)**|データベースにアクセスするために、有害な可能性があるアプリケーションが使用されました。 このアラートで実行中の侵入テストが検出されることがあります。 別のケースでは、このアラートで一般的な攻撃ツールを使用した攻撃が検出されます。|
|**Brute force SQL credentials (SQL 資格情報に対するブルート フォース攻撃)**|異なる資格情報でのログインの失敗が異常に多く発生しました。 このアラートで実行中の侵入テストが検出されることがあります。 別のケースでは、このアラートでブルート フォース攻撃が検出されます。|

SQL 脅威検出アラートについて詳しくは、「[Azure SQL Database の脅威の検出](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)」を参照し、脅威検出アラートに関するセクションをご覧ください。 また、[Azure Security Center の支援でサイバー攻撃がどのように明らかにされるか](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/)についての記事を参照し、攻撃を検出するために Security Center で悪意のある SQL アクティビティ検出がどのように使用されたかについての例を確認してください。

## Azure Storage <a name="azure-storage"></a>

>[!NOTE]
> Azure Storage の Advanced Threat Protection は、現時点では BLOB ストレージでのみ使用できます。

Advanced Threat Protection for Azure Storage では、ストレージ アカウントに対する通常と異なる潜在的に有害なアクセスの試行すなわちストレージ アカウントの悪用を検出するセキュリティ インテリジェンスが強化されます。 この保護層により、セキュリティの専門家でなくても脅威に対処し、セキュリティ監視システムを管理できます。

Security Center では、脅威を検出するために、BLOB ストレージに対する読み取り、書き込み、および削除要求の診断ログが分析され、アクティビティに異常がある場合にはアラートがトリガーされます。 詳細については、[Storage Analytics のログの構成](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)に関する記事を参照してください。

> [!div class="mx-tableFixed"]

|アラート:|説明|
|---|---|
|**Unusual location access anomaly (通常とは異なる場所の異常なアクセス)**|サンプリングされたネットワーク トラフィックの分析で、デプロイ内のリソースからの異常な発信 Remote Desktop Protocol (RDP) 通信が検出されました。 このアクティビティは、この環境では異常と考えられ、リソースが侵害され、外部 RDP エンドポイントに対するブルート フォース攻撃にそのリソースが現在使用されている可能性があります。 この種の活動によって、自分の IP が外部のエンティティによって悪意のある IP とフラグが付けられる可能性があることに注意してください。|
|**Application access anomaly (アプリケーションによる異常なアクセス)**|通常とは異なるアプリケーションがこのストレージ アカウントにアクセスしたことを示します。 原因として考えられるのは、攻撃者が新しいアプリケーションを使用してストレージ アカウントにアクセスしたことです。|
|**Anonymous access anomaly (匿名での異常なアクセス)**|ストレージ アカウントへのアクセス パターンに変化が生じたことを示します。 たとえば、アカウントへの匿名 (認証なし) アクセスがあり、それがこのアカウントでの最近のアクセス パターンと比較して予測外である場合などです。 原因として考えられるのは、攻撃者が BLOB ストレージを保持するコンテナーへのパブリック読み取りアクセスを悪用したことです。|
|**Tor 異常**|このアカウントが Tor (匿名化プロキシ) のアクティブな出口ノードとして知られている IP アドレスから正常にアクセスされていることを示します。 このアラートの重大度では、使用された認証の種類 (ある場合) と、これがこのようなアクセスの最初のケースかどうかが考慮されます。原因としては、攻撃者が Tor を使用してストレージ アカウントにアクセスしたか、正当なユーザーが Tor を使用してストレージ アカウントにアクセスしたことが考えられます。|
|**Data Exfiltration anomaly (データの異常な抜き取り)**|このストレージ コンテナーの最近のアクティビティと比較して、異常に大量のデータが抽出されたことを示します。 原因として考えられるのは、攻撃者が BLOB ストレージを保持するコンテナーから大量のデータを抽出したことです。|
|**Unexpected delete anomaly (予期しない異常な削除)**|このストレージ アカウントでの最近のアクティビティと比較して、アカウントで予期しない 1 つ以上の削除操作が発生したことを示します。 原因として考えられるのは、攻撃者がストレージ アカウントからデータを削除したことです。|
|**Upload Azure Cloud Service package (Azure クラウド サービス パッケージのアップロード)**|Azure クラウド サービス パッケージ (.cspkg ファイル) が、このアカウントでの最近のアクティビティと比較して、通常と異なる方法でストレージ アカウントにアップロードされたことを示します。 原因として考えられるのは、攻撃者が、ストレージ アカウントから Azure クラウド サービスに悪意のあるコードをデプロイしようとしていたことです。|
|**Permission access anomaly (アクセス許可の異常なアクセス)**|このストレージ コンテナーのアクセス許可が通常と異なる方法で変更されたことを示します。 原因として考えられるのは、攻撃者がコンテナーのアクセス許可を変更して、そのセキュリティ体制を弱めたり、持続性を獲得したりしたことです。|
|**Inspection access anomaly (検査の異常なアクセス)**|ストレージ アカウントのアクセス許可が、このアカウントでの最近のアクティビティと比較して、通常とは異なる方法で検査されたことを示します。 原因として考えられるのは、攻撃者が将来の攻撃のための偵察を実行したことです。|
|**Data Exploration anomaly (データの異常な探索)**|ストレージ アカウント内の BLOB またはコンテナーが、このアカウントでの最近のアクティビティと比較して、通常と異なる方法で列挙されたことを示します。 原因として考えられるのは、攻撃者が将来の攻撃のための偵察を実行したことです。|

>[!NOTE]
>Advanced Threat Protection for Azure Storage は、現在のところ Azure Government およびソブリン クラウドのリージョンでは使用できません。

ストレージのアラートについて詳しくは、「[Advanced Threat Protection for Azure Storage](../storage/common/storage-advanced-threat-protection.md)」記事を参照し、保護アラートに関するセクションをご覧ください。

## Cosmos DB<a name="cosmos-db"></a>

次のアラートは、Azure Cosmos DB アカウントに対して、通常と異なる潜在的に有害なアクセスやエクスプロイトが試行されると生成されます。

|アラート:|説明|
|---|---|
|**通常と異なる場所からのアクセス**|Cosmos DB アカウントへのアクセス パターンに変化があったことを示します。 最近のアクティビティと比較して、誰かが未知の IP アドレスからこのアカウントにアクセスしました。 攻撃者が Cosmos DB アカウントにアクセスしたか、または正当なユーザーが通常と異なる新しい地理的場所から Cosmos DB アカウントにアクセスしました。 たとえば、新しいアプリケーションまたは開発者によるリモート メンテナンスなどです。|
|**通常と異なるデータの流出**|Cosmos DB アカウントからのデータ抽出パターンに変化があったことを示します。 最近のアクティビティと比較して、誰かが通常と異なる量のデータを抽出しました。 攻撃者が Cosmos DB データベースから大量のデータを抽出しました。 たとえば、データの流出/漏えい、データの不正転送などです。 あるいは、正当なユーザーまたはアプリケーションが、コンテナーから通常と異なる量のデータを抽出しました。 たとえばメンテナンス バックアップ アクティビティなどです。|

詳細については、「[Azure Cosmos DB の Advanced Threat Protection](../cosmos-db/cosmos-db-advanced-threat-protection.md)」を参照してください。