---
title: Azure Active Directory B2C を使用して N8 Identity を構成するためのチュートリアル
titleSuffix: Azure AD B2C
description: 顧客アカウントの移行とカスタマー サービス要求 (CSR) の管理に対処するために Azure Active Directory B2C を使用して TheAccessHub 管理ツールを構成するためのチュートリアル。
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/26/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 337275cef0f2159cb5fac40ac0435408baf3bbef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96170924"
---
# <a name="tutorial-for-configuring-theaccesshub-admin-tool-with-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して TheAccessHub 管理ツールを構成するためのチュートリアル

このサンプル チュートリアルでは、Azure Active Directory (AD) B2C を N8 Identity の [TheAccessHub 管理ツール](https://n8id.com/products/theaccesshub-admintool/)と統合する方法についてのガイダンスを提供します。 このソリューションでは、顧客アカウントの移行とカスタマー サービス要求 (CSR) の管理に対処します。  

このソリューションは、次の要件の 1 つ以上に該当する場合に適しています。

- 既存のサイトがあり、Azure AD B2C に移行しようとしている。 しかし、パスワードなどの顧客アカウントの移行に苦労している。

- Azure AD B2C アカウントを管理するために CSR 用のツールを必要としている。

- CSR に代理管理を使用する必要がある。

- 多くのリポジトリのデータを同期して Azure AD B2C にマージしようとしている。

## <a name="pre-requisites"></a>前提条件

作業を開始するには、以下が必要です。

- Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。

- [Azure AD B2C テナント](./tutorial-create-tenant.md)。 テナントは Azure サブスクリプションにリンクされている必要があります。

- TheAccessHub 管理ツール環境:新しい環境をプロビジョニングするには、[N8 Identity](https://n8id.com/contact/) に問い合わせてください。

- [省略可能] 顧客データを移行するすべてのデータベースまたはライトウェイト ディレクトリ アクセス プロトコル (LDAP) の接続および資格情報。

- [省略可能] TheAccessHub 管理ツールをサインアップ ポリシー フローに統合する場合は、[カスタム ポリシー](./custom-policy-get-started.md)を使用するように構成された Azure AD B2C 環境。

## <a name="scenario-description"></a>シナリオの説明

TheAccessHub 管理ツールは、Azure 内の他のアプリケーションと同様に動作します。 N8 Identity の Azure サブスクリプション、または顧客のサブスクリプションのいずれかで実行できます。 次のアーキテクチャの図に、この実装を示します。

![n8identity のアーキテクチャ図を示す画像](./media/partner-n8identity/n8identity-architecture-diagram.png)

|手順 | 説明 |
|:-----| :-----------|
| 1. | ユーザーがログイン ページにアクセスします。 ユーザーがサインアップを選択して新しいアカウントを作成し、そのページに情報を入力します。 Azure AD B2C でユーザー属性が収集されます。
| 2. | Azure AD B2C でTheAccessHub 管理ツールが呼び出され、ユーザー属性が渡されます。
| 3. | TheAccessHub 管理ツールが、既存のデータベースで現在のユーザー情報を確認します。  
| 4. | ユーザー レコードがデータベースから TheAccessHub 管理ツールに同期されます。
| 5. | TheAccessHub 管理ツールが、代行の CSR/ヘルプデスク管理者とデータを共有します。
| 6. | TheAccessHub 管理ツールが Azure AD B2C とユーザー レコードを同期します。
| 7. |TheAccessHub 管理ツールからの成功/失敗応答に基づいて、Azure AD B2C がカスタマイズされたウェルカム メールをユーザーに送信します。

## <a name="create-a-global-admin-in-your-azure-ad-b2c-tenant"></a>Azure AD B2C テナントでグローバル管理者を作成する

TheAccessHub 管理ツールで Azure AD B2C テナントのユーザー情報を読み取り、変更を行うためには、全体管理者の代理として機能するためのアクセス許可が必要です。 通常の管理者に対する変更は、TheAccessHub 管理ツールがテナントと対話する機能に影響を与えません。

全体管理者を作成するには、こちらの手順を実行します。

1. Azure portal で、Azure AD B2C テナントに管理者としてサインインします。 **[Azure Active Directory]**  >  **[ユーザー]** に移動します
2. **[新しいユーザー]** を選択します
3. 顧客ではなく通常のディレクトリ ユーザーを作成するために、 **[ユーザーの作成]** を選択します
4. ID 情報フォームを完成させます

   a. ‘theaccesshub’ のようなユーザー名を入力します

   b. ‘TheAccessHub Service Account’ のような名前を入力します

5. **[パスワードの表示]** を選択し、後で使用するために初期パスワードをコピーします
6. 全体管理者ロールを割り当てます

   a. ユーザーの現在のロール **[ユーザー]** を選択して変更します

   b. 全体管理者のレコードを確認クします

   c.  >  **[作成]** を選択します

## <a name="connect-theaccesshub-admin-tool-with-your-azure-ad-b2c-tenant"></a>TheAccessHub 管理ツールを Azure AD B2C テナントに接続する

TheAccessHub 管理ツールは Microsoft の Graph API を使用して、ディレクトリを読み取り、変更を加えます。 テナントの全体管理者として機能します。 TheAccessHub 管理ツールには追加のアクセス許可が必要で、これはツール内で付与できます。

TheAccessHub 管理ツールがディレクトリにアクセスすることを承認するには、こちらの手順を実行します。

1. N8 Identity によって提供される資格情報を使用して TheAccessHub 管理ツールにログインします

2. **[System Admin]\(システム管理者\)**  >  **[Azure AD B2C Config]\(Azure AD B2C 構成\)** に移動します

3. **[接続の承認]** を選択します

4. 新しいウィンドウで、全体管理者アカウントでサインインします。 新しいサービス アカウントで初めてサインインする場合、パスワードのリセットを求められることがあります。

5. プロンプトに従って **[同意する]** を選択し、要求されたアクセス許可を TheAccessHub 管理ツールに付与します。

## <a name="configure-a-new-csr-user-using-your-enterprise-identity"></a>エンタープライズ ID を使用して新しい CSR ユーザーを構成する

既存のエンタープライズ Azure Active Directory 資格情報を使用して、TheAccessHub 管理ツールにアクセスする CSR/ヘルプデスク ユーザーを作成します。

シングル サインオン (SSO) を使用して CSR/ヘルプデスク ユーザーを構成するには、次の手順を実行することをお勧めします。

1. N8 Identity によって提供される資格情報を使用して TheAccessHub 管理ツールにログインします。

2. **[Manager Tools]\(マネージャー ツール\)**  >  **[Manage Colleagues]\(仕事仲間の管理\)** に移動します

3. **[Add Colleague]\(仕事仲間の追加\)** を選択します

4. **[Colleague Type Azure Administrator]\(仕事仲間の種類 Azure 管理者\)** を選択します

5. 仕事仲間のプロファイル情報を入力します

   a. [ホーム組織] を選択すると、このユーザーを管理するアクセス許可を持つユーザーが制御されます。

   b. [Login ID/Azure AD User Name]\(ログイン ID/Azure AD ユーザー名\) にユーザーの Azure Active Directory アカウントのユーザー プリンシパル名を入力します。

   c. [TheAccessHub Roles]\(TheAccessHub ロール\) タブで、マネージド ロールの [Helpdesk]\(ヘルプデスク\) をチェックします。 これにより、ユーザーは [Manage Colleagues]\(仕事仲間の管理\) ビューにアクセスできるようになります。 このユーザーが顧客を操作するにはさらに、このユーザーをグループに配置するか組織所有者にする必要があります。

6. **[Submit]\(送信\)** をクリックします。

## <a name="configure-a-new-csr-user-using-a-new-identity"></a>新しい ID を使用して新しい CSR ユーザーを構成する

TheAccessHub 管理ツールに固有の新しいローカル資格情報を使用して、TheAccessHub 管理ツールにアクセスする CSR/ヘルプデスク ユーザーを作成します。 これは Azure AD を使用していない企業の組織によって主に使用されます。

SSO を使用せずに [CSR/ヘルプデスク ユーザーをセットアップ](https://youtu.be/iOpOI2OpnLI)するには、こちらの手順を実行します。

1. N8 Identity によって提供される資格情報を使用して TheAccessHub 管理ツールにログインします

2. **[Manager Tools]\(マネージャー ツール\)**  >  **[Manage Colleagues]\(仕事仲間の管理\)** に移動します

3. **[Add Colleague]\(仕事仲間の追加\)** を選択します

4. **[Colleague Type Local Administrator]\(仕事仲間の種類ローカル管理者\)** を選択します

5. 仕事仲間のプロファイル情報を入力します

   a. [ホーム組織] を選択すると、このユーザーを管理するアクセス許可を持つユーザーが制御されます。

   b. **[TheAccessHub Roles]\(TheAccessHub ロール\)** タブで、マネージド ロールの **[Helpdesk]\(ヘルプデスク\)** をチェックします。 これにより、ユーザーは [Manage Colleagues]\(仕事仲間の管理\) ビューにアクセスできるようになります。 このユーザーが顧客を操作するにはさらに、このユーザーをグループに配置するか組織所有者にする必要があります。

6. **[Login ID/Email]\(ログイン ID/電子メール\)** と **[One Time Password]\(ワンタイム パスワード\)** の属性をコピーします。 これを新しいユーザーに提供します。 これらの資格情報は、TheAccessHub 管理ツールへのログインに使用されます。 ユーザーは、最初のログイン時に新しいパスワードを入力するように求められます。

7. **[送信]** を選択します

## <a name="configure-partitioned-csr-administration"></a>パーティション分割された CSR の管理を構成する

TheAccessHub 管理ツールで顧客および CSR/ヘルプデスク ユーザーを管理するためのアクセス許可は、組織階層を使用して管理されます。 すべての仕事仲間と顧客には、所属するホーム組織があります。 特定の仕事仲間または仕事仲間のグループを、組織の所有者として割り当てることができます。  組織の所有者は、自分が所有する組織やサブ組織の仕事仲間や顧客を管理 (変更) できます。 複数の仕事仲間が一連のユーザーを管理できるようにするために、多数のメンバーを含むグループを作成できます。 その後、そのグループを組織所有者として割り当てることができ、グループのすべてのメンバーは組織内の仕事仲間や顧客を管理できます。

### <a name="create-a-new-group"></a>新しいグループを作成する

1. N8 Identity によって提供される **資格情報** を使用して TheAccessHub 管理ツールにログインします。

2. **[Organization]\(組織\) > [Manage Groups]\(グループの管理\)** に移動します。

3. **[Add Group]\(グループの追加\)** を選択します。

4. **[Group name]\(グループ名\)** 、 **[Group description]\(グループの説明\)** 、および **[Group owner]\(グループ所有者\)** を入力します。

5. グループのメンバーにする仕事仲間のチェック ボックスを見つけてオンにし、 **[Add]\(追加\)** を選択します。

6. ページの下部には、グループのすべてのメンバーが表示されます。

7. 必要に応じて、行の末尾にある **[x]** を選択してメンバーを削除できます。

8. **[送信]** を選択します

### <a name="create-a-new-organization"></a>新しい組織を作成する

1. N8 Identity によって提供される資格情報を使用して TheAccessHub 管理ツールにログインします

2. [Organization]\(組織\) > **[Manage Organizations]\(組織の管理\)** に移動します。

3. **[Add Organization]\(組織の追加\)** を選択します。

4. **[Organization name]\(組織名\)** 、 **[Organization owner]\(組織所有者\)** 、 **[Parent organization]\(親組織\)** を指定します。

    a. 組織名は、顧客データに対応する値にするのが理想的です。 仕事仲間や顧客データを読み込むときに、読み込みに組織の名前を指定すると、仕事仲間が組織に自動的に配置されます。

    b. 所有者は、この組織およびその中のすべてのサブ組織の顧客と仕事仲間を管理するユーザーまたはグループを表します。

    c. 親組織は、本質的にこの組織に対する責任も有する他の組織を意味します。

5. **[Submit]\(送信\)** をクリックします。

### <a name="modify-the-hierarchy-via-the-tree-view"></a>ツリー ビューを使用して階層を変更する

1. N8 Identity によって提供される資格情報を使用して TheAccessHub 管理ツールにログインします

2. **[Manager Tools]\(マネージャー ツール\)**  >  **[Tree View]\(ツリー ビュー\)** に移動します。

3. この表示では、どの仕事仲間やグループがどの組織を管理できるかを視覚化できます。

4. 階層は、親として使用する組織の上に組織をドラッグすることによって変更できます。

5. 階層の変更が完了したら、 **[保存]** を選択します。

## <a name="customize-welcome-notification"></a>ウェルカム通知をカスタマイズする

TheAccessHub を使用して、以前の認証ソリューションのユーザーを Azure AD B2C に移行している間に、移行中に TheAccessHub によってユーザーに送信されるユーザーのウェルカム通知をカスタマイズすることができます。 このメッセージには通常、顧客が Azure AD B2C ディレクトリに新しいパスワードを設定するためのリンクが含まれています。

通知をカスタマイズするには、次のようにします。

1. N8 Identity によって提供される資格情報を使用して TheAccessHub にログインします

2. **[System Admin]\(システム管理者\)**  >  **[Notifications]\(通知\)** に移動します

3. **[Create Colleague template]\(仕事仲間テンプレートを作成する\)** を選択します

4. **[編集]** を選択します。

5. 必要に応じて、メッセージとテンプレートのフィールドを変更します。 テンプレート フィールドは HTML 対応で、HTML 形式の通知を顧客の電子メールに送信できます。

6. 終了したら、 **[保存]** を選択します。

## <a name="migrate-data-from-external-data-sources-to-azure-ad-b2c"></a>外部データソースから Azure AD B2Cにデータを移行する

TheAccessHub 管理ツールを使用すると、さまざまなデータベース、LDAP、CSV ファイルからデータをインポートし、そのデータを Azure AD B2C テナントにプッシュできます。 これを行うには、TheAccessHub 管理ツール内で Azure AD B2C ユーザーの仕事仲間の種類にデータを読み込みます。  データのソースが Azure でない場合、データは TheAccessHub 管理ツールと Azure AD B2C の両方に配置されます。 外部データのソースがコンピューター上の単純な .csv ファイルでない場合は、データ読み込みを実行する前にデータ ソースを設定します。 下の手順では、データ ソースの作成とデータの読み込みについて説明します。

### <a name="configure-a-new-data-source"></a>新しいデータ ソースを構成する

1. N8 Identity によって提供される資格情報を使用して TheAccessHub 管理ツールにログインします

2. **[System Admin]\(システム管理者\)**  >  **[Data Sources]\(データ ソース\)** に移動します

3. **[Add Data Source]\(データ ソースの追加\)** を選択します

4. このデータ ソースの **[Name]\(名前\)** と **[Type]\(種類\)** を指定します

5. データ ソースの種類に応じてフォーム データを入力します。

   **データベースの場合**

   a. **[Type]\(種類\)** – データベース

   b. **[Database type]\(データベースの種類\)** – サポートされているデータベースの種類のいずれかからデータベースを選択します。

   c. **[Connection URL]\(接続 URL\)** – 適切に書式設定された JDBC 接続文字列を入力します。 (例: ``jdbc:postgresql://myhost.com:5432/databasename``)。

   d. **[Username]\(ユーザー名\)** – データベースにアクセスするためのユーザー名を入力します

   e. **[Password]\(パスワード\)** – データベースにアクセスするためのパスワードを入力します

   f. **[Query]\(クエリ\)** – 顧客の詳細を抽出する SQL クエリを入力します。 (例: ``SELECT * FROM mytable;``)。

   g. **[Test Connection]\(テスト接続\)** を選択すると、接続が機能していることを確認するためのデータのサンプルが表示されます。

   **LDAP の場合**

   a. **[Type]\(種類\)** – LDAP

   b. **[Host]\(ホスト\)** – LDAP サーバーが実行されているコンピューターのホスト名または IP アドレスを入力します。 (例: ``mysite.com``)。

   c. **[Port]\(ポート\)** – LDAP サーバーがリッスンしているポート番号を入力します。

   d. **[SSL]** – TheAccessHub 管理ツールが SSL を使用して LDAP に安全に通信する必要がある場合は、このチェック ボックスをオンにします。 SSL を使用することを強くお勧めします。

   e. **[Login DN]\(ログイン DN\)** – ログインして LDAP 検索を実行するユーザー アカウントの DN を入力します

   f. **[Password]\(パスワード\)** – ユーザーのパスワードを入力します

   g. **[Base DN]\(ベース DN\)** –検索を実行する階層の最上位の DN を入力します

   h. **[Filter]\(フィルター\)** – 顧客レコードを取得する LDAP フィルター文字列を入力します

   i. **[Attributes]\(属性\)** – TheAccessHub 管理ツールに渡す顧客レコードの属性のコンマ区切りリストを入力します

   j. **[Test Connection]\(テスト接続\)** を選択すると、接続が機能していることを確認するためのデータのサンプルが表示されます。

   **OneDrive の場合**

   a. **[Type]\(種類\)** – OneDrive for Business

   b. **[Authorize Connection]\(接続の承認\)** を選択します

   c. 新しいウィンドウで **OneDrive** にログインするように求められ、OneDrive アカウントに対する読み取りアクセス権を持つユーザーを使用してログインするように求められます。 TheAccessHub 管理ツールは、CSV 読み込みファイルを読み取るようにこのユーザーのために動作します。

   d. プロンプトに従って **[同意する]** を選択し、要求されたアクセス許可を TheAccessHub 管理ツールに付与します。

6. 終了したら、 **[保存]** を選択します。  

### <a name="synchronize-data-from-your-data-source-into-azure-ad-b2c"></a>データ ソースから Azure AD B2C にデータを同期する

1. N8 Identity によって提供される資格情報を使用して TheAccessHub 管理ツールにログインします

2. **[System Admin]\(システム管理者\)**  >  **[Data Synchronization]\(データ同期\)** に移動します

3. **[New Load]\(新しい読み込み\)** を選択します

4. **[Colleague Type]\(仕事仲間の種類\)** で Azure AD B2C ユーザーを選択します

5. **[Source]\(ソース\)** を選択し、ポップアップ ダイアログでデータ ソースを選択します。 OneDrive データ ソースを作成した場合は、ファイルも選択します。

6. この読み込みを使用して新しい顧客アカウントを作成しない場合は、最初のポリシー　 **[IF colleague not found in TheAccessHub THEN]\(仕事仲間が TheAccessHub に見つからない場合\)** を **[Do Nothing]\(何もしない\)** に変更します。

7. この読み込みを使用して既存の顧客アカウントを更新しない場合は、2 番目のポリシー **[IF source and TheAccessHub data mismatch THEN]\(ソースと TheAccessHub のデータが一致しない場合\)** を **[Do Nothing]\(何もしない\)** に変更します。

8. **[次へ]** を選択します

9. **[Search-Mapping configuration]\(検索マッピングの構成\)** では、TheAccessHub 管理ツールに既に読み込まれている顧客と、読み込みレコードを関連付ける方法を確認します。 ソース内の 1 つまたは複数の識別属性を選択します。 この属性を、同じ値を保持する TheAccessHub 管理ツールの属性と照合します。 一致するものが見つかった場合は、既存のレコードが上書きされます。それ以外の場合は、新しい顧客が作成されます。 複数のチェックを順に行うことができます。 たとえば、最初に電子メールを確認し、次に姓と名を確認できます。

10. 左側のメニューで **[Data Mapping]\(データ マッピング\)** を選択します。

11. データ マッピング構成で、ソース属性から設定する TheAccessHub 管理ツールの属性を割り当てます。 すべての属性をマップする必要はありません。 マップされない属性は、既存の顧客に対して変更されません。

12. 既存の組織の名前である値を org_name 属性にマップすると、作成された新しい顧客がその組織に配置されます。

13. **[次へ]** を選択します

14. この読み込みを繰り返し実行させる必要がある場合は、毎日/毎週または月単位のスケジュールを指定できます。 それ以外の場合は、既定の **[Now]\(今\)** にしておきます。

15. **[送信]** を選択します

16. **[Now]\(今\) のスケジュール** が選択された場合、新しいレコードがデータ同期画面にすぐに追加されます。 検証フェーズが 100% に達したら、**新しいレコード** を選択して、読み込みの予想される結果を確認します。 スケジュールされた読み込みの場合、これらのレコードはスケジュールされた時刻より後にのみ表示されます。

17. エラーがない場合は、 **[Run]\(実行\)** を選択して変更をコミットします。 それ以外の場合は、 **[More]\(その他\)** メニューから **[Remove]\(削除\)** を選択して読み込みを削除します。 その後、ソース データを修正するか、マッピングを読み込んで、もう一度やり直してください。 エラーの数が少ない場合は、代わりに手動でレコードを更新し、各レコードの **[Update]\(更新\)** を選択して修正できます。 最終的に、エラーのあるまま続行し、TheAccessHub 管理ツールで **サポート介入** として後でエラーを解決できます。

18. 読み込みフェーズで **データ同期** レコードが 100% になると、その読み込みの結果として生じたすべての変更が開始されています。 Azure AD B2C で顧客の表示と変更の受信が開始されます。

## <a name="synchronize-azure-ad-b2c-customer-data"></a>Azure AD B2C の顧客データを同期する 

TheAccessHub 管理ツールは、1 回限りまたは継続的な操作として、Azure AD B2C のすべての顧客情報を TheAccessHub 管理ツールに同期できます。 これにより、CSR/ヘルプデスクの管理者は最新の顧客情報を確認できます。

Azure AD B2C 顧客データを TheAccessHub 管理ツールに同期するには、次のようにします。

1. N8 Identity によって提供される資格情報を使用して TheAccessHub 管理ツールにログインします

2. **[System Admin]\(システム管理者\)**  >  **[Data Synchronization]\(データ同期\)** に移動します

3. **[New Load]\(新しい読み込み\)** を選択します

4. **[Colleague Type]\(仕事仲間の種類\)** で Azure AD B2C ユーザーを選択します

5. **[Options]\(オプション\)** 手順では、既定値のままにします。

6. **[次へ]** を選択します

7. **[Data Mapping & Search]\(データ マッピングおよび検索\)** 手順では、既定値のままにします。 既存の組織の名前である値を **org_name** 属性にマップした場合を除き、作成された新しい顧客がその組織に配置されます。

8. **[次へ]** を選択します

9. この読み込みを繰り返し実行させる必要がある場合は、毎日/毎週または月単位のスケジュールを指定できます。 それ以外の場合は、既定の **[Now]\(今\)** にしておきます。 Azure AD B2C から定期的に同期することをお勧めします。

10. **[送信]** を選択します

11. **[Now]\(今\)** のスケジュールが選択された場合、新しいレコードがデータ同期画面にすぐに追加されます。 検証フェーズが 100% に達したら、新しいレコードを選択して、読み込みの予想される結果を確認します。 スケジュールされた読み込みの場合、これらのレコードはスケジュールされた時刻より後にのみ表示されます。

12. エラーがない場合は、 **[Run]\(実行\)** を選択して変更をコミットします。 それ以外の場合は、[More]\(その他\) メニューから **[Remove]\(\削除)** を選択して読み込みを削除します。 その後、ソース データを修正するか、マッピングを読み込んで、もう一度やり直してください。 エラーの数が少ない場合は、代わりに手動でレコードを更新し、各レコードの **[Update]\(更新\)** を選択して修正できます。 最終的に、エラーのあるまま続行し、TheAccessHub 管理ツールでサポート介入として後でエラーを解決できます。

13. 読み込みフェーズで **データ同期** レコードが 100% になると、その読み込みの結果として生じたすべての変更が開始されています。

## <a name="configure-azure-ad-b2c-policies"></a>Azure AD B2C ポリシーを構成する

場合によっては、Azure AD B2C に応じて TheAccessHub 管理ツールを最新の状態に同期する機能に制限があります。 TheAccessHub 管理ツールの API と Azure AD B2C ポリシーを活用して、変更が発生したときに TheAccessHub 管理ツールに通知できます。 このソリューションには、[Azure AD B2C カスタム ポリシー](./custom-policy-get-started.md)に関する技術的な知識が必要です。 次のセクションでは、TheAccessHub 管理ツールにサインアップ カスタム ポリシー内の新しいアカウントを通知するための、ポリシー ステップとセキュリティで保護された証明書の例を示します。

### <a name="create-a-secure-credential-to-invoke-theaccesshub-admin-tools-api"></a>TheAccessHub 管理ツールの API を呼び出すためのセキュリティで保護された資格情報を作成する

1. N8 Identity によって提供される資格情報を使用して TheAccessHub 管理ツールにログインします

2. **[System Admin]\(システム管理者\)**  >  **[Admin Tools]\(管理ツール\)**  >  **[API Security]\(API セキュリティ\)** に移動します

3. **[Generate]\(生成\)** を選択します

4. **[Certificate Password]\(証明書のパスワード\)** をコピーします

5. **[Download]\(ダウンロード\)** を選択して、クライアント証明書を取得します。

6. こちらの[チュートリアル](./secure-rest-api.md#https-client-certificate-authentication )に従って、Azure AD B2C にクライアント証明書を追加します。

### <a name="retrieve-your-custom-policy-examples"></a>カスタム ポリシーの例を取得する

1. N8 Identity によって提供される資格情報を使用して TheAccessHub 管理ツールにログインします

2. **[System Admin]\(システム管理者\)**  >  **[Admin Tools]\(管理ツール\)**  >  **[Azure B2C Policies]\(Azure B2C ポリシー\)** に移動します

3. Identity Experience Framework 構成から Azure AD B2C テナント ドメインと 2 つの Identity Experience Framework ID を指定します

4. **[保存]** を選びます。

5. **[Download]\(ダウンロード\)** を選択して、顧客がサインアップしたときに顧客を TheAccessHub 管理ツールに追加する基本ポリシーを含む zip ファイルを取得します。

6. こちらの[チュートリアル](./custom-policy-get-started.md)に従い、Azure AD B2C でカスタム ポリシーの設計を開始します。

## <a name="next-steps"></a>次のステップ

追加情報については、次の記事を参照してください。

- [Azure AD B2C のカスタム ポリシー](./custom-policy-overview.md)

- [Azure AD B2C のカスタム ポリシーの概要](./custom-policy-get-started.md?tabs=applications)