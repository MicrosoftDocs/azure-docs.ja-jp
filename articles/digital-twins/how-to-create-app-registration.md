---
title: アプリの登録を作成する
titleSuffix: Azure Digital Twins
description: Azure AD アプリの登録を、クライアント アプリ向けの認証オプションとして作成する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 10/13/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: acb5457f10c54a741a738dd8a1008e703b0f23b0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051023"
---
# <a name="create-an-app-registration-to-use-with-azure-digital-twins"></a>Azure Digital Twins で使用するアプリ登録を作成する

Azure Digital Twins インスタンスを使用する場合、カスタム クライアント アプリや [Azure Digital Twins Explorer](quickstart-adt-explorer.md) のようなサンプルなどのクライアント アプリケーションを使用してそのインスタンスを操作することが一般的です。 これらのアプリケーションを操作するためには、Azure Digital Twins で認証する必要があります。また、アプリで使用できる [認証メカニズム](how-to-authenticate-client.md)には、[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) **アプリの登録** が含まれます。

これは、すべての認証シナリオで必要とされるわけではありません。 ただし、**クライアント ID** や **テナント ID** など、アプリの登録が必要な認証方法またはコード サンプルを使用している場合は、この記事で設定方法を確認できます。

## <a name="using-azure-ad-app-registrations"></a>Azure AD アプリの登録の使用

[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) は、Microsoft のクラウドベースの ID およびアクセス管理サービスです。 Azure AD で **アプリの登録** を設定することは、クライアント アプリに Azure Digital Twins へのアクセスを許可する方法の 1 つです。

このアプリの登録では、[Azure Digital Twins API](how-to-use-apis-sdks.md) に対するアクセス許可を構成します。 後で、クライアント アプリは登録の **クライアントおよびテナント ID 値** を使用してこのアプリ登録に対して認証できます。結果として API への構成済みのアクセス許可が付与されます。

>[!TIP]
> 新しいアプリ登録を必要となるたびに設定することもできます。*または*、1 回だけ行い、1 つのアプリ登録を確立してそれを必要とするすべてのシナリオで共有することもできます。

## <a name="create-the-registration"></a>登録を作成する

まず、Azure portal 上で [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) に移動します (このリンクを使用するか、ポータルの検索バーを使って検索できます)。 サービス メニューから *[アプリの登録]* 、 *[+ 新しい登録]* の順に選択します。

:::image type="content" source="media/how-to-create-app-registration/new-registration.png" alt-text="[アプリの登録] メニュー オプションと [+ 新しい登録] ボタンが強調表示されている Azure portal 上の Azure AD サービス ページのビュー":::

以下の *[アプリケーションの登録]* ページで、要求される次の値を入力します。
* **Name**:登録に関連付ける Azure AD アプリケーションの表示名
* **サポートされているアカウントの種類**: *[この組織ディレクトリのみに含まれるアカウント (既定のディレクトリのみ - シングル テナント)]* を選択します
* **リダイレクト URI**:Azure AD アプリケーションの "*Azure AD アプリケーション応答 URL*"。 `http://localhost` の *パブリック クライアント/ネイティブ (モバイルとデスクトップ)* URI を追加します。

完了したら、 *[登録]* ボタンを押します。

:::image type="content" source="media/how-to-create-app-registration/register-an-application.png" alt-text="説明に示された値が入力されている [アプリケーションの登録] ページのビュー":::

登録の設定が完了したら、ポータルによって詳細ページにリダイレクトされます。

## <a name="collect-client-id-and-tenant-id"></a>クライアント ID とテナント ID を収集する

次に、アプリの登録に関する重要な値のいくつかを、詳細ページから収集します。

:::image type="content" source="media/how-to-create-app-registration/app-important-values.png" alt-text="アプリ登録の重要な値のポータル ビュー":::

**実際の** ページに表示される、"_**アプリケーション (クライアント) ID**_" と "_**ディレクトリ (テナント) ID**_" をメモしておきます。 これらは、クライアント アプリがこの登録を使用して Azure Digital Twins で認証するために必要な値です。

## <a name="provide-azure-digital-twins-api-permission"></a>Azure Digital Twins API のアクセス許可を指定する

次に、Azure Digital Twins API へのベースライン アクセス許可によって、作成したアプリ登録を構成します。

アプリ登録のポータル ページで、メニューから *[API のアクセス許可]* を選択します。 以下のアクセス許可ページで、 *[+ アクセス許可の追加]* ボタンを押します。

:::image type="content" source="media/how-to-create-app-registration/add-permission.png" alt-text="[API のアクセス許可] メニュー オプションと [+ アクセス許可の追加] ボタンが強調表示されている Azure portal 上のアプリの登録のビュー":::

以下の *[API アクセス許可の要求]* ページで、 *[所属する組織で使用している API]* タブに切り替えて、"*azure digital twins*" を探します。 検索結果から "_**Azure Digital Twins**_" を選択して、Azure Digital Twins API に対するアクセス許可の割り当てを進めます。

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1.png" alt-text="アプリケーション (クライアント) ID が 0b07f429-9f4b-4714-9392-cc5e8e80c8b0 である Azure Digital Twins を示す [API アクセス許可の要求] ページの検索結果のビュー。":::

