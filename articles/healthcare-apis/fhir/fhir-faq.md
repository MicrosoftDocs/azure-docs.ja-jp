---
title: Azure の FHIR サービスについての FAQ - Azure API for FHIR
description: FHIR API におけるデータの保存場所やバージョン サポートなど、Azure API for FHIR についてよく寄せられる質問とその回答を示します。
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 1/21/2021
ms.author: cavoeg
ms.openlocfilehash: 22d9df80bce6e79f0058567f8df76935e431e598
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019331"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>Azure API for FHIR についてよく寄せられる質問

## <a name="azure-api-for-fhir-the-basics"></a>Azure API for FHIR:基本操作

### <a name="what-is-fhir"></a>FHIR とは何でしょうか?
高速ヘルスケア相互運用性リソース (Fast Healthcare Interoperability Resources) は、略して FHIR ("ファイア" と発音) と呼ばれ、さまざまな医療システム間で医療データの交換を可能にする相互運用性標準です。 この標準は HL7 組織によって策定され、世界各国の医療機関で採用されています。 FHIR の最新バージョンは R4 (Release 4) です。 Azure API for FHIR では R4 がサポートされるほか、以前のバージョンの STU3 (Standard for Trial Use 3) もサポートされています。 FHIR の詳細については、[HL7.org](http://hl7.org/fhir/summary.html) のページを参照してください。

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>FHIR API のデータは Azure に格納されるのですか?

はい。データは Azure のマネージド データベースに格納されます。 Azure API for FHIR は、基になるデータ ストアへの直接アクセスを提供していません。

### <a name="what-identity-provider-do-you-support"></a>どの ID プロバイダーがサポートされますか?

現在当社は、ID プロバイダーとして Microsoft Azure Active Directory をサポートしています。

### <a name="what-is-the-recovery-point-objective-rpo-for-the-azure-api-for-fhir"></a>Azure API for FHIR の回復ポイントの目標 (RPO) はどれくらいですか?
Azure API for FHIR では、永続化プロバイダーとして Cosmos DB が使用されています。 このため、サービスの RPO は [Cosmos DB (単一リージョン)](../../cosmos-db/consistency-levels.md) と等しくなり、240 分未満です。

### <a name="what-fhir-version-do-you-support"></a>どのバージョンの FHIR がサポートされますか?

Azure API for FHIR (PaaS) と FHIR Server for Azure (オープンソース) では、どちらもバージョン 4.0.0 と 3.0.1 がサポートされます。

詳細については、「[サポートされている機能](fhir-features-supported.md)」を参照してください。 FHIR バージョン間 (つまり STU3 から R4) の変更点については、[HL7 FHIR のバージョン履歴](https://hl7.org/fhir/R4/history.html)に関するページを参照してください。

Azure IoT Connector for FHIR (プレビュー) は現在、FHIR バージョン R4 のみをサポートしており、Azure API for FHIR の R4 インスタンスでのみ表示されます。

### <a name="whats-the-difference-between-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>'Microsoft FHIR Server for Azure' と 'Azure API for FHIR' の違いは何ですか?

Azure API for FHIR は、オープンソースの Microsoft FHIR Server for Azure サーバーのホスト マネージド バージョンです。 マネージド サービスでは、メンテナンスや更新プログラムがすべて Microsoft によって提供されます。 

FHIR Server for Azure を実行するときに、基になるサービスに直接アクセスできますが、サーバーのメンテナンスや更新、PHI データを格納する場合はすべての必要なコンプライアンス作業も担当します。

開発の観点では、管理サービスだけに適用されないすべての機能はまず、オープンソースの Microsoft FHIR Server for Azure にデプロイされます。 オープンソースでの検証後、PaaS の Azure API for FHIR ソリューションにリリースされます。 オープンソース リリースから PaaS リリースまでの時間は、機能の複雑さやその他ロードマップ上の優先度によって異なります。 これは、Azure IoT Connector for FHIR (プレビュー) など、すべてのサービスで同じプロセスです。

### <a name="where-can-i-see-what-is-releasing-into-the-azure-api-for-fhir"></a>Azure API for FHIR にリリースされる内容はどこで確認できますか?

Azure API for FHIR にリリースされている内容の一部については、オープンソース FHIR サーバーの[リリース](https://github.com/microsoft/fhir-server/releases)を参照してください。 2020 年 11 月以降、オープンソース項目が管理サービスにリリースされる場合、項目に Azure-API-for-FHIR でタグ付けしています。 これらの機能は、通常、オープンソースのリリース ページに掲載された 2 週間後に利用できるようになります。 また、独自の環境でテストする場合に、[ここ] (https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) ) でビルドをテストする方法に関する手順も掲載しました。 追加の管理サービスの更新を共有するための最適な方法を評価しています。

### <a name="in-which-regions-is-azure-api-for-fhir-available"></a>Azure API for FHIR はどのリージョンで使用できますか?

現在、[複数の geo リージョン](https://azure.microsoft.com/global-infrastructure/services/?products=azure-api-for-fhir&regions=non-regional,us-east,us-east-2,us-central,us-north-central,us-south-central,us-west-central,us-west,us-west-2,canada-east,canada-central,usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia)で、パブリックと政府機関の両方に対して一般提供しています。 Microsoft の政府機関向けクラウド サービスの詳細については、[FedRAMP による Azure サービス](../../azure-government/compliance/azure-services-in-fedramp-auditscope.md)を確認してください。

### <a name="where-can-i-see-what-is-releasing-into-the-azure-api-for-fhir"></a>Azure API for FHIR にリリースされる内容はどこで確認できますか?

Azure API for FHIR にリリースされている内容の一部については、オープンソース FHIR サーバーの[リリース](https://github.com/microsoft/fhir-server/releases)を参照してください。 項目を管理サービスにリリースし、通常、オープンソースのリリース ページにそれらが掲載された 2 週間後に利用できるようにする場合に、Azure API for FHIR でそれらにタグ付けするようにしました。 また、独自の環境でテストする場合に、[ここに](https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md)ビルドをテストする方法に関する手順を掲載しました。 追加の管理サービスの更新を共有するための最適な方法を評価しています。

### <a name="what-is-smart-on-fhir"></a>SMART on FHIR とは何でしょうか?

SMART (Substitutable Medical Applications and Reusable Technology) on FHIR は、FHIR サーバーやその他の医療 IT システム (電子カルテ、医療情報交換など) にパートナー アプリケーションを統合するための一連のオープン仕様です。 SMART on FHIR アプリケーションを作成することにより、多数の異なるシステムから確実にそのアプリケーションにアクセスして活用することができます。
認証と Azure API for FHIR。 SMART の詳細については、[SMART Health IT](https://smarthealthit.org/) に関するページを参照してください。

### <a name="where-can-i-find-what-version-of-fhir-is-running-on-my-database"></a>データベースで実行されている FHIR のバージョンは、どこで確認できますか? 

機能ステートメントで公開されている正確な FHIR のバージョンは、"fhirVersion" プロパティで確認できます。

## <a name="fhir-implementations-and-specifications"></a>FHIR の実装と仕様

### <a name="can-i-create-a-custom-fhir-resource"></a>カスタム FHIR リソースを作成できますか?

カスタム FHIR リソースは使用できません。 カスタム FHIR リソースが必要な場合、拡張機能を使用して[基本的なリソース](http://www.hl7.org/fhir/basic.html)の上にカスタム リソースを構築できます。 

### <a name="are-extensions-supported-on-azure-api-for-fhir"></a>[拡張機能](https://www.hl7.org/fhir/extensibility.html)は、Azure API for FHIR でサポートされていますか?

有効な FHIR JSON データをサーバーに読み込むことができます。 拡張機能を定義する構造定義を格納する場合は、これを構造定義リソースとして保存できます。 現時点では、拡張機能を検索することはできません。

### <a name="what-is-the-limit-on-_count"></a>_count にはどのような制限がありますか?

_count の現在の上限は 100 です。 _count を 100 よりも大きい値に設定すると、バンドルに 100 レコードのみが表示されるという警告が表示されます。

### <a name="are-there-any-limitations-on-the-group-export-functionality"></a>グループ エクスポート機能に制限はありますか?

グループ エクスポートでは、[グループ リソース](https://www.hl7.org/fhir/group.html)のすべての特性ではなく、含まれている参照のみがグループからエクスポートされます。

### <a name="can-i-post-a-bundle-to-the-azure-api-for-fhir"></a>Azure API for FHIR にバンドルを投稿することはできますか?

現在、[バッチ バンドル](https://www.hl7.org/fhir/valueset-bundle-type.html)の投稿はサポートされていますが、Azure API for FHIR でのトランザクション バンドルの投稿はサポートされていません。 SQL によってサポートされているオープンソース FHIR サーバーを使用して、トランザクション バンドルを投稿できます。

### <a name="how-can-i-get-all-resources-for-a-single-patient-in-the-azure-api-for-fhir"></a>Azure API for FHIR で 1 人の患者のすべてのリソースを取得するにはどうすればよいですか?

Azure API for FHIR で、[コンパートメント検索](https://www.hl7.org/fhir/compartmentdefinition.html)をサポートしています。 これにより、特定の患者に関連するすべてのリソースを取得できます。 現時点で、コンパートメントには患者自体ではなく、患者に関連するすべてのリソースが含まれているため、結果に患者のリソースが必要な場合は、検索して、患者を取得する必要もあります。

このいくつかの例を次に示します。

* GET Patient/<id>/*
* GET Patient/<id>/Observation
* GET Patient/<id>/Observation?code=8302-2

### <a name="what-is-the-default-sort-when-searching-for-resources-in-azure-api-for-fhir"></a>Azure API for FHIR でリソースを検索するときの既定の並べ替えは何ですか?

最終更新日での並べ替えをサポートしています (_sort=_lastUpdated)。 サポートされているその他の検索パラメーターの詳細については、[サポートされている機能のページ](fhir-features-supported.md#search)を確認してください。

### <a name="does-the-azure-api-for-fhir-support-everything"></a>Azure API for FHIR で $everything はサポートされていますか? 

いいえ。 現時点では、$everything はサポートされていません。 ただし、2 つの API 呼び出しで実現できます。 たとえば、Patient$everything を取得するには、最初に /Patient/[ID] を使用して患者レコードを取得し、2 回目の呼び出しで /Patient/[ID]/* を使用してすべての患者データを取得します。

詳細については、こちらの[コミュニティの投稿](https://chat.fhir.org/#narrow/stream/179166-implementers/topic/.24everything.20with.20_type)を参照してください。 

### <a name="how-does-export-work"></a>$export はどのように動作しますか?

$export は、FHIR 仕様 (https://hl7.org/fhir/uv/bulkdata/export/index.html ) に含まれています。 FHIR サービスが管理対象 ID とストレージ アカウントで構成されていて、管理対象 ID がそのストレージ アカウントにアクセスできる場合、FHIR API で $export を呼び出すだけで、すべての FHIR リソースがストレージ アカウントにエクスポートされます。 詳細については、[$export の記事](export-data.md)を確認してください。

### <a name="is-de-identified-export-available-at-patient-and-group-level-as-well"></a>匿名化エクスポートは、患者レベルとグループ レベルで同様に使用できますか?
匿名化エクスポートは、現在、フル システム エクスポート (/$export) でのみサポートされており、患者のエクスポート (/Patient/$export) ではサポートされていません。 患者レベルでも使用できるようにする作業を行っています。

## <a name="using-azure-api-for-fhir"></a>Azure API for FHIR の使用

### <a name="how-do-i-enable-log-analytics-for-azure-api-for-fhir"></a>Azure API for FHIR のログ分析を有効にするにはどうすればよいですか?

診断ログを有効にし、これらのログのサンプル クエリを確認できるようにしています。 監査ログとサンプル クエリの有効化の詳細については、[このセクション](enable-diagnostic-logging.md)を確認してください。 ログに追加情報を含める場合は、[カスタム HTTP ヘッダーの使用](use-custom-headers.md)に関するページを確認してください。

### <a name="where-can-i-see-some-examples-of-using-the-azure-api-for-fhir-within-a-workflow"></a>ワークフロー内での Azure API for FHIR の使用例は、どこで確認できますか?

[正常性アーキテクチャ GitHub ページ](https://github.com/microsoft/health-architectures)に使用可能な参照アーキテクチャのコレクションがあります。

### <a name="where-can-i-see-an-example-of-connecting-a-web-application-to-azure-api-for-fhir"></a>Web アプリケーションを Azure API for FHIR に接続する例については、どこで見られますか?

アプリケーションとシナリオの例を含む、[正常性アーキテクチャの GitHub ページ](https://aka.ms/health-architectures)があります。 そこでは、Web アプリケーションを Azure API for FHIR に接続する方法を示しています。  

## <a name="azure-api-for-fhir-features-and-services"></a>Azure API for FHIR の機能とサービス 

### <a name="is-there-a-way-to-encrypt-my-data-using-my-personal-key-not-a-default-key"></a>既定のキーではなく個人用キーを使用してデータを暗号化する方法はありますか?

はい。Azure API for FHIR では、Cosmos DB のサポートを利用して、カスタマー マネージド キーを構成できます。 個人用キーでデータを暗号化する方法の詳細については、[このセクション](customer-managed-key.md)を確認してください。

## <a name="azure-api-for-fhir-preview-features"></a>Azure API for FHIR:プレビュー機能

### <a name="can-i-configure-scaling-capacity-for-azure-iot-connector-for-fhir-preview"></a>Azure IoT Connector for FHIR (プレビュー) の容量のスケーリングを構成できますか?

パブリック プレビュー期間中は Azure IoT Connector for FHIR が無料であるため、容量のスケーリングは固定され、制限されます。 パブリック プレビューで使用可能な Azure IoT Connector for FHIR 構成では、1 秒あたり約 200 メッセージというスループットが想定されています。 何らかの形式のリソース容量の構成が一般提供 (GA) で利用可能になります。

### <a name="why-cant-i-install-azure-iot-connector-for-fhir-preview-when-private-link-is-enabled-on-azure-api-for-fhir"></a>Azure API for FHIR で Private Link が有効になっている場合、Azure IoT Connector for FHIR (プレビュー) をインストールできないのはなぜですか?

現時点では、Azure IoT Connector for FHIR で Private Link 機能をサポートしていません。 このため、Azure API for FHIR で Private Link が有効になっている場合は、Azure IoT Connector for FHIR をインストールすることも、その逆を行うこともできません。 この制限事項は、Azure IoT Connector for FHIR が一般提供 (GA) で利用可能になると解消される予定です。
