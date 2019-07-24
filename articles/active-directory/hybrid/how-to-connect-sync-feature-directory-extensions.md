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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/05/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff0fd4d01eab739b79685c1de67cb8fe28873961
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56167203"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect 同期: ディレクトリ拡張機能
ディレクトリ拡張機能を使用すると、オンプレミスの Active Directory から独自の属性を使用して、Azure Active Directory (Azure AD) のスキーマを拡張できます。 この機能により、オンプレミスで引き続き管理する属性を使用して LOB アプリを構築できます。 これらの属性は、[Azure AD Graph API ディレクトリ拡張機能](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)または [Microsoft Graph](https://developer.microsoft.com/graph/) を通じて利用できます。 使用可能な属性を表示するには、それぞれ [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/) と [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) を使用します。

現在のところ、これらの属性を使用する Office 365 ワークロードはありません。

インストール ウィザードのカスタム設定パスで、どの追加属性を同期するかを構成します。

>[!NOTE]
>[使用可能な属性] ボックスでは、大文字と小文字は区別されます。

![スキーマ拡張機能のウィザード](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

インストールでは、次の属性が表示されます。これらは有効な候補です。

* ユーザーおよびグループ オブジェクト型
* 単一値の属性:文字列、ブール値、整数、バイナリ
* 複数値の属性:文字列、バイナリ


>[!NOTE]
> Azure AD Connect では、複数値の Active Directory 属性から Azure AD への同期が、複数値のディレクトリ拡張としてサポートされますが、現在、複数値のディレクトリ拡張属性でアップロードされたデータを取得/使用する方法はありません。

属性の一覧は、Azure AD Connect のインストール中に作成されたスキーマ キャッシュから読み取られます。 その他の属性で Active Directory スキーマを拡張した場合、このような新しい属性が表示されるようにするには、[スキーマを更新する](how-to-connect-installation-wizard.md#refresh-directory-schema)必要があります。

Azure AD のオブジェクトでは、ディレクトリ拡張機能に対して最大 100 個の属性を持つことができます。 最大長は 250 文字です。 属性値がそれより長い場合は、同期エンジンによって切り捨てられます。

これらの属性が利用できるアプリケーションは、Azure AD Connect のインストール中に登録されます。 このアプリケーションは、Azure Portal で確認できます。

![スキーマ拡張機能アプリ](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

属性には、拡張機能 \_{AppClientId}\_ というプレフィックスが付きます。 AppClientId の値は、使用している Azure AD テナントに存在するすべての属性で同じになります。

これで、これらの属性を Azure AD Graph API を通じて利用できるようになりました。 これにクエリを実行するには、[Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/) を使用します。

![Azure AD Graph Explorer](./media/how-to-connect-sync-feature-directory-extensions/extension4.png)

または、[Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer#) を使用して、Microsoft Graph API で属性にクエリを実行します。

>[!NOTE]
> 属性が返されるように要求する必要があります。 このように、明白に属性を選びます： https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division. 
>
> 詳細については、[Microsoft Graph:クエリ パラメーターの使用](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter)に関するページを参照してください。

## <a name="next-steps"></a>次の手順
[Azure AD Connect Sync](how-to-connect-sync-whatis.md) の構成に関するページをご覧ください。

「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。
