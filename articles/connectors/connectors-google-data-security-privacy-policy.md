---
title: Google コネクタのデータ セキュリティとプライバシー ポリシー
description: Azure Logic Apps において、Google のセキュリティとプライバシー ポリシーが、Gmail などの Google コネクタに与える影響について説明します
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: conceptual
ms.date: 06/05/2020
ms.openlocfilehash: 2a5204be638f108b40e431b148c9cb97788c4a52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91400759"
---
# <a name="data-security-and-privacy-policies-for-google-connectors-in-azure-logic-apps"></a>Azure Logic Apps における Google コネクタのデータ セキュリティとプライバシー ポリシー

**2020 年 5 月 1 日** 以降、Google の [データ セキュリティとプライバシー ポリシー](https://www.blog.google/technology/safety-security/project-strobe/)による変更が、[Gmail コネクタ](/connectors/gmail/)を使用するロジック アプリのワークフローに影響を与える可能性があります。 ロジック アプリで Gmail コンシューマー アカウント (@gmail.com または @googlemail.com で終わる電子メール アドレス) を使用して Gmail コネクタを使用している場合、ロジック アプリで使用できるのは、特定の [Google によって承認されたトリガー、アクション、およびコネクタ](#approved-connectors)のみになります。

> [!NOTE]
> ロジック アプリで、G-Suite ビジネス アカウント (カスタム ドメインを使用した電子メール アドレス) を使用して Gmail コネクタを使用している場合、ロジック アプリは影響を受けず、Gmail コネクタの使用に関する制限はありません。

## <a name="affected-logic-apps"></a>影響を受けるロジック アプリ

Gmail コネクタを使用するロジック アプリがある場合は、影響を受ける可能性のあるロジック アプリに関する電子メールを送信します。 ただし、**2020 年 6 月 15 日** 以降、非対応のワークフローはすべて無効になります。 次のいずれかのアクションを取ることができます。

* [このトピックの手順に従って](#update-affected-workflows)、影響を受けるロジック アプリを更新します。 Gmail のトリガーまたはアクションで認証に使用するクライアント ID とクライアント シークレットを提供する Google クライアント アプリを作成する必要があります。

* 無効になっているロジック アプリを再度有効にする前に、[Google によって承認されたコネクタ](#approved-connectors)のみを使用するよう、影響を受けるロジック アプリを更新します。

<a name="approved-connectors"></a>

## <a name="google-approved-connectors"></a>Google によって承認されたコネクタ

このポリシーの下で Gmail コンシューマー アカウントを使用する場合、変更される可能性のある特定の Google によって承認されたサービスのみで Gmail コネクタを使用できます。 Microsoft のエンジニアリング チームは引き続き Google と協力して、この一覧にサービスを追加しています。 ここでは、Gmail コンシューマー アカウントを使用する場合に、Gmail コネクタと同じロジック アプリ ワークフローで使用できる、Google によって承認されたトリガー、アクション、コネクタを示します。

* Logic Apps の組み込みのトリガーとアクション:バッチ、コントロール、データの操作、日付/時刻、フラット ファイル、Liquid、要求、スケジュール、変数、XML

  アプリはどこからでもデータを送受信できるため、Google によって承認されていない組み込みのトリガーとアクション (HTTP、Azure Functions、Azure Logic Apps など) によって、ロジック アプリが Gmail コネクタに非準拠になります。

* Google サービス:Gmail、Google カレンダー、Google コンタクト、Google Drive、Google スプレッドシート、および Google Tasks

* 承認された Microsoft サービス:Dynamics 365、Excel Online、Microsoft Teams、Microsoft 365、OneDrive、および SharePoint Online

* カスタマー マネージド データ ソースのコネクタ:FTP、RSS、SFTP、SMTP、および SQL Server

## <a name="non-compliant-examples"></a>非準拠の例

Google によって承認されていない組み込みのトリガーとアクションまたはマネージド コネクタで Gmail コネクタを使用するいくつかの例を次に示します。

* このロジック アプリは、HTTP 組み込みトリガーで Gmail コネクタを使用します。

  ![非準拠のロジック アプリ - 例 1](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-1.png)
  
  このロジック アプリはまた、承認されている Google カレンダー コネクタも使用します。

* このロジック アプリは、Azure Blob Storage コネクタで Gmail コネクタを使用します。

  ![非準拠のロジック アプリ - 例 2](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-2.png)

* このロジック アプリは、Twitter コネクタで Gmail コネクタを使用します。

  ![非準拠のロジック アプリ - 例 3](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-3.png)

最新情報については、[Gmail コネクタのテクニカル リファレンス ドキュメント](/connectors/gmail/)を参照してください。

<a name="update-affected-workflows"></a>

## <a name="steps-for-affected-logic-apps"></a>影響を受けるロジック アプリの手順

Gmail コンシューマー アカウント、または Google によって承認されていないコネクタで Gmail コネクタをロジック アプリで使用する必要がある場合は、企業内で個人または社内で使用する独自の Google アプリを作成できます。 このシナリオにおいて実行する必要がある、大まかな手順は次のとおりです。

1. [Google API コンソール](https://console.developers.google.com)を使用して Google クライアント アプリを作成します。

1. Gmail コネクタで、Google クライアント アプリのクライアント ID とクライアント シークレットの値を使用します。

詳細については、[Gmail コネクタのテクニカル リファレンス ドキュメント](/connectors/gmail/#authentication-and-bring-your-own-application)を参照してください。

### <a name="create-google-client-app"></a>Google クライアント アプリを作成する

クライアント アプリのプロジェクトをセットアップするには、[Google API コンソール ウィザード](https://console.developers.google.com/start/api?id=gmail&credential=client_key)を使用して、指示に従います。 または、詳細な手順については、[Gmail コネクタのテクニカル リファレンス ドキュメント](/connectors/gmail/#authentication-and-bring-your-own-application)の手順を確認してください。

完了すると、独自の **クライアント ID** と **クライアント シークレット** の値を除き、画面は次の例のようになります。これらの値は、ロジック アプリで後ほど使用します。

![Google クライアント アプリのクライアント ID とクライアント シークレット](./media/connectors-google-data-security-privacy-policy/google-api-console.png)

### <a name="use-client-app-settings-in-logic-app"></a>ロジック アプリでクライアント アプリ設定を使用する

Gmail トリガーまたはアクションで Google クライアント アプリのクライアント ID とクライアント シークレットを使用するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます。

1. 新しい Gmail トリガーまたはアクションを追加し、まったく新しい接続を作成している場合は、次の手順に進みます。 それ以外の場合は、Gmail のトリガーまたはアクションで、 **[接続の変更]**  >  **[新規追加]** の順に選択します。たとえば、次のように指定します。

   ![[接続の変更] > [新規追加] の順に選択](./media/connectors-google-data-security-privacy-policy/change-gmail-connection.png)

1. 接続情報を指定します。次に例を示します。

   ![接続情報を指定する](./media/connectors-google-data-security-privacy-policy/authentication-type-bring-your-own.png)

   | プロパティ | 値 | 説明 |
   |----------|-------|-------------|
   | **認証の種類** | **アプリケーション持ち込み** | 認証に独自のクライアント アプリを使用することを指定します。 |
   | **クライアント ID** | <*client-ID*> | Google クライアント アプリからのクライアント ID |
   | **クライアント シークレット** | <*client-secret*> | Google クライアント アプリからのクライアント シークレット |
   ||||

1. 完了したら、 **[サインイン]** を選択します。

   作成したクライアント アプリを示すページが表示されます。 Gmail コンシューマー アカウントを使用している場合は、クライアント アプリが Google によって検証されていないことを示すページが表示され、最初に Google アカウントへのアクセスを許可するように求められます。

   ![Google アカウントへのアクセスを要求するプロンプト](./media/connectors-google-data-security-privacy-policy/allow-access-authorized-domain.png)

1. 必要に応じて、 **[許可]** を選択します。

   これで、ロジック アプリで制限なく Gmail コネクタを使用できるようになりました。

## <a name="next-steps"></a>次のステップ

[Gmail コネクタ](/connectors/gmail/)についての詳細情報

