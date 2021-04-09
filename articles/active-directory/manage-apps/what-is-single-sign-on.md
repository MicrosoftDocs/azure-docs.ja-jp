---
title: Azure シングル サインオン (SSO) とは
description: シングル サインオン (SSO) が Azure Active Directory でどのように機能するかについて説明します。 SSO を使用すると、ユーザーはすべてのアプリケーションのパスワードを記憶する必要がなくなります。 また、SSO を使用すると、アカウント管理の方法が簡略化されます。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: overview
ms.date: 12/03/2019
ms.author: kenwith
ms.reviewer: arvindh, japere
ms.custom: contperf-fy21q1
ms.openlocfilehash: 36025a5a68e4c3d0de1d6b11a13df656c72745d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99260199"
---
# <a name="what-is-single-sign-on-sso"></a>シングル サインオン (SSO) とは

シングル サインオンを使用すると、使用するアプリケーションにいちいちサインインする必要がなくなります。 ユーザーが一度ログインすると、その資格情報は他のアプリにも使用されます。

エンド ユーザーの場合、SSO の詳細を気にする必要はあまりありません。 パスワードを入力しなくても、生産性を大いに高めるアプリを使用できます。 アプリは次の場所にあります: https://myapps.microsoft.com 。
 
管理者や IT プロフェッショナルの方は、SSO の詳細および Azure における SSO の実装の詳細についてお読みください。

## <a name="single-sign-on-basics"></a>シングル サインオンの基本
シングル サインオンによって、ユーザーがサインインしてアプリケーションを使用する方法が大きく進歩します。 シングル サインオン ベースの認証システムは、"先進認証" と呼ばれることがよくあります。 先進認証とシングル サインオンは、IAM (Identity and Access Management: ID とアクセス管理) と呼ばれるコンピューティング カテゴリに分類されます。 シングル サインオンが可能になるしくみを理解するには、こちらのビデオをご覧ください。

認証の基礎: 基礎 | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/fbSVgC8nGz4]

## <a name="single-sign-on-with-web-applications"></a>Web アプリケーションでのシングル サインオン
Web アプリケーションは想像以上に広く使われています。 さまざまな企業が Web アプリをホストし、サービスとして提供しています。 Web アプリの代表的な例としては Microsoft 365 や GitHub、Salesforce が挙げられますが、他にも膨大な数の Web アプリが存在します。 Web アプリには、コンピューター上の Web ブラウザーからアクセスします。 シングル サインオンを使用すると、何度もサインインしなくてもさまざまな Web アプリを行き来することができます。

Web アプリにおけるシングル サインオンの動作については、次の 2 つのビデオをご覧ください。

認証の基礎: Web アプリケーション | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/tCNcG1lcCHY]

認証の基礎: Web シングル サインオン | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/51B-jSOBF8U]

## <a name="cloud-versus-on-premises-hosted-apps"></a>クラウドでホストされるアプリとオンプレミスでホストされるアプリ
シングル サインオンの実装方法は、アプリがホストされている場所によって異なります。 ホスティングは、アプリケーションにアクセスするためにネットワーク トラフィックをルーティングする方法に関係するので重要です。 ローカル ネットワークでホストされているアプリ (これはオンプレミスのアプリと呼ばれます) にアクセスする場合、ユーザーはアプリを使用するためにインターネットにアクセスする必要はありません。 別の場所でホストされているアプリ (これはクラウドでホストされるアプリと呼ばれます) の場合、ユーザーはアプリを使用するためにインターネットにアクセスする必要があります。

> [!TIP]
> クラウドでホストされるアプリは、SaaS (サービスとしてのソフトウェア) アプリとも呼ばれます。 

クラウドでホストされるアプリのシングル サインオンは簡単です。 アプリにそれが使用されていることを ID プロバイダーに伝えます。 そのうえで、ID プロバイダーを信頼するようにアプリを構成します。 アプリの ID プロバイダーとして Azure AD を使用する方法については、[アプリケーション管理に関するクイックスタート シリーズ](add-application-portal.md)を参照してください。

> [!TIP]
> クラウドとインターネットという用語は、多くの場合置き換えることができます。 その理由は、ネットワーク図と関係があります。 すべてのコンポーネントを描画するのは現実的ではないため、図で大規模なコンピューター ネットワークを表すときは雲の形を使うのが一般的です。 インターネットは最もよく知られているネットワークであるため、これらの用語は容易に置き換えて使うことができます。 ただし、どのコンピューター ネットワークでもクラウドと呼ぶことができます。

シングル サインオンは、オンプレミス ベースのアプリにも使用できます。 オンプレミスで SSO を実現する技術は、アプリケーション プロキシと呼ばれます。 詳細については、「[シングル サインオンのオプション](sso-options.md)」を参照してください。

## <a name="multiple-identity-providers"></a>複数の ID プロバイダー
複数の ID プロバイダー間で機能するようにシングル サインオンをセットアップすることをフェデレーションといいます。 フェデレーションのしくみについては、こちらのビデオをご覧ください。

認証の基礎: フェデレーション | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/CjarTgjKcX8]


## <a name="next-steps"></a>次のステップ
* [アプリケーション管理のクイックスタート シリーズ](view-applications-portal.md)
* [シングル サインオンのオプション](sso-options.md)
