---
title: インクルード ファイル
description: インクルード ファイル
services: commercial marketplace
documentationcenter: partner-center-commercial-marketplace
author: qianw211
manager: evansma
editor: ''
ms.service: marketplace
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/13/2019
ms.author: v-qiwe
ms.custom: include file
ms.openlocfilehash: 32bc5f76e0be19ae5adb24f10094494d94eeb4d6
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019581"
---
**[体験版]** タブでは、デモ (つまり "体験版") を設定して、顧客が購入をコミットする前にオファーを試せるようにすることができます。 詳細については、記事「[体験版とは](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)」を参照してください。 オファーの体験版を提供する必要がなくなった場合は、 **[Offer setup]\(オファーの設定\)** ページに戻り、 **[Enable test drive]\(体験版を有効にする\)** をオフにします。

### <a name="technical-configuration"></a>技術的な構成
利用可能な体験版の種類は次のとおりで、それぞれに独自の技術的な構成要件があります。

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [ロジック アプリ](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (技術的な構成は不要)

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure Resource Manager の体験版の技術的な構成

お客様のソリューションを構成しているすべての Azure リソースが含まれたデプロイ テンプレート。 このシナリオに適合するのは、Azure リソースしか使用されていない製品です。 [Azure Resource Manager の体験版](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)の設定について詳しくご確認ください。

- **リージョン** (必須): 現在、体験版を利用可能にできるサポート対象の Azure リージョンは 26 か所です。 通常、顧客が最も近いリージョンを選択して最高のパフォーマンスを実現できるように、顧客の数が最も多いと予測されるリージョンで体験版を利用可能にする必要があります。 選択中の各リージョンで必要なすべてのリソースのデプロイが自分のサブスクリプションで許可されていることを確認する必要があります。

- **Instances**:使用可能なインスタンスの種類 (ホットまたはコールド) と数を選択します。これには、お客様のオファーが利用可能なリージョンの数が掛けられます。

**Hot**:この種類のインスタンスは選択したリージョンごとにデプロイされ、アクセスを待機します。 顧客は、デプロイを待つことなく体験版の "*ホット*" インスタンスにすぐにアクセスできます。 トレードオフとして、これらのインスタンスは Azure サブスクリプションで常に実行しているので、大きな稼働時間コストが発生します。 使用できる "*ホット*" インスタンスがない場合、ほとんどの顧客がフル デプロイを待つのを望まず、その結果顧客の利用が減るので、少なくとも 1 つの "*ホット*" インスタンスを用意することを強くお勧めします。

**コールド**:　この種類のインスタンスは、リージョンごとにデプロイできる可能性があるインスタンスの総数を表します。 コールド インスタンスでは、顧客が体験版を要求したときのデプロイ用に体験版の完全な Resource Manager テンプレートが必要です。そのため、"*コールド*" インスタンスでは、読み込みが "*ホット*" インスタンスよりもはるかに遅くなります。 トレードオフとして、体験版の期間のみの支払いで済みます。"*ホット*" インスタンスのように Azure サブスクリプションで常に実行されていることは "*ありません*"。

- **Test drive Azure Resource Manager template (体験版の Azure Resource Manager テンプレート)** : 目的の Azure Resource Manager テンプレートが含まれている .zip をアップロードします。  Azure Resource Manager テンプレートの作成については、記事「[クイック スタート: Azure portal を使用した Azure Resource Manager テンプレートの作成とデプロイ](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)」を参照してください。

- **体験版の期間** (必須): 体験版がアクティブな状態であり続ける期間の長さを時間数で入力します。 この期間が終わると、体験版は自動的に終了します。 この期間は、整数の時間でのみ設定できます (例: "2" 時間。"1.5" は無効)。

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Dynamics 365 の体験版の技術的な構成

Microsoft は、この種類の体験版を使用してサービスのプロビジョニングとデプロイをホストおよび維持することで、体験版の設定の複雑さを排除できます。 この種類のホストされた体験版では、体験版がターゲットとしている対象ユーザーが Business Central、Customer Engagement、Operations のいずれであっても、構成は同じです。

- **Max concurrent test drives (同時実行する体験版の最大数)** (必須):　体験版を同時に使用できる顧客の最大数を設定します。 体験版がアクティブになっている間、各同時ユーザーは Dynamics 365 のライセンスを消費します。そのため、設定された上限に対応するうえで十分な数のライセンスを使用可能にする必要があります。 推奨値は 3 ～ 5 です。

- **体験版の期間** (必須): 時間数を定義して、体験版がアクティブな状態であり続ける期間の長さを入力します。 この時間が経過すると、セッションが終了し、ライセンスが消費されなくなります。 オファーの複雑さに応じて、2 時間から 24 時間までの値にすることをお勧めします。 この期間は、整数の時間でのみ設定できます (例: "2" 時間。"1.5" は無効)。  ユーザーは、時間を使い切った後に体験版にもう一度アクセスしたい場合、新しいセッションを要求できます。

- **インスタンス URL** (必須): 顧客が体験版を開始する URL。 通常、インストールされたサンプル データを使用してアプリを実行する Dynamics 365 インスタンスの URL です (例: https://testdrive.crm.dynamics.com) 。

- **Instance Web API URL (インスタンスの Web API URL)** (必須): 自分の Microsoft 365 アカウントにログインして、 **[設定]** 、 **[カスタマイズ]** 、 **[開発者リソース]** 、 **[インスタンスの Web API] ([サービスのルート URL])** に移動することによって、自分の Dynamics 365 インスタンスの Web API URL を取得し、そこにある URL (例: https://testdrive.crm.dynamics.com/api/data/v9.0) をコピーします。

- **ロール名** (必須): Dynamics 365 のカスタム体験版で自分が定義したセキュリティ ロールの名前を入力します。 これは、体験版の使用中にユーザーに割り当てられます (例: test-drive-role)。

#### <a name="technical-configuration-for-logic-app-test-drive"></a>ロジック アプリの体験版の技術的な構成

すべてのカスタム製品には、この種類の体験版デプロイ テンプレートを使用する必要があります。これは、さまざまで複雑なソリューション アーキテクチャに対応します。 ロジック アプリの体験版の設定については、GitHub 上の [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) と [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) に関するページをご覧ください。

- **リージョン** (必須。単一選択のドロップダウン リスト): 現在、体験版を利用可能にできるサポート対象の Azure リージョンは 26 か所です。 お客様のロジック アプリのリソースは、自分が選択するリージョンにデプロイされます。 お客様のロジック アプリに、特定のリージョンに格納されるカスタム リソースがある場合は、ここで必ずそのリージョンを選択します。 これを行う最善の方法は、ポータルで自分の Azure サブスクリプションにローカルにロジック アプリを完全にデプロイし、それが正常に動作するのを確認したうえでこの選択を行うことです。

- **Max concurrent test drives (同時実行する体験版の最大数)** (必須):　体験版を同時に使用できる顧客の最大数を設定します。 これらの体験版は既にデプロイされており、顧客はデプロイを待つことなくそれらにすぐにアクセスできます。

- **体験版の期間** (必須): 体験版がアクティブな状態であり続ける期間の長さを時間数で入力します。 この期間が終わると、体験版は自動的に終了します。

- **Azure リソース グループ名** (必須): お客様のロジック アプリの体験版が保存される [Azure リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)の名前を入力します。

- **Azure logic app name (Azure ロジック アプリ名)** (必須): 体験版をユーザーに割り当てるロジック アプリの名前を入力します。 このロジック アプリは、上記の Azure リソース グループに保存される必要があります。

- **Deprovision logic app name (プロビジョニング解除ロジック アプリ名)** (必須): 顧客の完了後に体験版をプロビジョニング解除するロジック アプリの名前を入力します。 このロジック アプリは、上記の Azure リソース グループに保存される必要があります。

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Power BI の体験版では技術的な構成は不要

インタラクティブな Power BI の視覚化を示したい製品では、埋め込みリンクを使用して、カスタムビルドされたダッシュボードを体験版として共有できます。それ以上の技術的な構成は不要です。 [Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) テンプレート アプリの設定について詳しくご確認ください。

### <a name="deployment-subscription-details"></a>デプロイ サブスクリプションの詳細

体験版を自動的にデプロイするには、固有の Azure サブスクリプションを別個に作成し、指定してください (Power BI の体験版では不要です)。

- **Azure サブスクリプション ID** (Azure Resource Manager とロジック アプリでは必須): リソースの使用状況レポート用および課金用の Azure アカウント サービスへのアクセス権を付与するサブスクリプション ID を入力します。 まだお持ちでない場合、体験版に使用するために[別個の Azure サブスクリプションの作成](https://docs.microsoft.com/azure/billing/billing-create-subscription)を検討することをお勧めします。 Azure サブスクリプション ID は、[Azure portal](https://portal.azure.com/) にログインし、左側にあるメニューの **[サブスクリプション]** タブに移動して見つけることができます。 このタブを選択すると、自分のサブスクリプション ID (例: "a83645ac-1234-5ab6-6789-1h234g764ghty") が表示されます。

- **Azure AD テナント ID** (必須): 自分の Azure Active Directory (AD) [テナント ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) を入力します。 この ID を見つけるには、[Azure portal](https://portal.azure.com/) にサインインして左側のメニューで [Active Directory] タブを選択し、 **[プロパティ]** を選択してから、表示される**ディレクトリ ID** 番号 (例: 50c464d3-4930-494c-963c-1e951d15360e) を探します。 また、[https://www.whatismytenantid.com](https://www.whatismytenantid.com) で URL のドメイン名を使用して、組織のテナント ID を検索することもできます。

- **Azure AD tenant name (Azure AD テナント名)** (Dynamic 365 で必須): 自分の Azure Active Directory (AD) 名を入力します。 この名前を見つけるには、[Azure portal](https://portal.azure.com/) にサインインします。右上隅にある自分のアカウント名の下に、テナント名が表示されます。

- **Azure AD app ID (Azure AD アプリ ID)** (必須): 自分の Azure Active Directory (AD) [アプリケーション ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) を入力します。 この ID を見つけるには、[Azure portal](https://portal.azure.com/) にサインインして左側のメニューで [Active Directory] タブを選択し、 **[アプリの登録]** を選択してから、表示される**アプリケーション ID** 番号 (例: 50c464d3-4930-494c-963c-1e951d15360e) を探します。

- **Azure AD アプリ クライアントのシークレット** (必須): Azure AD アプリケーションの[クライアント シークレット](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)を入力します。 この値を探すには、[Azure portal](https://portal.azure.com/) にサインインします。 左側のメニューにある **[Azure Active Directory]** タブを選択し、 **[アプリの登録]** を選択してから、体験版アプリを選択します。 次に、 **[Certificates and secrets]\(証明書とシークレット\)** 、 **[New client secret]\(新しいクライアント シークレット\)** の順に選択し、説明を入力し、 **[Expires]\(有効期限\)** で **[Never]\(なし\)** を選択してから、 **[追加]** を選択します。 必ず値をコピーしておいてください。 (これを行う前にページから移動しないでください。移動すると、値にアクセスできなくなります。)

次のセクションに進む前に、必ず**保存**してください。

### <a name="test-drive-listings-optional"></a>体験版のリスト登録 (省略可能)

**[体験版]** タブの下にある **[Test Drive listings]\(体験版のリスト登録\)** オプションには、体験版が利用可能な言語 (と市場) が表示されます。現在、利用可能な唯一の場所は英語 (米国) です。 さらに、このページには、言語固有のリスト登録の状態と、それが追加された日付および時刻が表示されます。 言語と市場ごとに体験版の詳細 (説明、ユーザー マニュアル、ビデオなど) を定義する必要があります。

- **説明** (必須): ユーザーがお客様のオファーを購入するかどうかを決定する助けとなるように、体験版、デモの内容、試用するユーザーの目的、試せる機能、関連情報について説明します。 このフィールドには、最大で 3,000 文字のテキストを入力できます。 

- **[アクセス情報]** (Azure Resource Manager とロジックの体験版では必須): 顧客がこの体験版にアクセスして使用するうえで知っておく必要がある情報を説明します。 お客様のオファーの使用シナリオのほか、顧客が体験版を通じて機能にアクセスするために知っておくべき正確な情報を説明します。 このフィールドには、最大で 10,000 文字のテキストを入力できます。

- **ユーザー マニュアル** (必須): 体験版エクスペリエンスの詳細なチュートリアル。 ユーザー マニュアルは、体験版のエクスペリエンスで顧客が使用できる内容を正確にカバーし、疑問がある場合に参照するものとして機能する必要があります。 ファイルは PDF 形式にして、アップロード後に名前 (最大 255 文字) を付ける必要があります。

- **ビデオ: ビデオの追加** (省略可能): ビデオは YouTube または Vimeo にアップロードすることができ、リンクとサムネイル画像 (533 x 324 ピクセル) を使用してここで参照できます。これにより顧客は、オファーの機能を正しく使用する方法やそれらのメリットが際立つシナリオを把握する方法など、体験版への理解を深めるのに役立つ一連の情報を確認できます。
  - **名前** (必須)
  - **URL (YouTube または Vimeo のみ)** (必須)
  - **サムネイル (533 x 324 ピクセル)** : 画像ファイルは PNG 形式である必要があります。

これらのフィールドに入力した後、 **[保存]** を選択します。
