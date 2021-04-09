---
title: カスタム ロール定義の概要
description: マネージド アプリケーション用にカスタム ロールの定義を作成するという概念について説明します。
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: 7b7fff913c177703f959bfa103c8e310d01059e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "81391821"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Azure Managed Applications のカスタム ロールの定義アーティファクト

カスタム ロールの定義はマネージド アプリケーションのオプションのアーティファクトです。 その機能を実行するためにマネージド アプリケーションで必要となるアクセス許可を決定するために使用されます。

この記事では、カスタム ロールの定義アーティファクトとその機能の概要について説明します。

## <a name="custom-role-definition-artifact"></a>カスタム ロールの定義アーティファクト

カスタム ロールの定義アーティファクトに customRoleDefinition.json という名前を付ける必要があります。 マネージド アプリケーション定義を作成する .zip パッケージ内で createUiDefinition.json および mainTemplate.json と同じレベルに配置する必要があります。 .zip パッケージを作成してマネージド アプリケーション定義を発行する方法については、「[Azure マネージド アプリケーションの定義を発行する](publish-service-catalog-app.md)」を参照してください。

## <a name="custom-role-definition-schema"></a>カスタム ロールの定義のスキーマ

customRoleDefinition.json ファイルには、ロールの配列であるトップレベルの `roles` プロパティがあります。 これらのロールは、マネージド アプリケーションが機能するために必要なアクセス許可です。 現在のところ、組み込みロールのみが許可されますが、複数のロールを指定できます。 ロールはロール定義の ID またはロール名で参照できます。

カスタム ロールの定義のサンプル JSON:

```json
{
    "contentVersion": "0.0.0.1",
    "roles": [
        {
            "properties": {
                "roleName": "Contributor"
            }
        },
        {
            "id": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
        },
        {
            "id": "/providers/Microsoft.Authorization/roledefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
        }
    ]
}
```

## <a name="roles"></a>ロール

ロールは `$.properties.roleName` または `id` から構成されます。

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!NOTE]
> `id` または `roleName` フィールドを使用できます。 1 つだけ必須です。 これらのフィールドは、適用するロール定義を検索するために使用されます。 両方が指定されている場合、`id` フィールドが使用されます。

|プロパティ|必須|説明|
|---------|---------|---------|
|id|はい|組み込みロールの ID。 完全な ID を使用するか、GUID のみを使用できます。|
|roleName|はい|組み込みロールの名前。|