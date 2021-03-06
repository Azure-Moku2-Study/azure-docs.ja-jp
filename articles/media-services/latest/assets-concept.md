---
title: Azure Media Services のアセット | Microsoft Docs
description: この記事では、アセットとは何かについて説明し、Azure Media Services によるそれらの使用方法についても説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 61555eb6cca6995215ce43051abbda9aa43539ec
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284840"
---
# <a name="assets"></a>アセット

**アセット** には、デジタル ファイル (ビデオ、オーディオ、画像、サムネイルのコレクション、テキスト トラック、クローズド キャプション ファイルなど) と、それらのファイルに関するメタデータが含まれます。 デジタル ファイルがアセットにアップロードされた後は、Media Services エンコードおよびストリーミング ワークフローで使用できます。

アセットは [Azure Storage アカウント](storage-account-concept.md)内の BLOB コンテナーにマップされ、アセット内のファイルはブロック BLOB としてそのコンテナーに格納されます。 ストレージ SDK クライアントを使用して、コンテナー内のアセット ファイルを操作できます。

Azure Media Services は、アカウントが汎用 v2 (GPv2) を使用しているときに、BLOB 層をサポートします。 GPv2 を使用して、クール ストレージまたはコールド ストレージにファイルを移動できます。 コールド ストレージは、不要になったソース ファイルをアーカイブするのに適しています (エンコード後など)。

Media Services v3 では、アセットまたは HTTP(S) URL からジョブの入力を作成できます。 ジョブに対する入力として使用できるアセットを作成するには、[ローカル ファイルからのジョブの入力の作成](job-input-from-local-file-how-to.md)に関する記事を参照してください。

「[Media Services のストレージ アカウント](storage-account-concept.md)」と「[Transform と Job](transform-concept.md)」も参照してください。

## <a name="asset-definition"></a>アセットの定義

次の表は、アセットのプロパティとその定義を示しています。

|Name|type|説明|
|---|---|---|
|ID|文字列|リソースの完全修飾リソース ID。|
|name|文字列|リソースの名前。|
|properties.alternateId |文字列|アセットの代替 ID。|
|properties.assetId |文字列|アセット ID。|
|properties.container |文字列|アセットの BLOB コンテナーの名前。|
|properties.created |文字列|アセットの作成日。|
|properties.description |文字列|アセットの説明。|
|properties.lastModified |文字列|アセットの最終変更日。|
|properties.storageAccountName |文字列|ストレージ アカウントの名前。|
|properties.storageEncryptionFormat |AssetStorageEncryptionFormat |アセットの暗号化形式。 None または MediaStorageEncryption のいずれか。|
|type|文字列|リソースの種類。|

完全な定義については、「[Assets](https://docs.microsoft.com/rest/api/media/assets)」(アセット) を参照してください。

## <a name="filtering-ordering-paging"></a>フィルター処理、順序付け、ページング

Media Services は、アセットに対して次の OData クエリ オプションをサポートしています。 

* $filter 
* $orderby 
* $top 
* $skiptoken 

### <a name="filteringordering"></a>フィルター処理/順序付け

次の表は、これらのオプションをアセット プロパティに適用する方法をまとめたものです。 

|Name|filter|順序|
|---|---|---|
|ID|サポート:<br/>等しい<br/>より大きい<br/>より小さい|サポート:<br/>昇順<br/>降順|
|name|||
|properties.alternateId |サポート:<br/>等しい||
|properties.assetId |サポート:<br/>等しい||
|properties.container |||
|properties.created|サポート:<br/>等しい<br/>より大きい<br/>より小さい|サポート:<br/>昇順<br/>降順|
|properties.description |||
|properties.lastModified |||
|properties.storageAccountName |||
|properties.storageEncryptionFormat | ||
|type|||

次の C# 例は、作成日に基づいてフィルター処理を実行しています。

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

### <a name="pagination"></a>改ページ位置の自動修正

改ページ位置の自動修正は、4 つの有効な各並べ替え順序でサポートされています。 

クエリ応答に多数の項目が含まれている場合 (現在 1,000 を超えている場合)、サービスは "\@odata.nextLink" プロパティを返して結果の次のページを取得します。 この方法を利用して、結果セット全体のページングを実行できます。 ユーザーはページ サイズを構成できません。 

コレクションのページング中にアセットが作成または削除された場合、(コレクションの中で、ダウンロードされていない部分に対する変更の場合) その変更は返される結果に反映されます。 

次の C# 例は、アカウント内のすべてのアセットを列挙する方法を示しています。

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

REST の例については、「[Assets - List](https://docs.microsoft.com/rest/api/media/assets/list)」(アセット - リスト) を参照してください。


## <a name="storage-side-encryption"></a>ストレージ側の暗号化

保存時のアセットを保護するには、ストレージ側の暗号化でアセットを暗号化する必要があります。 次の表では、Media Services でのストレージ側暗号化のしくみを示します。

|暗号化オプション|説明|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services のストレージの暗号化|AES-256 暗号化、Media Services によって管理されるキー|サポートされています<sup>(1)</sup>|サポートされていません<sup>(2)</sup>|
|[Storage Service Encryption for Data at Rest](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Azure Storage によって提供されるサーバー側暗号化、Azure またはお客様が管理するキー|サポートされています|サポートされています|
|[ストレージ クライアント側暗号化](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Azure Storage によって提供されるクライアント側暗号化、お客様が Key Vault で管理するキー|サポートされていません|サポートされていません|

<sup>1</sup> Media Services は、クリアな、どのような形式でも暗号化されていないコンテンツの処理をサポートしますが、そうすることは推奨されません。

<sup>2</sup> Media Services v3 では、ストレージの暗号化 (AES-256 暗号化) は、Media Services v2 でアセットを作成した場合の下位互換性のためにのみサポートされています。 つまり、v3 は、既存のストレージの暗号化済みアセットでは動作しますが、そのようなアセットを新規作成することはできません。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ファイルのストリーミング](stream-files-dotnet-quickstart.md)
