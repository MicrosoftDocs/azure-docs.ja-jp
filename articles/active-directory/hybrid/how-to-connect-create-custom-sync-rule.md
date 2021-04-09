---
title: Azure AD Connect で同期規則をカスタマイズする方法 | Microsoft Docs
description: 同期規則エディターを使用して、新しい同期規則の編集や作成を行う方法について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2bb86988454141dc692b4a9967997c4ff7574a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90530490"
---
# <a name="how-to-customize-a-synchronization-rule"></a>同期規則をカスタマイズする方法

## <a name="recommended-steps"></a>**推奨される手順**

同期規則エディターを使用して、新しい同期規則の編集や作成を行うことができます。 同期規則を変更するには、詳しい知識のあるユーザーである必要があります。 あらゆる間違った変更により、ターゲット ディレクトリからオブジェクトが削除される場合があります。 同期規則の専門知識を得るため、[推奨されるドキュメント](#recommended-documents)をお読みください。 同期規則を変更するには、次の手順を行います。

* 以下に示すように、デスクトップのアプリケーション メニューから同期エディターを起動します。

    ![同期規則エディター メニュー](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/syncruleeditormenu.png)

* 既定値の同期規則をカスタマイズするために、[同期規則エディター] の [編集] ボタンをクリックして、既存の規則を複製します。これにより、標準的な既定の規則のコピーが作成され、無効になります。 優先順位が 100 未満の、複製した規則を保存します。  優先順位によって、属性フローの競合が発生した場合に、競合の解決でどの規則が優先されるか (より小さい数値) が決まります。

    ![同期規則エディター](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/clonerule.png)

* 特定の属性を変更する場合、複製した規則でのみ変更する属性を保持することが理想的です。  その後、既定値の規則を有効にし、変更した属性が複製した規則のものであり、既定の標準規則から他の属性が選択されるようにします。 

* 変更した属性の計算値が複製した規則では NULL であり、既定値の標準規則では NULL ではない場合、NULL ではない値が優先され、NULL 値と置き換えられることに注意してください。 NULL 値を NULL ではない値に置き換えない場合は、複製した規則で AuthoritativeNull を割り当てます。

* **アウトバウンド** 規則を変更するには、同期規則エディターからフィルターを変更します。

## <a name="recommended-documents"></a>**推奨されるドキュメント**
* [Azure AD Connect 同期:技術的概念](./how-to-connect-sync-technical-concepts.md)
* [Azure AD Connect 同期:アーキテクチャの概要](./concept-azure-ad-connect-sync-architecture.md)
* [Azure AD Connect 同期:宣言型のプロビジョニングについて](./concept-azure-ad-connect-sync-declarative-provisioning.md)
* [Azure AD Connect 同期:宣言型のプロビジョニングの式について](./concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Azure AD Connect 同期:既定の構成について](./concept-azure-ad-connect-sync-default-configuration.md)
* [Azure AD Connect 同期:ユーザー、グループ、および連絡先について](./concept-azure-ad-connect-sync-user-and-contacts.md)
* [Azure AD Connect 同期:シャドウ属性](./how-to-connect-syncservice-shadow-attributes.md)

## <a name="next-steps"></a>次の手順
- [Azure AD Connect 同期](how-to-connect-sync-whatis.md)。
- [ハイブリッド ID とは](whatis-hybrid-identity.md)。