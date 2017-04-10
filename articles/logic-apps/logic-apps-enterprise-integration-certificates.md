
---
title: "Enterprise Integration Pack での証明書の使用 |Microsoft Docs"
description: "Enterprise Integration Pack で証明書を使用する方法 | Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: cgronlun
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: c8cfe5fb933cf9056b971d3e524f3c9ebc910215
ms.openlocfilehash: b494d5abfdd4045776b408cbb8ec99ee3905cf72


---
# <a name="learn-about-certificates-and-enterprise-integration-pack"></a>証明書と Enterprise Integration Pack についての詳細情報
## <a name="overview"></a>概要
エンタープライズ統合では、証明書を使用して B2B 通信を保護します。 エンタープライズ統合アプリでは、次の2 種類の証明書を使用できます。

* パブリック証明書。証明機関 (CA) から購入する必要があります。
* プライベート証明書。自身で発行できます。 これらは自己署名証明書とも呼ばれます。

## <a name="what-are-certificates"></a>証明書の概要
証明書はデジタル ドキュメントであり、電子通信の参加者の ID の確認と、電子通信の保護を行うためのものです。

## <a name="why-use-certificates"></a>証明書を使用する理由
場合によっては、B2B 通信で機密性を保持する必要があります。 エンタープライズ統合では、次の&2; つの方法で証明書を使用してこれらの通信を保護します。

* メッセージの内容を暗号化することによって
* メッセージをデジタル署名することによって  

## <a name="upload-a-public-certificate"></a>パブリック証明書のアップロード

B2B 機能を備えたロジック アプリで *パブリック証明書* を使用するには、まず統合アカウントにこの証明書をアップロードする必要があります。  

証明書をアップロードすると、作成する [契約](logic-apps-enterprise-integration-agreements.md) で B2B メッセージのプロパティを定義して、B2B メッセージを保護できるようになります。  

次に、Azure Portal へのサインイン後に統合アカウントにパブリック証明書をアップロードする詳細な手順を示します。

1. **[その他のサービス]** を選択し、フィルター検索ボックスに「**統合**」と入力します。 結果の一覧から **[統合アカウント]** を選択します。     
![[参照]](media/logic-apps-enterprise-integration-certificates/overview-1.png)を選択します。  
2. 証明書の追加先となる統合アカウントを選択します。  
![証明書の追加先となる統合アカウントの選択](media/logic-apps-enterprise-integration-certificates/overview-3.png)  
3. **[証明書]** タイルを選択します。  
![[証明書] タイルの選択](media/logic-apps-enterprise-integration-certificates/certificate-1.png)
4. 表示された **[証明書]** ブレードで **[追加]** をクリックします。   
![[追加] ボタンのクリック](media/logic-apps-enterprise-integration-certificates/certificate-2.png)
5. 証明書の**名前**を入力して、ドロップダウンから証明書の種類として **[パブリック]** を選択します。  
6. **[証明書]** ボックスの右側にあるフォルダー アイコンをクリックします。 ファイル ピッカーが表示されたら、統合アカウントにアップロードする証明書を見つけて選択します。
7. 証明書を選択して、ファイル ピッカーで **[OK]** をクリックします。 これにより、証明書を検証し、統合アカウントにアップロードできます。
8. 最後に、**[証明書の追加]** ブレードに戻って **[OK]** をクリックします。  
![[OK] ボタンのクリック](media/logic-apps-enterprise-integration-certificates/certificate-3.png)  
9. **[証明書]** タイルを選択します。 新しく追加された証明書が表示されます。  
![新しい証明書の確認](media/logic-apps-enterprise-integration-certificates/certificate-4.png)  

## <a name="upload-a-private-certificate"></a>プライベート証明書のアップロード

B2B 機能を備えたロジック アプリで*プライベート証明書*を使用するには、次の手順に従ってプライベート証明書を統合アカウントにアップロードします。

1. [Key Vault に秘密キーをアップロード](../key-vault/key-vault-get-started.md "Key Vault についての詳細情報")して、**キー名**を指定します。 
   
   > [!TIP]
   > Key Vault に対して操作を実行するには、Logic Apps を承認する必要があります。 Logic Apps サービス プリンシパルにアクセスを付与するには、次の PowerShell コマンドを使用します: `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`  
   > 
   > 

前の手順を実行したら、統合アカウントにプライベート証明書を追加します。

次に、Azure Portal へのサインイン後に統合アカウントにプライベート証明書をアップロードする詳細な手順を示します。  
 
1. 証明書の追加先となる統合アカウントを選択し、**[証明書]** タイルを選択します。  
![[証明書] タイルの選択](media/logic-apps-enterprise-integration-certificates/certificate-1.png)  
2. 表示された **[証明書]** ブレードで **[追加]** をクリックします。   
![[追加] ボタンのクリック](media/logic-apps-enterprise-integration-certificates/certificate-2.png)
3. 証明書の**名前**を入力して、ドロップダウンから証明書の種類として **[プライベート]** を選択します。   
4. **[証明書]** ボックスの右側にあるフォルダー アイコンをクリックします。 ファイル ピッカーが表示されたら、統合アカウントにアップロードする、対応するパブリック証明書を見つけて選択します。   
   
   > [!Note]
   > プライベート証明書を追加するときに、対応するパブリック証明書を追加することが重要です。これは、[AS2 契約](logic-apps-enterprise-integration-as2.md)で送受信するメッセージの署名と暗号化の設定に使用されます。
   > 
   >   

5. **[リソース グループ]**、**[Key Vault]**、**[キー名]** の順に選択し、**[OK]** ボタンをクリックします。  
![証明書の追加](media/logic-apps-enterprise-integration-certificates/privatecertificate-1.png)  
6. **[証明書]** タイルを選択します。 新しく追加された証明書が表示されます。
![新しい証明書の確認](media/logic-apps-enterprise-integration-certificates/privatecertificate-2.png)  



* [B2B 契約の作成](logic-apps-enterprise-integration-agreements.md)  
* [Key Vault についての詳細情報](../key-vault/key-vault-get-started.md "Key Vault についての詳細情報")  




<!--HONumber=Feb17_HO1-->


