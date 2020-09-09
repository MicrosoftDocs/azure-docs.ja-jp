---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 537c86512db4706077d0089a4f71ee945623c26b
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095496"
---
### <a name="configure-your-notification-hub-with-apns-information"></a>APNS 情報で通知ハブを構成する

**[Notification Services]** で **[Apple]** を選択し、先ほど「[Notification Hubs の証明書を作成する](#creating-a-certificate-for-notification-hubs)」セクションで選択したアプローチに応じて適切な手順に従います。  

> [!NOTE]
> **[アプリケーション モード]** の **[Production]\(運用\)** は、ストアからアプリを購入したユーザーにプッシュ通知を送信する場合にのみ使用します。

### <a name="option-1-using-a-p12-push-certificate"></a>オプション 1: .p12 プッシュ証明書を使用する

1. **[Certificate]** を選択します。

1. ファイル アイコンを選択します。

1. 先ほどエクスポートした .p12 ファイルを選択し、 **[Open]\(開く\)** を選択します。

1. 必要に応じて、適切なパスワードを指定します。

1. **[サンドボックス]** モードを選択します。

1. **[保存]** を選択します。

### <a name="option-2-using-token-based-authentication"></a>オプション 2: トークンベースの認証を使用する

1. **[トークン]** を選択します。
1. 先ほど取得した次の値を入力します。

    - **キー ID**
    - **バンドル ID**
    - **チーム ID**
    - **トークン**

1. **[サンドボックス]** を選択します。
1. **[保存]** を選択します。
