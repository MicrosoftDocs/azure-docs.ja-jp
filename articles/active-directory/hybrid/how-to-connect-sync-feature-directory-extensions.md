---
title: 'Azure AD Connect 同期: ディレクトリ拡張機能 | Microsoft Docs'
description: このトピックでは、Azure AD Connect のディレクトリ拡張機能について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/12/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25d4152783129fa1c5950d6cf6287332bf90d32a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97976879"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect 同期: ディレクトリ拡張機能
ディレクトリ拡張機能を使用すると、オンプレミスの Active Directory から独自の属性を使用して、Azure Active Directory (Azure AD) のスキーマを拡張できます。 この機能により、オンプレミスで引き続き管理する属性を使用して LOB アプリを構築できます。 これらの属性は、[拡張機能](/graph/extensibility-overview
)から使用できます。 使用可能な属性を確認するには、[Microsoft Graph エクスプローラー](https://developer.microsoft.com/graph/graph-explorer)を使用します。 この機能を使用して、Azure AD に動的グループを作成することもできます。

現在のところ、これらの属性を使用する Microsoft 365 ワークロードはありません。

## <a name="customize-which-attributes-to-synchronize-with-azure-ad"></a>Azure AD と同期する属性をカスタマイズする

インストール ウィザードのカスタム設定パスで、どの追加属性を同期するかを構成します。

> [!NOTE]
> バージョン 1.2.65.0 以前の Azure AD Connect では、 **[使用可能な属性]** の検索ボックスで大文字と小文字が区別されます。

![スキーマ拡張機能のウィザード](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

インストールでは、次の属性が表示されます。これらは有効な候補です。

* ユーザーおよびグループ オブジェクト型
* 単一値の属性: 文字列、ブール値、整数、バイナリ
* 複数値の属性: 文字列、バイナリ


>[!NOTE]
> Azure AD Connect によって Active Directory の複数値属性が複数値属性拡張機能として Azure AD に同期された後、属性を SAML 要求に含めることができます。 しかし、API 呼び出しを通じてこのデータを使用することはできません。

属性の一覧は、Azure AD Connect のインストール中に作成されたスキーマ キャッシュから読み取られます。 その他の属性で Active Directory スキーマを拡張した場合、このような新しい属性が表示されるようにするには、[スキーマを更新する](how-to-connect-installation-wizard.md#refresh-directory-schema)必要があります。

Azure AD のオブジェクトでは、ディレクトリ拡張機能に対して最大 100 個の属性を持つことができます。 最大長は 250 文字です。 属性値がそれより長い場合は、同期エンジンによって切り捨てられます。

## <a name="configuration-changes-in-azure-ad-made-by-the-wizard"></a>ウィザードが Azure AD に対して行う構成の変更

これらの属性が利用できるアプリケーションは、Azure AD Connect のインストール中に登録されます。 このアプリケーションは、Azure Portal で確認できます。 その名前は常に **Tenant Schema Extension App** となります。

![スキーマ拡張機能アプリ](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

このアプリを表示するには、 **[すべてのアプリケーション]** を選択する必要があります。

属性には、**extension \_{ApplicationId}\_** というプレフィックスが付きます。 ApplicationId の値は、使用している Azure AD テナントに存在するすべての属性で同じになります。 この値は、このトピックで取り上げる他のすべてのシナリオで必要になります。

## <a name="viewing-attributes-using-the-microsoft-graph-api"></a>Microsoft Graph API を使用した属性の表示

[Microsoft Graph エクスプローラー](https://developer.microsoft.com/graph/graph-explorer#)を使用することで、Microsoft Graph API を利用してこれらの属性を使用できるようになりました。

>[!NOTE]
> Microsoft Graph API で、属性が返されるように要求する必要があります。 次のような属性を明示的に選択します: `https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division`。
>
> 詳細については、[Microsoft Graph: クエリ パラメーターの使用](/graph/query-parameters#select-parameter)に関するトピックをご覧ください。

>[!NOTE]
> AADConnect からの属性値を、AADConnect によって作成されていない拡張属性に同期させることはサポートされていません。 これを行うと、パフォーマンスの問題が発生し、予期しない結果が生じる可能性があります。 同期では、上記のように作成された拡張属性のみがサポートされます。

## <a name="use-the-attributes-in-dynamic-groups"></a>動的グループで属性を使用する

より有益なシナリオの 1 つは、動的セキュリティや Microsoft 365 グループでこれらの属性を使用することです。

1. Azure AD に新しいグループを作成します。 適切な名前を付け、 **[メンバーシップの種類]** が **[動的ユーザー]** になっていることを確認します。

   ![新しいグループを含むスクリーンショット](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup1.png)

2. **[動的クエリの追加]** を選択します。 プロパティを見ても、それらの拡張属性は表示されません。 それらは最初に自分で追加する必要があります。 **[カスタム拡張機能のプロパティを取得します]** をクリックし、アプリケーション ID を入力して、 **[プロパティの更新]** をクリックします。

   ![ディレクトリ拡張が追加された画面のスクリーンショット](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup2.png) 

3. プロパティのドロップダウンを開くと、今度は、追加した属性が表示されていることがわかります。

   ![UI に表示されるようになった新しい属性を含むスクリーンショット](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup3.png)

   実際の要件に合わせて式を完成させます。 この例では、ルールを **(user.extension_9d98ed114c4840d298fad781915f27e4_division -eq "Sales and marketing")** に設定しています。

4. グループの作成後、しばらく待つと、Azure AD によってメンバーが設定されます。それらのメンバーを確認してください。

   ![動的グループ内のメンバーを含むスクリーンショット](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup4.png)  

## <a name="next-steps"></a>次のステップ
[Azure AD Connect Sync](how-to-connect-sync-whatis.md) の構成に関するページをご覧ください。

「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。
