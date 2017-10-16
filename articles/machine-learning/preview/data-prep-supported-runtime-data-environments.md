---
title: "Azure Machine Learning Data Preparation の実行環境とデータ環境のサポートされている組み合わせ | Microsoft Docs"
description: "このドキュメントでは、Azure ML Data Prep 用の各種ランタイムおよびデータ ソースのサポートされている組み合わせの全一覧を示します。"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 02e0ca96bff7781c242b4c5e965b229065e71725
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="supported-matrix-for-this-release"></a>このリリースでサポートされているマトリックス 
コードで Data Sources を使用して、または Data Preparations でデータを読み込み、Pandas または Spark データフレームのどちらかを取得するとき、実験的コンピューティング環境とデータの場所の組み合わせとして、次のものがサポートされています。

|     |ローカル ファイル  |Azure BLOB  |SQL Server データベース***  |
|---------|---------|---------|---------|---------|
|ローカルの Python    |     サポートされています    |サポートされていません         | サポートされていません        |         |
|Docker (Linux VM) Python     |プロジェクト ファイルのみでサポート*         | サポートされていません        |        サポートされていません |         |
|Docker (Linux VM) PySpark     |プロジェクト ファイルのみでサポート*     |サポートされています         | サポートされています**        |         |
|Azure Data Science Virtual Machine Python     |プロジェクト ファイルのみでサポート*         |サポートされていません         |サポートされていません         |         |
|Azure Data Science Virtual Machine PySPark     | プロジェクト ファイルのみでサポート*        |サポートされていません         |サポートされていません         |         |
|Azure HDInsight PySpark     | サポートされていません        |サポートされています         |サポートされています**         |         |
|Azure HDInsight Python     | サポートされていません        | サポートされていません        | サポートされていません        |         |

Azure Data Lake Store は現在、どのコンピューティング ターゲット向けにもサポートされていません。

ローカル ファイル パスが使用されるとき、プロジェクト内部のファイルはコンピューティング環境にコピーされた後、そこで読み取られます。 プロジェクトの外にあるファイルはコピーされず、コンピューティング環境ではパスが解決されなくなります。 コードにおいて、ローカルでの実行時はローカル ファイルを使用し、異なる実行構成では Azure Storage BLOB に切り替えることが可能になるよう、Data Source Substitution の使用を検討してください。 Data Sources でのサンプリング サポートを使用し、特定の実行構成においてのみ大規模データに対する実行を管理することもできます。

**Maven JDBC SQL Server ドライバー 6.2.1 を使用します。 このパッケージ (または互換性のあるパッケージ) が、コンピューティング環境用の spark_dependencies.yml ファイルに含まれていることを確認する必要があります。

***コンピューティング環境からデータベースと通信できるという条件のもと、Azure SQL Database、Azure SQL Data Warehouse、または Microsoft SQL Server をサポートします。 
