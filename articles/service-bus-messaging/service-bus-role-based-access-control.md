---
title: Azure Service Bus のロールベースのアクセス制御 (RBAC) (プレビュー) | Microsoft Docs
description: Azure Service Bus のロールベースのアクセス制御
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2018
ms.author: aschhab
ms.openlocfilehash: 6f5390162ce56a0e77ef41740d7e88f3546c5530
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444731"
---
# <a name="active-directory-role-based-access-control-preview"></a>Active Directory のロールベースのアクセス制御 (プレビュー)

Microsoft Azure では、Azure Active Directory (Azure AD) を利用して、リソースとアプリケーションの統合されたアクセス制御管理が提供されています。 Azure AD では、Azure ベースのアプリケーション専用にユーザー アカウントとアプリケーションを管理することも、既存の Active Directory インフラストラクチャと Azure AD を統合して、Azure リソースとAzure でホストされたアプリケーションも含む全社的なシングル サインオンを実現することもできます。 その場合、これらの Azure AD のユーザー ID とアプリケーション ID をグローバルなロールおよびサービス固有のロールに割り当てて、Azure リソースへのアクセス権を付与することができます。

Azure Service Bus の場合、名前空間およびそれに関連するすべてのリソースの Azure Portal および Azure リソース管理 API による管理は、*ロールベースのアクセス制御* (RBAC) モデルを使って既に保護されています。 ランタイム操作の RBAC は、現在パブリック プレビュー段階の機能です。

Azure AD の RBAC を使うアプリケーションは、SAS ルールとキーまたは Service Bus に固有のアクセス トークンを処理する必要はありません。 クライアント アプリは、Azure AD とやり取りして認証コンテキストを確立し、Service Bus 用のアクセス トークンを取得します。 対話型ログインを必要とするドメイン ユーザー アカウントでは、アプリケーションはどのような資格情報も直接処理しません。

## <a name="service-bus-roles-and-permissions"></a>Service Bus のロールとアクセス許可

Azure では、Service Bus 名前空間へのアクセスを承認するための次の組み込み RBAC ロールが提供されています。

* [Azure Service Bus データ所有者 (プレビュー)](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner-preview):Service Bus 名前空間とそのエンティティ (キュー、トピック、サブスクリプション、およびフィルター) へのデータ アクセスが可能です。

>[!IMPORTANT]
> 以前は、 **"所有者"** または **"共同作成者"** ロールへのマネージド ID の追加がサポートされていました。
>
> しかし、 **"所有者"** と **"共同作成者"** ロールのデータ アクセス特権は受け入れられなくなります。 **"所有者"** または **"共同作成者"** ロールを使用していた場合、それらは **"Azure Service Bus データ所有者 (プレビュー)"** ロールを利用するように変更する必要があります。

## <a name="use-service-bus-with-an-azure-ad-domain-user-account"></a>Azure AD ドメイン ユーザー アカウントで Service Bus を使用する

次のセクションでは、対話型の Azure AD ユーザーにサインインを求めるサンプル アプリケーションを作成して実行するために必要な手順、そのユーザー アカウントに Event Hubs のアクセス権を付与する方法、およびその ID を使って Service Bus にアクセスする方法について説明します。

この概要では、簡単なコンソール アプリケーションについて説明します。[そのコードは GitHub にあります](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)。

### <a name="create-an-active-directory-user-account"></a>Active Directory のユーザー アカウントを作成する

この最初のステップは省略可能です。 すべての Azure サブスクリプションは Azure Active Directory テナントと自動的に組み合わされ、Azure サブスクリプションへのアクセス権がある場合、ユーザー アカウントは既に登録されています。 つまり、何もしなくてもアカウントを使うことができます。

それでもこのシナリオ用に専用のアカウントを作成したい場合は、[以下の手順に従って](../automation/automation-create-aduser-account.md)ください。 Azure Active Directory テナントにアカウントを作成するアクセス許可が必要ですが、大規模なエンタープライズ シナリオでは必要ない場合があります。

### <a name="create-a-service-bus-namespace"></a>Service Bus 名前空間を作成する

次に、[Service Bus Messaging 名前空間を作成します](service-bus-create-namespace-portal.md)。

名前空間を作成した後、ポータルでその **[アクセス制御 (IAM)]** ページに移動し、 **[ロールの割り当ての追加]** をクリックして、Azure AD ユーザー アカウントを所有者ロールに追加します。 自分専用のユーザー アカウントを使い、名前空間を作成した場合は、既に所有者ロールになっています。 別のアカウントをロールに追加するには、 **[アクセス許可の追加]** パネルの **[選択]** フィールドで Web アプリケーションの名前を検索し、エントリをクリックします。 その後、 **[保存]** をクリックします。

これで、そのユーザー アカウントは、Service Bus 名前空間と以前に作成したキューにアクセスできるようになります。

### <a name="register-the-application"></a>アプリケーションを登録する

サンプル アプリケーションを実行できるようにするには、その前に、アプリケーションを Azure AD に登録し、アプリケーションが Azure Service Bus にアクセスすることを許可する同意プロンプトに同意します。

サンプル アプリケーションはコンソール アプリケーションであるため、ネイティブ アプリケーションを登録し、**Microsoft.ServiceBus** に対する API アクセス許可を "必要なアクセス許可" セットに追加する必要があります。 また、ネイティブ アプリケーションには識別子として機能する Azure AD の**リダイレクト URI** も必要です。この URI はネットワーク宛先である必要はありません。 この例では、サンプル コードが `https://servicebus.microsoft.com` を既に使っているため、この URI を使います。

登録の詳細な手順については、[こちらのチュートリアル](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)をご覧ください。 手順に従って**ネイティブ** アプリを登録した後、更新手順に従って **Microsoft.ServiceBus** API を必要なアクセス許可に追加します。 手順を実行している間に、**TenantId** と **ApplicationId** を書き留めておきます。これらの値はアプリケーションを実行するために必要です。

### <a name="run-the-app"></a>アプリの実行

サンプルを実行する前に、App.config ファイルを編集し、シナリオに応じて、次の値を設定します。

- `tenantId`:**TenantId** の値に設定します。
- `clientId`:**ApplicationId** の値に設定します。
- `clientSecret`:クライアント シークレットを使ってサインオンする場合は、Azure AD で作成します。 また、ネイティブ アプリの代わりに Web アプリまたは API を使います。 また、前に作成した名前空間の **[アクセス制御 (IAM)]** にアプリを追加します。
- `serviceBusNamespaceFQDN`:新しく作成した Service Bus 名前空間の完全な DNS 名に設定します (例: `example.servicebus.windows.net`)。
- `queueName`:作成したキューの名前に設定します。
- 前の手順においてアプリで指定したリダイレクト URI です。

コンソール アプリケーションを実行すると、シナリオの選択を求められます。 **[Interactive User Login]\(対話型のユーザー ログイン\)** をクリックし、その番号を入力して Enter キーを押します。 アプリケーションはログイン ウィンドウを表示し、Service Bus へのアクセスの同意を求めた後、サービスを使ってログイン ID を用いた送信/受信シナリオを実行します。

## <a name="next-steps"></a>次の手順

Service Bus メッセージングの詳細については、次のトピックをご覧ください。

* [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus のトピックとサブスクリプションの使用方法](service-bus-dotnet-how-to-use-topics-subscriptions.md)
