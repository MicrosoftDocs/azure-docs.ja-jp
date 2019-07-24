---
title: インクルード ファイル
description: インクルード ファイル
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 12/14/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 69951693f9d3bacb556453aba954620815884d43
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2019
ms.locfileid: "56333919"
---
## <a name="create-a-service-principal"></a>サービス プリンシパルの作成

コンテナー レジストリへのアクセス権を持つサービス プリンシパルを作成するには、[Azure Cloud Shell](../articles/cloud-shell/overview.md) または [Azure CLI](/cli/azure/install-azure-cli) のローカル インストールで、次のスクリプトを実行します。 スクリプトは Bash シェル用に書式設定されています。

スクリプトを実行する前に、`ACR_NAME` 変数をコンテナー レジストリの名前で更新します。 `SERVICE_PRINCIPAL_NAME` 値は、Azure Active Directory テナント内で一意である必要があります。 "`'http://acr-service-principal' already exists.`" エラーが発生した場合は、別のサービス プリンシパルの名前を指定します。

別のアクセス権を付与する場合は、必要に応じて [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] コマンドの `--role` の値を変更することができます。 ロールの一覧については、[ACR のロールとアクセス許可](https://github.com/Azure/acr/blob/master/docs/roles-and-permissions.md)に関するページを参照してください。

スクリプトを実行した後、サービス プリンシパルの **ID** と**パスワード**を書き留めます。 その資格情報を作成したら、サービス プリンシパルとして、コンテナー レジストリに対する認証を受けるアプリケーションやサービスを構成できます。

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh --> [!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

## <a name="use-an-existing-service-principal"></a>既存のサービス プリンシパルの使用

既存のサービス プリンシパルにレジストリへのアクセスを与えるには、サービス プリンシパルに新しいロールを割り当てる必要があります。 新しいサービス プリンシパルの作成と同様に、特に、プル、プッシュとプル、所有者のアクセスを付与できます。

次のスクリプトでは、[az role assignment create][az-role-assignment-create] コマンドを使って、`SERVICE_PRINCIPAL_ID` 変数で指定したサービス プリンシパルに*プル* アクセス許可を付与します。 異なるレベルのアクセスを付与する場合は、`--role` の値を調整します。


<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh --> [!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
