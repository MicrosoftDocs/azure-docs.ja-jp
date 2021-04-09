---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: f74d4cbc17e49345534a37e9e6612a36e19be295
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "73903704"
---
ただし、Azure テナントの外部で大規模にイメージを共有する場合は、共有を容易にするためにアプリ登録を作成することをお勧めします。  アプリ登録を使用すると、次のようにより複雑な共有シナリオが可能になります。 

* ある会社が別の会社を買収し、Azure インフラストラクチャは別々のテナントに分散しているときの共有イメージの管理。 
* Azure パートナーは、お客様の代理で Azure インフラストラクチャを管理します。 イメージのカスタマイズはパートナーのテナント内で行われますが、インフラストラクチャのデプロイはお客様のテナント内で行われます。 


## <a name="create-the-app-registration"></a>アプリの登録を作成する

イメージ ギャラリーのリソースを共有するために、両方のテナントが使用するアプリケーション登録を作成します。
1. [Azure portal で [アプリの登録 (プレビュー)]](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/) を開きます。    
1. ページ上部のメニューから **[New registration]\(新しい登録\)** を選択します。
1. **[名前]** に「*myGalleryApp*」と入力します。
1. **[サポートされているアカウントの種類]** で、 **[任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント]** を選択します。
1. **[リダイレクト URI]** に「 *https://www.microsoft.com* 」と入力し、 **[登録]** を選択します。 アプリの登録が作成されたら、概要ページが開きます。
1. 概要ページの **[Application (client) ID]\(アプリケーション (クライアント) ID\)** をコピーし、後で使用するために保存します。   
1. **[Certificates & secrets]\(証明書とシークレット\)** を選択してから、 **[New client secret]\(新しいクライアント シークレット\)** を選択します。
1. **[説明]** に「*Shared image gallery cross-tenant app secret*」と入力します。
1. **[有効期限]** を **[1 年]** の既定値のままにして **[追加]** を選択します。
1. シークレットの値をコピーし、それを安全な場所に保存します。 ページを閉じると、取得できなくなります。


共有イメージ ギャラリーを使用するためのアクセス許可をアプリの登録に付与します。
1. Azure portal で、他のテナントと共有する共有イメージ ギャラリーを選択します。
1. **[アクセス制御 (IAM)]** を選択したら、 **[ロール割り当ての追加]** で *[追加]* を選択します。 
1. **[ロール]** で **[閲覧者]** を選択します。
1. **[アクセスの割り当て先]** で **[Azure AD のユーザー、グループ、サービス プリンシパル]** のままにします。
1. **[選択]** に「*myGalleryApp*」と入力し、一覧に表示されたら選択します。 完了したら、 **[保存]** を選択します。


## <a name="give-tenant-2-access"></a>テナント 2 にアクセス権を付与する

ブラウザーを使用してサインインを要求することで、テナント 2 にアプリケーションへのアクセス権を付与します。 *\<Tenant2 ID>* を、イメージ ギャラリーを共有するテナントのテナント ID に置き換えます。 *\<Application (client) ID>* を、作成したアプリの登録のアプリケーション ID に置き換えます。 置き換えが完了したら、URL をブラウザーに貼り付け、サインイン プロンプトに従ってテナント 2 にサインインします。

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

[Azure portal](https://portal.azure.com) でテナント 2 としてサインインし、アプリの登録に、VM を作成するリソース グループへのアクセス権を付与します。

1. リソース グループを選択し、 **[アクセス制御 (IAM)]** を選択します。 **[ロールの割り当ての追加]** で **[追加]** を選択します。 
1. **[ロール]** に「**共同作成者**」と入力します。
1. **[アクセスの割り当て先]** で **[Azure AD のユーザー、グループ、サービス プリンシパル]** のままにします。
1. **[選択]** に「*myGalleryApp*」と入力し、一覧に表示されたら選択します。 完了したら、 **[保存]** を選択します。

> [!NOTE]
> 同じマネージド イメージを使用して別のイメージ バージョンを作成する前に、そのイメージ バージョンが構築とレプリケーションを完全に完了するまで待つ必要があります。

