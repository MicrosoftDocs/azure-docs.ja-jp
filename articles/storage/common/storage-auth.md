---
title: Azure Storage へのアクセスを承認する | Microsoft Docs
description: Azure Active Directory、共有キー認証、共有アクセス署名など、Azure Storage へのアクセスを承認するさまざまな方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c20f699a2d2270d11935b0216b1655390ece211c
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671056"
---
# <a name="authorizing-access-to-azure-storage"></a>Azure Storage へのアクセスを承認する

ストレージ アカウントのデータにアクセスするたびに、クライアントは HTTP/HTTPS 経由で Azure Storage に要求を行います。 クライアントにデータへのアクセスに必要なアクセス許可があることをサービスが確認できるように、セキュリティで保護されたリソースに対するすべての要求が承認される必要があります。

次の表は、リソースへのアクセスを承認するために Azure Storage に用意されているオプションをまとめたものです。

|  |共有キー (ストレージ アカウント キー)  |Shared Access Signature (SAS)  |Azure Active Directory (Azure AD)  |匿名のパブリック読み取りアクセス  |
|---------|---------|---------|---------|---------|
|Azure BLOB     |[サポートされています](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[サポートされています](storage-sas-overview.md)         |[サポートされています](storage-auth-aad.md)         |[サポートされています](../blobs/storage-manage-access-to-resources.md)         |
|Azure Files (SMB)     |[サポートされています](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |サポートされていません         |[AAD ドメイン サービスでのみサポートされています](../files/storage-files-active-directory-overview.md)         |サポートされていません         |
|Azure Files (REST)     |[サポートされています](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[サポートされています](storage-sas-overview.md)         |サポートされていません         |サポートされていません         |
|Azure キュー     |[サポートされています](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[サポートされています](storage-sas-overview.md)         |[サポートされています](storage-auth-aad.md)         |サポートされていません         |
|Azure テーブル     |[サポートされています](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[サポートされています](storage-sas-overview.md)         |サポートされていません         |サポートされていません         |

ここでは、各認証オプションについて簡単に説明します。

- BLOB とキュー用の **Azure Active Directory (Azure AD) 統合**。 Azure AD には、ストレージ アカウント内のリソースに対するクライアントのアクセスをきめ細かく制御するロールベースのアクセス制御 (RBAC) が用意されています。 BLOB とキューに対する Azure AD 統合の詳細については、[Azure Active Directory を使用した Azure Storage への要求の認証](storage-auth-aad.md)に関する記事を参照してください。

- ファイルの **Azure AD Domain Services (DS) 統合 (プレビュー)** 。 Azure Files は、Azure AD DS を介したサーバー メッセージ ブロック (SMB) を介した ID ベースの認証をサポートしています。 これには、ストレージ アカウント内のリソースへのクライアントのアクセスを細かく制御できる RBAC が用意されています。 ドメイン サービスを使用したファイルに対する Azure AD 統合の詳細については、「[Azure Files での SMB アクセスに対する Azure Active Directory Domain Services (AAD DS) 認証の概要 (プレビュー)](../files/storage-files-active-directory-overview.md)」を参照してください。

- BLOB、ファイル、キュー、およびテーブル用の**共有キー認証**。 共有キーを使用するクライアントは、ストレージ アカウントのアクセス キーを使用して署名されたすべての要求にヘッダーを渡します。 詳細については、[共有キーによる承認](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)に関するページを参照してください。
- BLOB、ファイル、キュー、およびテーブル用の**共有アクセス署名**。 共有アクセス署名 (SAS) には、ストレージ アカウント内のリソースに対する制限付きの委任アクセス機能があります。 署名が有効な期間、または付与するアクセス許可に制約を追加すると、柔軟にアクセスを管理できます。 詳細については、「[Shared Access Signatures (SAS) の使用](storage-sas-overview.md)」をご覧ください。
- コンテナーと BLOB 用の**匿名パブリック読み取りアクセス** 承認は必要ありません。 詳細については、「 [コンテナーと BLOB への匿名読み取りアクセスを管理する](../blobs/storage-manage-access-to-resources.md)」を参照してください。  

既定では、Azure Storage のすべてのリソースはセキュリティで保護され、アカウント所有者だけが使用できます。 前述の承認戦略のいずれかを使用して、ストレージ アカウントのリソースに対するアクセスをクライアントに許可することはできますが、セキュリティと使いやすさを最大限に高めるには Azure AD を使用することをお勧めします。 
