---
title: Azure Portal を使用したメディア処理のスケール設定 | Microsoft Docs
description: このチュートリアルでは、Azure Portal を使用したメディア処理のスケール設定の手順について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/04/2017
ms.author: juliako
ms.openlocfilehash: 51973916c97282ac93032ab833402d9d1356647e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
ms.locfileid: "33785781"
---
# <a name="change-the-reserved-unit-type"></a>予約ユニットの種類の変更
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [ポータル](media-services-portal-scale-media-processing.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

> [!NOTE]
> 最新バージョンの Java SDK を入手し、Java での開発を始めるには、「[Azure Media Services 用 Java クライアント SDK の概要](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use)」を参照してください。 <br/>
> Media Services 用の最新の PHP SDK をダウンロードするには、[Packagist リポジトリ](https://packagist.org/packages/microsoft/windowsazure#v0.5.7)でバージョン 0.5.7 の Microsoft/WindowAzure パッケージを検索してください。  

## <a name="overview"></a>概要

Media Services アカウントは、メディア処理タスクを処理する速度を決定する予約ユニットの種類に関連付けられます。 予約ユニットの種類は、**S1**、**S2**、**S3** から選択できます。 たとえば、同じエンコード ジョブの場合に、予約ユニットの種類として **S1** よりも **S2** を使用する方が、ジョブの実行が高速になります。

予約ユニットの種類を指定するだけでなく、**予約ユニット** (RU) を使用したアカウントのプロビジョニングを指定することもできます。 プロビジョニングされた RU の数によって、所定のアカウントで並列処理できるメディア タスクの数が決まります。

>[!NOTE]
>RU は、Azure Media Indexer を使用するインデックス作成ジョブを含む、すべてのメディア処理を並列化するために動作します。 ただし、エンコードとは異なり、インデックス作成ジョブでは高速予約ユニットを使用した高速処理は行われません。

> [!IMPORTANT]
> メディア処理のスケール設定の詳細については、 [概要](media-services-scale-media-processing-overview.md) に関するトピックを必ず確認してください。
> 
> 

## <a name="scale-media-processing"></a>メディア処理のスケール設定
予約ユニットの種類と予約ユニットの数を変更するには、以下の手順に従います。

1. [Azure Portal](https://portal.azure.com/) で Azure Media Services アカウントを選択します。
2. **[設定]** ウィンドウで **[メディア占有ユニット]** をクリックします。
   
    選択した予約ユニットの種類の予約ユニット数を変更するには、画面上部にある **[Media Served Units]\(メディア予約ユニット\)** スライダーを使用します。
   
    **[占有ユニットの種類]** を変更するには、**[Speed of reserved processing units]\(占有処理ユニットの速度\)** バーをクリックします。 次に、必要な価格レベルとして、S1、S2、または S3 を選択します。
   
3. [保存] ボタンを押して、変更を保存します。
   
    [保存] をクリックすると、新しい予約ユニットが割り当てられます。

## <a name="next-steps"></a>次の手順
Media Services のラーニング パスを確認します。

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

