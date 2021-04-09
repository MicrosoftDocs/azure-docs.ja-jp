---
title: Azure Resource Manager テンプレートを使用して管理ツールをデプロイする - Azure
description: Azure Resource Manager テンプレートを使用して Windows Virtual Desktop (クラシック) のリソースを管理するためのユーザー インターフェイス ツールをインストールする方法。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f96365bcdf64d19dc0b894f2f1230233b3137bc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94842653"
---
# <a name="deploy-a-windows-virtual-desktop-classic-management-tool-with-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して Windows Virtual Desktop (クラシック) 管理ツールをデプロイする

>[!IMPORTANT]
>この内容は、Azure Resource Manager Windows Virtual Desktop オブジェクトをサポートしていない Windows Virtual Desktop (クラシック) に適用されます。

この記事の手順では、Azure Resource Manager テンプレートを使用して UI をデプロイする方法を説明します。

## <a name="important-considerations"></a>重要な考慮事項

このアプリでは Windows Virtual Desktop を操作するために同意が必要なため、このツールは企業間 (B2B) シナリオをサポートしていません。 Azure Active Directory (AAD) テナントの各サブスクリプションでは、それ専用として管理ツールを個別にデプロイする必要があります。

この管理ツールはサンプルです。 Microsoft は、セキュリティと品質の重要な更新プログラムを提供します。 [ソース コードは GitHub から入手できます](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy)。 お客様やパートナーには、各自のビジネス ニーズに合わせてツールをカスタマイズすることをお勧めします。

管理ツールは、次のブラウザーに対応しています。
- Google Chrome 68 以降
- Microsoft Edge 40.15063 以降
- Mozilla Firefox 52.0 以降
- Safari 10 以降 (macOS のみ)

## <a name="what-you-need-to-deploy-the-management-tool"></a>管理ツールのデプロイに必要なもの

管理ツールをデプロイする前に、アプリの登録を作成して管理 UI をデプロイする Azure Active Directory (Azure AD) ユーザーが必要です。 このユーザーには、次の要件があります。

- Azure AD Multi-Factor Authentication (MFA) が無効になっている
- ご使用の Azure サブスクリプション内にリソースを作成するためのアクセス許可を持っている
- Azure AD アプリケーションを作成するためのアクセス許可を持っている 次のステップに従い、あなたのユーザーが[「必要なアクセス許可」](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)の指示に従って必要なアクセス許可を保持しているかどうかを確認します。

管理ツールをデプロイして構成した後は、管理 UI を起動し、すべてが動作することを確認することを推奨します。 管理 UI を起動するユーザーには、Windows Virtual Desktop テナントを表示/編集できるロールの割り当てが必要です。

## <a name="deploy-the-management-tool"></a>管理ツールをデプロイする

