---
title: Azure サブスクリプションとアカウントをアクティブ化する | Microsoft Docs
description: Azure Resource Manager API による新規アカウントおよび既存アカウントへのアクセスを有効にし、アカウントに関する一般的な問題を解決します。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/19/2019
ms.topic: quickstart
ms.service: cost-management
manager: vitavor
ms.custom: secdec18
ms.openlocfilehash: 53ac79c257e88380d462e5ef69ed494ae9967c07
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155181"
---
# <a name="activate-azure-subscriptions-and-accounts-with-cloudyn"></a>Cloudyn で Azure サブスクリプションとアカウントをアクティブ化する

Azure Resource Manager の資格情報を追加または更新すると、Cloudyn は Azure テナント内のすべてのアカウントとサブスクリプションを検出できるようになります。 仮想マシンで Azure Diagnostics 拡張機能も有効になっていると、Cloudyn は CPU やメモリなどの拡張メトリックを収集できます。 この記事では、Azure Resource Manager API を使って新規アカウントおよび既存アカウントに対するアクセスを有効にする方法を説明します。 また、アカウントに関する一般的な問題を解決する方法についても説明します。

サブスクリプションが _アクティブ化されていない_ 場合、Cloudyn はほとんどの Azure サブスクリプション データにアクセスできません。 Cloudyn がこれらのデータにアクセスできるように、_アクティブ化されていない_ アカウントを編集する必要があります。

## <a name="required-azure-permissions"></a>必要な Azure アクセス許可

この記事の手順を最後まで行うには、特定のアクセス許可が必要です。 ユーザーまたはユーザーのテナント管理者は、次のアクセス許可を両方とも持っている必要があります。

- Azure AD テナントに CloudynCollector アプリケーションを登録するためのアクセス許可。
- Azure サブスクリプションのロールにアプリケーションを割り当てる権限。

