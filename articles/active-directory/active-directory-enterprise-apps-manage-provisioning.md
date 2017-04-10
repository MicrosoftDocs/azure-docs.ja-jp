---
title: "Azure Active Directory プレビューでのエンタープライズ アプリケーションのユーザー プロビジョニング管理 | Microsoft Docs"
description: "Azure Active Directory プレビューを使用してエンタープライズ アプリケーションのユーザー アカウント プロビジョニングを管理する方法について説明します"
services: active-directory
documentationcenter: 
author: asmalser
manager: femila
editor: 
ms.assetid: 34ac4028-a5aa-40d9-a93b-0db4e0abd793
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/12/2016
ms.author: asmalser
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 103eade46452d979705e06dd77441f42d7a514b8


---
# <a name="preview-managing-user-account-provisioning-for-enterprise-apps-in-the-new-azure-portal"></a>プレビュー: 新しい Azure Portal でエンタープライズ アプリケーションのユーザー アカウント プロビジョニングを管理する
この記事では、[Azure Portal](https://portal.azure.com) を使用して、自動ユーザー アカウント プロビジョニングとプロビジョニング解除をサポートしているアプリケーション (特に [Azure Active Directory アプリケーション ギャラリー](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery)の "おすすめ" カテゴリから追加されたアプリケーション) の自動ユーザー アカウント プロビジョニングとプロビジョニング解除を管理する方法について説明します。 新しい Azure Portal では、この管理エクスペリエンスは現在パブリック プレビュー中です。この記事では、新機能と、プレビュー期間中に適用されるいくつかの一時的な制限について説明します。 [プレビューの機能については、こちらの記事をご覧ください。](active-directory-preview-explainer.md)

自動ユーザー アカウント プロビジョニングの詳細とそのしくみについては、「 [Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](active-directory-saas-app-provisioning.md)」を参照してください。

## <a name="finding-your-apps-in-the-new-portal"></a>新しいポータルでアプリを検索する
2016 年 9 月の時点で、新しい Azure Portal では、ディレクトリ管理者が [Azure クラシック ポータル](https://manage.windowsazure.com)内の [Azure Active Directory アプリケーション ギャラリー](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery)を使用してディレクトリでのシングル サインオンを構成したすべてのアプリケーションを表示および管理できます。

これらのアプリケーションは、新しい Azure Portal の **[エンタープライズ アプリケーション]** セクション (左側のナビゲーション領域にある **[More Services (その他のサービス)]** メニューからアクセスできます) で見つけることができます。 エンタープライズ アプリケーションとは、組織のユーザーが使用するデプロイ済みのアプリケーションです。

![Enterprise Applications blade][0]

左側にある **[すべてのアプリケーション]** リンクを選択すると、ギャラリーから追加されたアプリを含め、構成済みのアプリの一覧が表示されます。 アプリを選択すると、そのアプリのリソース ブレードが読み込まれます。リソース ブレードでは、そのアプリのレポートを表示することや、さまざまな設定を管理することができます。

左側にある **[プロビジョニング]** を選択すると、ユーザー アカウント プロビジョニングの設定を管理できます。

![Application resource blade][1]

## <a name="provisioning-modes"></a>プロビジョニング モード
**[プロビジョニング]** ブレードの先頭には**[モード]** メニューがあり、エンタープライズ アプリケーションでサポートされているプロビジョニング モードが表示され、プロビジョニング モードを構成できます。 利用可能なオプションは、次のとおりです。

* **[自動]** - Azure AD でこのアプリケーションに対する API ベースの自動ユーザー アカウント プロビジョニングやプロビジョニング解除がサポートされている場合、このオプションが表示されます。 このモードを選択すると、表示されるインターフェイスに従って、アプリケーションのユーザー管理 API に接続するように Azure AD を構成し、Azure AD とアプリの間でのユーザー アカウント データのフロー方法を定義するアカウント マッピングとワークフローを作成して、Azure AD プロビジョニング サービスを管理できます。
* **[手動]** - Azure AD でこのアプリケーションに対するユーザー アカウントの自動プロビジョニングがサポートされていない場合、このオプションが表示されます。 このオプションは、アプリケーションが提供するユーザーの管理とプロビジョニング機能 (SAML のジャストインタイム プロビジョニングなど) に基づき、外部プロセスを使用して、そのアプリケーションに格納されているユーザー アカウント レコードを管理する必要があることを意味します。

## <a name="configuring-automatic-user-account-provisioning"></a>自動ユーザー アカウント プロビジョニングを構成する
**[自動]** オプションを選択すると、4 つのセクションに分かれた画面が表示されます。

### <a name="admin-credentials"></a>[Admin Credentials (管理者の資格情報)]
ここでは、Azure AD をアプリケーションのユーザー管理 API に接続するために必要な資格情報を入力します。 必要な入力は、アプリケーションによって異なります。 資格情報の種類と特定のアプリケーションの要件の詳細については、 [その特定のアプリケーションの構成に関するチュートリアル](active-directory-saas-app-provisioning.md#list-of-apps-that-support-automated-user-provisioning)を参照してください。

**[接続テスト]** をクリックすると、資格情報をテストできます (Azure AD が、指定された資格情報を使用してアプリのプロビジョニング アプリへの接続を試みます)。

### <a name="mappings"></a>マッピング
ここでは、ユーザー アカウントをプロビジョニングまたは更新する場合に、Azure AD とターゲット アプリケーションの間でフローするユーザー属性を確認および編集できます。

Azure AD ユーザー オブジェクトと各 SaaS アプリのユーザー オブジェクトの間には、構成済みの一連のマッピングが存在します。 アプリによっては、グループや連絡先といった他のタイプのオブジェクトを管理するものもあります。 テーブル内のこれらのマッピングのいずれかを選択すると、右側にマッピング エディターが表示され、そこでマッピングを確認およびカスタマイズできます。

![Application resource blade][2]

最初のプレビュー期間中にサポートされているカスタマイズは次のとおりです。

* Azure AD ユーザー オブジェクトと SaaS アプリのユーザー オブジェクトなど、特定のオブジェクトのマッピングを有効および無効にする。
* Azure AD ユーザー オブジェクトからアプリのユーザー オブジェクトにフローする属性を編集する。 属性マッピングの詳細については、「 [属性マッピングの種類について](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-types)」を参照してください。
* Azure AD がターゲット アプリケーションに対して実行するプロビジョニング操作をフィルター処理する。これは、Azure Portal の新機能です。 Azure AD でオブジェクトを完全に同期するのではなく、実行される操作を制限することができます。 たとえば、**[更新]** のみを選択すると、Azure AD はアプリケーションの既存のユーザー アカウントの更新のみを行い、新しいユーザー アカウントは作成しません。 **[作成]** のみを選択すると、Azure は新しいユーザー アカウントの作成のみを行い、既存のユーザー アカウントは更新しません。 この機能により、アカウントの作成ワークフローと更新ワークフローで異なるマッピングを作成できます。 アプリごとに複数のマッピングを作成する完全な機能の提供は、プレビュー期間の後半に予定しています。

### <a name="settings"></a>Settings
このセクションでは、選択したアプリケーションに対して Azure AD プロビジョニング サービスを開始および停止できるだけでなく、必要に応じてプロビジョニング キャッシュのクリアやサービスの再起動を行うこともできます。

アプリケーションに対して初めてプロビジョニングを有効にする場合は、**[プロビジョニング状態]** を **[オン]** に変更して、サービスを有効にします。 そうすると、Azure AD プロビジョニング サービスが初期同期を実行します。初期同期では、**[ユーザーとグループ]** セクションで割り当てられたユーザーが読み取られ、そのユーザーのターゲット アプリケーションが照会され、Azure AD の **[マッピング]** セクションで定義されているプロビジョニング操作が実行されます。 このプロセス中に、プロビジョニング サービスは管理対象のユーザー アカウントに関するキャッシュ データを格納します。そのため、割り当てのスコープに存在しない、ターゲット アプリケーション内の管理対象外のアカウントはプロビジョニング解除操作の影響を受けません。 初期同期の後、プロビジョニング サービスは 10 分間隔で自動的にユーザーとグループ オブジェクトを同期します。

**[プロビジョニング状態]** を **[オフ]** に変更すると、単純にプロビジョニング サービスが一時停止します。 この状態では、アプリのユーザーやグループ オブジェクトの作成、更新、削除が行われることはありません。 状態をオンに戻すと、サービスは中断したところから再開します。

**[Clear current state and restart synchronization (現在の状態をクリアして、同期を再実行する)]** チェック ボックスをオンにし、保存すると、プロビジョニング サービスが停止され、Azure AD で管理しているアカウントに関するキャッシュ データが破棄されて、サービスが再実行され、初期同期がもう一度実行されます。 このオプションを使用すると、プロビジョニング デプロイ プロセスを繰り返し開始できます。

### <a name="synchronization-details"></a>[Synchronization Details (同期の詳細)]
このセクションには、アプリケーションに対してプロビジョニング サービスを最初に実行した時間と最後に実行した時間や、管理しているユーザーとグループ オブジェクトの数など、プロビジョニング サービスの操作の詳細が表示されます。

**プロビジョニング アクティビティ レポート** (Azure AD とターゲット アプリケーションの間で作成、更新、削除されたすべてのユーザーとグループのログが表示されます) および**プロビジョニング エラー レポート** (読み取り、作成、更新、削除に失敗したユーザーとグループ オブジェクトの詳細なエラー メッセージが表示されます) へのリンクがあります。 

[0]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-blade.PNG
[1]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning.PNG
[2]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning-mapping.PNG



<!--HONumber=Dec16_HO5-->


