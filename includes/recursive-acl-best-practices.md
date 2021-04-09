---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 02/03/2021
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: b0be074c995fcc62f63a4a2ebf4149d3040e7b6b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101751060"
---
## <a name="best-practices"></a>ベスト プラクティス

このセクションでは、ACL を再帰的に設定するためのベスト プラクティス ガイドラインについて説明します。 

#### <a name="handling-runtime-errors"></a>実行時エラーの処理

実行時エラーは、さまざまな理由で発生する可能性があります (たとえば、停電やクライアント接続の問題など)。 実行時エラーが発生した場合は、再帰的な ACL プロセスを再開します。 ACL は、悪影響を及ぼすことなく項目に再適用できます。 

#### <a name="handling-permission-errors-403"></a>アクセス許可エラー (403) の処理

再帰的な ACL プロセスの実行中にアクセス制御の例外が発生した場合、AD の[セキュリティ プリンシパル](../articles/role-based-access-control/overview.md#security-principal)に、ディレクトリ階層内にある 1 つ以上の子項目に ACL を適用するための十分なアクセス許可がない可能性があります。 アクセス許可エラーが発生すると、プロセスが停止し、継続トークンが提供されます。 アクセス許可の問題を修正してから、継続トークンを使用して残りのデータセットを処理します。 既に正常に処理済みのディレクトリとファイルをもう一度処理する必要はありません。 再帰的な ACL プロセスを再開することも選択できます。 ACL は、悪影響を及ぼすことなく項目に再適用できます。 

#### <a name="credentials"></a>資格情報 

ターゲット ストレージ アカウントまたはコンテナーのスコープ内の[ストレージ BLOB データ所有者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner)ロールが割り当てられている Azure AD セキュリティ プリンシパルをプロビジョニングすることをお勧めします。 

#### <a name="performance"></a>パフォーマンス 

待機時間を短縮するには、ストレージ アカウントと同じリージョンにある Azure 仮想マシン (VM) で再帰的な ACL プロセスを実行することをお勧めします。 

#### <a name="acl-limits"></a>ACL の制限

ディレクトリまたはファイルに適用できる ACL の最大数は、アクセス ACL が 32 個と既定 ACL が 32 個です。 詳細については、[Azure Data Lake Storage Gen2 でのアクセス制御](../articles/storage/blobs/data-lake-storage-access-control.md)に関するページを参照してください。