---
title: 体験版の技術的な構成、Microsoft コマーシャル マーケット プレース
description: 体験版について説明します。 体験版があれば、新規顧客は購入を決める前にオファーを試すことができます。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/13/2019
author: trkeya
ms.author: trkeya
ms.openlocfilehash: 553eba3898a5ea42e5d478603e35e82c68abcab1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462899"
---
# <a name="test-drive-technical-configuration"></a>体験版の技術的な構成

Microsoft コマーシャル マーケットプレースの [体験版] オプションを使用すると、自社製品の主要な機能に関する実践的なセルフガイド ツアーを構成できます。 体験版があれば、新規顧客は購入を決める前にオファーを試すことができます。 詳細については、「[体験版とは](what-is-test-drive.md)」を参照してください。

オファーの体験版を提供する必要がなくなった場合は、 **[オファーのセットアップ]** ページに戻り、 **[体験版を有効にする]** をオフにします。 すべてのオファーの種類に体験版を使用できるわけではありません。

## <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager の体験版

これは、仮想マシンまたは Azure アプリ オファーの唯一のテスト ドライブ オプションであり、非常に詳細な設定が必要です。 後述するセクション「[デプロイ サブスクリプションの詳細](#deployment-subscription-details)」および「[体験版のリスト登録](#test-drive-listings)」をお読みになってから、[Azure Resource Manager 体験版の構成](azure-resource-manager-test-drive.md)に関する別のトピックに進んでください。

## <a name="hosted-test-drive"></a>ホストされた体験版

Microsoft は、サービスのプロビジョニングとプロビジョニング解除をホストおよび維持することで、体験版の設定の複雑さを排除できます。 この種類の体験版では、体験版がターゲットとしている対象ユーザーが Dynamics 365 Customer Engagement と Dynamics 365 Operations のどちらであっても、構成は同じです。

- **同時実行体験版の最大数** (必須) – 体験版を同時に使用できる顧客の最大数を設定します。 体験版がアクティブになっている間、各同時ユーザーは Dynamics 365 のライセンスを消費します。そのため、設定された上限に対応するうえで十分な数のライセンスを確実に使用可能にしてください。 推奨値は 3 から 5 です。

- **[体験版の期間]** (必須) – 各顧客に対して体験版が有効である時間数を入力します。 この期間が経過すると、セッションが終了し、ライセンスが消費されなくなります。 オファーの複雑さに応じて、2 時間から 24 時間までの値にすることをお勧めします。 この期間は、整数の時間でのみ設定できます (たとえば、"2 時間" は有効ですが、"1.5 時間" は有効ではありません)。 ユーザーは、時間を使い切った後に体験版にもう一度アクセスしたい場合、新しいセッションを要求できます。

- **インスタンス URL** (必須) – 顧客が体験版を開始する URL。 通常、インストールされたサンプル データを使用してアプリを実行する Dynamics 365 インスタンスの URL です (例: `https://testdrive.crm.dynamics.com`)。

- **インスタンスの Web API URL** (必須) – お使いの Microsoft 365 アカウントにログインし、 **[設定]**  >  **[カスタマイズ]**  >  **[開発者リソース]**  >  **[インスタンスの Web API] ([サービスのルート URL])** の順に移動することで、お使いの Dynamics 365 インスタンスの Web API URL を取得し、その URL (例: `https://testdrive.crm.dynamics.com/api/data/v9.0`) をここにコピーします。

- **ロール名** (必須) – カスタム Dynamics 365 体験版に定義したセキュリティ ロール名を指定します。体験版の間、これがユーザーに割り当てられます。(例: test-drive-role)。

ご利用の Dynamics 365 環境を体験版用に設定してから、AppSource にアクセス許可を付与することで、ご利用のテナント内で体験版ユーザーのプロビジョニングおよびプロビジョニング解除を行う方法のヘルプについては、[こちらの手順](./test-drive-azure-subscription-setup.md)に従ってください。

ホストされている体験版を一覧表示して構成するための詳しい手順については、「[ホストされた体験版の詳細な構成](./test-drive-hosted-detailed-config.md)」ページを参照してください。

## <a name="logic-app-test-drive"></a>ロジック アプリの体験版

この種類の体験版は、Microsoft によってホストされていません。 これは、Dynamics 365 オファーなどのカスタム リソース (さまざまな複雑なソリューション アーキテクチャが含まれている) に接続するために使用します。 ロジック アプリの体験版の設定については、GitHub 上の [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) と [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) に関するページをご覧ください。

- **リージョン** (必須、単一選択のドロップダウン リスト) – 現在、体験版を利用可能にできるサポート対象の Azure リージョンは 26 か所です。 お客様のロジック アプリのリソースは、自分が選択するリージョンにデプロイされます。 お客様のロジック アプリに、特定のリージョンに格納されるカスタム リソースがある場合は、ここで必ずそのリージョンを選択します。 最善の方法は、ポータルで自分の Azure サブスクリプションにローカルにロジック アプリを完全にデプロイし、それが正常に動作するのを確認したうえでこの選択を行うことです。

- **同時実行体験版の最大数** (必須) – 体験版を同時に使用できる顧客の最大数を設定します。 これらの体験版は既にデプロイされており、顧客はデプロイを待つことなくそれらにすぐにアクセスできます。

- **体験版の期間** (必須) – 体験版がアクティブな状態であり続ける期間の長さを時間数で入力します。 この期間が終わると、体験版は自動的に終了します。

- **Azure リソース グループ名** (必須) – ロジック アプリの体験版が保存される [Azure リソース グループ](../azure-resource-manager/management/overview.md#resource-groups)名を入力します。

- **Azure logic app name (Azure ロジック アプリ名)** (必須) – 体験版をユーザーに割り当てるロジック アプリの名前を入力します。 このロジック アプリは、上記の Azure リソース グループに保存される必要があります。

- **ロジック アプリ名のプロビジョニング解除** (必須) – 顧客の完了後に体験版をプロビジョニング解除するロジック アプリの名前を入力します。 このロジック アプリは、上記の Azure リソース グループに保存される必要があります。

## <a name="power-bi-test-drive"></a>Power BI の体験版

インタラクティブな Power BI の視覚化を示したい製品では、埋め込みリンクを使用して、カスタムビルドされたダッシュボードを体験版として共有できます。それ以上の技術的な構成は不要です。 ここで必要なことは、埋め込み Power BI の URL をアップロードすることだけです。

Power BI アプリの設定の詳細については、[Power BI アプリとは](/power-bi/service-template-apps-overview)に関するページを参照してください。

## <a name="deployment-subscription-details"></a>デプロイ サブスクリプションの詳細

Microsoft が体験版のデプロイを代行できるようにするには、固有の Azure サブスクリプションを別に作成して指定します (Power BI 体験版の場合は必要ありません)。

- **[Azure サブスクリプション ID]** (Azure Resource Manager および Logic Apps では必須) – リソースの使用状況レポート用および課金用の Azure アカウント サービスへのアクセス権を付与するサブスクリプション ID を入力します。 まだお持ちでない場合、体験版に使用するために[別個の Azure サブスクリプションの作成](../cost-management-billing/manage/create-subscription.md)を検討することをお勧めします。 Azure サブスクリプション ID は、[Azure portal](https://portal.azure.com/) にログインし、左側にあるメニューの **[サブスクリプション]** タブに移動して見つけることができます。 このタブを選択すると、自分のサブスクリプション ID (例: "a83645ac-1234-5ab6-6789-1h234g764ghty") が表示されます。

- **Azure AD テナント ID** (必須) – ご利用の Azure Active Directory (AD) [テナント ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)) を入力します。 この ID を見つけるには、[Azure portal](https://portal.azure.com/) にサインインし、左側のメニューで [Active Directory] タブを選択します。 **[プロパティ]** を選択した後、表示される **ディレクトリ ID** 番号を確認します (例: 50c464d3-4930-494c-963c-1e951d15360e)。 また、[https://www.whatismytenantid.com](https://www.whatismytenantid.com) でドメイン名アドレスを使用して、組織のテナント ID を検索することもできます。

- **Azure AD テナント名** (Dynamic 365 では必須) – お使いの Azure Active Directory (AD) 名を入力します。 この名前を見つけるには、[Azure portal](https://portal.azure.com/) にサインインします。右上隅にある自分のアカウント名の下に、テナント名が表示されます。

- **Azure AD アプリ ID** (必須) – ご利用の Azure Active Directory (AD) [アプリケーション ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) を入力します。 この ID を見つけるには、[Azure portal](https://portal.azure.com/) にサインインして左側のメニューで [Active Directory] タブを選択し、 **[アプリの登録]** を選んでから、一覧表示される **アプリケーション ID** 番号を探します (`50c464d3-4930-494c-963c-1e951d15360e` など)。

- **Azure AD アプリ クライアントのシークレット** (必須) – ご利用の Azure AD アプリケーションの [クライアント シークレット](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)) を入力します。 この値を探すには、[Azure portal](https://portal.azure.com/) にサインインします。 左側のメニューにある **[Azure Active Directory]** タブを選択し、 **[アプリの登録]** を選択してから、体験版アプリを選択します。 次に、 **[Certificates and secrets]\(証明書とシークレット\)** 、 **[New client secret]\(新しいクライアント シークレット\)** の順に選択し、説明を入力し、 **[Expires]\(有効期限\)** で **[Never]\(なし\)** を選択してから、 **[追加]** を選択します。 必ず値をコピーしておいてください。 値をコピーする前に、ページから移動しないでください。

## <a name="test-drive-listings"></a>体験版のリスト登録

パートナー センターの **[体験版]** タブの下にある **[Test Drive listings]\(体験版のリスト登録\)** オプションには、体験版が利用可能な言語 (と市場) が表示されます。現在、利用可能な唯一の場所は英語 (米国) です。 さらに、このページには、言語固有のリスト登録の状態と、それが追加された日付および時刻が表示されます。 言語と市場ごとに体験版の詳細 (説明、ユーザー マニュアル、ビデオなど) を定義する必要があります。

- **説明** (必須): ユーザーがお客様のオファーを購入するかどうかを決定する助けとなるように、体験版、デモの内容、試用するユーザーの目的、試せる機能、関連情報について説明します。 このフィールドには、最大で 3,000 文字のテキストを入力できます。

- **[アクセス情報]** (Azure Resource Manager とロジックの体験版では必須): 顧客がこの体験版にアクセスして使用するうえで知っておく必要がある情報を説明します。 お客様のオファーの使用シナリオのほか、顧客が体験版を通じて機能にアクセスするために知っておくべき正確な情報を説明します。 このフィールドには、最大で 10,000 文字のテキストを入力できます。

- **ユーザー マニュアル** (必須): 体験版エクスペリエンスの詳細なチュートリアル。 ユーザー マニュアルは、体験版のエクスペリエンスで顧客が使用できる内容を正確にカバーし、疑問がある場合に参照するものとして機能する必要があります。 ファイルは PDF 形式にして、アップロード後に名前 (最大 255 文字) を付ける必要があります。

- **ビデオ: ビデオの追加** (省略可能): 他の場所でホストされているビデオは、リンクとサムネイル画像 (533 x 324 ピクセル) を使用してここで参照できます。これにより顧客は、オファーの機能を正しく使用する方法やそれらのメリットが際立つシナリオを把握する方法など、体験版への理解を深めるのに役立つ一連の情報を確認できます。
  - **名前** (必須)
  - **URL** (YouTube または Vimeo のみ。必須)
  - **サムネイル** (533 x 324 ピクセル) – 画像は PNG 形式とする必要があります。

現在、パートナー センターで体験版を作成している場合は、続行する前に、 **[下書きの保存]** を選択します。

ホストされている体験版を一覧表示して構成するための詳しい手順については、「[ホストされた体験版の詳細な構成](./test-drive-hosted-detailed-config.md)」ページを参照してください。

## <a name="additional-resources"></a>その他のリソース

## <a name="next-steps"></a>次のステップ

- [体験版のベスト プラクティス](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [概要](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF、ポップアップ ブロックがオフになっていることを確認してください)
- [商業マーケットプレースで既存のオファーを更新する](partner-center-portal/update-existing-offer.md)