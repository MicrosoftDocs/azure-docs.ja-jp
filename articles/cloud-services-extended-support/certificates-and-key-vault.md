---
title: Azure Cloud Services (延長サポート) での証明書の格納と使用
description: Azure Cloud Services (延長サポート) での証明書の格納と使用のプロセス
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 9e69b4e9279f9147c2ee13d42a42aec0c5a15d96
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744061"
---
# <a name="use-certificates-with-azure-cloud-services-extended-support"></a>Azure Cloud Services (延長サポート) で証明書を使用する

Key Vault は Cloud Services (延長サポート) に関連付けられている証明書を格納するために使用されます。 Key Vault は、[Azure portal](https://docs.microsoft.com/azure/key-vault/general/quick-create-portal) および [PowerShell](https://docs.microsoft.com/azure/key-vault/general/quick-create-powershell) を使用して作成できます。 証明書を Key Vault に追加してから、サービス構成ファイルでその証明書の拇印を参照します。 また、Cloud Services (延長サポート) リソースで Key Vault からシークレットとして格納されている証明書を取得できるように、適切なアクセス許可に対して Key Vault を有効にする必要があります。  

## <a name="upload-a-certificate-to-key-vault"></a>Key Vault に証明書をアップロードする 

1.  Azure portal にサインインし、Key Vault に移動します。 Key Vault を設定していない場合は、この同じウィンドウで作成することを選択できます。

2. **[アクセス ポリシー]** を選択します

    :::image type="content" source="media/certs-and-key-vault-1.png" alt-text="Key Vault ブレードからのアクセス ポリシーの選択を示す画像。":::

3. アクセス ポリシーに次のプロパティが含まれるようにします。
    - **アクセスの有効化: Azure Virtual Machines (展開用)**
    - **アクセスの有効化: Azure Resource Manager (テンプレートの展開用)** 

    :::image type="content" source="media/certs-and-key-vault-2.png" alt-text="Azure portal のアクセス ポリシー ウィンドウを示す画像。":::
 
4.  **[証明書]** を選択します 

    :::image type="content" source="media/certs-and-key-vault-3.png" alt-text="Azure portal 内の Key Vault ブレード ポリシー ウィンドウからの証明書オプションの選択を示す画像。":::

5. **[生成/インポート]** を選択します

    :::image type="content" source="media/certs-and-key-vault-4.png" alt-text="生成/インポート オプションの選択を示す画像":::

4.  証明書のアップロードを完了するために必要な情報を入力します。 

    :::image type="content" source="media/certs-and-key-vault-5.png" alt-text="Azure portal 内のインポート ウィンドウを示す画像。":::

5.  証明書の詳細をサービス構成 (.cscfg) ファイルのロールに追加します。 Azure portal 内の証明書の拇印が、サービス構成 (.cscfg) ファイルの拇印と一致するようにします。 
    
    ```json
    <Certificate name="<your cert name>" thumbprint="<thumbprint in key vault" thumbprintAlgorithm="sha1" /> 
    ```

## <a name="next-steps"></a>次のステップ 
- Cloud Services (延長サポート) の[デプロイの前提条件](deploy-prerequisite.md)を確認します。
- Cloud Services (延長サポート) に関して[よく寄せられる質問](faq.md)を確認します。
- [Azure portal](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[テンプレート](deploy-template.md) または [Visual Studio](deploy-visual-studio.md) を使用して、Cloud Services (延長サポート) をデプロイします。