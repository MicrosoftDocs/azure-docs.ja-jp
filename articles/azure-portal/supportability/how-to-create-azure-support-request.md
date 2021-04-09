---
title: Azure サポート要求を作成する方法
description: サポートが必要なお客様は、Azure portal を使用して、セルフサービス ソリューションを見つけたり、サポート リクエストを作成および管理したりできます。
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.topic: how-to
ms.date: 06/25/2020
ms.openlocfilehash: 71aba8f2769a75805f47d90b71a8db4eeb26a97a
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652396"
---
# <a name="create-an-azure-support-request"></a>Azure サポート要求を作成する

Azure では、サポート要求 (サポート チケットとも呼ばれる) を作成して管理できます。 この記事で説明するように、[Azure portal ](https://portal.azure.com)上で要求を作成して管理することができます。 [Azure サポート チケット REST API ](/rest/api/support)を使用して、または [Azure CLI](/cli/azure/azure-cli-support-request) を使用して、プログラムによってリクエストを作成して管理することもできます。

> [!NOTE]
> Azure portal の URL は、組織がデプロイされている Azure クラウドに固有のものです。
>
>* 商用利用の Azure portal は次のとおりです。[https://portal.azure.com](https://portal.azure.com)
>* ドイツの Azure portal は次のとおりです: [https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* 米国政府機関向けの Azure portal は次のとおりです: [https://portal.azure.us](https://portal.azure.us)

サポート リクエスト エクスペリエンスでは、主に次の 3 つの目標に重点を置いています。

* **合理化**:サポートとトラブルシューティングを見つけやすくして、サポート リクエストの送信方法を簡略化します。
* **統合**:Azure リソースに関する問題のトラブルシューティングを行う際に、コンテキストを切り替えずに、サポート リクエストを容易に開くことができます。
* **効率性**: 問題を効率的に解決するためにサポート エンジニアが必要とする重要な情報を集めます。

Azure は、サブスクリプション管理 (請求、割り当て調整、アカウント振り替えなど) に無制限のサポートを提供します。 技術的なサポートについては、サポート プランが必要になります。 詳細については、「[サポート プランの比較](https://azure.microsoft.com/support/plans)」を参照してください。

## <a name="getting-started"></a>作業の開始

Azure portal で **[ヘルプとサポート]** にアクセスできます。 これは、Azure portal メニュー、グローバル ヘッダー、またはサービスのリソース メニューから使用できます。 サポート リクエストを提出するには、適切なアクセス許可が必要です。

### <a name="azure-role-based-access-control"></a>Azure ロールベースのアクセス制御

サポート リクエストを作成するには、[所有者](../../role-based-access-control/built-in-roles.md#owner)か、[共同作成者](../../role-based-access-control/built-in-roles.md#contributor)か、またはサブスクリプション レベルで[サポート リクエスト共同作成者](../../role-based-access-control/built-in-roles.md#support-request-contributor)ロールが割り当てられている必要があります。 Azure Active Directory シナリオなどのサブスクリプションを使用せずにサポート リクエストを作成するには、[管理者](../../active-directory/roles/permissions-reference.md)である必要があります。

### <a name="go-to-help--support-from-the-global-header"></a>グローバル ヘッダーから [ヘルプとサポート] にアクセスする

Azure portal 内の任意の場所からサポート リクエストを開始するには、次の操作を行います。

1. **[?]** を グローバル ヘッダーで選択します。 次に、 **[ヘルプとサポート]** を選択します。

   ![ヘルプとサポート](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

1. **[新しいサポート リクエスト]** を選択します。 画面の指示に従って、発生している問題に関する情報を提供します。 Microsoft は考えられる解決策をいくつか提案し、問題の詳細を収集し、サポート リクエストの送信と追跡を支援します。

   ![新しいサポート要求](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>リソース メニューから [ヘルプとサポート] にアクセスする

リソースのコンテキストでサポート リクエストを開始するには、現在は次の操作を行います。

1. リソース メニューの **[サポート + トラブルシューティング]** セクションで、 **[新しいサポート リクエスト]** を選択します。

   ![コンテキストで](./media/how-to-create-azure-support-request/incontext2lower.png)

1. 画面の指示に従って、発生している問題に関する情報を Microsoft に提供します。 リソースからサポート リクエスト プロセスを開始すると、いくつかのオプションがあらかじめ自動的に選択されています。

## <a name="create-a-support-request"></a>サポート要求の作成

Microsoft は、発生している問題について情報を収集してその解決を支援するためのいくつかの手順について説明します。 各手順については、次のセクションで説明します。

### <a name="basics"></a>基本

サポート要求プロセスの最初の手順は、ユーザーが抱える問題とそのサポート プランに必要な基本情報を集めることです。

**[新しいサポート リクエスト]** の **[基本]** タブで、セレクターを使用して、Microsoft への問題の説明を開始します。 まず、問題の種類の一般的なカテゴリを特定し、関連するサブスクリプションを選択します。 **Windows を実行する仮想マシン** など、サービスを選択します。 仮想マシンの名前などのリソースを選択します。 ご自分の言葉で問題を記述してから、 **[問題の種類]** と **[問題のサブタイプ]** を選択して、より具体的にご説明下さい。

![[基本] ブレード](./media/how-to-create-azure-support-request/basics2lower.png)

### <a name="solutions"></a>ソリューション

Microsoft は基本的な情報を収集できたら、次にユーザーにソリューションを提示して、お客様に試してもらいます。 場合によっては、クイック診断を実行することもあります。 ソリューションは Azure エンジニアによって作成されたものであり、大抵の一般的な問題はこれで解決します。

### <a name="details"></a>詳細

次に、Microsoft は問題に関する追加の詳細を収集します。 この手順で徹底的かつ詳細な情報が提供していただくことで、Microsoft はお客様のサポート リクエストを適切なエンジニアに転送することができます。

1. 可能であれば、問題が発生した時期と、再現手順をお知らせください。 ログ ファイルや診断からの出力などのファイルをアップロードできます。 ファイルのアップロードの詳細については、「[ファイルのアップロードのガイドライン](how-to-manage-azure-support-request.md#file-upload-guidelines)」を参照してください。

1. 問題に関するすべての情報を記入したら、サポートを受ける方法を選択します。 **[詳細]** の **[サポート方法]** セクションで、影響の重大度を選択します。 最高重大度は[サポート プラン](https://azure.microsoft.com/support/plans)ごとに異なります。

    既定では、 **[診断情報の共有]** オプションが選択されています。 これにより、Azure サポートが Azure リソースから[診断情報](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/)を収集できるようになります。 場合によっては、仮想マシンのメモリへのアクセスを要求するなど、既定では選択されていない 2 つ目の質問があります。

1. ご自分の希望する連絡方法、連絡のための都合のよいタイミング、およびサポート言語を指定します。

1. 次に、 **[連絡先情報]** セクションに入力して、ご自分への連絡方法をお知らせください。

### <a name="review--create"></a>確認と作成

各タブですべての必須情報を入力して、 **[確認および作成]** を選択します。 サポートに送信する詳細情報を確認します。 必要に応じて、任意のタブに戻り、変更を行います。 完了したサポート リクエストが適切であれば、 **[作成]** を選択します。

サポート エンジニアが、指定された方法を使用してお客様に連絡します。 初回の応答時間については、「[サポート内容と応答性](https://azure.microsoft.com/support/plans/response/)」を参照してください。


## <a name="next-steps"></a>次のステップ

Azure のセルフヘルプ サポート オプションの詳細については、次のビデオをご覧ください。

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

詳細については、次のリンクを参照してください。

* [Azure サポート リクエストを管理する方法](how-to-manage-azure-support-request.md)
* [Azure サポート チケット REST API](/rest/api/support)
* [お客様からのフィードバックとご提案の送信](https://feedback.azure.com/forums/266794-support-feedback)
* [Twitter](https://twitter.com/azuresupport) でのご協力
* [Microsoft Q&A 質問ページ](/answers/products/azure)で同僚からのヘルプを得る
* [Azure サポートに関する FAQ で詳細を確認する](https://azure.microsoft.com/support/faq)