---
title: アプリケーションのエンド ユーザー エクスペリエンス - Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD) には、組織内のエンド ユーザーにアプリケーションをデプロイするためのカスタマイズ可能な方法が複数用意されています。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/03/2019
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74c6787068cf8ba1e86cbf43955d0ac995aa8de1
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702107"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Azure Active Directory でのアプリケーションのエンド ユーザー エクスペリエンス

Azure Active Directory (Azure AD) には、組織内のエンド ユーザーにアプリケーションをデプロイするためのカスタマイズ可能な方法が複数用意されています。

* Azure AD アクセス パネル
* Office 365 アプリケーション起動プログラム
* フェデレーション アプリへの直接サインオン
* フェデレーション アプリ、パスワードベースのアプリ、または既存のアプリへのディープ リンク

管理者は、自分の判断で、組織内でのデプロイにどの方法を使用するかを決定することができます。

## <a name="azure-ad-access-panel"></a>Azure AD アクセス パネル

アクセス パネル (https://myapps.microsoft.com ) は Web ベースのポータルで、Azure Active Directory の組織アカウントを持つエンド ユーザーが、Azure AD 管理者によってアクセスを許可されたクラウドベースのアプリケーションを表示および起動することができます。 [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/) のエンド ユーザーの場合、アクセス パネルを介してセルフサービスのグループ管理機能を利用することもできます。

![Azure AD アクセス パネル ポータルのスクリーンショット](media/what-is-single-sign-on/azure-ad-access-panel.png)

アクセス パネルは Azure Portal から独立しているため、ユーザーが Azure サブスクリプションまたは Office 365 サブスクリプションを持っている必要はありません。

Azure AD アクセス パネルの詳細については、「 [アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)」を参照してください。

## <a name="office-365-application-launcher"></a>Office 365 アプリケーション起動プログラム

Office 365 をデプロイしている組織では、Azure AD を介してユーザーに割り当てられているアプリケーションは Office 365 ポータル ([https://portal.office.com/myapps](https://portal.office.com/myapps)) にも表示されます。 これにより、組織のユーザーは、2 つ目のポータルを使用することなく簡単かつ手軽にアプリを起動できます。これは、Office 365 を使用する組織に対して推奨されるアプリ起動ソリューションです。

![Office 365 ポータルを示すスクリーンショット](./media/end-user-experiences/microsoft-365-portal-office-com.png)

Office 365 アプリケーション起動プログラムの詳細については、 [Office 365 アプリ起動プログラムにアプリを表示する方法](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher)に関するページを参照してください。

## <a name="direct-sign-on-to-federated-apps"></a>フェデレーション アプリへの直接サインオン

SAML 2.0、WS-Federation、または OpenID Connect をサポートするほとんどのフェデレーション アプリでは、ユーザーがアプリケーションから開始し、自動リダイレクトかサインイン用リンクのクリックによって Azure AD 経由でサインインする機能もサポートしています。 これはサービス プロバイダーによって開始されるサインオンと呼ばれ、Azure AD アプリケーション ギャラリーのほとんどのフェデレーション アプリケーションでこの機能がサポートされています (詳細については、Azure portal のアプリのシングル サインオンの構成ウィザードからリンクされているドキュメントを参照してください)。

![モバイル アプリ サインイン ページの例](./media/end-user-experiences/workdaymobile.png)

## <a name="direct-sign-on-links"></a>直接サインオンのリンク

Azure AD では、パスワードベースのシングル サインオン、リンクされたシングル サインオン、任意の形式のフェデレーション シングル サインオンをサポートする個々のアプリケーションへの直接シングル サインオン リンクもサポートされます。

これらのリンクは、特定のアプリケーションについてユーザーを Azure AD サインイン プロセスに送るための特別に生成された URL です。ユーザーは、Office 365 または Azure AD アクセス パネルからアプリケーションを起動する必要はありません。 これらの**ユーザー アクセス URL** は、使用できるエンタープライズ アプリケーションのプロパティの下にあります。 Azure portal で、 **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** を選択します。 アプリケーションを選択し、 **[プロパティ]** を選択します。

![Twitter プロパティのユーザー アクセス URL の例](media/end-user-experiences/direct-sign-on-link.png)

これらのリンクをコピーして任意の場所に貼り付けることにより、選択したアプリケーションへのサインイン リンクを提供できます。 これらのリンクは、電子メールや、ユーザー アプリケーション アクセス用に設定した任意のカスタムの Web ベースのポータルに貼り付けることができます。 Twitter 用の Azure AD 直接シングル サインオン URL の例を次に示します。

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

アクセス パネルの組織に固有の URL と同様、myapps.microsoft.com ドメインの後にディレクトリのアクティブ ドメインまたは検証済みのドメインの 1 つを追加して、この URL をカスタマイズすることもできます。 これにより、サインイン ページで組織のブランド設定が即座に読み込まれ、ユーザーが最初にユーザー ID を入力する必要がなくなります。

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

許可されたユーザーがこれらのアプリケーションに固有のリンクのいずれかをクリックすると、最初に組織のサインイン ページ (まだ署名されていない状態を想定) が表示され、サインイン後は最初にアクセス パネルで停止することなくアプリにリダイレクトされます。 パスワードベースのシングル サインオン用ブラウザー拡張機能など、ユーザーがアプリケーションにアクセスするための前提条件を満たしていない場合、リンクをクリックすると、ユーザーは不足している拡張機能をインストールするよう要求されます。 アプリケーションのシングル サインオン構成が変更された場合でもリンク URL は変わりません。

これらのリンクにはアクセス パネルや Office 365 と同じアクセス制御メカニズムが使用され、Azure portal でアプリケーションに割り当てられているユーザーまたはグループのみが正常に認証されます。 ただし、許可されていないユーザーには、アクセスが与えられていないことを示すメッセージと、アクセスが許可されている使用可能なアプリケーションを表示するアクセス パネルを呼び出すためのリンクが示されます。

## <a name="next-steps"></a>次の手順

「[Azure Active Directory のデプロイ計画](../fundamentals/active-directory-deployment-plans.md)」で、デプロイ計画を確認する
