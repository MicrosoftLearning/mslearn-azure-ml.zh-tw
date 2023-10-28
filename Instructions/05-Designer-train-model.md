---
lab:
  title: 使用 Azure Machine Learning Designer定型模型
---

# 使用 Azure Machine Learning Designer定型模型

Azure Machine Learning Designer提供拖放介面，可讓您定義工作流程。 您可以建立工作流程來定型模型、測試及比較多個演算法，並輕鬆進行。

在此練習中，您將使用Designer來快速定型和比較兩種分類演算法。

## 開始之前

您將需要具有系統管理層級存取權的 [Azure 訂用帳戶](https://azure.microsoft.com/free?azure-portal=true)。

## 佈建 Azure Machine Learning 工作區

Azure Machine Learning *工作區* 提供集中位置來管理您需要定型和管理模型的所有資源和資產。 您可以透過 Studio、Python SDK 和 Azure CLI 與 Azure Machine Learning 工作區互動。

您將使用使用 Azure CLI 來布建工作區和必要資源的 Shell 腳本。 接下來，您將使用Azure Machine Learning 工作室中的Designer來定型和比較模型。

### 建立工作區和計算叢集

若要建立 Azure Machine Learning 工作區和計算叢集，您將使用 Azure CLI。 所有必要的命令都會分組在殼層腳本中，供您執行。

1. 在瀏覽器中，開啟位於 的 `https://portal.azure.com/` Azure 入口網站，使用您的 Microsoft 帳戶登入。
1. 選取搜尋 \[ 方塊右側頁面頂端的>_*] (Cloud Shell*) 按鈕。 這會在入口網站底部開啟 Cloud Shell 窗格。
1. 如果系統詢問，請選取 **[Bash** ]。 第一次開啟 Cloud Shell 時，系統會要求您選擇您想要使用 (*Bash* 或 *PowerShell*) 的殼層類型。
1. 如果系統要求您為 Cloud Shell 建立 **儲存體** ，請檢查是否已指定正確的訂用帳戶，然後選取 [建立儲存體]。 等候儲存體建立。
1. 在終端機中，輸入下列命令以複製此存放庫：

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > 使用 `SHIFT + INSERT` 將複製的程式碼貼入Cloud Shell。

1. 複製存放庫之後，輸入下列命令來變更此實驗室的資料夾，並執行它所包含的 setup.sh 腳本：

    ```azurecli
    cd azure-ml-labs/Labs/05
    ./setup.sh
    ```

    > 忽略任何 (錯誤，) 表示未安裝延伸模組的訊息。

1. 等候腳本完成 - 這通常需要大約 5-10 分鐘的時間。

## 設定新的管線

當您建立工作區和必要的計算叢集時，您可以開啟Azure Machine Learning 工作室，並使用Designer建立定型管線。

1. 在Azure 入口網站中，流覽至名為**mlw-dp100-... 的**Azure Machine Learning 工作區。
1. 選取 Azure Machine Learning 工作區，然後在其 [ **概觀** ] 頁面中，選取 **[啟動 Studio**]。 另一個索引標籤會在瀏覽器中開啟，以開啟Azure Machine Learning 工作室。
1. 關閉任何出現在 Studio 中的快顯視窗。
1. 在Azure Machine Learning 工作室中，流覽至 [**計算**] 頁面，並確認您在上一節中建立的計算叢集是否存在。 叢集應該閒置，且有 0 個節點正在執行。
1. 流覽至**Designer**頁面。
1. 選取頁面頂端的 [ **自訂** ] 索引標籤。
1. 使用自訂群組件建立新的空白管線。
1. 選取右邊的鉛筆圖示，將預設管線名稱 (**Pipeline-Created-on-date*...) `Train-Diabetes-Classifier` 變更為 。


## 建立新管線

若要定型模型，您需要資料。 您可以使用儲存在資料存放區中的任何資料，或使用可公開存取的 URL。

1. 在左側功能表中，選取 [ **資料] 索引** 標籤。
1. 將 **diabetes-folder** 元件拖放至畫布。

    既然您已擁有資料，您可以使用工作區內已存在的自訂群組件來建立管線， (已在安裝) 期間為您建立。

1. 在左側功能表中，選取 [ **元件]** 索引標籤。
1. 將 [ **移除空白資料列** ] 元件拖放到 **畫布的 diabetes-folder**下方。
1. 將資料的輸出連接到新元件的輸入。
1. 將 [ **標準化數值** 資料行] 元件拖放到畫布的 [ **移除空白資料列**] 下方。
1. 將上一個元件的輸出連接到新元件的輸入。
1. 將定型 **決策樹分類器模型** 元件拖放到畫布的 **[標準化數值資料行**] 下方。
1. 將上一個元件的輸出連接到新元件的輸入。
1. 選取 [ **設定 & 提交** ]，然後在 [ **設定管線作業** ] 頁面中建立新的實驗並將其命名為 `diabetes-designer-pipeline` ，然後選取 [ **下一步**]。
1. 在 [ **輸入] & [輸出** ] 不會進行任何變更，然後選取 [ **下一步**]。
1. 在 **[執行時間設定** ] 上，選取 **[計算叢集**]，然後在 [ **選取 Azure ML 計算叢集** ] 下選取 *您的 aml-cluster*。
1. 選取 **[檢閱 + 提交** ]，然後選取 [ **提交** ] 以啟動管線執行。
1. 您可以移至 [ **管線** ] 頁面並選取 **Train-Diabetes-Classifier** 管線，以檢查執行的狀態。
1. 等到所有元件都成功完成。

    提交作業將會初始化計算叢集。 計算叢集閒置到現在為止，叢集可能需要一些時間才能調整為超過 0 個節點。 一旦叢集調整大小，它就會自動開始執行管線。

您將能夠追蹤每個元件的執行。 當管線失敗時，您將能夠探索哪個元件失敗，以及失敗的原因。 錯誤訊息會顯示在作業概觀的 [ **輸出 + 記錄** ] 索引標籤中。

## 將第二個模型定型以比較

若要比較演算法和評估效能更好，您可以在一個管線內定型兩個模型並進行比較。

1. 返回**Designer**，然後選取**Train-Diabetes-Classifier**管線草稿。
1. 將訓練 **羅吉斯回歸分類器模型** 元件新增至畫布，再新增至另一個定型元件旁。
1. 將 **標準化數值** 資料行元件的輸出連接到新定型元件的輸入。
1. 在頂端，選取 [ **設定 & 提交**]。
1. 在 [ **基本]** 頁面上，建立名為 `designer-compare-classification` 的新實驗，並加以執行。
1. 選取 **[檢閱 + 提交** ]，然後選取 [ **提交** ] 以啟動管線執行。
1. 您可以移至 **[管線**] 頁面，並使用**designer-compare-classification**實驗選取**Train-Diabetes-Classifier**管線，以檢查執行的狀態。
1. 等到所有元件都成功完成。  
1. 選取 **[作業概觀**]，然後選取 [ **計量] 索引** 標籤，以檢閱這兩個訓練元件的結果。
1. 請嘗試並判斷哪一個模型執行得更好。

## 刪除 Azure 資源

當您完成探索 Azure Machine Learning 時，應該刪除您所建立的資源，以避免不必要的 Azure 成本。

1. 關閉 [Azure Machine Learning 工作室] 索引標籤，並返回Azure 入口網站。
1. 在 Azure 入口網站的 [首頁] 上，選取 [資源群組]。
1. 選取 **rg-dp100-...** 資源群組。
1. 在資源群組的 [概觀] 頁面頂端，選取 [刪除資源群組]。
1. 輸入資源群組名稱以確認要刪除，然後選取 [刪除]。
