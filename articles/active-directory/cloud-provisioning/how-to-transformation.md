---
title: Azure AD Connect クラウド プロビジョニングの変換
description: この記事では、変換により既定の属性マッピングを変更する方法について説明します。
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: a15a25ce4fbe1a43c2a5065bf88f5c40c5865721
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85360692"
---
# <a name="transformations"></a>変換

変換を使用すると、クラウド プロビジョニングを使用した Azure Active Directory (Azure AD) との属性の同期方法の既定の動作を変更できます。

このタスクを実行するには、スキーマを編集してから、Web 要求を使用して再送信する必要があります。

クラウド プロビジョニング属性の詳細については、「[Azure AD スキーマについて](concept-attributes.md)」を参照してください。


## <a name="retrieve-the-schema"></a>スキーマを取得する
スキーマを取得するには、「[スキーマの表示](concept-attributes.md#view-the-schema)」に説明されている手順に従います。 

## <a name="custom-attribute-mapping"></a>カスタム属性のマッピング
カスタム属性のマッピングを追加するには、次の手順に従います。

1. スキーマを [Visual Studio Code](https://code.visualstudio.com/) などのテキスト エディターまたはコード エディターにコピーします。
1. スキーマで更新するオブジェクトを見つけます。

   ![スキーマ内のオブジェクト](media/how-to-transformation/transform1.png)</br>
1. ユーザー オブジェクトで `ExtensionAttribute3` のコードを見つけます。

    ```
                            {
                                "defaultValue": null,
                                "exportMissingReferences": false,
                                "flowBehavior": "FlowWhenChanged",
                                "flowType": "Always",
                                "matchingPriority": 0,
                                "targetAttributeName": "ExtensionAttribute3",
                                "source": {
                                    "expression": "Trim([extensionAttribute3])",
                                    "name": "Trim",
                                    "type": "Function",
                                    "parameters": [
                                        {
                                            "key": "source",
                                            "value": {
                                                "expression": "[extensionAttribute3]",
                                                "name": "extensionAttribute3",
                                                "type": "Attribute",
                                                "parameters": []
                                            }
                                        }
                                    ]
                                }
                            },
    ```
1. company 属性が `ExtensionAttribute3` にマップされるようにコードを編集します。

   ```
                                    {
                                        "defaultValue": null,
                                        "exportMissingReferences": false,
                                        "flowBehavior": "FlowWhenChanged",
                                        "flowType": "Always",
                                        "matchingPriority": 0,
                                        "targetAttributeName": "ExtensionAttribute3",
                                        "source": {
                                            "expression": "Trim([company])",
                                            "name": "Trim",
                                            "type": "Function",
                                            "parameters": [
                                                {
                                                    "key": "source",
                                                    "value": {
                                                        "expression": "[company]",
                                                        "name": "company",
                                                        "type": "Attribute",
                                                        "parameters": []
                                                    }
                                                }
                                            ]
                                        }
                                    },
   ```
 1. スキーマを Graph Explorer にコピーして、 **[要求の種類]** を **[PUT]** に変更し、 **[クエリの実行]** を選択します。

    ![クエリを実行する](media/how-to-transformation/transform2.png)

 1. 次に Azure portal で、クラウド プロビジョニング構成に移動し、 **[プロビジョニングを再開する]** を選択します。

    ![プロビジョニングを再開する](media/how-to-transformation/transform3.png)

 1. しばらくしてから、Graph Explorer で次のクエリを実行して、属性が設定されていることを確認します: `https://graph.microsoft.com/beta/users/{Azure AD user UPN}`。
 1. これで、値が表示されます。

    ![値が表示されます](media/how-to-transformation/transform4.png)

## <a name="custom-attribute-mapping-with-function"></a>関数を使用したカスタム属性マッピング
より高度なマッピングでは、データを操作し、組織のニーズに合わせて属性の値を作成できる関数を使用できます。

このタスクを実行するには、上記の手順を実行してから、最終的な値の作成に使用する関数を編集します。

式の構文と例の詳細については、「[Azure Active Directory における属性マッピングの式の書き方](reference-expressions.md)」を参照してください。


## <a name="next-steps"></a>次のステップ 

- [プロビジョニングとは](what-is-provisioning.md)
- [Azure AD Connect クラウド プロビジョニングとは](what-is-cloud-provisioning.md)
