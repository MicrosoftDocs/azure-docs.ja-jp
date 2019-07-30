---
title: 無効化された Azure Access Control Service (ACS) 名前空間を再度有効化する方法
description: Azure Access Control Service (ACS) 名前空間を見つけて有効化し、2019 年 2 月 4 日まで有効化状態の延長を要請する方法について説明します。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: ryanwi
ms.reviewer: jlu
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1cb17109d53c6e08c8607959207e367086032f6d
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482703"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>方法:無効化された Access Control Service 名前空間を再アクティブ化する

2017 年 11 月に、Azure Active Directory (Azure AD) のサービスである Microsoft Azure Access Control (ACS) が 2018 年 11 月 7 日で終了となると告知しました。

その後、ACS サブスクリプションの管理メールに ACS サービス終了に関する電子メールを送信しています。2018 年 11 月 7 日のサービス終了まで残り 12 か月、9 か月、6 か月、3 か月、1 か月、2 週間、1 週間、1 日のタイミングで送信されます。

2018 年 10 月 3 日に、2018 年 11 月 7 日までの移行を完了できないお客様に延長サービスを告知しました (電子メールと[ブログ投稿](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)で)。 この告知には、延長の要請方法も含まれていました。

## <a name="why-your-namespace-is-disabled"></a>名前空間が無効となっている理由

延長を選択していない場合、2018 年 11 月 7 日より ACS 名前空間の無効化を開始します。 お客様は既に 2019 年 2 月 4 日までの延長を要請していることでしょうが、そうでない場合は、PowerShell を介して名前空間を有効にすることができなくなります。

> [!NOTE]
> サブスクリプションのサービス管理者または共同管理者でなければ、PowerShell コマンドを実行したり、延長を要求したりできません。

## <a name="find-and-enable-your-acs-namespaces"></a>自分の ACS 名前空間を見つけ、有効にする

ACS PowerShell を使用して自分の ACS 名前空間をすべて一覧表示し、無効になっている名前空間を再度有効にできます。

1. ACS PowerShell をダウンロードしてインストールする:
    1. PowerShell ギャラリーに移動し、[Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2) をダウンロードします。
    1. モジュールをインストールする:

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. 使用できるすべてのコマンドの一覧を取得する:

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        特定のコマンドのヘルプを表示するには、次を実行します。

        ```powershell
        Get-Help [Command-Name] -Full
        ```
    
        `[Command-Name]` は、ACS コマンドの名前です。
1. **Connect-AcsAccount** コマンドレットを使用して ACS に接続します。 

    コマンドを実行する前に、**Set-ExecutionPolicy** を実行して実行ポリシーを変更することが必要な場合があります。
1. **Get-AcsSubscription** コマンドレットを使用して、使用可能な Azure サブスクリプションを一覧表示します。
1. **Get-AcsNamespace** コマンドレットを使用して、ACS 名前空間を一覧表示します。
1. `State` が `Disabled` であることを確認することで名前空間が無効になっていることを確認します。

    [![名前空間が無効になっていることを確認する](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    `nslookup {your-namespace}.accesscontrol.windows.net` を使用し、ドメインが依然として有効であるかどうかを確認することもできます。

1. **Enable-AcsNamespace** コマンドレットを使用し、ACS 名前空間を有効にします。

    名前空間を有効にしたら、2019 年 2 月 4 日の前にもう一度名前空間が無効にならないように延長を要請できます。 この日付を過ぎると、ACS に対するすべての要求が失敗します。

## <a name="request-an-extension"></a>延長を要請する

Microsoft では、2019 年 1 月 21 日より、新しい延長要請を受け付けています。

2019 年 2 月 4 日までの延長を要請したお客様については、名前空間の無効化が開始されます。 お客様は引き続き PowerShell を使用して名前空間を再度有効にすることができますが、名前空間は 48 時間後に再び無効になります。

2019 年 3 月 4 日以降、お客様は PowerShell を介して名前空間を再び有効にすることができなくなります。

これ以上の延長は自動的に承認されなくなります。 移行するのにさらに時間が必要な場合は、[Azure サポート](https://portal.azure.com/#create/Microsoft.Support)まで詳細な移行タイムラインをお知らせください。

### <a name="to-request-an-extension"></a>延長を要請するには

1. Azure portal にサインインし、[新しいサポート リクエスト](https://portal.azure.com/#create/Microsoft.Support)を作成します。
1. 次の例に示すように、新しいサポート リクエスト フォームに入力します。

    | サポート リクエストのフィールド | 値 |
    |-----------------------|--------------------|
    | **問題の種類** | `Technical` |
    | **サブスクリプション** | 対象のサブスクリプションに設定します |
    | **サービス** | `All services` |
    | **リソース** | `General question/Resource not available` |
    | **問題の種類** | `ACS to SAS Migration` |
    | **[件名]** | 問題を説明します |

   ![新しいテクニカル サポート リクエストの例](./media/howto-reactivate-disabled-acs-namespaces/new-technical-support-request.png)

<!--

1. Navigate to your ACS namespace's management portal by going to `https://{your-namespace}.accesscontrol.windows.net`.
1. Select the **Read Terms** button to read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/), which will direct you to a page with the updated Terms of Use.

    [![Select the Read Terms button](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png#lightbox)

1. Select **Request Extension** on the banner at the top of the page. The button will only be enabled after you read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/).

    [![Select the Request Extension button](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png#lightbox)

1. After the extension request is registered, the page will refresh with a new banner at the top of the page.

    [![Updated page with refreshed banner](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png#lightbox)
-->

## <a name="help-and-support"></a>ヘルプとサポート

- この方法を試して問題が発生した場合、[Azure サポート](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)までお問い合わせください。
- ACS のサービス終了についてご質問やご意見がございましたら、Microsoft までお問い合わせください (acsfeedback@microsoft.com)。

## <a name="next-steps"></a>次の手順

- 「[方法: Azure Access Control Service からの移行](active-directory-acs-migration.md)」を参照して、ACS の提供終了に関する情報を確認します。
