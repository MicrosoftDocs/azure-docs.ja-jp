---
title: Azure Security Center 上で SQL 情報保護ポリシーをカスタマイズする | Microsoft Docs
description: Azure Security Center 上で情報保護ポリシーをカスタマイズする方法について説明します。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: rkarlin
ms.openlocfilehash: 9b63fb963408b8f22453c7ea78e36a49402273a7
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56105724"
---
# <a name="customize-the-sql-information-protection-policy-in-azure-security-center-preview"></a>Azure Security Center 上で SQL 情報保護ポリシーをカスタマイズする (プレビュー)
 
Azure Security Center 上で、Azure テナント全体に対する SQL 情報保護ポリシーを定義およびカスタマイズできます。

Information Protection は、Azure データ リソース内の機密データを検出、分類、ラベル付け、保護するための高度なセキュリティ機能です。 最も機微なデータの検出と分類 (ビジネス/金融、医療、PII など) は、組織の情報保護水準において極めて重要な役割を果たします。 これは、以下のケースのインフラストラクチャとして機能します。
- データのプライバシー基準および規制のコンプライアンス要件を満たす支援
- さまざまなセキュリティ シナリオ (機密データに対する異常なアクセスの監視 (監査) とアラートなど)
- 非常に機密性の高いデータを含むデータ ストアへのアクセスの制御と、セキュリティの強化
 
[SQL Information Protection](../sql-database/sql-database-data-discovery-and-classification.md) により、Azure SQL Database に対して現在サポートされている SQL データ ストアに対して、このパラダイムが実装されます。 SQL Information Protection では、潜在的な機密データが自動的に検出されて分類され、分類属性を使用して機密データを永続的にタグ付けするためのラベル付けメカニズムが提供されます。データベースの分類状態を示す詳細ダッシュボードも提供されます。 さらに、SQL クエリの結果セットの機密性が計算されるので、機密データを抽出するクエリを明示的に監査し、データを保護できます。 SQL Information Protection について詳しくは、「[Azure SQL Database のデータの検出と分類](../sql-database/sql-database-data-discovery-and-classification.md)」をご覧ください。
 
分類メカニズムは、分類を構成する 2 つの主なコンストラクト (**ラベル**と**情報の種類**) に基づきます。
- **ラベル** – メインの分類属性であり、列に格納されているデータの機密レベルを定義するために使われます。 
- **情報の種類** – 列に格納されているデータの種類に対する追加の細分性を提供します。
 
Information Protection には、既定で使用されるラベルと情報の種類の組み込みセットが付属しています。 これらをカスタマイズするために、Azure Security Center 上で情報保護ポリシーをカスタマイズできます。
 
## <a name="customize-the-information-protection-policy"></a>情報の保護ポリシーをカスタマイズする
Azure テナントの情報保護ポリシーをカスタマイズするには、[テナントのルート管理グループに対する管理特権](security-center-management-groups.md)が必要です。 
 
1. Security Center のメイン メニューで、**[セキュリティ ポリシー]** を選択します。
2. **[階層ビュー (プレビュー)]** を選択し、**[Tenant Root Group]\(テナント ルート グループ\)** の下の **[設定の編集]** をクリックします。
 
   ![情報保護ポリシーを構成する](./media/security-center-info-protection-policy/security-policy.png) 
 
3. **[ポリシー コンポーネント]** の下の **[Information Protection]** をクリックします。 **[Information protection settings]\(Information Protection の設定\)** ページで、現在のラベル セットを表示できます。 これらは、データの機密レベルの分類に使用される主な分類属性です。 ここから、テナントの **[Information protection labels]\(Information Protection ラベル\)** と **[情報の種類]** を構成できます。 
 
### <a name="customizing-labels"></a>ラベルのカスタマイズ
 
