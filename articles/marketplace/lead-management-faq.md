---
title: リード管理に関する問題と問題解決 - Microsoft パートナー センター
description: パートナー センターでコマーシャル マーケットプレースを構成するときに頻繁に発生するエラーと問題について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 10/01/2020
ms.openlocfilehash: 681498073a5f111e3babd50443623589ffeec02f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92330922"
---
# <a name="common-questions-and-troubleshooting-for-lead-configuration"></a>リード構成でよくある問題と問題解決

この記事では、コマーシャル マーケットプレース オファーのリード管理でよく寄せられる質問に回答します。 パートナー センターでカスタマー リレーションシップ マネジメント (CRM) システムのリードを構成するときに発生することがあるエラーも取り上げます。

## <a name="common-questions-about-lead-management"></a>リード管理についてよく寄せられる質問

#### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>リードの送信先の設定方法に関するヘルプはどこで入手できますか?

CRM システムをコマーシャル マーケットプレース オファーに接続する方法の概要については、「[コマーシャル マーケットプレース オファーからの顧客リード](partner-center-portal/commercial-marketplace-get-customer-leads.md)」を参照してください。 エラーが発生した場合、以下のトラブルシューティングのガイダンスを確認してください。 他にもサポートが必要な場合、[パートナー センターのヘルプとサポート](https://aka.ms/marketplacepublishersupport)を通じてサポート チケットを送信してください。 次に、 **[オファーの作成]**  >  **[オファーの種類]**  >  **[潜在顧客管理の構成]** を選択し します。

#### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>コマーシャル マーケットプレースでオファーを公開するには、リードのターゲットを構成する必要がありますか?

回答は、発行しているオファーの種類によって異なります。 サービスとしてのソフトウェア (SaaS) および Dynamics 365 for Customer Engagement では、 **[連絡する]** を使用してすべての Dynamics 365 for Finance and Operations オファー、すべての Dynamics 365 Business Central オファー、すべてのコンサルティング サービス オファーを表示します。 その結果、リードのターゲットに接続する必要があります。 オファーの種類が一覧に表示されていない場合は、リードのターゲットへの接続は必要ありません。 ビジネス チャンスを逃さないように、リードのターゲットを構成することをお勧めします。

#### <a name="how-can-i-find-the-test-lead"></a>テスト リードを確認するにはどうすればよいですか?

リードのターゲットで `"MSFT_TEST"` を検索します。 Microsoft のテスト リードの例を以下に示します。 テスト リードの形式は、リードのターゲットによって異なります。

```
{
    "userDetails": {
      "FirstName": "MSFT_TEST_636573304831318844",
      "LastName": "MSFT_TEST_636573304831318844",
      "Email": "MSFT_TEST_636573304831318844@test.com",
      "Phone": "1234567890",
      "Country": "US",
      "Company": "MSFT_TEST_636573304831318844",
      "Title": "MSFT_TEST_636573304831318844"
    },
    "LeadSource": "AzureMarketplace",
    "ActionCode": "INS",
    "OfferTitle": "Contoso Test"
    "Description": "MSFT_TEST_636573304831318844"
}
```

#### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>ライブ オファーがあるのですが、なぜリードが表示されないのですか?

リードのターゲットへの接続が有効であることを確認します。 パートナー センター内でオファーの **[発行]** を選択すると、Microsoft からテスト リードが送信されます。 テスト リードが表示された場合、接続は有効です。 また、プレビュー手順でオファーのプレビューの取得を試みることによっても、リードの接続をテストすることができます。 コマーシャル マーケットプレースの一覧にある、 **[今すぐ入手する]** 、 **[連絡する]** 、または、 **[無料試用版]** を選択します。

また、正しいデータを探していることを確認してください。 リード ターゲットに送信するリード データの説明については、「[リード データについて](partner-center-portal/commercial-marketplace-get-customer-leads.md)」を参照してください。

#### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>リードのターゲットとして Azure BLOB ストレージを構成したのですが、リードが表示されないのはなぜですか?

Azure BLOB ストレージはリードのターゲットとしてサポートされなくなったため、オファーによって生成された潜在顧客は失われています。 他のいずれかの[リードのターゲット オプション](partner-center-portal/commercial-marketplace-get-customer-leads.md)に切り替えてください。 

#### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>コマーシャル マーケットプレースからの電子メールを受信したのですが、CRM でリードを確認できないのはなぜですか?

エンドユーザーの電子メール ドメインが .edu である可能性があります。 プライバシー上の理由から、個人情報が .edu ドメインから渡されることはありません。 [パートナー センターのヘルプとサポート](https://aka.ms/marketplacepublishersupport)を通じてサポート チケットを送信します。

#### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Azure テーブルをリードのターゲットとして構成しました。 リードを表示するにはどうすればよいですか?

Azure テーブルに格納されているリード データには、Azure portal からアクセスできます。 また、zure Storage アカウントのテーブル データを表示するために [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を無料でダウンロードしてインストールすることもできます。

#### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>Azure テーブルをリードのターゲットとして構成しました。 新しいコマーシャル マーケットプレースのリードが送信されるたびに通知を受け取ることはできますか?

はい。 [Azure テーブルを使用したリード管理の構成](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md)に関するページの手順に従い、Azure テーブルにリードが追加された場合にメールを送信する Microsoft フローを設定します。

#### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>リードのターゲットとして Salesforce を構成したのですが、リードが見つからないのはなぜですか?

Web-to-Lead フォームが候補リストに基づく必須フィールドになっているかどうかを確認してください。 なっている場合は、フィールドを任意指定のテキスト フィールドに切り替えてください。

#### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>リードのターゲットに関して問題があり、一部のリードを取得できませんでした。 リードを電子メールで受信することはできますか?

個人情報に関するポリシーがあるため、セキュリティ保護されていない電子メールでリード情報を共有することはできません。

#### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Azure テーブルをリードのターゲットとして構成しました。 料金はいくらかかりますか?

リード生成データは小さいものです。 ほとんどすべてのパブリッシャーで 1 GB 未満です。 コストは、受信したリードの数によって異なります。 たとえば、1 か月に 1,000 のリードを受信した場合、コストは約 50 セントになります。 ストレージの価格の詳細については、「[Azure Storage の価格の概要](https://azure.microsoft.com/pricing/details/storage/)」を参照してください。

質問への回答がなかった場合は、[パートナー センターのヘルプとサポート](https://aka.ms/marketplacepublishersupport)から Microsoft サポートにお問い合わせください。 次に、 **[オファーの作成]**  >  **[オファーの種類]**  >  **[潜在顧客管理の構成]** を選択し します。

#### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>新しい顧客リードを受信したときに私宛に電子メール通知が届きます。 これらの電子メールを受信する別のユーザーを構成するにはどうすればよいですか?

パートナーセンターでオファーにアクセスします。 **[オファーのセットアップ]** ページに移動し、 **[リード管理]**  >  **[編集]** を選択します。 **[連絡先の電子メール]** フィールドの電子メール アドレスを更新します。

## <a name="troubleshooting-lead-configuration-errors"></a><a id="publishing-config-errors"></a> リード構成エラーのトラブルシューティング

**Could not save the lead to Dynamics CRM. \(リードを Dynamics CRM に保存できませんでした。\)Check the Dynamics CRM account settings. \(Dynamics CRM のアカウント設定を確認してください。\)LastCRMError:Unable to sign in to Dynamics CRM, LastCRMException: (LastCRMError: Dynamics CRM LastCRMException にサインインできません:)** 

> Microsoft 365 認証が選択されている場合は、ユーザー アカウントとパスワードが有効であることを確認してください。 Azure Active Directory が選択されている場合は、テナント ID、アプリケーション ID およびアプリケーション シークレット キーが、Azure Active Directory で設定されたものと一致するかどうか確認してください。 [こちら](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)で説明されている手順に従います。 アカウントのユーザー名/パスワードが有効である場合は、それに Dynamics 365 へのアクセス権があることと、ライセンスが割り当てられていることを確認してください (Azure Active Directory を使用している場合は手順 11. ～ 15.、Office ユーザーを使用している場合は「セキュリティ設定」)。 

**Could not save the lead to Dynamics CRM. \(リードを Dynamics CRM に保存できませんでした。\)User does not have create permissions for the leadsourcecode attribute in the lead entity (ユーザーにリード エンティティの leadsourcecode 属性の作成アクセス許可がありません)** 

> アプリケーション/ユーザーに、Microsoft Marketplace リード ライターへのセキュリティ ロールが割り当てられていません。 [こちら](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)の説明に従ってください (Azure Active Directory を使用している場合は手順 11 ～ 15、Office ユーザーを使用している場合は「セキュリティ設定」)。

**Could not save the lead to Dynamics CRM using AAD. \(AAD を使用してリードを Dynamics CRM に保存できませんでした。\)Exception::Tenant not found. (Exception:: テナントが見つかりません。)This instance may happen if there are no active subscriptions for the tenant. \(このインスタンスは、テナントに対するアクティブなサブスクリプションがない場合に発生する可能性があります。\)**  

> リード管理セクションで指定されたディレクトリ ID が、有効なディレクトリではありません。 手順 2 の説明に従って、ディレクトリ ID を取得してください ([こちら](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)の「Azure Active Directory」)。

**Could not save the lead to Dynamics CRM. \(リードを Dynamics CRM に保存できませんでした。\)LastCRMError:SecLib::RetrievePrivilegeForUser failed - no roles are assigned to user. (LastCRMError: SecLib::RetrievePrivilegeForUser が失敗しました - ユーザーにロールが割り当てられていません。)**  

> 解決策:Microsoft Marketplace リード ライターにセキュリティ ロールを割り当ててください。 [こちら](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)の「セキュリティ設定」の説明に従ってください。

**Could not save the lead to Dynamics CRM using AAD. \(AAD を使用してリードを Dynamics CRM に保存できませんでした。\)Exception::Application with identifier was not found in the directory (Exception:: 識別子を持つアプリケーションがディレクトリ内に見つかりませんでした)** 

> リード管理セクションで指定されたアプリケーション ID が、有効なディレクトリではありません。 手順 8 の説明に従って、ディレクトリ ID を取得してください ([こちら](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)の「Azure Active Directory」)。 

**Could not save the lead to Dynamics CRM using AAD. \(AAD を使用してリードを Dynamics CRM に保存できませんでした。\)Exception::Requested tenant identifier is not valid and not valid external domain format (Exception:: 要求されたテナント識別子は無効であり、有効な外部ドメイン形式ではありません)** 

> リード管理セクションで指定されたディレクトリ ID が、有効なディレクトリではありません。 手順 2 の説明に従って、ディレクトリ ID を取得してください ([こちら](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)の「Azure Active Directory」)。 

**Could not save the lead to Dynamics CRM using AAD. \(AAD を使用してリードを Dynamics CRM に保存できませんでした。\)Exception::Error validating credentials.:Exception:: Error validating credentials.: Invalid client secret is provided. (Exception:: 資格情報の検証中にエラーが発生しました。: 無効なクライアント シークレットが指定されています。)** 

> 解決策:Azure portal にサインインし、アプリケーション キーがパートナー センターの内容に一致しているかどうかを確認してください。 手順 10 の説明に従って、パスワードを生成してください ([こちら](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)の「Azure Active Directory」)。 

**Could not save the lead to Dynamics CRM. \(リードを Dynamics CRM に保存できませんでした。\)LastCRMError:The request channel timed out while waiting for a reply after 00:02:00. (LastCRMError: 応答の待機中、00:02:00 後に要求チャネルがタイムアウトしました。)Increase the timeout value passed to the call to Request or increase the SendTimeout value on the Binding. \(要求への呼び出しに渡されるタイムアウト値を増やすか、バインドの SendTimeout 値を増やしてください。\)The time allotted to this operation may have been a portion of a longer timeout. \(この操作に割り当てられた時間は、より長いタイムアウト時間の一部であった可能性があります。\)**  

> 解決策:パートナー センターにサインインし、[オファーのセットアップ] >> [Customer leads]\(顧客のリード\) >> [URL] の順に移動し、それが有効な Dynamic CRM インスタンスであるかどうかを確認してください。

