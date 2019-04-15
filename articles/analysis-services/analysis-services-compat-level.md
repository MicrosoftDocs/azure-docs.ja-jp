---
title: Azure Analysis Services でのデータ モデルの互換性レベル| Microsoft Docs
description: 表形式のデータ モデルの互換性レベルを理解します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 67a6c99253c549f0b8d3b55809b35b81756843eb
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58803495"
---
# <a name="compatibility-level-for-analysis-services-tabular-models"></a>Analysis Services 表形式モデルの互換性レベル

*互換性レベル*とは、Analysis Services エンジンにおけるリリースに固有の動作を指します。 通常、互換性レベルの変更は、SQL Server のメジャー リリースと一致します。 これらの変更は Azure Analysis Services にも実装され、両方のプラットフォーム間のパリティを維持します。 互換性レベルの変更は、テーブルモデルに利用可能な機能にも影響を与えます。 たとえば、DirectQuery とテーブル オブジェクトのメタデータは、互換性レベルによって実装が異なります。 互換性レベルは、Visual Studio (SSDT) のテーブル モデル プロジェクトので指定されます。

Azure Analysis Services は、互換性レベル 1200 と 1400 の表形式モデルをサポートしています。 最新の互換性レベルは、1400 です。 このレベルは、SQL Server 2017 Analysis Services と一致します。 互換性レベル 1400 の主な機能は次のとおりです。

*  データ接続および、 TOM API と TMSL スクリプトのためのサポートをインポートする新しい機能。 
*  Get Data 式と M 式の使用による、データ変換およびデータ マッシュ アップ機能。
*  メジャーは、DAX 式で Detail Rows プロパティをサポートします。 このプロパティは、Microsoft Excel などのクライアント ツールで、集約されたレポートから詳細なデータをドリルダウンできるようにします。 たとえば、ユーザーがある地域と月の売上合計を表示するときに、関連した注文の詳細を表示できます。 
*  表と列に含まれるデータに加え、その表と列の名前のオブジェクト レベルのセキュリティ。
*  不規則な階層のサポートの強化。
*  パフォーマンスと監視の機能強化。

> [!NOTE]
> Azure Analysis Services は、インポートされた Power BI Desktop ファイルを 1465 互換性レベルでサポートします。 ただし Power BI Desktop からのインポート機能は、プレビュー機能のまま 2019 年 3 月に廃止され、サービスから削除されました。 1465 互換性レベルの既存のモデルは引き続きサポートされます。  


## <a name="set-compatibility-level"></a>互換性レベルの設定

 SSDT で新しい表形式モデル プロジェクトを作成する場合、**[テーブル モデル デザイナー]** ダイアログボックスで互換性レベルを指定します。 
  
 ![ssas_tabularproject_compat1200](./media/analysis-services-compat-level/aas-tabularproject-compat.png)  
  
 **[今後このメッセージを表示しない]** オプションを選択すると、後続のすべてのプロジェクトで、ここで指定した互換性レベルが既定値として使用されます。 SSDT の既定の互換性レベルは、**[ツール]** > **[オプション]** で変更することができます。  
  
 SSDT で既存のテーブル モデル プロジェクトをアップグレードするには、モデルの **[プロパティ]** ウィンドウで **[互換性レベル]** プロパティを設定します。 互換性レベルのアップグレードは元に戻せないので、注意してください。
  
## <a name="check-compatibility-level-for-a-tabular-model-database-in-sql-server-management-studio"></a>SQL Server Management Studio での表形式モデルのデータベースの互換性レベルの確認 

 SSMS で、データベース名を右クリックして > **[プロパティ]** > **[互換性レベル]** と移動します。  
  
## <a name="check-supported-compatibility-level-for-a-server-in-ssms"></a>SSMS のサーバーでサポートされる互換性レベルの確認  

 SSMS で、サーバー名を右クリックして >  **[プロパティ]** > **[サポートされている互換性レベル]** と移動します。  
  
 このプロパティは、サーバーで実行されるデータベースの最上位の互換性レベルを指定します (プレビューを除く)。 サポートされる互換性レベルは変更できません。  

> [!NOTE]
> SSMS で Azure Analysis Services サーバーに接続したとき、**[サポートされている互換性レベル]** プロパティには **1200** と表示されます。 これは既知の問題であり、今後の SSMS の更新で解決されます。 解決されれば、サポートされている最も高い互換性レベルがこのプロパティに表示されます。

## <a name="next-steps"></a>次の手順

  [Analysis Services を管理する](analysis-services-manage.md)  
