---
title: Azure Security Center でのデータ サービスの脅威検出 | Microsoft Docs
description: このトピックでは、Azure Security Center で使用可能なデータ サービスのアラートについて説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: memildin
ms.openlocfilehash: 763349c11a7777bfc807a60e1806c3bb4010245e
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202586"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Azure Security Center でのデータ サービスの脅威検出

 Azure Security Center では、データ ストレージ サービスのログが分析され、データ リソースへの脅威が検出されると、アラートがトリガーされます。 このトピックでは、Security Center によって次のサービスに対して生成されるアラートの一覧を示します。

* [Azure SQL Database と Azure SQL Data Warehouse](#data-sql)
* [Azure Storage](#azure-storage)
* [Azure Cosmos DB](#cosmos-db)

## SQL Database と SQL Data Warehouse <a name="data-sql"></a>

SQL の脅威の検出により、データベースへのアクセスやデータベースの悪用を試みる、異常で有害と考えられる不自然な動作が識別されます。 Security Center により SQL 監査ログが分析されます。Security Center は、SQL エンジンでネイティブに実行されます。

|アラート:|説明|
|---|---|
|**Vulnerability to SQL injection (SQL インジェクションにつながる脆弱性)**|アプリケーションにより、データベースでエラーのある SQL ステートメントが生成されました。 これは、SQL インジェクション攻撃に対する脆弱性があることを示している可能性があります。 エラーのあるステートメントの理由としては、次の 2 つが考えられます。 アプリケーション コードの欠陥により、エラーのある SQL ステートメントが作成された可能性がある。 または、アプリケーション コードまたはストアド プロシージャでユーザー入力がサニタイズされず、エラーのある SQL ステートメントが作成され、SQL インジェクションに悪用される可能性がある。|
|**SQL インジェクションの可能性**|SQL インジェクションに脆弱な特定されたアプリケーションに対してアクティブな悪用が発生しました。 これは、攻撃者が脆弱なアプリケーション コードまたはストアド プロシージャを使用して、悪意のある SQL ステートメントを挿入しようとしていることを意味します。|
|**Access from unusual location (通常とは異なる場所からのアクセス)**|SQL Server へのアクセス パターンに変化があり、何者かが通常とは異なる地理的な場所からサーバーにサインインしました。 このアラートで正当なアクション (新しいアプリケーションや開発者メンテナンス) が検出されることがあります。 別のケースでは、このアラートによって悪意のあるアクション (元従業員や外部の攻撃者など) が検出されます。|
|**Access from unfamiliar principal (通常とは異なるプリンシパルからのアクセス)**|SQL Server へのアクセス パターンに変化がありました。 何者かが、通常とは異なるプリンシパル (ユーザー) を使用してサーバーにサインインしました。 このアラートで正当なアクション (新しいアプリケーションや開発者メンテナンス) が検出されることがあります。 別のケースでは、このアラートによって悪意のあるアクション (元従業員や外部の攻撃者など) が検出されます。|
|**Access from a potentially harmful application (潜在的に有害なアプリケーションからのアクセス)**|データベースにアクセスするために、有害な可能性があるアプリケーションが使用されました。 このアラートで実行中の侵入テストが検出されることがあります。 別のケースでは、このアラートで一般的なツールを使用した攻撃が検出されます。|
|**Brute force SQL credentials (SQL 資格情報に対するブルート フォース攻撃)**|異なる資格情報を使用したサインインの失敗が異常に多く発生しました。 このアラートで実行中の侵入テストが検出されることがあります。 このアラートでブルート フォース攻撃が検出されることもあります。|

SQL 脅威検出アラートの詳細については、[Azure SQL Database の脅威の検出](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)に関する記事を参照してください。 特に、脅威検出アラートに関するセクションをご覧ください。 また、[Azure Security Center の支援でサイバー攻撃がどのように明らかにされるか](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/)について参照し、Security Center で悪意のある SQL アクティビティ検出を使用して攻撃を検出した例を確認してください。

## ストレージ <a name="azure-storage"></a>

>[!NOTE]
> Advanced Threat Protection for Storage は、現時点では Blob Storage でのみ使用できます。

Advanced Threat Protection for Storage にセキュリティ インテリジェンスの層が追加され、ストレージ アカウントにアクセスまたは悪用する、通常とは異なる潜在的に有害な試行が検出されます。 この保護層により、セキュリティの専門家でなくても脅威に対処し、セキュリティ監視システムを管理できます。

Security Center では、脅威を検出するために、BLOB ストレージに対する読み取り、書き込み、および削除要求の診断ログが分析され、アクティビティに異常がある場合にはアラートがトリガーされます。 詳細については、[Storage Analytics ログの構成](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)に関するページを参照してください。

> [!div class="mx-tableFixed"]

|アラート:|説明|
|---|---|
|**Unusual location access anomaly (通常とは異なる場所の異常なアクセス)**|サンプリングされたネットワーク トラフィックの分析で、デプロイ内のリソースからの異常な発信 Remote Desktop Protocol (RDP) 通信が検出されました。 このアクティビティは、この環境では異常と見なされます。 リソースが侵害されていて、現在、外部 RDP エンドポイントのブルート フォース攻撃に使用されていることを示す可能性があります。 この種の活動によって、自分の IP が外部のエンティティによって悪意のある IP とフラグが付けられる可能性があることに注意してください。|
|**Application access anomaly (アプリケーションによる異常なアクセス)**|通常とは異なるアプリケーションがこのストレージ アカウントにアクセスしたことを示します。 原因として考えられるのは、攻撃者が新しいアプリケーションを使用して、ご自分のストレージ アカウントにアクセスしたことです。|
|**Anonymous access anomaly (匿名での異常なアクセス)**|ストレージ アカウントへのアクセス パターンに変化が生じたことを示します。 たとえば、アカウントへの匿名 (認証なし) アクセスがあり、それがこのアカウントでの最近のアクセス パターンと比較して予測外である場合などです。 原因として考えられるのは、攻撃者が Blob Storage を保持するコンテナーへのパブリック読み取りアクセスを悪用したことです。|
|**Tor 異常**|このアカウントが Tor (匿名化プロキシ) のアクティブな出口ノードとして知られている IP アドレスから正常にアクセスされていることを示します。 このアラートの重大度では、使用された認証の種類 (ある場合) と、これがそのようなアクセスの最初のケースであるかどうかが考慮されます。 原因として考えられるのは、攻撃者が Tor を使用して、ご自分のストレージ アカウントにアクセスした、あるいは正当なユーザーが Tor を使用して、ご利用のストレージ アカウントにアクセスしたことです。|
|**Data Exfiltration anomaly (データの異常な抜き取り)**|このストレージ コンテナーの最近のアクティビティと比較して、異常に大量のデータが抽出されたことを示します。 原因として考えられるのは、攻撃者が Blob Storage を保持するコンテナーから大量のデータを抽出したことです。|
|**Unexpected delete anomaly (予期しない異常な削除)**|このストレージ アカウントでの最近のアクティビティと比較して、アカウントで予期しない 1 つ以上の削除操作が発生したことを示します。 原因として考えられるのは、攻撃者がストレージ アカウントからデータを削除したことです。|
|**Upload Azure Cloud Services package (Azure クラウド サービス パッケージのアップロード)**|Azure Cloud Services パッケージ (.cspkg ファイル) が、このアカウントでの最近のアクティビティと比較して、通常と異なる方法でストレージ アカウントにアップロードされたことを示します。 原因として考えられるのは、攻撃者が、ストレージ アカウントから Azure クラウド サービスに悪意のあるコードをデプロイしようとしていたことです。|
|**Permission access anomaly (アクセス許可の異常なアクセス)**|このストレージ コンテナーのアクセス許可が通常と異なる方法で変更されたことを示します。 原因として考えられるのは、攻撃者がコンテナーのアクセス許可を変更して、そのセキュリティ体制を弱めたり、持続性を獲得したことです。|
|**Inspection access anomaly (検査の異常なアクセス)**|ストレージ アカウントのアクセス許可が、このアカウントでの最近のアクティビティと比較して、通常とは異なる方法で検査されたことを示します。 原因として考えられるのは、攻撃者が将来の攻撃のための偵察を実行したことです。|
|**Data Exploration anomaly (データの異常な探索)**|ストレージ アカウント内の BLOB またはコンテナーが、このアカウントでの最近のアクティビティと比較して、通常と異なる方法で列挙されたことを示します。 原因として考えられるのは、攻撃者が将来の攻撃のための偵察を実行したことです。|

>[!NOTE]
>Advanced Threat Protection for Storage は、現在、Azure Government およびソブリン クラウドのリージョンでは使用できません。

ストレージのアラートの詳細については、[Advanced Threat Protection for Azure Storage](../storage/common/storage-advanced-threat-protection.md)に関する記事を参照してください。 特に、「保護アラート」セクションをご覧ください。

## Azure Cosmos DB<a name="cosmos-db"></a>

次のアラートは、Azure Cosmos DB アカウントに対して、通常と異なる潜在的に有害なアクセスやエクスプロイトが試行されると生成されます。

|アラート:|説明|
|---|---|
|**通常と異なる場所からのアクセス**|Azure Cosmos DB アカウントへのアクセス パターンに変化があったことを示します。 最近のアクティビティと比較して、誰かが未知の IP アドレスからこのアカウントにアクセスしました。 攻撃者がアカウントにアクセスしてたか、あるいは正当なユーザーが通常と異なる新しい地理的場所からアカウントにアクセスしました。 後者の例には、新しいアプリケーションまたは開発者からのリモート メンテナンスがあります。|
|**通常と異なるデータの流出**|Azure Cosmos DB アカウントからのデータ抽出パターンに変化があったことを示します。 最近のアクティビティと比較して、誰かが通常と異なる量のデータを抽出しました。 攻撃者が、Azure Cosmos DB データベースから大量のデータを抽出した可能性があります (たとえば、データの窃盗や漏えい、データの不正な転送など)。 あるいは、正当なユーザーまたはアプリケーションによって、コンテナーから通常とは異なる量のデータが抽出された可能性もあります (メンテナンス バックアップ アクティビティの場合など)。|

詳細については、「[Azure Cosmos DB の Advanced Threat Protection](../cosmos-db/cosmos-db-advanced-threat-protection.md)」を参照してください。