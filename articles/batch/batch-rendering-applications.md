---
title: レンダリング アプリケーション - Azure Batch
description: 事前インストールされている Batch のレンダリング アプリケーション
services: batch
ms.service: batch
author: laurenhughes
ms.author: lahugh
ms.date: 09/10/2019
ms.topic: conceptual
ms.openlocfilehash: 2b0a132c156cc12d317bf51488625191bb8091fc
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881469"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>VM イメージをレンダリングするために事前インストールされているアプリケーション

Azure Batch では任意のレンダリング アプリケーションを使用できます。 ただし、Azure Marketplace の VM イメージは、事前インストールされている一般的なアプリケーションで使用します。

該当する場合、事前インストールされているレンダリング アプリケーションを従量課金制ライセンスで使用できます。 Batch のプールが作成されたら、必要なアプリケーションを指定し、1 分ごとに VM とアプリケーションの両方の料金が発生します。 アプリケーションの料金は、[Azure Batch の料金ページ](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering)で確認できます。

一部のアプリケーションは Windows でのみサポートされますが、ほとんどは Windows と Linux の両方でサポートされます。

## <a name="applications-on-centos-7-rendering-images"></a>CentOS 7 レンダリング イメージのアプリケーション

次の一覧は、CentOS 7.6、バージョン 1.1.6 のレンダリング イメージに適用されます。

* Autodesk Maya I/O 2017 Update 5 (cut 201708032230)
* Autodesk Maya I/O 2018 Update 2 (cut 201711281015)
* Autodesk Maya I/O 2019 Update 1
* Autodesk Arnold for Maya 2017 (Arnold バージョン 5.3.1.1) MtoA-3.2.1.1-2017
* Autodesk Arnold for Maya 2018 (Arnold バージョン 5.3.1.1) MtoA-3.2.1.1-2018
* Autodesk Arnold for Maya 2019 (Arnold バージョン 5.3.1.1) MtoA-3.2.1.1-2019
* Chaos Group V-Ray for Maya 2017 (バージョン 3.60.04)
* Chaos Group V-Ray for Maya 2018 (バージョン 3.60.04)
* Blender (2.68)
* Blender (2.8)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>最新の Windows Server 2016 レンダリング イメージのアプリケーション

次の一覧は、Windows Server 2016、バージョン 1.3.7 レンダリング イメージに適用されます。

* Autodesk Maya I/O 2017 Update 5 (バージョン 17.4.5459)
* Autodesk Maya I/O 2018 Update 4 (バージョン 18.4.0.7622)
* Autodesk 3ds Max I/O 2019 Update 1 (バージョン 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Update 4 (バージョン 20.4.0.4254)
* Autodesk Arnold for Maya 2017 (Arnold バージョン 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold for Maya 2018 (Arnold バージョン 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold for 3ds Max 2018 (Arnold バージョン 5.0.2.4)(vバージョン 1.2.926)
* Autodesk Arnold for 3ds Max 2019 (Arnold バージョン 5.0.2.4)(バージョン 1.2.926)
* Chaos Group V-Ray for Maya 2018 (バージョン 3.52.03)
* Chaos Group V-Ray for 3ds Max 2018 (バージョン 3.60.02)
* Chaos Group V-Ray for Maya 2019 (バージョン 3.52.03)
* Chaos Group V-Ray for 3ds Max 2019 (バージョン 4.10.01)
* Blender (2.79)


> [!NOTE]
> Chaos Group V-ray for 3ds Max 2019 (バージョン 4.10.01) では、V-Ray に破壊的変更があります。 以前のバージョン (バージョン 3.60.02) を使用する場合は、Windows Server 2016、バージョン 1.3.2 レンダリング ノードを使用してください。

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>以前の Windows Server 2016 レンダリング イメージのアプリケーション

次の一覧は、Windows Server 2016、バージョン 1.3.2 レンダリング イメージに適用されます。

* Autodesk Maya I/O 2017 Update 5 (バージョン 17.4.5459)
* Autodesk Maya I/O 2018 Update 4 (バージョン 18.4.0.7622)  
* Autodesk 3ds Max I/O 2019 Update 1 (バージョン 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Update 4 (バージョン 20.4.0.4254)
* Autodesk Arnold for Maya 2017 (Arnold バージョン 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold for Maya 2018 (Arnold バージョン 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold for 3ds Max (Arnold バージョン 5.0.2.4)(バージョン 1.2.926)
* Chaos Group V-Ray for Maya 2019 (バージョン 3.52.03)
* Chaos Group V-Ray for 3ds Max 2018 (バージョン 3.60.02)
* Blender (2.79)

## <a name="next-steps"></a>次の手順

レンダリング VM イメージを使用するには、プール作成時にプールの構成で指定する必要があります。詳しくは、[Batch プールのレンダリング機能](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools)に関するページをご覧ください。
