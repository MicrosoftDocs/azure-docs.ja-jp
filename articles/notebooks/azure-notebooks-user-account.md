---
title: Azure Notebooks へのサインイン
description: Microsoft アカウントまたは職場/学校アカウントを使用して、Azure Notebook のユーザー アカウント構成します。
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 0d657fcc-26bc-41dd-abf0-3e5cfd66e0e0
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: kraigb
ms.openlocfilehash: 311f4d612c9deab23e7537b0c53ff3932c312cd4
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608012"
---
# <a name="your-user-account-for-azure-notebooks"></a>Azure Notebooks のユーザー アカウント

Azure Notebooks は、ユーザー アカウントでサインインしてもしなくても使用できます。

- サインインししない場合は、Notebooks を実行できますが、プロジェクトの一部として Notebooks やデータ ファイルを保持することはできません。 たとえば、Azure Notebooks へのリンクを受信するユーザーは、サインインをしなくても Notebooks を利用できます。
- サインインすると、Azure Notebooks はそのアカウントで利用するすべてのプロジェクトを保持します。 サインイン ユーザーには、他のユーザーと自分のプロジェクトや Notebooks を共有できるユーザー ID も付与されます。
  - Azure Notebooks を使用するアカウントが Azure サブスクリプションにも関連付けられている場合、さらに強力なサーバー上で Notebooks を実行したり、プライベートの Notebooks を作成したり、個々のユーザーに Notebooks へのアクセス許可を付与したりできる追加のメリットがあります。

Azure Notebooks にサインインするには、Microsoft アカウントまたは「職場または学校」アカウントのいずれかが必要です。 Notebook ページの右上隅にある**サインイン** コマンドを選択すると、アカウントの入力が求められます。

![Azure Notebooks のサインイン コマンド](media/accounts/sign-in-command.png)

Azure Notebooks では、すべての作業は、サインインに使用したアカウントに関連付けられます。 各アカウントは、[ユーザー プロファイル](azure-notebooks-user-profile.md)で固有のユーザー ID を持つ必要もあります。 その結果、別セットのプロジェクトと別の ID を保持する必要がある場合に、別のアカウントを使用して Azure Notebooks にサインインできます。 たとえば、データ サイエンス チームの各メンバーは、個人用のアカウントと、社外の人と作業する場合に提示する共有グループ アカウントの両方を持つことができます。 同様に、インストラクターも、外部コンサルティングまたはオープン ソースの作業で使用されるアカウントとは異なる、教育の役割用のアカウントを保持することができます。

## <a name="microsoft-accounts"></a>Microsoft アカウント

Microsoft アカウントは、Windows、Azure、outlook.com、OneDrive、XBox Live などの、任意の数の Microsoft 製品とサービスへのサインインに使用されます。 これらのサービスを使用する場合は、Azure Notebook で使用できる Microsoft アカウントを事前に持っている可能性があります。

持っているかどうかが不明な場合は、アカウント プロンプトで **[作成]** を選択します。 任意のプロバイダーが提供する任意の電子メール アドレスを使用して、新しい Microsoft アカウントを作成できます。

![新しい Microsoft アカウントを作成するためのコマンド](media/accounts/create-new-microsoft-account.png)

> [!Note]
> 既にアカウントが関連付けられている電子メール アドレスで新しいアカウントを作成しようとすると、「職場または学校のメール アドレスではここにサインアップできません。Gmail や Yahoo! などの個人用メールを使用するか、新しい Outlook メールを取得してください」というメッセージが表示される場合があります。 Use a personal email, such as Gmail or Yahoo!, or get a new Outlook email." その場合は、新しいアカウントを作成せずに仕事用メール アドレスでサインインしてみてください。

お子様が使用しているアカウントは、Azure Notebooks へのアクセスは既定でブロックされます。 お子様が使用しているアカウントでサインインすると、次のエラーが表示されます。

![Error when attempting to sign in with a child account: something went wrong, your parent has blocked access (お子様のアカウントでサインインしようとしたエラー: 問題が発生しました。保護者がアクセスをブロックしています)](media/accounts/child-account-error.png)

アクセスを有効にするには、親は次の手順を実行する必要があります。

1. `https://account.live.com/mk` にアクセスし、親のアカウントでサインインします。
1. 該当するお子様のセクションで、**[このお子様によるサードパーティ製アプリへのアクセスを管理する]** を選択します。
1. 次のページで、**[アクセスの有効化]** を選択します。
1. 次回にお子様のアカウントを使用して Azure Notebooks にログインするときに、表示されるアクセス許可プロンプトで **[はい]** を選択します。

> [!Warning]
> Azure Notebooks のサード パーティ製アプリへのアクセスを有効にすると、その他のすべてのサード パーティ製アプリへのアクセスも有効になります。 親はアクセスを有効にする場合にはよい判断力を働かせ、お子様のアクティビティをさらに注意深く監視することをお勧めします。

## <a name="work-or-school-accounts"></a>職場または学校アカウント

職場または学校のアカウントは、組織の管理者によって作成され、組織のメンバーは Office 365 などの Microsoft クラウド サービスにアクセスしたり、ドメインに参加しているコンピューター上の Windows にサインインするためのアカウントとして使用したりできます。 職場または学校のアカウントは、通常は any-user@contoso.com などの組織の電子メール アドレスを使用します。

職場または学校のアカウントで Azure Notebooks にサインインするには、管理者の同意が必要となる場合があります。Azure Notebooks はアカウントの電子メール アドレスやユーザーのブラウザー情報などの情報を収集または使用する (ただし公開はしない) からです (ブラウザーのデータは、一般的な用途に応じて機能を最適化するために使用されます)。

組織アカウントの管理者は、ユーザーが個別に同意することが制限されている場合には、ユーザーに代わって同意する必要があります。 この場合、ユーザーには、「ユーザーは、このアプリケーションにアクセスできません」: 

![「ユーザーは、このアプリケーションにアクセスできません」というメッセージが職場または学校のアカウントを使用する場合に表示されます。](media/accounts/consent-permissions-denied.png)

管理者として同意するには、[管理者の同意ページ](https://notebooks.azure.com/account/adminConsent)を使用します。そのページにはプロセスについての説明があります。

## <a name="next-steps"></a>次の手順  

> [!div class="nextstepaction"]
> [プロファイルとユーザー ID の編集](azure-notebooks-user-profile.md)
