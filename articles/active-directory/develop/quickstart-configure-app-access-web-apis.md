---
title: クイック スタート:Web API にアクセスするためにアプリを構成する | Azure
titleSuffix: Microsoft identity platform
description: このクイック スタートでは、クライアント アプリケーションに対してスコープ指定されたリソースへのアクセス (アクセス許可) を有効にするために、Microsoft ID プラットフォームで Web API を表すアプリ登録を構成します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, contperf-fy21q1
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: 0b064e8491b5d4fa988e9f476143cc0711e3934a
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "100651376"
---
# <a name="quickstart-configure-a-client-application-to-access-a-web-api"></a>クイック スタート:Web API にアクセスするようにクライアント アプリケーションを構成する

このクイック スタートでは、Microsoft ID プラットフォームに登録されたクライアント アプリに、独自の Web API へのスコープ指定されたアクセス許可ベースのアクセスを提供します。 また、クライアント アプリに Microsoft Graph へのアクセスを提供します。

クライアント アプリの登録時に Web API のスコープを指定することにより、それらのスコープを含むアクセス トークンを Microsoft ID プラットフォームからクライアント アプリに取得できます。 次にそのコード内で、Web API により、アクセス トークンにあるスコープに基づいて、リソースに対するアクセス許可ベースのアクセスを提供できます。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションを持つ Azure アカウント - [アカウントを無料で作成する](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* 次の項目の完了: 「[クイックスタート: アプリケーションを登録する](quickstart-register-app.md)」
* 次の項目の完了: 「[クイックスタート: Web API を公開するようにアプリケーションを構成する](quickstart-configure-app-expose-web-apis.md)」

## <a name="add-permissions-to-access-your-web-api"></a>Web API にアクセスするためのアクセス許可を追加する

最初のシナリオでは、クライアント アプリに独自の Web API へのアクセス権を付与します。両方とも、前提条件の一部として登録済みである必要があります。 クライアント アプリと Web API の両方をまだ登録していない場合は、この 2 つの[前提条件](#prerequisites)に関する記事の手順を完了してください。

この図は、2 つのアプリの登録が相互にどのように関連しているかを示しています。 このセクションでは、クライアント アプリの登録にアクセス許可を追加します。

:::image type="content" source="media/quickstart-configure-app-access-web-apis/diagram-01-app-permission-to-api-scopes.svg" alt-text="右側に Web API と公開されたスコープ、左側にそれらのスコープをアクセス許可として選択したクライアント アプリが示されている直線図" border="false":::

クライアント アプリと Web API の両方を登録し、スコープを作成して API を公開したら、次の手順に従って、API に対するクライアントのアクセス許可を構成できます。

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
1. 複数のテナントにアクセスできる場合は、上部のメニューの **[ディレクトリ + サブスクリプション]** フィルター :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: を使用して、クライアント アプリの登録を含むテナントを選択します。
1. **[Azure Active Directory]**  >  **[アプリの登録]** を選択し、(Web API "*ではなく*") クライアント アプリケーションを選択します。
1. **[API のアクセス許可]** 、 **[アクセス許可の追加]** 、 **[自分の API]** の順に選択します。
1. 前提条件の一部として登録した Web API を選択します。

    既定では、 **[委任されたアクセス許可]** が選択されています。 委任されたアクセス許可は、サインインしたユーザーとして Web API にアクセスするクライアント アプリに適しています。そのアクセスは、次のステップで選択するアクセス許可に限定する必要があります。 この例では、 **[委任されたアクセス許可]** を選択したままにします。

    **[アプリケーションのアクセス許可]** は、サインインや同意のためのユーザー操作なしに、それら自体として Web API にアクセスする必要があるサービスまたはデーモン タイプのアプリケーションを対象としています。 Web API のアプリケーション ロールを定義していない限り、このオプションは無効になります。
1. **[アクセス許可の選択]** で、Web API 用にスコープを定義したリソースを展開し、サインインしたユーザーの代わりとしてのクライアント アプリに必要なアクセス許可を選択します。

    前のクイック スタートで指定したスコープ名の例を使用した場合、**Employees.Read.All** と **Employees.Write.All** が表示されます。
    **Employees.Read.All** を選択するか、前提条件の完了時に作成した別のアクセス許可を選択します。
1. **[アクセス許可の追加]** を選択してプロセスを完了します。

ご自身の API にアクセス許可を追加した後、選択したアクセス許可が **[構成されたアクセス許可]** に表示されます。 次の図では、*Employees.Read.All* の委任されたアクセス許可の例をクライアント アプリの登録に追加しています。

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-02-configured-permissions-pane.png" alt-text="新しく追加されたアクセス許可を示す Azure portal の [構成されたアクセス許可] ウィンドウ":::

Microsoft Graph API に対する *User.Read* アクセス許可も表示されています。 このアクセス許可は、Azure portal にアプリを登録すると自動的に追加されます。

## <a name="add-permissions-to-access-microsoft-graph"></a>Microsoft Graph にアクセスするためのアクセス許可を追加する

アプリケーションでは、サインインしたユーザーの代わりに独自の Web API にアクセスすることに加えて、Microsoft Graph に格納されているユーザーの (またはその他の) データにアクセスしたり、そのデータを変更したりすることが必要な場合があります。 または、サービスまたはデーモン アプリがそれ自体として Microsoft Graph にアクセスし、ユーザーによる操作なしで操作を実行することが必要な場合があります。

### <a name="delegated-permission-to-microsoft-graph"></a>Microsoft Graph への委任されたアクセス許可

Microsoft Graph に対する委任されたアクセス許可を構成することで、クライアント アプリケーションがログインしたユーザーの代わりに操作を実行できるようになり、たとえば、電子メールを読んだり、プロファイルを変更したりできます。 既定では、クライアント アプリのユーザーは、構成済みの委任されたアクセス許可への同意をログイン時に求められます。

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
1. 複数のテナントにアクセスできる場合は、上部のメニューの **[ディレクトリ + サブスクリプション]** フィルター :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: を使用して、クライアント アプリの登録を含むテナントを選択します。
1. **[Azure Active Directory]**  >  **[アプリの登録]** を選択し、クライアント アプリケーションを選択します。
1. **[API のアクセス許可]**  >  **[アクセス許可の追加]**  >  **[Microsoft Graph]** を選択します。
1. **[委任されたアクセス許可]** を選択します。 Microsoft Graph には多くのアクセス許可が公開されており、最もよく使用されるものが一覧の一番上に表示されます。
1. **[アクセス許可の選択]** で次のアクセス許可を選択します。

    | 権限       | 説明                                         |
    |------------------|-----------------------------------------------------|
    | `email`          | ユーザーの電子メール アドレスの表示                           |
    | `offline_access` | アクセス権を付与したデータへのアクセスの管理 |
    | `openid`         | ユーザーをサインインする                                       |
    | `profile`        | ユーザーの基本プロファイルの表示                           |
1. **[アクセス許可の追加]** を選択してプロセスを完了します。

アクセス許可を構成すると常に、アプリのユーザーは、アプリが彼らに代わってリソース API にアクセスできるようにするための同意をサインイン時に求められます。

管理者は、ユーザーがそうすることを求められることがないように、"*すべての*" ユーザーに代わって同意を与えることもできます。 管理者の同意については、この記事の「[API のアクセス許可と管理者の同意に関する詳細](#more-on-api-permissions-and-admin-consent)」で後ほど説明します。

### <a name="application-permission-to-microsoft-graph"></a>Microsoft Graph へのアプリケーションのアクセス許可

ユーザーによる操作や同意なしにそれ自体として認証される必要があるアプリケーション用に、アプリケーションのアクセス許可を構成します。 アプリケーションのアクセス許可は、通常、API に "ヘッドレス" 方式でアクセスするバックグラウンド サービスやデーモン アプリ、および別の (ダウンストリーム) API にアクセスする Web API によって使用されます。

以下の手順では、例として、Microsoft Graph に *Files.Read.All* アクセス許可を付与します。

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
1. 複数のテナントにアクセスできる場合は、上部のメニューの **[ディレクトリ + サブスクリプション]** フィルター :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: を使用して、クライアント アプリの登録を含むテナントを選択します。
1. **[Azure Active Directory]**  >  **[アプリの登録]** を選択し、クライアント アプリケーションを選択します。
1. **[API のアクセス許可]**  >  **[アクセス許可の追加]**  >  **[Microsoft Graph]**  >  **[アプリケーションのアクセス許可]** を選択します。
1. Microsoft Graph によって公開されているすべてのアクセス許可が、 **[アクセス許可の選択]** に表示されます。
1. アプリケーションに付与する 1 つ以上のアクセス許可を選択します。 たとえば、組織内のファイルをスキャンし、特定のファイルの種類または名前について警告するデーモン アプリがあるとします。

    **[アクセス許可の選択]** で、 **[ファイル]** を展開し、*Files.Read.All* アクセス許可を選択します。
1. **[アクセス許可の追加]** を選択します.

Microsoft Graph の *Files.Read.All* アクセス許可など、一部のアクセス許可では、管理者の同意が必要です。 管理者の同意を付与するには、 **[管理者の同意の付与]** ボタンを選択します。これについては、後ほど「[管理者の同意のボタン](#admin-consent-button)」セクションで説明します。

### <a name="configure-client-credentials"></a>クライアントの資格情報を構成する

アプリケーションのアクセス許可を使用するアプリは、独自の資格情報を使用してそれ自体として認証を行い、ユーザーによる操作を必要としません。 アプリケーション (または API) がアプリケーションのアクセス許可を使用して、Microsoft Graph、独自の Web API、または別の API にアクセスできるようにするには、そのクライアント アプリの資格情報を構成する必要があります。

アプリの資格情報の構成の詳細については、「[資格情報の追加](quickstart-register-app.md#add-credentials)」のセクションが含まれる「[クイックスタート: Microsoft ID プラットフォームにアプリケーションを登録する](quickstart-register-app.md)」を参照してください。

## <a name="more-on-api-permissions-and-admin-consent"></a>API のアクセス許可と管理者の同意に関する詳細

アプリ登録の **[API のアクセス許可]** ウィンドウには、[[構成されたアクセス許可]](#configured-permissions) テーブルが含まれています。また、[[付与されたその他のアクセス許可]](#other-permissions-granted) のテーブルが含まれている場合もあります。 両方のテーブルと[管理者の同意のボタン](#admin-consent-button)について以下のセクションで説明します。

### <a name="configured-permissions"></a>構成されたアクセス許可

**[API のアクセス許可]** ウィンドウの **[構成されたアクセス許可]** テーブルには、基本操作のためにアプリケーションに必要なアクセス許可の一覧 ("*必須リソース アクセス*" (RRA) リスト) が表示されます。 ユーザーまたは管理者は、アプリを使用する前に、これらのアクセス許可に同意する必要があります。 その他のオプションのアクセス許可は、後から (動的な同意を使用して) 実行時に要求できます。

これは、ユーザーがアプリに関して同意する必要がある最小限のアクセス許可の一覧です。 他にもある可能性がありますが、これらは常に必要です。 セキュリティのため、およびユーザーと管理者がアプリをより快適に使用できるようにするため、必要のないことは要求しないでください。

このテーブルに表示されるアクセス許可を追加または削除するには、前述の手順または「[付与されたその他のアクセス許可](#other-permissions-granted)」(次のセクションで説明します) の手順を使用します。 管理者は、テーブルに表示される API のアクセス許可の完全なセットに対して管理者の同意を付与し、個々のアクセス許可の同意を取り消すことができます。

### <a name="other-permissions-granted"></a>付与されたその他のアクセス許可

**[API のアクセス許可]** ウィンドウに、 **[{お使いのテナント} に付与されたその他のアクセス許可]** というテーブルが表示される場合もあります。 **[{お使いのテナント} に付与されたその他のアクセス許可]** のテーブルには、アプリケーション オブジェクトに対して明示的に構成されていない、テナントに付与されたアクセス許可が示されます。 これらのアクセス許可は動的に要求され、同意されたものです。 このセクションは、該当するアクセス許可が少なくとも 1 つ存在する場合にのみ表示されます。

このテーブルに表示される API のアクセス許可の完全なセットまたは個々のアクセス許可を **[構成されたアクセス許可]** セクションに追加できます。 管理者は、このセクションの API または個々のアクセス許可に対する管理者の同意を取り消すこともできます。

### <a name="admin-consent-button"></a>管理者の同意のボタン

管理者は、 **[{お使いのテナント} に管理者の同意を与えます]** ボタンを使用して、アプリケーションに対して構成されたアクセス許可に管理者の同意を付与できます。 このボタンを選択すると、同意アクションの確認を求めるダイアログが表示されます。

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-03-grant-admin-consent-button.png" alt-text="Azure portal の [構成されたアクセス許可] ウィンドウで強調表示された、管理者の同意の付与ボタン":::

同意を付与すると、管理者の同意が必要なアクセス許可が、同意付与済みとして表示されます。

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-04-admin-consent-granted.png" alt-text="Files.Read.All のアクセス許可に対して管理者の同意が付与されていることを示す、Azure portal の [アクセス許可の構成] テーブル":::

**管理者の同意を付与** するボタンは、管理者でない場合、またはアプリケーションにアクセス許可が構成されていない場合は "*無効*" になります。 アクセス許可が付与されていてもまだ構成されていない場合、管理者の同意ボタンをクリックすると、これらのアクセス許可を処理するように求められます。 構成されたアクセス許可にそれらを追加するか、それらを削除することができます。

## <a name="next-steps"></a>次のステップ

このシリーズの次のクイックスタートに進んで、アプリケーションにアクセスできるアカウントの種類を構成する方法を確認してください。 たとえば、組織内のユーザーのみにアクセスを制限したり (シングルテナント)、他の Azure AD テナントのユーザーを許可したり (マルチテナント)、個人用 Microsoft アカウントを持つユーザーを許可したり (MSA) することができます。

> [!div class="nextstepaction"]
> [アプリケーションによってサポートされるアカウントを変更する](quickstart-modify-supported-accounts.md)
