---
title: アプリケーションをレンダリングする
description: Azure Batch では任意のレンダリング アプリケーションを使用できます。 ただし、Azure Marketplace の VM イメージは、事前インストールされている一般的なアプリケーションで使用します。
ms.date: 02/12/2021
ms.topic: how-to
ms.openlocfilehash: 18fc142d2fa9c819b9872c64ef7aae4c73cc2428
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635338"
---
# <a name="pre-installed-applications-on-batch-rendering-vm-images"></a>VM イメージを Batch レンダリングするために事前インストールされているアプリケーション

Azure Batch では任意のレンダリング アプリケーションを使用できます。 ただし、Azure Marketplace の VM イメージは、事前インストールされている一般的なアプリケーションで使用します。

該当する場合、事前インストールされているレンダリング アプリケーションを従量課金制ライセンスで使用できます。 Batch のプールが作成されたら、必要なアプリケーションを指定し、1 分ごとに VM とアプリケーションの両方の料金が発生します。 アプリケーションの料金は、[Azure Batch の料金ページ](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering)で確認できます。

一部のアプリケーションは Windows でのみサポートされますが、ほとんどは Windows と Linux の両方でサポートされます。

## <a name="applications-on-latest-centos-7-rendering-image"></a>最新の CentOS 7 レンダリング イメージのアプリケーション

次の一覧は、CentOS レンダリング イメージ (バージョン 1.2.0) に適用されます。

* Autodesk Maya I/O 2020 Update 4.6
* Autodesk Arnold for Maya 2020 (Arnold バージョン 6.2.0.0) MtoA-4.2.0-2020
* Chaos Group V-Ray for Maya 2020 (バージョン 5.00.21)
* Blender (2.80)
* AZ 10

## <a name="applications-on-latest-windows-server-rendering-image"></a>最新の Windows Server レンダリング イメージのアプリケーション

次の一覧は、Windows Server レンダリング イメージ (バージョン 1.5.0) に適用されます。

* Autodesk Maya I/O 2020 Update 4.4
* Autodesk 3ds Max I/O 2021 Update 3
* Autodesk Arnold for Maya 2020 (Arnold バージョン 6.1.0.1) MtoA-4.1.1.1-2020
* Autodesk Arnold for 3ds Max 2021 (Arnold バージョン 6.1.0.1) MAXtoA-4.2.2.20-2021
* Chaos Group V-Ray for Maya 2020 (バージョン 5.00.21)
* Chaos Group V-Ray for 3ds Max 2021 (バージョン 5.00.05)
* Blender (2.79)
* Blender (2.80)
* AZ 10

> [!IMPORTANT]
> V-Ray with Maya を [Azure Batch 拡張機能テンプレート](https://github.com/Azure/batch-extension-templates)の外で実行するには、レンダリングの前に `vrayses.exe` を開始します。 テンプレートの外で vrayses を開始するには、次のコマンドを使用します: `%MAYA_2020%\vray\bin\vrayses.exe"`。
>
> 例については、GitHub にある [Maya および V-Ray テンプレート](https://github.com/Azure/batch-extension-templates/blob/master/templates/maya/render-vray-windows/pool.template.json)の開始タスクを参照してください。

## <a name="applications-on-previous-windows-server-rendering-images"></a>以前の Windows Server レンダリング イメージのアプリケーション

次の一覧は、Windows Server 2016、バージョン 1.3.8 レンダリング イメージに適用されます。

* Autodesk Maya I/O 2017 Update 5 (バージョン 17.4.5459)
* Autodesk Maya I/O 2018 Update 6 (バージョン 18.4.0.7622)
* Autodesk Maya I/O 2019
* Autodesk 3ds Max I/O 2018 Update 4 (バージョン 20.4.0.4254)
* Autodesk 3ds Max I/O 2019 Update 1 (バージョン 21.2.0.2219)
* Autodesk 3ds Max I/O 2020 Update 2
* Autodesk Arnold for Maya 2017 (Arnold バージョン 5.3.0.2) MtoA-3.2.0.2-2017
* Autodesk Arnold for Maya 2018 (Arnold バージョン 5.3.0.2) MtoA-3.2.0.2-2018
* Autodesk Arnold for Maya 2019 (Arnold バージョン 5.3.0.2) MtoA-3.2.0.2-2019
* Autodesk Arnold for 3ds Max 2018 (Arnold バージョン 5.3.0.2)(バージョン 1.2.926)
* Autodesk Arnold for 3ds Max 2019 (Arnold バージョン 5.3.0.2)(バージョン 1.2.926)
* Autodesk Arnold for 3ds Max 2020 (Arnold バージョン 5.3.0.2)(バージョン 1.2.926)
* Chaos Group V-Ray for Maya 2017 (バージョン 4.12.01)
* Chaos Group V-Ray for Maya 2018 (バージョン 4.12.01)
* Chaos Group V-Ray for Maya 2019 (バージョン 4.04.03)
* Chaos Group V-Ray for 3ds Max 2018 (バージョン 4.20.01)
* Chaos Group V-Ray for 3ds Max 2019 (バージョン 4.20.01)
* Chaos Group V-Ray for 3ds Max 2020 (バージョン 4.20.01)
* Blender (2.79)
* Blender (2.80)
* AZ 10

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

## <a name="applications-on-previous-centos-rendering-images"></a>以前の CentOS レンダリング イメージのアプリケーション

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

## <a name="next-steps"></a>次のステップ

レンダリング VM イメージを使用するには、プール作成時にプールの構成で指定する必要があります。詳しくは、[Batch プールのレンダリング機能](./batch-rendering-functionality.md)に関するページをご覧ください。
