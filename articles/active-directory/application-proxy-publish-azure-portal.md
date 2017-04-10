---
title: "Azure AD アプリケーション プロキシを使用したアプリの発行 | Microsoft Docs"
description: "Azure Portal で Azure AD アプリケーション プロキシを使用して、オンプレミス アプリケーションをクラウドに発行します。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: harshja
ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 5acfbfbe7327fc28fa909e6ef7e0f9b6ce8b0e54
ms.openlocfilehash: e0be8f4617c2fdbe1cb6fe2f904bf9ea7056c55e

---


# <a name="publish-applications-using-azure-ad-application-proxy---public-preview"></a>Azure AD アプリケーション プロキシを使用したアプリケーションの発行 - パブリック プレビュー

> [!div class="op_single_selector"]
> * [Azure ポータル](application-proxy-publish-azure-portal.md)
> * [Azure クラシック ポータル](active-directory-application-proxy-publish.md)

Azure Active Directory (AD) アプリケーション プロキシを使用して、オンプレミス アプリケーションを発行してインターネット経由でアクセスできるようにすることで、リモート ワーカーをサポートできます。 Azure Portal を通して、ローカル ネットワークで実行中のアプリケーションの発行と、ネットワークの外部からセキュリティで保護されたリモート アクセスの提供を実行できます。

この記事では、アプリケーション プロキシを使用してオンプレミス アプリを発行する手順を説明します。 この記事の手順を最後まで実行すると、アプリケーションのシングル サインオン、個人用に設定した情報、またはセキュリティ要件を構成する準備が整います。

アプリケーション プロキシを初めて使用する場合は、「[オンプレミス アプリケーションへの安全なリモート アクセスを実現する方法](active-directory-application-proxy-get-started.md)」で、この機能の詳細を確認してください。


## <a name="publish-an-on-premises-app-for-remote-access"></a>リモート アクセスを行うためのオンプレミス アプリの発行


> [!TIP]
> アプリケーション プロキシを初めて使用する場合は、既にパスワード ベースの認証が設定されているアプリケーションを選択してください。 アプリケーション プロキシは、これ以外の種類の認証もサポートしていますが、短時間で操作を完了するには、パスワード ベースのアプリが最も適しています。 

1. [Azure Portal](https://portal.azure.com/) に管理者としてサインインします。
2. **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]**  >  **[追加]**を選択します。

  ![エンタープライズ アプリケーションの追加](./media/application-proxy-publish-azure-portal/add-app.png)

3. [カテゴリ] ページで、**[On-premises application (オンプレミスのアプリケーション)]** を選びます。  

  ![独自のアプリケーションの追加](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. アプリケーションについて以下の情報を入力します。

   - **名前**: アクセス パネルに表示されるアプリケーションの名前。 

   - **内部 URL**: プライベート ネットワーク内からアプリケーションにアクセスするために、アプリケーション プロキシ コネクタで使用されるアドレス。 バックエンド サーバー上の特定のパスを指定して発行できます。この場合、サーバーのそれ以外のパスは発行されません。 この方法では、同じサーバー上の複数のサイトを別々のアプリとして発行し、それぞれに独自の名前とアクセス規則を付与することができます。

     > [!TIP]
     > パスを発行する場合は、アプリケーションに必要な画像、スクリプト、スタイル シートが、すべてそのパスに含まれていることを確認してください。 たとえば、アプリケーションが https://yourapp/app にあり、https://yourapp/media にある画像を使用する場合は、パスとして https://yourapp/ を発行する必要があります。

   - **外部 URL**: ネットワークの外部からアプリにアクセスするためにユーザーが移動するアドレス。
   - **事前認証**: ユーザーにアプリケーションへのアクセス権を付与する前にアプリケーション プロキシがユーザーを認証する方法。 

     - Azure Active Directory: アプリケーション プロキシによってユーザーが Azure AD のサインイン ページにリダイレクトされます。これにより、ディレクトリとアプリケーションに対するユーザーのアクセス許可が認証されます。 このオプションは既定値のままにすることをお勧めします。
     - パススルー: アプリケーションにアクセスするための Azure Active Directory に対するユーザーの認証は必要ありません。 ただし、バックエンドで認証要件を設定できます。
   - **ヘッダーの URL を変換しますか?**: URL をヘッダーに変換するか、元のままにするかを選択します。 
   - **コネクタ グループ**: コネクタは、アプリケーションへのリモート アクセスを処理します。コネクタ グループを使用して、コネクタとアプリをリージョン、ネットワーク、または目的別に整理できます。 コネクタ グループが作成されていない場合、アプリは **[既定]** グループに割り当てられ、[コネクタ グループの作成](active-directory-application-proxy-connectors-azure-portal.md)を求める警告メッセージが表示されます。

   ![アプリケーションの作成](./media/application-proxy-publish-azure-portal/configure-app.png)

8. **[追加]**を選択します。


## <a name="add-a-test-user"></a>テスト ユーザーの選択 

アプリが正常に発行されたことをテストするために、アクセス権のあるユーザー アカウントを追加します。 

1. [クイック スタート] ブレードに戻り、**[Assign a user for testing (テスト用のユーザーの割り当て)]** を選択します。

  ![テスト用のユーザーの割り当て](./media/application-proxy-publish-azure-portal/assign-user.png)

2. [ユーザーとグループ] ブレードで、**[追加]** を選択します。

  ![ユーザーまたはグループの選択](./media/application-proxy-publish-azure-portal/add-user.png)

3. [割り当ての追加] ブレードで、**[ユーザーとグループ]** を選択し、追加するアカウントを選択します。 
4. **[割り当て]** を選択します。

## <a name="test-your-published-app"></a>発行したアプリのテスト

ブラウザーで、発行手順中に構成した外部 URL に移動します。 スタート画面が表示され、設定したテスト アカウントでサインインできるはずです。

![発行したアプリのテスト](./media/application-proxy-publish-azure-portal/test-app.png)


## <a name="next-steps"></a>次のステップ
- [コネクタのダウンロード](active-directory-application-proxy-enable.md)と[コネクタ グループの作成](active-directory-application-proxy-connectors-azure-portal.md)を行って、アプリケーションを別のネットワークや場所に発行します。

- 新しく発行したアプリに対する[シングル サインオンを設定](application-proxy-sso-azure-portal.md)します。



<!--HONumber=Feb17_HO2-->


