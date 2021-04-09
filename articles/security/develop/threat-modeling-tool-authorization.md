---
title: 承認 - Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: Threat Modeling Tool での承認の対応策について説明します。 潜在的な脅威と軽減手順の一覧を確認します。
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: devx-track-csharp
ms.openlocfilehash: 38b147a85a26fd1e0be4f5dc6b63ae4c1331d348
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101093999"
---
# <a name="security-frame-authorization--mitigations"></a>セキュリティ フレーム:承認 | 対応策 
| 製品/サービス | [アーティクル] |
| --------------- | ------- |
| **コンピューターの信頼の境界** | <ul><li>[デバイス上のデータへの未承認アクセスを制限する適切な ACL が構成されていることを確認する](#acl-restricted-access)</li><li>[ユーザー固有の機密アプリケーション コンテンツがユーザー プロファイル ディレクトリに保存されていることを確認する](#sensitive-directory)</li><li>[デプロイされたアプリケーションが最小権限で実行されていることを確認する](#deployed-privileges)</li></ul> |
| **Web アプリケーション** | <ul><li>[ビジネス ロジックのフローを連続した順序で実施する](#sequential-logic)</li><li>[列挙を防ぐレート制限メカニズムを実装する](#rate-enumeration)</li><li>[適切な承認確認を設定して、最小権限の原則に従う](#principle-least-privilege)</li><li>[ビジネス ロジックとリソースのアクセスの承認は、受信要求パラメーターに基づいて判断しない](#logic-request-parameters)</li><li>[コンテンツとリソースが列挙不可能であること、または強制的ブラウズでアクセスできないことを確認する](#enumerable-browsing)</li></ul> |
| **[データベース]** | <ul><li>[最小権限のアカウントを使用して Database サーバーに接続していることを確認する](#privileged-server)</li><li>[テナントがお互いのデータにアクセスできないように行レベル セキュリティの RLS を実装する](#rls-tenants)</li><li>[Sysadmin ロールには必要かつ有効なユーザーのみを割り当てる](#sysadmin-users)</li></ul> |
| **IoT クラウド ゲートウェイ** | <ul><li>[最小権限のトークンを使用してクラウド ゲートウェイに接続する](#cloud-least-privileged)</li></ul> |
| **Azure Event Hub** | <ul><li>[送信専用のアクセス許可 SAS キーを使用してデバイス トークンを生成する](#sendonly-sas)</li><li>[Event Hub への直接アクセスを提供するアクセス トークンは使用しない](#access-tokens-hub)</li><li>[必要最小限のアクセス許可が付与されている SAS キーを使用して Event Hub に接続する](#sas-minimum-permissions)</li></ul> |
| **Azure Document DB** | <ul><li>[可能な限りリソース トークンを使用して Azure Cosmos DB に接続する](#resource-docdb)</li></ul> |
| **Azure の信頼の境界** | <ul><li>[Azure RBAC を使用して Azure サブスクリプションへのアクセスをきめ細かく管理する](#grained-rbac)</li></ul> |
| **Service Fabric の信頼の境界** | <ul><li>[Azure RBAC を使用してクラスター操作へのクライアントのアクセスを制限する](#cluster-rbac)</li></ul> |
| **Dynamics CRM** | <ul><li>[必要に応じて、セキュリティのモデリングを実行し、フィールド レベルのセキュリティを使用する](#modeling-field)</li></ul> |
| **Dynamics CRM ポータル** | <ul><li>[ポータルのセキュリティ モデルとその他の CRM が異なることを念頭に置きながら、ポータル アカウントのセキュリティ モデルを実行する](#portal-security)</li></ul> |
| **Azure ストレージ** | <ul><li>[Azure Table Storage のエンティティの範囲で詳細なアクセス許可を付与する](#permission-entities)</li><li>[Azure Resource Manager を使用して、Azure ストレージ アカウントへの Azure のロールベースのアクセス制御 (Azure RBAC) を有効にする](#rbac-azure-manager)</li></ul> |
| **モバイル クライアント** | <ul><li>[暗黙的な脱獄または root 化検出を実装する](#rooting-detection)</li></ul> |
| **WCF** | <ul><li>[WCF での脆弱なクラス参照](#weak-class-wcf)</li><li>[WCF - 承認コントロールを実装する](#wcf-authz)</li></ul> |
| **Web API** | <ul><li>[ASP.NET Web API で適切な承認メカニズムを実装する](#authz-aspnet)</li></ul> |
| **IoT デバイス** | <ul><li>[さまざまなアクセス許可レベルを必要とする各種アクションをデバイスがサポートしている場合は、デバイスで承認チェックを実行する](#device-permission)</li></ul> |
| **IoT フィールド ゲートウェイ** | <ul><li>[さまざまなアクセス許可レベルを必要とする各種アクションをデバイスがサポートしている場合は、フィールド ゲートウェイで承認チェックを実行する](#field-permission)</li></ul> |

## <a name="ensure-that-proper-acls-are-configured-to-restrict-unauthorized-access-to-data-on-the-device"></a><a id="acl-restricted-access"></a>デバイス上のデータへの未承認アクセスを制限する適切な ACL が構成されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | コンピューターの信頼の境界 | 
| **SDL フェーズ**               | デプロイ |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | デバイス上のデータへの未承認アクセスを制限する適切な ACL が構成されていることを確認します|

## <a name="ensure-that-sensitive-user-specific-application-content-is-stored-in-user-profile-directory"></a><a id="sensitive-directory"></a>ユーザー固有の機密アプリケーション コンテンツがユーザー プロファイル ディレクトリに保存されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | コンピューターの信頼の境界 | 
| **SDL フェーズ**               | デプロイ |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | ユーザー固有の機密アプリケーション コンテンツが、ユーザー プロファイル ディレクトリに保存されていることを確認します。 これによりコンピューター上の複数のユーザーがお互いのデータにアクセスできなくなります。|

## <a name="ensure-that-the-deployed-applications-are-run-with-least-privileges"></a><a id="deployed-privileges"></a>デプロイされたアプリケーションが最小権限で実行されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | コンピューターの信頼の境界 | 
| **SDL フェーズ**               | デプロイ |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | デプロイされたアプリケーションが最小権限で実行されていることを確認します。 |

## <a name="enforce-sequential-step-order-when-processing-business-logic-flows"></a><a id="sequential-logic"></a>ビジネス ロジックのフローを連続した順序で実施する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | これが正規ユーザーによって実行されたことを確認するには、ビジネス ロジックのフローが連続した順序でのみ実施されるようにアプリケーションを指定します。すべての手順が現実的な人間の活動時間に処理されている必要があり、処理順序がランダムだったり、手順が省略されていたり、他のユーザーが処理した手順があったり、トランザクション送信が早すぎたりしてはいけません。|

## <a name="implement-rate-limiting-mechanism-to-prevent-enumeration"></a><a id="rate-enumeration"></a>列挙を防ぐレート制限メカニズムを実装する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | 重要な識別子は必ずランダムにします。 匿名ページに CAPTCHA コントロールを実装してください。 エラーと例外によって特定のデータが表示されないようにします|

## <a name="ensure-that-proper-authorization-is-in-place-and-principle-of-least-privileges-is-followed"></a><a id="principle-least-privilege"></a>適切な承認確認を設定して、最小権限の原則に従う

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | <p>最小権限の原則とは、ユーザーアカウントに対して、そのユーザーにとって必要な権限のみを付与することです。 たとえば、バックアップ ユーザーはソフトウェアをインストールする必要がありません。このため、バックアップ アプリケーションとバックアップ関連のアプリケーションを実行する権限のみが付与されます。 それ以外の権限 (新しいソフトウェアをインストールする権限など) は付与されません。 この原則は、通常のユーザー アカウントで作業している個人のコンピューター ユーザーにも当てはまり、絶対に必要な状況でのみ、パスワードで保護された管理者アカウント (つまりスーパーユーザー アカウント) が割り当てられます。 </p><p>この原則は、Web アプリケーションにも適用できます。 つまり、セッションを使用するロール ベースの認証方法だけを使用するのではなく、データベース認証システムによって権限をユーザーに割り当てます。 ユーザーが適切にログインしているかどうかの確認には引き続きセッションを使用しますが、ユーザーに特定のロールを割り当てるのではなく、システムで実行できるアクションを確認するための権限を割り当てます。 また、この方法の大きなメリットは、ユーザーに低い権限を割り当てる必要がある場合、割り当てがセッションに依存していないため、セッションが期限切れになるのを待つ必要がなく、変更が直ちに適用される点です。</p>|

## <a name="business-logic-and-resource-access-authorization-decisions-should-not-be-based-on-incoming-request-parameters"></a><a id="logic-request-parameters"></a>ビジネス ロジックとリソースのアクセスの承認は、受信要求パラメーターに基づいて判断しない

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | ユーザーがデータの確認しか行えないように制限されているかどうかをチェックするときは必ず、サーバー側でアクセス制限を処理する必要があります。 userID は、ログイン時にセッション変数内に保存し、データベースからユーザー データを取得するときに使用します |

### <a name="example"></a>例
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
これでデータを取得するための識別子がサーバー側で処理されるようになり、攻撃者がアプリケーションの操作を改ざんおよび変更できなくなりました。

## <a name="ensure-that-content-and-resources-are-not-enumerable-or-accessible-via-forceful-browsing"></a><a id="enumerable-browsing"></a>コンテンツとリソースが列挙不可能であること、または強制的ブラウズでアクセスできないことを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | <p>機密性の高い静的な構成ファイルは Web ルートに保持しないでください。 公開する必要のないコンテンツについては、適切なアクセス制御を適用するか、コンテンツ自体を削除します。</p><p>また、強制的ブラウズとブルート フォース手法は通常組み合わせて使用され、サーバー上のディレクトリとファイルを列挙するために、できるだけ多くの URL へのアクセスを試み、情報を収集します。 攻撃者は、一般的なファイルすべてのバリエーションを調べることができます。 たとえば、パスワード ファイルの検索については、psswd.txt、password.htm、password.dat などのファイルのバリエーションが対象となります。</p><p>これを防ぐには、ブルート フォース試行を検出する機能を追加します。</p>|

## <a name="ensure-that-least-privileged-accounts-are-used-to-connect-to-database-server"></a><a id="privileged-server"></a>最小権限のアカウントを使用して Database サーバーに接続していることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | データベース | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [SQL 権限の階層](/sql/relational-databases/security/permissions-hierarchy-database-engine)、[SQL セキュリティ保護可能なリソース](/sql/relational-databases/security/securables) |
| **手順** | 最小権限のアカウントを使用してデータベースに接続します。 アプリケーションのログインはデータベース内に制限し、選択したストアド プロシージャのみを実行します。 アプリケーションのログインには、直接テーブル アクセスは含まれません。 |

## <a name="implement-row-level-security-rls-to-prevent-tenants-from-accessing-each-others-data"></a><a id="rls-tenants"></a>テナントがお互いのデータにアクセスできないように行レベル セキュリティの RLS を実装する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | データベース | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | SQL Azure、OnPrem |
| **属性**              | SQL バージョン - V12、SQL バージョン - MsSQL2016 |
| **参照**              | [SQL Server の行レベルのセキュリティ (RLS)](/sql/relational-databases/security/row-level-security) |
| **手順** | <p>行レベルのセキュリティを使用すると、クエリを実行しているユーザーの特性 (たとえば、グループ メンバーシップや実行コンテキストなど) に基づいて、データベース テーブル内の行へのアクセスを制御できます。</p><p>Row-Level Security (RLS) は、アプリケーションでセキュリティの設計やコーディングを簡略化します。 RLS を使用すると、データ行のアクセスに対して制限を実装できます。 たとえば、ワーカーが自分の部署に関連するデータ行にのみアクセスできるようにしたり、顧客のデータ アクセスをその顧客の会社のデータにのみ制限したりできます。</p><p>アクセスの制限のロジックは、別のアプリケーション層のデータから離れてではなく、データベース層にあります。 任意の層からデータへのアクセスが試行されるたびに、データベース システムにはアクセス制限が適用されます。 これによりセキュリティ システムの侵入口が減り、そのシステムの信頼性と堅牢性が向上します。</p><p>|

そのまま使用できるデータベース機能としての RLS は、SQL Server 2016 以降、Azure SQL Database、および SQL Managed Instance にのみ適用できます。 そのまま使用できる RLS 機能が実装されていない場合、データ アクセスはビューとプロシージャの使用に制限されます

## <a name="sysadmin-role-should-only-have-valid-necessary-users"></a><a id="sysadmin-users"></a>Sysadmin ロールには必要かつ有効なユーザーのみを割り当てる

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | データベース | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [SQL 権限の階層](/sql/relational-databases/security/permissions-hierarchy-database-engine)、[SQL セキュリティ保護可能なリソース](/sql/relational-databases/security/securables) |
| **手順** | SysAdmin 固定サーバー ロールには限られたメンバーのみを割り当て、アプリケーションで使用されるアカウントは追加しないようにします。  ロールのユーザーの一覧を確認し、不要なアカウントはすべて削除してください|

## <a name="connect-to-cloud-gateway-using-least-privileged-tokens"></a><a id="cloud-least-privileged"></a>最小権限のトークンを使用してクラウド ゲートウェイに接続する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | IoT クラウド ゲートウェイ | 
| **SDL フェーズ**               | デプロイ |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | ゲートウェイの選択 - Azure IoT Hub |
| **参照**              | [IoT Hub Access Control](../../iot-hub/iot-hub-devguide.md) |
| **手順** | クラウド ゲートウェイ (IoT Hub) に接続するさまざまなへのコンポーネントに、最小権限のアクセス許可を付与します。 代表的な例として、デバイスの管理/プロビジョニング コンポーネントでは registryread/write が、イベント プロセッサ (ASA) では Service Connect が使用されています。 個別のデバイスは、デバイスの資格情報を使用してを接続されます|

## <a name="use-a-send-only-permissions-sas-key-for-generating-device-tokens"></a><a id="sendonly-sas"></a>送信専用のアクセス許可 SAS キーを使用してデバイス トークンを生成する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure Event Hub | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [Event Hubs の認証とセキュリティ モデルの概要](../../event-hubs/authenticate-shared-access-signature.md) |
| **手順** | SAS キーは、個別のデバイス トークンを生成するときに使用します。 特定の発行元に対してデバイス トークンを生成中は、送信専用のアクセス許可 SAS キーを使用してください|

## <a name="do-not-use-access-tokens-that-provide-direct-access-to-the-event-hub"></a><a id="access-tokens-hub"></a>Event Hub への直接アクセスを提供するアクセス トークンは使用しない

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure Event Hub | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [Event Hubs の認証とセキュリティ モデルの概要](../../event-hubs/authenticate-shared-access-signature.md) |
| **手順** | イベント ハブへの直接アクセスを許可するトークンはデバイスに付与しないでください。 発行元へのアクセスのみを提供する最小特権のトークンをデバイスに対して使用すると、そのデバイスを特定し、それが悪意のあるデバイスまたは侵害されたデバイスであることがわかったときに禁止できます。|

## <a name="connect-to-event-hub-using-sas-keys-that-have-the-minimum-permissions-required"></a><a id="sas-minimum-permissions"></a>必要最小限のアクセス許可が付与されている SAS キーを使用して Event Hub に接続する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure Event Hub | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [Event Hubs の認証とセキュリティ モデルの概要](../../event-hubs/authenticate-shared-access-signature.md) |
| **手順** | Event Hub に接続するさまざまなバックエンド アプリケーションに、最小権限のアクセス許可を付与します。 バックエンド アプリケーションごとに個別の SAS キーを生成し、必要なアクセス許可 (送信、受信、または管理) のみをそのキーに提供してください。|

## <a name="use-resource-tokens-to-connect-to-cosmos-db-whenever-possible"></a><a id="resource-docdb"></a>可能な限り、リソース トークンを使用して Cosmos DB に接続する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure Document DB | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | リソース トークンは、Azure Cosmos DB アクセス許可のリソースに関連付けられていて、データベースのユーザーと、ユーザーが持つ特定の Azure Cosmos DB アプリケーションのリソース (コレクション、ドキュメントなど) へのアクセス許可の間のリレーションシップをキャプチャします。 クライアントにマスター キーや読み取り専用キーを知らせたくない場合、たとえば、クライアントがモバイル クライアント、デスクトップ クライアントなどのエンド ユーザー アプリケーションの場合は、常にリソース トークンを使用して Azure Cosmos DB にアクセスします。マスター キーや読み取り専用キーは、こうしたキーを安全に格納できるバックエンド アプリケーションから使用してください。|

## <a name="enable-fine-grained-access-management-to-azure-subscription-using-azure-rbac"></a><a id="grained-rbac"></a>Azure RBAC を使用して Azure サブスクリプションへのアクセスをきめ細かく管理する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure の信頼の境界 | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [Azure サブスクリプション リソースへのアクセスを管理するための Azure ロールの割り当て](../../role-based-access-control/role-assignments-portal.md)  |
| **手順** | Azure のロールベースのアクセス制御 (Azure RBAC) を使用すると、Azure のきめ細かなアクセス管理が可能になります。 Azure RBAC を使用すると、職務に必要な範囲のアクセス権だけをユーザーに付与することができます。|

## <a name="restrict-clients-access-to-cluster-operations-using-service-fabric-rbac"></a><a id="cluster-rbac"></a>Service Fabric RBAC を使用してクラスター操作へのクライアントのアクセスを制限する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Service Fabric の信頼の境界 | 
| **SDL フェーズ**               | デプロイ |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 環境 - Azure |
| **参照**              | [Service Fabric のロールベースのアクセス制御 (Service Fabric クライアント用)](../../service-fabric/service-fabric-cluster-security-roles.md) |
| **手順** | <p>Azure Service Fabric では、Service Fabric クラスターに接続されるクライアントのために、管理者用とユーザー用の 2 つの異なるアクセス コントロールの種類がサポートされています。 アクセス制御を使用すると、クラスター管理者は、ユーザーのグループごとに特定のクラスター操作へのアクセスを制限して、クラスターのセキュリティを強化できます。</p><p>管理者には、管理機能へのフル アクセス権 (読み取り/書き込み機能など) があります。 ユーザーには、既定で管理機能 (クエリ機能など) と、アプリケーションとサービスを解決する機能への読み取りアクセス権のみがあります。</p><p>クラスターの作成時に、2 つのクライアント ロール (管理者とクライアント) を指定し、それぞれに個別の証明書を設定します。</p>|

## <a name="perform-security-modeling-and-use-field-level-security-where-required"></a><a id="modeling-field"></a>必要に応じて、セキュリティのモデリングを実行し、フィールド レベルのセキュリティを使用する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Dynamics CRM | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | 必要に応じて、セキュリティのモデリングを実行し、フィールド レベルのセキュリティを使用します|

## <a name="perform-security-modeling-of-portal-accounts-keeping-in-mind-that-the-security-model-for-the-portal-differs-from-the-rest-of-crm"></a><a id="portal-security"></a>ポータルのセキュリティ モデルとその他の CRM が異なることを念頭に置きながら、ポータル アカウントのセキュリティ モデルを実行する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Dynamics CRM ポータル | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | ポータルのセキュリティ モデルとその他の CRM が異なることを念頭に置きながら、ポータル アカウントのセキュリティ モデルを実行します|

## <a name="grant-fine-grained-permission-on-a-range-of-entities-in-azure-table-storage"></a><a id="permission-entities"></a>Azure Table Storage のエンティティの範囲で詳細なアクセス許可を付与する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure Storage | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | StorageType - テーブル |
| **参照**              | [SAS を使用して Azure ストレージ アカウントのオブジェクトへのアクセスを委任する方法](../../storage/blobs/security-recommendations.md#identity-and-access-management) |
| **手順** | ビジネス シナリオによっては、さまざまな関係者に対応した機密データ (さまざまな国/地域に関連する機密データなど) を、Azure Table Storage に格納しなければならないことがあります。 このような場合は、パーティションと行キー範囲を指定することで SAS 署名を構成し、ユーザーが、国/地域固有のデータにアクセスできるようにします。| 

## <a name="enable-azure-role-based-access-control-azure-rbac-to-azure-storage-account-using-azure-resource-manager"></a><a id="rbac-azure-manager"></a>Azure Resource Manager を使用して、Azure ストレージ アカウントへの Azure のロールベースのアクセス制御 (Azure RBAC) を有効にする

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure Storage | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、ストレージ アカウントをセキュリティで保護する方法](../../storage/blobs/security-recommendations.md) |
| **手順** | <p>新しいストレージ アカウントを作成するときに、クラシックまたは Azure Resource Manager のデプロイ モデルを選択します。 Azure にリソースを作成するクラシック モデルでは、サブスクリプション (つまりストレージ アカウント) に対するオールオアナッシング形式のアクセス権のみを許可します。</p><p>Azure Resource Manager モデルでは、Azure Active Directory を使用して、リソース グループにストレージ アカウントを追加し、特定のストレージ アカウントの管理プレーンに対するアクセス権を制御します。 たとえば、ストレージ アカウント キーへのアクセス権を特定のユーザーに付与し、他のユーザーはそのストレージ アカウントに関する情報を読み取ることはできても、ストレージ アカウント キーにはアクセスでないようにすることができます。</p>|

## <a name="implement-implicit-jailbreak-or-rooting-detection"></a><a id="rooting-detection"></a>暗黙的な脱獄または root 化検出を実装する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | モバイル クライアント | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | <p>携帯電話が root 化または脱獄された場合に備えて、アプリケーションは独自の構成およびユーザー データを保護する必要があります。 root 化/脱獄は未承認のアクセスを意味し、通常のユーザーが自身の携帯電話で行うことはありません。 したがって、アプリケーションには、アプリケーション起動時に携帯電話が root 化されたかどうかを検出する暗黙的な検出ロジックが必要です。</p><p>この検出ロジックは、通常は root ユーザーのみがアクセスできる次のようなファイルに、アクセスできるだけです。</p><ul><li>/system/app/Superuser.apk</li><li>/sbin/su</li><li>/system/bin/su</li><li>/system/xbin/su</li><li>/data/local/xbin/su</li><li>/data/local/bin/su</li><li>/system/sd/xbin/su</li><li>/system/bin/failsafe/su</li><li>/data/local/su</li></ul><p>このファイルのいずれかにアプリケーションがアクセスできる場合、そのアプリケーションは root ユーザーとして実行されています。</p>|

## <a name="weak-class-reference-in-wcf"></a><a id="weak-class-wcf"></a>WCF での脆弱なクラス参照

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | WCF | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック、NET Framework 3 |
| **属性**              | 該当なし  |
| **参照**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10))、[Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **手順** | <p>脆弱なクラス参照が使用されているため、攻撃者が未承認コードを実行できる可能性があります。 プログラムが参照するのは、一意に識別されていないユーザー定義のクラスです。 この厳密に識別されていないクラスが .NET によって読み込まれると、CLR 型ローダーは、指定した順序で次の場所にあるクラスを検索します。</p><ol><li>アセンブリの種類がわかっている場合、ローダーは構成ファイルのリダイレクト場所、GAC、構成ファイルを使用している現在のアセンブリ、およびアプリケーション ベース ディレクトリを検索します</li><li>アセンブリがわからない場合、ローダーは現在のアセンブリ、mscorlib、および TypeResolve イベント ハンドラーによって返される場所を検索します</li><li>この CLR 検索順序は、Type Forwarding メカニズム、AppDomain.TypeResolve イベントなどのフックで変更できます</li></ol><p>攻撃者が、同じ名前のクラスを別に作成して、CLR が最初に読み込む代替場所に配置することにより CLR 検索順序を悪用すると、攻撃者によって提供されたコードが CLR によって意図せずに実行されます</p>|

### <a name="example"></a>例
以下の WCF 構成ファイルの `<behaviorExtensions/>` 要素は、カスタム動作クラスを、特定の WCF 拡張機能に追加するよう WCF に指示します。
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
完全修飾名 (厳密な名前) を使用することで型が一意に識別され、システムのセキュリティが強化されます。 machine.config ファイルと app.config ファイルに型を登録するときに、完全修飾アセンブリ名を使用します。

### <a name="example"></a>例
以下の WCF 構成ファイルの `<behaviorExtensions/>` 要素は、厳密に参照されるカスタム動作クラスを、特定の WCF 拡張機能に追加するよう WCF に指示します。
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""Microsoft.ServiceModel.Samples.MyBehaviorSection, MyBehavior,
            Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```

## <a name="wcf-implement-authorization-control"></a><a id="wcf-authz"></a>WCF - 承認コントロールを実装する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | WCF | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック、NET Framework 3 |
| **属性**              | 該当なし  |
| **参照**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10))、[Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **手順** | <p>このサービスでは、承認コントロールが使用されません。 クライアントが特定の WCF サービスを呼び出すと、WCF は、呼び出し元に、サーバーでサービス メソッドを実行する権限が付与されていることを確認するさまざまな承認スキームを提供します。 承認コントロールが WCF サービスに対して有効でない場合は、認証されたユーザーが、権限の昇格を実行できます。</p>|

### <a name="example"></a>例
次の構成では、サービスを実行するときに、クライアントの認証レベルを確認しないよう WCF に指示します。
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""None"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
そして、サービスの承認スキームを使用して、サービス メソッドの呼び出し元によるこの操作の実行が許可されていることを確認します。 WCF には 2 つのモードが用意されています。この WCF により、カスタム承認スキームの定義が許可されます。 UseWindowsGroups モードでは Windows のロールとユーザーを、UseAspNetRoles モードでは、SQL Server などの ASP.NET ロール プロバイダーを使用して、認証を行います。

### <a name="example"></a>例
次の構成では、追加サービスを実行する前に、クライアントが Administrators グループに含まれていることを確認するよう WCF に指示します。
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""UseWindowsGroups"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
その後、サービスは次のように宣言されます。
```
[PrincipalPermission(SecurityAction.Demand,
Role = ""Builtin\\Administrators"")]
public double Add(double n1, double n2)
{
double result = n1 + n2;
return result;
}
```

## <a name="implement-proper-authorization-mechanism-in-aspnet-web-api"></a><a id="authz-aspnet"></a>ASP.NET Web API で適切な承認メカニズムを実装する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web API | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック、MVC5 |
| **属性**              | 該当なし、ID プロバイダー - ADFS、ID プロバイダー - Azure AD |
| **参照**              | [ASP.NET Web API の認証と承認](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) |
| **手順** | <p>アプリケーションが Azure AD または ADFS 要求を ID プロバイダーとして使用している場合、アプリケーション ユーザーのロール情報は、その Azure AD または ADFS 要求から派生していることがあります。また、アプリケーション自体がその情報を提供している場合もあります。 いずれの場合も、カスタム承認の実装によって、ユーザー ロール情報を検証する必要があります。</p><p>アプリケーションが Azure AD または ADFS 要求を ID プロバイダーとして使用している場合、アプリケーション ユーザーのロール情報は、その Azure AD または ADFS 要求から派生していることがあります。また、アプリケーション自体がその情報を提供している場合もあります。 いずれの場合も、カスタム承認の実装によって、ユーザー ロール情報を検証する必要があります。</p>

### <a name="example"></a>例
```csharp
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
public class ApiAuthorizeAttribute : System.Web.Http.AuthorizeAttribute
{
        public async override Task OnAuthorizationAsync(HttpActionContext actionContext, CancellationToken cancellationToken)
        {
            if (actionContext == null)
            {
                throw new Exception();
            }

            if (!string.IsNullOrEmpty(base.Roles))
            {
                bool isAuthorized = ValidateRoles(actionContext);
                if (!isAuthorized)
                {
                    HandleUnauthorizedRequest(actionContext);
                }
            }

            base.OnAuthorization(actionContext);
        }

public bool ValidateRoles(actionContext)
{
   //Authorization logic here; returns true or false
}

}
```
保護する必要があるすべてのコント ローラーおよびアクション メソッドは、上記の属性で修飾する必要があります。
```csharp
[ApiAuthorize]
public class CustomController : ApiController
{
     //Application code goes here
}
```

## <a name="perform-authorization-checks-in-the-device-if-it-supports-various-actions-that-require-different-permission-levels"></a><a id="device-permission"></a>さまざまなアクセス許可レベルを必要とする各種アクションをデバイスがサポートしている場合は、デバイスで承認チェックを実行する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | IoT デバイス | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | <p>デバイスは呼び出し元を承認して、要求された操作の実行に必要なアクセス許可が呼び出し元に付与されているかどうかを確認する必要があります。 たとえば、デバイスがスマート ドア ロックで、クラウドから監視できるとします。これには、リモート ドア ロックのような機能も用意されています。</p><p>スマート ドア ロックは、カードを持っている人が物理的にドアの近くにきた場合にのみ、ロック解除機能を提供します。 この場合、クラウド ゲートウェイには、ドアのロック解除コマンドを送信する権限が付与されていないため、ドアのロック解除機能が提供されないように、リモート コマンドとコントロールを実装する必要があります。</p>|

## <a name="perform-authorization-checks-in-the-field-gateway-if-it-supports-various-actions-that-require-different-permission-levels"></a><a id="field-permission"></a>さまざまなアクセス許可レベルを必要とする各種アクションをデバイスがサポートしている場合は、フィールド ゲートウェイで承認チェックを実行する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | IoT フィールド ゲートウェイ | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | フィールド ゲートウェイは呼び出し元を承認して、要求された操作の実行に必要なアクセス許可が呼び出し元に付与されているかどうかを確認する必要があります。 たとえば、管理ユーザー インターフェイス/API にはさまざまなアクセス許可があり、それを使用して、自身に接続されているフィールド ゲートウェイやデバイスを構成します。|