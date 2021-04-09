---
title: Azure AD Connect のアンインストール
description: このドキュメントでは、Azure AD Connect のアンインストール方法について説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5eb537a70c69745c8067ffb71cfb895a0875945
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96934137"
---
# <a name="uninstall-azure-ad-connect"></a>Azure AD Connect のアンインストール

このドキュメントでは、Azure AD Connect の正しいアンインストール方法について説明します。

## <a name="uninstall-azure-ad-connect-from-the-server"></a>サーバーから Azure AD Connect をアンインストールする
まず、Azure AD Connect を、それが実行されているサーバーから削除する必要があります。  次の手順に従います。

 1. Azure AD Connect が実行されているサーバーで、 **[コントロール パネル]** に移動します。
 2. **[プログラムのアンインストール]** をクリックします。
 ![[プログラムのアンインストール]](media/how-to-connect-uninstall/uninstall-1.png)</br>
 
 3. **[Azure AD Connect]** を選びます。
 ![Azure AD Connect を選択する](media/how-to-connect-uninstall/uninstall-2.png)</br>
 
 4. メッセージが表示されたら、 **[はい]** をクリックして確認します。
 5. この確認により、Azure AD Connect 画面が表示されます。  **[削除]** をクリックします。
 ![Remove](media/how-to-connect-uninstall/uninstall-3.png)</br>
 
 6. この操作が完了したら、 **[終了]** をクリックします。
 7. ![終了](media/how-to-connect-uninstall/uninstall-4.png)</br>
 
 8. **[コントロール パネル]** に戻り、 **[最新の情報に更新]** をクリックすると、すべての当該コンポーネントが削除されています。


## <a name="next-steps"></a>次のステップ

- 「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。
- [既存の ADSync データベースを使用して Azure AD Connect をインストールする](how-to-connect-install-existing-database.md)
- [SQL によって委任された管理者のアクセス許可を使用して Azure AD Connect をインストールする](how-to-connect-install-sql-delegation.md)

