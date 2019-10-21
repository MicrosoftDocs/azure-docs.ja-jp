---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 10/01/2019
ms.author: marsma
ms.openlocfilehash: 67a0832f868fecd47983aa8cddff9cdf139b3f2a
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2019
ms.locfileid: "71702183"
---
1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. 左側のメニューで **[Azure Active Directory]** (Azure AD B2C *ではない*) を選択します。 または、 **[すべてのサービス]** を選択してから、 **[Azure Active Directory]** を検索して選択します。
1. **[管理]** の **[アプリの登録 (レガシ)]** を選択します。
1. **[新しいアプリケーションの登録]** を選択します。
1. アプリケーションの名前を入力します。 たとえば、*managementapp1* と入力します。
1. **[アプリケーションの種類]** で、 **[Web アプリ / API]** を選択します。
1. **[サインオン URL]** に、有効な URL を入力します。 たとえば、「 `https://localhost` 」のように入力します。 エンドポイントを到達可能にする必要はありませんが、有効な URL にする必要があります。
1. **作成** を選択します。
1. **[登録済みのアプリケーション]** の概要ページに表示される **[アプリケーション ID]** を記録します。 この値は、後の手順で使用します。
