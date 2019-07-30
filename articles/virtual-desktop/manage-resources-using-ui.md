---
title: 管理ツールのデプロイ - Azure
description: Windows Virtual Desktop プレビューのリソースを管理するためのユーザー インターフェイス ツールをインストールする方法。
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: v-chjenk
ms.openlocfilehash: 9579db9836ef41706f2c6be09570fa7c1459e14f
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620450"
---
# <a name="tutorial-deploy-a-management-tool"></a>チュートリアル:管理ツールをデプロイする

管理ツールは、Microsoft Windows Virtual Desktop プレビューのリソースを管理するためのユーザー インターフェイス (UI) を提供します。 このチュートリアルでは、管理ツールをデプロイして接続する方法を学習します。

>[!NOTE]
>これらの手順は、組織の既存のプロセスで使用できる Windows Virtual Desktop プレビュー固有の構成に対するものです。

## <a name="important-considerations"></a>重要な考慮事項

このアプリでは Windows Virtual Desktop を操作するために同意が必要なため、このツールは企業間 (B2B) シナリオをサポートしていません。 Azure Active Directory (AAD) テナントの各サブスクリプションでは、それ専用として管理ツールを個別にデプロイする必要があります。

この管理ツールはサンプルです。 Microsoft は、セキュリティと品質の重要な更新プログラムを提供します。 [ソース コードは GitHub から入手できます](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy)。 お客様やパートナーには、各自のビジネス ニーズに合わせてツールをカスタマイズすることをお勧めします。

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの実行に必要なもの

Azure Resource Manager テンプレートをデプロイする前に、管理 UI をデプロイする Azure Active Directory ユーザーが必要です。 このユーザーには、次の要件があります。

- Azure Multi-Factor Authentication (MFA) が無効になっている
- ご使用の Azure サブスクリプション内にリソースを作成するためのアクセス許可を持っている
- Azure AD アプリケーションを作成するためのアクセス許可を持っている これらの手順に従って、実際のユーザーが[必要なアクセス許可](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)を持っているかどうかを確認します。

Azure Resource Manager テンプレートをデプロイした後に、管理 UI を起動して検証する必要があります。 このユーザーには、次の要件があります。
- ご使用の Windows Virtual Desktop テナントを表示または編集するためのロールが割り当てられている

## <a name="run-the-azure-resource-manager-template-to-provision-the-management-ui"></a>Azure Resource Manager テンプレートを実行して、管理 UI をプロビジョニングする

