---
title: Azure Active Directory B2C の課金モデル
description: Azure AD B2C の月間アクティブ ユーザー (MAU) 課金モデルと、特定の Azure サブスクリプションの課金を有効にする方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 10/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f88993db2ca7fa697aadb584fdfcbd9fe200b11c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85386064"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Azure Active Directory B2C の課金モデル

Azure Active Directory B2C (Azure AD B2C) の使用量は、リンクされた Azure サブスクリプションに課金され、月間アクティブ ユーザー (MAU) の課金モデルが使用されます。 以下のセクションでは、Azure AD B2C リソースをサブスクリプションにリンクする方法と MAU 課金モデルのしくみについて説明します。

> [!IMPORTANT]
> この記事に価格情報は含まれていません。 使用量の課金と価格の最新情報については、「[Azure Active Directory B2C の価格](https://azure.microsoft.com/pricing/details/active-directory-b2c/)」を参照してください。

## <a name="monthly-active-users-mau-billing"></a>月間アクティブ ユーザー (MAU) の課金

Azure AD B2C の課金は一意のユーザーの数と 1 か月間の認証アクティビティに基づいて計算されます。これは月間アクティブ ユーザー (MAU) の課金と呼ばれます。

**2019 年 11 月 1 日**以降、新しく作成されたすべての Azure AD B2C テナントには、月間アクティブ ユーザー (MAU) ごとに課金されます。 2019 年 11 月 1 日以降に[サブスクリプションにリンク](#link-an-azure-ad-b2c-tenant-to-a-subscription)されている既存のテナントには、月間アクティブ ユーザー (MAU) ごとに課金されます。

2019 年 11 月 1 日より前にサブスクリプションにリンクされていた既存の Azure AD B2C テナントがある場合は、次のいずれかを選択できます。

* 月間アクティブ ユーザー (MAU) の課金モデルにアップグレードする、または
* 認証ごとの課金モデルのままにする

### <a name="upgrade-to-monthly-active-users-billing-model"></a>月間アクティブ ユーザーの課金モデルにアップグレードする

Azure AD B2C リソースへの管理アクセス権を持つ Azure サブスクリプション所有者は、MAU 課金モデルに切り替えることができます。 課金オプションは、Azure AD B2C リソースで構成されます。

月間アクティブ ユーザー (MAU) の課金への切り替えは、**元に戻すことができません**。 Azure AD B2C リソースを MAU ベースの課金モデルに変換した後に、そのリソースを認証ごとの課金モデルに戻すことはできません。

既存の Azure AD B2C リソースについて MAU の課金に切り替えるには、次の手順を実行します。

1. サブスクリプション所有者として [Azure portal](https://portal.azure.com) にサインインします。
1. 上部のメニューで **[ディレクトリ + サブスクリプション]** フィルターを選択し、MAU の課金にアップグレードする Azure AD B2C ディレクトリを選択します。<br/>
    ![Azure portal の [ディレクトリ + サブスクリプション] フィルター](./media/billing/portal-mau-01-select-b2c-directory.png)
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. Azure AD B2C テナントの **[概要]** ページで、 **[リソース名]** の下のリンクを選択します。 Azure AD テナントの Azure AD B2C リソースにリダイレクトされます。<br/>
    ![Azure portal で強調表示されている Azure AD B2C リソースのリンク](./media/billing/portal-mau-02-b2c-resource-link.png)
1. Azure AD B2C リソースの **[概要]** ページで、 **[請求可能な単位]** の下にある **[認証ごと (MAU に変更)]** リンクを選択します。<br/>
    ![Azure portal で強調表示されている [MAU に変更] リンク](./media/billing/portal-mau-03-change-to-mau-link.png)
1. **[確認]** を選択して、MAU の課金へのアップグレードを完了します。<br/>
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

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>Azure AD B2C テナントをサブスクリプションにリンクする

Azure Active Directory B2C (Azure AD B2C) の使用料金は、Azure サブスクリプションに課金されます。 Azure AD B2C テナントを作成したら、テナント管理者はその Azure AD B2C テナントを Azure サブスクリプションに明示的にリンクする必要があります。

サブスクリプションをリンクするには、ターゲットの Azure サブスクリプション内に Azure AD B2C "*リソース*" を作成します。 1 つの Azure サブスクリプションで、仮想マシン、ストレージ アカウント、Logic Apps などの他の Azure リソースと共に、複数の Azure AD B2C リソースを作成できます。 サブスクリプション内のすべてのリソースを表示するには、サブスクリプションが関連付けられている Azure Active Directory (Azure AD) テナントに移動します。

Azure AD B2C テナントにリンクされているサブスクリプションは、追加の Azure AD B2C リソースを含む Azure AD B2C の使用や他の Azure リソースの課金に使用できます。 これは、Azure AD B2C テナント内における他の Azure ライセンス ベース サービスまたは Office 365 ライセンスの追加には使用できません。

### <a name="prerequisites"></a>前提条件

* [Azure サブスクリプション](https://azure.microsoft.com/free/)
* サブスクリプションにリンクする [Azure AD B2C テナント](tutorial-create-tenant.md)
  * テナント管理者である必要があります
  * テナントはまだサブスクリプションにリンクされていない必要があります

### <a name="create-the-link"></a>リンクを作成する

1. [Azure portal](https://portal.azure.com) にサインインする
1. 上部のメニューで **[ディレクトリ + サブスクリプション]** フィルターを選択し、(Azure AD B2C テナントを含むディレクトリ "*ではなく*") 使用する Azure サブスクリプションを含むディレクトリを選択します。
1. **[リソースの作成]** を選択し、**[Marketplace を検索]** フィールドに「`Active Directory B2C`」と入力し、**[Azure Active Directory B2C]** を選択します。
1. **[作成]**
1. **[Link an existing Azure AD B2C Tenant to my Azure subscription]\(既存の Azure AD B2C テナントを Azure サブスクリプションにリンクする\)** を選択します。
1. ドロップダウンから **[Azure AD B2C テナント]** を選択します。 自分がグローバル管理者であり、サブスクリプションにまだリンクされていないテナントのみが表示されます。 **[Azure AD B2C リソース名]** フィールドには、選択した Azure AD B2C テナントのドメイン名が入力されます。
1. 自分が管理者であるアクティブな Azure **[サブスクリプション]** を選択します。
1. **[リソース グループ]** の **[新規作成]** を選択し、 **[リソース グループの場所]** を指定します。 ここでのリソース グループ設定は、Azure AD B2C テナントの場所、パフォーマンス、または課金状態に影響しません。
1. **作成** を選択します。
    ![Azure portal の Azure AD B2C リソース作成ページ](./media/billing/portal-01-create-b2c-resource-page.png)

Azure AD B2C テナントについてこれらの手順を完了すると、Azure Direct または Enterprise Agreement の条項 (該当する場合) に従って Azure サブスクリプションに課金されます。

### <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Azure AD B2C テナント リソースを管理する

Azure サブスクリプションに Azure AD B2C リソースを作成すると、他の Azure リソースと共に "B2C テナント" の種類の新しいリソースが表示されます。

このリソースは、以下に使用できます。

* サブスクリプションに移動して課金情報を表示する
* GUID 形式で Azure AD B2C テナントのテナント ID を取得する
* Azure AD B2C テナントに移動する
* サポート リクエストを送信します
* Azure AD B2C テナント リソースを他の Azure サブスクリプションまたはリソース グループに移動する

![Azure portal の B2C リソース設定ページ](./media/billing/portal-02-b2c-resource-overview.png)

### <a name="regional-restrictions"></a>リージョンの制限

サブスクリプションで Azure リソースの作成にリージョンの制限を設定した場合、その制限により、Azure AD B2C リソースを作成できない場合があります。

この問題を軽減するには、リージョンの制限を緩和します。

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Azure クラウド ソリューション プロバイダー (CSP) サブスクリプション

Azure AD B2C では Azure クラウド ソリューション プロバイダー (CSP) サブスクリプションがサポートされています。 機能は、Azure AD B2C とすべての Azure リソースで、API または Azure portal を使用して使用できます。 CSP サブスクリプション管理者は、他の Azure リソースと同様に、Azure AD B2C との関係をリンク、移動、および削除できます。

ロールベースのアクセス制御を使用した Azure AD B2C の管理は、Azure AD B2C テナントと Azure CSP サブスクリプション間の関連付けの影響を受けません。 ロールベースのアクセス制御は、サブスクリプションベースのロールではなく、テナントベースのロールを使用して実現されます。

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Azure AD B2C テナントの課金サブスクリプションを変更する

Azure AD B2C テナントは、ソースと宛先のサブスクリプションが同じ Azure Active Directory テナント内に存在する場合、別のサブスクリプションに移動できます。

Azure AD B2C テナントなどの Azure リソースを別のサブスクリプションに移動する方法については、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../azure-resource-manager/management/move-resource-group-and-subscription.md)」を参照してください。

移動を開始する前に、必ず記事全体に目を通して、このような移動についての制限事項および要件を十分に理解してください。 リソースを移動する手順だけでなく、移動前のチェックリストや移動操作の検証方法などの重要な情報が含まれています。

## <a name="next-steps"></a>次のステップ

最新の価格情報について、「[Azure Active Directory B2C の価格](https://azure.microsoft.com/pricing/details/active-directory-b2c/)」を参照します。
