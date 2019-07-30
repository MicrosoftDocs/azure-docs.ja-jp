---
title: クラウド マーケットプレースのリード管理 | Azure Marketplace と AppSource
description: Azure Marketplace と AppSource へのオファーや技術的成果物の公開に関連するさまざまなトピックについて概説します
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: yijenj
ms.service: marketplace
ms.topic: article
ms.date: 10/05/2018
ms.author: pabutler
ms.openlocfilehash: a89921d4b5ec25bc0c924646d3bd41aecf3e8d9c
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67870923"
---
# <a name="lead-management-for-cloud-marketplace"></a>クラウド マーケットプレースのリード管理


優れたビジネスの中心は顧客です。 今日の製品入手の変革において、マーケティング担当者は顧客と直接つながり、関係を築くことに注力する必要があります。 これが、良質の潜在顧客を創出することが販売サイクルにとって重要なツールである理由です。 [Cloud パートナー ポータル](https://cloudpartner.azure.com/)でオファーをリストしたら、顧客がマーケットプレースの製品に関心を示したり、商品をデプロイした後すぐに、各種のツールを使用して、顧客の連絡先情報をプログラムで受け取ることができます。 



## <a name="what-are-leads-in-the-marketplace"></a>マーケットプレースのリードとは?

リードとは、Marketplace の製品に関心を示したり、それらの製品をデプロイしようとしている顧客から送られるものです。 CRM から Cloud パートナー ポータル内のリストに対して正しく設定されていれば、製品が Azure Marketplace と AppSource のどちらにリストされているかにかかわらず、顧客からのリードを取得することができます 


## <a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>CRM システムを Cloud パートナー ポータルに接続する方法

リードの取得を開始するには、Cloud パートナー ポータル上のリード管理コネクタを使用します。このコネクタは、CRM の情報を使用して、利用可能な CRM システムの一覧に簡単に接続できるように設計されています。 これにより、外部システムと統合するために多くのエンジニアリング作業を行わなくても、Marketplace で獲得したリードを簡単に活用できるようになります。

ここでは、リードの各送信先を接続する方法について、手順ごとに説明していきます。

**Dynamics CRM Online** - Dynamics CRM Online を構成してリードを取得する手順については、[ここをクリック](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)してください。

**Marketo** - Marketo Lead Configuration を設定してリードを取得する手順については、[ここをクリック](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo)してください。

**Salesforce** - Salesforce インスタンスを設定してリードを取得する手順については、[ここをクリック](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce)してください。

**Azure Table** - Azure テーブルにリードを取得するための Azure ストレージ アカウントを設定する手順については、[ここをクリック](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table)してください。

**Https エンドポイント** - Https エンドポイントを設定してリードを取得する手順については、[ここをクリック](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https)してください。

リードの送信先を適切に構成し、プランで [発行] をクリックすると、接続が検証され、テスト リードが送信されます。 公開前にプランを確認したときに、プレビュー環境でプランを自分で入手してみることで、リードの接続をテストすることもできます。 リードが失われないように、リード設定を最新の状態に保つことが重要です。そのため、パートナー側で変更を加えるたびに、これらの接続を必ず更新してください。


### <a name="what-are-the-next-steps"></a>次のステップ

技術的な設定が完了したら、これらのリードを現在の販売/マーケティング戦略と運用プロセスに組み込む必要があります。 Microsoft は、パートナーの販売プロセス全体について理解を深めることに関心を持っており、パートナーを成功に導くために、パートナーと緊密に連携して質の高いリードと十分なデータを提供していきたいと考えてます。 顧客を成功に導く上で役立つ追加情報と共に送信されるリードを最適化し、強化する方法についてフィードバックをお待ちしています。 Marketplace Leads によって販売チームがより大きな成果を挙げることができるようにするためのフィードバックや提案に関心がある場合は、ぜひお知らせください。



## <a name="common-lead-configuration-errors-during-publishing-on-cloud-partner-portal"></a>Cloud パートナー ポータルでの公開時に起こる一般的なリード構成エラー 

**Could not save the lead to Dynamics CRM. \(リードを Dynamics CRM に保存できませんでした。\)Check the Dynamics CRM account settings. \(Dynamics CRM のアカウント設定を確認してください。\)LastCRMError:Unable to sign in to Dynamics CRM, LastCRMException: (LastCRMError: Dynamics CRM LastCRMException にサインインできません:)** 

> O365 認証が選択されている場合は、ユーザー アカウントとパスワードが有効であることを確認してください。 AAD が選択されている場合は、テナント ID、アプリケーション ID およびアプリケーション シークレット キーが、AAD で設定されたものと一致するかどうか確認してください。 [こちら](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)の説明に従ってください。アカウントのユーザー名/パスワードが有効である場合は、Dynamics 365 へのアクセス権があることと、ライセンスが割り当てられていることを確認してください (Azure Active Directory を使用している場合は手順 11 ～ 15、Office ユーザーを使用している場合はセキュリティ設定)。 

 
**Could not save the lead to Dynamics CRM. \(リードを Dynamics CRM に保存できませんでした。\)User does not have create permissions for the leadsourcecode attribute in the lead entity (ユーザーにリード エンティティの leadsourcecode 属性の作成アクセス許可がありません)** 

> アプリケーション/ユーザーに、Microsoft Marketplace リード ライターへのセキュリティ ロールが割り当てられていません。 [こちら](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)の説明に従ってください (Azure Active Directory を使用している場合は手順 11 ～ 15、Office ユーザーを使用している場合はセキュリティ設定)。

**Could not save the lead to Dynamics CRM using AAD. \(AAD を使用してリードを Dynamics CRM に保存できませんでした。\)Exception::Tenant not found. (Exception:: テナントが見つかりません。)This instance may happen if there are no active subscriptions for the tenant. \(このインスタンスは、テナントに対するアクティブなサブスクリプションがない場合に発生する可能性があります。\)**  

> リード管理セクションで指定されたディレクトリ ID が、有効なディレクトリではありません。 手順 2 の説明に従って、ディレクトリ ID を取得してください ([こちら](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)の「Azure Active Directory」) 

**Could not save the lead to Dynamics CRM. \(リードを Dynamics CRM に保存できませんでした。\)LastCRMError:SecLib::RetrievePrivilegeForUser failed - no roles are assigned to user. (LastCRMError: SecLib::RetrievePrivilegeForUser が失敗しました - ユーザーにロールが割り当てられていません。)**  

> 解決策:Microsoft Marketplace リード ライターにセキュリティ ロールを割り当ててください。 [こちら](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)のセキュリティ設定の説明に従ってください 

**Could not save the lead to Dynamics CRM using AAD. \(AAD を使用してリードを Dynamics CRM に保存できませんでした。\)Exception::Application with identifier was not found in the directory (Exception:: 識別子を持つアプリケーションがディレクトリ内に見つかりませんでした)** 

> リード管理セクションで指定されたアプリケーション ID が、有効なディレクトリではありません。 手順 8 の説明に従って、ディレクトリ ID を取得してください ([こちら](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)の「Azure Active Directory」)。 

**Could not save the lead to Dynamics CRM using AAD. \(AAD を使用してリードを Dynamics CRM に保存できませんでした。\)Exception::Requested tenant identifier is not valid and not valid external domain format (Exception:: 要求されたテナント識別子は無効であり、有効な外部ドメイン形式ではありません)** 

> リード管理セクションで指定されたディレクトリ ID が、有効なディレクトリではありません。 手順 2 の説明に従って、ディレクトリ ID を取得してください ([こちら](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)の「Azure Active Directory」)。 

**Could not save the lead to Dynamics CRM using AAD. \(AAD を使用してリードを Dynamics CRM に保存できませんでした。\)Exception::Error validating credentials.:Exception:: Error validating credentials.: Invalid client secret is provided. (Exception:: 資格情報の検証中にエラーが発生しました。: 無効なクライアント シークレットが指定されています。)** 

> 解決策:Azure Portal にサインインし、アプリケーション キーが Cloud パートナー ポータルのものと一致しているかどうかチェックしてください。 手順 10 の説明に従って、パスワードを生成してください ([こちら](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)の「Azure Active Directory」)。 

**Could not save the lead to Dynamics CRM. \(リードを Dynamics CRM に保存できませんでした。\)LastCRMError:The request channel timed out while waiting for a reply after 00:02:00. (LastCRMError: 応答の待機中、00:02:00 後に要求チャネルがタイムアウトしました。)Increase the timeout value passed to the call to Request or increase the SendTimeout value on the Binding. \(要求への呼び出しに渡されるタイムアウト値を増やすか、バインドの SendTimeout 値を増やしてください。\)The time allotted to this operation may have been a portion of a longer timeout. \(この操作に割り当てられた時間は、より長いタイムアウト時間の一部であった可能性があります。\)**  

> 解決策:Cloud パートナー ポータルにサインインし、電子店舗の詳細 >> リードの送信先 >> URL をチェックして、有効な Dynamic CRM インスタンスであることを確認してください

## <a name="frequently-asked-questions"></a>よく寄せられる質問

**リードとは何ですか? また、Marketplace のパブリッシャーにとってリードが重要なのはなぜですか?** 

リードとは、Marketplace から製品をデプロイしょうとしている顧客のことです。 オファーに対してリードの送信先を設定すれば、製品が [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us) と [AppSource](https://appsource.microsoft.com/) のどちらにリストされていても、製品に関心を持っている顧客のリードを取得することができます。  


**リードの送信先の設定方法に関するヘルプはどこで入手できますか?** 

こちらのドキュメントをご覧ください:「[潜在顧客の獲得](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)」。または、aka.ms/marketplacepublishersupport からオファーの種類とリード管理を選択し、サポート チケットを送信してください。 



**Marketplace でオファーを公開するには、リードの送信先を構成する必要がありますか?**

はい (Contact Me SaaS アプリまたはコンサルティング サービスを公開する場合)。  
 


**リード構成が正しいことを確認するにはどうすればよいですか?**

オファーを設定し、リードの送信先を設定したら、オファーを公開します。 Marketplace では、リードの検証時に、オファーで構成されたリードの送信先へテスト リードが送信されます。 


**テスト リードを確認するにはどうすればよいですか?**


リードの送信先から "MSFT_TEST" を検索します。次に示すのは、テスト リード データのサンプルです。 

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

 

**ライブ オファーがあるのですが、リードが表示されません。**

各リードについては、選択したリード送信先のフィールドにデータが渡されます。リードは次の形式で送信されます:**Source-Action|Offer** 

  *ソース:*

    “AzureMarketplace”, 
    “AzurePortal”, 
    “TestDrive”,  
    “SPZA” (acronym for AppSource) 

  *アクション:*

    “INS” – Stands for Installation. This is on Azure Marketplace or AppSource whenever a customer hits the button to acquire your product. 
    “PLT” – Stands for Partner Led Trial. This is on AppSource whenever a customer hits the Contact me button. 

    “DNC” – Stands for Do Not Contact. This is on AppSource whenever a Partner who was cross listed on your app page gets requested to be contacted. We are sharing the heads up that this customer was cross listed on your app, but they do not need to be contacted. 

    “Create” – This is inside Azure Portal only and is whenever a customer purchases your offer to their account. 

    “StartTestDrive” – This is for Test Drives only and is whenever a customer starts their test drive. 


  *オファー:*

    “checkpoint.check-point-r77-10sg-byol”, 
    “bitnami.openedxcypress”, 
    “docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a” 

 

  *次に示すのは、顧客情報のサンプル データです*

    { 

    "FirstName":"John", 

    "LastName":"Smith", 

    "Email":"jsmith@microsoft.com", 

    "Phone":"1234567890", 

    "Country":"US", 

    "Company":"Microsoft", 

    "Title":"CTO" 

    } 

詳しくは、[リード情報](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)をご覧ください。 


**リードの送信先として Azure BLOB を構成したのですが、リードが表示されないのはなぜですか?** 

リードは、リードの送信先として Azure BLOB ストレージを選択した場合にのみ書き込まれます。 リードをリアルタイムに取得するには、Azure テーブルに切り替えてください 


**Marketplace からの電子メールを受信したのですが、CRM でリードを確認できないのはなぜですか?**  

エンドユーザーの電子メール ドメインが .edu である可能性があります。 プライバシー上の理由により、.edu ドメインからの PII データを渡すことはできません。 aka.ms/marketplacepublishersupport からサポート チケットを送信してください 


 **リードの送信先として Azure Table/Azure BLOB を構成したのですが、リードを表示するにはどうすればよいですか?** 

BLOB やテーブルには Azure Portal からアクセスできます。また、[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を無料でダウンロードしてインストールし、Azure Storage アカウントのテーブル/BLOB を表示することもできます。 


**リードの送信先として Azure Table を構成したのですが、Marketplace から新しいリードが送信された際に通知を受け取ることはできますか?** 

はい。[こちら](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table)のドキュメントの説明に従って、Azure Table と Azure Function を設定してください。 



**リードの送信先として Salesforce を構成したのですが、リードが見つからないのはなぜですか?** 

Web-to-リード フォームが候補リストに基づく必須フィールドになっているかどうかを確認してください。 なっている場合は、フィールドを任意指定のテキスト フィールドに切り替えてください。  
 

**リードの送信先に関して問題があり、一部のリードを取得できませんでした。リードを電子メールで受信することはできますか?** 

PII (個人を特定できる情報) ポリシーがあるため、セキュリティ保護されていない電子メールでリード情報を共有することはできません。 



**リードの送信先として Azure Storage (BLOB/Table) を構成したのですが、コストはどれくらいになりますか?** 

リード生成データは少量です (ほぼすべてのパブリッシャーで 1 GB 未満です)。 コストは受信したリードの数によって異なりますが、1 か月間に 1,000 件のリードを受信した場合、コストは約 50 セントになります。 
