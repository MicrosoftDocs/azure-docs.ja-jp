---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/16/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 06db7bcb5698f152dd5062762fdb3d59ae326e22
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102603249"
---
Azure ファイル共有の SMB マルチチャネルには、現在、次の制限があります。
- ローカルでの冗長 FileStorage アカウントでのみ使用できます。
- Windows クライアントでのみサポートされます。 
- 最大チャネル数は 4 です。
- SMB ダイレクトはサポートされていません。
- ストレージ アカウントのプライベート エンドポイントはサポートされていません。
- オンプレミスの Active Directory Domain Services (AD DS) または Azure AD DS の [ID ベースの認証](../articles/storage/files/storage-files-active-directory-overview.md)が Azure Files に対して有効になっているストレージ アカウントの場合は、SMB クライアントが Windows エクスプローラーを使用して、ディレクトリとファイルに対する NTFS アクセス許可を構成することができません。
    - アクセス許可を構成する代わりに、Windows [icacls](/windows-server/administration/windows-commands/icacls) ツールまたは [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl) コマンドを使用してください。

