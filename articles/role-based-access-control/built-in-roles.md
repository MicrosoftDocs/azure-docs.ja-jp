---
title: Azure 組み込みロール - Azure RBAC
description: この記事では、Azure ロールベースのアクセス制御 (Azure RBAC) の Azure 組み込みロールについて説明します。 Actions、NotActions、DataActions、NotDataActions を一覧表示します。
services: active-directory
ms.service: role-based-access-control
ms.topic: reference
ms.workload: identity
author: rolyon
ms.author: rolyon
ms.date: 02/25/2021
ms.custom: generated
ms.openlocfilehash: baa783d0dfb54e0e606e3f6c6d5397941ae7b7bc
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011631"
---
# <a name="azure-built-in-roles"></a>Azure 組み込みロール

[Azure ロールベースのアクセス制御 (Azure RBAC)](overview.md) には、ユーザー、グループ、サービス プリンシパル、マネージド ID に割り当てることのできる Azure 組み込みロールがいくつかあります。 ロールの割り当ては、Azure リソースへのアクセスを制御する方法です。 組み込みロールが組織の特定のニーズを満たさない場合は、独自の [Azure カスタム ロール](custom-roles.md)を作成することができます。

この記事では、常に進化している Azure 組み込みロールが一覧表示されています。 最新のロールを取得するには、[Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) または [az role definition list](/cli/azure/role/definition#az-role-definition-list) を使用してください。 Azure Active Directory (Azure AD) の管理者ロールについては、「[Azure Active Directory での管理者ロールのアクセス許可](../active-directory/roles/permissions-reference.md)」を参照してください。

次の表に、各組み込みロールの簡単な説明と一意の ID を示します。 ロール名をクリックすると、各ロールの `Actions`、`NotActions`、`DataActions`、`NotDataActions` の一覧が表示されます。 これらのアクションの意味と、管理とデータ プレーンへの適用方法については、「[Azure ロールの定義について](role-definitions.md)」を参照してください。

## <a name="all"></a>All

> [!div class="mx-tableFixed"]
> | 組み込みのロール | 説明 | id |
> | --- | --- | --- |
> | **全般** |  |  |
> | [Contributor](#contributor) | すべてのリソースを管理するためのフル アクセスが付与されますが、Azure RBAC でロールを割り当てたり、Azure Blueprints で割り当てを管理したり、イメージ ギャラリーを共有したりすることはできません。 | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [所有者](#owner) | Azure RBAC でロールを割り当てる権限を含め、すべてのリソースを管理するためのフル アクセスを付与します。 | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Reader](#reader) | すべてのリソースを表示しますが、変更を加えることはできません。 | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [User Access Administrator](#user-access-administrator) | Azure リソースに対するユーザー アクセスを管理します。 | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Compute** |  |  |
> | [Classic Virtual Machine Contributor](#classic-virtual-machine-contributor) | 従来の仮想マシンを管理できますが、アクセスすることはできません。また、接続先の仮想ネットワークやストレージ アカウントにもアクセスできません。 | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Virtual Machine Administrator Login](#virtual-machine-administrator-login) | ポータルで仮想マシンを表示し、管理者としてログインします | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Virtual Machine Contributor](#virtual-machine-contributor) | 仮想マシンを管理できますが、アクセスすることはできません。また、接続先の仮想ネットワークやストレージ アカウントにもアクセスできません。 | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [Virtual Machine User Login](#virtual-machine-user-login) | ポータルで仮想マシンを表示し、通常のユーザーとしてログインします。 | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **ネットワーク** |  |  |
> | [CDN Endpoint Contributor](#cdn-endpoint-contributor) | CDN エンドポイントを管理できますが、アクセス権を他のユーザーに付与することはできません。 | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [CDN Endpoint Reader](#cdn-endpoint-reader) | CDN エンドポイントを表示できますが、変更はできません。 | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [CDN Profile Contributor](#cdn-profile-contributor) | CDN プロファイルとそのエンドポイントを管理できますが、アクセス権を他のユーザーに付与することはできません。 | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [CDN Profile Reader](#cdn-profile-reader) | CDN プロファイルとそのエンドポイントを表示できますが、変更はできません。 | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Classic Network Contributor](#classic-network-contributor) | 従来のネットワークを管理できます。ただし、それらへのアクセスは含まれません。 | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [DNS Zone Contributor](#dns-zone-contributor) | Azure DNS の DNS ゾーンとレコード セットを管理できますが、それにアクセスできるユーザーを制御することはできません。 | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [Network Contributor](#network-contributor) | ネットワークを管理できます。ただし、それらへのアクセスは含まれません。 | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [プライベート DNS ゾーンの共同作成者](#private-dns-zone-contributor) | プライベート DNS ゾーンのリソースを管理できますが、リンク先の仮想ネットワークを管理することはできません。 | b12aa53e-6015-4669-85d0-8515ebb3ae7f |
> | [Traffic Manager Contributor](#traffic-manager-contributor) | Traffic Manager プロファイルを管理できますが、それにアクセスできるユーザーを制御することはできません。 | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Storage** |  |  |
> | [Avere 共同作成者](#avere-contributor) | Avere vFXT クラスターを作成および管理できます。 | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Avere オペレーター](#avere-operator) | クラスターを管理するために Avere vFXT クラスターによって使用されます | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Backup Contributor](#backup-contributor) | バックアップ サービスを管理できますが、資格情報コンテナーの作成や他のユーザーに対するアクセス権の付与を行うことはできません | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Backup Operator](#backup-operator) | バックアップ サービスを管理できます (バックアップの削除、資格情報コンテナーの作成、他のユーザーに対するアクセス権の付与を除く) | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Backup Reader](#backup-reader) | バックアップ サービスを表示できますが、変更を行うことはできません | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Classic Storage Account Contributor](#classic-storage-account-contributor) | 従来のストレージ アカウントを管理できますが、アクセスすることはできません。 | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [従来のストレージ アカウント キー オペレーターのサービス ロール](#classic-storage-account-key-operator-service-role) | 従来のストレージ アカウント キー オペレーターは、従来のストレージ アカウントでのキーの一覧表示と再生成を行うことができます | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Data Box Contributor](#data-box-contributor) | Data Box サービスですべてを管理できます (他のユーザーに対するアクセス権の付与を除く)。 | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Data Box 閲覧者](#data-box-reader) | Data Box サービスを管理できます (注文の作成または注文の詳細の編集、および他のユーザーに対するアクセス権の付与を除く)。 | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Data Lake Analytics Developer](#data-lake-analytics-developer) | 独自のジョブを送信、監視、管理できますが、Data Lake Analytics アカウントを作成または削除することはできません。 | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | [Reader and Data Access](#reader-and-data-access) | すべてを表示することができますが、ストレージ アカウントや含まれるリソースの削除や作成はできません。 ストレージ アカウント キーへのアクセスを使用して、ストレージ アカウントに含まれるすべてのデータへの読み取り/書き込みアクセスも許可されます。 | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Storage Account Contributor](#storage-account-contributor) | ストレージ アカウントの管理を許可します。 アカウント キーへのアクセスを提供します。これを使用して、共有キー認証を使用してデータにアクセスすることができます。 | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [ストレージ アカウント キー オペレーターのサービス ロール](#storage-account-key-operator-service-role) | ストレージ アカウント アクセス キーを一覧表示および再生成できます。 | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [ストレージ BLOB データ共同作成者](#storage-blob-data-contributor) | Azure Storage コンテナーと BLOB の読み取り、書き込み、削除を行います。 特定のデータ操作に必要なアクションについては、「[Permissions for calling blob and queue data operations (BLOB およびキューのデータの操作を呼び出すためのアクセス許可)](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」をご覧ください。 | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [ストレージ BLOB データ所有者](#storage-blob-data-owner) | Azure Storage Blob コンテナーとデータに対するフル アクセス (POSIX アクセスの制御の割り当てを含む) を提供します。 特定のデータ操作に必要なアクションについては、「[Permissions for calling blob and queue data operations (BLOB およびキューのデータの操作を呼び出すためのアクセス許可)](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」をご覧ください。 | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [ストレージ BLOB データ閲覧者](#storage-blob-data-reader) | Azure Storage コンテナーと BLOB の読み取りと一覧表示を行います。 特定のデータ操作に必要なアクションについては、「[Permissions for calling blob and queue data operations (BLOB およびキューのデータの操作を呼び出すためのアクセス許可)](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」をご覧ください。 | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Storage Blob デリゲータ](#storage-blob-delegator) | Azure AD 資格情報で署名されたコンテナーまたは BLOB 用の共有アクセス署名を作成するために使用できるユーザー委任キーを取得します。 詳細については、「[ユーザー委任 SAS を作成する](/rest/api/storageservices/create-user-delegation-sas)」を参照してください。 | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [記憶域ファイル データの SMB 共有の共同作成者](#storage-file-data-smb-share-contributor) | Azure ファイル共有のファイルまたはディレクトリに対する読み取り、書き込み、削除のアクセス権を許可します。 このロールに相当する機能は Windows ファイル サーバーに組み込まれていません。 | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [記憶域ファイル データの SMB 共有の管理者特権共同作成者](#storage-file-data-smb-share-elevated-contributor) | Azure ファイル共有のファイルまたはディレクトリに対する ACL の読み取り、書き込み、削除、変更を許可します。 このロールは、Windows ファイル サーバーでのファイル共有 ACL の変更に相当します。 | a7264617-510b-434b-a828-9731dc254ea7 |
> | [ストレージ ファイル データの SMB 共有の閲覧者](#storage-file-data-smb-share-reader) | Azure ファイル共有のファイルまたはディレクトリに対する読み取りアクセスを許可します。 このロールは、Windows ファイル サーバーでのファイル共有 ACL の読み取りに相当します。 | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [ストレージ キュー データ共同作成者共同作成者](#storage-queue-data-contributor) | Azure Storage キューおよびキュー メッセージの読み取り、書き込み、削除を行います。 特定のデータ操作に必要なアクションについては、「[Permissions for calling blob and queue data operations (BLOB およびキューのデータの操作を呼び出すためのアクセス許可)](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」をご覧ください。 | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [ストレージ キュー データのメッセージ プロセッサ](#storage-queue-data-message-processor) | Azure Storage キューからのメッセージのピーク、取得、削除を行います。 特定のデータ操作に必要なアクションについては、「[Permissions for calling blob and queue data operations (BLOB およびキューのデータの操作を呼び出すためのアクセス許可)](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」をご覧ください。 | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [ストレージ キュー データ メッセージ送信者](#storage-queue-data-message-sender) | Azure Storage キューにメッセージを追加します。 特定のデータ操作に必要なアクションについては、「[Permissions for calling blob and queue data operations (BLOB およびキューのデータの操作を呼び出すためのアクセス許可)](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」をご覧ください。 | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [ストレージ キュー データ閲覧者](#storage-queue-data-reader) | Azure Storage キューおよびキュー メッセージの読み取りと一覧表示を行います。 特定のデータ操作に必要なアクションについては、「[Permissions for calling blob and queue data operations (BLOB およびキューのデータの操作を呼び出すためのアクセス許可)](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」をご覧ください。 | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Web** |  |  |
> | [Azure Maps データ共同作成者](#azure-maps-data-contributor) | Azure Maps アカウントからのマップ関連データへの読み取り、書き込み、削除のアクセスを付与します。 | 8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204 |
> | [Azure Maps データ閲覧者](#azure-maps-data-reader) | Azure Maps アカウントからマップ関連データを読み取るためのアクセス権を付与します。 | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Search Service Contributor](#search-service-contributor) | Search サービスを管理できます。ただし、それらへのアクセスは含まれません。 | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [SignalR AccessKey 閲覧者](#signalr-accesskey-reader) | SignalR サービス アクセス キーを読み取ります | 04165923-9d83-45d5-8227-78b77b0a687e |
> | [SignalR アプリ サーバー (プレビュー)](#signalr-app-server-preview) | AAD の認証オプションを使用して、アプリ サーバーが SignalR Service にアクセスできるようにします。 | 420fcaa2-552c-430f-98ca-3264be4806c7 |
> | [SignalR 共同作成者](#signalr-contributor) | SignalR のサービス リソースの作成、読み取り、更新、削除を行います | 8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761 |
> | [SignalR サーバーレス共同作成者 (プレビュー)](#signalr-serverless-contributor-preview) | AAD の認証オプションを使用して、アプリがサーバーレス モードでサービスにアクセスできるようにします。 | fd53cd77-2268-407a-8f46-7e7863d0f521 |
> | [SignalR Service 所有者 (プレビュー)](#signalr-service-owner-preview) | Azure SignalR Service REST API へのフル アクセス | 7e4f1700-ea5a-4f59-8f37-079cfe29dce3 |
> | [SignalR Service 閲覧者 (プレビュー)](#signalr-service-reader-preview) | Azure SignalR Service REST API への読み取り専用アクセス | ddde6b66-c0df-4114-a159-3618637b3035 |
> | [Web Plan Contributor](#web-plan-contributor) | Web サイトの Web プランを管理できます。ただし、それらへのアクセスは含まれません。 | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [Website Contributor](#website-contributor) | Web サイト (Web プランではない) を管理できます。ただし、それらへのアクセスは含まれません。 | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Containers** |  |  |
> | [AcrDelete](#acrdelete) | acr の削除 | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | ACR イメージ署名者 | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | acr のプル | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | acr のプッシュ | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | ACR 検査データ閲覧者 | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | ACR 検査データ作成者 | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Azure Kubernetes Service クラスター管理者ロール](#azure-kubernetes-service-cluster-admin-role) | クラスター管理者の資格情報アクションを一覧表示します。 | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Azure Kubernetes Service クラスター ユーザー ロール](#azure-kubernetes-service-cluster-user-role) | クラスター ユーザーの資格情報アクションを一覧表示します。 | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | [Azure Kubernetes Service 共同作成者ロール](#azure-kubernetes-service-contributor-role) | Azure Kubernetes Service クラスターへの読み取りおよび書き込みアクセスを許可します。 | ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8 |
> | [Azure Kubernetes Service RBAC 管理者](#azure-kubernetes-service-rbac-admin) | リソース クォータと名前空間の更新または削除を除き、クラスターおよび名前空間のすべてのリソースを管理できます。 | 3498e952-d568-435e-9b2c-8d77e338d7f7 |
> | [Azure Kubernetes Service RBAC クラスター管理者](#azure-kubernetes-service-rbac-cluster-admin) | クラスター内のすべてのリソースを管理できます。 | b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b |
> | [Azure Kubernetes Service RBAC 閲覧者](#azure-kubernetes-service-rbac-reader) | 名前空間内のほとんどのオブジェクトを表示するための読み取り専用アクセスが許可されます。 ロールまたはロールのバインドを表示することはできません。 このロールでは、Secrets の表示は許可されません。これは、Secrets の内容を読み取ると、名前空間の ServiceAccount 資格情報にアクセスでき、それにより名前空間の任意の ServiceAccount として API にアクセスできるようになるためです (特権エスカレーションの形式)。 クラスター スコープでこのロールを適用すると、すべての名前空間へのアクセス権が付与されます。 | 7f6c6a51-bcf8-42ba-9220-52d62157d7db |
> | [Azure Kubernetes Service RBAC ライター](#azure-kubernetes-service-rbac-writer) | 名前空間内のほとんどのオブジェクトに対する読み取り/書き込みアクセスが許可されます。このロールでは、ロールまたはロールのバインドの表示または変更が許可されません。 ただし、このロールを使用すると、Secrets にアクセスし、名前空間内の任意の ServiceAccount としてポッドを実行できるので、名前空間内の任意の ServiceAccount の API アクセス レベルを取得するために使用できます。 クラスター スコープでこのロールを適用すると、すべての名前空間へのアクセス権が付与されます。 | a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb |
> | **データベース** |  |  |
> | [Cosmos DB アカウントの閲覧者ロール](#cosmos-db-account-reader-role) | Cosmos DB アカウントのデータを読み取ることができます。 Azure Cosmos DB アカウントの管理については、「[DocumentDB Account Contributor](#documentdb-account-contributor)」をご覧ください。 | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Cosmos DB オペレーター](#cosmos-db-operator) | Azure Cosmos DB アカウントを管理することができます。ただし、アカウント内のデータにはアクセスできません。 アカウント キーと接続文字列へのアクセスは禁止されます。 | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Cosmos DB データベースまたはアカウントのコンテナーの復元要求を送信できます | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [CosmosRestoreOperator](#cosmosrestoreoperator) | 継続的バックアップモードで Cosmos DB データベース アカウントの復元操作を実行できます | 5432c526-bc82-444a-b7ba-57c5b0b5b34f |
> | [DocumentDB Account Contributor](#documentdb-account-contributor) | Azure Cosmos DB アカウントを管理できます。 Azure Cosmos DB は以前は DocumentDB と呼ばれていました。 | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Redis Cache Contributor](#redis-cache-contributor) | Redis Caches を管理できます。ただし、それらへのアクセスは含まれません。 | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [SQL DB Contributor](#sql-db-contributor) | SQL データベースを管理できます。ただし、それらへのアクセスは含まれません。 また、セキュリティ関連のポリシーまたは親 SQL Server を管理することはできません。 | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [SQL マネージド インスタンス共同作成者](#sql-managed-instance-contributor) | SQL マネージド インスタンスと必要なネットワーク構成を管理することができますが、他のユーザーにアクセス権を付与することはできません。 | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [SQL Security Manager](#sql-security-manager) | SQL サーバーとデータベースのセキュリティ関連のポリシーを管理できます。ただし、それらへのアクセスは管理できません。 | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [SQL Server Contributor](#sql-server-contributor) | SQL サーバーとデータベースを管理できます。ただし、それらへのアクセスや、それらのセキュリティ関連ポリシーは管理できません。 | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Analytics** |  |  |
> | [Azure Event Hubs データ所有者](#azure-event-hubs-data-owner) | Azure Event Hubs リソースへのフル アクセスを許可します。 | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Azure Event Hubs データ受信者](#azure-event-hubs-data-receiver) | Azure Event Hubs リソースへの受信アクセスを許可します。 | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Azure Event Hubs データ送信者](#azure-event-hubs-data-sender) | Azure Event Hubs リソースへの送信アクセスを許可します。 | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Data Factory Contributor](#data-factory-contributor) | データ ファクトリまたデータ ファクトリ内の子リソースを作成し管理します。 | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [Data Purger](#data-purger) | Log Analytics ワークスペースの非公開データを削除します。 | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [HDInsight クラスター オペレーター](#hdinsight-cluster-operator) | HDInsight クラスター構成の読み取りと変更を実行できます。 | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [HDInsight ドメイン サービス共同作成者](#hdinsight-domain-services-contributor) | HDInsight Enterprise セキュリティ パッケージに必要なドメイン サービス関連の操作の読み取り、作成、変更、削除を行うことができます。 | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Log Analytics Contributor](#log-analytics-contributor) | Log Analytics 共同作成者は、すべての監視データを読み取り、監視設定を編集できます。 監視設定の編集には、VM 拡張機能の VM への追加、Azure Storage からログの収集を設定できるようにするためのストレージ アカウント キーの読み取り、Automation アカウントの作成と構成、ソリューションの追加、すべての Azure リソースでの Azure Diagnostics の構成が含まれます。 | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Log Analytics Reader](#log-analytics-reader) | Log Analytics Reader は、すべての監視データの表示と検索、およびすべての Azure リソース上の Azure Diagnostics 構成の表示など、監視設定の表示を行うことができます。 | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | [Purview データ キュレーター](#purview-data-curator) | Microsoft.Purview データ キュレーターでは、カタログ データ オブジェクトの作成、読み取り、変更、削除や、オブジェクト間のリレーションシップの確立を行うことができます。 このロールはプレビュー段階にあり、変更される可能性があります。 | 8a3c2885-9b38-4fd2-9d99-91af537c1347 |
> | [Purview データ リーダー](#purview-data-reader) | Microsoft.Purview データ リーダーは、カタログ データ オブジェクトを読み取ることができます。 このロールはプレビュー段階にあり、変更される可能性があります。 | ff100721-1b9d-43d8-af52-42b69c1272db |
> | [Purview データ ソース管理者](#purview-data-source-administrator) | Microsoft.Purview データ ソース管理者は、データ ソースとデータ スキャンを管理できます。 このロールはプレビュー段階にあり、変更される可能性があります。 | 200bba9e-f0c8-430f-892b-6f0794863803 |
> | [Schema Registry Contributor (プレビュー)](#schema-registry-contributor-preview) | Schema Registry グループおよびスキーマの読み取り、書き込み、および削除を行います。 | 5dffeca3-4936-4216-b2bc-10343a5abb25 |
> | [Schema Registry Reader (プレビュー)](#schema-registry-reader-preview) | Schema Registry グループおよびスキーマの読み取りと一覧表示を行います。 | 2c56ea50-c6b3-40a6-83c0-9d98858bc7d2 |
> | **ブロックチェーン** |  |  |
> | [ブロックチェーン メンバー ノードへのアクセス (プレビュー)](#blockchain-member-node-access-preview) | ブロックチェーン メンバー ノードにアクセスできるようにします | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **AI + 機械学習** |  |  |
> | [Cognitive Services 共同作成者](#cognitive-services-contributor) | Cognitive Services のキーの作成、読み取り、更新、削除、管理を行うことができます。 | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Cognitive Services Custom Vision Contributor](#cognitive-services-custom-vision-contributor) | プロジェクトの表示、作成、編集、削除を含む、プロジェクトへのフル アクセス。 | c1ff6cc2-c111-46fe-8896-e0ef812ad9f3 |
> | [Cognitive Services Custom Vision Deployment](#cognitive-services-custom-vision-deployment) | モデルの公開、非公開、またはエクスポートを行います。 Deployment は、プロジェクトを表示できますが、更新することはできません。 | 5c4089e1-6d96-4d2f-b296-c1bc7137275f |
> | [Cognitive Services Custom Vision Labeler](#cognitive-services-custom-vision-labeler) | トレーニング画像の表示と編集、およびイメージ タグの作成、追加、または削除を行うことができます。 ラベラーはプロジェクトを表示できますが、トレーニング画像とタグ以外は更新できません。 | 88424f51-ebe7-446f-bc41-7fa16989e96c |
> | [Cognitive Services Custom Vision Reader](#cognitive-services-custom-vision-reader) | プロジェクトでの読み取り専用のアクション。 閲覧者がこのプロジェクトを作成または更新することはできません。 | 93586559-c37d-4a6b-ba08-b9f0940c2d73 |
> | [Cognitive Services Custom Vision Trainer](#cognitive-services-custom-vision-trainer) | プロジェクトの表示、作成、およびモデルの公開、非公開、エクスポートを含む、モデルのトレーニングを行うことができます。 トレーナーがこのプロジェクトを作成または削除することはできません。 | 0a5ae4ab-0d65-4eeb-be61-29fc9b54394b |
> | [Cognitive Services データ閲覧者 (プレビュー)](#cognitive-services-data-reader-preview) | Cognitive Services データを読み取ります。 | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Cognitive Services Metrics Advisor Administrator](#cognitive-services-metrics-advisor-administrator) | システム レベルの構成を含む、プロジェクトへのフル アクセス。 | cb43c632-a144-4ec5-977c-e80c4affc34a |
> | [Cognitive Services QnA Maker エディター](#cognitive-services-qna-maker-editor) | KB の作成、編集、インポート、およびエクスポートが可能になります。 KB を公開または削除することはできません。 | f4cc2bf9-21be-47a1-bdf1-5c5804381025 |
> | [Cognitive Services QnA Maker 閲覧者](#cognitive-services-qna-maker-reader) | KB のみ、読み取りとテストが可能になります。 | 466ccd10-b268-4a11-b098-b4849f024126 |
> | [Cognitive Services ユーザー](#cognitive-services-user) | Cognitive Services のキーの読み取りおよび一覧表示を行うことができます。 | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **複合現実** |  |  |
> | [Remote Rendering 管理者](#remote-rendering-administrator) | ユーザーに、Azure Remote Rendering での変換、セッション管理、レンダリング、および診断の機能を提供します。 | 3df8b902-2a6f-47c7-8cc5-360e9b272a7e |
> | [Remote Rendering クライアント](#remote-rendering-client) | ユーザーに、Azure Remote Rendering でのセッション管理、レンダリング、および診断の機能を提供します。 | d39065c4-c120-43c9-ab0a-63eed9795f0a |
> | [Spatial Anchors アカウント共同作成者](#spatial-anchors-account-contributor) | アカウントで Spatial Anchors を管理します (削除は含まない) | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Spatial Anchors アカウント所有者](#spatial-anchors-account-owner) | アカウントで Spatial Anchors を管理します (削除も含む) | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Spatial Anchors アカウント閲覧者](#spatial-anchors-account-reader) | アカウントで Spatial Anchors のプロパティを検索して読み取ります | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **統合** |  |  |
> | [API Management Service Contributor](#api-management-service-contributor) | サービスと API を管理できます | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [API Management Service Operator Role](#api-management-service-operator-role) | サービスを管理できますが、API は対象外です | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [API Management Service Reader Role](#api-management-service-reader-role) | サービスと API への読み取り専用アクセスです | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [App Configuration データ所有者](#app-configuration-data-owner) | App Configuration データへのフル アクセスを許可します。 | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | [App Configuration データ閲覧者](#app-configuration-data-reader) | App Configuration データへの読み取りアクセスを許可します。 | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Azure Service Bus データ所有者](#azure-service-bus-data-owner) | Azure Service Bus リソースへのフル アクセスを許可します。 | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Azure Service Bus データ受信者](#azure-service-bus-data-receiver) | Azure Service Bus リソースへの受信アクセスを許可します。 | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Azure Service Bus データ送信者](#azure-service-bus-data-sender) | Azure Service Bus リソースへの送信アクセスを許可します。 | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Azure Stack Registration Owner](#azure-stack-registration-owner) | Azure Stack の登録を管理できます。 | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [EventGrid EventSubscription 共同作成者](#eventgrid-eventsubscription-contributor) | EventGrid のイベント サブスクリプション操作を管理できます。 | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [EventGrid EventSubscription 閲覧者](#eventgrid-eventsubscription-reader) | EventGrid のイベント サブスクリプションを読み取ることができます。 | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [FHIR データ共同作成者](#fhir-data-contributor) | ユーザーまたはプリンシパルに FHIR データへのフル アクセスを許可するロール | 5a1fc7df-4bf1-4951-a576-89034ee01acd |
> | [FHIR データ エクスポーター](#fhir-data-exporter) | ユーザーまたはプリンシパルに FHIR データの読み取りとエクスポートを許可するロール | 3db33094-8700-4567-8da5-1501d4e7e843 |
> | [FHIR データ リーダー](#fhir-data-reader) | ユーザーまたはプリンシパルに FHIR データの読み取りを許可するロール | 4c8d0bbc-75d3-4935-991f-5f3c56d81508 |
> | [FHIR データ ライター](#fhir-data-writer) | ユーザーまたはプリンシパルに FHIR データの読み取りと書き込みを許可するロール | 3f88fce4-5892-4214-ae73-ba5294559913 |
> | [統合サービス環境の共同作成者](#integration-service-environment-contributor) | 統合サービス環境を管理できますが、それらにアクセスすることはできません。 | a41e2c5b-bd99-4a07-88f4-9bf657a760b8 |
> | [統合サービス環境の開発者](#integration-service-environment-developer) | 開発者が統合サービス環境でワークフロー、統合アカウント、および API 接続を作成および更新することを許可します。 | c7aa55d3-1abb-444a-a5ca-5e51e485d6ec |
> | [Intelligent Systems Account Contributor](#intelligent-systems-account-contributor) | Intelligent Systems のアカウントを管理できます。ただし、それらへのアクセスは含まれません。 | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Logic App Contributor](#logic-app-contributor) | ロジック アプリを管理できますが、アクセス権を変更することはできません。 | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Logic App Operator](#logic-app-operator) | ロジック アプリの読み取り、有効化、無効化ができますが、編集または更新はできません。 | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **ID** |  |  |
> | [Managed Identity Contributor](#managed-identity-contributor) | ユーザー割り当て ID の作成、読み取り、更新、削除を行います | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Managed Identity Operator](#managed-identity-operator) | ユーザー割り当て ID の読み取りと割り当てを行います | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Security** |  |  |
> | [Attestation Contributor](#attestation-contributor) | 構成証明プロバイダー インスタンスの読み取り、書き込み、または削除ができます | bbf86eb8-f7b4-4cce-96e4-18cddf81d86e |
> | [Attestation Reader](#attestation-reader) | 構成証明プロバイダーのプロパティを読み取ることができます | fd1bd22b-8476-40bc-a0bc-69b95687b9f3 |
> | [Azure Sentinel 共同作成者](#azure-sentinel-contributor) | Azure Sentinel 共同作成者 | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Azure Sentinel 閲覧者](#azure-sentinel-reader) | Azure Sentinel 閲覧者 | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Azure Sentinel レスポンダー](#azure-sentinel-responder) | Azure Sentinel レスポンダー | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Key Vault Administrator](#key-vault-administrator) | キー コンテナーとその内部にあるすべてのオブジェクト (証明書、キー、シークレットを含む) に対して、すべてのデータ プレーン操作を実行します。 キー コンテナー リソースの管理やロール割り当ての管理はできません。 「Azure ロールベースのアクセス制御」アクセス許可モデルを使用するキー コンテナーでのみ機能します。 | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
> | [Key Vault Certificates Officer](#key-vault-certificates-officer) | キーコンテナーの証明書に対して、アクセス許可の管理を除く任意の操作を実行します。 「Azure ロールベースのアクセス制御」アクセス許可モデルを使用するキー コンテナーでのみ機能します。 | a4417e6f-fecd-4de8-b567-7b0420556985 |
> | [Key Vault Contributor](#key-vault-contributor) | キー コンテナーを管理しますが、Azure RBAC でのロール割り当ては許可されず、シークレット、キー、証明書へのアクセスも許可されません。 | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Key Vault Crypto Officer](#key-vault-crypto-officer) | キーコンテナーのキーに対して、アクセス許可の管理を除く任意の操作を実行します。 「Azure ロールベースのアクセス制御」アクセス許可モデルを使用するキー コンテナーでのみ機能します。 | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
> | [Key Vault Crypto Service Encryption User](#key-vault-crypto-service-encryption-user) | キーのメタデータを読み取り、wrap および unwrap 操作を実行します。 「Azure ロールベースのアクセス制御」アクセス許可モデルを使用するキー コンテナーでのみ機能します。 | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
> | [Key Vault Crypto User](#key-vault-crypto-user) | キーを使用した暗号化操作を実行します。 「Azure ロールベースのアクセス制御」アクセス許可モデルを使用するキー コンテナーでのみ機能します。 | 12338af0-0e69-4776-bea7-57ae8d297424 |
> | [Key Vault Reader](#key-vault-reader) | キー コンテナーとその証明書、キー、シークレットのメタデータを読み取ります。 シークレット コンテンツやキー マテリアルなどの機密値を読み取ることはできません。 「Azure ロールベースのアクセス制御」アクセス許可モデルを使用するキー コンテナーでのみ機能します。 | 21090545-7ca7-4776-b22c-e363652d74d2 |
> | [Key Vault Secrets Officer](#key-vault-secrets-officer) | キーコンテナーのシークレットに対して、アクセス許可の管理を除く任意の操作を実行します。 「Azure ロールベースのアクセス制御」アクセス許可モデルを使用するキー コンテナーでのみ機能します。 | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
> | [Key Vault Secrets User](#key-vault-secrets-user) | シークレット コンテンツを読み取ります。 「Azure ロールベースのアクセス制御」アクセス許可モデルを使用するキー コンテナーでのみ機能します。 | 4633458b-17de-408a-b874-0445c86b69e6 |
> | [Managed HSM contributor](#managed-hsm-contributor) | マネージド HSM プールを管理できます。ただし、それらへのアクセスは含まれません。 | 18500a29-7fe2-46b2-a342-b16a415e101d |
> | [Security Admin](#security-admin) | Security Center の表示および更新のアクセス許可。 セキュリティ閲覧者と同じアクセス許可があり、セキュリティ ポリシーの更新、アラートと推奨事項の無視も可能になります。 | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Security Assessment Contributor](#security-assessment-contributor) | 評価を Security Center にプッシュできます | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [セキュリティ マネージャー (レガシ)](#security-manager-legacy) | これは、レガシ ロールです。 代わりに Security Admin を使用してください。 | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Security Reader](#security-reader) | Security Center の表示アクセス許可。 推奨事項、警告、セキュリティ ポリシー、セキュリティの状態を閲覧できますが、変更することはできません。 | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **DevOps** |  |  |
> | [DevTest Labs User](#devtest-labs-user) | Azure DevTest Labs で仮想マシンの接続、起動、再起動、シャットダウンができます。 | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [Lab Creator](#lab-creator) | Azure ラボ アカウントに新しいラボを作成できます。 | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **監視** |  |  |
> | [Application Insights Component Contributor](#application-insights-component-contributor) | Application Insights コンポーネントを管理できます | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Application Insights Snapshot Debugger](#application-insights-snapshot-debugger) | Application Insights スナップショット デバッガーで収集されたデバック スナップショットの表示とダウンロードを実行できるアクセス許可をユーザーに与えます。 これらのアクセス許可は、[所有者](#owner)ロールまたは[共同作成者](#contributor)ロールには含まれないことに注意してください。 ユーザーに Application Insights スナップショット デバッガー ロールを与える場合は、そのロールをユーザーに直接付与する必要があります。 このロールは、カスタム ロールに追加されるときに認識されません。 | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Monitoring Contributor](#monitoring-contributor) | すべての監視データを読み取り、監視設定を編集できます。 [「Azure Monitor での役割、アクセス許可、およびセキュリティの概要」](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles)も参照してください。 | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [監視メトリック パブリッシャー](#monitoring-metrics-publisher) | Azure リソースに対するメトリックの公開を有効にします | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Monitoring Reader](#monitoring-reader) | すべての監視データ (メトリック、ログなど) を読み取ることができます。 [「Azure Monitor での役割、アクセス許可、およびセキュリティの概要」](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles)も参照してください。 | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Workbook Contributor](#workbook-contributor) | 共有ブックを保存できます。 | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [Workbook Reader](#workbook-reader) | ブックの読み取りが可能です。 | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | **管理 + ガバナンス** |  |  |
> | [Automation Job Operator](#automation-job-operator) | Automation Runbook を使用してジョブを作成および管理します。 | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Automation Operator](#automation-operator) | Automation オペレーターはジョブを開始、停止、中断、再開することができます | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Automation Runbook Operator](#automation-runbook-operator) | Runbook のジョブを作成する方法については、Runbook のプロパティを参照してください。 | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Azure Connected Machine のオンボード](#azure-connected-machine-onboarding) | Azure Connected Machine をオンボードできます。 | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Azure Connected Machine のリソース管理者](#azure-connected-machine-resource-administrator) | Azure Connected Machine の読み取り、書き込み、削除、再オンボードを実行できます。 | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [Billing Reader](#billing-reader) | 課金データへの読み取りアクセスを許可します | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [ブループリント共同作成者](#blueprint-contributor) | ブループリントの定義を管理できますが、それらを割り当てることはできません。 | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [ブループリント オペレーター](#blueprint-operator) | 既存の発行済みのブループリントを割り当てることはできますが、ブループリントの新規作成はできません。 これは、ユーザーが割り当てたマネージド ID を使用して割り当てが行われた場合にのみ機能することに注意してください。 | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Cost Management 共同作成者](#cost-management-contributor) | コストを表示し、コストの構成 (予算、エクスポートなど) を管理することができます。 | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Cost Management 閲覧者](#cost-management-reader) | コストのデータと構成 (予算、エクスポートなど) を表示することができます。 | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [Hierarchy Settings Administrator](#hierarchy-settings-administrator) | ユーザーに、階層設定の編集と削除を許可します | 350f8d15-c687-4448-8ae1-157740a3936d |
> | [Kubernetes クラスター - Azure Arc のオンボード](#kubernetes-cluster---azure-arc-onboarding) | connectedClusters リソースを作成するため、あらゆるユーザーまたはサービスを承認するロール定義 | 34e09817-6cbe-4d01-b1a2-e0eac5743d41 |
> | [Managed Application Contributor Role](#managed-application-contributor-role) | マネージド アプリケーション リソースの作成を許可します。 | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Managed Application Operator Role](#managed-application-operator-role) | マネージド アプリケーション リソースに対する読み取りとアクションの実行が可能です。 | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Managed Applications 閲覧者](#managed-applications-reader) | マネージド アプリおよび要求 JIT アクセスでリソースを読み取ることができます。 | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [マネージド サービスの登録割り当て削除ロール](#managed-services-registration-assignment-delete-role) | マネージド サービスの登録割り当て削除ロールを使用すると、テナント管理ユーザーは、テナントに割り当てられている登録割り当てを削除できます。 | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [管理グループ共同作成者](#management-group-contributor) | 管理グループ共同作成者ロール | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [管理グループ閲覧者](#management-group-reader) | 管理グループ閲覧者ロール | ac63b705-f282-497d-ac71-919bf39d939d |
> | [New Relic APM Account Contributor](#new-relic-apm-account-contributor) | New Relic Application Performance Management のアカウントとアプリケーションを管理できます。ただし、それらへのアクセスは含まれません。 | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Policy Insights データ ライター (プレビュー)](#policy-insights-data-writer-preview) | リソース ポリシーに対する読み取りアクセスとリソース コンポーネント ポリシー イベントへの書き込みアクセスを許可します。 | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Reservation Purchaser](#reservation-purchaser) | 予約を購入できるようになります | f7b75c60-3036-4b75-91c3-6b41c27c1689 |
> | [リソース ポリシーの共同作成者](#resource-policy-contributor) | リソース ポリシーの作成または変更、サポート チケットの作成、リソースまたは階層の読み取りを行う権限を持つユーザー。 | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Site Recovery Contributor](#site-recovery-contributor) | 資格情報コンテナーの作成とロールの割り当てを除く、Site Recovery サービスを管理できます | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Site Recovery Operator](#site-recovery-operator) | フェールオーバーとフェールバックを実行できますが、その他の Site Recovery 管理操作は実行しません | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Site Recovery Reader](#site-recovery-reader) | Site Recovery の状態を表示できますが、その他の管理操作は実行できません | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Support Request Contributor](#support-request-contributor) | Support request を作成して管理できます | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [タグ共同作成者](#tag-contributor) | エンティティ自体へのアクセスを提供することなく、エンティティのタグを管理できます。 | 4a9ae827-6dc8-4573-8ac7-8239d42aa03f |
> | **その他** |  |  |
> | [Azure Digital Twins データ所有者](#azure-digital-twins-data-owner) | Digital Twins データプレーンのフル アクセス ロール | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
> | [Azure Digital Twins データ リーダー](#azure-digital-twins-data-reader) | Digital Twins データプレーン プロパティの読み取り専用ロール | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |
> | [BizTalk Contributor](#biztalk-contributor) | BizTalk Services を管理できます。ただし、それらへのアクセスは含まれません。 | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Desktop Virtualization Application Group Contributor](#desktop-virtualization-application-group-contributor) | デスクトップ仮想化アプリケーション グループの共同作成者。 | 86240b0e-9422-4c43-887b-b61143f32ba8 |
> | [Desktop Virtualization Application Group Reader](#desktop-virtualization-application-group-reader) | デスクトップ仮想化アプリケーション グループの閲覧者。 | aebf23d0-b568-4e86-b8f9-fe83a2c6ab55 |
> | [デスクトップ仮想化共同作成者](#desktop-virtualization-contributor) | デスクトップ仮想化の共同作成者。 | 082f0a83-3be5-4ba1-904c-961cca79b387 |
> | [Desktop Virtualization Host Pool Contributor](#desktop-virtualization-host-pool-contributor) | デスクトップ仮想化ホスト プールの共同作成者。 | e307426c-f9b6-4e81-87de-d99efb3c32bc |
> | [Desktop Virtualization Host Pool Reader](#desktop-virtualization-host-pool-reader) | デスクトップ仮想化ホスト プールの閲覧者。 | ceadfde2-b300-400a-ab7b-6143895aa822 |
> | [デスクトップ仮想化閲覧者](#desktop-virtualization-reader) | デスクトップ仮想化の閲覧者。 | 49a72310-ab8d-41df-bbb0-79b649203868 |
> | [Desktop Virtualization Session Host Operator](#desktop-virtualization-session-host-operator) | デスクトップ仮想化セッション ホストのオペレーター。 | 2ad6aaab-ead9-4eaa-8ac5-da422f562408 |
> | [デスクトップ仮想化ユーザー](#desktop-virtualization-user) | ユーザーにアプリケーション グループ内のアプリケーションを使用することを許可します。 | 1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63 |
> | [Desktop Virtualization User Session Operator](#desktop-virtualization-user-session-operator) | デスクトップ仮想化のユーザー セッションのオペレーター。 | ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6 |
> | [Desktop Virtualization Workspace Contributor](#desktop-virtualization-workspace-contributor) | デスクトップ仮想化ワークスペースの共同作成者。 | 21efdde3-836f-432b-bf3d-3e8e734d4b2b |
> | [Desktop Virtualization Workspace Reader](#desktop-virtualization-workspace-reader) | デスクトップ仮想化ワークスペースの閲覧者。 | 0fa44ee9-7a7d-466b-9bb2-2bf446b1204d |
> | [Disk Backup Reader](#disk-backup-reader) | ディスク バックアップを実行するためにコンテナーをバックアップするアクセス許可を提供します。 | 3e5e47e6-65f7-47ef-90b5-e5dd4d455f24 |
> | [Disk Restore Operator](#disk-restore-operator) | ディスクの復元を実行するためにコンテナーをバックアップするアクセス許可を提供します。 | b50d9833-a0cb-478e-945f-707fcc997c13 |
> | [Disk Snapshot Contributor](#disk-snapshot-contributor) | ディスプのスナップショットを管理するためにコンテナーをバックアップするアクセス許可を提供します。 | 7efff54f-a5b4-42b5-a1c5-5411624893ce |
> | [Scheduler Job Collections Contributor](#scheduler-job-collections-contributor) | スケジューラ ジョブ コレクションを管理できます。ただし、それらへのアクセスは含まれません。 | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | [Services Hub Operator](#services-hub-operator) | Services Hub Operator を使用すると、サービス ハブ コネクタに関連するすべての読み取り、書き込み、削除の操作を実行できます。 | 82200a5b-e217-47a5-b665-6d8765ee745b |


## <a name="general"></a>全般


### <a name="contributor"></a>Contributor

すべてのリソースを管理するためのフル アクセスが付与されますが、Azure RBAC でロールを割り当てたり、Azure Blueprints で割り当てを管理したり、イメージ ギャラリーを共有したりすることはできません。 [詳細情報](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | * | あらゆる種類のリソースの作成と管理 |
> | **NotActions** |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/Delete | ロール、ポリシーの割り当て、ポリシーの定義、ポリシー セットの定義を削除します。 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/Write | ロール、ロールの割り当て、ポリシーの割り当て、ポリシーの定義、ポリシー セットの定義を作成します。 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/elevateAccess/Action | テナント スコープで、ユーザー アクセス管理者のアクセス権を呼び出し元に付与します。 |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/write | 任意のブループリント割り当てを作成または更新します |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/delete | 任意のブループリント割り当てを削除します |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/galleries/share/action | ギャラリーを別のスコープに共有します |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, but does not allow you to assign roles in Azure RBAC, manage assignments in Azure Blueprints, or share image galleries.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete",
        "Microsoft.Compute/galleries/share/action"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="owner"></a>所有者

Azure RBAC でロールを割り当てる権限を含め、すべてのリソースを管理するためのフル アクセスを付与します。 [詳細情報](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | * | あらゆる種類のリソースの作成と管理 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, including the ability to assign roles in Azure RBAC.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader"></a>Reader

すべてのリソースを表示しますが、変更を加えることはできません。 [詳細情報](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | */read | 機密データを除くあらゆる種類のリソースの読み取り |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View all resources, but does not allow you to make any changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "permissions": [
    {
      "actions": [
        "*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="user-access-administrator"></a>User Access Administrator

Azure リソースに対するユーザー アクセスを管理します。 [詳細情報](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | */read | 機密データを除くあらゆる種類のリソースの読み取り |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/* | 承認の管理 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage user access to Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "User Access Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="compute"></a>Compute


### <a name="classic-virtual-machine-contributor"></a>Classic Virtual Machine Contributor

従来の仮想マシンを管理できますが、アクセスすることはできません。また、接続先の仮想ネットワークやストレージ アカウントにもアクセスできません。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/domainNames/* | 従来のコンピューティング ドメイン名の作成と管理 |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/* | 仮想マシンの作成と管理 |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/networkSecurityGroups/join/action |  |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/link/action | 予約済み IP をリンクします。 |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/read | 予約済み IP を取得します。 |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/join/action | 仮想ネットワークに参加します。 |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/read | 仮想ネットワークを取得します。 |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/disks/read | ストレージ アカウント ディスクを返します。 |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/images/read | ストレージ アカウント イメージを返します。 (非推奨になりました。 'Microsoft.ClassicStorage/storageAccounts/vmImages' を使用してください。) |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | ストレージ アカウントのアクセス キーを一覧表示します。 |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/read | 特定のアカウントのストレージ アカウントを返します。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "name": "d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/domainNames/*",
        "Microsoft.ClassicCompute/virtualMachines/*",
        "Microsoft.ClassicNetwork/networkSecurityGroups/join/action",
        "Microsoft.ClassicNetwork/reservedIps/link/action",
        "Microsoft.ClassicNetwork/reservedIps/read",
        "Microsoft.ClassicNetwork/virtualNetworks/join/action",
        "Microsoft.ClassicNetwork/virtualNetworks/read",
        "Microsoft.ClassicStorage/storageAccounts/disks/read",
        "Microsoft.ClassicStorage/storageAccounts/images/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-administrator-login"></a>Virtual Machine Administrator Login

ポータルで仮想マシンを表示し、管理者としてログインします。[詳細](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | パブリック IP アドレス定義を取得します。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | ロード バランサー定義を取得します。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | ネットワーク インターフェイスの定義を取得します。  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/action | 仮想マシンに通常のユーザーとしてログインします。 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/loginAsAdmin/action | Windows 管理者または Linux のルート ユーザーの権限で仮想マシンにログインします。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as administrator",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "name": "1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action",
        "Microsoft.Compute/virtualMachines/loginAsAdmin/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Administrator Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-contributor"></a>Virtual Machine Contributor

仮想マシンを管理できますが、アクセスすることはできません。また、接続先の仮想ネットワークやストレージ アカウントにもアクセスできません。 [詳細情報](../virtual-machines/linux/tutorial-govern-resources.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* | コンピューティング可用性セットの作成と管理 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/locations/* | コンピューティングの場所の作成と管理 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* | 仮想マシンの作成、更新、削除、起動、再起動、電源オフを含む、すべての仮想マシン操作を実行します。 仮想マシンで定義済みのスクリプトを実行します。 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachineScaleSets/* | 仮想マシン スケールセットの作成と管理 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/write | 新しいディスクを作成するか、既存のディスクを更新します。 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/read | ディスクのプロパティを取得します。 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/delete | ディスクを削除します。 |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/schedules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/applicationGateways/backendAddressPools/join/action | アプリケーション ゲートウェイのバックエンド アドレス プールを接続します。 警告不可能です。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/join/action | ロード バランサーのバックエンド アドレス プールを接続します。 警告不可能です。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatPools/join/action | ロード バランサーの受信 NAT プールを接続します。 警告不可能です。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/join/action | ロード バランサーのインバウンド NAT 規則を接続します。 警告不可能です。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/probes/join/action | ロード バランサーのプローブの使用を許可します。 たとえば、このアクセス許可では、VM スケール セットの healthProbe プロパティでプローブを参照できます。 警告不可能です。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | ロード バランサー定義を取得します。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/locations/* | ネットワークの場所の作成と管理 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* | ネットワーク インターフェイスの作成と管理 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | ネットワーク セキュリティ グループに参加します。 警告不可能です。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/read | ネットワーク セキュリティ グループの定義を取得します。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/join/action | パブリック IP アドレスに接続します。 警告不可能です。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | パブリック IP アドレス定義を取得します。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | 仮想ネットワークに参加します。 警告不可能です。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/write | バックアップの保護インテントを作成します |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | 保護された項目のオブジェクトの詳細を返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/write | バックアップ保護項目を作成します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | すべての保護ポリシーを返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/write | 保護ポリシーを作成します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細を返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/write | "コンテナーの作成" 操作では、"コンテナー" 型の Azure リソースを作成します。 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.SqlVirtualMachine](resource-provider-operations.md#microsoftsqlvirtualmachine)/* |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | 指定されたストレージ アカウントのアクセス キーを返します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/locations/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/virtualMachineScaleSets/*",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/delete",
        "Microsoft.DevTestLab/schedules/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/loadBalancers/probes/join/action",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/locations/*",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Network/networkSecurityGroups/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/write",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.SqlVirtualMachine/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-user-login"></a>Virtual Machine User Login

ポータルで仮想マシンを表示し、通常のユーザーとしてログインします。 [詳細情報](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | パブリック IP アドレス定義を取得します。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | ロード バランサー定義を取得します。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | ネットワーク インターフェイスの定義を取得します。  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/action | 仮想マシンに通常のユーザーとしてログインします。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as a regular user.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb879df8-f326-4884-b1cf-06f3ad86be52",
  "name": "fb879df8-f326-4884-b1cf-06f3ad86be52",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine User Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="networking"></a>ネットワーク


### <a name="cdn-endpoint-contributor"></a>CDN Endpoint Contributor

CDN エンドポイントを管理できますが、アクセス権を他のユーザーに付与することはできません。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/endpoints/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "name": "426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-endpoint-reader"></a>CDN Endpoint Reader

CDN エンドポイントを表示できますが、変更はできません。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/endpoints/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "name": "871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-contributor"></a>CDN Profile Contributor

CDN プロファイルとそのエンドポイントを管理できますが、アクセス権を他のユーザーに付与することはできません。 [詳細情報](../cdn/cdn-app-dev-net.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN profiles and their endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "name": "ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-reader"></a>CDN Profile Reader

CDN プロファイルとそのエンドポイントを表示できますが、変更はできません。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN profiles and their endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f96442b-4075-438f-813d-ad51ab4019af",
  "name": "8f96442b-4075-438f-813d-ad51ab4019af",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-network-contributor"></a>Classic Network Contributor

従来のネットワークを管理できます。ただし、それらへのアクセスは含まれません。 [詳細情報](../virtual-network/virtual-network-manage-peering.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/* | 従来のネットワークの作成と管理 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "name": "b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicNetwork/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="dns-zone-contributor"></a>DNS Zone Contributor

Azure DNS の DNS ゾーンとレコード セットを管理できますが、それにアクセスできるユーザーを制御することはできません。 [詳細情報](../dns/dns-protect-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/dnsZones/* | DNS ゾーンとレコードの作成と管理 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DNS zones and record sets in Azure DNS, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/befefa01-2a29-4197-83a8-272ff33ce314",
  "name": "befefa01-2a29-4197-83a8-272ff33ce314",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/dnsZones/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="network-contributor"></a>Network Contributor

ネットワークを管理できます。ただし、それらへのアクセスは含まれません。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/* | ネットワークの作成と管理 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7",
  "name": "4d97b98b-1d4f-4787-a291-c67834d212e7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="private-dns-zone-contributor"></a>プライベート DNS ゾーンの共同作成者

プライベート DNS ゾーンのリソースを管理できますが、リンク先の仮想ネットワークを管理することはできません。 [詳細情報](../dns/dns-protect-private-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsZones/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationResults/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationStatuses/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/join/action | 仮想ネットワークに参加します。 警告不可能です。 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage private DNS zone resources, but not the virtual networks they are linked to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "name": "b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/privateDnsZones/*",
        "Microsoft.Network/privateDnsOperationResults/*",
        "Microsoft.Network/privateDnsOperationStatuses/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/join/action",
        "Microsoft.Authorization/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Private DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="traffic-manager-contributor"></a>Traffic Manager Contributor

Traffic Manager プロファイルを管理できますが、それにアクセスできるユーザーを制御することはできません。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/trafficManagerProfiles/* |  |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Traffic Manager profiles, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "name": "a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/trafficManagerProfiles/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Traffic Manager Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="storage"></a>ストレージ


### <a name="avere-contributor"></a>Avere 共同作成者

Avere vFXT クラスターを作成および管理できます。 [詳細情報](../avere-vfxt/avere-vfxt-deploy-plan.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/*/read |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/proximityPlacementGroups/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/*/read |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/read | 仮想ネットワーク サブネットの定義を取得します。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | 仮想ネットワークに参加します。 警告不可能です。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | ストレージ アカウントや SQL Database などのリソースをサブネットに結合します。 警告不可能です。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | ネットワーク セキュリティ グループに参加します。 警告不可能です。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/*/read |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | ストレージ アカウントの作成と管理 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/resources/read | リソース グループのリソースを取得します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | BLOB を削除した結果を返します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | BLOB または BLOB の一覧を返します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | BLOB の書き込みの結果を返します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can create and manage an Avere vFXT cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "name": "4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/proximityPlacementGroups/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/disks/*",
        "Microsoft.Network/*/read",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/*/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="avere-operator"></a>Avere オペレーター

クラスターを管理するために Avere vFXT クラスターによって使用されます。[詳細](../avere-vfxt/avere-vfxt-manage-cluster.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/read | 仮想マシンのプロパティを取得します。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | ネットワーク インターフェイスの定義を取得します。  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/write | ネットワーク インターフェイスを作成するか、既存のネットワーク インターフェイスを更新します。  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/read | 仮想ネットワーク サブネットの定義を取得します。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | 仮想ネットワークに参加します。 警告不可能です。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | ネットワーク セキュリティ グループに参加します。 警告不可能です。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/delete | コンテナーを削除した結果を返します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | コンテナーの一覧を返します |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/write | BLOB コンテナーのプット結果を返します |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | BLOB を削除した結果を返します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | BLOB または BLOB の一覧を返します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | BLOB の書き込みの結果を返します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Used by the Avere vFXT cluster to manage the cluster",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "name": "c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-contributor"></a>Backup Contributor

バックアップ サービスを管理できますが、資格情報コンテナーの作成や他のユーザーに対するアクセス権の付与を行うことはできません。[詳細](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/* | バックアップ管理操作の結果の管理 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/* | Recovery Services コンテナーのバックアップ ファブリック内でのバックアップ コンテナーの作成および管理 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/action | コンテナーの一覧を更新します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | バックアップ ジョブの作成および管理 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | ジョブをエクスポートします。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | バックアップ管理操作の結果の作成および管理 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/* | バックアップ ポリシーの作成および管理 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | バックアップできるアイテムの作成および管理 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/* | バックアップ アイテムの作成および管理 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/* | バックアップ アイテムを保持するコンテナーの作成および管理 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupSecurityPIN/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | Recovery Services の保護された項目と保護されたサーバーの概要を返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/* | Recovery Services コンテナー内のバックアップに関連する証明書の作成および管理 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | コンテナーに関連する拡張情報の作成および管理 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Recovery Services コンテナーのアラートを取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | 登録済み ID の管理 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/* | Recovery Services コンテナーの使用状況の作成および管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/action | 保護された項目に対する操作を検証します |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/write | "コンテナーの作成" 操作では、"コンテナー" 型の Azure リソースを作成します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | Recovery Services コンテナーに対するバックアップ操作の状態を返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | コンテナーに登録されているすべてのバックアップ管理サーバーを返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/read | すべての保護可能なコンテナーを取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | Recovery Services コンテナーのバックアップの状態を確認します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/action |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | 機能を検証します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | アラートを解決する。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | リソース プロバイダーの操作の一覧を返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | 特定の操作の操作の状態を取得します |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | すべてのバックアップ保護の意図を一覧表示します |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup service,but can't create vaults and give access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e467623-bb1f-42f4-a55d-6e525e11384b",
  "name": "5e467623-bb1f-42f4-a55d-6e525e11384b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupSecurityPIN/*",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/*",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/Vaults/usages/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-operator"></a>Backup Operator

バックアップ サービスを管理できます (バックアップの削除、資格情報コンテナーの作成、他のユーザーに対するアクセス権の付与を除きます)。[詳細](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/read | 操作の状態を返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/read | 保護コンテナーに対して実行された操作の結果を取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | 保護された項目のバックアップを実行します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | 保護された項目に対して実行された操作の結果を取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | 保護された項目に対して実行された操作の状態を返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | 保護された項目のオブジェクトの詳細を返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | 保護された項目のインスタント項目回復をプロビジョニングします。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/accessToken/action | リージョンをまたがる復元の AccessToken を取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | 保護された項目の復旧ポイントを取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | 保護された項目の復旧ポイントを復元します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | 保護された項目のインスタント項目回復を取り消します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/write | バックアップ保護項目を作成します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/read | すべての登録済みコンテナーを返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/action | コンテナーの一覧を更新します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | バックアップ ジョブの作成および管理 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | ジョブをエクスポートします。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | バックアップ管理操作の結果の作成および管理 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/read | ポリシー操作の結果を取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | すべての保護ポリシーを返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | バックアップできるアイテムの作成および管理 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/read | すべての保護された項目の一覧を返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/read | サブスクリプションに属するすべてのコンテナーを返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | Recovery Services の保護された項目と保護されたサーバーの概要を返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/write | "リソース証明書を更新" 操作では、リソース/コンテナー資格情報証明書を更新します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/write | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Recovery Services コンテナーのアラートを取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | "操作結果を取得" 操作を使用すると、非同期で送信された操作の状態と結果を取得できます。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | " コンテナーを取得" 操作を使用すると、リソースの登録済みコンテナーを取得できます。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/write | "サービス コンテナーを登録" 操作を使用すると、コンテナーを Recovery Services に登録できます。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細を返します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/action | 保護された項目に対する操作を検証します |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | Recovery Services コンテナーに対するバックアップ操作の状態を返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operations/read | ポリシー操作の状態を取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/write | 登録済みコンテナーを作成します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/inquire/action | コンテナー内のワークロードを照会します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | コンテナーに登録されているすべてのバックアップ管理サーバーを返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/write | バックアップの保護インテントを作成します |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/read | バックアップ保護の意図を取得します |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/read | すべての保護可能なコンテナーを取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/items/read | コンテナー内のすべての項目を取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | Recovery Services コンテナーのバックアップの状態を確認します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/action |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | 機能を検証します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupAadProperties/read | リージョンをまたがる復元の 3 番目のリージョンにおける認証用 AAD プロパティを取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrJobs/action | Recovery Services コンテナーのセカンダリ リージョンにおけるリージョンをまたがる復元ジョブを一覧表示します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrJob/action | Recovery Services コンテナーのセカンダリ リージョンにおけるリージョンをまたがる復元ジョブの詳細を取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrossRegionRestore/action | リージョンをまたがる復元をトリガーします。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrOperationResults/read | Recovery Services コンテナーに対する CRR 操作の結果を返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrOperationsStatus/read | Recovery Services コンテナーに対する CRR 操作の状態を返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | アラートを解決する。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | リソース プロバイダーの操作の一覧を返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | 特定の操作の操作の状態を取得します |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | すべてのバックアップ保護の意図を一覧表示します |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup services, except removal of backup, vault creation and giving access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00c29273-979b-4161-815c-10b084fb9324",
  "name": "00c29273-979b-4161-815c-10b084fb9324",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action",
        "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/accessToken/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/write",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/write",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/locations/backupAadProperties/read",
        "Microsoft.RecoveryServices/locations/backupCrrJobs/action",
        "Microsoft.RecoveryServices/locations/backupCrrJob/action",
        "Microsoft.RecoveryServices/locations/backupCrossRegionRestore/action",
        "Microsoft.RecoveryServices/locations/backupCrrOperationResults/read",
        "Microsoft.RecoveryServices/locations/backupCrrOperationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-reader"></a>Backup Reader

バックアップ サービスを表示できますが、変更を行うことはできません。[詳細](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp は、サービスによって使用される内部操作です。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/read | 操作の状態を返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/read | 保護コンテナーに対して実行された操作の結果を取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | 保護された項目に対して実行された操作の結果を取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | 保護された項目に対して実行された操作の状態を返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | 保護された項目のオブジェクトの詳細を返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | 保護された項目の復旧ポイントを取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/read | すべての登録済みコンテナーを返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/operationResults/read | ジョブ操作の結果を返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/read | すべてのジョブ オブジェクトを返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | ジョブをエクスポートします。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/read | Recovery Services コンテナーに対するバックアップ操作の結果を返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/read | ポリシー操作の結果を取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | すべての保護ポリシーを返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/read | すべての保護された項目の一覧を返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/read | サブスクリプションに属するすべてのコンテナーを返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | Recovery Services の保護された項目と保護されたサーバーの概要を返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Recovery Services コンテナーのアラートを取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | "操作結果を取得" 操作を使用すると、非同期で送信された操作の状態と結果を取得できます。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | " コンテナーを取得" 操作を使用すると、リソースの登録済みコンテナーを取得できます。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/read | Recovery Services コンテナーのストレージ構成を返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/read | Recovery Services コンテナーの構成を返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | Recovery Services コンテナーに対するバックアップ操作の状態を返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operations/read | ポリシー操作の状態を取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | コンテナーに登録されているすべてのバックアップ管理サーバーを返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/read | バックアップ保護の意図を取得します |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/items/read | コンテナー内のすべての項目を取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | Recovery Services コンテナーのバックアップの状態を確認します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | アラートを解決する。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | リソース プロバイダーの操作の一覧を返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | 特定の操作の操作の状態を取得します |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | すべてのバックアップ保護の意図を一覧表示します |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細を返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | 機能を検証します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view backup services, but can't make changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "name": "a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/read",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-contributor"></a>Classic Storage Account Contributor

従来のストレージ アカウントを管理できますが、アクセスすることはできません。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/* | ストレージ アカウントの作成と管理 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic storage accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "name": "86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-key-operator-service-role"></a>従来のストレージ アカウント キー オペレーターのサービス ロール

従来のストレージ アカウント キー オペレーターは、従来のストレージ アカウントでのキーの一覧表示と再生成を行うことができます。[詳細](../key-vault/secrets/overview-storage-keys.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listkeys/action | ストレージ アカウントのアクセス キーを一覧表示します。 |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/regeneratekey/action | ストレージ アカウントの既存のアクセス キーを再生成します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Classic Storage Account Key Operators are allowed to list and regenerate keys on Classic Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "name": "985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ClassicStorage/storageAccounts/listkeys/action",
        "Microsoft.ClassicStorage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-contributor"></a>Data Box Contributor

Data Box サービスですべてを管理できます (他のユーザーに対するアクセス権の付与を除く)。 [詳細情報](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/* |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything under Data Box Service except giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/add466c9-e687-43fc-8d98-dfcf8d720be5",
  "name": "add466c9-e687-43fc-8d98-dfcf8d720be5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Databox/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-reader"></a>Data Box 閲覧者

Data Box サービスを管理できます (注文の作成または注文の詳細の編集、および他のユーザーに対するアクセス権の付与を除く)。 [詳細情報](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/*/read |  |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/jobs/listsecrets/action |  |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/jobs/listcredentials/action | 注文に関連する暗号化されていない資格情報を一覧表示します。 |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/availableSkus/action | このメソッドは、使用可能な SKU の一覧を返します。 |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/validateInputs/action | このメソッドでは、すべての種類の検証が行われます。 |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/regionConfiguration/action | このメソッドでは、リージョンの構成が返されます。 |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/validateAddress/action | 配送先住所を検証し、存在する場合には別の住所を指定します。 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Data Box Service except creating order or editing order details and giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "name": "028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Databox/*/read",
        "Microsoft.Databox/jobs/listsecrets/action",
        "Microsoft.Databox/jobs/listcredentials/action",
        "Microsoft.Databox/locations/availableSkus/action",
        "Microsoft.Databox/locations/validateInputs/action",
        "Microsoft.Databox/locations/regionConfiguration/action",
        "Microsoft.Databox/locations/validateAddress/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-lake-analytics-developer"></a>Data Lake Analytics Developer

独自のジョブを送信、監視、管理できますが、Data Lake Analytics アカウントを作成または削除することはできません。 [詳細情報](../data-lake-analytics/data-lake-analytics-manage-use-portal.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.BigAnalytics/accounts/* |  |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Delete | DataLakeAnalytics アカウントを削除します。 |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/TakeOwnership/action | 他のユーザーによって送信されたジョブを取り消すアクセス許可を付与します。 |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Write | DataLakeAnalytics アカウントを作成または更新します。 |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Write | DataLakeAnalytics アカウントのリンクされた DataLakeStore アカウントを作成または更新します。 |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Delete | DataLakeAnalytics アカウントから DataLakeStore アカウントのリンクを解除します。 |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Write | DataLakeAnalytics アカウントのリンクされたストレージ アカウントを作成または更新します。 |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Delete | DataLakeAnalytics アカウントからストレージ アカウントをリンク解除します。 |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Write | ファイアウォール規則を作成または更新します。 |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Delete | ファイアウォール規則を削除します。 |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Write | コンピューティング ポリシーを作成または更新します。 |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Delete | コンピューティング ポリシーを削除します。 |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you submit, monitor, and manage your own jobs but not create or delete Data Lake Analytics accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/47b7735b-770e-4598-a7da-8b91488b4c88",
  "name": "47b7735b-770e-4598-a7da-8b91488b4c88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BigAnalytics/accounts/*",
        "Microsoft.DataLakeAnalytics/accounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.BigAnalytics/accounts/Delete",
        "Microsoft.BigAnalytics/accounts/TakeOwnership/action",
        "Microsoft.BigAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action",
        "Microsoft.DataLakeAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Write",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Write",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Lake Analytics Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader-and-data-access"></a>Reader and Data Access

すべてを表示することができますが、ストレージ アカウントや含まれるリソースの削除や作成はできません。 ストレージ アカウント キーへのアクセスを使用して、ストレージ アカウントに含まれるすべてのデータへの読み取り/書き込みアクセスも許可されます。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | 指定されたストレージ アカウントのアクセス キーを返します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/ListAccountSas/action | 指定されたストレージ アカウントのアカウント SAS トークンを返します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything but will not let you delete or create a storage account or contained resource. It will also allow read/write access to all data contained in a storage account via access to storage account keys.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c12c1c16-33a1-487b-954d-41c89c60f349",
  "name": "c12c1c16-33a1-487b-954d-41c89c60f349",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/ListAccountSas/action",
        "Microsoft.Storage/storageAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader and Data Access",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-contributor"></a>Storage Account Contributor

ストレージ アカウントの管理を許可します。 アカウント キーへのアクセスを提供します。これを使用して、共有キー認証を使用してデータにアクセスすることができます。 [詳細情報](../storage/common/storage-auth-aad.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | 分析サーバーの診断の設定の作成、更新、または読み取りを行います |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | ストレージ アカウントや SQL Database などのリソースをサブネットに結合します。 警告不可能です。 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | ストレージ アカウントの作成と管理 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage storage accounts, including accessing storage account keys which provide full access to storage account data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "name": "17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-key-operator-service-role"></a>ストレージ アカウント キー オペレーターのサービス ロール

ストレージ アカウント アクセス キーを一覧表示および再生成できます。 [詳細情報](../storage/common/storage-account-keys-manage.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | 指定されたストレージ アカウントのアクセス キーを返します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/regeneratekey/action | 指定されたストレージ アカウントのアクセス キーを再生成します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Storage Account Key Operators are allowed to list and regenerate keys on Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/81a9662b-bebf-436f-a333-f67b29880f12",
  "name": "81a9662b-bebf-436f-a333-f67b29880f12",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-contributor"></a>ストレージ BLOB データ共同作成者

Azure Storage コンテナーと BLOB の読み取り、書き込み、削除を行います。 特定のデータ操作に必要なアクションについては、「[Permissions for calling blob and queue data operations (BLOB およびキューのデータの操作を呼び出すためのアクセス許可)](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」をご覧ください。 [詳細情報](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/delete | コンテナーを削除します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | コンテナーまたはコンテナーの一覧を返します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/write | コンテナーのメタデータまたはプロパティを変更します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Blob service 用のユーザー委任キーを返します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | BLOB を削除する |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | BLOB または BLOB の一覧を返します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | BLOB に書き込みます。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/move/action | パス間で BLOB を移動します |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/add/action | BLOB コンテンツを追加した結果を返します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write and delete access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "name": "ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-owner"></a>ストレージ BLOB データ所有者

Azure Storage Blob コンテナーとデータに対するフル アクセス (POSIX アクセスの制御の割り当てを含む) を提供します。 特定のデータ操作に必要なアクションについては、「[Permissions for calling blob and queue data operations (BLOB およびキューのデータの操作を呼び出すためのアクセス許可)](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」をご覧ください。 [詳細情報](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/* | コンテナーのフル アクセス許可。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Blob service 用のユーザー委任キーを返します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/* | BLOB のフル アクセス許可。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "name": "b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/*",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-reader"></a>ストレージ BLOB データ閲覧者

Azure Storage コンテナーと BLOB の読み取りと一覧表示を行います。 特定のデータ操作に必要なアクションについては、「[Permissions for calling blob and queue data operations (BLOB およびキューのデータの操作を呼び出すためのアクセス許可)](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」をご覧ください。 [詳細情報](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | コンテナーまたはコンテナーの一覧を返します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Blob service 用のユーザー委任キーを返します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | BLOB または BLOB の一覧を返します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-delegator"></a>Storage Blob デリゲータ

Azure AD 資格情報で署名されたコンテナーまたは BLOB 用の共有アクセス署名を作成するために使用できるユーザー委任キーを取得します。 詳細については、「[ユーザー委任 SAS を作成する](/rest/api/storageservices/create-user-delegation-sas)」を参照してください。 [詳細情報](/rest/api/storageservices/get-user-delegation-key)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Blob service 用のユーザー委任キーを返します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for generation of a user delegation key which can be used to sign SAS tokens",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "name": "db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Delegator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-contributor"></a>記憶域ファイル データの SMB 共有の共同作成者

Azure ファイル共有のファイルまたはディレクトリに対する読み取り、書き込み、削除のアクセス権を許可します。 このロールに相当する機能は Windows ファイル サーバーに組み込まれていません。 [詳細情報](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | ファイル/フォルダーまたはファイル/フォルダーの一覧を返します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/write | ファイルの書き込みまたはフォルダーの作成の結果を返します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/delete | ファイル/フォルダーの削除の結果を返します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "name": "0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-elevated-contributor"></a>記憶域ファイル データの SMB 共有の管理者特権共同作成者

Azure ファイル共有のファイルまたはディレクトリに対する ACL の読み取り、書き込み、削除、変更を許可します。 このロールは、Windows ファイル サーバーでのファイル共有 ACL の変更に相当します。 [詳細情報](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | ファイル/フォルダーまたはファイル/フォルダーの一覧を返します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/write | ファイルの書き込みまたはフォルダーの作成の結果を返します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/delete | ファイル/フォルダーの削除の結果を返します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/modifypermissions/action | ファイル/フォルダーに対するアクセス許可の変更の結果を返します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, delete and modify NTFS permission access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7264617-510b-434b-a828-9731dc254ea7",
  "name": "a7264617-510b-434b-a828-9731dc254ea7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Elevated Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-reader"></a>記憶域ファイル データの SMB 共有の閲覧者

Azure ファイル共有のファイルまたはディレクトリに対する読み取りアクセスを許可します。 このロールは、Windows ファイル サーバーでのファイル共有 ACL の読み取りに相当します。 [詳細情報](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | ファイル/フォルダーまたはファイル/フォルダーの一覧を返します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure File Share over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aba4ae5f-2193-4029-9191-0cb91df5e314",
  "name": "aba4ae5f-2193-4029-9191-0cb91df5e314",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-contributor"></a>ストレージ キュー データ共同作成者

Azure Storage キューおよびキュー メッセージの読み取り、書き込み、削除を行います。 特定のデータ操作に必要なアクションについては、「[Permissions for calling blob and queue data operations (BLOB およびキューのデータの操作を呼び出すためのアクセス許可)](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」をご覧ください。 [詳細情報](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/delete | キューを削除します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/read | キューまたはキューの一覧を返します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/write | キューのメタデータまたはプロパティを変更します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/delete | 1 つまたは複数のメッセージをキューから削除します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | 1 つまたは複数のメッセージをキューからピークまたは取得します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/write | メッセージをキューに追加します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/process/action | メッセージを処理した結果を返します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "name": "974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/write",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-processor"></a>ストレージ キュー データのメッセージ プロセッサ

Azure Storage キューからのメッセージのピーク、取得、削除を行います。 特定のデータ操作に必要なアクションについては、「[Permissions for calling blob and queue data operations (BLOB およびキューのデータの操作を呼び出すためのアクセス許可)](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」をご覧ください。 [詳細情報](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | メッセージをピークします。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/process/action | メッセージを取得および削除します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for peek, receive, and delete access to Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "name": "8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Processor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-sender"></a>ストレージ キュー データ メッセージ送信者

Azure Storage キューにメッセージを追加します。 特定のデータ操作に必要なアクションについては、「[Permissions for calling blob and queue data operations (BLOB およびキューのデータの操作を呼び出すためのアクセス許可)](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」をご覧ください。 [詳細情報](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/add/action | メッセージをキューに追加します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for sending of Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "name": "c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-reader"></a>ストレージ キュー データ閲覧者

Azure Storage キューおよびキュー メッセージの読み取りと一覧表示を行います。 特定のデータ操作に必要なアクションについては、「[Permissions for calling blob and queue data operations (BLOB およびキューのデータの操作を呼び出すためのアクセス許可)](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」をご覧ください。 [詳細情報](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/read | キューまたはキューの一覧を返します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | 1 つまたは複数のメッセージをキューからピークまたは取得します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/19e7f393-937e-4f77-808e-94535e297925",
  "name": "19e7f393-937e-4f77-808e-94535e297925",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="web"></a>Web


### <a name="azure-maps-data-contributor"></a>Azure Maps データ共同作成者

Azure Maps アカウントからのマップ関連データへの読み取り、書き込み、削除のアクセスを付与します。 [詳細情報](../azure-maps/azure-maps-authentication.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/read |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/write |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/delete |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read, write, and delete access to map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204",
  "name": "8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/*/read",
        "Microsoft.Maps/accounts/*/write",
        "Microsoft.Maps/accounts/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-maps-data-reader"></a>Azure Maps データ閲覧者

Azure Maps アカウントからマップ関連データを読み取るためのアクセス権を付与します。 [詳細情報](../azure-maps/azure-maps-authentication.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/read |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "name": "423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-service-contributor"></a>Search Service Contributor

Search サービスを管理できます。ただし、それらへのアクセスは含まれません。 [詳細情報](../search/search-security-rbac.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Search](resource-provider-operations.md#microsoftsearch)/searchServices/* | 検索サービスの作成と管理 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Search services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "name": "7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Search/searchServices/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Search Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-accesskey-reader"></a>SignalR AccessKey 閲覧者

SignalR サービス アクセス キーを読み取ります

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/*/read |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/listkeys/action | 管理ポータルで、または API を使用して SignalR のアクセス キーの値を表示します。 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read SignalR Service Access Keys",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/04165923-9d83-45d5-8227-78b77b0a687e",
  "name": "04165923-9d83-45d5-8227-78b77b0a687e",
  "permissions": [
    {
      "actions": [
        "Microsoft.SignalRService/*/read",
        "Microsoft.SignalRService/SignalR/listkeys/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR AccessKey Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-app-server-preview"></a>SignalR アプリ サーバー (プレビュー)

AAD の認証オプションを使用して、アプリ サーバーが SignalR Service にアクセスできるようにします。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/accessKey/action | ClientToken に署名するための一時的な AccessKey を生成します。 |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/serverConnection/write | サーバー接続を開始します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets your app server access SignalR Service with AAD auth options.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/420fcaa2-552c-430f-98ca-3264be4806c7",
  "name": "420fcaa2-552c-430f-98ca-3264be4806c7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/accessKey/action",
        "Microsoft.SignalRService/SignalR/serverConnection/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR App Server (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-contributor"></a>SignalR 共同作成者

SignalR のサービス リソースの作成、読み取り、更新、削除を行います

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/* |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete SignalR service resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761",
  "name": "8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761",
  "permissions": [
    {
      "actions": [
        "Microsoft.SignalRService/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-serverless-contributor-preview"></a>SignalR サーバーレス共同作成者 (プレビュー)

AAD の認証オプションを使用して、アプリがサーバーレス モードでサービスにアクセスできるようにします。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/clientToken/action | クライアント接続を開始するための ClientToken を生成します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets your app access service in serverless mode with AAD auth options.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fd53cd77-2268-407a-8f46-7e7863d0f521",
  "name": "fd53cd77-2268-407a-8f46-7e7863d0f521",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/clientToken/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Serverless Contributor (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-service-owner-preview"></a>SignalR Service 所有者 (プレビュー)

Azure SignalR Service REST API へのフル アクセス

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/hub/send/action | ハブ内のすべてのクライアント接続にメッセージをブロードキャストします。 |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/send/action | メッセージをグループにブロードキャストします。 |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/read | グループの存在、またはグループ内のユーザーの存在を確認します。 |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/write | グループに参加するか、グループから脱退します。 |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/send/action | クライアント接続にメッセージを直接送信します。 |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/read | クライアント接続の存在を確認します。 |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/write | クライアント接続を閉じます。 |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/send/action | 複数のクライアント接続で構成されている可能性のあるユーザーにメッセージを送信します。 |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/read | ユーザーの存在を確認します。 |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/write |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to Azure SignalR Service REST APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7e4f1700-ea5a-4f59-8f37-079cfe29dce3",
  "name": "7e4f1700-ea5a-4f59-8f37-079cfe29dce3",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/hub/send/action",
        "Microsoft.SignalRService/SignalR/group/send/action",
        "Microsoft.SignalRService/SignalR/group/read",
        "Microsoft.SignalRService/SignalR/group/write",
        "Microsoft.SignalRService/SignalR/clientConnection/send/action",
        "Microsoft.SignalRService/SignalR/clientConnection/read",
        "Microsoft.SignalRService/SignalR/clientConnection/write",
        "Microsoft.SignalRService/SignalR/user/send/action",
        "Microsoft.SignalRService/SignalR/user/read",
        "Microsoft.SignalRService/SignalR/user/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Service Owner (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-service-reader-preview"></a>SignalR Service 閲覧者 (プレビュー)

Azure SignalR Service REST API への読み取り専用アクセス

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/read | グループの存在、またはグループ内のユーザーの存在を確認します。 |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/read | クライアント接続の存在を確認します。 |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/read | ユーザーの存在を確認します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to Azure SignalR Service REST APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ddde6b66-c0df-4114-a159-3618637b3035",
  "name": "ddde6b66-c0df-4114-a159-3618637b3035",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/group/read",
        "Microsoft.SignalRService/SignalR/clientConnection/read",
        "Microsoft.SignalRService/SignalR/user/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Service Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="web-plan-contributor"></a>Web Plan Contributor

Web サイトの Web プランを管理できます。ただし、それらへのアクセスは含まれません。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/* | サーバー ファームの作成と管理 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/hostingEnvironments/Join/Action | App Service Environment に参加します |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the web plans for websites, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "name": "2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/serverFarms/*",
        "Microsoft.Web/hostingEnvironments/Join/Action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Web Plan Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="website-contributor"></a>Website Contributor

Web サイト (Web プランではない) を管理できます。ただし、それらへのアクセスは含まれません。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/* | Insights コンポーネントの作成と管理 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/certificates/* | Web サイト証明書の作成と管理 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/listSitesAssignedToHostName/read | ホスト名に割り当てられたサイトの名前を取得します。 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/join/action | App Service プランに参加します。 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | App Service プランのプロパティを取得します。 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/sites/* | Web サイトの作成と管理 (サイト作成では、関連付けられた App Service プランに対する書き込みアクセス許可も必要です) |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage websites (not web plans), but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/de139f84-1756-47ae-9be6-808fbbe84772",
  "name": "de139f84-1756-47ae-9be6-808fbbe84772",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/certificates/*",
        "Microsoft.Web/listSitesAssignedToHostName/read",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Website Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="containers"></a>Containers


### <a name="acrdelete"></a>AcrDelete

acr の削除 [詳細](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/artifacts/delete | コンテナー レジストリの成果物を削除します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr delete",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/artifacts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrDelete",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrimagesigner"></a>AcrImageSigner

acr イメージ署名者 [詳細](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/sign/write | コンテナー レジストリのコンテンツの信頼メタデータをプッシュ/プルします。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr image signer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6cef56e8-d556-48e5-a04f-b8e64114680f",
  "name": "6cef56e8-d556-48e5-a04f-b8e64114680f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/sign/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrImageSigner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpull"></a>AcrPull

acr のプル [詳細](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/read | コンテナー レジストリからイメージをプルまたは取得します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr pull",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "name": "7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPull",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpush"></a>AcrPush

acr のプッシュ [詳細](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/read | コンテナー レジストリからイメージをプルまたは取得します。 |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/push/write | コンテナー レジストリにイメージをプッシュするか書き込みます。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr push",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8311e382-0749-4cb8-b61a-304f252e45ec",
  "name": "8311e382-0749-4cb8-b61a-304f252e45ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read",
        "Microsoft.ContainerRegistry/registries/push/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPush",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinereader"></a>AcrQuarantineReader

ACR 検査データ閲覧者

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/read | コンテナー レジストリから検疫済みのイメージをプルまたは取得します |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cdda3590-29a3-44f6-95f2-9f980659eb04",
  "name": "cdda3590-29a3-44f6-95f2-9f980659eb04",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineReader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinewriter"></a>AcrQuarantineWriter

ACR 検査データ作成者

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/read | コンテナー レジストリから検疫済みのイメージをプルまたは取得します |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/write | 検疫済みイメージの検疫状態を書き込むか変更します |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data writer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "name": "c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read",
        "Microsoft.ContainerRegistry/registries/quarantine/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineWriter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Azure Kubernetes Service クラスター管理者ロール

クラスター管理者の資格情報アクションを一覧表示します。 [詳細情報](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterAdminCredential/action | 管理対象クラスターの clusterAdmin 資格情報を一覧表示します。 |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/accessProfiles/listCredential/action | 資格情報の一覧の取得を使用し、ロール名を指定してマネージド クラスターのアクセス プロファイルを取得します。 |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | マネージド クラスターを取得します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster admin credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "name": "0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action",
        "Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster Admin Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-user-role"></a>Azure Kubernetes Service クラスター ユーザー ロール

クラスター ユーザーの資格情報アクションを一覧表示します。 [詳細情報](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | 管理対象クラスターの clusterUser 資格情報を一覧表示します。 |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | マネージド クラスターを取得します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster user credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "name": "4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-contributor-role"></a>Azure Kubernetes Service 共同作成者ロール

Azure Kubernetes Service クラスターへの読み取りおよび書き込みアクセスを許可します [詳細](../aks/concepts-identity.md)

> [!div class="mx-tableFixed"]
> | Actions | 説明 |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | マネージド クラスターを取得します。 |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/write | 新しいマネージド クラスターを作成するか、既存のものを更新します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read and write Azure Kubernetes Service clusters",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "name": "ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/read",
        "Microsoft.ContainerService/managedClusters/write",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-admin"></a>Azure Kubernetes Service RBAC 管理者

リソース クォータと名前空間の更新または削除を除き、クラスターおよび名前空間のすべてのリソースを管理できます。 [詳細情報](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | デプロイを作成または更新します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | サブスクリプション操作の結果を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | サブスクリプションの一覧を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | 管理対象クラスターの clusterUser 資格情報を一覧表示します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/write | resourcequotas を書き込みます |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/delete | resourcequotas を削除します |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/write | namespaces を書き込みます |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/delete | 名前空間を削除します |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources under cluster/namespace, except update or delete resource quotas and namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3498e952-d568-435e-9b2c-8d77e338d7f7",
  "name": "3498e952-d568-435e-9b2c-8d77e338d7f7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": [
        "Microsoft.ContainerService/managedClusters/resourcequotas/write",
        "Microsoft.ContainerService/managedClusters/resourcequotas/delete",
        "Microsoft.ContainerService/managedClusters/namespaces/write",
        "Microsoft.ContainerService/managedClusters/namespaces/delete"
      ]
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-cluster-admin"></a>Azure Kubernetes Service RBAC クラスター管理者

クラスター内のすべてのリソースを管理できます。 [詳細情報](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | デプロイを作成または更新します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | サブスクリプション操作の結果を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | サブスクリプションの一覧を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | 管理対象クラスターの clusterUser 資格情報を一覧表示します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources in the cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "name": "b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Cluster Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-reader"></a>Azure Kubernetes Service RBAC 閲覧者

名前空間内のほとんどのオブジェクトを表示するための読み取り専用アクセスが許可されます。 ロールまたはロールのバインドを表示することはできません。 このロールでは、Secrets の表示は許可されません。これは、Secrets の内容を読み取ると、名前空間の ServiceAccount 資格情報にアクセスでき、それにより名前空間の任意の ServiceAccount として API にアクセスできるようになるためです (特権エスカレーションの形式)。 クラスター スコープでこのロールを適用すると、すべての名前空間へのアクセス権が付与されます。 [詳細情報](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | デプロイを作成または更新します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | サブスクリプション操作の結果を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | サブスクリプションの一覧を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/controllerrevisions/read | controllerrevisions を読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/daemonsets/read | デーモンセットを読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/deployments/read | デプロイを読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/replicasets/read | レプリカセットを読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/statefulsets/read | ステートフルセットを読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/autoscaling/horizontalpodautoscalers/read | horizontalpodautoscalers を読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/cronjobs/read | cronjobs を読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/jobs/read | ジョブを読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/read | configmaps を読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/endpoints/read | エンドポイントを読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events.k8s.io/events/read | イベントを読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events/read | イベントを読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/daemonsets/read | デーモンセットを読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/deployments/read | デプロイを読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/ingresses/read | イングレスを読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/networkpolicies/read | networkpolicies を読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/replicasets/read | レプリカセットを読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/read | limitranges を読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/read | 名前空間を読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/ingresses/read | イングレスを読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/networkpolicies/read | networkpolicies を読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/read | persistentvolumeclaims を読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/pods/read | ポッドを読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/policy/poddisruptionbudgets/read | poddisruptionbudgets を読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/read | replicationcontrollers を読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/read | replicationcontrollers を読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/read | resourcequotas を読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/serviceaccounts/read | serviceaccounts を読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/services/read | サービスを読み取ります |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read-only access to see most objects in a namespace. It does not allow viewing roles or role bindings. This role does not allow viewing Secrets, since reading the contents of Secrets enables access to ServiceAccount credentials in the namespace, which would allow API access as any ServiceAccount in the namespace (a form of privilege escalation). Applying this role at cluster scope will give access across all namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "name": "7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/apps/controllerrevisions/read",
        "Microsoft.ContainerService/managedClusters/apps/daemonsets/read",
        "Microsoft.ContainerService/managedClusters/apps/deployments/read",
        "Microsoft.ContainerService/managedClusters/apps/replicasets/read",
        "Microsoft.ContainerService/managedClusters/apps/statefulsets/read",
        "Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/read",
        "Microsoft.ContainerService/managedClusters/batch/cronjobs/read",
        "Microsoft.ContainerService/managedClusters/batch/jobs/read",
        "Microsoft.ContainerService/managedClusters/configmaps/read",
        "Microsoft.ContainerService/managedClusters/endpoints/read",
        "Microsoft.ContainerService/managedClusters/events.k8s.io/events/read",
        "Microsoft.ContainerService/managedClusters/events/read",
        "Microsoft.ContainerService/managedClusters/extensions/daemonsets/read",
        "Microsoft.ContainerService/managedClusters/extensions/deployments/read",
        "Microsoft.ContainerService/managedClusters/extensions/ingresses/read",
        "Microsoft.ContainerService/managedClusters/extensions/networkpolicies/read",
        "Microsoft.ContainerService/managedClusters/extensions/replicasets/read",
        "Microsoft.ContainerService/managedClusters/limitranges/read",
        "Microsoft.ContainerService/managedClusters/namespaces/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/read",
        "Microsoft.ContainerService/managedClusters/persistentvolumeclaims/read",
        "Microsoft.ContainerService/managedClusters/pods/read",
        "Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/read",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/read",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/read",
        "Microsoft.ContainerService/managedClusters/resourcequotas/read",
        "Microsoft.ContainerService/managedClusters/serviceaccounts/read",
        "Microsoft.ContainerService/managedClusters/services/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-writer"></a>Azure Kubernetes Service RBAC ライター

名前空間内のほとんどのオブジェクトに対する読み取り/書き込みアクセスが許可されます。このロールでは、ロールまたはロールのバインドの表示または変更が許可されません。 ただし、このロールを使用すると、Secrets にアクセスし、名前空間内の任意の ServiceAccount としてポッドを実行できるので、名前空間内の任意の ServiceAccount の API アクセス レベルを取得するために使用できます。 クラスター スコープでこのロールを適用すると、すべての名前空間へのアクセス権が付与されます。 [詳細情報](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | デプロイを作成または更新します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | サブスクリプション操作の結果を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | サブスクリプションの一覧を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/controllerrevisions/read | controllerrevisions を読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/daemonsets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/deployments/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/replicasets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/statefulsets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/autoscaling/horizontalpodautoscalers/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/cronjobs/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/jobs/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/endpoints/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events.k8s.io/events/read | イベントを読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events/read | イベントを読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/daemonsets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/deployments/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/ingresses/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/networkpolicies/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/replicasets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/read | limitranges を読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/read | 名前空間を読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/ingresses/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/networkpolicies/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/pods/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/policy/poddisruptionbudgets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/read | resourcequotas を読み取ります |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/secrets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/serviceaccounts/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/services/* |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read/write access to most objects in a namespace.This role does not allow viewing or modifying roles or role bindings. However, this role allows accessing Secrets and running Pods as any ServiceAccount in the namespace, so it can be used to gain the API access levels of any ServiceAccount in the namespace. Applying this role at cluster scope will give access across all namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "name": "a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/apps/controllerrevisions/read",
        "Microsoft.ContainerService/managedClusters/apps/daemonsets/*",
        "Microsoft.ContainerService/managedClusters/apps/deployments/*",
        "Microsoft.ContainerService/managedClusters/apps/replicasets/*",
        "Microsoft.ContainerService/managedClusters/apps/statefulsets/*",
        "Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/*",
        "Microsoft.ContainerService/managedClusters/batch/cronjobs/*",
        "Microsoft.ContainerService/managedClusters/batch/jobs/*",
        "Microsoft.ContainerService/managedClusters/configmaps/*",
        "Microsoft.ContainerService/managedClusters/endpoints/*",
        "Microsoft.ContainerService/managedClusters/events.k8s.io/events/read",
        "Microsoft.ContainerService/managedClusters/events/read",
        "Microsoft.ContainerService/managedClusters/extensions/daemonsets/*",
        "Microsoft.ContainerService/managedClusters/extensions/deployments/*",
        "Microsoft.ContainerService/managedClusters/extensions/ingresses/*",
        "Microsoft.ContainerService/managedClusters/extensions/networkpolicies/*",
        "Microsoft.ContainerService/managedClusters/extensions/replicasets/*",
        "Microsoft.ContainerService/managedClusters/limitranges/read",
        "Microsoft.ContainerService/managedClusters/namespaces/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/*",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/*",
        "Microsoft.ContainerService/managedClusters/persistentvolumeclaims/*",
        "Microsoft.ContainerService/managedClusters/pods/*",
        "Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/*",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/*",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/*",
        "Microsoft.ContainerService/managedClusters/resourcequotas/read",
        "Microsoft.ContainerService/managedClusters/secrets/*",
        "Microsoft.ContainerService/managedClusters/serviceaccounts/*",
        "Microsoft.ContainerService/managedClusters/services/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="databases"></a>データベース


### <a name="cosmos-db-account-reader-role"></a>Cosmos DB アカウントの閲覧者ロール

Cosmos DB アカウントのデータを読み取ることができます。 Azure Cosmos DB アカウントの管理については、「[DocumentDB Account Contributor](#documentdb-account-contributor)」をご覧ください。 [詳細情報](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/*/read | 任意のコレクションの読み取り |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlykeys/action | データベース アカウントの読み取り専用キーを読み取ります。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/read | メトリック定義を読み取ります。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/read | メトリックを読み取ります。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read Azure Cosmos DB Accounts data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "name": "fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDB/*/read",
        "Microsoft.DocumentDB/databaseAccounts/readonlykeys/action",
        "Microsoft.Insights/MetricDefinitions/read",
        "Microsoft.Insights/Metrics/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Account Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmos-db-operator"></a>Cosmos DB オペレーター

Azure Cosmos DB アカウントを管理することができます。ただし、アカウント内のデータにはアクセスできません。 アカウント キーと接続文字列へのアクセスは禁止されます。 [詳細情報](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | ストレージ アカウントや SQL Database などのリソースをサブネットに結合します。 警告不可能です。 |
> | **NotActions** |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlyKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/regenerateKey/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listConnectionStrings/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleDefinitions/write | SQL ロールの定義を作成または更新します |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleDefinitions/delete | SQL ロールの定義を削除します |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleAssignments/write | SQL ロールの割り当てを作成または更新します |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleAssignments/delete | SQL ロールの割り当てを削除します |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Cosmos DB accounts, but not access data in them. Prevents access to account keys and connection strings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/230815da-be43-4aae-9cb4-875f7bd000aa",
  "name": "230815da-be43-4aae-9cb4-875f7bd000aa",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [
        "Microsoft.DocumentDB/databaseAccounts/readonlyKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/regenerateKey/*",
        "Microsoft.DocumentDB/databaseAccounts/listKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/*",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions/write",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions/delete",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments/write",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosbackupoperator"></a>CosmosBackupOperator

Cosmos DB データベースまたはアカウントのコンテナーの復元要求を送信できます。[詳細](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/backup/action | バックアップを構成するための要求を送信します |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/restore/action | 復元要求を送信します |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can submit restore request for a Cosmos DB database or a container for an account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "name": "db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/databaseAccounts/backup/action",
        "Microsoft.DocumentDB/databaseAccounts/restore/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosBackupOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosrestoreoperator"></a>CosmosRestoreOperator

継続的バックアップモードで Cosmos DB データベース アカウントの復元操作を実行できます

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/locations/restorableDatabaseAccounts/restore/action | 復元要求を送信します |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/locations/restorableDatabaseAccounts/*/read |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/locations/restorableDatabaseAccounts/read | 復元可能なデータベース アカウントを読み取るか、すべての復元可能なデータベースアカウントを一覧表示します |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can perform restore action for Cosmos DB database account with continuous backup mode",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5432c526-bc82-444a-b7ba-57c5b0b5b34f",
  "name": "5432c526-bc82-444a-b7ba-57c5b0b5b34f",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosRestoreOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="documentdb-account-contributor"></a>DocumentDB Account Contributor

Azure Cosmos DB アカウントを管理できます。 Azure Cosmos DB は以前は DocumentDB と呼ばれていました。 [詳細情報](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* | Azure Cosmos DB アカウントの作成と管理 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | ストレージ アカウントや SQL Database などのリソースをサブネットに結合します。 警告不可能です。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DocumentDB accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5bd9cd88-fe45-4216-938b-f97437e15450",
  "name": "5bd9cd88-fe45-4216-938b-f97437e15450",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DocumentDB Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="redis-cache-contributor"></a>Redis Cache Contributor

Redis Caches を管理できます。ただし、それらへのアクセスは含まれません。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Cache](resource-provider-operations.md#microsoftcache)/register/action | "Microsoft.Cache" リソース プロバイダーをサブスクリプションに登録します。 |
> | [Microsoft.Cache](resource-provider-operations.md#microsoftcache)/redis/* | Redis キャッシュの作成と管理 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Redis caches, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e0f68234-74aa-48ed-b826-c38b57376e17",
  "name": "e0f68234-74aa-48ed-b826-c38b57376e17",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cache/register/action",
        "Microsoft.Cache/redis/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Redis Cache Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-db-contributor"></a>SQL DB Contributor

SQL データベースを管理できます。ただし、それらへのアクセスは含まれません。 また、セキュリティ関連のポリシーまたは親 SQL Server を管理することはできません。 [詳細情報](../data-share/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/*/read |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/* | SQL データベースの作成と管理 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/read | サーバーの一覧を返すか、指定されたサーバーのプロパティを取得します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | メトリックを読み取ります。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | メトリック定義を読み取ります。 |
> | **NotActions** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | 監査設定の編集 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | データベースの BLOB 監査レコードを取得します。 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | データ マスク ポリシーの編集 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | セキュリティ警告ポリシーの編集 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | セキュリティ基準の編集 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL databases, but not access to them. Also, you can't manage their security-related policies or their parent SQL servers.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "name": "9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/databases/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL DB Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-managed-instance-contributor"></a>SQL マネージド インスタンス共同作成者

SQL マネージド インスタンスと必要なネットワーク構成を管理することができますが、他のユーザーにアクセス権を付与することはできません。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/routeTables/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/*/read |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/instanceFailoverGroups/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/* |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | メトリックを読み取ります。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | メトリック定義を読み取ります。 |
> | **NotActions** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/delete | 特定のマネージド サーバーの Azure Active Directory のみの認証オブジェクトを削除します |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/write | 特定のマネージド サーバーの Azure Active Directory のみの認証オブジェクトを追加または更新します |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL Managed Instances and required network configuration, but can't give access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "name": "4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Network/networkSecurityGroups/*",
        "Microsoft.Network/routeTables/*",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/locations/instanceFailoverGroups/*",
        "Microsoft.Sql/managedInstances/*",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/*",
        "Microsoft.Network/virtualNetworks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/delete",
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/write"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Managed Instance Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-security-manager"></a>SQL Security Manager

SQL サーバーとデータベースのセキュリティ関連のポリシーを管理できます。ただし、それらへのアクセスは管理できません。 [詳細情報](../azure-sql/database/azure-defender-for-sql.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | ストレージ アカウントや SQL Database などのリソースをサブネットに結合します。 警告不可能です。 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/administratorAzureAsyncOperation/read | マネージド インスタンスの Azure 非同期管理者操作の結果を取得します。 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/transparentDataEncryption/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/auditingSettings/* | SQL サーバー監査設定の作成と管理 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/extendedAuditingSettings/read | 指定されたサーバーで構成されている拡張サーバー BLOB 監査ポリシーの詳細を取得します。 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | SQL サーバー データベース監査設定の作成と管理 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | データベースの BLOB 監査レコードを取得します。 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | SQL サーバー データベース データ マスク ポリシーの作成と管理 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/read | 指定されたデータベースで構成されている拡張 BLOB 監査ポリシーの詳細を取得します。 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/read | データベースの一覧を返すか、指定されたデータベースのプロパティを取得します。 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/read | データベースのスキーマを取得します。 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/read | データベースの列を取得します。 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/read | データベースのテーブルを取得します。 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | SQL サーバー データベース セキュリティの警告のポリシーの作成と管理 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | SQL サーバー データベース セキュリティ基準の作成と管理 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/transparentDataEncryption/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/firewallRules/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/read | サーバーの一覧を返すか、指定されたサーバーのプロパティを取得します。 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/securityAlertPolicies/* | SQL サーバー セキュリティの警告のポリシーの作成と管理 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/read | マネージド インスタンスの一覧を返すか、指定されたマネージド インスタンスのプロパティを取得します。 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/* |  |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/sqlVulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/administrators/read | Managed Instance の管理者の一覧を取得します。 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/administrators/read | 特定の Azure Active Directory 管理者オブジェクトを取得します |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the security-related policies of SQL servers and databases, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "name": "056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/administratorAzureAsyncOperation/read",
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/transparentDataEncryption/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/read",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/read",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/transparentDataEncryption/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/firewallRules/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Support/*",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/*",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/*",
        "Microsoft.Security/sqlVulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/administrators/read",
        "Microsoft.Sql/servers/administrators/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Security Manager",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-server-contributor"></a>SQL Server Contributor

SQL サーバーとデータベースを管理できます。ただし、それらへのアクセスや、それらのセキュリティ関連ポリシーは管理できません。 [詳細情報](../azure-sql/database/authentication-aad-configure.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/*/read |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/* | SQL サーバーの作成と管理 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | メトリックを読み取ります。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | メトリック定義を読み取ります。 |
> | **NotActions** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/auditingSettings/* | SQL サーバー監査設定の編集 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | SQL サーバー データベース監査設定の編集 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | データベースの BLOB 監査レコードを取得します。 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | SQL サーバー データベース データ マスク ポリシーの編集 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | SQL サーバー データベースのセキュリティ警告ポリシーの編集 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | SQL サーバー データベースのセキュリティ基準の編集 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/securityAlertPolicies/* | SQL サーバーのセキュリティ警告ポリシーの編集 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/delete | 特定のサーバーの Azure Active Directory のみの認証オブジェクトを削除します |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/write | 特定のサーバーの Azure Active Directory 認証オブジェクトのみを追加または更新します |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL servers and databases, but not access to them, and not their security -related policies.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "name": "6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/*",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/delete",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/write"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Server Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="analytics"></a>Analytics


### <a name="azure-event-hubs-data-owner"></a>Azure Event Hubs データ所有者

Azure Event Hubs リソースへのフル アクセスを許可します。 [詳細情報](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f526a384-b230-433a-b45c-95f59c4a2dec",
  "name": "f526a384-b230-433a-b45c-95f59c4a2dec",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-receiver"></a>Azure Event Hubs データ受信者

Azure Event Hubs リソースへの受信アクセスを許可します。 [詳細情報](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/consumergroups/read |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/receive/action |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows receive access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "name": "a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/consumergroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-sender"></a>Azure Event Hubs データ送信者

Azure Event Hubs リソースへの送信アクセスを許可します。 [詳細情報](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/read |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/send/action |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows send access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2b629674-e913-4c01-ae53-ef4638d8f975",
  "name": "2b629674-e913-4c01-ae53-ef4638d8f975",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-factory-contributor"></a>Data Factory Contributor

データ ファクトリまたデータ ファクトリ内の子リソースを作成し管理します。 [詳細情報](../data-factory/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.DataFactory](resource-provider-operations.md#microsoftdatafactory)/dataFactories/* | Data Factory と Data Factory に含まれる子リソースを作成および管理します。 |
> | [Microsoft.DataFactory](resource-provider-operations.md#microsoftdatafactory)/factories/* | Data Factory と Data Factory に含まれる子リソースを作成および管理します。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/write | eventSubscription を作成または更新します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and manage data factories, as well as child resources within them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/673868aa-7521-48a0-acc6-0f60742d39f5",
  "name": "673868aa-7521-48a0-acc6-0f60742d39f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DataFactory/dataFactories/*",
        "Microsoft.DataFactory/factories/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.EventGrid/eventSubscriptions/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Factory Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-purger"></a>Data Purger

Log Analytics ワークスペースの非公開データを削除します。 [詳細情報](../azure-monitor/logs/personal-data-mgmt.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/purge/action | Application Insights からデータを削除します。 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Log Analytics のデータの表示 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/purge/action | ワークスペースから指定されたデータを削除します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can purge analytics data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "name": "150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/components/*/read",
        "Microsoft.Insights/components/purge/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/purge/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Purger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-cluster-operator"></a>HDInsight クラスター オペレーター

HDInsight クラスター構成の読み取りと変更を実行できます。 [詳細情報](../hdinsight/hdinsight-migrate-granular-access-cluster-configurations.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/*/read |  |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/getGatewaySettings/action | HDInsight クラスター向けのアプリケーションを取得します |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/updateGatewaySettings/action | HDInsight クラスターのゲートウェイ設定を更新します |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/configurations/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | デプロイ操作を取得または一覧表示します。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and modify HDInsight cluster configurations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/61ed4efc-fab3-44fd-b111-e24485cc132a",
  "name": "61ed4efc-fab3-44fd-b111-e24485cc132a",
  "permissions": [
    {
      "actions": [
        "Microsoft.HDInsight/*/read",
        "Microsoft.HDInsight/clusters/getGatewaySettings/action",
        "Microsoft.HDInsight/clusters/updateGatewaySettings/action",
        "Microsoft.HDInsight/clusters/configurations/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Cluster Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-domain-services-contributor"></a>HDInsight ドメイン サービス共同作成者

HDInsight Enterprise セキュリティ パッケージに必要なドメイン サービス関連の操作の読み取り、作成、変更、および削除を行うことができます。[詳細](../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/*/read |  |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/domainServices/*/read |  |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can Read, Create, Modify and Delete Domain Services related operations needed for HDInsight Enterprise Security Package",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "name": "8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "permissions": [
    {
      "actions": [
        "Microsoft.AAD/*/read",
        "Microsoft.AAD/domainServices/*/read",
        "Microsoft.AAD/domainServices/oucontainer/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Domain Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-contributor"></a>Log Analytics 共同作成者

Log Analytics 共同作成者は、すべての監視データを読み取り、監視設定を編集できます。 監視設定の編集には、VM 拡張機能の VM への追加、Azure Storage からログの収集を設定できるようにするためのストレージ アカウント キーの読み取り、Automation アカウントの作成と構成、ソリューションの追加、すべての Azure リソースでの Azure Diagnostics の構成が含まれます。 [詳細情報](../azure-monitor/logs/manage-access.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | */read | 機密データを除くあらゆる種類のリソースの読み取り |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/* |  |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/extensions/* |  |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | ストレージ アカウントのアクセス キーを一覧表示します。 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/extensions/* |  |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/extensions/write | Azure Arc 拡張機能をインストールまたは更新されます |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | 分析サーバーの診断の設定の作成、更新、または読み取りを行います |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/* |  |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourcegroups/deployments/* |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | 指定されたストレージ アカウントのアクセス キーを返します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Contributor can read all monitoring data and edit monitoring settings. Editing monitoring settings includes adding the VM extension to VMs; reading storage account keys to be able to configure collection of logs from Azure Storage; creating and configuring Automation accounts; adding solutions; and configuring Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "name": "92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Automation/automationAccounts/*",
        "Microsoft.ClassicCompute/virtualMachines/extensions/*",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.Compute/virtualMachines/extensions/*",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.OperationalInsights/*",
        "Microsoft.OperationsManagement/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-reader"></a>Log Analytics 閲覧者

Log Analytics Reader は、すべての監視データの表示と検索、およびすべての Azure リソース上の Azure Diagnostics 構成の表示など、監視設定の表示を行うことができます。 [詳細情報](../azure-monitor/logs/manage-access.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | */read | 機密データを除くあらゆる種類のリソースの読み取り |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | 新しいエンジンを使用して検索します。 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | 検索クエリを実行します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/read | ワークスペースの共有キーを取得します。 これらのキーを使用して、Microsoft Operational Insights エージェントをワークスペースに接続します。 |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Reader can view and search all monitoring data as well as and view monitoring settings, including viewing the configuration of Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/73c42c96-874c-492b-b04d-ab87d138a893",
  "name": "73c42c96-874c-492b-b04d-ab87d138a893",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.OperationalInsights/workspaces/sharedKeys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="purview-data-curator"></a>Purview データ キュレーター

Microsoft.Purview データ キュレーターでは、カタログ データ オブジェクトの作成、読み取り、変更、削除や、オブジェクト間のリレーションシップの確立を行うことができます。 このロールはプレビュー段階にあり、変更される可能性があります。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/read | Microsoft Purview プロバイダーのアカウント リソースを読み取ります。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/data/read | データ オブジェクトを読み取ります。 |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/data/write | データ オブジェクトを作成、更新、および削除します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "The Microsoft.Purview data curator can create, read, modify and delete catalog data objects and establish relationships between objects. This role is in preview and subject to change.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a3c2885-9b38-4fd2-9d99-91af537c1347",
  "name": "8a3c2885-9b38-4fd2-9d99-91af537c1347",
  "permissions": [
    {
      "actions": [
        "Microsoft.Purview/accounts/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Purview/accounts/data/read",
        "Microsoft.Purview/accounts/data/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Purview Data Curator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="purview-data-reader"></a>Purview データ リーダー

Microsoft.Purview データ リーダーは、カタログ データ オブジェクトを読み取ることができます。 このロールはプレビュー段階にあり、変更される可能性があります。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/read | Microsoft Purview プロバイダーのアカウント リソースを読み取ります。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/data/read | データ オブジェクトを読み取ります。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "The Microsoft.Purview data reader can read catalog data objects. This role is in preview and subject to change.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ff100721-1b9d-43d8-af52-42b69c1272db",
  "name": "ff100721-1b9d-43d8-af52-42b69c1272db",
  "permissions": [
    {
      "actions": [
        "Microsoft.Purview/accounts/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Purview/accounts/data/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Purview Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="purview-data-source-administrator"></a>Purview データ ソース管理者

Microsoft.Purview データ ソース管理者は、データ ソースとデータ スキャンを管理できます。 このロールはプレビュー段階にあり、変更される可能性があります。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/read | Microsoft Purview プロバイダーのアカウント リソースを読み取ります。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/scan/read | データ ソースとスキャンを読み取ります。 |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/scan/write | データ ソースの作成、更新、削除、およびスキャンの管理を行います。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "The Microsoft.Purview data source administrator can manage data sources and data scans. This role is in preview and subject to change.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/200bba9e-f0c8-430f-892b-6f0794863803",
  "name": "200bba9e-f0c8-430f-892b-6f0794863803",
  "permissions": [
    {
      "actions": [
        "Microsoft.Purview/accounts/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Purview/accounts/scan/read",
        "Microsoft.Purview/accounts/scan/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Purview Data Source Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="schema-registry-contributor-preview"></a>Schema Registry Contributor (プレビュー)

Schema Registry グループおよびスキーマの読み取り、書き込み、および削除を行います。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemagroups/* |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemas/* |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read, write, and delete Schema Registry groups and schemas.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5dffeca3-4936-4216-b2bc-10343a5abb25",
  "name": "5dffeca3-4936-4216-b2bc-10343a5abb25",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/namespaces/schemagroups/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/namespaces/schemas/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Schema Registry Contributor (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="schema-registry-reader-preview"></a>Schema Registry Reader (プレビュー)

Schema Registry グループおよびスキーマの読み取りと一覧表示を行います。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemagroups/read | SchemaGroup リソースの説明の一覧を取得します |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemas/read | スキーマを取得する |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and list Schema Registry groups and schemas.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2c56ea50-c6b3-40a6-83c0-9d98858bc7d2",
  "name": "2c56ea50-c6b3-40a6-83c0-9d98858bc7d2",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/namespaces/schemagroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/namespaces/schemas/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Schema Registry Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="blockchain"></a>ブロックチェーン


### <a name="blockchain-member-node-access-preview"></a>ブロックチェーン メンバー ノードへのアクセス (プレビュー)

ブロックチェーン メンバー ノードにアクセスできるようにします。[詳細](../blockchain/service/configure-aad.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/read | 既存のブロックチェーン メンバーのトランザクション ノードを取得または一覧表示します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.Blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/connect/action | ブロックチェーン メンバーのトランザクション ノードに接続します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for access to Blockchain Member nodes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "name": "31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Blockchain Member Node Access (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="ai--machine-learning"></a>AI + 機械学習


### <a name="cognitive-services-contributor"></a>Cognitive Services 共同作成者

Cognitive Services のキーの作成、読み取り、更新、削除、管理を行うことができます。 [詳細情報](../cognitive-services/cognitive-services-virtual-networks.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | [Microsoft.Features](resource-provider-operations.md#microsoftfeatures)/features/read | サブスクリプションの機能を取得します。 |
> | [Microsoft.Features](resource-provider-operations.md#microsoftfeatures)/providers/features/read | 指定されたリソース プロバイダーのサブスクリプションの機能を取得します。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | 分析サーバーの診断の設定の作成、更新、または読み取りを行います |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/read | ログ定義を読み取ります。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/read | メトリック定義を読み取ります。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | メトリックを読み取ります。 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | デプロイ操作を取得または一覧表示します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | サブスクリプション操作の結果を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | サブスクリプションの一覧を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourcegroups/deployments/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, read, update, delete and manage keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "name": "25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.CognitiveServices/*",
        "Microsoft.Features/features/read",
        "Microsoft.Features/providers/features/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-contributor"></a>Cognitive Services Custom Vision Contributor

プロジェクトの表示、作成、編集、削除を含む、プロジェクトへのフル アクセス。 [詳細情報](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/* |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to the project, including the ability to view, create, edit, or delete projects.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c1ff6cc2-c111-46fe-8896-e0ef812ad9f3",
  "name": "c1ff6cc2-c111-46fe-8896-e0ef812ad9f3",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Custom Vision Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-deployment"></a>Cognitive Services Custom Vision Deployment

モデルの公開、非公開、またはエクスポートを行います。 Deployment は、プロジェクトを表示できますが、更新することはできません。 [詳細情報](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/predictions/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/iterations/publish/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/iterations/export/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/quicktest/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/classify/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/detect/* |  |
> | **NotDataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | プロジェクトをエクスポートします。 |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Publish, unpublish or export models. Deployment can view the project but can't update.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5c4089e1-6d96-4d2f-b296-c1bc7137275f",
  "name": "5c4089e1-6d96-4d2f-b296-c1bc7137275f",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*/read",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/publish/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/export/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/quicktest/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/classify/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/detect/*"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Deployment",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-labeler"></a>Cognitive Services Custom Vision Labeler

トレーニング画像の表示と編集、およびイメージ タグの作成、追加、または削除を行うことができます。 ラベラーはプロジェクトを表示できますが、トレーニング画像とタグ以外は更新できません。 [詳細情報](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/predictions/query/action | 予測エンドポイントに送信された画像を取得します。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/images/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/tags/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/images/suggested/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/tagsandregions/suggestions/action | この API は、タグ付けされていないイメージの配列/バッチとタグの信頼度に対して、推奨されるタグと領域を取得します。 タグが見つからない場合は、空の配列を返します。 |
> | **NotDataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | プロジェクトをエクスポートします。 |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View, edit training images and create, add, remove, or delete the image tags. Labelers can view the project but can't update anything other than training images and tags.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/88424f51-ebe7-446f-bc41-7fa16989e96c",
  "name": "88424f51-ebe7-446f-bc41-7fa16989e96c",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*/read",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/query/action",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/images/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/tags/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/images/suggested/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/tagsandregions/suggestions/action"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Labeler",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-reader"></a>Cognitive Services Custom Vision Reader

プロジェクトでの読み取り専用のアクション。 閲覧者がこのプロジェクトを作成または更新することはできません。 [詳細情報](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/predictions/query/action | 予測エンドポイントに送信された画像を取得します。 |
> | **NotDataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | プロジェクトをエクスポートします。 |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only actions in the project. Readers can't create or update the project.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/93586559-c37d-4a6b-ba08-b9f0940c2d73",
  "name": "93586559-c37d-4a6b-ba08-b9f0940c2d73",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*/read",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/query/action"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-trainer"></a>Cognitive Services Custom Vision Trainer

プロジェクトの表示、作成、およびモデルの公開、非公開、エクスポートを含む、モデルのトレーニングを行うことができます。 トレーナーがこのプロジェクトを作成または削除することはできません。 [詳細情報](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/* |  |
> | **NotDataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/action | プロジェクトを作成します。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/delete | 特定のプロジェクトを削除します。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/import/action | プロジェクトをインポートします。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | プロジェクトをエクスポートします。 |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View, edit projects and train the models, including the ability to publish, unpublish, export the models. Trainers can't create or delete the project.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0a5ae4ab-0d65-4eeb-be61-29fc9b54394b",
  "name": "0a5ae4ab-0d65-4eeb-be61-29fc9b54394b",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/action",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/delete",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/import/action",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Trainer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-data-reader-preview"></a>Cognitive Services データ閲覧者 (プレビュー)

Cognitive Services データを読み取ります。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read Cognitive Services data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "name": "b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Data Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-metrics-advisor-administrator"></a>Cognitive Services Metrics Advisor Administrator

システム レベルの構成を含む、プロジェクトへのフル アクセス。 [詳細情報](../cognitive-services/metrics-advisor/how-tos/alerts.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/MetricsAdvisor/* |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to the project, including the system level configuration.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cb43c632-a144-4ec5-977c-e80c4affc34a",
  "name": "cb43c632-a144-4ec5-977c-e80c4affc34a",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/MetricsAdvisor/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Metrics Advisor Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-qna-maker-editor"></a>Cognitive Services QnA Maker エディター

KB の作成、編集、インポート、およびエクスポートが可能になります。 KB を公開または削除することはできません。 [詳細情報](../cognitive-services/qnamaker/reference-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleAssignments/read | ロールの割り当てに関する情報を取得します。 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleDefinitions/read | ロール定義に関する情報を取得します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/read | ナレッジベースの一覧または特定のナレッジベースの詳細を取得します。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/download/read | ナレッジベースをダウンロードします。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/create/write | 新しいナレッジベースを作成する非同期操作。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/write | ナレッジベースを変更する、またはナレッジベースの内容を置き換えるための非同期操作。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/generateanswer/action | ナレッジベースのクエリを実行するための GenerateAnswer 呼び出し。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/train/action | ナレッジベースに提案を追加する呼び出しをトレーニングします。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/alterations/read | ランタイムから変更をダウンロードします。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/alterations/write | 変更データを置換します。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/read | エンドポイント用のエンドポイント キーを取得します |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/refreshkeys/action | エンドポイント キーを再生成します。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/read | エンドポイントのエンドポイント設定を取得します |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/write | エンドポイントのエンドポイント設定を更新します。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/operations/read | 長期実行されている特定の操作の詳細を取得します。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/read | ナレッジベースの一覧または特定のナレッジベースの詳細を取得します。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/download/read | ナレッジベースをダウンロードします。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/create/write | 新しいナレッジベースを作成する非同期操作。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/write | ナレッジベースを変更する、またはナレッジベースの内容を置き換えるための非同期操作。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/generateanswer/action | ナレッジベースのクエリを実行するための GenerateAnswer 呼び出し。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/train/action | ナレッジベースに提案を追加する呼び出しをトレーニングします。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/alterations/read | ランタイムから変更をダウンロードします。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/alterations/write | 変更データを置換します。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/read | エンドポイント用のエンドポイント キーを取得します |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/refreshkeys/action | エンドポイント キーを再生成します。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/read | エンドポイントのエンドポイント設定を取得します |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/write | エンドポイントのエンドポイント設定を更新します。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/operations/read | 長期実行されている特定の操作の詳細を取得します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Let's you create, edit, import and export a KB. You cannot publish or delete a KB.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f4cc2bf9-21be-47a1-bdf1-5c5804381025",
  "name": "f4cc2bf9-21be-47a1-bdf1-5c5804381025",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/create/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/train/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/alterations/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/refreshkeys/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/operations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/create/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/train/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/refreshkeys/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/operations/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services QnA Maker Editor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-qna-maker-reader"></a>Cognitive Services QnA Maker 閲覧者

KB のみ、読み取りとテストが可能になります。 [詳細情報](../cognitive-services/qnamaker/reference-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleAssignments/read | ロールの割り当てに関する情報を取得します。 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleDefinitions/read | ロール定義に関する情報を取得します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/read | ナレッジベースの一覧または特定のナレッジベースの詳細を取得します。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/download/read | ナレッジベースをダウンロードします。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/generateanswer/action | ナレッジベースのクエリを実行するための GenerateAnswer 呼び出し。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/alterations/read | ランタイムから変更をダウンロードします。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/read | エンドポイント用のエンドポイント キーを取得します |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/read | エンドポイントのエンドポイント設定を取得します |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/read | ナレッジベースの一覧または特定のナレッジベースの詳細を取得します。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/download/read | ナレッジベースをダウンロードします。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/generateanswer/action | ナレッジベースのクエリを実行するための GenerateAnswer 呼び出し。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/alterations/read | ランタイムから変更をダウンロードします。 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/read | エンドポイント用のエンドポイント キーを取得します |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/read | エンドポイントのエンドポイント設定を取得します |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Let's you read and test a KB only.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/466ccd10-b268-4a11-b098-b4849f024126",
  "name": "466ccd10-b268-4a11-b098-b4849f024126",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services QnA Maker Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-user"></a>Cognitive Services ユーザー

Cognitive Services のキーの読み取りおよび一覧表示を行うことができます。 [詳細情報](../cognitive-services/authentication.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/listkeys/action | キーを一覧表示します。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | クラシック メトリック アラートを読み取ります |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/read | リソースの診断設定を読み取ります |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/read | ログ定義を読み取ります。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/read | メトリック定義を読み取ります。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | メトリックを読み取ります。 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | デプロイ操作を取得または一覧表示します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | サブスクリプション操作の結果を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | サブスクリプションの一覧を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and list keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a97b65f3-24c7-4388-baec-2e87135dc908",
  "name": "a97b65f3-24c7-4388-baec-2e87135dc908",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.CognitiveServices/accounts/listkeys/action",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Insights/diagnosticSettings/read",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="mixed-reality"></a>複合現実


### <a name="remote-rendering-administrator"></a>Remote Rendering 管理者

ユーザーに、Azure Remote Rendering の変換、セッション管理、レンダリング、および診断の機能を提供します。[詳細](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/action | 資産の変換を開始します |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/read | 資産の変換プロパティを取得します |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/delete | 資産の変換を停止します |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/read | セッションのプロパティを取得します |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/action | セッションを開始します |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/delete | セッションを停止します |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/render/read | セッションに接続します |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/diagnostic/read | Remote Rendering インスペクターに接続します |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with conversion, manage session, rendering and diagnostics capabilities for Azure Remote Rendering",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "name": "3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="remote-rendering-client"></a>Remote Rendering クライアント

ユーザーに、Azure Remote Rendering でのセッション管理、レンダリング、および診断の機能を提供します。 [詳細情報](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/read | セッションのプロパティを取得します |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/action | セッションを開始します |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/delete | セッションを停止します |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/render/read | セッションに接続します |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/diagnostic/read | Remote Rendering インスペクターに接続します |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with manage session, rendering and diagnostics capabilities for Azure Remote Rendering.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "name": "d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Client",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-contributor"></a>Spatial Anchors アカウント共同作成者

アカウントで空間アンカーを管理できますが、削除することはできません。[詳細](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/create/action | 空間アンカーを作成します。 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | 近くにある空間アンカーを検出します。 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | 空間アンカーのプロパティを取得します。 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | 空間アンカーを探します。 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Azure Spatial Anchors サービスの品質を改善するために診断データを送信します。 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/write | 空間アンカーのプロパティを更新します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, but not delete them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "name": "8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-owner"></a>Spatial Anchors アカウント所有者

アカウントで空間アンカーを管理できます。削除も可能です。[詳細](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/create/action | 空間アンカーを作成します。 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/delete | 空間アンカーを削除します。 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | 近くにある空間アンカーを検出します。 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | 空間アンカーのプロパティを取得します。 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | 空間アンカーを探します。 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Azure Spatial Anchors サービスの品質を改善するために診断データを送信します。 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/write | 空間アンカーのプロパティを更新します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, including deleting them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/70bbe301-9835-447d-afdd-19eb3167307c",
  "name": "70bbe301-9835-447d-afdd-19eb3167307c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/delete",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-reader"></a>Spatial Anchors アカウント閲覧者

アカウントで空間アンカーのプロパティを検索して読み取ることができます。[詳細](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | 近くにある空間アンカーを検出します。 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | 空間アンカーのプロパティを取得します。 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | 空間アンカーを探します。 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Azure Spatial Anchors サービスの品質を改善するために診断データを送信します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you locate and read properties of spatial anchors in your account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "name": "5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="integration"></a>統合


### <a name="api-management-service-contributor"></a>API Management Service Contributor

サービスと API を管理できます。[詳細](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/* | API Management サービスの作成と管理 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service and the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c",
  "name": "312a565d-c81f-4fd8-895a-4e21e48d571c",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-operator-role"></a>API Management Service Operator Role

サービスを管理できますが、API は対象外です。[詳細](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/*/read | API Management サービス インスタンスの読み取り |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/backup/action | ユーザーが指定したストレージ アカウント内の指定されたコンテナーに API Management サービスをバックアップします。 |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/delete | API Management サービス インスタンスを削除します。 |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/managedeployments/action | SKU/ユニット数を変更し、API Management サービスのリージョン デプロイを追加または削除します。 |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/read | API Management サービス インスタンスのメタデータの読み取り |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/restore/action | ユーザーが指定したストレージ アカウント内の指定されたコンテナーからの API Management サービスの復元 |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/updatecertificate/action | API Management サービスの TLS/SSL 証明書をアップロードします。 |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/updatehostname/action | API Management サービスのカスタム ドメイン名を設定、更新、または削除します。 |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/write | API Management サービスのインスタンスが作成または更新されます |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/users/keys/read | ユーザーに関連付けられたキーを取得します |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service but not the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "name": "e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/backup/action",
        "Microsoft.ApiManagement/service/delete",
        "Microsoft.ApiManagement/service/managedeployments/action",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.ApiManagement/service/restore/action",
        "Microsoft.ApiManagement/service/updatecertificate/action",
        "Microsoft.ApiManagement/service/updatehostname/action",
        "Microsoft.ApiManagement/service/write",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-reader-role"></a>API Management Service Reader Role

サービスと API への読み取り専用アクセスです。[詳細](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/*/read | API Management サービス インスタンスの読み取り |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/read | API Management サービス インスタンスのメタデータの読み取り |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/users/keys/read | ユーザーに関連付けられたキーを取得します |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to service and APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/71522526-b88f-4d52-b57f-d31fc3546d0d",
  "name": "71522526-b88f-4d52-b57f-d31fc3546d0d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-owner"></a>App Configuration データ所有者

App Configuration データへのフル アクセスを許可します。 [詳細情報](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/read |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/write |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/delete |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows full access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "name": "5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read",
        "Microsoft.AppConfiguration/configurationStores/*/write",
        "Microsoft.AppConfiguration/configurationStores/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-reader"></a>App Configuration データ閲覧者

App Configuration データへの読み取りアクセスを許可します。 [詳細情報](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/read |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/516239f1-63e1-4d78-a4de-a74fb236a071",
  "name": "516239f1-63e1-4d78-a4de-a74fb236a071",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-owner"></a>Azure Service Bus データ所有者

Azure Service Bus リソースへのフル アクセスを許可します。 [詳細情報](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/090c5cfd-751d-490a-894a-3ce6f1109419",
  "name": "090c5cfd-751d-490a-894a-3ce6f1109419",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-receiver"></a>Azure Service Bus データ受信者

Azure Service Bus リソースへの受信アクセスを許可します。 [詳細情報](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/queues/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/receive/action |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for receive access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "name": "4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-sender"></a>Azure Service Bus データ送信者

Azure Service Bus リソースへの送信アクセスを許可します。 [詳細情報](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/queues/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/send/action |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for send access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "name": "69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-stack-registration-owner"></a>Azure Stack Registration Owner

Azure Stack の登録を管理できます。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/edgeSubscriptions/read | Azure Stack Edge のサブスクリプションのプロパティを取得します。 |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/products/*/action |  |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/products/read | Azure Stack Marketplace の製品のプロパティを取得します |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/read | Azure Stack の登録のプロパティを取得します |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Stack registrations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "name": "6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "permissions": [
    {
      "actions": [
        "Microsoft.AzureStack/edgeSubscriptions/read",
        "Microsoft.AzureStack/registrations/products/*/action",
        "Microsoft.AzureStack/registrations/products/read",
        "Microsoft.AzureStack/registrations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Stack Registration Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-contributor"></a>EventGrid EventSubscription 共同作成者

EventGrid のイベント サブスクリプション操作を管理できます。 [詳細情報](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/* |  |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/read | グローバル イベント サブスクリプションをトピックの種類ごとに一覧表示します |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/read | リージョンのイベント サブスクリプションを一覧表示します |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/read | リージョンのイベント サブスクリプションを topictype ごとに一覧表示します |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage EventGrid event subscription operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "name": "428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/*",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-reader"></a>EventGrid EventSubscription 閲覧者

EventGrid のイベント サブスクリプションを読み取ることができます。 [詳細情報](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/read | eventSubscription を削除します。 |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/read | グローバル イベント サブスクリプションをトピックの種類ごとに一覧表示します |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/read | リージョンのイベント サブスクリプションを一覧表示します |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/read | リージョンのイベント サブスクリプションを topictype ごとに一覧表示します |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read EventGrid event subscriptions.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2414bbcf-6497-4faf-8c65-045460748405",
  "name": "2414bbcf-6497-4faf-8c65-045460748405",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-contributor"></a>FHIR データ共同作成者

ユーザーまたはプリンシパルに FHIR データへのフル アクセスを許可するロールです。[詳細](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/* |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal full access to FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "name": "5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-exporter"></a>FHIR データ エクスポーター

ユーザーまたはプリンシパルに FHIR データの読み取りとエクスポートを許可するロールです。[詳細](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/read | FHIR リソースを読み取ります (検索とバージョン管理された履歴を含みます)。  |
> | Microsoft.HealthcareApis/services/fhir/resources/export/action | エクスポート操作 ($export)。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and export FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3db33094-8700-4567-8da5-1501d4e7e843",
  "name": "3db33094-8700-4567-8da5-1501d4e7e843",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read",
        "Microsoft.HealthcareApis/services/fhir/resources/export/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Exporter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-reader"></a>FHIR データ リーダー

ユーザーまたはプリンシパルに FHIR データの読み取りを許可するロールです。[詳細](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/read | FHIR リソースを読み取ります (検索とバージョン管理された履歴を含みます)。  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "name": "4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-writer"></a>FHIR データ ライター

ユーザーまたはプリンシパルに FHIR データの読み取りと書き込みを許可するロールです。[詳細](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/* |  |
> | **NotDataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/hardDelete/action | ハード削除 (バージョン履歴を含みます)。 |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and write FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3f88fce4-5892-4214-ae73-ba5294559913",
  "name": "3f88fce4-5892-4214-ae73-ba5294559913",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*"
      ],
      "notDataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/hardDelete/action"
      ]
    }
  ],
  "roleName": "FHIR Data Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-contributor"></a>統合サービス環境の共同作成者

統合サービス環境を管理できますが、それらにアクセスすることはできません。 [詳細情報](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/* |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage integration service environments, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "name": "a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-developer"></a>統合サービス環境の開発者

開発者が統合サービス環境でワークフロー、統合アカウント、および API 接続を作成および更新することを許可します。 [詳細情報](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/read | 統合サービス環境を読み取ります。 |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/*/join/action |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows developers to create and update workflows, integration accounts and API connections in integration service environments.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "name": "c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/read",
        "Microsoft.Logic/integrationServiceEnvironments/*/join/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="intelligent-systems-account-contributor"></a>Intelligent Systems Account Contributor

Intelligent Systems のアカウントを管理できます。ただし、それらへのアクセスは含まれません。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | Microsoft.IntelligentSystems/accounts/* | Intelligent Systems アカウントの作成と管理 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Intelligent Systems accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/03a6d094-3444-4b3d-88af-7477090a9e5e",
  "name": "03a6d094-3444-4b3d-88af-7477090a9e5e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.IntelligentSystems/accounts/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Intelligent Systems Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-contributor"></a>Logic App Contributor

ロジック アプリを管理できますが、アクセス権を変更することはできません。 [詳細情報](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | ストレージ アカウントのアクセス キーを一覧表示します。 |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/read | 特定のアカウントのストレージ アカウントを返します。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | 分析サーバーの診断の設定の作成、更新、または読み取りを行います |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logdefinitions/* | このアクセス許可は、ポータルを使用してアクティビティ ログにアクセスする必要があるユーザーに必要です。 アクティビティ ログのログのカテゴリを一覧表示します。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/* | メトリック定義 (リソースの使用可能なメトリックの種類の一覧) を読み取ります。 |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/* | Logic Apps リソースを管理します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | サブスクリプション操作の結果を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | 指定されたストレージ アカウントのアクセス キーを返します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connectionGateways/* | 接続ゲートウェイを作成および管理します。 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connections/* | 接続を作成および管理します。 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/customApis/* | カスタム API を作成および管理します。 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/join/action | App Service プランに参加します。 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | App Service プランのプロパティを取得します。 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/sites/functions/listSecrets/action | 関数のシークレットを一覧表示します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage logic app, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "name": "87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logdefinitions/*",
        "Microsoft.Insights/metricDefinitions/*",
        "Microsoft.Logic/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*",
        "Microsoft.Web/connections/*",
        "Microsoft.Web/customApis/*",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/functions/listSecrets/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-operator"></a>Logic App Operator

ロジック アプリの読み取り、有効化、無効化ができますが、編集または更新はできません。 [詳細情報](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/*/read | Insights のアラート ルールを読み取ります |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/*/read | Logic Apps の診断設定を取得します |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/*/read | Logic Apps の利用可能なメトリックを取得します。 |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/*/read | Logic Apps リソースを読み取ります。 |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/disable/action | ワークフローを無効にします。 |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/enable/action | ワークフローを有効にします。 |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/validate/action | ワークフローを検証します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | デプロイ操作を取得または一覧表示します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | サブスクリプション操作の結果を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connectionGateways/*/read | 接続ゲートウェイを読み取ります。 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connections/*/read | 接続を読み取ります。 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/customApis/*/read | カスタム API を読み取ります。 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | App Service プランのプロパティを取得します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read, enable and disable logic app.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "name": "515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*/read",
        "Microsoft.Insights/metricAlerts/*/read",
        "Microsoft.Insights/diagnosticSettings/*/read",
        "Microsoft.Insights/metricDefinitions/*/read",
        "Microsoft.Logic/*/read",
        "Microsoft.Logic/workflows/disable/action",
        "Microsoft.Logic/workflows/enable/action",
        "Microsoft.Logic/workflows/validate/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*/read",
        "Microsoft.Web/connections/*/read",
        "Microsoft.Web/customApis/*/read",
        "Microsoft.Web/serverFarms/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="identity"></a>ID


### <a name="managed-identity-contributor"></a>Managed Identity Contributor

ユーザー割り当て ID の作成、読み取り、更新、削除を行います。[詳細](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/read | 既存のユーザー割り当て ID を取得します。 |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/write | 新しいユーザー割り当て ID を作成するか、既存のユーザー割り当て ID に関連付けられているタグを更新します。 |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/delete | 既存のユーザー割り当て ID を削除します。 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "name": "e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/write",
        "Microsoft.ManagedIdentity/userAssignedIdentities/delete",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-identity-operator"></a>Managed Identity Operator

ユーザー割り当て ID の読み取りと割り当てを行います。[詳細](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/read |  |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/assign/action |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and Assign User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f1a07417-d97a-45cb-824c-7a7467783830",
  "name": "f1a07417-d97a-45cb-824c-7a7467783830",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="security"></a>セキュリティ


### <a name="attestation-contributor"></a>Attestation Contributor

構成証明プロバイダー インスタンスの読み取り、書き込み、または削除ができます。[詳細](../attestation/quickstart-powershell.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Attestation/attestationProviders/attestation/read |  |
> | Microsoft.Attestation/attestationProviders/attestation/write |  |
> | Microsoft.Attestation/attestationProviders/attestation/delete |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read write or delete the attestation provider instance",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/bbf86eb8-f7b4-4cce-96e4-18cddf81d86e",
  "name": "bbf86eb8-f7b4-4cce-96e4-18cddf81d86e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Attestation/attestationProviders/attestation/read",
        "Microsoft.Attestation/attestationProviders/attestation/write",
        "Microsoft.Attestation/attestationProviders/attestation/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Attestation Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="attestation-reader"></a>Attestation Reader

構成証明プロバイダーのプロパティを読み取ることができます。[詳細](../attestation/troubleshoot-guide.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Attestation/attestationProviders/attestation/read |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read the attestation provider properties",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fd1bd22b-8476-40bc-a0bc-69b95687b9f3",
  "name": "fd1bd22b-8476-40bc-a0bc-69b95687b9f3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Attestation/attestationProviders/attestation/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Attestation Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-contributor"></a>Azure Sentinel 共同作成者

Azure Sentinel 共同作成者です。[詳細](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/* |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | 新しいエンジンを使用して検索します。 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Log Analytics のデータの表示 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/* |  |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | 既存の OMS ソリューションを取得します。 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | ワークスペース内のデータに対してクエリを実行します |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | ワークスペースのデータソースを取得します。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/read | プライベート ブックが読み取られます |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ab8e14d6-4a74-4a29-9ba8-549422addade",
  "name": "ab8e14d6-4a74-4a29-9ba8-549422addade",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Insights/myworkbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-reader"></a>Azure Sentinel 閲覧者

Azure Sentinel 閲覧者です。[詳細](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/read |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/action | ユーザーの承認とライセンスを確認します |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatintelligence/indicators/query/action | 脅威インテリジェンス インジケーターをクエリします |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/action | 脅威インテリジェンス インジケーターをクエリします |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | 新しいエンジンを使用して検索します。 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Log Analytics のデータの表示 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/LinkedServices/read | 指定されたワークスペースのリンクされたサービスを取得します。 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/read | 保存された検索クエリを取得します。 |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | 既存の OMS ソリューションを取得します。 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | ワークスペース内のデータに対してクエリを実行します |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | ワークスペースのデータソースを取得します。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | ブックを読み取ります |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/read | プライベート ブックが読み取られます |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "name": "8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/query/action",
        "Microsoft.SecurityInsights/threatIntelligence/queryIndicators/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/LinkedServices/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Insights/myworkbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-responder"></a>Azure Sentinel レスポンダー

Azure Sentinel レスポンダーです。[詳細](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/read |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/action | ユーザーの承認とライセンスを確認します |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/automationRules/* |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/cases/* |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/incidents/* |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/appendTags/action | 脅威インテリジェンス インジケーターにタグを追加します |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatintelligence/indicators/query/action | 脅威インテリジェンス インジケーターをクエリします |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/bulkTag/action | 脅威インテリジェンスに一括でタグを付けます |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/appendTags/action | 脅威インテリジェンス インジケーターにタグを追加します |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/replaceTags/action | 脅威インテリジェンス インジケーターのタグを置換します |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/action | 脅威インテリジェンス インジケーターをクエリします |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | 新しいエンジンを使用して検索します。 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Log Analytics のデータの表示 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | ワークスペースのデータソースを取得します。 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/read | 保存された検索クエリを取得します。 |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | 既存の OMS ソリューションを取得します。 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | ワークスペース内のデータに対してクエリを実行します |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | ワークスペースのデータソースを取得します。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | ブックを読み取ります |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/read | プライベート ブックが読み取られます |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/cases/*/Delete |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/incidents/*/Delete |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Responder",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "name": "3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/automationRules/*",
        "Microsoft.SecurityInsights/cases/*",
        "Microsoft.SecurityInsights/incidents/*",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/appendTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/query/action",
        "Microsoft.SecurityInsights/threatIntelligence/bulkTag/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/appendTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/replaceTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/queryIndicators/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Insights/myworkbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.SecurityInsights/cases/*/Delete",
        "Microsoft.SecurityInsights/incidents/*/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Responder",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-administrator"></a>Key Vault Administrator

キー コンテナーとその内部にあるすべてのオブジェクト (証明書、キー、シークレットを含む) に対して、すべてのデータ プレーン操作を実行します。 キー コンテナー リソースの管理やロール割り当ての管理はできません。 「Azure ロールベースのアクセス制御」アクセス許可モデルを使用するキー コンテナーでのみ機能します。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Key Vault 名が有効であり、使用されていないことを確認します。 |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | 論理的に削除された Key Vault のプロパティを表示します。 |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Microsoft.KeyVault リソース プロバイダーで使用できる操作を一覧表示します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/* |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform all data plane operations on a key vault and all objects in it, including certificates, keys, and secrets. Cannot manage key vault resources or manage role assignments. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "name": "00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-certificates-officer"></a>Key Vault Certificates Officer

キーコンテナーの証明書に対して、アクセス許可の管理を除く任意の操作を実行します。 「Azure ロールベースのアクセス制御」アクセス許可モデルを使用するキー コンテナーでのみ機能します。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Key Vault 名が有効であり、使用されていないことを確認します。 |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | 論理的に削除された Key Vault のプロパティを表示します。 |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Microsoft.KeyVault リソース プロバイダーで使用できる操作を一覧表示します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/certificatecas/* |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/certificates/* |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the certificates of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4417e6f-fecd-4de8-b567-7b0420556985",
  "name": "a4417e6f-fecd-4de8-b567-7b0420556985",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/certificatecas/*",
        "Microsoft.KeyVault/vaults/certificates/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Certificates Officer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-contributor"></a>Key Vault Contributor

キー コンテナーを管理しますが、Azure RBAC でのロール割り当ては許可されず、シークレット、キー、証明書へのアクセスも許可されません。 [詳細情報](../key-vault/general/secure-your-key-vault.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/deletedVaults/purge/action | 論理的に削除された Key Vault を消去します。 |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/hsmPools/* |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/managedHsms/* |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage key vaults, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f25e0fa2-a7c8-4377-a976-54943a77a395",
  "name": "f25e0fa2-a7c8-4377-a976-54943a77a395",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.KeyVault/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.KeyVault/locations/deletedVaults/purge/action",
        "Microsoft.KeyVault/hsmPools/*",
        "Microsoft.KeyVault/managedHsms/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-officer"></a>Key Vault Crypto Officer

キーコンテナーのキーに対して、アクセス許可の管理を除く任意の操作を実行します。 「Azure ロールベースのアクセス制御」アクセス許可モデルを使用するキー コンテナーでのみ機能します。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Key Vault 名が有効であり、使用されていないことを確認します。 |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | 論理的に削除された Key Vault のプロパティを表示します。 |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Microsoft.KeyVault リソース プロバイダーで使用できる操作を一覧表示します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/* |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the keys of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "name": "14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Officer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-service-encryption-user"></a>Key Vault Crypto Service Encryption User

キーのメタデータを読み取り、wrap および unwrap 操作を実行します。 「Azure ロールベースのアクセス制御」アクセス許可モデルを使用するキー コンテナーでのみ機能します。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/write | eventSubscription を作成または更新します。 |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/read | eventSubscription を削除します。 |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/delete | eventSubscription を削除します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/read | 指定された資格情報コンテナー内のキーを一覧表示するか、キーのプロパティおよび公開マテリアルを読み取ります。 非対称キーの場合、この操作では公開キーを公開し、署名の暗号化や検証などの公開キー アルゴリズムを実行する機能が含まれます。 秘密キーと対称キーが公開されることはありません。 |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/wrap/action | Key Vault キーで対称キーをラップします。 Key Vault キーが非対称の場合は、この操作を読み取りアクセス権を持つプリンシパルで実行できることに注意してください。 |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/unwrap/action | Key Vault キーで対称キーをラップ解除します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read metadata of keys and perform wrap/unwrap operations. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "name": "e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventGrid/eventSubscriptions/write",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/eventSubscriptions/delete"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Service Encryption User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-user"></a>Key Vault Crypto User

キーを使用した暗号化操作を実行します。 「Azure ロールベースのアクセス制御」アクセス許可モデルを使用するキー コンテナーでのみ機能します。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/read | 指定された資格情報コンテナー内のキーを一覧表示するか、キーのプロパティおよび公開マテリアルを読み取ります。 非対称キーの場合、この操作では公開キーを公開し、署名の暗号化や検証などの公開キー アルゴリズムを実行する機能が含まれます。 秘密キーと対称キーが公開されることはありません。 |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/update/action | 特定のキーに関連付けられている、指定された属性を更新します。 |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/backup/action | キーのバックアップ ファイルを作成します。 このファイルは、同じサブスクリプションの Key Vault でキーを復元するために使用できます。 制限が適用される場合があります。 |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/encrypt/action | キーでプレーンテキストを暗号化します。 キーが非対称の場合は、この操作を読み取りアクセス権を持つプリンシパルで実行できることに注意してください。 |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/decrypt/action | キーで暗号化テキストの暗号化を解除します。 |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/wrap/action | Key Vault キーで対称キーをラップします。 Key Vault キーが非対称の場合は、この操作を読み取りアクセス権を持つプリンシパルで実行できることに注意してください。 |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/unwrap/action | Key Vault キーで対称キーをラップ解除します。 |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/sign/action | キーでメッセージ ダイジェスト (ハッシュ) に署名します。 |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/verify/action | キーでメッセージ ダイジェスト (ハッシュ) の署名を検証します。 キーが非対称の場合は、この操作を読み取りアクセス権を持つプリンシパルで実行できることに注意してください。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform cryptographic operations using keys. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/12338af0-0e69-4776-bea7-57ae8d297424",
  "name": "12338af0-0e69-4776-bea7-57ae8d297424",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/update/action",
        "Microsoft.KeyVault/vaults/keys/backup/action",
        "Microsoft.KeyVault/vaults/keys/encrypt/action",
        "Microsoft.KeyVault/vaults/keys/decrypt/action",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action",
        "Microsoft.KeyVault/vaults/keys/sign/action",
        "Microsoft.KeyVault/vaults/keys/verify/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-reader"></a>Key Vault Reader

キー コンテナーとその証明書、キー、シークレットのメタデータを読み取ります。 シークレット コンテンツやキー マテリアルなどの機密値を読み取ることはできません。 「Azure ロールベースのアクセス制御」アクセス許可モデルを使用するキー コンテナーでのみ機能します。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Key Vault 名が有効であり、使用されていないことを確認します。 |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | 論理的に削除された Key Vault のプロパティを表示します。 |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Microsoft.KeyVault リソース プロバイダーで使用できる操作を一覧表示します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/readMetadata/action | シークレットの値ではなく、プロパティを一覧表示または表示します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read metadata of key vaults and its certificates, keys, and secrets. Cannot read sensitive values such as secret contents or key material. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/21090545-7ca7-4776-b22c-e363652d74d2",
  "name": "21090545-7ca7-4776-b22c-e363652d74d2",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-officer"></a>Key Vault Secrets Officer

キーコンテナーのシークレットに対して、アクセス許可の管理を除く任意の操作を実行します。 「Azure ロールベースのアクセス制御」アクセス許可モデルを使用するキー コンテナーでのみ機能します。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Key Vault 名が有効であり、使用されていないことを確認します。 |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | 論理的に削除された Key Vault のプロパティを表示します。 |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Microsoft.KeyVault リソース プロバイダーで使用できる操作を一覧表示します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/* |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the secrets of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "name": "b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets Officer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-user"></a>Key Vault Secrets User

シークレット コンテンツを読み取ります。 「Azure ロールベースのアクセス制御」アクセス許可モデルを使用するキー コンテナーでのみ機能します。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/getSecret/action | シークレットの値を取得します。 |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/readMetadata/action | シークレットの値ではなく、プロパティを一覧表示または表示します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read secret contents. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4633458b-17de-408a-b874-0445c86b69e6",
  "name": "4633458b-17de-408a-b874-0445c86b69e6",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/getSecret/action",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-hsm-contributor"></a>Managed HSM contributor

マネージド HSM プールを管理できます。ただし、それらへのアクセスは含まれません。 [詳細情報](../key-vault/managed-hsm/secure-your-managed-hsm.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/managedHSMs/* |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage managed HSM pools, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18500a29-7fe2-46b2-a342-b16a415e101d",
  "name": "18500a29-7fe2-46b2-a342-b16a415e101d",
  "permissions": [
    {
      "actions": [
        "Microsoft.KeyVault/managedHSMs/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed HSM contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-admin"></a>セキュリティ管理者

Security Center の表示および更新のアクセス許可。 セキュリティ閲覧者と同じアクセス許可があり、セキュリティ ポリシーの更新、アラートと推奨事項の無視も可能になります。 [詳細情報](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyAssignments/* | ポリシーの割り当ての作成と管理 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyDefinitions/* | ポリシー定義の作成と管理 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyExemptions/* | ポリシー適用除外の作成と管理 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policySetDefinitions/* | ポリシー セットの作成と管理 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | 認証済みユーザーの管理グループを一覧表示します。 |
> | [Microsoft.operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Log Analytics のデータの表示 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/* | セキュリティ コンポーネントおよびポリシーの作成と管理 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Admin Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "name": "fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Authorization/policyAssignments/*",
        "Microsoft.Authorization/policyDefinitions/*",
        "Microsoft.Authorization/policyExemptions/*",
        "Microsoft.Authorization/policySetDefinitions/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-assessment-contributor"></a>Security Assessment Contributor

評価を Security Center にプッシュできます

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/assessments/write | サブスクリプションで利用可能なセキュリティ評価を作成または更新します |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you push assessments to Security Center",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "name": "612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Security/assessments/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Assessment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-manager-legacy"></a>セキュリティ マネージャー (レガシ)

これは、レガシ ロールです。 代わりに Security Admin を使用してください。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/*/read | 従来の仮想マシンの構成情報の読み取り |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/*/write | 従来の仮想マシンの構成の書き込み |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/*/read | 従来のネットワークに関する構成情報の読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/* | セキュリティ コンポーネントおよびポリシーの作成と管理 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "This is a legacy role. Please use Security Administrator instead",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "name": "e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/*/read",
        "Microsoft.ClassicCompute/virtualMachines/*/write",
        "Microsoft.ClassicNetwork/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Manager (Legacy)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-reader"></a>セキュリティ閲覧者

Security Center の表示アクセス許可。 推奨事項、警告、セキュリティ ポリシー、セキュリティの状態を閲覧できますが、変更することはできません。 [詳細情報](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | クラシック メトリック アラートを読み取ります |
> | [Microsoft.operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Log Analytics のデータの表示 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/*/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/*/read | セキュリティ コンポーネントとポリシーの読み取り |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/*/read |  |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/iotDefenderSettings/packageDownloads/action | ダウンロード可能な IoT Defender パッケージ情報を取得します |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/iotDefenderSettings/downloadManagerActivation/action | サブスクリプション クォータ データを含むマネージャー アクティブ化ファイルをダウンロードします |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/iotSensors/downloadResetPassword/action | IoT センサーのリセット パスワード ファイルをダウンロードします |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | 認証済みユーザーの管理グループを一覧表示します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "name": "39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*/read",
        "Microsoft.Support/*/read",
        "Microsoft.Security/iotDefenderSettings/packageDownloads/action",
        "Microsoft.Security/iotDefenderSettings/downloadManagerActivation/action",
        "Microsoft.Security/iotSensors/downloadResetPassword/action",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="devops"></a>DevOps


### <a name="devtest-labs-user"></a>DevTest Labs User

Azure DevTest Labs で仮想マシンの接続、起動、再起動、シャットダウンができます。 [詳細情報](../devtest-labs/devtest-lab-add-devtest-user.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/read | 可用性セットのプロパティを取得します。 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read | 仮想マシンのプロパティ (VM サイズ、実行時の状態、VM 拡張機能など) の読み取り |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/deallocate/action | 仮想マシンを電源オフにし、コンピューティング リソースを解放します。 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/read | 仮想マシンのプロパティを取得します。 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/restart/action | 仮想マシンを再起動します。 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/start/action | 仮想マシンを起動します。 |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/*/read | ラボのプロパティの読み取り |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/claimAnyVm/action | ラボ内のランダムに要求可能な仮想マシンを要求します。 |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/createEnvironment/action | ラボで仮想マシンを作成します。 |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/ensureCurrentUserProfile/action | 現在のユーザーがラボで有効なプロファイルを保有していることを確認します。 |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/delete | 数式を削除します。 |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/read | 数式を読み取ります。 |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/write | 数式を追加または変更します。 |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/policySets/evaluatePolicies/action | ラボ ポリシーを評価します。 |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualMachines/claim/action | 既存の仮想マシンの所有権を取得します。 |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualmachines/listApplicableSchedules/action | 存在する場合は、該当する開始/停止のスケジュールを一覧表示します。 |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualMachines/getRdpFileContents/action | 仮想マシンの RDP ファイルの内容を表す文字列を取得します。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/join/action | ロード バランサーのバックエンド アドレス プールを接続します。 警告不可能です。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/join/action | ロード バランサーのインバウンド NAT 規則を接続します。 警告不可能です。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/*/read | ネットワーク インターフェイスのプロパティ (例: そのネットワーク インターフェイスが含まれているすべてのロード バランサー) の読み取り |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/join/action | 仮想マシンをネットワーク インターフェイスに接続します。 警告不可能です。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | ネットワーク インターフェイスの定義を取得します。  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/write | ネットワーク インターフェイスを作成するか、既存のネットワーク インターフェイスを更新します。  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/*/read | パブリック IP アドレスのプロパティの読み取り |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/join/action | パブリック IP アドレスに接続します。 警告不可能です。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | パブリック IP アドレス定義を取得します。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | 仮想ネットワークに参加します。 警告不可能です。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | デプロイ操作を取得または一覧表示します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | デプロイを取得または一覧表示します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | 指定されたストレージ アカウントのアクセス キーを返します。 |
> | **NotActions** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/vmSizes/read | 仮想マシンを更新する際に使用できるサイズを一覧表示します。 |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you connect, start, restart, and shutdown your virtual machines in your Azure DevTest Labs.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/76283e04-6283-4c54-8f91-bcf1374a3c64",
  "name": "76283e04-6283-4c54-8f91-bcf1374a3c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/read",
        "Microsoft.Compute/virtualMachines/*/read",
        "Microsoft.Compute/virtualMachines/deallocate/action",
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Compute/virtualMachines/restart/action",
        "Microsoft.Compute/virtualMachines/start/action",
        "Microsoft.DevTestLab/*/read",
        "Microsoft.DevTestLab/labs/claimAnyVm/action",
        "Microsoft.DevTestLab/labs/createEnvironment/action",
        "Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action",
        "Microsoft.DevTestLab/labs/formulas/delete",
        "Microsoft.DevTestLab/labs/formulas/read",
        "Microsoft.DevTestLab/labs/formulas/write",
        "Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action",
        "Microsoft.DevTestLab/labs/virtualMachines/claim/action",
        "Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action",
        "Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/networkInterfaces/*/read",
        "Microsoft.Network/networkInterfaces/join/action",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/publicIPAddresses/*/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listKeys/action"
      ],
      "notActions": [
        "Microsoft.Compute/virtualMachines/vmSizes/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DevTest Labs User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="lab-creator"></a>Lab Creator

Azure ラボ アカウントに新しいラボを作成できます。 [詳細情報](../lab-services/add-lab-creator.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/*/read |  |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/createLab/action | ラボ アカウントにラボを作成します。 |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getPricingAndAvailability/action | ラボ アカウントのサイズ、地域、およびオペレーティング システムの組み合わせの価格と可用性を取得します。 |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getRestrictionsAndUsage/action | このサブスクリプションのコアに関する制限と使用状況が取得されます |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create new labs under your Azure Lab Accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "name": "b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.LabServices/labAccounts/*/read",
        "Microsoft.LabServices/labAccounts/createLab/action",
        "Microsoft.LabServices/labAccounts/getPricingAndAvailability/action",
        "Microsoft.LabServices/labAccounts/getRestrictionsAndUsage/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Lab Creator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="monitor"></a>モニター


### <a name="application-insights-component-contributor"></a>Application Insights Component Contributor

Application Insights コンポーネントを管理できます。[詳細](../azure-monitor/app/resources-roles-access-control.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 従来のアラート ルールの作成と管理 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/generateLiveToken/read | Live Metrics のトークンを取得 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* | 新しいアラート ルールの作成と管理 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/* | Insights コンポーネントの作成と管理 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/topology/read | トポロジを読み取ります |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/transactions/read | トランザクションを読み取ります |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Insights Web テストの作成と管理 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage Application Insights components",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e",
  "name": "ae349356-3a1b-4a5e-921d-050484c6347e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/generateLiveToken/read",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/topology/read",
        "Microsoft.Insights/transactions/read",
        "Microsoft.Insights/webtests/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Component Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="application-insights-snapshot-debugger"></a>Application Insights Snapshot Debugger

Application Insights スナップショット デバッガーで収集されたデバック スナップショットの表示とダウンロードを実行できるアクセス許可をユーザーに与えます。 これらのアクセス許可は、[所有者](#owner)ロールまたは[共同作成者](#contributor)ロールには含まれないことに注意してください。 ユーザーに Application Insights スナップショット デバッガー ロールを与える場合は、そのロールをユーザーに直接付与する必要があります。 このロールは、カスタム ロールに追加されるときに認識されません。 [詳細情報](../azure-monitor/app/snapshot-debugger.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/*/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives user permission to use Application Insights Snapshot Debugger features",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "name": "08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Snapshot Debugger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-contributor"></a>Monitoring Contributor

すべての監視データを読み取り、監視設定を編集できます。 [「Azure Monitor での役割、アクセス許可、およびセキュリティの概要」](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles)も参照してください。 [詳細情報](../azure-monitor/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | */read | 機密データを除くあらゆる種類のリソースの読み取り |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alerts/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alertsSummary/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/actiongroups/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/activityLogAlerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/AlertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/* | Insights コンポーネントの作成と管理 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRuleAssociations/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/DiagnosticSettings/* | 分析サーバーの診断の設定の作成、更新、または読み取りを行います |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/eventtypes/* | サブスクリプションのアクティビティ ログのイベント (管理イベント) を一覧表示します。 このアクセス許可は、アクティビティ ログへのプログラムによるアクセスとポータル アクセスの両方に適用されます。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/LogDefinitions/* | このアクセス許可は、ポータルを使用してアクティビティ ログにアクセスする必要があるユーザーに必要です。 アクティビティ ログのログのカテゴリを一覧表示します。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricalerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/* | メトリック定義 (リソースの使用可能なメトリックの種類の一覧) を読み取ります。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/* | リソースのメトリックを読み取ります。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | Microsoft Insights プロバイダーを登録します。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Insights Web テストの作成と管理 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopes/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopeOperationStatuses/* |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/write | 新しいワークスペースを作成するか、既存のワークスペースの顧客 ID を指定して既存のワークスペースにリンクします。 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/intelligencepacks/* | Log Analytics ソリューション パックの読み取り/書き込み/削除を行います。 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/* | Log Analytics によって保存された検索の読み取り/書き込み/削除を行います。 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | 検索クエリを実行します。 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/action | ワークスペースの共有キーを取得します。 これらのキーを使用して、Microsoft Operational Insights エージェントをワークスペースに接続します。 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/storageinsightconfigs/* | Log Analytics ストレージ インサイト構成の読み取り/書き込み/削除を行います。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | [Microsoft.WorkloadMonitor](resource-provider-operations.md#microsoftworkloadmonitor)/monitors/* | ゲスト VM の正常性モニターに関する情報を取得します。 |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartDetectorAlertRules/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/actionRules/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartGroups/* |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data and update monitoring settings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "name": "749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.AlertsManagement/alerts/*",
        "Microsoft.AlertsManagement/alertsSummary/*",
        "Microsoft.Insights/actiongroups/*",
        "Microsoft.Insights/activityLogAlerts/*",
        "Microsoft.Insights/AlertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/dataCollectionRules/*",
        "Microsoft.Insights/dataCollectionRuleAssociations/*",
        "Microsoft.Insights/DiagnosticSettings/*",
        "Microsoft.Insights/eventtypes/*",
        "Microsoft.Insights/LogDefinitions/*",
        "Microsoft.Insights/metricalerts/*",
        "Microsoft.Insights/MetricDefinitions/*",
        "Microsoft.Insights/Metrics/*",
        "Microsoft.Insights/Register/Action",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Insights/privateLinkScopes/*",
        "Microsoft.Insights/privateLinkScopeOperationStatuses/*",
        "Microsoft.OperationalInsights/workspaces/write",
        "Microsoft.OperationalInsights/workspaces/intelligencepacks/*",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.OperationalInsights/workspaces/sharedKeys/action",
        "Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*",
        "Microsoft.Support/*",
        "Microsoft.WorkloadMonitor/monitors/*",
        "Microsoft.AlertsManagement/smartDetectorAlertRules/*",
        "Microsoft.AlertsManagement/actionRules/*",
        "Microsoft.AlertsManagement/smartGroups/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-metrics-publisher"></a>監視メトリック パブリッシャー

Azure リソースに対するメトリックの公開を有効にします。[詳細](../azure-monitor/insights/container-insights-update-metrics.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | Microsoft Insights プロバイダーを登録します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Write | メトリックを書き込みます。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Enables publishing metrics against Azure resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c390055eb",
  "name": "3913510d-42f4-4e42-8a64-420c390055eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/Register/Action",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Insights/Metrics/Write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Metrics Publisher",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-reader"></a>Monitoring Reader

すべての監視データ (メトリック、ログなど) を読み取ることができます。 [「Azure Monitor での役割、アクセス許可、およびセキュリティの概要」](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles)も参照してください。 [詳細情報](../azure-monitor/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | */read | 機密データを除くあらゆる種類のリソースの読み取り |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | 検索クエリを実行します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "name": "43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-contributor"></a>Workbook Contributor

共有ブックを保存できます。 [詳細情報](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/write | ブックを作成または更新します |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/delete | ブックを削除します |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | ブックを読み取ります |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can save shared workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "name": "e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/workbooks/write",
        "Microsoft.Insights/workbooks/delete",
        "Microsoft.Insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-reader"></a>Workbook Reader

ブックの読み取りが可能です。 [詳細情報](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [microsoft.insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | ブックを読み取ります |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "name": "b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "permissions": [
    {
      "actions": [
        "microsoft.insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management--governance"></a>管理 + ガバナンス


### <a name="automation-job-operator"></a>Automation ジョブ オペレーター

Automation Runbook を使用してジョブを作成および管理します。 [詳細情報](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/read | Hybrid Runbook Worker リソースを読み取ります。 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/read | Azure Automation ジョブを取得します。 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/resume/action | Azure Automation ジョブを再開します。 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/stop/action | Azure Automation ジョブを停止します。 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/streams/read | Azure Automation ジョブ ストリームを取得します。 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/suspend/action | Azure Automation ジョブを中断します。 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/write | Azure Automation ジョブを作成します。 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/output/read | ジョブの出力を取得します。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and Manage Jobs using Automation Runbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "name": "4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Job Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-operator"></a>Automation Operator

Automation オペレーターは、ジョブを開始、停止、中断、および再開できます。[詳細](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/read | Hybrid Runbook Worker リソースを読み取ります。 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/read | Azure Automation ジョブを取得します。 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/resume/action | Azure Automation ジョブを再開します。 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/stop/action | Azure Automation ジョブを停止します。 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/streams/read | Azure Automation ジョブ ストリームを取得します。 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/suspend/action | Azure Automation ジョブを中断します。 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/write | Azure Automation ジョブを作成します。 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/read | Azure Automation ジョブ スケジュールを取得します。 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/write | Azure Automation ジョブ スケジュールを作成します。 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/linkedWorkspace/read | Automation アカウントにリンクされているワークスペースを取得します |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/read | Azure Automation アカウントを取得します。 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/runbooks/read | Azure Automation Runbook を取得します。 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/schedules/read | Azure Automation スケジュール資産を取得します。 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/schedules/write | Azure Automation スケジュール資産を作成または更新します。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/output/read | ジョブの出力を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Automation Operators are able to start, stop, suspend, and resume jobs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d3881f73-407a-4167-8283-e981cbba0404",
  "name": "d3881f73-407a-4167-8283-e981cbba0404",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobSchedules/read",
        "Microsoft.Automation/automationAccounts/jobSchedules/write",
        "Microsoft.Automation/automationAccounts/linkedWorkspace/read",
        "Microsoft.Automation/automationAccounts/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Automation/automationAccounts/schedules/read",
        "Microsoft.Automation/automationAccounts/schedules/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-runbook-operator"></a>Automation Runbook オペレーター

Runbook のジョブを作成する方法については、Runbook のプロパティを参照してください。 [詳細情報](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/runbooks/read | Azure Automation Runbook を取得します。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read Runbook properties - to be able to create Jobs of the runbook.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "name": "5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Runbook Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-onboarding"></a>Azure Connected Machine のオンボード

Azure Connected Machine をオンボードできます。 [詳細情報](../azure-arc/servers/onboard-service-principal.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/read | Azure Arc マシンを読み取ります |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/write | Azure Arc マシンが書き込まれます |
> | [Microsoft.GuestConfiguration](resource-provider-operations.md#microsoftguestconfiguration)/guestConfigurationAssignments/read | ゲスト構成の割り当てを取得します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "name": "b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.GuestConfiguration/guestConfigurationAssignments/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-resource-administrator"></a>Azure Connected Machine のリソース管理者

Azure Connected Machine の読み取り、書き込み、削除、再オンボードを実行できます。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/read | Azure Arc マシンを読み取ります |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/write | Azure Arc マシンが書き込まれます |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/delete | Azure Arc マシンが削除されます |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/reconnect/action |  |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/extensions/write | Azure Arc 拡張機能をインストールまたは更新されます |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/*/read |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read, write, delete and re-onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cd570a14-e51a-42ad-bac8-bafd67325302",
  "name": "cd570a14-e51a-42ad-bac8-bafd67325302",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.HybridCompute/machines/delete",
        "Microsoft.HybridCompute/machines/reconnect/action",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.HybridCompute/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Resource Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="billing-reader"></a>Billing Reader

課金データへの読み取りアクセスを許可します。[詳細](../cost-management-billing/manage/manage-billing-access.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/*/read | 課金情報の読み取り |
> | [Microsoft.Commerce](resource-provider-operations.md#microsoftcommerce)/*/read |  |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/*/read |  |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | 認証済みユーザーの管理グループを一覧表示します。 |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/read |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to billing data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "name": "fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Billing/*/read",
        "Microsoft.Commerce/*/read",
        "Microsoft.Consumption/*/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Billing Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-contributor"></a>ブループリント共同作成者

ブループリントの定義を管理できますが、それらを割り当てることはできません。 [詳細情報](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprints/* | ブループリントの定義またはブループリント アーティファクトを作成および管理します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage blueprint definitions, but not assign them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/41077137-e803-4205-871c-5a86e6a753b4",
  "name": "41077137-e803-4205-871c-5a86e6a753b4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprints/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-operator"></a>ブループリント オペレーター

既存の発行済みのブループリントを割り当てることはできますが、ブループリントの新規作成はできません。 これは、ユーザーが割り当てたマネージド ID を使用して割り当てが行われた場合にのみ機能することに注意してください。 [詳細情報](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/* | ブループリント割り当てを作成および管理します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can assign existing published blueprints, but cannot create new blueprints. NOTE: this only works if the assignment is done with a user-assigned managed identity.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/437d2ced-4a38-4302-8479-ed2bcb43d090",
  "name": "437d2ced-4a38-4302-8479-ed2bcb43d090",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprintAssignments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-contributor"></a>Cost Management 共同作成者

コストを表示し、コストの構成 (予算、エクスポートなど) を管理できます。[詳細](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/* |  |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/* |  |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingPeriods/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | サブスクリプションの一覧を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/configurations/read | 構成の取得 |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/recommendations/read | 推奨事項を読み取ります。 |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | 認証済みユーザーの管理グループを一覧表示します。 |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingProperty/read |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view costs and manage cost configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/434105ed-43f6-45c7-a02f-909b2ba83430",
  "name": "434105ed-43f6-45c7-a02f-909b2ba83430",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*",
        "Microsoft.CostManagement/*",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Billing/billingProperty/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-reader"></a>Cost Management 閲覧者

コストのデータと構成 (予算、エクスポートなど) を表示できます。[詳細](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/*/read |  |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/read |  |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingPeriods/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | サブスクリプションの一覧を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/configurations/read | 構成の取得 |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/recommendations/read | 推奨事項を読み取ります。 |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | 認証済みユーザーの管理グループを一覧表示します。 |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingProperty/read |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view cost data and configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/72fafb9e-0641-4937-9268-a91bfd8191a3",
  "name": "72fafb9e-0641-4937-9268-a91bfd8191a3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Billing/billingProperty/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hierarchy-settings-administrator"></a>Hierarchy Settings Administrator

ユーザーに、階層設定の編集と削除を許可します

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/settings/write | 管理グループ階層の設定が作成または更新されます。 |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/settings/delete | 管理グループ階層の設定が削除されます。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows users to edit and delete Hierarchy Settings",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/350f8d15-c687-4448-8ae1-157740a3936d",
  "name": "350f8d15-c687-4448-8ae1-157740a3936d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/settings/write",
        "Microsoft.Management/managementGroups/settings/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Hierarchy Settings Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="kubernetes-cluster---azure-arc-onboarding"></a>Kubernetes クラスター - Azure Arc のオンボード

connectedClusters リソースを作成するため、あらゆるユーザーまたはサービスを承認するロール定義 [詳細](../azure-arc/kubernetes/connect-cluster.md)

> [!div class="mx-tableFixed"]
> | Actions | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | デプロイを作成または更新します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | サブスクリプション操作の結果を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | サブスクリプションの一覧を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Write | connectedClusters を書き込みます |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/read | connectedClusters を読み取ります |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role definition to authorize any user/service to create connectedClusters resource",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "name": "34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Kubernetes/connectedClusters/Write",
        "Microsoft.Kubernetes/connectedClusters/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Kubernetes Cluster - Azure Arc Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-contributor-role"></a>Managed Application Contributor Role

マネージド アプリケーション リソースの作成を許可します。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | */read | 機密データを除くあらゆる種類のリソースの読み取り |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/applications/* |  |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/register/action | ソリューションに登録します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for creating managed application resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/641177b8-a67a-45b9-a033-47bc880bb21e",
  "name": "641177b8-a67a-45b9-a033-47bc880bb21e",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/*",
        "Microsoft.Solutions/register/action",
        "Microsoft.Resources/subscriptions/resourceGroups/*",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-operator-role"></a>Managed Application Operator Role

マネージド アプリケーション リソースに対する読み取りとアクションの実行が可能です。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | */read | 機密データを除くあらゆる種類のリソースの読み取り |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/applications/read | アプリケーションの一覧を取得します。 |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/*/action |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and perform actions on Managed Application resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "name": "c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/read",
        "Microsoft.Solutions/*/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-applications-reader"></a>Managed Applications 閲覧者

マネージド アプリおよび要求 JIT アクセスでリソースを読み取ることができます。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | */read | 機密データを除くあらゆる種類のリソースの読み取り |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/jitRequests/* |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read resources in a managed app and request JIT access.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "name": "b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Solutions/jitRequests/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Applications Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-services-registration-assignment-delete-role"></a>マネージド サービスの登録割り当て削除ロール

マネージド サービスの登録割り当て削除ロールを使用すると、テナント管理ユーザーは、テナントに割り当てられている登録割り当てを削除できます。 [詳細情報](../lighthouse/how-to/remove-delegation.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/read | マネージド サービスの登録割り当ての一覧を取得します。 |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/delete | マネージド サービスの登録割り当てを削除します。 |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/operationStatuses/read | リソースの操作の状態を読み取ります。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Managed Services Registration Assignment Delete Role allows the managing tenant users to delete the registration assignment assigned to their tenant.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/91c1777a-f3dc-4fae-b103-61d183457e46",
  "name": "91c1777a-f3dc-4fae-b103-61d183457e46",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedServices/registrationAssignments/read",
        "Microsoft.ManagedServices/registrationAssignments/delete",
        "Microsoft.ManagedServices/operationStatuses/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Services Registration assignment Delete Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-contributor"></a>管理グループ共同作成者

管理グループ共同作成者ロールです。[詳細](../governance/management-groups/overview.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/delete | 管理グループを削除します。 |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | 認証済みユーザーの管理グループを一覧表示します。 |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/delete | 管理グループからサブスクリプションの関連付けを解除します。 |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/write | 既存のサブスクリプションと管理グループを関連付けます。 |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/write | 管理グループを作成または更新します。 |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/read | 指定された管理グループのサブスクリプションを一覧表示します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Contributor Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "name": "5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/delete",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/delete",
        "Microsoft.Management/managementGroups/subscriptions/write",
        "Microsoft.Management/managementGroups/write",
        "Microsoft.Management/managementGroups/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-reader"></a>管理グループ閲覧者

管理グループ閲覧者ロール

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | 認証済みユーザーの管理グループを一覧表示します。 |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/read | 指定された管理グループのサブスクリプションを一覧表示します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ac63b705-f282-497d-ac71-919bf39d939d",
  "name": "ac63b705-f282-497d-ac71-919bf39d939d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="new-relic-apm-account-contributor"></a>New Relic APM Account Contributor

New Relic Application Performance Management のアカウントとアプリケーションを管理できます。ただし、それらへのアクセスは含まれません。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | NewRelic.APM/accounts/* |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage New Relic Application Performance Management accounts and applications, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d28c62d-5b37-4476-8438-e587778df237",
  "name": "5d28c62d-5b37-4476-8438-e587778df237",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "NewRelic.APM/accounts/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "New Relic APM Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="policy-insights-data-writer-preview"></a>Policy Insights データ ライター (プレビュー)

リソース ポリシーに対する読み取りアクセスとリソース コンポーネント ポリシー イベントへの書き込みアクセスを許可します。 [詳細情報](../governance/policy/concepts/policy-for-kubernetes.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/read | ポリシー割り当てに関する情報を取得します。 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policydefinitions/read | ポリシー定義に関する情報を取得します。 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyexemptions/read | ポリシー適用除外についての情報を取得します。 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/read | ポリシー セットの定義に関する情報を取得します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/checkDataPolicyCompliance/action | 指定されたコンポーネントのコンプライアンス状態をデータ ポリシーと照合します。 |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/policyEvents/logDataEvents/action | リソース コンポーネントのポリシー イベントをログに記録します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to resource policies and write access to resource component policy events.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "name": "66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/policyassignments/read",
        "Microsoft.Authorization/policydefinitions/read",
        "Microsoft.Authorization/policyexemptions/read",
        "Microsoft.Authorization/policysetdefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.PolicyInsights/checkDataPolicyCompliance/action",
        "Microsoft.PolicyInsights/policyEvents/logDataEvents/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Policy Insights Data Writer (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reservation-purchaser"></a>Reservation Purchaser

予約を購入できるようになります。[詳細](../cost-management-billing/reservations/prepare-buy-reservation.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | サブスクリプションの一覧を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/register/action | キャパシティ リソース プロバイダーに登録し、キャパシティ リソースを作成できるようにします。 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/register/action | Microsoft.Compute リソース プロバイダーにサブスクリプションを登録します。 |
> | [Microsoft.SQL](resource-provider-operations.md#microsoftsql)/register/action | Microsoft SQL Database リソース プロバイダーにサブスクリプションを登録し、Microsoft SQL Database を作成できるようにします。 |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/register/action | 従量課金プラン RP に登録します |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/catalogs/read | 予約のカタログを読み取ります。 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleAssignments/read | ロールの割り当てに関する情報を取得します。 |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/reservationRecommendations/read | サブスクリプションの予約インスタンスに関する 1 つのレコメンデーションまたは共有のレコメンデーションの一覧を取得します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/supporttickets/write | サポート チケットの作成と更新を許可します |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you purchase reservations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f7b75c60-3036-4b75-91c3-6b41c27c1689",
  "name": "f7b75c60-3036-4b75-91c3-6b41c27c1689",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Capacity/register/action",
        "Microsoft.Compute/register/action",
        "Microsoft.SQL/register/action",
        "Microsoft.Consumption/register/action",
        "Microsoft.Capacity/catalogs/read",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Consumption/reservationRecommendations/read",
        "Microsoft.Support/supporttickets/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reservation Purchaser",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="resource-policy-contributor"></a>リソース ポリシー共同作成者

リソース ポリシーの作成または変更、サポート チケットの作成、リソースまたは階層の読み取りを行う権限を持つユーザー。 [詳細情報](../governance/policy/overview.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | */read | 機密データを除くあらゆる種類のリソースの読み取り |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/* | ポリシーの割り当ての作成と管理 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policydefinitions/* | ポリシー定義の作成と管理 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyexemptions/* | ポリシー適用除外の作成と管理 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/* | ポリシー セットの作成と管理 |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Users with rights to create/modify resource policy, create support ticket and read resources/hierarchy.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/36243c78-bf99-498c-9df9-86d9f8d28608",
  "name": "36243c78-bf99-498c-9df9-86d9f8d28608",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/policyassignments/*",
        "Microsoft.Authorization/policydefinitions/*",
        "Microsoft.Authorization/policyexemptions/*",
        "Microsoft.Authorization/policysetdefinitions/*",
        "Microsoft.PolicyInsights/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Resource Policy Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-contributor"></a>Site Recovery Contributor

資格情報コンテナーの作成とロールの割り当て以外の Site Recovery サービスを管理できます。[詳細](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp は、サービスによって使用される内部操作です。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/action | AllocateStamp は、サービスによって使用される内部操作です。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/write | "リソース証明書を更新" 操作では、リソース/コンテナー資格情報証明書を更新します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | コンテナーに関連する拡張情報の作成および管理 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | 登録済み ID の管理 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/* | レプリケーションの警告設定の作成または更新 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | イベントを読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/* | レプリケーション ファブリックの作成と管理 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/* | レプリケーション ジョブの作成と管理 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/* | レプリケーション ポリシーの作成と管理 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/* | 復旧計画の作成と管理 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/* | Recovery Services コンテナーのストレージ構成の作成と管理 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細を返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | "コンテナー トークン" 操作を使用すると、コンテナー レベルのバックエンド操作のコンテナー トークンを取得できます。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | Recovery Services コンテナーのアラートの読み取り |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationOperationStatus/read | コンテナー レプリケーション操作の状態を読み取ります |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Site Recovery service except vault creation and role assignment",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "name": "6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/*",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/*",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/*",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/*",
        "Microsoft.RecoveryServices/Vaults/storageConfig/*",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/vaults/replicationOperationStatus/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-operator"></a>Site Recovery Operator

フェールオーバーとフェールバックを実行できますが、その他の Site Recovery 管理操作は実行できません。[詳細](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp は、サービスによって使用される内部操作です。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/action | AllocateStamp は、サービスによって使用される内部操作です。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | "操作結果を取得" 操作を使用すると、非同期で送信された操作の状態と結果を取得できます。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | " コンテナーを取得" 操作を使用すると、リソースの登録済みコンテナーを取得できます。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/read | アラート設定を読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | イベントを読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/checkConsistency/action | ファブリックの一貫性を確認します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/read | ファブリックを読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/reassociateGateway/action | ゲートウェイを再関連付けします。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/renewcertificate/action | ファブリックの証明書を更新します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/read | ネットワークを読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | ネットワーク マッピングを読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/read | 保護コンテナーを読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | 保護可能な項目を読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | 復旧ポイントを適用します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | フェールオーバーのコミット。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | 計画されたフェールオーバー。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | 保護された項目を読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | レプリケーションの復旧ポイントを読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | レプリケーションを修復します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | 保護された項目を再保護します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | 保護コンテナーを切り替えます。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | テスト フェールオーバー |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | テスト フェールオーバーのクリーンアップ。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | [フェールオーバー] |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | モビリティ サービスを更新します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | 保護コンテナー マッピングを読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Recovery Services プロバイダーを読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | プロバイダーを更新します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/read | ストレージの分類を読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | ストレージ分類マッピングを読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationvCenters/read | vCenter を読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/* | レプリケーション ジョブの作成と管理 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/read | ポリシーを読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/failoverCommit/action | フェールオーバーのコミットの復旧計画。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/plannedFailover/action | 計画されたフェールオーバーの復旧計画。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/read | 復旧計画を読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/reProtect/action | 再保護の復旧計画。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/testFailover/action | テスト フェールオーバーの復旧計画。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/testFailoverCleanup/action | テスト フェールオーバーのクリーンアップの復旧計画。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/unplannedFailover/action | フェールオーバーの復旧計画。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | Recovery Services コンテナーのアラートの読み取り |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細を返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | "コンテナー トークン" 操作を使用すると、コンテナー レベルのバックエンド操作のコンテナー トークンを取得できます。 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you failover and failback but not perform other Site Recovery management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/494ae006-db33-4328-bf46-533a6560a3ca",
  "name": "494ae006-db33-4328-bf46-533a6560a3ca",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-reader"></a>Site Recovery Reader

Site Recovery の状態を表示できますが、その他の管理操作は実行できません。[詳細](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp は、サービスによって使用される内部操作です。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Recovery Services コンテナーのアラートを取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | "操作結果を取得" 操作を使用すると、非同期で送信された操作の状態と結果を取得できます。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | " コンテナーを取得" 操作を使用すると、リソースの登録済みコンテナーを取得できます。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/read | アラート設定を読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | イベントを読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/read | ファブリックを読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/read | ネットワークを読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | ネットワーク マッピングを読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/read | 保護コンテナーを読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | 保護可能な項目を読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | 保護された項目を読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | レプリケーションの復旧ポイントを読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | 保護コンテナー マッピングを読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Recovery Services プロバイダーを読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/read | ストレージの分類を読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | ストレージ分類マッピングを読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationvCenters/read | vCenter を読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/read | ジョブを読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/read | ポリシーを読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/read | 復旧計画を読み取ります |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細を返します。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | "コンテナー トークン" 操作を使用すると、コンテナー レベルのバックエンド操作のコンテナー トークンを取得できます。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view Site Recovery status but not perform other management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "name": "dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/read",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="support-request-contributor"></a>Support Request Contributor

サポート リクエストを作成して管理できます。[詳細](../azure-portal/supportability/how-to-create-azure-support-request.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create and manage Support requests",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "name": "cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Support Request Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="tag-contributor"></a>タグ共同作成者

エンティティ自体へのアクセスを提供することなく、エンティティのタグを管理できます。 [詳細情報](../azure-resource-manager/management/tag-resources.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/resources/read | リソース グループのリソースを取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resources/read | サブスクリプションのリソースを取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/tags/* |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage tags on entities, without providing access to the entities themselves.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "name": "4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
        "Microsoft.Resources/subscriptions/resources/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/tags/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Tag Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="other"></a>その他


### <a name="azure-digital-twins-data-owner"></a>Azure Digital Twins データ所有者

Digital Twins データプレーンのフル アクセス ロール [詳細情報](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/* | 任意のイベント ルートの読み取り、削除、作成、更新 |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/* | 任意のデジタル ツインの読み取り、作成、更新、削除 |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/commands/* | デジタル ツインでの任意のコマンドの呼び出し |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/relationships/* | 任意のデジタル ツイン関係の読み取り、作成、更新、削除 |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/models/* | 任意のモデルの読み取り、作成、更新、削除 |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/query/* | 任意のデジタル ツイン グラフのクエリ |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access role for Digital Twins data-plane",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/bcd981a7-7f74-457b-83e1-cceb9e632ffe",
  "name": "bcd981a7-7f74-457b-83e1-cceb9e632ffe",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DigitalTwins/eventroutes/*",
        "Microsoft.DigitalTwins/digitaltwins/*",
        "Microsoft.DigitalTwins/digitaltwins/commands/*",
        "Microsoft.DigitalTwins/digitaltwins/relationships/*",
        "Microsoft.DigitalTwins/models/*",
        "Microsoft.DigitalTwins/query/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Digital Twins Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-digital-twins-data-reader"></a>Azure Digital Twins データ リーダー

Digital Twins データプレーン プロパティの読み取り専用ロール [詳細](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/read | 任意のデジタル ツインの読み取り |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/relationships/read | 任意のデジタル ツイン関係の読み取り |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/read | 任意のイベント ルートの読み取り |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/models/read | 任意のモデルの読み取り |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/query/action | 任意のデジタル ツイン グラフのクエリ |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only role for Digital Twins data-plane properties",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d57506d4-4c8d-48b1-8587-93c323f6a5a3",
  "name": "d57506d4-4c8d-48b1-8587-93c323f6a5a3",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DigitalTwins/digitaltwins/read",
        "Microsoft.DigitalTwins/digitaltwins/relationships/read",
        "Microsoft.DigitalTwins/eventroutes/read",
        "Microsoft.DigitalTwins/models/read",
        "Microsoft.DigitalTwins/query/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Digital Twins Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="biztalk-contributor"></a>BizTalk Contributor

BizTalk Services を管理できます。ただし、それらへのアクセスは含まれません。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | Microsoft.BizTalkServices/BizTalk/* | BizTalk Services の作成と管理 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage BizTalk services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "name": "5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BizTalkServices/BizTalk/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "BizTalk Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-application-group-contributor"></a>Desktop Virtualization Application Group Contributor

デスクトップ仮想化アプリケーション グループの共同作成者。 [詳細情報](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/* |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Hostpools を読み取ります |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/read | hostpools/sessionhosts を読み取ります |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of the Desktop Virtualization Application Group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86240b0e-9422-4c43-887b-b61143f32ba8",
  "name": "86240b0e-9422-4c43-887b-b61143f32ba8",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/applicationgroups/*",
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Application Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-application-group-reader"></a>Desktop Virtualization Application Group Reader

デスクトップ仮想化アプリケーション グループの閲覧者。 [詳細情報](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/*/read |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/read | applicationgroups を読み取ります |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Hostpools を読み取ります |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/read | hostpools/sessionhosts を読み取ります |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | デプロイを取得または一覧表示します。 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | クラシック メトリック アラートを読み取ります |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of the Desktop Virtualization Application Group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aebf23d0-b568-4e86-b8f9-fe83a2c6ab55",
  "name": "aebf23d0-b568-4e86-b8f9-fe83a2c6ab55",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/applicationgroups/*/read",
        "Microsoft.DesktopVirtualization/applicationgroups/read",
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Application Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-contributor"></a>デスクトップ仮想化共同作成者

デスクトップ仮想化の共同作成者。 [詳細情報](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of Desktop Virtualization.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/082f0a83-3be5-4ba1-904c-961cca79b387",
  "name": "082f0a83-3be5-4ba1-904c-961cca79b387",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-host-pool-contributor"></a>Desktop Virtualization Host Pool Contributor

デスクトップ仮想化ホスト プールの共同作成者。 [詳細情報](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of the Desktop Virtualization Host Pool.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e307426c-f9b6-4e81-87de-d99efb3c32bc",
  "name": "e307426c-f9b6-4e81-87de-d99efb3c32bc",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Host Pool Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-host-pool-reader"></a>Desktop Virtualization Host Pool Reader

デスクトップ仮想化ホスト プールの閲覧者。 [詳細情報](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/*/read |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Hostpools を読み取ります |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | デプロイを取得または一覧表示します。 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | クラシック メトリック アラートを読み取ります |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of the Desktop Virtualization Host Pool.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ceadfde2-b300-400a-ab7b-6143895aa822",
  "name": "ceadfde2-b300-400a-ab7b-6143895aa822",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/*/read",
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Host Pool Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-reader"></a>デスクトップ仮想化閲覧者

デスクトップ仮想化の閲覧者。 [詳細情報](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/*/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | デプロイを取得または一覧表示します。 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | クラシック メトリック アラートを読み取ります |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of Desktop Virtualization.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/49a72310-ab8d-41df-bbb0-79b649203868",
  "name": "49a72310-ab8d-41df-bbb0-79b649203868",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-session-host-operator"></a>Desktop Virtualization Session Host Operator

デスクトップ仮想化セッション ホストのオペレーター。 [詳細情報](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Hostpools を読み取ります |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Operator of the Desktop Virtualization Session Host.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2ad6aaab-ead9-4eaa-8ac5-da422f562408",
  "name": "2ad6aaab-ead9-4eaa-8ac5-da422f562408",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Session Host Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-user"></a>デスクトップ仮想化ユーザー

ユーザーにアプリケーション グループ内のアプリケーションを使用することを許可します。 [詳細情報](../virtual-desktop/delegated-access-virtual-desktop.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | "*なし*" |  |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationGroups/useApplications/action | ApplicationGroup を使用します。 |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows user to use the applications in an application group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "name": "1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DesktopVirtualization/applicationGroups/useApplications/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-user-session-operator"></a>Desktop Virtualization User Session Operator

デスクトップ仮想化のユーザー セッションのオペレーター。 [詳細情報](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Hostpools を読み取ります |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/read | hostpools/sessionhosts を読み取ります |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/usersessions/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Operator of the Desktop Virtualization Uesr Session.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6",
  "name": "ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization User Session Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-workspace-contributor"></a>Desktop Virtualization Workspace Contributor

デスクトップ仮想化ワークスペースの共同作成者。 [詳細情報](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/workspaces/* |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/read | applicationgroups を読み取ります |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of the Desktop Virtualization Workspace.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/21efdde3-836f-432b-bf3d-3e8e734d4b2b",
  "name": "21efdde3-836f-432b-bf3d-3e8e734d4b2b",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/workspaces/*",
        "Microsoft.DesktopVirtualization/applicationgroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Workspace Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-workspace-reader"></a>Desktop Virtualization Workspace Reader

デスクトップ仮想化ワークスペースの閲覧者。 [詳細情報](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/workspaces/read | ワークスペースを読み取ります |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/read | applicationgroups を読み取ります |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | デプロイを取得または一覧表示します。 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | クラシック メトリック アラートを読み取ります |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of the Desktop Virtualization Workspace.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0fa44ee9-7a7d-466b-9bb2-2bf446b1204d",
  "name": "0fa44ee9-7a7d-466b-9bb2-2bf446b1204d",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/workspaces/read",
        "Microsoft.DesktopVirtualization/applicationgroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Workspace Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-backup-reader"></a>Disk Backup Reader

ディスク バックアップを実行するためにコンテナーをバックアップするアクセス許可を提供します。 [詳細情報](../backup/disk-backup-faq.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/read | ディスクのプロパティを取得します。 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/beginGetAccess/action | BLOB へのアクセス用にディスクの SAS URI を取得します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides permission to backup vault to perform disk backup.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e5e47e6-65f7-47ef-90b5-e5dd4d455f24",
  "name": "3e5e47e6-65f7-47ef-90b5-e5dd4d455f24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/beginGetAccess/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-restore-operator"></a>Disk Restore Operator

ディスクの復元を実行するためにコンテナーをバックアップするアクセス許可を提供します。 [詳細情報](../backup/restore-managed-disks.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/write | 新しいディスクを作成するか、既存のディスクを更新します。 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/read | ディスクのプロパティを取得します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides permission to backup vault to perform disk restore.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b50d9833-a0cb-478e-945f-707fcc997c13",
  "name": "b50d9833-a0cb-478e-945f-707fcc997c13",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Restore Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-snapshot-contributor"></a>Disk Snapshot Contributor

ディスプのスナップショットを管理するためにコンテナーをバックアップするアクセス許可を提供します。 [詳細情報](../backup/backup-managed-disks.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/delete | スナップショットを削除します。 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/write | 新しいスナップショットを作成するか、既存のスナップショットを更新します。 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/read | スナップショットのプロパティを取得します。 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/beginGetAccess/action | BLOB アクセス用のスナップショットの SAS URI を取得します。 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/endGetAccess/action | スナップショットの SAS URI を取り消します。 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/beginGetAccess/action | BLOB へのアクセス用にディスクの SAS URI を取得します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | 指定されたストレージ アカウントのアクセス キーを返します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/write | 指定されたパラメーターを使用してストレージ アカウントを作成するか、プロパティまたはタグを更新します。または、指定されたストレージ アカウントのカスタム ドメインを追加します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/delete | 既存のストレージ アカウントを削除します。 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides permission to backup vault to manage disk snapshots.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7efff54f-a5b4-42b5-a1c5-5411624893ce",
  "name": "7efff54f-a5b4-42b5-a1c5-5411624893ce",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Compute/snapshots/delete",
        "Microsoft.Compute/snapshots/write",
        "Microsoft.Compute/snapshots/read",
        "Microsoft.Compute/snapshots/beginGetAccess/action",
        "Microsoft.Compute/snapshots/endGetAccess/action",
        "Microsoft.Compute/disks/beginGetAccess/action",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/write",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Snapshot Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="scheduler-job-collections-contributor"></a>Scheduler Job Collections Contributor

スケジューラ ジョブ コレクションを管理できます。ただし、それらへのアクセスは含まれません。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | クラシック メトリック アラートの作成と管理 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Scheduler](resource-provider-operations.md#microsoftscheduler)/jobcollections/* | ジョブ コレクションの作成と管理 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | サポート チケットの作成と更新 |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Scheduler job collections, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "name": "188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Scheduler/jobcollections/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Scheduler Job Collections Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="services-hub-operator"></a>Services Hub Operator

Services Hub Operator を使用すると、サービス ハブ コネクタに関連するすべての読み取り、書き込み、削除の操作を実行できます。 [詳細情報](/services-hub/health/sh-connector-roles)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | ロールとロール割り当ての読み取り |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | デプロイの作成と管理 |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/write | サービス ハブ コネクタを作成または更新します |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/read | サービス ハブ コネクタを表示または一覧表示します |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/delete | サービス ハブ コネクタを削除します |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/checkAssessmentEntitlement/action | 指定されたサービス ハブ ワークスペースの評価エンタイトルメントを一覧表示します |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/supportOfferingEntitlement/read | 指定されたサービス ハブ ワークスペースのサポート オファリングのエンタイトルメントを表示します |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/workspaces/read | 指定されたユーザーのサービス ハブ ワークスペースを一覧表示します |
> | **NotActions** |  |
> | "*なし*" |  |
> | **DataActions** |  |
> | "*なし*" |  |
> | **NotDataActions** |  |
> | "*なし*" |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Services Hub Operator allows you to perform all read, write, and deletion operations related to Services Hub Connectors.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/82200a5b-e217-47a5-b665-6d8765ee745b",
  "name": "82200a5b-e217-47a5-b665-6d8765ee745b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.ServicesHub/connectors/write",
        "Microsoft.ServicesHub/connectors/read",
        "Microsoft.ServicesHub/connectors/delete",
        "Microsoft.ServicesHub/connectors/checkAssessmentEntitlement/action",
        "Microsoft.ServicesHub/supportOfferingEntitlement/read",
        "Microsoft.ServicesHub/workspaces/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Services Hub Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="next-steps"></a>次のステップ

- [リソース プロバイダーとサービスの対応](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Azure カスタム ロール](custom-roles.md)
- [Azure Security Center におけるアクセス許可](../security-center/security-center-permissions.md)
