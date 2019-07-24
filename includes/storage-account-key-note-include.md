---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3cfdca99c91dc54a711801d92aa0da91fb9703e4
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246997"
---
> [!IMPORTANT]
> ストレージ アカウント キーは、ストレージ アカウントの root パスワードに似ています。 アカウント キーは常に慎重に保護してください。 このキーを他のユーザーに配布したり、ハードコーディングしたり、他のユーザーがアクセスできるプレーン テキストで保存したりしないでください。 アカウント キーが侵害されたと思われる場合は、Azure Portal を使用してキーを再生成してください。
> 
> SAS (Shared Access Signature) トークンは、アカウント アクセス キーと同じように厳重な保護が必要です。 きめ細かな制御を提供する一方で、SAS はストレージ アカウント内のリソースへのアクセス権を付与するため、パブリックに共有してはなりません。 トラブルシューティングのために共有が必要な場合は、機密情報を除去したバージョンのログ ファイルを使用すること、または SAS トークン (存在する場合) をログ ファイルから削除することを検討し、スクリーンショットに SAS 情報が含まれていないことも確認してください。
> 
> セキュリティ強化のため、可能であれば BLOB およびキュー ストレージ アプリケーション (プレビュー) には Azure Active Directory (Azure AD) 認証を使用することをお勧めします。 詳細については、[Azure Active Directory を使用して Azure BLOB およびキューへのアクセスを認証する (プレビュー)](https://docs.microsoft.com/azure/storage/common/storage-auth-aad) 方法に関するページを参照してください。
