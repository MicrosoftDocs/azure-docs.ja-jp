---
title: 'ハイブリッド ID: ディレクトリ統合ツールの比較 | Microsoft Docs'
description: このページでは、ディレクトリ統合に使用できる各種ディレクトリ統合ツールを包括的に比較した表を提供しています。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/07/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70e5c7e5fa428d5a71ca1a7468bbaab2fc94078e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101643842"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>ハイブリッド ID ディレクトリ統合ツールの比較
ディレクトリ統合ツールは、長年にわたって成長および進化してきました。  


- [FIM](/previous-versions/windows/desktop/forefront-2010/ff182370(v=vs.100)) と [MIM](/microsoft-identity-manager/microsoft-identity-manager-2016) は引き続きサポートされており、主にオンプレミス システム間の同期が有効になります。   [FIM Windows Azure AD Connector](/previous-versions/mim/dn511001(v=ws.10)) は FIM と MIM の両方でサポートされていますが、新しいデプロイには推奨されません。Notes や SAP HCM などのオンプレミス ソースを使用しているお客様は、MIM を使用して Active Directory Domain Services (AD DS) にデータを入力し、Azure AD Connect 同期または Azure AD Connect クラウド プロビジョニングを使用して AD DS から Azure AD に同期する必要があります。
- AD DS フォレストと Azure AD 間で同期するために、[Azure AD Connect 同期](how-to-connect-sync-whatis.md)には、以前に DirSync および Azure AD Sync でリリースされたコンポーネントと機能が組み込まれています。  
- [Azure AD Connect クラウド プロビジョニング](../cloud-sync/what-is-cloud-sync.md) は、AD DS から Azure AD に同期するための新しい Microsoft エージェントです。買収した会社の AD フォレストが親会社の AD フォレストから分離されている合併や買収などのシナリオに役立ちます。

Azure AD Connect 同期と Azure AD Connect クラウド プロビジョニングの違いの詳細については、記事「[Azure AD Connect クラウド プロビジョニングとは](../cloud-sync/what-is-cloud-sync.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。