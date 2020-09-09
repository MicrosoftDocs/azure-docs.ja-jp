---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 05/04/2020
ms.author: mimart
ms.openlocfilehash: de1b67265c5c3b3f7247b7f21506eed88abfd550
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900357"
---
1. Amazon アカウントの資格情報で [Amazon 開発者コンソール](https://developer.amazon.com/dashboard)にサインインします。
1. まだ行っていない場合は、 **[Sign Up (サインアップ)]** をクリックして、開発者登録手順に従い、ポリシーを受け入れます。
1. ダッシュボードで **[Login with Amazon]\(Amazon でログイン\)** を選択します。
1. **[Create a New Security Profile]\(新しいセキュリティ プロファイルの作成\)** を選択します。
1. **セキュリティ プロファイル名**、**セキュリティ プロファイルの説明**、**プライバシーに関する声明の同意の URL** (例: `https://www.contoso.com/privacy`) を入力します。プライバシーに関する声明の URL は、ユーザーにプライバシー情報が提供される管理対象のページです。 **[保存]** をクリックします。
1. **[Login with Amazon Configurations]\(Amazon 構成でログイン\)** セクションで、作成した**セキュリティ プロファイル名**を選択し、**管理**アイコンをクリックして、 **[Web 設定]** を選択します。
1. **[Web Settings (Web 設定)]** セクションで、 **[Client ID (クライアント ID)]** の値をコピーします。 **[Show Secret (シークレットの表示)]** を選択して、クライアント シークレットを取得しコピーします。 テナントで ID プロバイダーとして Amazon アカウントを構成するには、この両方が必要です。 **[Client Secret]** は、重要なセキュリティ資格情報です。
1. **[Web 設定]** セクションで **[編集]** を選択します。 **[許可されたオリジン]** と **[Allowed Return URLs]\(許可された戻り先 URL\)** に、(上記の) 該当する URL を入力します。 
1. **[保存]** をクリックします。
