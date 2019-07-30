---
title: Teams からの Azure AD アプリケーション プロキシ アプリへのアクセス | Microsoft Docs
description: Azure AD アプリケーション プロキシを使用して、Microsoft Teams からオンプレミス アプリケーションにアクセスします。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/05/2017
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 037e005993a54e525560571a6d893197af99b6a0
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807774"
---
# <a name="access-your-on-premises-applications-through-microsoft-teams"></a>Microsoft Teams からのオンプレミス アプリケーションへのアクセス

Azure Active Directory アプリケーション プロキシを使用すると、どこからでもシングル サインオンでオンプレミス アプリケーションにアクセスできます。 また、Microsoft Teams を使用すると共同作業が効率化します。 この 2 つを統合することで、あらゆる状況下でチームの生産性を向上させることができます。

ユーザーは[タブを使用](https://support.office.com/article/Video-Using-Tabs-7350a03e-017a-4a00-a6ae-1c9fe8c497b3?ui=en-US&rs=en-US&ad=US)して、Teams のチャネルにクラウド アプリを追加することができますが、使用する SharePoint サイトや計画ツールがオンプレミスでホストされている場合は、 アプリケーション プロキシを使用します。 アプリケーション プロキシを通じて公開したアプリは、そのアプリにリモートでアクセスするときと同じ外部 URL を使用して、チャネルに追加することができます。 また、アプリケーション プロキシは Azure Active Directory を通じて認証するので、ユーザーはシングル サインオンを使用できます。

## <a name="install-the-application-proxy-connector-and-publish-your-app"></a>アプリケーション プロキシ コネクタのインストールとアプリの公開

まだ実行していない場合は、[テナントのアプリケーション プロキシを構成し、コネクタをインストール](application-proxy-add-on-premises-application.md)します。 次に、リモート アクセス用に[オンプレミス アプリケーションを公開](application-proxy-add-on-premises-application.md)します。 アプリケーションを公開するときに、外部 URL を書き留めておきます。これは、Teams にアプリを追加するときに必要になります。

既にアプリケーションを公開していて、その外部 URL がわからない場合は、[Azure Portal](https://portal.azure.com) で確認できます。 ポータルにサインインしたら、 **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** に移動し、アプリケーションを選び、 **[アプリケーション プロキシ]** を選択します。

## <a name="add-your-app-to-teams"></a>Teams へのアプリの追加

アプリケーション プロキシを通じてアプリを公開したら、そのアプリを直接 Teams チャネルにタブとして追加できること、およびチームの全員がアクセスできるようになることをユーザーに通知します。 ユーザーに次の手順を実行してもらいます。

1. アプリを追加する Teams チャネルに移動し、 **+** を選択してタブを追加します。

   ![[+] を選択し、Teams にタブを追加します。](./media/application-proxy-integrate-with-teams/add-tab.png)

1. タブのオプションから **[Web サイト]** を選択します。

   ![タブの追加画面から Web サイトを選択します。](./media/application-proxy-integrate-with-teams/website.png)

1. タブに名前を付けたら、URL としてアプリケーション プロキシの外部 URL を設定します。

   ![タブの名前を指定し、外部 URL を追加します。](./media/application-proxy-integrate-with-teams/tab-name-url.png)

チームのいずれかのメンバーがタブを追加すると、そのタブはチャネル内の全ユーザーに表示されます。 アプリへのアクセス権を持つすべてのユーザーが、Microsoft Teams の資格情報を使ったシングル サインオンでアクセスできます。 アプリへのアクセス権を持たないユーザーの場合、Teams にタブは表示されますが、オンプレミスのアプリケーションと Azure Portal の公開バージョンへのアクセス権が付与されるまで、アクセスはブロックされます。

## <a name="next-steps"></a>次の手順

- アプリケーション プロキシで[オンプレミスの SharePoint サイトを公開する](application-proxy-integrate-with-sharepoint-server.md)方法を学びます。
- 外部 URL に[カスタム ドメイン](application-proxy-configure-custom-domain.md)を使用するようアプリを構成します。
