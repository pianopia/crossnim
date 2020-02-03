# crossnim
Crossplatform based on Nim and Vulkan

# 理想のcrossplatform
Background Graphic API: Vulkan
Nim言語で記述
コンポーネントを使いまわせる
Xcodeすら使わない

# 記述式
# 下のレイヤーから記載する
# layout.nimui

```
Container:
    key: "DealContainer"
    width:100%
    height:100%

    ListView:
        key: "DealList"
        width: 100%
        height: 100%
        canReload: true

    FloatingActionButtom:
        src: "img/test.png"
        position:

ListCell:
    isStock: true
    key: "BannerCell"
    width: 100%
    height: 50px

    Image:
        width: 100%
        height: 100%

ListCell:
    isStock: true
    key: "DealCell"
    width: 100%
    height:200px

    Image:
        width: 80%
        margin: 10px 10% 10px 10%
        radius: 5px

    Container:
        width: 100%
        height: 50px

        Text:
            key: "DealTitle"

        Text:
            key: "DealPrice"

```


# データをレイアウトにマッピング


# main.nim

```
# API
proc fetchDealList(): Seq[Deal]

proc reloadDealList():
    fetchDealList().mapData(mapDealList())

# データマッピング
proc mapDealList(deal: Deal): key.DealCell =
    var dealCell = generateUI(key.DealCell)
    dealCell.DealTitle.text = deal.dealTitle
    dealCell.DealPrice.text = deal.dealPrice

fetchDealList().mapData(mapDealList())

proc showDetail(context: Context) =
    showModal(key.DealDetail, isSetHistory: false)

main.onRunning

main.onBack =
    history.back()
    history.back(3)
    if history.current == "DealDetail":
        debugLog("DealDetail")

# ライフサイクル
key.DealContainer.onStart =
    # APIを叩いてDealCellに入れ、DealCellをDealListに入れる
    key.DealList.cell = fetchDealList().mapDealList()

key.DealContainer.onRunning =
    key.DealList.onReload(mapDealList())
    key.DealCell.onTouch()

key DealContainer.onStop =
    discard
```
