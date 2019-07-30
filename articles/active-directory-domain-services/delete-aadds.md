---
title: Azure Active Directory Domain Services の無効化 | Microsoft Docs
description: Azure Portal を使用して Azure Active Directory Domain Services を無効にする
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: 2f9b7f5342753c27bd8159056c8f162a5a4b78d3
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473545"
---
# <a name="disable-azure-active-directory-domain-services-using-the-azure-portal"></a>Azure Portal を使用して Azure Active Directory Domain Services を無効にする
この記事では、Azure ポータルを使用して、Azure AD ディレクトリの Azure Active Directory Domain Services を無効にする方法を示します。

> [!WARNING]
> **削除は永続的であり、元に戻すことはできません。**
> 慎重に進めてください。 マネージド ドメインを削除する場合、次の点に注意してください。
>   * マネージド ドメインのドメイン コント ローラーはプロビジョニング解除され、仮想ネットワークから削除されます。
>   * マネージド ドメイン上のデータは完全に削除されます。 これには、マネージド ドメインで作成したカスタム OU、GPO、カスタムの DNS レコード、サービス プリンシパル、GMSA などが含まれます。
>   * マネージド ドメインに参加しているコンピューターでは、ドメインとの信頼関係が失われ、ドメインへの参加を解消する必要があります。
>   * 企業の AD 資格情報を使用してこれらのコンピューターにサインインすることはできません。 代わりに、該当のコンピューターのローカル管理者の資格情報を使用します。
> マネージド ドメインを削除しても、Azure AD ディレクトリが削除されたり、ディレクトリに悪影響を与えたりすることはありません。

Azure AD Domain Services のマネージド ドメインを削除するには、次の手順を実行します。
1. Azure Portal で、[Azure AD Domain Services 拡張機能](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices)に移動します。
2. マネージド ドメインの名前をクリックします。

    ![削除するドメインの選択](./media/getting-started/domain-services-delete-select-domain.png)

3. **[概要]** ページで、 **[削除]** ボタンをクリックします。

    ![ドメインの削除](./media/getting-started/domain-services-delete-domain.png)

4. 削除を確認するには、マネージド ドメインの DNS ドメイン名を入力します。 終了したら、 **[削除]** ボタンをクリックします。

    ![ドメインの削除の確認](./media/getting-started/domain-services-delete-domain-confirm.png)

マネージド ドメインは、約 15 ~ 20 分で削除されます。

どの機能によってユーザーが将来 Azure AD Domain Services を選択するかを理解するために、 [フィードバックの共有](contact-us.md) を検討してください。 このフィードバックは、デプロイのニーズとユースケースに合わせたサービスの拡張に活用させていただきます。
