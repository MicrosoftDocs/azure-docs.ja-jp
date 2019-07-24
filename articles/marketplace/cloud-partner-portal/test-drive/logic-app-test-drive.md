---
title: ロジック アプリの体験版 | Microsoft Docs
description: Dynamics AX/CRM インスタンスまたは Azure 以外の他のリソースと接続する体験版を作成する方法について説明します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 4fd946b53956509844ad0a9396575f1ee2450414
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58338600"
---
<a name="logic-app-test-drive"></a>ロジック アプリの体験版
====================

この記事は、AppSource にオファーがあり、Dynamics AX/CRM インスタンスまたは Azure 以外の他のリソースと接続する体験版を作成しようと考えている、発行元を対象としたものです。

<a name="how-to-build-a-logic-app-test-drive"></a>ロジック アプリの体験版を作成する方法
-----------------------------------

ロジック アプリの体験版に関するドキュメントは、現在はまだ、[Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) と [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) に関するものが GitHub にあります。詳しくはそちらをご覧ください。

<a name="how-to-publish-a-test-drive"></a>体験版の発行方法
---------------------------

体験版の作成が済んだので、このセクションでは体験版を正常に発行するために必要な各フィールドについて説明します。

![体験版の機能を有効にする](./media/azure-resource-manager-test-drive/howtopub1.png)

最初の最も重要なフィールドは、オファーで体験版を有効にするかどうかの切り替えです。[はい] を選択すると、必要なフィールドを含むフォームの残りの部分を設定できるようになります。**[いいえ]** を選択するとフォームは無効になり、体験版を無効にして再発行した場合、運用環境から体験版が削除されます。

*メモ*:ユーザーがアクティブに使用している体験版がある場合、それらの体験版はセッションの有効期限が切れるまで引き続き実行します。

### <a name="details"></a>詳細

次に設定するセクションは、体験版オファーに関する詳細です。

![体験版の詳細](./media/azure-resource-manager-test-drive/howtopub2.png)

**[Description]\(説明\) -** *[必須フィールド]* 体験版に関する主要な説明をここに記述します。 顧客はここを読んで、体験版でカバーされている製品のシナリオを理解します。 

**[User Manual]\(ユーザー マニュアル\) -** *[必須フィールド]* 体験版エクスペリエンスの詳細なチュートリアルです。 顧客はこれを開き、体験版で顧客に行ってもらいたいことの正確な手順を把握します。 この内容は簡単に理解して実行できるものにすることが重要です。 (.pdf ファイルにする必要があります)

**[Test Drive Demo Video]\(体験版デモ ビデオ\) -** "\[推奨\]" ユーザー マニュアルと似ており、体験版のエクスペリエンスのビデオ チュートリアルを含めるのが最善です。 顧客は体験版を使用する前または使用中にこれを見て、体験版で顧客に行ってもらいたいことの正確な手順を把握します。 この内容は簡単に理解して実行できるものにすることが重要です。

- **[Name]\(名前\)** - ビデオのタイトル
- **[Link]\(リンク\)** - YouTube または Vimeo の URL を埋め込む必要があります。 埋め込み URL を取得する方法の例を後で示します。
- **[Thumbnail]\(サムネイル\)** - 高品質の画像 (533 x 324 ピクセル) にする必要があります。 体験版エクスペリエンスの一部のスクリーンショットをここに表示することをお勧めします。

体験版使用中の顧客に対するこれらのフィールドの表示を次に示します。

