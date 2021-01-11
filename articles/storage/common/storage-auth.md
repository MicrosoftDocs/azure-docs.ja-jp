---
title: データ操作の承認
titleSuffix: Azure Storage
description: Azure Active Directory、共有キーによる承認、共有アクセス署名 (SAS) など、Azure Storage へのアクセスを承認するさまざまな方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: a56fdf8bc3bfa93c12cce66664e68b223fa97ff2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87029537"
---
# <a name="authorizing-access-to-data-in-azure-storage"></a>Azure Storage 内のデータへのアクセスの承認

ストレージ アカウントのデータにアクセスするたびに、クライアントは HTTP/HTTPS 経由で Azure Storage に要求を行います。 クライアントにデータへのアクセスに必要なアクセス許可があることをサービスが確認できるように、セキュリティで保護されたリソースに対するすべての要求が承認される必要があります。

次の表は、リソースへのアクセスを承認するために Azure Storage に用意されているオプションをまとめたものです。

| Azure の成果物 | 共有キー (ストレージ アカウント キー) | Shared Access Signature (SAS) | Azure Active Directory (Azure AD) | オンプレミス Active Directory Domain Services (プレビュー) | 匿名のパブリック読み取りアクセス |
| -------------- | -------------------------------- | ----------------------------- | --------------------------------- | ------------------------------------------------------ | ---------------------------- |
|Azure BLOB     |[サポートされています](/rest/api/storageservices/authorize-with-shared-key/)         |[サポートされています](storage-sas-overview.md)         |[サポートされています](storage-auth-aad.md)         |サポートされていません|[サポートされています](../blobs/storage-manage-access-to-resources.md)         |
|Azure Files (SMB)     |[サポートされています](/rest/api/storageservices/authorize-with-shared-key/)         |サポートされていません         |[AAD ドメイン サービスでのみサポートされています](../files/storage-files-active-directory-overview.md)         |[サポートされています。資格情報を Azure AD と同期する必要があります](../files/storage-files-active-directory-overview.md)|サポートされていません         |
|Azure Files (REST)     |[サポートされています](/rest/api/storageservices/authorize-with-shared-key/)         |[サポートされています](storage-sas-overview.md)         |サポートされていません         |サポートされていません |サポートされていません。         |
|Azure キュー     |[サポートされています](/rest/api/storageservices/authorize-with-shared-key/)         |[サポートされています](storage-sas-overview.md)         |[サポートされています](storage-auth-aad.md)         |サポートされていません | サポートされていません         |
|Azure テーブル     |[サポートされています](/rest/api/storageservices/authorize-with-shared-key/)         |[サポートされています](storage-sas-overview.md)         |サポートされていません         |サポートされていません| サポートされていません         |

ここでは、各認証オプションについて簡単に説明します。

- BLOB とキュー用の **Azure Active Directory (Azure AD) 統合**。 Azure AD には、ストレージ アカウント内のリソースに対するクライアントのアクセスを制御するロールベースのアクセス制御 (RBAC) が用意されています。 BLOB とキューに対する Azure AD 統合の詳細については、「[Azure Active Directory を使用して BLOB とキューへのアクセスを承認する](storage-auth-aad.md)」を参照してください。

- Azure Files の **Azure Active Directory Domain Services (Azure AD DS) 認証**。 Azure Files では、Azure AD DS を介したサーバー メッセージ ブロック (SMB) の ID ベースの承認がサポートされています。 RBAC を使用して、ストレージ アカウント内の Azure Files リソースへのクライアントのアクセスを細かく制御できます。 ドメイン サービスを利用した Azure Files 認証に関する詳細については、[概要](../files/storage-files-active-directory-overview.md)のページを参照してください。

- Azure Files 用の**オンプレミス Active Directory Domain Services (AD DS、またはオンプレミス AD DS) 認証 (プレビュー)** 。 Azure Files では、AD DS を使用した SMB での ID ベースの承認がサポートされています。 AD DS 環境は、オンプレミス マシンまたは Azure VM でホストできます。 Files への SMB アクセスは、ドメインに参加しているマシン (オンプレミスまたは Azure) からの AD DS 資格情報を使用してサポートされます。 共有レベルのアクセス制御には RBAC の組み合わせを、ディレクトリまたはファイル レベルのアクセス許可の適用には NTFS DACL を使用できます。 ドメイン サービスを利用した Azure Files 認証に関する詳細については、[概要](../files/storage-files-active-directory-overview.md)のページを参照してください。

- BLOB、ファイル、キュー、およびテーブル用の**共有キー認証**。 共有キーを使用するクライアントは、ストレージ アカウントのアクセス キーを使用して署名されたすべての要求にヘッダーを渡します。 詳細については、[共有キーによる承認](/rest/api/storageservices/authorize-with-shared-key/)に関するページを参照してください。
- BLOB、ファイル、キュー、およびテーブル用の**共有アクセス署名**。 共有アクセス署名 (SAS) には、ストレージ アカウント内のリソースに対する制限付きの委任アクセス機能があります。 署名が有効な期間、または付与するアクセス許可に制約を追加すると、柔軟にアクセスを管理できます。 詳細については、「[Shared Access Signatures (SAS) の使用](storage-sas-overview.md)」をご覧ください。
- コンテナーと BLOB 用の**匿名パブリック読み取りアクセス** 承認は必要ありません。 詳細については、「 [コンテナーと BLOB への匿名読み取りアクセスを管理する](../blobs/storage-manage-access-to-resources.md)」を参照してください。  

既定では、Azure Storage のすべてのリソースはセキュリティで保護され、アカウント所有者だけが使用できます。 前述の承認戦略のいずれかを使用して、ストレージ アカウントのリソースに対するアクセスをクライアントに許可することはできますが、セキュリティと使いやすさを最大限に高めるには Azure AD を使用することをお勧めします。

## <a name="next-steps"></a>次のステップ

- [Azure Active Directory を使用して Azure の BLOB およびキューへのアクセスを承認する](storage-auth-aad.md)
- [共有キーを使用して承認する](/rest/api/storageservices/authorize-with-shared-key/)
- [共有アクセス署名 (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](storage-sas-overview.md)
