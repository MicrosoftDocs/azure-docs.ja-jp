---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 2f490a5b12484a91e963d068810b292d7761521a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95986564"
---
> [!NOTE]
> Azure DNS を使用すると、Azure App Service のカスタム DNS 名を構成できます。 詳細については、「[Azure DNS を使用して Azure サービス用のカスタム ドメイン設定を提供する](../articles/dns/dns-custom-domain.md#app-service-web-apps)」をご覧ください。
>
>

1. ドメイン プロバイダーの Web サイトにサインインします。

1. DNS レコードの管理ページを探します。 各ドメイン プロバイダーには独自の DNS レコード インターフェイスがあるので、プロバイダーのドキュメントを参照してください。 **[ドメイン名]** 、 **[DNS]** 、 **[ネーム サーバー管理]** というラベルが付いたサイトの領域を探します。

   多くの場合、DNS レコードのページを見つけるには、アカウント情報を表示し、 **[マイドメイン]** などのリンクを探します。 そのページに移動し、 **[ゾーン ファイル]** 、 **[DNS レコード]** 、 **[詳細構成]** のような名前のリンクを探します。

   以下のスクリーンショットは、DNS レコード ページの例です。

   ![DNS レコード ページの例を示すスクリーンショット。](./media/app-service-web-access-dns-records-no-h/example-record-ui.png)

1. このスクリーンショットの例では、レコードを作成するために **[追加]** を選択します。 プロバイダーによっては、追加するレコード タイプごとに異なるリンクが用意されています。 この場合も、プロバイダーのドキュメントを参照してください。

> [!NOTE]
> 一部のプロバイダー (GoDaddy など) では、別の **[変更を保存]** リンクを選択するまで DNS レコードの変更が反映されません。
