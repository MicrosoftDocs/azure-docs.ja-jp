---
title: Privileged Identity Management における Azure AD ロールの機能 | Microsoft Docs
description: Privileged Identity Management (PIM) での割り当てのために、Azure AD ロールを管理する方法
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 07/10/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4478c9c286c06d5d6c5593195a0e93abd286b8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92371513"
---
# <a name="management-capabilities-for-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management での Azure AD ロールの管理機能

Privileged Identity Management での Azure AD ロールの管理機能が更新され、Azure AD ロールと Azure リソース ロールの管理方法が統合されました。 以前は、Azure リソース ロール向けの Privileged Identity Management には Azure AD ロールでは利用できないいくつかの重要な機能がありました。

最近ロール アウトされた更新では、これらの 2 つを単一の管理エクスペリエンスに統合し、Azure AD ロールでも Azure リソース ロールと同じ機能を利用できるようになりました。 この記事では、更新された機能と要件を説明します。

## <a name="time-bound-assignments"></a>期限付き割り当て

以前、ロールの割り当てには、*有資格* と *永続* の 2 つの状態がありました。 現在では、それぞれの割り当ての種類に、開始時間と終了時間を設定できます。 この追加により、割り当てには次の 4 つの状態が設定できるようになりました。

- [Eligible permanently]\(永続的に有資格\)
- [Active permanently]\(永続的にアクティブ\)
- 有資格と、割り当てへの開始日と終了日の指定
- 永続と、割り当てへの開始日と終了日の指定

ユーザーに有資格の割り当てを付与して毎回ロールをアクティブにするのが望ましくないケースでは、多くの場合、割り当てに有効期限を設定することで Azure AD 組織を保護することができます。 たとえば、有資格である一時的なユーザーが何人かいる場合、有効期限を設定して、それらのユーザーの作業が終了したらロールの割り当てから自動的に削除するようにすることができます。

## <a name="new-role-settings"></a>新しいロール設定

Azure AD ロール向けの新しい設定も追加されました。

- **以前** は、アクティブ化はロールごとにのみ構成することができました。 つまり、多要素認証要件やインシデントまたは要求チケット要件などのアクティブ化設定は、特定のロールに対して有資格であるすべてのユーザーに適用されました。
- **今** では、ロールをアクティブ化する前に個々のユーザーが多要素認証を実行する必要があるかどうかを構成できるようになりました。 さらに、特定のロールに関連する Privileged Identity Management のメールを詳細に管理できるようになりました。

## <a name="extend-and-renew-assignments"></a>割り当ての延長と更新

期限付き割り当てについて知ると、期限が切れたらロールはどうなるのか、という疑問がわくと思います。 この新しいバージョンでは、このシナリオに対して 2 つの選択肢があります。

- **拡張**:ロールの割り当ての有効期限が近付くと、ユーザーは Privileged Identity Management を使用してそのロールの割り当ての延長を要求できます
- **更新**:ロールの割り当ての有効期限が切れた場合、ユーザーは Privileged Identity Management を使用してそのロールの割り当ての更新を要求できます

ユーザーが行うこれらの操作には、グローバル管理者または特権ロール管理者からの承認が必要です。 管理者は、これらの有効期限を管理する仕事から解放されます。 延長または更新の要求を待ち、その要求が有効であれば承認するだけでよいのです。

## <a name="api-changes"></a>API の変更

顧客が更新されたバージョンを自分の Azure AD 組織にロール アウトした場合、既存の Graph API は使用できなくなります。 [Azure リソース ロール向け Graph API](/graph/api/resources/privilegedidentitymanagement-resources?view=graph-rest-beta) の使用に移行する必要があります。 その API を使用して Azure AD ロールを管理するには、署名内の `/azureResources` を `/aadroles` と置き換え、`resourceId` にディレクトリ ID を使用します。

Microsoft は、以前の API を使用しているすべての顧客に連絡し、この変更について事前に伝えるためにできる限りのことをしてきました。 自分の Azure AD 組織が新しいバージョンに移行したのに、まだ古い API を使用している場合は、pim_preview@microsoft.com から Microsoft のチームに連絡してください。

## <a name="powershell-change"></a>PowerShell の変更

Azure AD ロール向けの Privileged Identity Management PowerShell モジュールを使用している顧客の場合、更新により、PowerShell は使用できなくなります。 以前のコマンドレットの代わりに、Azure AD プレビュー PowerShell モジュール内の Privileged Identity Management コマンドレットを使用する必要があります。 [PowerShell ギャラリー](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)から Azure AD PowerShell モジュールをインストールします。 これで、[この PowerShell モジュールの PIM 操作のドキュメントとサンプルを確認](powershell-for-azure-ad-roles.md)できます。

## <a name="next-steps"></a>次のステップ

- [Azure AD のカスタム ロールを割り当てる](azure-ad-custom-roles-assign.md)
- [Azure AD のカスタム ロールの割り当てを削除または更新する](azure-ad-custom-roles-update-remove.md)
- [Azure AD のカスタム ロールの割り当てを構成する](azure-ad-custom-roles-configure.md)
- [Azure AD のロールの定義](../roles/permissions-reference.md)