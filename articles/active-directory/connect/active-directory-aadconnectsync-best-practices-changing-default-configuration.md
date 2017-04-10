---
title: "Azure AD Connect Sync: 既定の構成の変更 | Microsoft Docs"
description: "Azure AD Connect Sync の既定の構成を変更するためのベスト プラクティスを紹介します。"
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 7638a031-1635-4942-94c3-fce8f09eed5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 08ec09275bbcaffaf25cf45a0f2f466fdeb60dc5
ms.openlocfilehash: 954923e699ef2160b0a16cb369f8a86b3beacd39


---
# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Azure AD Connect Sync: 既定の構成を変更するためのベスト プラクティス
このトピックの目的は、Azure AD Connect Sync に対する、サポートされている変更とサポートされていない変更について説明することです。

Azure AD Connect で作成された構成は、オンプレミスの Active Directory を Azure AD と同期するほとんどの環境において "そのままで" 動作します。 ただし、場合によっては、特定のニーズや要件を満たすために構成にいくつか変更を適用する必要があります。

## <a name="changes-to-the-service-account"></a>サービス アカウントに対する変更
Azure AD Connect Sync は、インストール ウィザードによって作成されたサービス アカウントで実行されます。 このサービス アカウントは、同期で使用されるデータベースの暗号化キーを保持します。 アカウントの作成には 127 文字の長いパスワードが使用され、そのパスワードは無期限に設定されています。

* サービス アカウントのパスワードの変更またはリセットは、 **サポートされていません** 。 サービス アカウントのパスワードを変更またはリセットすると、暗号化キーが破棄され、サービスはデータベースにアクセスすることも、開始することもできません。

## <a name="changes-to-the-scheduler"></a>スケジューラに対する変更
ビルド 1.1 (2016 年 2 月) のリリース以降では、既定の 30 分の同期サイクルとは異なる同期サイクルで [スケジューラ](active-directory-aadconnectsync-feature-scheduler.md) を構成できます。

## <a name="changes-to-synchronization-rules"></a>同期規則に対する変更
インストール ウィザードには、最も一般的なシナリオに対応できる構成が用意されています。 構成を変更する必要がある場合、サポートされる構成を維持するには、次の規則に従う必要があります。

* 既定の "直接" 属性フローが自分の所属している組織に適さない場合は、 [属性フローを変更](active-directory-aadconnectsync-change-the-configuration.md#other-common-attribute-flow-changes) できます。
* [属性をフローしない](active-directory-aadconnectsync-change-the-configuration.md#do-not-flow-an-attribute) ように設定したうえで、Azure AD に既にある属性値を削除する必要がある場合は、このシナリオ用に規則を作成する必要があります。
* [不要な同期規則は削除するのではなく無効](#disable-an-unwanted-sync-rule) にしてください。 削除した規則は、アップグレード時に再作成されます。
* [標準の規則を変更](#change-an-out-of-box-rule)するには、元の規則のコピーを作成し、標準の規則を無効にする必要があります。 同期規則エディターが役に立ちます。
* 同期規則エディターを使用して、カスタムの同期規則をエクスポートします。 このエディターには、障害復旧シナリオにおいて同期規則を簡単に再作成をするために使用できる PowerShell スクリプトが用意されています。

> [!WARNING]
> 標準の同期規則には拇印があります。 これらの規則に変更を加えた場合、拇印が一致しなくなります。 その後、Azure AD Connect の新しいリリースを適用しようとすると、問題が発生する可能性があります。 変更する場合は、この記事の方法に従ってください。

### <a name="disable-an-unwanted-sync-rule"></a>不要な同期規則は削除するのではなく無効
標準の同期規則を削除しないでください。 これは、次回アップグレード時に再作成されます。

場合によっては、インストール ウィザードで生成された構成がトポロジで動作していないことがあります。 たとえば、アカウント リソース フォレスト トポロジがあり、アカウント フォレストのスキーマを Exchange スキーマで拡張している場合、Exchange の規則は、アカウント フォレスト用とリソース フォレスト用の両方が作成されます。 この場合、Exchange 用の同期規則を無効にする必要があります。

![無効な同期規則](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

上の図では、インストール ウィザードによって、アカウント フォレスト内にある古い Exchange 2003 スキーマが検出されています。 このスキーマ拡張が追加されてから、リソース フォレストが Fabrikam の環境に導入されました。 古い Exchange 実装の属性が同期されないようにするには、図のように同期規則を無効にする必要があります。

### <a name="change-an-out-of-box-rule"></a>標準の規則の変更
標準の規則を変更する必要があるのは、結合規則を変更しなければならない場合のみです。 属性フローを変更する必要がある場合は、標準の規則よりも優先順位が高い同期規則を作成してください。 実際に複製する必要がある規則は **In from AD - User Join** のみです。 他のすべての規則は、優先順位が高い規則で上書きできます。

標準の規則を変更する必要がある場合は、標準の規則のコピーを作成し、元の規則を無効にする必要があります。 そして、コピーした規則を変更します。 同期規則エディターは、この手順で役立ちます。 標準の規則を開くと、次のダイアログ ボックスが表示されます。  
![標準の規則の警告](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

**[はい]** を選択して規則のコピーを作成します。 複製した規則を開きます。  
![コピーした規則](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

このコピーした規則のスコープ、結合、変換について必要な変更を加えます。

## <a name="next-steps"></a>次のステップ
**概要トピック**

* [Azure AD Connect sync: 同期を理解してカスタマイズする](active-directory-aadconnectsync-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)



<!--HONumber=Feb17_HO1-->


