---
title: "Azure Security Center でストレージ アカウントの暗号化を有効にする | Microsoft Docs"
description: "このドキュメントでは、「**Enable encryption for Azure Storage Account**」 (Azure Storage アカウント暗号化の有効化) という Azure Security Center の推奨事項を実装する方法について説明します。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 7b5df25f46a2824acb361401ab90c960b8e5978f
ms.openlocfilehash: 5b580183002ae5c42cc08343cea7f659c635d590


---
# <a name="enable-encryption-for-azure-storage-account-in-azure-security-center"></a>Azure Security Center での Azure Storage アカウント暗号化の有効化
Azure Security Center は、Azure Storage Service Encryption for Data at Rest の有効化を勧める場合があります。

Storage Service Encryption (SSE) は、データが Azure ストレージに書き込まれたときに暗号化し、データが取得される前に復号化します。  現在、SSE は Azure Blob service のみに対応し、ブロック BLOB、ページ BLOB、追加 BLOB で使用できます。  詳細については、「[Storage Service Encryption for Data at Rest](../storage/storage-service-encryption.md)」を参照してください。


> [!Note]
> 暗号化を有効にしたとき、暗号化されるのは新しいデータのみです。 ストレージ アカウント内の既存の BLOB は暗号化されません。 既存の BLOB を暗号化するには、[Storage Service Encryption の FAQ](../storage/storage-service-encryption.md#frequently-asked-questions-about-storage-service-encryption-for-data-at-rest) に関する記事を参照してください。
>
>

Storage Service Encryption は Resource Manager ストレージ アカウントでのみサポートされます。 現在、クラシック ストレージ アカウントはサポートされていません。 クラシック デプロイ モデルと Resource Manager デプロイ モデルについて理解するには、[Azure デプロイ モデル](../azure-classic-rm.md)に関する記事を参照してください。

> [!NOTE]
> このドキュメントでは、サンプルのデプロイを使用してサービスについて紹介します。  このドキュメントはステップ バイ ステップ ガイドではありません。
>
>

## <a name="implement-the-recommendation"></a>推奨事項の実装
1. **[推奨事項]** ブレードで、**[Enable encryption for Azure Storage Account (Azure Storage アカウント暗号化の有効化)]** を選択します。
   ![Enable encryption for storage account][1]
2. **[Enable storage encryption (ストレージ暗号化の有効化)]** ブレードが開きます。 このブレードには、ストレージの暗号化が無効になっている Azure Storage アカウントが一覧表示されます。 この例では、**storageacct1** を選択します。
   ![Enable storage encryption][2]
3. **storageacct1**の**[暗号化]** ブレードが開きます。 **[Enabled]**を選択します。
   ![Encryption blade][3]
4. [ **保存**] を選択します。

これで、**storageacct1** のストレージ暗号化が有効になりました。


## <a name="see-also"></a>関連項目
このドキュメントでは、「Enable encryption for Azure Storage Account」 (Azure Storage アカウント暗号化の有効化) という Security Center の推奨事項を実装する方法について説明しました。 Azure Storage Service Encryption の詳細については、次のリソースを参照してください。

* [Azure Storage Service Encryption for Data at Rest](../storage/storage-service-encryption.md)

セキュリティ センターの詳細については、次を参照してください。

* 「[Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md)」 - Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* 「[Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)」 - Azure リソースの正常性を監視する方法について説明しています。
* 「[Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」 - セキュリティの警告の管理と対応の方法について説明しています。
* 「[Azure セキュリティ センターでのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」 - 推奨事項に従って Azure リソースを保護する方法について説明しています。
* 「[Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)」 - このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) - Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

<!--Image references-->
[1]: ./media/security-center-enable-encryption-for-storage-account/enable-encryption-for-storage-account.png
[2]: ./media/security-center-enable-encryption-for-storage-account/enable-storage-encryption.png
[3]: ./media/security-center-enable-encryption-for-storage-account/encryption-blade.png



<!--HONumber=Jan17_HO1-->


