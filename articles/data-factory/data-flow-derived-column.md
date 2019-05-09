---
title: Azure Data Factory Data Flow Derived Column Transformation
description: Azure Data Factory Data Flow Derived Column Transformation
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 1335b86431bcf345f452ead0c95926d295ae185f
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728762"
---
# <a name="azure-data-factory-data-flow-derived-column-transformation"></a>Azure Data Factory Data Flow Derived Column Transformation

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Use the Derived Column transformation to generate new columns in your data flow or to modify existing fields.

![derive column](media/data-flow/dc1.png "Derived Column")

You can perform multiple Derived Column actions in a single Derived Column transformation. Click "Add Column" to transform more than 1 column in the single transformation step.

In the Column field, either select an existing column to overwrite with a new derived value, or click "Create New Column" to generate a new column with the newly derived value.

The Expression text box will open the Expression Builder where you can build the expression for the derived columns using expression functions.
