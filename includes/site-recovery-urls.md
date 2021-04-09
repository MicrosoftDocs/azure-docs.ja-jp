---
title: インクルード ファイル
description: インクルード ファイル
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/12/2018
ms.author: raynew
ms.openlocfilehash: f4cd9cad3813378fcdc3f06e8ab1c28eced4f93c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95999189"
---
名前 | 商用の URL | 政府機関の URL | [説明]
--- | --- | --- | ---
Azure Active Directory | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | Azure Active Directory を使用したアクセス制御と ID 管理に使用されます。 
Backup | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | レプリケーション データの転送と調整に使用。
レプリケーション | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | レプリケーション管理操作と調整に使用。
Storage | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | レプリケートされたデータを格納するストレージ アカウントへのアクセスに使用。
テレメトリ (オプション) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | テレメトリに使用されます。
時刻の同期 | ``time.windows.com`` | ``time.nist.gov`` | すべてのデプロイでシステム時刻とグローバル時刻の間の時刻同期を確認するために使用されます。


