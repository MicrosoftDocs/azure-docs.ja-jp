---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ff2ed5abbf2ce67a0b96a5da450b83832403db1f
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269357"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) は、[Azure Active Directory (Azure AD) Domain Services](../articles/active-directory-domain-services/overview.md) を使用した、SMB (Server Message Block) 上の ID ベースの認証をサポートします。 ドメインに参加している Windows 仮想マシン (VM) は、[Azure AD](../articles/active-directory/fundamentals/active-directory-whatis.md) の資格情報を使用して Azure ファイル共有にアクセスできます。 

Azure AD で[ロールベースのアクセス制御 (RBAC)](../articles/role-based-access-control/overview.md) を使用して、ユーザーやグループなどの ID に対する Azure Files 共有レベルのアクセスを管理できます。 Azure Files へのアクセスに使用されるアクセス許可の共通セットを含むカスタムの RBAC ロールを定義することができます。 カスタムの RBAC ロールを Azure AD ID に割り当てると、その ID には、それらのアクセス許可に従って Azure ファイル共有へのアクセス権が与えられます。

プレビューの一環として、Azure Files は、ファイル共有内のすべてのファイルとディレクトリに対する [NTFS DACL](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) の保持、継承、および適用もサポートしています。 ファイル共有から Azure Files に (または逆方向に) データをコピーする場合、NTFS DACL が保持されるように指定することができます。 このように、Azure Files を使用してバックアップ シナリオを実装し、オンプレミスのファイル共有とクラウドのファイル共有の間で NTFS DACLS を保持することができます。 

> [!NOTE]
> - Linux VM では、SMB アクセスに対する Azure AD Domain Services 認証はサポートされていません。 サポートされているのは Windows VM のみです。
> - Active Directory ドメイン参加済みコンピューターでは、SMB アクセスに対する Azure AD Domain Services 認証はサポートされていません。 当面は、[Azure Files Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) を活用してデータを Azure Files に移行し、オンプレミスの AD ドメイン参加済みコンピューターから AD 証明書を使用してアクセス制御の実施を続行することを検討できます。 
> - SMB アクセスに対する Azure AD Domain Services 認証は、2018 年 9 月 24 日より後に作成されたストレージ アカウントのみで利用できます。
> - Azure File Sync Service によって管理される Azure ファイル共有では、SMB アクセスに対する Azure AD Domain Services 認証と NTFS DACL の永続化はサポートされません。 