CloudynCollector アプリケーションを割り当てるには、Azure サブスクリプションのアカウントに `Microsoft.Authorization/*/Write` アクセス権が必要です。 このアクションは、[所有者](../role-based-access-control/built-in-roles.md#owner)ロールまたは[ユーザー アクセス管理者](../role-based-access-control/built-in-roles.md#user-access-administrator)ロールを通じて許可されます。

アカウントが**共同作成者**ロールに割り当てられている場合は、アプリケーションの割り当てに必要な適切なアクセス許可がありません。 CloudynCollector アプリケーションを Azure サブスクリプションに割り当てようとすると、エラーが表示されます。

### <a name="check-azure-active-directory-permissions"></a>Azure Active Directory のアクセス許可を確認する

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. Azure Portal で、 **[Azure Active Directory]** を選びます。
3. Azure Active Directory で **[ユーザー設定]** を選択します。
4. **[アプリの登録]** オプションを確認します。
    - **[はい]** に設定されている場合は、管理者以外のユーザーでも AD アプリを登録できます。 この設定は、Azure AD テナント内のすべてのユーザーがアプリを登録できることを意味します。  
    ![[ユーザー設定] で [アプリの登録] を選択](./media/activate-subs-accounts/app-register.png)
    - **[アプリの登録]** オプションが **[いいえ]** に設定されている場合は、テナント管理者ユーザーのみが Azure Active Directory アプリを登録できます。 テナント管理者が CloudynCollector アプリケーションを登録する必要があります。


## <a name="add-an-account-or-update-a-subscription"></a>アカウントを追加するか、サブスクリプションを更新する

アカウントを追加するか、またはサブスクリプションを更新すると、Cloudyn に Azure データへのアクセス権が付与されます。

### <a name="add-a-new-account-subscription"></a>新しいアカウント (サブスクリプション) を追加する

1. Cloudyn ポータルで、右上にある歯車アイコンをクリックし、 **[クラウド アカウント]** を選択します。
2. **[Add new account]\(新しいアカウントの追加\)** をクリックし、 **[Add new account]\(新しいアカウントの追加\)** ボックスを表示します。 必要な情報を入力します。  
    ![[新しいアカウントを追加] ボックスに必要な情報を入力](./media/activate-subs-accounts/add-new-account.png)

### <a name="update-a-subscription"></a>サブスクリプションを更新する

1. アカウント管理で Cloudyn に既に存在する _アクティブ化されていない_ サブスクリプションを更新する場合は、親 _テナント GUID_ の右にある編集用の鉛筆アイコンをクリックします。 サブスクリプションは親テナントの下でグループ化されるため、サブスクリプションを個別にアクティブ化しないようにします。
    ![[サブスクリプションの再検出] ボックスで自分のテナント ID を選択](./media/activate-subs-accounts/existing-sub.png)
2. 必要に応じて、テナント ID を入力します。 テナント ID がわからない場合は、次の手順を使って調べます。
    1. [Azure Portal](https://portal.azure.com) にサインインします。
    2. Azure Portal で、 **[Azure Active Directory]** を選びます。
    3. テナント ID を取得するには、Azure AD テナントの **[プロパティ]** を選択します。
    4. [ディレクトリ ID] の GUID をコピーします。 この値がテナント ID です。
    詳しくは、「[テナント ID を取得する](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)」をご覧ください。
3. 必要に応じて、レート ID を選びます。 レート ID がわからない場合は、次の手順を使って調べます。
    1. Azure Portal の右上にあるユーザー情報をクリックし、 **[明細の表示]** をクリックします。
    2. **[請求先アカウント]** で **[サブスクリプション]** をクリックします。
    3. **[個人用サブスクリプション]** で、サブスクリプションを選びます。
    4. **[プラン ID]** の下にレート ID が表示されます。 サブスクリプションのオファー ID をコピーします。
4. [新しいアカウントの追加] (または [サブスクリプションの編集]) ボックスで、 **[保存]** (または **[次へ]** ) をクリックします。 Azure Portal にリダイレクトされます。
5. ポータルにサインインします。 **[許可]** をクリックして、Cloudyn Collector の Azure アカウントへのアクセスを承認します。

    [Cloudyn Accounts] (Cloudyn アカウント) 管理ページにリダイレクトされ、サブスクリプションが**アクティブな**アカウント状態に更新されます。 Resource Manager の列の下に緑のチェックマーク記号が表示されます。

    1 つ以上のサブスクリプションに緑色のチェックマーク記号が表示されていない場合は、そのサブスクリプションのリーダー アプリ (CloudynCollector) を作成するためのアクセス許可がないことを示しています。 そのサブスクリプションのより高いアクセス許可を持つユーザーが、この手順を繰り返す必要があります。

このプロセスを説明している「[Cloudyn での Azure Resource Manager への接続](https://youtu.be/oCIwvfBB6kk)」のビデオを見てください。

>[!VIDEO https://www.youtube.com/embed/oCIwvfBB6kk?ecver=1]

## <a name="resolve-common-indirect-enterprise-set-up-problems"></a>間接加入契約のエンタープライズ設定に関する一般的な問題を解決する

Cloudyn ポータルを始めて使用する際、Enterprise Agreement またはクラウド ソリューション プロバイダー (CSP) ユーザーのお客様の場合、次のメッセージが表示されることがあります。

- **Cloudyn の設定**ウィザードに、*The specified API key is not a top level enrollment key (指定された API キーは最上位レベルの登録キーではありません)* と表示されます。
- Enterprise Agreement ポータル: *Direct Enrollment – No* (直接登録 – いいえ)。
- Cloudyn ポータルに *No usage data was found for the last 30 days.Please contact your distributor to make sure markup was enabled for your Azure account\(過去 30 日間に使用状況データが見つかりませんでした。ディストリビューターに連絡してお使いの Azure アカウントに対してマークアップが有効になっていることを確認してください\)* と表示される。

前述のメッセージは、Azure Enterprise Agreement を再販業者または CSP から購入したことを示します。 Cloudyn でデータを確認するには、再販業者または CSP がユーザーの Azure アカウントの "_マークアップ_" を有効にする必要があります。

問題の修正方法は次のとおりです。

1. 再販業者がユーザーのアカウントの "_マークアップ_" を有効にする。 手順については、「[間接加入契約者向けオンボード ガイド](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide)」をご覧ください。
2. ユーザーが Cloudyn で使用する Azure Enterprise Agreement キーを生成する。 手順については、「[Azure エンタープライズ契約を登録してコスト データを表示する](https://docs.microsoft.com/azure/cost-management/quick-register-ea)」をご覧ください。

Azure Enterprise Agreement API キーを生成して Cloudyn を設定する前に、次の手順に従って Azure Billing API を有効にする必要があります。

- [企業ユーザー向けの Reporting API の概要](../billing/billing-enterprise-api.md)
- 「**Enabling data access to the API (API へのデータ アクセスを有効にする)** 」の「[Microsoft Azure Enterprise Portal Reporting API](https://ea.azure.com/helpdocs/reportingAPI)」

また、部門管理者、アカウント所有者、およびエンタープライズ管理者に、Billing API による "_請求を確認する_" アクセス許可を付与する必要がある場合もあります。

Cloudyn を有効にできるのは、Azure サービス管理者だけです。 共同管理者のアクセス許可では不十分です。 ただし、この管理者の要件は回避できます。 Azure Active Directory 管理者に、PowerShell スクリプトを使用して **CloudynAzureCollector** を承認するアクセス許可を付与するように要求できます。 次のスクリプトは、Azure Active Directory の Service Principal **CloudynAzureCollector** を登録するアクセス許可を付与します。


```powershell
#THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

#Tenant - enter your tenant ID or Name
$tenant = "<ReplaceWithYourTenantID>"

#Cloudyn Collector application ID
$appId = "83e638ef-7885-479f-bbe8-9150acccdb3d"

#URL to activate the consent screen
$url = "https://login.windows.net/"+$tenant+"/oauth2/authorize?api-version=1&response_type=code&client_id="+$appId+"&redirect_uri=http%3A%2F%2Flocalhost%3A8080%2FCloudynJava&prompt=consent"

#Choose your browser, the default is Internet Explorer

#Chrome
#[System.Diagnostics.Process]::Start("chrome.exe", "--incognito $url")

#Firefox
#[System.Diagnostics.Process]::Start("firefox.exe","-private-window $url" )

#IExplorer
[System.Diagnostics.Process]::Start("iexplore.exe","$url -private" )

```

## <a name="next-steps"></a>次の手順

- Cloudyn の最初のチュートリアルをまだ完了していない場合は、それを「[使用状況とコストを確認する](tutorial-review-usage.md)」で参照してください。
