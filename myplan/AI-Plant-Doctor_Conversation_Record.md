# AI-Plant-Doctor 專題對話紀錄

> 紀錄類型：整理版對話紀錄（非逐字匯出）
> 資料依據：使用者提供的 `NBLM.docx` 與本次對話中完成的資訊圖表需求。
> 目標 Repo：<https://github.com/yb-802/DIC3-AI-Plant-Doctor>
> 預定存放位置：`/myplan/`

---

## 1. 使用者需求

使用者提供 `NBLM.docx`，要求分析文件內容，製作一張 A4 橫式、高密度、具編碼的 PNG 資訊圖表。

後續使用者指定 GitHub Repo：

- Repository：`yb-802/DIC3-AI-Plant-Doctor`
- 預定目錄：`myplan`
- 本次希望將對話紀錄以 Markdown（`.md`）格式上傳至該目錄。

## 2. NBLM.docx 內容重點整理

### 2.1 專題核心定位

專題名稱／系統：**AI-Plant-Doctor**

定位為領域智慧代理人（Domain AI Agent），整合：

- AI Agent
- Domain Knowledge
- Data Analytics
- Real-World Application

目標使用者為台灣中部小型溫室番茄農與家庭園藝者。系統整合 ESP32 IoT 感測器、影像診斷模型、OOD 偵測、氣象與病害風險模型，以及植物保護知識庫。

感測資料包含溫度、濕度、光照、土壤濕度與葉面濕潤等資訊。風險分析包含 VPD 與 TOMCAST DSV；知識層包含植物保護手冊 RAG 與鑑別知識圖譜。系統由 ReAct LLM Planner 協調工具，形成多模態資料採集、差分診斷、選擇性拒答與 IPM 建議流程。

### 2.2 A2Z SOP：A–F 六階段研究故事

1. **A — Attention Getter：動機與引題**
   - 以全球病蟲害損失、氣候變遷與農業管理人力老化等議題說明需求。
   - 引用文件中的 FarmerChat 案例，說明 AI 農業顧問的應用時機。

2. **B — But：挑戰與 Research Gap**
   - 影像模型在實驗室與田間環境的表現可能有明顯落差。
   - 單靠照片不符合病害三角的診斷思維。
   - LLM 可能產生缺乏地區依據的情境幻覺。

3. **C — Cure：解決方案**
   - 提出 AI-Plant-Doctor。
   - 模仿植物醫師的診斷流程，綜合影像、環境感測與栽培紀錄。

4. **D — Development：方法與系統設計**
   - 以 LLM Planner 協調工具。
   - 使用 Log-linear pooling 融合影像機率、環境先驗（VPD、TOMCAST DSV）與知識先驗。
   - 設計主動追問與拒答機制。

5. **E — Experiments：實驗與評估**
   - 資料集：PlantVillage、PlantDoc、TOM2024。
   - Baselines：純 CNN、Zero-shot VLM、無工具 LLM、純規則 TOMCAST。
   - 指標：Macro-F1、ECE、coverage–risk（AURC）、引用支持率與 SUS。

6. **F — Findings：預期成果**
   - 提升田間診斷可靠度。
   - 正確拒答不確定案例。
   - 降低缺乏依據的用藥建議。
   - 交付完整且可重現的開源 AIoT Agent 系統。

### 2.3 五大專家思維模式

1. **病害三角**：病害涉及寄主、病原與環境；影像只是證據之一，需結合感測器時序資料與作物資訊。
2. **病徵不等於病因／鑑別診斷**：維護多個候選假說，透過排除法診斷，不直接武斷輸出單一結果。
3. **先區分生物性與非生物性因素**：先排查水分逆境、肥傷、日燒等可能性。
4. **預防重於治療（IPM）**：建議依栽培、物理、生物、化學的順序考量；藥劑建議需有引用依據。
5. **流行病學（氣象 × 時間）**：累積 TOMCAST DSV，並結合氣候風險進行預警。

### 2.4 三大專業爭論

1. **實驗室高準確率 vs. 田間泛化能力**
   - 文件指出實驗室與田間表現可能有明顯落差。
   - 系統立場：影像只能作為其中一項證據。

2. **LLM 顧問可及性 vs. 幻覺與用藥安全**
   - LLM 可擴大農業知識服務的可及性，但可能產生地區情境幻覺。
   - 對策：RAG 引用驗證與 Guardrail。

3. **感測模型驅動 vs. 專家巡田與減藥**
   - 感測模型具有減藥潛力，但感測器位置可能造成資料誤差。
   - 系統定位：輔助巡田，而非取代專家。

### 2.5 八大系統組件

| 編號 | 組件 | 內容 |
|---|---|---|
| ① | User / Scenario | LINE Bot、Web Dashboard |
| ② | Query / Input | 文字、葉片正反面與整株影像、ESP32 MQTT 感測串流 |
| ③ | Planner Agent | ReAct 意圖分類、任務分解、安全 Guardrails |
| ④ | Data Retrieval | InfluxDB 時序庫、CWA 氣象 API、向量庫檢索 |
| ⑤ | Analytics Engine | 影像診斷模型／OOD 偵測、VPD 分析、TOMCAST DSV |
| ⑥ | Domain Knowledge | 病害三角規則、症狀–病因知識圖譜、RAG 向量庫 |
| ⑦ | Decision | Log-linear pooling、τ 門檻驗證、追問／轉介 |
| ⑧ | Output | LINE 診斷報告、Grad-CAM 熱圖、感測趨勢卡片 |

### 2.6 三大技術表達

- **System Architecture**：呈現模組 ①–⑧ 的系統架構。
- **Flowchart**：包含影像品質判定、感測資料補值、τ 門檻判斷與追問迴圈。
- **Pseudocode**：Algorithm 1（Agent 差分診斷迴圈）與 Algorithm 2（IoT 環境先驗）。

## 3. 本次資訊圖表製作結果

依據上述內容製作 A4 橫式高密度資訊圖表，包含：

1. 專題核心定位
2. A–F 六階段研究故事
3. 實驗資料集與評估指標
4. 五大領域專家思維
5. 三大專業爭論
6. 八大系統組件
7. 三大技術表達與核心決策鏈
8. 預期成果與安全原則

輸出規格：

- 格式：PNG
- 尺寸：3508 × 2480 px
- 解析度：300 DPI
- 紙張：A4 橫式

產出檔案：`AI_Plant_Doctor_Infographic_A4_Landscape.png`

## 4. GitHub 上傳安排

使用者指定將本 Markdown 對話紀錄上傳至：

- Repo：`https://github.com/yb-802/DIC3-AI-Plant-Doctor`
- 目錄：`/myplan`
- 建議檔名：`AI-Plant-Doctor_Conversation_Record.md`

**上傳狀態：尚未上傳。** 需先完成 GitHub 帳號／Repo 權限連線，並確認可寫入該 Repository 後，再建立或更新檔案。
