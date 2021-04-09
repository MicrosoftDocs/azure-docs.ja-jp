---
title: Azure Active Directory B2C の課金モデル
description: Azure AD B2C の月間アクティブ ユーザー数 (MAU) の課金モデル、Azure AD B2C テナントを Azure サブスクリプションにリンクする方法、および Premium レベルの適切な価格を選択する方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: a54ed9dc6557d9b613485bf28e74af0c59fc9e5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99225210"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Azure Active Directory B2C の課金モデル

Azure Active Directory B2C (Azure AD B2C) の価格は、1 か月間に認証アクティビティを行った一意のユーザーの数である、月間アクティブ ユーザー数 (MAU) に基づいています。 この課金モデルは、Azure AD B2C テナントと [Azure AD ゲスト ユーザー コラボレーション (B2B)](../active-directory/external-identities/external-identities-pricing.md) の両方に適用されます。 MAU の課金は、無料レベルと柔軟で予測可能な価格を提供することによってコストを削減するのに役立ちます。 この記事では、MAU の課金、サブスクリプションへの Azure AD B2C テナントのリンク、および価格レベルの変更について説明します。

> [!IMPORTANT]
> この記事には価格の詳細は含まれていません。 使用量の課金と価格の最新情報については、「[Azure Active Directory B2C の価格](https://azure.microsoft.com/pricing/details/active-directory-b2c/)」を参照してください。 Azure AD B2C サービスが利用できる地域とユーザー データの格納場所に関する詳細については、[Azure AD B2C の利用可能なリージョンとデータの保存場所](data-residency.md)に関するページも参照してください。

## <a name="what-do-i-need-to-do"></a>何をする必要がありますか?

MAU の課金を利用するには、Azure AD B2C テナントを Azure サブスクリプションにリンクする必要があります。 また、Azure AD B2C Premium P2 の機能 (リスクベースの条件付きアクセスなど) を使用する場合は、Azure AD B2C テナントを別の価格レベルに切り替えることが必要になる場合もあります。

|テナントが以下の場合  |以下を実行する必要があります。  |
|---------|---------|
| 既に MAU 単位で課金されている Azure AD B2C テナント     | 何もしない。 ユーザーが Azure AD B2C テナントから認証を受けると、MAU ベースの課金モデルを使用して自動的に課金されます。        |
| サブスクリプションにまだリンクされていない Azure AD B2C テナント     |  [Azure AD B2C テナントをサブスクリプションにリンク](#link-an-azure-ad-b2c-tenant-to-a-subscription)して MAU の課金をアクティブ化します。     |
| 2019 年 11 月 1 日より前にサブスクリプションにリンクされた Azure AD B2C テナント    | [MAU の課金に切り替える (推奨)](#switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants)か、認証ごとの課金モデルをそのまま継続します。     |
| Azure AD B2C テナントであり、さらに Premium の機能 (リスクベースの条件付きアクセスなど) を使用する    | 使用する機能をサポートしている [Azure AD 価格レベルに変更](#change-your-azure-ad-pricing-tier)します。        |
|  |  |

## <a name="about-the-monthly-active-users-mau-billing-model"></a>月間アクティブ ユーザー数 (MAU) の課金モデルについて

MAU の課金は、**2019 年 11 月 1 日** に Azure AD B2C テナントに対して実施されました。 その日以降に作成され、サブスクリプションにリンクされた Azure AD B2C テナントは、MAU 単位で課金されてきました。 サブスクリプションにリンクされていない Azure AD B2C テナントがある場合は、ここで作成する必要があります。 2019 年 11 月 1 日より前にサブスクリプションにリンクされていた既存の Azure AD B2C テナントがある場合は、月間アクティブ ユーザー数 (MAU) の課金モデルにアップグレードすることをお勧めしますが、認証ごとの課金モデルをそのまま継続することもできます。
  
また、Azure AD B2C テナントは、使用する機能に基づいて、適切な Azure 価格レベルにリンクされている必要があります。 Premium 機能には、Azure AD B2C [Premium P1 または P2 価格](https://azure.microsoft.com/pricing/details/active-directory-b2c/)が必要です。 新しい機能を使用するときに、価格レベルのアップグレードが必要になることがあります。 たとえば、条件付きアクセスでは、テナントに対して Azure AD B2C Premium P2 価格レベルを選択する必要があります。

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>Azure AD B2C テナントをサブスクリプションにリンクする

Azure Active Directory B2C (Azure AD B2C) の使用料金は、Azure サブスクリプションに課金されます。 ターゲットの Azure サブスクリプション内に Azure AD B2C *リソース* を作成して、Azure AD B2C テナントを Azure サブスクリプションに明示的にリンクする必要があります。 1 つの Azure サブスクリプションで、仮想マシン、ストレージ アカウント、Logic Apps などの他の Azure リソースと共に、複数の Azure AD B2C リソースを作成できます。 サブスクリプション内のすべてのリソースを表示するには、サブスクリプションが関連付けられている Azure Active Directory (Azure AD) テナントに移動します。

Azure AD B2C テナントにリンクされているサブスクリプションは、追加の Azure AD B2C リソースを含む Azure AD B2C の使用や他の Azure リソースの課金に使用できます。 これは、Azure AD B2C テナント内における他の Azure ライセンス ベース サービスまたは Office 365 ライセンスの追加には使用できません。

### <a name="prerequisites"></a>前提条件

* [Azure サブスクリプション](https://azure.microsoft.com/free/)
* サブスクリプションにリンクする [Azure AD B2C テナント](tutorial-create-tenant.md)
  * テナント管理者である必要があります
  * テナントはまだサブスクリプションにリンクされていない必要があります

### <a name="create-the-link"></a>リンクを作成する

1. [Azure portal](https://portal.azure.com) にサインインします。
2. 上部のメニューで **[ディレクトリ + サブスクリプション]** フィルターを選択し、(Azure AD B2C テナントを含むディレクトリ "*ではなく*") 使用する Azure サブスクリプションを含むディレクトリを選択します。
3. **[リソースの作成]** を選択し、**[Marketplace を検索]** フィールドに「`Active Directory B2C`」と入力し、**[Azure Active Directory B2C]** を選択します。
4. **［作成］** を選択します
5. **[Link an existing Azure AD B2C Tenant to my Azure subscription]\(既存の Azure AD B2C テナントを Azure サブスクリプションにリンクする\)** を選択します。
6. ドロップダウンから **[Azure AD B2C テナント]** を選択します。 自分がグローバル管理者であり、サブスクリプションにまだリンクされていないテナントのみが表示されます。 **[Azure AD B2C リソース名]** フィールドには、選択した Azure AD B2C テナントのドメイン名が入力されます。
7. 自分が管理者であるアクティブな Azure **[サブスクリプション]** を選択します。
8. **[リソース グループ]** の **[新規作成]** を選択し、**[リソース グループの場所]** を指定します。 ここでのリソース グループ設定は、Azure AD B2C テナントの場所、パフォーマンス、または課金状態に影響しません。
9. **［作成］** を選択します

    ![Azure portal の Azure AD B2C リソース作成ページ](./media/billing/portal-01-create-b2c-resource-page.png)

Azure AD B2C テナントについてこれらの手順を完了すると、Azure Direct または Enterprise Agreement の条項 (該当する場合) に基づいて Azure サブスクリプションに課金されます。

## <a name="change-your-azure-ad-pricing-tier"></a>Azure AD 価格レベルを変更する

テナントは、Azure AD B2C テナントで使用する機能に基づいて、適切な Azure 価格レベルにリンクされている必要があります。 Premium 機能には、[Azure Active Directory B2C の価格](https://azure.microsoft.com/pricing/details/active-directory-b2c/)に関するページで説明されているように、Azure AD B2C Premium P1 または P2 が必要です。 場合によっては、新しい機能を使用するときに、価格レベルのアップグレードが必要になることがあります。 たとえば、Azure AD B2C で Identity Protection、リスクベースの条件付きアクセス、Premium P2 の将来の機能などを 使用する場合は、テナントに対して Azure AD B2C Premium P2 価格レベルを選択する必要があります。

価格レベルを変更するには、次の手順に従います。

1. Azure portal にサインインします。

2. 上部のメニューで **[ディレクトリ + サブスクリプション]** フィルターを選択してから、Azure B2C テナントのリンク先の Azure サブスクリプションが含まれるディレクトリを選択します (Azure AD B2C テナント自体を選択 *しないでください*)。

3. ポータルの上部にある検索ボックスに、Azure AD B2C テナントの名前を入力します。 次に、 **[リソース]** の下の検索結果からテナントを選択します。

4. リソースの **[概要]** ページで、 **[価格レベル]** の下にある **[変更]** を選択します。

   ![価格レベルの変更](media/billing/change-pricing-tier.png)
 
5. 有効にする機能が含まれている価格レベルを選択します。

   ![価格レベルの選択](media/billing/select-tier.png)

## <a name="switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants"></a>MAU の課金に切り替える (2019 年 11 月より前の Azure AD B2C テナント)

**2019 年 11 月 1 日** より前に Azure AD B2C テナントをサブスクリプションにリンクした場合は、以前の認証ごとの課金モデルが使用されています。 月間アクティブ ユーザー数 (MAU) の課金モデルにアップグレードすることをお勧めします。 課金オプションは、Azure AD B2C リソースで構成されます。

月間アクティブ ユーザー (MAU) の課金への切り替えは、**元に戻すことができません**。 Azure AD B2C リソースを MAU ベースの課金モデルに変換した後に、そのリソースを認証ごとの課金モデルに戻すことはできません。

既存の Azure AD B2C リソースについて MAU の課金に切り替えるには、次の手順を実行します。

1. Azure AD B2C リソースへの管理アクセス権を持つサブスクリプション所有者として [Azure portal](https://portal.azure.com) にサインインします。

2. 上部のメニューで **[ディレクトリ + サブスクリプション]** フィルターを選択し、MAU の課金にアップグレードする Azure AD B2C ディレクトリを選択します。<br/>

    ![Azure portal の [ディレクトリ + サブスクリプション] フィルター](./media/billing/portal-mau-01-select-b2c-directory.png)

3. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。

4. Azure AD B2C テナントの **[概要]** ページで、**[リソース名]** の下のリンクを選択します。 Azure AD テナントの Azure AD B2C リソースにリダイレクトされます。<br/>

    ![Azure portal で強調表示されている Azure AD B2C リソースのリンク](./media/billing/portal-mau-02-b2c-resource-link.png)

5. Azure AD B2C リソースの **[概要]** ページで、**[請求可能な単位]** の下にある **[認証ごと (MAU に変更)]** リンクを選択します。<br/>

    ![Azure portal で強調表示されている [MAU に変更] リンク](./media/billing/portal-mau-03-change-to-mau-link.png)

6. **[確認]** を選択して、MAU の課金へのアップグレードを完了します。<br/>

    ![Azure portal の MAU ベースの課金確認ダイアログ](./media/billing/portal-mau-04-confirm-change-to-mau.png)


### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>認証ごとの課金から MAU の課金に移行するときに想定されること

MAU ベースの測定は、サブスクリプション/リソース所有者が変更を確定するとすぐに有効になります。 毎月の請求書には、変更まで課金された認証の単位と、変更以降の新しい MAU の単位が反映されます。

移行月にユーザーが二重にカウントされることはありません。 変更前に認証した一意のアクティブ ユーザーには、1 か月間の認証ごとのレートが課金されます。 これらの同じユーザーは、サブスクリプションの残りの請求サイクルの MAU 計算には含まれません。 次に例を示します。

* Contoso B2C テナントには 1,000 人のユーザーがいます。 毎月 250 人のユーザーがアクティブです。 サブスクリプション管理者は、月の 10 日に認証ごとから月間アクティブ ユーザー (MAU) に変更します。
* 1 日から 10 日の課金には、認証ごとのモデルを使用して課金されます。
  * この期間 (1 日から 10 日) に 100 人のユーザーがサインインすると、それらのユーザーには "*その月分の支払い*" とタグが付けられます。
* 10 日 (移行の有効期間) からの課金には、MAU レートで課金されます。
  * この期間 (10 日から 30 日) に追加で 150 人のユーザーがサインインすると、追加の 150 人に対してのみ課金されます。
  * 最初の 100 人のユーザーの継続的なアクティビティは、その月の残りの課金には影響しません。

移行の請求期間中、サブスクリプションの所有者には、Azure サブスクリプションの課金明細書に次の両方の方法 (認証ごとと MAU ごと) の両方のエントリが表示される可能性があります。

* 認証ごとを反映する変更日時までの使用状況のエントリ。
* 月間アクティブ ユーザー (MAU) を反映する変更後の使用状況のエントリ。

Azure AD B2C の使用量の課金と価格の最新情報については、「[Azure Active Directory B2C の価格](https://azure.microsoft.com/pricing/details/active-directory-b2c/)」を参照してください。

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Azure AD B2C テナント リソースを管理する

Azure サブスクリプションに Azure AD B2C リソースを作成すると、他の Azure リソースと共に "B2C テナント" の種類の新しいリソースが表示されます。

このリソースは、以下に使用できます。

* サブスクリプションに移動して課金情報を表示する
* GUID 形式で Azure AD B2C テナントのテナント ID を取得する
* Azure AD B2C テナントに移動する
* サポート リクエストを送信します
* Azure AD B2C テナント リソースを他の Azure サブスクリプションまたはリソース グループに移動する

### <a name="regional-restrictions"></a>リージョンの制限

サブスクリプションで Azure リソースの作成にリージョンの制限を設定した場合、その制限により、Azure AD B2C リソースを作成できない場合があります。

この問題を軽減するには、リージョンの制限を緩和します。

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Azure クラウド ソリューション プロバイダー (CSP) サブスクリプション

Azure AD B2C では Azure クラウド ソリューション プロバイダー (CSP) サブスクリプションがサポートされています。 機能は、Azure AD B2C とすべての Azure リソースで、API または Azure portal を使用して使用できます。 CSP サブスクリプション管理者は、他の Azure リソースと同様に、Azure AD B2C との関係をリンク、移動、および削除できます。

ロールベースのアクセス制御を使用した Azure AD B2C の管理は、Azure AD B2C テナントと Azure CSP サブスクリプション間の関連付けの影響を受けません。 ロールベースのアクセス制御は、サブスクリプションベースのロールではなく、テナントベースのロールを使用して実現されます。

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Azure AD B2C テナントの課金サブスクリプションを変更する

### <a name="move-using-azure-resource-manager"></a>Azure Resource Manager を使用した移動

ソースと宛先のサブスクリプションが同じ Azure Active Directory テナント内に存在する場合は、Azure Resource Manager を使用して Azure AD B2C テナントを別のサブスクリプションに移動できます。

Azure AD B2C テナントなどの Azure リソースを別のサブスクリプションに移動する方法については、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../azure-resource-manager/management/move-resource-group-and-subscription.md)」を参照してください。

移動を開始する前に、必ず記事全体に目を通して、このような移動についての制限事項および要件を十分に理解してください。 リソースを移動する手順だけでなく、移動前のチェックリストや移動操作の検証方法などの重要な情報が含まれています。

### <a name="move-by-unlinking-and-relinking"></a>リンク解除と再リンクによる移動

ソースと宛先のサブスクリプションが異なる Azure Active Directory テナントに関連付けられている場合は、上記の Azure Resource Manager を使用した移動を行うことはできません。 ただし、Azure AD B2C テナントをソースのサブスクリプションからリンク解除して、宛先のサブスクリプションに再リンクする方法でも同じ結果を得ることができます。 削除するオブジェクトが Azure AD B2C テナント自体ではなく、*課金リンク* のみであるため、この方法は安全です。 ユーザー、アプリ、ユーザー フローなどはどれも影響を受けません。

1. Azure AD B2C ディレクトリ自体で、宛先の Azure AD テナント (宛先の Azure サブスクリプションがリンクされているテナント) から [ゲスト ユーザーを招待](user-overview.md#guest-user)し、このユーザーに Azure AD B2C の **グローバル管理者** ロールがあることを確認します。
1. 上記の「[Azure AD B2C テナント リソースを管理する](#manage-your-azure-ad-b2c-tenant-resources)」セクションで説明したように、ソースの Azure サブスクリプション内の Azure AD B2C を表す *Azure リソース* に移動します。 実際の Azure AD B2C テナントに切り替えないでください。
1. **[概要]** ページで、 **[削除]** ボタンを選択します。 これによって、関連する Azure AD B2C テナントのユーザーやアプリケーションが削除されることは *ありません*。 ソースのサブスクリプションから課金リンクが削除されるだけです。
1. 手順 1 で管理者として Azure AD B2C に追加されたユーザー アカウントを使用して Azure portal にサインインします。 次に、宛先の Azure サブスクリプション (宛先の Azure Active Directory テナントにリンクされているもの) に移動します。 
1. 上記の「[リンクを作成する](#create-the-link)」手順に従って、宛先のサブスクリプション内に課金リンクを再確立します。
1. これで、Azure AD B2C リソースが宛先の Azure サブスクリプション (ターゲットの Azure Active Directory にリンクされているもの) に移動され、今後はこのサブスクリプションを通じて課金されるようになります。

## <a name="next-steps"></a>次のステップ

最新の価格情報について、「[Azure Active Directory B2C の価格](https://azure.microsoft.com/pricing/details/active-directory-b2c/)」を参照します。