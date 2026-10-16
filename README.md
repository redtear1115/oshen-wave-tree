# 樹蔭協作平台（PUMA 黑客松 Issue #1）

[PUMA 黑客松 Issue #1「把三千棵樹種回來」](https://puma.taipei/hackathon#issue-1) 的提案與 Demo。

市民可以快速找到樹蔭、回報想要樹蔭的地方；政府公開處理進度，並從市民的使用與回饋知道哪些樹蔭最被需要。開放資料負責底圖與「能不能種」的初審，市民資料負責需求與優先順序。對應政見：[都市微森林](https://puma.taipei/policies/%E9%83%BD%E5%B8%82%E5%BE%AE%E6%A3%AE%E6%9E%97) 的 3-30-300 目標。

- 提案簡報：[`slides/index.html`](slides/index.html)

## 資料

`material/raw/` 為 2026-10-16 下載的原始檔，皆為真實開放資料，依[政府資料開放授權條款第 1 版](https://data.gov.tw/license)使用。

| 檔案 | 資料集 | 提供機關 | 座標 |
| --- | --- | --- | --- |
| `TaipeiTree.csv` | [臺北市行道樹及公園樹木分布圖](https://data.gov.tw/dataset/128274)：行道樹 | 工務局公園處 | TWD97 |
| `TaipeiParkTree.csv` | 同上：公園樹木 | 工務局公園處 | TWD97 |
| `TaipeiTreeHole.csv` | 同上：樹穴 | 工務局公園處 | TWD97 |
| `treepit_nwo.json` | [臺北市人行道固定設施物＿樹穴](https://data.gov.tw/dataset/134908) | 工務局新工處 | EPSG:3826 |
| `sidewalk.json` | [臺北市人行道範圍圖](https://data.taipei/dataset/detail?id=715d3a83-8445-4496-b6bf-b0900538b7e7) | 工務局新工處 | EPSG:3826 |
| `parks.json` | [臺北市公園基本資料](https://data.taipei/dataset/detail?id=ea732fb5-4bec-4be7-93f2-8ab91e74a6c6) | 工務局公園處 | WGS84 |

`material/processed/` 為處理後的檔案：

- `treepits_merged.geojson`：兩份樹穴資料合併（WGS84）。由 `scripts/merge_treepits.py` 產生。

模擬資料（回報、+1、評價、處理進度、移除紀錄）尚未加入；加入時會放在獨立資料夾並在此標明。

## 重新產生

需要 [uv](https://docs.astral.sh/uv/)：

```sh
uv run scripts/merge_treepits.py
```

### 樹穴合併規則

- 公園處 CSV 有重複列，同一樹穴常以「路名」與「行政區」各出現一次，先依 `TreeHoleID` 合併。
- 公園處樹穴點位距新工處樹穴多邊形 2 m 內視為同一樹穴（`source: both`），否則新增為點位（`source: pkl`）。
- `has_tree`：1.5 m 內有登錄的行道樹或公園樹。`false` 只代表「候選空樹穴」，可能是座標偏移或新植未登錄，需現場確認。
- 公園處樹穴沒有行政區時，取最近新工處樹穴的行政區（`district_inferred: true`）。
