---
title: コマーシャル マーケットプレースのリード管理 | Azure Marketplace と AppSource
description: Azure Marketplace と AppSource へのオファーや技術的成果物の公開に関連するさまざまなトピックについて概説します
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/14/2020
ms.openlocfilehash: b08324654a046b5a2296f6e533107c1b26a0e1fc
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88606891"
---
# <a name="lead-management-for-the-commercial-marketplace"></a>コマーシャル マーケットプレースのリード管理

優れたビジネスの中心は顧客です。 今日の製品入手の変革において、マーケティング担当者は顧客と直接つながり、関係を築くことに注力する必要があります。 これが、良質の潜在顧客を創出することが販売サイクルにとって重要なツールである理由です。 [パートナー センター](https://partner.microsoft.com/)でオファーを一覧表示した後、顧客がマーケットプレース内の製品に関心を示すか、またはその製品をデプロイしたらすぐに、その顧客の連絡先情報をプログラムで受信できるようになるツールがあります。 

## <a name="what-are-leads-in-the-marketplace"></a>マーケットプレースのリードとは?

リードとは、Marketplace の製品に関心を示したり、それらの製品をデプロイしようとしている顧客から送られるものです。 製品が Azure Marketplace または AppSource に一覧表示されているかどうかにかかわらず、それが CRM からパートナー センターの一覧に正しく設定されていれば、顧客からリードを受信できるようになります。 

## <a name="how-to-connect-your-crm-system-with-partner-center"></a>CRM システムをパートナー センターに接続する方法

リードの取得を開始するために、パートナー センターのリード管理コネクタは、使用可能な CRM システムの一覧に CRM 情報と共に容易に組み込むことができるように設計されています。 これにより、外部システムと統合するために多くのエンジニアリング作業を行わなくても、Marketplace で獲得したリードを簡単に活用できるようになります。

ここでは、リードの各送信先を接続する方法について、手順ごとに説明していきます。

**Dynamics CRM Online** - リードを取得するために Dynamics CRM Online を構成する方法については、「[Dynamics 365 for Customer Engagement のリード管理の構成](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)」を参照してください。

**Marketo** - リードを取得するために Marketo Lead Configuration を設定する手順については、「[Marketo でリード管理を構成する](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md)」を参照してください。

**Salesforce** - リードを取得するために Salesforce インスタンスを設定する手順については、「[Salesforce 用にリード管理を構成する](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md)」を参照してください。

**Azure テーブル** - Azure テーブルでリードを取得するために Azure Storage アカウントを設定する手順については、「[Azure テーブルを使用してリード管理を構成する](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md)」を参照してください。

**HTTPS エンドポイント** - リードを取得するために HTTPS エンドポイントを設定する手順については、「[HTTPS エンドポイントを使用して潜在顧客管理を構成する](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md)」を参照してください。

リードの送信先を適切に構成し、プランで [発行] をクリックすると、接続が検証され、テスト リードが送信されます。 公開前にプランを確認したときに、プレビュー環境でプランを自分で入手してみることで、リードの接続をテストすることもできます。 リードが失われないように、リード設定を最新の状態に保つことが重要です。そのため、パートナー側で変更を加えるたびに、これらの接続を必ず更新してください。

### <a name="what-are-the-next-steps"></a>次のステップ

技術的な設定が完了したら、これらのリードを現在の販売/マーケティング戦略と運用プロセスに組み込む必要があります。 Microsoft は、パートナーの販売プロセス全体について理解を深めることに関心を持っており、パートナーを成功に導くために、パートナーと緊密に連携して質の高いリードと十分なデータを提供していきたいと考えてます。 顧客を成功に導く上で役立つ追加情報と共に送信されるリードを最適化し、強化する方法についてフィードバックをお待ちしています。 Marketplace Leads によって販売チームがより大きな成果を挙げることができるようにするためのフィードバックや提案に関心がある場合は、ぜひお知らせください。

## <a name="common-lead-configuration-errors-during-publishing-in-partner-center"></a><a id="publishing-config-errors"></a> パートナー センターでの公開中の一般的なリード構成エラー

**Could not save the lead to Dynamics CRM. \(リードを Dynamics CRM に保存できませんでした。\)Check the Dynamics CRM account settings. \(Dynamics CRM のアカウント設定を確認してください。\)LastCRMError:Unable to sign in to Dynamics CRM, LastCRMException: (LastCRMError: Dynamics CRM LastCRMException にサインインできません:)** 

> O365 認証が選択されている場合は、ユーザー アカウントとパスワードが有効であることを確認してください。 AAD が選択されている場合は、テナント ID、アプリケーション ID およびアプリケーション シークレット キーが、AAD で設定されたものと一致するかどうか確認してください。 [こちら](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)で説明されている手順に従います。 アカウントのユーザー名/パスワードが有効である場合は、それに Dynamics 365 へのアクセス権があることと、ライセンスが割り当てられていることを確認してください (Azure Active Directory を使用している場合は手順 11. ～ 15.、Office ユーザーを使用している場合は「セキュリティ設定」)。 

**Could not save the lead to Dynamics CRM. \(リードを Dynamics CRM に保存できませんでした。\)User does not have create permissions for the leadsourcecode attribute in the lead entity (ユーザーにリード エンティティの leadsourcecode 属性の作成アクセス許可がありません)** 

> アプリケーション/ユーザーに、Microsoft Marketplace リード ライターへのセキュリティ ロールが割り当てられていません。 [こちら](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)の説明に従ってください (Azure Active Directory を使用している場合は手順 11 ～ 15、Office ユーザーを使用している場合はセキュリティ設定)。

**Could not save the lead to Dynamics CRM using AAD. \(AAD を使用してリードを Dynamics CRM に保存できませんでした。\)Exception::Tenant not found. (Exception:: テナントが見つかりません。)This instance may happen if there are no active subscriptions for the tenant. \(このインスタンスは、テナントに対するアクティブなサブスクリプションがない場合に発生する可能性があります。\)**  

> リード管理セクションで指定されたディレクトリ ID が、有効なディレクトリではありません。 手順 2. の説明に従って、ディレクトリ ID を取得してください ([こちら](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)の「Azure Active Directory」)。

**Could not save the lead to Dynamics CRM. \(リードを Dynamics CRM に保存できませんでした。\)LastCRMError:SecLib::RetrievePrivilegeForUser failed - no roles are assigned to user. (LastCRMError: SecLib::RetrievePrivilegeForUser が失敗しました - ユーザーにロールが割り当てられていません。)**  

> 解決策:Microsoft Marketplace リード ライターにセキュリティ ロールを割り当ててください。 [こちら](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)の「セキュリティ設定」の説明に従ってください。

**Could not save the lead to Dynamics CRM using AAD. \(AAD を使用してリードを Dynamics CRM に保存できませんでした。\)Exception::Application with identifier was not found in the directory (Exception:: 識別子を持つアプリケーションがディレクトリ内に見つかりませんでした)** 

> リード管理セクションで指定されたアプリケーション ID が、有効なディレクトリではありません。 手順 8 の説明に従って、ディレクトリ ID を取得してください ([こちら](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)の「Azure Active Directory」)。 

**Could not save the lead to Dynamics CRM using AAD. \(AAD を使用してリードを Dynamics CRM に保存できませんでした。\)Exception::Requested tenant identifier is not valid and not valid external domain format (Exception:: 要求されたテナント識別子は無効であり、有効な外部ドメイン形式ではありません)** 

> リード管理セクションで指定されたディレクトリ ID が、有効なディレクトリではありません。 手順 2 の説明に従って、ディレクトリ ID を取得してください ([こちら](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)の「Azure Active Directory」)。 

**Could not save the lead to Dynamics CRM using AAD. \(AAD を使用してリードを Dynamics CRM に保存できませんでした。\)Exception::Error validating credentials.:Exception:: Error validating credentials.: Invalid client secret is provided. (Exception:: 資格情報の検証中にエラーが発生しました。: 無効なクライアント シークレットが指定されています。)** 

> 解決策:Azure portal にサインインし、アプリケーション キーがパートナー センターの内容に一致しているかどうかを確認してください。 手順 10 の説明に従って、パスワードを生成してください ([こちら](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)の「Azure Active Directory」)。 

**Could not save the lead to Dynamics CRM. \(リードを Dynamics CRM に保存できませんでした。\)LastCRMError:The request channel timed out while waiting for a reply after 00:02:00. (LastCRMError: 応答の待機中、00:02:00 後に要求チャネルがタイムアウトしました。)Increase the timeout value passed to the call to Request or increase the SendTimeout value on the Binding. \(要求への呼び出しに渡されるタイムアウト値を増やすか、バインドの SendTimeout 値を増やしてください。\)The time allotted to this operation may have been a portion of a longer timeout. \(この操作に割り当てられた時間は、より長いタイムアウト時間の一部であった可能性があります。\)**  

> 解決策:パートナー センターにサインインし、[ネットショップの詳細] >> [リードの宛先] >> [URL] に移動し、それが有効な Dynamic CRM インスタンスであるかどうかを確認してください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

**リードとは何ですか? また、Marketplace のパブリッシャーにとってリードが重要なのはなぜですか?** 

リードとは、Marketplace から製品をデプロイしょうとしている顧客のことです。 オファーに対してリードの送信先を設定すれば、製品が [Azure Marketplace](https://azuremarketplace.microsoft.com) と [AppSource](https://appsource.microsoft.com/) のどちらにリストされていても、製品に関心を持っている顧客のリードを取得することができます。  

**リードの送信先の設定方法に関するヘルプはどこで入手できますか?** 

ドキュメントは、[潜在顧客の獲得](./partner-center-portal/commercial-marketplace-get-customer-leads.md)で見つけることができます。または[ヘルプとサポート](https://aka.ms/marketplacepublishersupport)でサポート チケットを送信します。 オファーの種類とリード管理を選択します。 

**Marketplace でオファーを公開するには、リードの送信先を構成する必要がありますか?**

はい (Contact Me SaaS アプリまたはコンサルティング サービスを公開する場合)。  

**リード構成が正しいことを確認するにはどうすればよいですか?**

オファーを設定し、リードの送信先を設定したら、オファーを公開します。 Marketplace では、リードの検証時に、オファーで構成されたリードの送信先へテスト リードが送信されます。 

**テスト リードを確認するにはどうすればよいですか?**

リードの送信先から "MSFT_TEST" を検索します。次に示すのは、テスト リード データのサンプルです。 

```text
company = MSFT_TEST_636573304831318844 

country = US 

description = MSFT_TEST_636573304831318844 

email = MSFT_TEST_636573304831318844@test.com

encoding = UTF-8 

encoding = UTF-8 

first_name = MSFT_TEST_636573304831318844 

last_name = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844|\<Offer Name> 

oid = 00Do0000000ZHog 

phone = 1234567890 

title = MSFT_TEST_636573304831318844 
```

**ライブ オファーがあるのですが、リードが表示されません。**

各リードについては、選択したリード送信先のフィールドにデータが渡されます。リードは次の形式で送信されます:**Source-Action|Offer**

- *ソース:*
  - AzureMarketplace
  - AzurePortal
  - TestDrive  
  - SPZA (AppSource の頭字語)

- *アクション:*
  - "INS" - インストールを表します。 Azure Marketplace または AppSource で、顧客が製品を入手するボタンをクリックしたときに適用されます。
  - "PLT" - パートナー主導の試用版 (Partner Led Trial) を表します。 AppSource で顧客が [Contact me (お問い合わせ)] をクリックしたときに適用されます。
  - "DNC" - 連絡不要を表します。 AppSource で、アプリ ページにクロスリストされたパートナーへの問い合わせが要求されたときに適用されます。 この顧客がアプリでクロスリストされたことが通知されますが、顧客に連絡する必要はありません。
  - "Create" - Azure portal 内でのみ適用されます。顧客がアカウントにプランを購入したときに適用されます。
  - "StartTestDrive" - 体験版専用です。顧客が体験版を起動したときに適用されます。

- *オファー:*
  - "checkpoint.check-point-r77-10sg-byol"、
  - "bitnami.openedxcypress"、
  - "docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a"

*次に示すのは、顧客情報のサンプル データです*

```json
{ 
"FirstName":"John",
"LastName":"Smith",
"Email":"jsmith@microsoft.com",
"Phone":"1234567890",
"Country":"US",
"Company":"Microsoft",
"Title":"CTO"
}
```

詳しくは、[リード情報](./partner-center-portal/commercial-marketplace-get-customer-leads.md)をご覧ください。 

**リードの送信先として Azure BLOB を構成したのですが、リードが表示されないのはなぜですか?** 

リードは、リードの送信先として Azure BLOB ストレージを選択した場合にのみ書き込まれます。 リードをリアルタイムに受信するには、Azure テーブルに切り替えます。

**Marketplace からの電子メールを受信したのですが、CRM でリードを確認できないのはなぜですか?**  

エンドユーザーの電子メール ドメインが .edu である可能性があります。 プライバシー上の理由から、個人を特定できるデータを .edu ドメインから渡すことはしていません。 [ヘルプとサポート](https://aka.ms/marketplacepublishersupport)でサポート チケットを送信します。

**リードの送信先として Azure Table/Azure BLOB を構成したのですが、リードを表示するにはどうすればよいですか?** 

Azure portal から BLOB またはテーブルにアクセスするか、あるいは Azure Storage アカウントのテーブル/BLOB を表示するために [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を無料でダウンロードしてインストールすることができます。 

**リードの送信先として Azure Table を構成したのですが、Marketplace から新しいリードが送信された際に通知を受け取ることはできますか?** 

はい。[こちら](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md)のドキュメントの説明に従って、Azure Table と Azure Function を設定してください。 

**リードの送信先として Salesforce を構成したのですが、リードが見つからないのはなぜですか?**

Web-to-リード フォームが候補リストに基づく必須フィールドになっているかどうかを確認してください。 なっている場合は、フィールドを任意指定のテキスト フィールドに切り替えてください。  
 
**リードの送信先に関して問題があり、一部のリードを取得できませんでした。リードを電子メールで受信することはできますか?** 

プライバシー ポリシーのために、セキュリティで保護されていない電子メール経由でリード情報を共有することはできません。 

**リードの送信先として Azure Storage (BLOB/Table) を構成したのですが、コストはどれくらいになりますか?** 

リード生成データは少量です (ほぼすべてのパブリッシャーで 1 GB 未満です)。 コストは受信したリードの数によって異なりますが、1 か月間に 1,000 件のリードを受信した場合、コストは約 50 セントになります。 