開始する前に、[Windows Virtual Desktop の同意ページ](https://rdweb.wvd.microsoft.com)にアクセスして指定した Azure Active Directory (AAD) に対する同意をサーバーおよびクライアント アプリが得ていることを確認してください。

これらの手順に従って、Azure Resource Manager テンプレートをデプロイします。

1. [GitHub の Azure RDS-Templates ページ](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy)に移動します。
2. テンプレートを Azure にデプロイします。
    - Enterprise サブスクリプションにデプロイする場合は、下へスクロールし、 **[Azure に配置する]** を選択します。 「[テンプレート パラメーターのガイダンス](#guidance-for-template-parameters)」を参照してください。
    - クラウド ソリューション プロバイダー サブスクリプションにデプロイする場合は、これらの手順に従って Azure にデプロイします。
        1. 下にスクロールし、 **[Deploy to Azure]\(Azure へのデプロイ\)** を右クリックしてから **[リンク先をコピー]** を選択します。
        2. メモ帳などのテキスト エディターを開き、そこにリンクを貼り付けます。
        3. <https://portal.azure.com/> の直後のハッシュタグ (#) の前に、アット マーク (@) とそれに続けてテナント ドメイン名を入力します。 形式の例を次に示します。<https://portal.azure.com/@Contoso.onmicrosoft.com#create/>。
        4. クラウド ソリューション プロバイダー サブスクリプションに対する管理者/共同作成者のアクセス許可を持つユーザーとして Azure portal にサインインします。
        5. テキスト エディターにコピーしたリンクをアドレス バーに貼り付けます。

### <a name="guidance-for-template-parameters"></a>テンプレート パラメーターのガイダンス
このツールを構成するためのパラメーターを入力する方法は次のとおりです。

- これは、RD ブローカーの URL (https:\//rdbroker.wvd.microsoft.com/) です。
- これは、リソースの URL (https:\//mrs-prod.ame.gbl/mrs-RDInfra-prod) です。
- MFA が無効になっているご自分の AAD 資格情報を使用して、Azure にサインインします。 「[Azure Resource Manager テンプレートの実行に必要なもの](#what-you-need-to-run-the-azure-resource-manager-template)」を参照してください。
- この管理ツールには、ご使用の Azure Active Directory に登録されるアプリケーションの一意の名前を使用します。たとえば、Apr3UX です。

## <a name="provide-consent-for-the-management-tool"></a>管理ツールに対して同意を与える

GitHub の Azure Resource Manager テンプレートが完成すると、Azure portal 内で、1 つの App Service プランと共に 2 つのアプリ サービスがリソース グループに含まれていることを確認できます。

管理ツールにサインインして使用する前に、管理ツールに関連付けられている新しい Azure Active Directory アプリケーションに対して同意を与える必要があります。 同意を与えることにより、管理ツールにサインインしたユーザーに代わり、このツールが Windows Virtual Desktop の管理呼び出しを行うことを許可したことになります。

![UI 管理ツールに同意するときに提示されるアクセス許可を示すスクリーンショット。](media/management-ui-delegated-permissions.png)

このツールへのサインインに使用できるユーザーを確認するには、[Azure Active Directory のユーザー設定ページ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)に移動し、 **[ユーザーはアプリが自身の代わりに会社のデータにアクセスすることを許可できます]** の値に注目します。

![ユーザーがユーザー自身についてのみアプリケーションに与えることができるかどうかを示すスクリーンショット。](media/management-ui-user-consent-allowed.png)

- この値が **[はい]** に設定されている場合、Azure Active Directory 内の任意のユーザー アカウントを使用してサインインし、そのユーザーについてのみ同意を与えることができます。 ただし、後で別のユーザーを使用して管理ツールにサインインした場合、同じ同意をもう一度実行する必要があります。
- この値が **[いいえ]** に設定されている場合、Azure Active Directory のグローバル管理者としてサインインし、ディレクトリ内のすべてのユーザーについて管理者の同意を与える必要があります。 他のユーザーには、同意プロンプトが表示されません。


同意を与えるのに使用するユーザーを決めたら、これらの手順に従って、このツールに同意を与えます。

1. ご使用の Azure リソースに移動し、テンプレート内で指定した名前の Azure App Services リソース (たとえば、Apr3UX) を選択し、それに関連付けられている URL (たとえば、<https://rdmimgmtweb-210520190304.azurewebsites.net>) に移動します。
2. 適切な Azure Active Directory ユーザー アカウントを使用してサインインします。
3. グローバル管理者を使用して認証した場合、ここで **[組織の代理として同意する]** チェック ボックスをオンにできます。 **[同意する]** を選択して同意を与えます。
   
   ![ユーザーまたは管理者に表示される同意ページ全体を示すスクリーンショット。](media/management-ui-consent-page.png)

これで管理ツールに移動します。

## <a name="use-the-management-tool"></a>管理ツールを使用する

組織または指定したユーザーについて同意を与えた後は、いつでも管理ツールにアクセスできます。

これらの手順に従って、ツールを起動します。

1. テンプレート内で指定した名前の Azure App Services リソース (たとえば、Apr3UX) を選択し、それに関連付けられている URL (たとえば、<https://rdmimgmtweb-210520190304.azurewebsites.net>) に移動します。
2. ご自分の Windows Virtual Desktop 資格情報を使用してサインインします。
3. テナント グループを選択するよう求められたら、ドロップダウン リストから **[Default Tenant Group]\(既定のテナント グループ\)** を選択します。

> [!NOTE]
> カスタムのテナント グループがある場合は、ドロップダウン リストから選択せずに、手動でその名前を入力します。

## <a name="next-steps"></a>次の手順

管理ツールをデプロイして接続する方法を学習したので、Azure Service Health を使用して、サービスの問題と正常性の勧告を監視する方法を学習できます。

> [!div class="nextstepaction"]
> [サービス アラートを設定するチュートリアル](./set-up-service-alerts.md)
