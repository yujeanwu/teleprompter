# 口播提詞錄影機

手機用的單頁網頁:前鏡頭錄影 + 提詞。整個程式只有 `index.html`(沒有建置步驟、沒有外部套件,只從 Google Fonts 載字型)。

- 公開網址:https://yujeanwu.github.io/teleprompter/
- GitHub repo:yujeanwu/teleprompter,GitHub Pages 從 `main` 根目錄發佈
- 更新方式:改 `index.html` → commit → push,大約 1~2 分鐘後網址會更新。手機上要重新整理才會看到新版。

## 功能

- 編輯頁:貼講稿(空一行＝換段,`**字**` 標黃色重點),設定速度、字級、提詞區高度、視線位置,存在手機的 localStorage。
- 錄影頁:`getUserMedia` 開前鏡頭(預覽畫面左右鏡像,錄出來的不鏡像),`MediaRecorder` 錄影,優先錄 mp4,不行才用 webm。錄完可以回放,用 Web Share 存到相簿,或直接下載。
- 跟著聲音捲動(預設開):`webkitSpeechRecognition`(zh-TW、continuous、interim)。拿最後聽到的 10 個字,在目前位置前 40 字到後 80 字的範圍裡用 LCS 找最像的地方,把字幕對過去。往回跳要更高的相似度才會跳。辨識斷掉會自動重開,失敗太多次或沒有權限時,改回固定速度捲動。
  - `recognition.start(audioTrack)`:新版 Chrome 可以直接用錄影那條麥克風。不支援的瀏覽器會忽略這個參數。
- 每個字都包成 `<span data-k>`,`norm[]` 是去掉標點、轉小寫後的字。`tops[]` 是每個字的 offsetTop,用來換算捲動位置。

## 測試注意

- 開相機需要 https 或 localhost:`python -m http.server 8765` 之後打開 http://localhost:8765/。
- Claude 的瀏覽器窗格不能開相機和麥克風,窗格隱藏時 requestAnimationFrame 也不會跑。測試時用 canvas.captureStream 假裝相機、用假的 SpeechRecognition 類別送辨識結果,再把 requestAnimationFrame 換成 setTimeout。
- 還沒在真的手機上驗證的部分:錄影和語音辨識同時搶麥克風的狀況,以及 Android 重開辨識時的提示音會不會被錄進去。
