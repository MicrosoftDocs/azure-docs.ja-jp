---
title: "Azure Government パートナー向けの Azure Marketplace | Microsoft Docs"
description: "この記事では、Azure Government アプリケーションの機能の比較と開発におけるガイダンスを示します。"
services: azure-government
cloud: gov
documentationcenter: 
author: tsingh
manager: asimm
ms.assetid: 7790d3c5-d0fa-4662-b4f0-a174cb7d6c9f
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/14/2016
ms.author: zakramer;tsingh;divacc
translationtype: Human Translation
ms.sourcegitcommit: 0839e8e57b2149e50d4512d28b1e57e6592be458
ms.openlocfilehash: 70821864520ff18ba8c64be0ea66376bccee7148


---
# <a name="azure-marketplace-for-azure-government"></a>Azure Government 向けの Azure Marketplace
Azure Marketplace へのサービスの発行に興味をお持ちの Azure Government パートナー様は、この記事で詳細をご覧ください。

Azure Government Marketplace では、現在、BYOL VM イメージとソリューション テンプレートがサポートされています。 ベスト プラクティスとして、Azure Government に発行する前に、ソリューション テンプレートが Azure パブリック クラウドと Azure Government クラウドの両方で機能することを確認してください。 VM イメージのみを発行する場合は、下の発行手順に進んでください。

## <a name="pre-publishing-validation-for-solution-templates"></a>ソリューション テンプレートの発行前の検証

Azure Government にソリューション テンプレートを発行する前に、いくつかの一般的なベスト プラクティスの領域をチェックして、テンプレートが Azure パブリック クラウドと Azure Government の両方で機能することを確認することをお勧めします。

1.  Azure パブリック クラウドについては、エンドポイントがソリューション テンプレートにハード コーディングされていないことを確認します。 そうしたエンドポイントは他のどの Azure 環境でも無効です。 代わりに、有効なエンドポイントを API 呼び出しで取得するようにソリューション テンプレートを変更します。  

  例:

  不適切な VHD URI (ハードコード) "uri": "[concat('https://', variables('storageAccountName'), '.blob.core.windows.net/',  '/osdisk.vhd')]",

  適切な VHD URI (参照)

  "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'osdisk.vhd')]",

  構文を修正することで、テンプレートはどのクラウド (Gov、パブリック Azure、AzureStack、China など) でも確実に機能するようになります。

2.  ソリューション テンプレートで使用するリソースが発行先の主権のあるクラウドで使用できることを確認します。
Azure の RP と API バージョン

    ポータルでリソース エクスプローラーを使用して Azure Government での利用可能性を確認します。

  1.    Azure Government ポータルにログインします。
  2.    リソース エクスプローラーを起動し、https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-supported-services#supported-regions に記載されている手順に従います。

  最もよく使用される拡張機能 (Fairfax での利用可能性)  

  | 発行元/種類 | Fairfax で利用可能なバージョン |
  | --- | --- |
  | Microsoft.OSTCExtensions/CustomScriptForLinux | 1、1.1、1.2.2.0、1.3.0.2、1.4.1.0、1.5.2.0 |
  | Microsoft.Compute/CustomScriptExtension | 1.2、1.3、1.4、1.7、1.8 |
  | Microsoft.Azure.Extensions/DockerExtension | 利用不可 |
  | Microsoft.Azure.Extensions/CustomScript | 2.0.2 |
  | Microsoft.OSTCExtensions/LinuxDiagnostic | 2.0.9005、2.1.9005、2.2.9005、2.3.9011 |
  | Microsoft.Powershell/DSC | 2.19.0.0 |

> [!NOTE]
> 新しいリージョンが追加されるため、使用できる値の一覧に場所を追加する場合は、定期的に更新する必要があります。  


## <a name="publishing"></a>発行
> [!NOTE]
> Azure 認定 Marketplace パートナー以外のお客様は、先に進む前に[プランを発行して管理する](../marketplace-publishing/marketplace-publishing-getting-started.md)ための手順を完了してください。
>
>

### <a name="step-1"></a>手順 1
[Azure 発行](https://publish.windowsazure.com)にサインインします。

### <a name="step-2"></a>手順 2.
発行するサービスをクリックします。

### <a name="step-3"></a>手順 3.
**[SKUS (SKU)]** をクリックし、**[Azure Government Cloud (Azure Government クラウド)]** チェック ボックスをオンにします。

> [!NOTE]
> サポートされているのはライセンス持ち込み (BYOL) SKU のみです。  このオプションは、従量課金の (PayG) SKU では利用できません。
>
>

![SKU と Azure Government クラウドのオプションが表示されているサービス ページ](./media/government-manage-marketplace-partner-1.png)

### <a name="step-4"></a>手順 4.
**[+ Add Certification (証明書の追加)]** リンクをクリックし、発行するサービスの証明書へのリンクを追加します。

![[Add Certification (証明書の追加)] リンクのあるサービス ページ](./media/government-manage-marketplace-partner-2.png)

### <a name="step-5"></a>手順 5.
発行ポータルでイメージをテストするには、Microsoft Azure Government の[試用版アカウント](https://azuregov.microsoft.com/trial/azuregovtrial)を依頼します。

米国連邦政府、州政府、地方自治体、または部族の各組織にサービスを提供するパートナーとしてのお客様の資格は、検証が行われたうえで、電子メール経由で承認されます。  試用版アカウントは&3; ～&5; 営業日以内に利用可能になります。

### <a name="step-6"></a>手順 6.
**[発行]**、**[Push to Staging (ステージングにプッシュ)]** の順にクリックします。

![[発行] および [Push to Staging (ステージングにプッシュ)] オプション](./media/government-manage-marketplace-partner-3.png)

ステージングされたサービスにアクセス可能な、ホワイトリストに登録済みのサブスクリプションを入力するよう求めるメッセージが表示されます。 新たに取得した Azure Government 試用版アカウントのサブスクリプション ID を入力します。

![サービスにアクセス可能なサブスクリプション ID を指定するプロンプト](./media/government-manage-marketplace-partner-4.png)

### <a name="step-7"></a>手順 7.
サービスのステージングが成功したら、Azure Government 試用版アカウントを使用して [Azure Portal](https://portal.azure.us) にサインインすることで、イメージをテストできます。

### <a name="step-8"></a>手順 8.
試用版サブスクリプションを使用してイメージを検証したら、**[発行]** をクリックして運用環境への移行の承認依頼を行うことで、サービスの提供を開始できます。

![運用環境への移行の承認依頼コマンド](./media/government-manage-marketplace-partner-5.png)

## <a name="next-steps"></a>次のステップ
補足情報と最新情報については、[Microsoft Azure Government のブログ](https://blogs.msdn.microsoft.com/azuregov/)をご覧ください。



<!--HONumber=Dec16_HO3-->