1. 既存のラベルを編集または削除したり、新しいラベルを追加したりできます。 既存のラベルを編集するには、そのラベルを選択し、上部の、または右側のコンテキスト メニューから **[構成]** をクリックします。 新しいラベルを追加するには、上部のメニュー バー内またはラベル テーブルの下部にある **[ラベルの作成]** をクリックします。
2. **[機密ラベルを構成する]** 画面で、ラベルの名前と説明を作成または変更できます。 **[有効]** スイッチのオンとオフを切り替えることにより、ラベルがアクティブか無効かを設定することもできます。 最後に、ラベルに関連付けられている情報の種類を追加または削除することができます。 その情報の種類と一致する、検出されたデータにより、関連付けられている機密ラベルが分類の推奨事項に自動的に含められます。
3. Click **OK**.
 
   ![機密ラベルの構成](./media/security-center-info-protection-policy/config-sensitivity-label.png)
 
4. ラベルは、機密度の昇順で一覧表示されます。 ラベルの順位付けを変更するには、テーブル内でラベルをドラッグして並べ替えるか、**[上へ移動]** と **[下へ移動]** を使用して順序を変更します。 
 
    ![情報保護ポリシーを構成する](./media/security-center-info-protection-policy/move-up.png)
 
5. 完了したら、画面の上部にある **[保存]** を必ずクリックしてください。
 
 
## <a name="adding-and-customizing-information-types"></a>情報の種類の追加とカスタマイズ
 
1. **[情報の種類を管理する]** をクリックして、情報の種類を管理およびカスタマイズできます。
2. 新しい **[情報の種類]** を追加するには、トップ メニューの **[情報の種類を作成する]** を選択します。 **[情報の種類]** の名前、説明、検索パターン文字列を構成できます。 検索パターン文字列では、オプションで、ワイルドカード文字 ('%' 文字を使用) を含むキーワードを使用できます。自動検出エンジンでは、このキーワードを使用して、列のメタデータに基づいてデータベース内の機密データが識別されます。
 
    ![情報保護ポリシーを構成する](./media/security-center-info-protection-policy/info-types.png)
 
3. 追加の検索パターン文字列の追加、既存の文字列の一部の無効化、または説明の変更により、組み込みの **[情報の種類]** を構成することもできます。 組み込みの **[情報の種類]** を削除したり、その名前を編集したりすることはできません。 
4. **[情報の種類]** は、検出の順位付けの昇順で一覧表示されます。つまり、リスト内の上位の種類の照合が最初に試みられます。 情報の種類間で順位を変更するには、テーブル内で種類を適切な位置にドラッグするか、**[上へ移動]** と **[下へ移動]** を使用して順序を変更します。 
5. 完了したら、**[OK]** をクリックします。
6. 情報の種類の管理が完了したら、特定のラベルの **[構成]** をクリックし、情報の種類を適宜追加または削除することで、適切なラベルに適切な種類を関連付けてください。
7. メインの **[ラベル]** ブレードで **[保存]** をクリックして、すべての変更を適用します。
 
情報保護ポリシーは、完全に定義されて保存された後、テナント内のすべての Azure SQL データベース上のデータの分類に適用されます。
 
 
## <a name="next-steps"></a>次の手順
 
この記事では、Azure Security Center 上での SQL 情報保護ポリシーの定義について説明しました。 SQL Information Protection を使用して、SQL データベース内の機密データを分類および保護する方法について詳しくは、「[Azure SQL Database のデータの検出と分類](../sql-database/sql-database-data-discovery-and-classification.md)」をご覧ください。 

Azure Security Center 上のセキュリティ ポリシーとデータ セキュリティについて詳しくは、次の記事をご覧ください。
 
- [セキュリティ ポリシーの概要](security-center-policies-overview.md):Security Center 上のセキュリティ ポリシーの概要を示します
- [Azure Security Center でのセキュリティ ポリシーの設定](tutorial-security-policy.md):Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明します
- [Azure Security Center のデータ セキュリティ](security-center-data-security.md):Security Center でデータがどのように管理および保護されているかを説明しています


