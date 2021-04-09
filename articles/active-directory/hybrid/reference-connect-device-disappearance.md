---
title: Azure AD Connect 1.4.xx.x およびデバイスの消失について | Microsoft Docs
description: このドキュメントでは、バージョン 1.4. xx.x の Azure AD Connect で発生する問題について説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: bc159452c81a673ca4a7ed46aa7eff19fd9209eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "73176029"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>Azure AD Connect 1.4.xx.x およびデバイスの消失について
バージョン 1.4.xx.x の Azure AD Connect をご利用のお客様に、Widows デバイスの一部または全部が Azure AD から消えるという現象が発生することがあります。 これらのデバイス ID が、条件付きアクセスの承認時に Azure AD によって使用されることはないため、これについて心配する必要はありません。 Hybrid Azure AD Join 用に正しく Azure AD に登録された Windows デバイスがこの変更によって削除されることはありません。

Azure AD 内のデバイス オブジェクトの削除がエクスポート削除しきい値を超えていることが確認された場合は、お客様がこれらの削除の実行を許可することをお勧めします。 [方法: 削除のしきい値を超えた場合に削除の実行を許可する](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>バックグラウンド
Hybrid Azure AD 参加済みとして登録された Windows デバイスは、Azure AD 内でデバイス オブジェクトとして表されます。 それらのデバイス オブジェクトは、条件付きアクセスに使用できます。 Windows 10 デバイスは、Azure AD Connect を介してクラウドに同期され、ダウン レベルの Windows デバイスは AD FS またはシームレス シングル サインオンを使用して直接登録されます。

## <a name="windows-10-devices"></a>Windows 10 デバイス
Azure AD Connect を介してクラウドに同期されるのは、Hybrid Azure AD Join によって特定の userCertificate 属性値が構成されている Windows 10 デバイスだけです。 以前のバージョンの Azure AD Connect では、この要件が厳密に適用されておらず、結果的に、不要なデバイス オブジェクトが Azure AD に生じていました。 これらのデバイスは Azure AD に登録するように意図されていなかったため、Azure AD のそうしたデバイスは常に "保留中" の状態のままでした。 

このバージョンの Azure AD Connect では、Hybrid Azure AD 参加済みとなるように正しく構成された Windows 10 デバイスだけが同期されます。 Azure AD 参加固有の userCertificate のない Windows 10 デバイス オブジェクトは Azure AD から削除されます。

## <a name="down-level-windows-devices"></a>ダウンレベルの Windows デバイス
[ダウンレベルの Windows デバイス](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)は、Azure AD Connect によって同期されるべきではありません。 過去に誤って同期された Azure AD 内のデバイスは、Azure AD から削除されるようになりました。 Azure AD Connect が[ダウンレベルの Windows デバイス](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)の削除を試みる場合、そのデバイスは、[Windows 10 以外のコンピューター向けの Microsoft Workplace Join の MSI](https://www.microsoft.com/download/details.aspx?id=53554) によって作成されたものではなく、他の Azure AD 機能から利用することはできません。

一部のお客様については、Windows デバイスを正しく登録し、そうしたデバイスがデバイス ベースの条件付きアクセスに完全に参加できるように「[方法: Hybrid Azure Active Directory 参加の実装を計画する](../devices/hybrid-azuread-join-plan.md)」を再確認する必要がある場合があります。 

## <a name="how-can-i-verify-which-devices-are-deleted-with-this-update"></a>この更新プログラムで削除されるデバイスはどのように確認できますか?

削除されるデバイスを確認するには、次の PowerShell スクリプトを使用できます。https://gallery.technet.microsoft.com/scriptcenter/Export-Hybrid-Azure-AD-f8e51436

このスクリプトは、Active Directory コンピューター オブジェクトに格納されている証明書、具体的には、Hybrid Azure AD 参加機能によって発行された証明書に関するレポートを生成します。
これは、AD のコンピューター オブジェクトの UserCertificate プロパティに存在する証明書を確認し、存在している、有効期限が切れていない各証明書について、その証明書が Hybrid Azure AD 参加機能に対して発行された (つまり、サブジェクト名が CN = {ObjectGUID} と一致する) かどうかを検証します。
以前は、Azure AD Connect は、有効な証明書を少なくとも 1 つ含むコンピューターを Azure AD に同期していましたが、Azure AD Connect バージョン 1.4 からは、同期エンジンが Hybrid Azure AD 参加証明書を特定できるようになり、有効な Hybrid Azure AD 参加証明書ががない限り、‘cloudfilter’ によってコンピューター オブジェクトを Azure AD に同期しないようにします。
既に AD に同期されていても、有効な Hybrid Azure AD 参加証明書がない Azure AD デバイスは、同期エンジンによって削除されます (CloudFiltered = TRUE)。

## <a name="next-steps"></a>次の手順
- [Azure AD Connect のバージョン履歴](reference-connect-version-history.md)