>[!NOTE]
> サービスの以前の (2020 年 7 月より前の) パブリック プレビューで作成された既存の Azure Digital Twins インスタンスがお使いのサブスクリプションにまだある場合は、代わりに "_**Azure Smart Spaces Service**_" を検索して選択する必要があります。 これは、同じ API セットの古い名前です ("*アプリケーション (クライアント) ID*" が上記のスクリーンショットと同じであることに注意してください)。この手順の他に、操作手順に変更はありません。
> :::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1-smart-spaces.png" alt-text="Azure Smart Spaces Service が表示された [API アクセス許可の要求] ページの検索結果のビュー":::

次に、これらの API に対して付与するアクセス許可を選択します。 **[Read (1)]\(読み取り (1)\)** アクセス許可を展開して、 *[Read.Write]\(読み取り.書き込み\)* と示されたチェック ボックスをオンにし、このアプリ登録に読み取りおよび書き込みのアクセス許可を付与します。

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-2.png" alt-text="Azure Digital Twins API の [Read.Write]\(読み取り.書き込み\) アクセス許可を選択した [API アクセス許可の要求] ページのビュー":::

完了したら、 *[アクセス許可の追加]* を押します。

### <a name="verify-success"></a>成功を確認する

*[API のアクセス許可]* ページで、読み取りおよび書き込みのアクセス許可が反映された Azure Digital Twins のエントリがあることを確認します。

:::image type="content" source="media/how-to-create-app-registration/verify-api-permissions.png" alt-text="Azure Digital Twins に対する [Read/Write Access]\(読み取り/書き込みアクセス\) が表示された、Azure AD アプリ登録の API のアクセス許可のポータル ビュー":::

また、アプリ登録の *manifest.json* 内で Azure Digital Twins への接続を検証できます。これは、API のアクセス許可を追加したときに、Azure Digital Twins 情報によって自動的に更新されました。

これを行うには、メニューから *[マニフェスト]* を選択して、アプリ登録のマニフェスト コードを表示します。 コード ウィンドウの一番下までスクロールし、`requiredResourceAccess` の下のこれらのフィールドを探します。 値は、次のスクリーンショットの値と一致している必要があります。

:::image type="content" source="media/how-to-create-app-registration/verify-manifest.png" alt-text="Azure AD アプリ登録のためのマニフェストのポータル ビュー。&quot;requiredResourceAccess&quot; 下で入れ子になっている &quot;resourceAppId&quot; 値 の 0b07f429-9f4b-4714-9392-cc5e8e80c8b0、および &quot;resourceAccess > id&quot; 値の 4589bd03-58cb-4e6c-b17f-b580e39652f8":::

これらの値がない場合は、[API アクセス許可の追加に関するセクション](#provide-azure-digital-twins-api-permission)の手順を再試行してください。

## <a name="other-possible-steps-for-your-organization"></a>組織でのその他の考えられる手順

アプリ登録を正常に設定するには、サブスクリプションの所有者または管理者からの追加のアクションが組織で必要になる可能性があります。 必要な手順は、組織の具体的な設定によって異なることがあります。

サブスクリプションの所有者または管理者による実行が必要になる可能性がある一般的な潜在的なアクティビティのいくつかを次に示します。 これらの操作は、Azure portal の [*Azure AD アプリ登録*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)のページから実行できます。
* アプリ登録に対する管理者の同意を付与する。 組織では、サブスクリプション内のすべてのアプリ登録について、Azure AD で *[管理者の同意が必要]* がグローバルに有効になっている可能性があります。 その場合は、有効にするアプリ登録について、アプリ登録の *[API のアクセス許可]* ページで所有者/管理者がユーザーの会社に対してこのボタンを選択する必要があります。

    :::image type="content" source="media/how-to-create-app-registration/grant-admin-consent.png" alt-text=" [API のアクセス許可] にある [管理者の同意の付与] ボタンのポータル ビュー":::
  - 同意が正常に付与された場合は、Azure Digital Twins のエントリに _[ **(ユーザーの会社)** に付与されました]_ の *[状態]* 値が表示されます。
   
    :::image type="content" source="media/how-to-create-app-registration/granted-admin-consent-done.png" alt-text=" [API のアクセス許可] で会社に付与された管理者の同意のポータル ビュー":::
* パブリック クライアント アクセスをアクティブ化する
* Web およびデスクトップへのアクセスに特定の応答 URL を設定する
* 暗黙の OAuth2 認証フローを許可する

アプリ登録とそのさまざまな設定オプションの詳細については、「[*Microsoft ID プラットフォームにアプリケーションを登録する*](/graph/auth-register-app-v2)」を参照してください。

## <a name="next-steps"></a>次の手順

この記事では、クライアント アプリケーションを Azure Digital Twins API で認証するために使用できる Azure AD アプリの登録を設定しました。

次に、認証メカニズムについて、アプリの登録を使用するものと、使用しないものについて確認します。
* [*方法: アプリ認証コードを作成する*](how-to-authenticate-client.md)