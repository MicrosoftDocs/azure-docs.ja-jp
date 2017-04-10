---
title: "NoSQL データベースのセキュリティ - Azure DocumentDB | Microsoft Docs"
description: "Azure DocumentDB が NoSQL データ用のデータベースの保護とデータのセキュリティを提供する方法について説明します。"
keywords: "NoSQL データベースのセキュリティ, 情報のセキュリティ, データのセキュリティ, データベースの暗号化, データベースの保護, セキュリティ ポリシー, セキュリティ テスト"
services: documentdb
author: mimig1
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: a02a6a82-3baf-405c-9355-7a00aaa1a816
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: ca5f5696e9478d5087a09fff16742f65ea3e5589
ms.lasthandoff: 03/10/2017


---

# <a name="documentdb-nosql-database-security"></a>DocumentDB NoSQL データベースのセキュリティ

 この記事では、NoSQL データベースのセキュリティに関するベスト プラクティスと、データベース侵害を防止、検出、および対応するために役立つ Azure DocumentDB の主な機能について説明します。

## <a name="how-do-i-secure-my-nosql-database"></a>NoSQL データベースをセキュリティ保護する方法 

データのセキュリティは、顧客自身とデータベース プロバイダーの共同責任です。 顧客として選択したデータベース プロバイダーによって、引き受ける責任の範囲は変化する可能性があります。 オンプレミス ソリューションを選択した場合、エンドポイントの保護からハードウェアの物理的なセキュリティに至るまでのすべてを顧客自身が提供する必要がありますが、これは簡単な仕事ではありません。 Azure DocumentDB などの PaaS クラウド データベース プロバイダーを選択した場合、顧客が関与する範囲は大幅に縮小されます。 マイクロソフトの「[Shared Responsibilities for Cloud Computing (クラウドコンピューティングの共同責任)](https://aka.ms/sharedresponsibility)」ホワイト ペーパーから借用した次の図は、Azure DocumentDB のような PaaS プロバイダーを使用すると、顧客の責任がどのように減少するかを示しています。

![顧客とデータベース プロバイダーの責任](./media/documentdb-nosql-database-security/nosql-database-security-responsibilities.png)

上記の図は高度なクラウドのセキュリティ コンポーネントを示していますが、具体的には、どのような項目を NoSQL データベース ソリューションのために考慮する必要があるでしょうか。 また、ソリューションを相互に比較するにはどうすればよいでしょうか。 

次の要件のチェックリストを使用して、NoSQL データベース システムを比較することをお勧めします。

- ネットワークのセキュリティとファイアウォールの設定
- ユーザー認証ときめ細かいユーザー制御
- 局地的な障害に対応するためにデータをグローバルにレプリケートする能力
- 1 つのデータ センターから別のデータセンターへのフェールオーバーを実行する能力
- データ センター内でのローカルなデータのレプリケーション
- データの自動バックアップ
- バックアップから削除されたデータの復元
- 機密データの保護と分離
- 攻撃の監視
- 攻撃への対応
- データ ガバナンス制約に対してデータをジオフェンスで準拠させる能力
- 保護されたデータセンター内でのサーバーの物理的な保護

さらに、最近発生した[大規模なデータベース侵害](http://thehackernews.com/2017/01/mongodb-database-security.html)は、次の単純ではあるが重要な要件を思い出させてくれます。
- 修正プログラムの適用によって常に最新の状態が維持されるサーバー
- 既定/SSL 暗号化による HTTPS
- 強力なパスワードを持つ管理者アカウント

## <a name="how-does-azure-documentdb-secure-my-database"></a>Azure DocumentDB がデータベースをセキュリティで保護する方法

上記のリストを検討してみましょう。Azure DocumentDB が対応しているするセキュリティ要件はいくつあるでしょうか。 すべてです。

各要件を掘り下げてみましょう。

|セキュリティ要件|DocumentDB のセキュリティ手法|
|---|---|---|
|ネットワークのセキュリティ|IP ファイアウォールの使用は、NoSQL データベースをセキュリティ保護するための第&1; の保護層です。 DocumentDB は、受信ファイアウォールでのポリシーに基づく IP ベースのアクセス制御をサポートしています。 IP ベースのアクセス制御は、従来のデータベース システムで使用されているファイアウォール ルールに似ていますが、DocumentDB データベース アカウントは、承認されたコンピューターまたはクラウド サービスからのみアクセスできるように拡張されています。 <br><br>DocumentDB では、特定の IP アドレス (168.61.48.0)、IP 範囲 (168.61.48.0/8)、および IP アドレスと範囲の組み合わせを有効にすることができます。 <br><br>この許可リストに含まれていないコンピューターから送信されたすべての要求は、DocumentDB によってブロックされます。 承認されたコンピューターとクラウド サービスから送信された要求がリソースへのアクセス権を取得するには、認証プロセスを完了する必要があります。<br><br>詳細については、「[DocumentDB のファイアウォール サポート](documentdb-firewall-support.md)」を参照してください。|
|承認|DocumentDB は、承認のためにハッシュ ベースのメッセージ認証コード (HMAC) を使用します。 <br><br>各要求は、シークレット アカウント キーを使用してハッシュされ、base&64; エンコード ハッシュが呼び出しと一緒に DocumentDB に送信されます。 DocumentDB サービスは、要求を検証するために、適切なシークレット キーとプロパティを使用してハッシュを生成し、その値と要求に含まれる値を比較します。 2 つの値が一致した場合、承認操作は成功し、要求が処理されます。一致しない場合、承認は失敗し、要求は拒否されます。<br><br>[マスター キー](documentdb-secure-access-to-data.md#master-keys)または[リソース トークン](documentdb-secure-access-to-data.md#resource-tokens)のいずれかを使用して、ドキュメントなどのリソースに対するきめ細かいアクセス制御を実行できます。<br><br>詳細については、「[DocumentDB のデータへのアクセスのセキュリティ保護](documentdb-secure-access-to-data.md)」を参照してください。|
|ユーザーとアクセス許可|アカウントの[マスター キー](#master-key)を使用して、ユーザー リソースとアクセス許可リソースをデータベースごとに作成することができます。 [リソース トークン](#resource-token)は、データベース内のアクセス許可に関連付けられ、ユーザーがデータベース内のアプリケーション リソースにどのようにアクセスできるか (読み取り/書き込み、読み取り専用、またはアクセスなし) を判断します。 アプリケーション リソースには、コレクション、ドキュメント、添付ファイル、ストアド プロシージャ、トリガー、および UDF が含まれます。 リソース トークンは認証中に使用され、リソースへのアクセスが提供されるか拒否されます。<br><br>詳細については、「[DocumentDB のデータへのアクセスのセキュリティ保護](documentdb-secure-access-to-data.md)」を参照してください。|
|Active Directory の統合 (RBAC)| データベース アカウントへのアクセスは、Azure Portal でアクセス制御 (IAM) を使用して提供することもできます。 IAM は ロールベースのアクセス制御を提供し、Active Directory と統合されています。 次の図に示すように、個人やグループの組み込みのロールまたはカスタム ロールを使用できます。<br><br>![Azure Portal でのアクセス制御 (IAM) - NoSQL データベースのセキュリティ](./media/documentdb-nosql-database-security/nosql-database-security-identity-access-management-iam-rbac.png)|
|グローバル レプリケーション|DocumentDB は設定不要のグローバル分散を実行し、ボタンをクリックするだけで Azure の世界中のデータセンターにデータをレプリケートすることができます。 グローバル レプリケーションでは、グローバルなスケールを行い、全世界のデータに低待機時間でアクセスすることができます。<br><br>セキュリティの点では、グローバル レプリケーションは、局地的な障害からデータが保護されることを保証します。<br><br>詳細については、[データのグローバル分散](documentdb-distribute-data-globally.md)に関するページを参照してください。|
|リージョン間フェールオーバー|1 つ以上のデータセンターにデータをレプリケートしているときに、リージョンのデータセンターがオフラインになった場合、DocumentDB は、操作を自動的にロールオーバーします。 データをレプリケートするリージョンを使用して、フェールオーバーするリージョンの優先度リストを作成することができます。 <br><br>詳細については、「[Azure DocumentDB のリージョン内フェールオーバー](documentdb-regional-failovers.md)」を参照してください。|
|ローカル レプリケーション|DocumentDB は、1 つのデータセンター内でも、高可用性を維持するためにデータを自動的にレプリケートします。[一貫性レベル](documentdb-consistency-levels.md)は顧客自身が選択できます。 これにより、 [99.99% の稼働時間の可用性 (SLA)](https://azure.microsoft.com/support/legal/sla/documentdb/v1_0/) が保証され、金銭的な保証も受けられます。これは他の NoSQL データベース サービスが提供できないものです。|
|オンライン バックアップの自動化|DocumentDB データベースは定期的にバックアップされ、ジオリダンダント ストアに格納されます。 <br><br>詳細については、「[DocumentDB での自動オンライン バックアップと復元](documentdb-online-backup-and-restore.md)」を参照してください。|
|削除されたデータの復元|自動化されたオンライン バックアップを使用して、誤って削除したデータを最大&30; 日以内に回復することができます。 <br><br>詳細については、「[DocumentDB での自動オンライン バックアップと復元](documentdb-online-backup-and-restore.md)」を参照してください。|
|機密データの保護と分離|PII と他の機密データを特定のコレクションと読み取り/書き込みに分離するか、読み取り専用アクセスを特定のユーザーに限定することができます。|
|攻撃の監視|監査ログとアクティビティ ログを使用することで、アカウントの通常のアクティビティと異常なアクティビティを監視できます。 リソースに対して実行された操作、操作を開始した人物、操作の発生日時、操作の状態などを確認できます。<br><br>![Azure DocumentDB のアクティビティ ログ](./media/documentdb-nosql-database-security/nosql-database-security-application-logging.png)|
|攻撃への対応|Azure のサポートに連絡して攻撃の可能性を報告すると、5 段階のインシデント対応プロセスが開始されます。 この 5 段階のプロセスの目標は、問題が検出され、調査が開始された後、通常のサービスのセキュリティと操作を可能な限り早急に復元することです。<br><br>詳細については、「[Microsoft Azure Security Response in the Cloud (クラウドでの Microsoft Azure のセキュリティへの対応)](https://aka.ms/securityresponsepaper)」を参照してください。|
|ジオフェンス|DocumentDB は、主権地域 (ドイツ、中国、米国政府など) に対するデータ ガバナンスとコンプライアンスを保証します。|
|施設の保護|DocumentDB のデータは、Azure の保護されたデータセンター内の SSD に格納されます。<br><br>詳細については、[Microsoft グローバルデータセンター](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)に関するページを参照してください。|
|HTTPS/SSL/TLS の暗号化|クライアントからサービスへの DocumentDB のすべての通信には、SSL/TLS 1.2 が適用されます。 また、データセンター内とデータセンター間のすべてのレプリケーションには、SSL/TLS 1.2 が適用されます。|
|サーバーへの修正プログラムの適用|管理された NoSQL データベースとして、DocumentDB は、サーバーの管理と修正プログラムの適用を行う必要性を排除します。顧客に代わって DocumentDB が自動的に実行します。|
|強力なパスワードを持つ管理者アカウント|今でもこの要件について言及する必要があることは信じ難いことですが、一部の競合他社とは異なり、DocumentDB では、パスワードを持たない管理アカウントは存在できません。<br><br> SSL と HMAC のシークレット ベースの認証によるセキュリティは、既定で組み込まれています。|
|セキュリティとデータ保護の認証|DocumentDB は、[ISO 27001](https://www.microsoft.com/en-us/TrustCenter/Compliance/ISO-IEC-27001)[European Model Clauses (EUMC)](https://www.microsoft.com/en-us/TrustCenter/Compliance/EU-Model-Clauses)、および [HIPAA](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA) の認証を取得しています。 その他の認証の取得も進行中です|

## <a name="next-steps"></a>次のステップ

マスター キーとリソース トークンの詳細については、「[DocumentDB のデータへのアクセスのセキュリティ保護](documentdb-secure-access-to-data.md)」を参照してください。

マイクロソフトが取得している認証の詳細については、[Azure セキュリティ センター](https://azure.microsoft.com/en-us/support/trust-center/)に関するページを参照してください。