開始する前に、[Windows Virtual Desktop の同意ページ](https://rdweb.wvd.microsoft.com)にアクセスして指定した Azure Active Directory (AAD) に対する同意をサーバーおよびクライアント アプリが得ていることを確認してください。

これらの手順に従って、Azure Resource Manager テンプレートをデプロイします。

1. [GitHub の Azure RDS-Templates ページ](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy)に移動します。
2. テンプレートを Azure にデプロイします。
    - Enterprise サブスクリプションにデプロイする場合は、下へスクロールし、 **[Azure に配置する]** を選択します。
    - クラウド ソリューション プロバイダー サブスクリプションにデプロイする場合は、これらの手順に従って Azure にデプロイします。
        1. 下にスクロールし、 **[Deploy to Azure]\(Azure へのデプロイ\)** を右クリックしてから **[リンク先をコピー]** を選択します。
        2. メモ帳などのテキスト エディターを開き、そこにリンクを貼り付けます。
        3. <https://portal.azure.com/> の直後のハッシュタグ (#) の前に、アット マーク (@) とそれに続けてテナント ドメイン名を入力します。 形式の例を次に示します<https://portal.azure.com/@Contoso.onmicrosoft.com#create/>。
        4. クラウド ソリューション プロバイダー サブスクリプションに対する管理者/共同作成者のアクセス許可を持つユーザーとして Azure portal にサインインします。
        5. テキスト エディターにコピーしたリンクをアドレス バーに貼り付けます。
3. パラメーターを入力するときには、次の手順を実行します。
    - **isServicePrincipal** パラメーターには、**false** を選択します。
    - 資格情報には、多要素認証を無効にした Azure AD 資格情報を入力します。 これらの資格情報は、Azure AD アプリケーションと Azure リソースを作成するために使用されます。 詳細については、[管理ツールのデプロイに必要なもの](#what-you-need-to-deploy-the-management-tool)に関するページを参照してください。
    - **applicationName** には、ご使用の Azure Active Directory に登録されるアプリの一意の名前を使用します。 この名前は、Web アプリの URL にも使用されます。 たとえば、"Apr3UX" のような名前を使用できます。
4. パラメーターを入力したら、使用条件に同意して、 **[購入]** を選択します。

## <a name="provide-consent-for-the-management-tool"></a>管理ツールに対して同意を与える

GitHub の Azure Resource Manager テンプレートが完成すると、Azure portal 内で、1 つの App Service プランと共に 2 つのアプリ サービスがリソース グループに含まれていることを確認できます。

管理ツールにサインインして使用する前に、管理ツールに関連付けられている新しい Azure AD アプリケーションに対して同意を与える必要があります。 同意を与えることで、管理ツールはツールに現在サインインしているユーザーに代わって Windows Virtual Desktop の管理呼び出しを行うことができるようになります。

> [!div class="mx-imgBorder"]
> ![UI 管理ツールに同意するときに提示されるアクセス許可を示すスクリーンショット。](../media/management-ui-delegated-permissions.png)

このツールへのサインインに使用できるユーザーを確認するには、[Azure Active Directory のユーザー設定ページ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)に移動し、 **[ユーザーはアプリが自身の代わりに会社のデータにアクセスすることを許可できます]** の値に注目します。

> [!div class="mx-imgBorder"]
> ![ユーザーがユーザー自身についてのみアプリケーションに同意を与えることができるかどうかを示すスクリーンショット。](../media/management-ui-user-consent-allowed.png)

- この値が **[はい]** に設定されている場合、Azure Active Directory 内の任意のユーザー アカウントを使用してサインインし、そのユーザーについてのみ同意を与えることができます。 ただし、後で別のユーザーを使用して管理ツールにサインインした場合、同じ同意をもう一度実行する必要があります。
- この値が **[いいえ]** に設定されている場合、Azure Active Directory のグローバル管理者としてサインインし、ディレクトリ内のすべてのユーザーについて管理者の同意を与える必要があります。 他のユーザーには、同意プロンプトが表示されません。


同意を与えるために使用するユーザーを決めたら、これらの手順に従って、このツールに同意を与えます。

1. ご使用の Azure リソースに移動し、テンプレート内で指定した名前の Azure App Services リソース (たとえば、Apr3UX) を選択し、それに関連付けられている URL (たとえば、`https://rdmimgmtweb-210520190304.azurewebsites.net`) に移動します。
2. 適切な Azure Active Directory ユーザー アカウントを使用してサインインします。
3. グローバル管理者を使用して認証した場合、ここで **[組織の代理として同意する]** チェック ボックスをオンにできます。 **[同意する]** を選択して同意を与えます。

   > [!div class="mx-imgBorder"]
   > ![ユーザーまたは管理者に表示される同意ページ全体を示すスクリーンショット。](../media/management-ui-consent-page.png)

これで管理ツールに移動します。

## <a name="use-the-management-tool"></a>管理ツールを使用する

組織または指定したユーザーについて同意を与えた後は、いつでも管理ツールにアクセスできます。

これらの手順に従って、ツールを起動します。

1. テンプレート内で指定した名前の Azure App Services リソース (たとえば、Apr3UX) を選択し、それに関連付けられている URL (たとえば、`https://rdmimgmtweb-210520190304.azurewebsites.net`) に移動します。
2. ご自分の Windows Virtual Desktop 資格情報を使用してサインインします。
3. テナント グループを選択するよう求められたら、ドロップダウン リストから **[Default Tenant Group]\(既定のテナント グループ\)** を選択します。
4. **[既定のテナント グループ]** を選択すると、ウィンドウの右側にメニューが表示されます。 このメニューの中から、テナント グループの名前を探して選択します。

  > [!NOTE]
  > カスタムのテナント グループがある場合は、ドロップダウン リストから選択せずに、手動でその名前を入力します。

## <a name="report-issues"></a>レポートに関する問題

管理ツールまたはその他の Windows Virtual Desktop ツールで問題が発生した場合には、「[Remote Desktop Services のための Azure Resource Manager テンプレート](https://github.com/Azure/RDS-Templates/blob/master/README.md)」の指示に従い、GitHub でそれを報告してください。

## <a name="next-steps"></a>次のステップ

これで、管理ツールをデプロイして接続する方法が学習できました。次は、Azure Service Health を使用してサービスの問題と正常性の勧告をモニターする方法を学習しましょう。 詳細については、[「サービスアラート設定のチュートリアル」](set-up-service-alerts-2019.md)を参照してください。