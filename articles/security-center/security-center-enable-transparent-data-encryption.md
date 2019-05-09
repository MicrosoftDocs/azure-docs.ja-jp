---
title: Azure Security Center での Transparent Data Encryption の有効化 | Microsoft Docs
description: このドキュメントでは、"**Transparent Data Encryption を有効にする**" という Azure Security Center の推奨事項を実装する方法について説明します。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e4be8a0e-2118-4ee9-a266-69e52d9f7f8e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: d5ddec40a1b20e377ec18ce871018f674557e7b4
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56111766"
---
# <a name="enable-transparent-data-encryption-in-azure-security-center"></a>Azure Security Center での Transparent Data Encryption の有効化
Azure Security Center では、Transparent Data Encryption (TDE) がまだ有効になっていない場合に、SQL Database で TDE を有効にすることを勧めます。 TDE によってデータが保護されます。データベース、関連するバックアップ、保存されているトランザクション ログ ファイルを暗号化することで、コンプライアンス要件を満たすことができます。アプリケーションに変更を加える必要はありません。 詳細については、「[Azure SQL Database での Transparent Data Encryption](https://msdn.microsoft.com/library/dn948096)」をご覧ください。

この推奨事項は Azure SQL サービスにのみ適用されます。仮想マシンで実行されている SQL は含まれません。

> [!NOTE]
> このドキュメントでは、サンプルのデプロイを使用してサービスについて紹介します。  ステップ バイ ステップ ガイドではありません。
>
>

## <a name="implement-the-recommendation"></a>推奨事項の実装
1. **[推奨事項]** ブレードで、**[Transparent Data Encryption を有効にする]** を選択します。
   ![Transparent Data Encryption を有効にする][1]
2. **[SQL データベースで Transparent Data Encryption を有効にする]** ブレードが開きます。 TDE を有効にする SQL Database を選択します。
   ![TDE を有効にする SQL DB の選択][2]
3. **[Transparent Data Encryption]** ブレードで、[データ暗号化] の **[オン]** を選択し、ブレードの上部のリボンにある **[保存]** をクリックします。
   ![TDE の有効化][3]

   選択した SQL Database で TDE が有効になると、**[暗号化の状態]** が **[暗号化済み]** に変わります。    

   ![暗号化の状態][4]

## <a name="see-also"></a>関連項目
この記事では、"Transparent Data Encryption を有効にする" という Security Center の推奨事項を実装する方法について説明しました。 SQL TDE の詳細については、次の記事をご覧ください。

* [Azure SQL Database での Transparent Data Encryption](https://msdn.microsoft.com/library/dn948096)
* [Transparent Data Encryption (TDE) の概要](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティ ポリシーの設定](tutorial-security-policy.md) 」-- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* [Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md) 」-- 推奨事項に従って Azure リソースを保護する方法について説明しています。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md) 」-- Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」-- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md) 」-- パートナー ソリューションの正常性状態を監視する方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」-- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](https://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティに関する最新のニュースと情報を入手できます。

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]:./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png
