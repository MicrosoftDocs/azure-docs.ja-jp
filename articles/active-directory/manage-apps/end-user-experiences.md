---
title: アプリケーションのエンドユーザー エクスペリエンス - Azure Active Directory
description: Azure Active Directory (Azure AD) には、組織内のエンド ユーザーにアプリケーションをデプロイするためのカスタマイズ可能な方法が複数用意されています。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: kenwith
ms.reviewer: arvindh
ms.openlocfilehash: 0d3f1cab0637c7409cbc45f4f5a5374b2b5b6a77
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259255"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Azure Active Directory でのアプリケーションのエンド ユーザー エクスペリエンス

Azure Active Directory (Azure AD) には、組織内のエンド ユーザーにアプリケーションをデプロイするためのカスタマイズ可能な方法が複数用意されています。

* Azure AD のマイ アプリ
* Microsoft 365 アプリケーション起動プログラム
* フェデレーション アプリへの直接サインオン
* フェデレーション アプリ、パスワードベースのアプリ、または既存のアプリへのディープ リンク

管理者は、自分の判断で、組織内でのデプロイにどの方法を使用するかを決定することができます。

## <a name="azure-ad-my-apps"></a>Azure AD のマイ アプリ

マイ アプリ (https://myapps.microsoft.com ) は Web ベースのポータルで、Azure Active Directory の組織アカウントを持つエンド ユーザーが、Azure AD 管理者によってアクセスを許可されたアプリケーションを表示および起動することができます。 [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/) のエンド ユーザーの場合、マイ アプリを介してセルフサービスのグループ管理機能を利用することもできます。

既定では、すべてのアプリケーションが 1 つのページにまとめて表示されます。 しかし、コレクションを使用して関連するアプリケーションをグループ化し、別々のタブで表示すれば、アプリケーションが見つけやすくなります。 たとえば、コレクションを使用して、特定の担当業務、タスク、プロジェクトなどに関連したアプリケーションの論理グループを作成することができます。 詳細については、「[マイ アプリ ポータルでコレクションを作成する](access-panel-collections.md)」を参照してください。 

マイ アプリは Azure portal から独立しているため、ユーザーが Azure サブスクリプションや Microsoft 365 サブスクリプションを持っている必要はありません。

Azure AD のマイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="microsoft-365-application-launcher"></a>Microsoft 365 アプリケーション起動プログラム

Microsoft 365 をデプロイしている組織では、Azure AD を介してユーザーに割り当てられているアプリケーションは Office 365 ポータル ([https://portal.office.com/myapps](https://portal.office.com/myapps)) にも表示されます。 これにより、組織のユーザーは、2 つ目のポータルを使用することなく簡単かつ手軽にアプリを起動できます。これは、Microsoft 365 を使用する組織に対して推奨されるアプリ起動ソリューションです。

Office 365 アプリケーション起動プログラムの詳細については、 [Office 365 アプリ起動プログラムにアプリを表示する方法](/previous-versions/office/office-365-api/)に関するページを参照してください。

## <a name="direct-sign-on-to-federated-apps"></a>フェデレーション アプリへの直接サインオン

SAML 2.0、WS-Federation、または OpenID Connect をサポートするほとんどのフェデレーション アプリでは、ユーザーがアプリケーションから開始し、自動リダイレクトかサインイン用リンクのクリックによって Azure AD 経由でサインインする機能もサポートしています。 これはサービス プロバイダーによって開始されるサインオンと呼ばれ、Azure AD アプリケーション ギャラリーのほとんどのフェデレーション アプリケーションでこの機能がサポートされています (詳細については、Azure portal のアプリのシングル サインオンの構成ウィザードからリンクされているドキュメントを参照してください)。

## <a name="direct-sign-on-links"></a>直接サインオンのリンク

Azure AD では、パスワードベースのシングル サインオン、リンクされたシングル サインオン、任意の形式のフェデレーション シングル サインオンをサポートする個々のアプリケーションへの直接シングル サインオン リンクもサポートされます。

これらのリンクは、特定のアプリケーションについてユーザーを Azure AD サインイン プロセスに送るための特別に生成された URL です。ユーザーは、Azure AD のマイ アプリまたは Microsoft 365 からアプリケーションを起動する必要はありません。 これらの **ユーザー アクセス URL** は、使用できるエンタープライズ アプリケーションのプロパティの下にあります。 Azure portal で、 **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** を選択します。 アプリケーションを選択し、**[プロパティ]** を選択します。

![Twitter プロパティのユーザー アクセス URL の例](media/end-user-experiences/direct-sign-on-link.png)

これらのリンクをコピーして任意の場所に貼り付けることにより、選択したアプリケーションへのサインイン リンクを提供できます。 これらのリンクは、電子メールや、ユーザー アプリケーション アクセス用に設定した任意のカスタムの Web ベースのポータルに貼り付けることができます。 Twitter 用の Azure AD 直接シングル サインオン URL の例を次に示します。

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

組織に固有のマイ アプリの URL と同様、*myapps.microsoft.com* ドメインの後にディレクトリのアクティブ ドメインまたは検証済みのドメインの 1 つを追加して、この URL をカスタマイズすることもできます。 これにより、サインイン ページで組織のブランド設定が即座に読み込まれ、ユーザーが最初にユーザー ID を入力する必要がなくなります。

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

許可されたユーザーがこれらのアプリケーションに固有のリンクのいずれかをクリックすると、最初に組織のサインイン ページ (まだ署名されていない状態を想定) が表示され、サインイン後は最初にマイ アプリで停止することなくアプリにリダイレクトされます。 パスワードベースのシングル サインオン用ブラウザー拡張機能など、ユーザーがアプリケーションにアクセスするための前提条件を満たしていない場合、リンクをクリックすると、ユーザーは不足している拡張機能をインストールするよう要求されます。 アプリケーションのシングル サインオン構成が変更された場合でもリンク URL は変わりません。

これらのリンクにはマイ アプリや Microsoft 365 と同じアクセス制御メカニズムが使用され、Azure portal でアプリケーションに割り当てられているユーザーまたはグループのみが正常に認証されます。 ただし、許可されていないユーザーには、アクセスが与えられていないことを示すメッセージと、アクセスが許可されている使用可能なアプリケーションを表示するマイ アプリを呼び出すためのリンクが示されます。

## <a name="next-steps"></a>次のステップ

* [アプリケーション管理のクイックスタート シリーズ](view-applications-portal.md)
* [シングル サインオンとは](what-is-single-sign-on.md)
* [Azure Active Directory とアプリケーションの統合のファースト ステップ ガイド](plan-an-application-integration.md)