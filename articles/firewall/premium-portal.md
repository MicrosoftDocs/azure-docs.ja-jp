---
title: Azure portal での Azure Firewall Premium プレビュー
description: Azure portal での Azure Firewall Premium プレビューについて説明します。
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 3d56fc73faeb0d48ba7e5b21449c61d6213afa40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100549484"
---
# <a name="azure-firewall-premium-preview-in-the-azure-portal"></a>Azure portal での Azure Firewall Premium プレビュー

> [!IMPORTANT]
> 現在、Azure Firewall Premium はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

 Azure Firewall Premium プレビューは、機密度が高く、規制された環境に必要な機能を備えた次世代のファイアウォールです。 このパックには、次の機能があります。

- **TLS 検査** - アウトバウンド トラフィックを復号化し、データを処理し、その後にデータを暗号化して宛先に送信します。
- **IDPS** - ネットワーク侵入検出と防止システム (IDPS) を使用すると、ネットワーク アクティビティを監視して悪意のあるアクティビティがないか確認し、このアクティビティに関する情報をログに記録し、それを報告して、任意でそのブロックを試みることができます。
- **URL フィルタリング** - Azure Firewall の FQDN フィルタリング機能を拡張して URL 全体が考慮対象になるようにします。 たとえば、`www.contoso.com` の代わりに `www.contoso.com/a/c` となります。
- **Web カテゴリ** - 管理者は、ギャンブルの Web サイトやソーシャル メディアの Web サイトといった Web サイト カテゴリへのユーザーのアクセスを許可または禁止できます。

詳細については、「[Azure Firewall Premium の機能](premium-features.md)」を参照してください。

## <a name="deploy-the-firewall"></a>ファイアウォールをデプロイする

Azure Firewall Premium プレビューのデプロイは、標準の Azure Firewall のデプロイに似ています。

:::image type="content" source="media/premium-portal/premium-portal.png" alt-text="ポータルのデプロイ":::

**[ファイアウォール層]** には **[Premium (プレビュー)]** を選択し、 **[ファイアウォール ポリシー]** には既存の Premium ポリシーを選択するか、新しいポリシーを作成します。

## <a name="configure-the-premium-policy"></a>Premium ポリシーを構成する

Premium ファイアウォール ポリシーの構成は、標準のファイアウォール ポリシーの構成に似ています。 Premium ポリシーを使用すると、Premium 機能を構成できます。

:::image type="content" source="media/premium-portal/premium-policy.png" alt-text="Premium ポリシーのデプロイ":::

### <a name="rule-configuration"></a>ルールの構成

Premium ポリシーでアプリケーション ルールを構成するときに、追加の Premium 機能を構成することができます。

:::image type="content" source="media/premium-portal/premium-application-rule.png" alt-text="Premium ルール":::

## <a name="next-steps"></a>次のステップ

実行中の Azure Firewall Premium プレビュー機能を確認するには、「[Azure Firewall Premium プレビューをデプロイして構成する](premium-deploy.md)」を参照してください。