![体験版のフィールドのルック アンド フィール](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>技術的構成

次のセクションでは、体験版ロジック アプリを構成し、体験版インスタンスの動作を具体的に定義します。

![体験版の技術的構成](./media/azure-resource-manager-test-drive/howtopub5_logicapp.png)

- **[Region]\(リージョン\)** - *[必須フィールド]* 選択したリージョンに体験版ロジック アプリのリソースがデプロイされます。

    *注:* ロジック アプリに、リージョンに格納されるカスタム リソースがある場合は、ここで必ずそのリージョンを選択します。 これを行う最善の方法としては、**ポータルでお使いの Azure サブスクリプションにローカルにロジック アプリを完全にデプロイし、動作を確認**した後で、この値を入力します。

- **[Maximum Concurrent Test Drives]\(同時に実行する体験版の最大数\)** - *[必須フィールド]* 選択したリージョンごとに既にデプロイされていてアクセスを待っている体験版インスタンスの数です。 顧客は、デプロイを待つことなくすぐにこの体験版にアクセスできます。

    *注:* ウェビナー/クラスで、N 人の受講者全員に体験版を提供したい場合は、N 個のホット インスタンスで発行した後、クラスが終了したら、再発行して通常の個数のホット インスタンスに戻すことをお勧めします。

- **[Test Drive Duration (hours)]\(体験版期間 (時間)\) -** *[必須フィールド]* 体験版をアクティブなままにしておく期間の時間数です。 この期間が終わると、体験版は自動的に終了します。

- **[Azure Resource Group Name]\(Azure リソース グループ名\) -** *[必須フィールド]* ロジック アプリの体験版を保存するリソース グループの名前を指定します。

- **[Assign Logic App Name]\(ロジック アプリ名の割り当て\) -** *[必須フィールド]* 顧客が取得する前に体験版でユーザーを割り当てるために使用するロジック アプリを指定します。そのロジック アプリの名前を入力します。 このファイルが上記のリソース グループに保存されることを確認します。

- **[Deprovision Logic App Name]\(ロジック アプリ名のプロビジョニング解除\) -** *[必須フィールド]* 体験版で作成されたすべてのリソースをプロビジョニング解除するためのロジック アプリの名前を指定します。 このファイルが上記のリソース グループに保存されることを確認します。

- **[Access Information]\(アクセス情報\) -** *[必須フィールド]* 顧客が体験版を取得すると、アクセス情報が顧客に対して表示されます。 これらの説明は、体験版 Resource Manager テンプレートからの役に立つ出力パラメーターを共有するためのものです。 出力パラメーターを含めるには、二重中かっこを使用し (例: **{{outputname}}**)、正しい場所に挿入します。 (フロントエンドでレンダリングするため、HTML 文字列形式にすることをお勧めします)。

### <a name="test-drive-deployment-subscription-details"></a>体験版デプロイ サブスクリプションの詳細

設定する最後のセクションでは、Azure サブスクリプションと Azure Active Directory (AD) を接続することで、体験版を自動的にデプロイできるようにします。

![体験版デプロイ サブスクリプションの詳細](./media/azure-resource-manager-test-drive/subdetails1.png)

**[Azure Subscription ID]\(Azure サブスクリプション ID\)** *[必須フィールド]* Azure サービスと Azure portal へのアクセス権を付与します。 このサブスクリプションで、リソースの使用状況が報告され、サービスが課金されます。 体験版 "**専用**" の Azure サブスクリプションがまだない場合は、先に進んで作成してください。 Azure サブスクリプション ID を確認するには、Azure portal にログインし、左側のメニューでサブスクリプションに移動します。
(例: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Azure サブスクリプション](./media/azure-resource-manager-test-drive/subdetails2.png)

**[Azure AD Tenant ID]\(Azure AD テナント ID\)** *[必須フィールド]* 使用できるテナント ID が既にある場合は、[プロパティ] -\> [ディレクトリ ID] の下で確認できます。

![Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

または、Azure Active Directory で新しいテナントを作成します。

![Azure Active Directory のプロパティ画面](./media/azure-resource-manager-test-drive/subdetails4.png)

!Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails5.png)

![Azure Active Directory テナント](./media/azure-resource-manager-test-drive/subdetails6.png)

**[Azure AD App ID]\(Azure AD アプリ ID\)** *[必須フィールド]* 次の手順では、新しいアプリケーションを作成して登録します。 このアプリケーションを使用して、体験版インスタンスの操作を実行します。

1. 新しく作成したディレクトリまたは既存のディレクトリに移動し、フィルター ウィンドウで Azure Active Directory を選択します。
2. [アプリの登録] を探して、[追加] をクリックします
3. アプリケーション名を指定します。
4. 種類として [Web app / API]\(Web アプリ/API\) を選択します
5. サインオン URL に任意の値を指定します。ここでは、このフィールドは使用しません。
6. [作成] をクリックします。
7. アプリケーションが作成されたら、[プロパティ] -\> [Set the application as multi-tenant]\(アプリケーションをマルチテナントとして設定する\) に移動し、[保存] をクリックします。

[保存] をクリックします。 最後の手順では、この登録済みアプリのアプリケーション ID を取得して、体験版のフィールドに貼り付けます。

![Azure Active Directory のアプリケーション ID](./media/azure-resource-manager-test-drive/subdetails7.png)

アプリケーションを使用してサブスクリプションにデプロイするので、サブスクリプションで共同作成者としてアプリケーションを追加する必要があります。 その手順は以下のとおりです。

1. [サブスクリプション] ブレードに移動し、体験版のみに使用している適切なサブスクリプションを選択します。
1. **[アクセス制御 (IAM)]** をクリックします。
1. **[ロールの割り当て]** タブをクリックします。![Azure Active Directory、新しいアクセス制御プリンシパルの追加](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. **[ロールの割り当ての追加]** をクリックします。
1. **共同作成者**としてロールを設定します。
1. Azure AD アプリケーションの名前を入力し、ロールを割り当てるアプリケーションを選択します。
    ![Azure Active Directory のアクセス許可](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. **[Save]** をクリックします。

**[Azure AD App Key]\(Azure AD アプリ キー\)** *[必須フィールド]* 最後のフィールドでは、認証キーを生成します。 キーの下で、キーの説明を追加し、期間を無期限に設定して、[保存] を選択します。 キーの有効期限が切れないようにすることが**重要**です。切れると、運用環境の体験版が中断されます。 この値をコピーし、必要な体験版のフィールドに貼り付けます。

![Azure Active Directory のキー セクション](./media/azure-resource-manager-test-drive/subdetails8.png)

> [!CAUTION]
> 現在、Base64 でエンコードされたキーを生成しないため、Azure アプリケーション登録プレビューを使用することはできません。


<a name="next-steps"></a>次の手順
----------

体験版のすべてのフィールドを設定したので、オファーを**再発行**します。 体験版が認定プロセスに合格したら、オファーの**プレビュー**で顧客エクスペリエンスを広範にテストする必要があります。 UI で体験版を開始し、体験版が完全に正しくデプロイされていることを確認します。

重要なこととして、体験版は顧客用にプロビジョニングされており、顧客がその使用を終了すると体験版サービスによってリソース グループが自動的にクリーンアップされるので、体験版のどの部分も削除しないでください。

プレビューのオファリングに問題がなければ、**稼働**を開始します。 オファーが発行されると Microsoft による最終的なレビュー プロセスが行われ、エクスペリエンス全体が再確認されます。 何らかの理由によりオファーが却下された場合は、オファーのエンジニアリング連絡先に通知され、必要な修正が説明されます。

さらに質問がある場合やトラブルシューティングに関するアドバイスが必要な場合、あるいは体験版をより成功させたい場合は、「[FAQ, Troubleshooting, & Best Practices](./marketing-and-best-practices.md)」(FAQ、トラブルシューティング、およびベスト プラクティス) をご覧ください。
