---
title: Azure Defender for Key Vault - 利点と機能
description: Azure Defender for Key Vault の利点と機能について学習します。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: f127a24bec5567a3868ae77cb8a52f0af2a19603
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100680"
---
# <a name="introduction-to-azure-defender-for-key-vault"></a>Azure Defender for Key Vault の概要

Azure Key Vault は、暗号化キーとシークレット (証明書、接続文字列、パスワードなど) を保護するクラウド サービスです。 

Azure ネイティブの場合は、**Azure Defender for Key Vault** を有効にします。Azure Key Vault の場合は、セキュリティ インテリジェンスのレイヤーを追加して、Advanced Threat Protection を有効にします。 

## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般提供 (GA)|
|価格:|**Azure Defender for Key Vault** の課金については、「[Security Center の価格](https://azure.microsoft.com/pricing/details/security-center/)」をご覧ください|
|クラウド:|![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![No](./media/icons/no-icon.png) ナショナル/ソブリン (US Gov、China Gov、その他の Gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-key-vault"></a>Azure Defender for Key Vault の利点は何ですか?

Azure Defender によって、異常であり、害を及ぼす可能性のある、Key Vault アカウントに対するアクセスまたは悪用の試みが検出されます。 この保護層により、セキュリティの専門家でなくても、サードパーティ製のセキュリティ監視システムを管理する必要なしに、脅威に対処することができます。  

異常なアクティビティが発生した場合、Azure Defender によって、アラートが表示され、必要に応じて組織の関連するメンバーにメールが送信されます。 これらのアラートには、不審なアクティビティの詳細と、脅威の調査や修復方法に関する推奨事項が含まれます。 

## <a name="azure-defender-for-key-vault-alerts"></a>Azure Defender for Key Vault のアラート
Azure Defender for Key Vault からアラートを受け取った場合は、[Azure Defender for Key Vault への応答](defender-for-key-vault-usage.md)に関するページの説明に従って調査し、アラートに対応することをお勧めします。 Azure Defender for Key Vault によって、アプリケーションと資格情報が保護されているため、アラートをトリガーしたアプリケーションまたはユーザーを熟知している場合でも、あらゆるアラートを取り巻く状況を確認することが重要です。

アラートは Key Vault の **[セキュリティ]** ページ、Azure Defender ダッシュボード、Security Center のアラート ページに表示されます。

:::image type="content" source="./media/defender-for-key-vault-intro/key-vault-security-page.png" alt-text="Azure Key Vault のセキュリティ ページ":::


> [!TIP]
> 「[Azure Security Center での Azure Key Vault 脅威検出の検証](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)」の手順に従って、Azure Defender for Key Vault のアラートをシミュレートできます。


## <a name="next-steps"></a>次の手順

この記事では、Azure Defender for Key Vault について学習しました。

関連資料については、次の記事をご覧ください。 

- [Key Vault セキュリティ アラート](alerts-reference.md#alerts-azurekv)--すべての Azure Security Center アラートの参照テーブルの Key Vault セクション
- [Security Center のデータを連続的にエクスポートする](continuous-export.md)
- [Azure Defender のアラートの抑制](alerts-suppression-rules.md)