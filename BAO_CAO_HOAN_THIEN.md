# BAO CAO HOAN THIEN DE TAI PHAN TICH DU LIEU MANG XA HOI

Tai lieu nay giai thich de hieu tung phan theo khung 3.1 -> 3.6 de ban co the nop bao cao va thuyet trinh.

## 3.1. Mo ta bai toan va du lieu thuc nghiem

### Bai toan
- Phan tich du lieu bai dang mang xa hoi (Twitter/X) de hieu yeu to nao tac dong den muc do tuong tac.
- Xay dung mo hinh du doan `engagement` (tong luot tuong tac) cho bai dang.

### Muc tieu
- Hieu hanh vi tuong tac cua nguoi dung theo noi dung va thoi gian.
- Tim ra khung gio va loai noi dung co hieu qua cao.
- Tao mo hinh ho tro du doan hieu qua bai dang.

### Du lieu su dung
- Tap du lieu: `data/Data LIWC 01 02 23.csv`
- Quy mo sau khi lam sach: ~23,006 bai dang
- Cac cot nghiep vu chinh:
  - `content`: noi dung bai dang
  - `time_post`: thoi gian dang bai
  - `likes`: luot thich
  - `shares`: luot chia se
  - `comments`: luot binh luan
  - `media_type`: loai noi dung

## 3.2. Phan tich va khao sat du lieu ban dau (EDA so bo)

Muc tieu EDA la tra loi 3 cau hoi don gian:
1) Du lieu co day du va hop le khong?
2) Tuong tac phan bo nhu the nao?
3) Co xu huong noi bat theo gio dang hoac loai noi dung khong?

Cac noi dung da thuc hien trong notebook:
- Kiem tra kich thuoc du lieu, kieu du lieu tung cot.
- Thong ke mo ta cho likes/shares/comments/engagement.
- Xem cac bai dang co tuong tac cao.
- Khao sat tuong tac theo gio dang va theo `media_type`.

## 3.3. Tien xu ly du lieu

Day la phan quan trong de dam bao dau vao cho phan tich va mo hinh:

1. Chuan hoa ten cot
- Anh xa cot goc sang schema de de xu ly:
  - `Status text` -> `content`
  - `Date1` -> `time_post`
  - `like_count` -> `likes`
  - `retweet_count` -> `shares`
  - `reply_count` -> `comments`
  - `ContentType` -> `media_type`

2. Lam sach du lieu
- Kiem tra va xu ly gia tri thieu (null).
- Loai bo ban ghi trung lap (duplicate).
- Chuyen `time_post` sang dang datetime.
- Chuyen likes/shares/comments sang so, xu ly gia tri am ve 0.

3. Tao dac trung (feature engineering)
- `engagement = likes + shares + comments`
- `hour_posted`: gio dang bai
- `day_posted`: thu/ngay dang bai
- `content_length`: do dai noi dung
- Va mot so bien mo rong khac trong notebook.

Ket qua: du lieu tu 7 cot goc duoc mo rong len ~19 cot de phan tich va huan luyen mo hinh.

## 3.4. Truc quan hoa du lieu

Du an da tao va luu bo hinh anh trong thu muc `output/`:
- `viz_01_overview.png`
- `viz_02_time_analysis.png`
- `viz_03_time_series.png`
- `viz_04_content_heatmap.png`
- `viz_05_content_analysis.png`
- `viz_06_model_evaluation.png`
- `exploratory_analysis.png`

Y nghia truc quan:
- Bieu do phan bo cho thay engagement lech manh (co outlier).
- Bieu do theo gio cho thay khung gio co xu huong tuong tac cao.
- Bieu do theo loai noi dung cho thay su khac biet hieu qua giua cac nhom noi dung.
- Heatmap tuong quan giup nhin nhanh moi lien he giua cac bien.

## 3.5. Phan tich du lieu / Xay dung mo hinh

### Bai toan mo hinh hoa
- Du doan bien lien tuc `engagement` => bai toan hoi quy.

### Quy trinh
- Chon tap dac trung (so + phan loai sau ma hoa).
- Chia train/test (80/20) bang `train_test_split`.
- Huan luyen 3 mo hinh:
  1) Linear Regression
  2) Random Forest Regressor
  3) Gradient Boosting Regressor

### Chi so danh gia
- `R2`: do giai thich phuong sai (cang cao cang tot).
- `MAE`: sai so tuyet doi trung binh (cang thap cang tot).
- `RMSE`: can bac hai sai so binh phuong trung binh (cang thap cang tot).

## 3.6. Ket qua thuc nghiem

Ket qua trong notebook:
- Linear Regression: `R2 = -0.0030`, `MAE = 153.95`, `RMSE = 708.25`
- Random Forest: `R2 = -0.2118`, `MAE = 172.33`, `RMSE = 778.51`
- Gradient Boosting: `R2 = -0.1859`, `MAE = 160.57`, `RMSE = 770.13`

### Dien giai de hieu
- Tat ca `R2` am => mo hinh hien tai chua du doan tot hon baseline trung binh.
- Tuy nhien, du an van co gia tri o goc do:
  - Lam sach du lieu dung quy trinh
  - EDA va truc quan hoa ro rang
  - Co so sanh nhieu mo hinh va metric day du

### Nguyen nhan co the khien mo hinh chua tot
- Engagement thuong lech manh, nhieu outlier.
- Dac trung van con don gian, chua khai thac sau tu text.
- Co the thieu bien giai thich quan trong (chu de, sentiment, hashtag, network effects...).

### Huong cai thien de bao ve phan ket qua
1. Doi target:
- Du doan `log1p(engagement)` thay vi engagement thuan de giam lech.

2. Nang cap dac trung:
- Them text features (TF-IDF/embeddings), do dai hashtag, emoji, mention.
- Tach dac trung thoi gian chi tiet hon (cuoi tuan, gio cao diem).

3. Xu ly outlier:
- Winsorize/clip theo percentile hoac robust scaling.

4. Tinh chinh mo hinh:
- Dung `GridSearchCV`/`RandomizedSearchCV`.
- Thu them XGBoost/LightGBM (neu duoc phep moi truong).

5. Danh gia chat che hon:
- K-fold cross-validation.
- Bao cao them baseline va confidence interval.

## 3.7. Phan mo rong (lam them) — Phan tich cam xuc & Mo hinh phan loai

Day la phan LAM THEM so voi pipeline co ban (3.1-3.6), bo sung huong NLP va dat lai bai toan mo hinh.

### 3.7.1. Phan tich cam xuc (Sentiment Analysis)
- Phuong phap: tu dien cam xuc (lexicon-based), khong can thu vien ngoai.
- Co xu ly tu phu dinh (not, never...) de dao dau diem cam xuc.
- Gan moi bai dang mot `sentiment_score`; phan loai: Tich cuc / Trung tinh / Tieu cuc.
- Ket qua phan bo: ~64.8% Trung tinh, ~34.0% Tich cuc, ~1.2% Tieu cuc.
- Bo sung `sentiment_score` lam dac trung moi cho mo hinh phan loai.
- Hinh: `output/viz_07_sentiment_analysis.png`.

### 3.7.2. Bai toan phan loai "tuong tac cao / thap"
- Dat lai bai toan: thay vi du doan gia tri engagement (hoi quy, R2 thap/am),
  du doan bai dang co tuong tac cao hay khong (`high_engagement = engagement > trung vi`).
- Dac trung: ket hop TF-IDF tren `content` (uni-gram + bi-gram, 2000 features)
  voi cac dac trung so (gio dang, ngay, do dai, hashtag/mention/url, sentiment_score, media_type).
- Mo hinh: Logistic Regression va Random Forest.
- Hinh: `output/viz_08_classification.png` (ma tran nham lan, ROC, top tu khoa).

### 3.7.3. Ket qua phan mo rong
- Logistic Regression: Accuracy = 0.7925, Precision = 0.7869, Recall = 0.7990, F1 = 0.7929, ROC-AUC = 0.8717
- Random Forest:       Accuracy = 0.7923, Precision = 0.7888, Recall = 0.7950, F1 = 0.7919, ROC-AUC = 0.8738

### 3.7.4. Y nghia
- Ket qua phan loai TOT VA RO RANG (Accuracy ~79%, ROC-AUC ~0.87), khac han R2 am cua hoi quy.
- Cho thay: voi du lieu mang xa hoi lech manh, viec DAT LAI bai toan (phan loai) va
  KHAI THAC VAN BAN (TF-IDF) hieu qua hon nhieu so voi ep mo hinh hoi quy gia tri tuyet doi.
- TF-IDF tren noi dung dong gop lon: nhieu tu khoa cu the la tin hieu manh cho bai tuong tac cao.

## Ket luan ngan gon de trinh bay

- Du an da hoan thanh day du pipeline khoa hoc du lieu:
  **Mo ta bai toan -> Tien xu ly -> EDA -> Truc quan -> Mo hinh -> Danh gia.**
- Phan manh nhat hien tai la EDA, tien xu ly va phan tich truc quan.
- Da bo sung PHAN LAM THEM (3.7): phan tich cam xuc + mo hinh phan loai,
  cho ket qua tot (Accuracy ~79%, ROC-AUC ~0.87) — giai quyet diem yeu R2 am cua phan hoi quy.

## Huong dan su dung tai lieu nay khi bao cao

- Neu giang vien hoi "Da du yeu cau chua?": tra loi "Da du 3.1-3.6 va co them phan lam them 3.7 (sentiment + phan loai) voi ket qua tot (Accuracy ~79%, AUC ~0.87)."
- Neu hoi "Phan lam them la gi?": tra loi "Bo sung NLP: phan tich cam xuc lexicon-based va dat lai bai toan thanh phan loai tuong tac cao/thap dung TF-IDF, cho ket qua tot hon han hoi quy."
- Neu hoi "Gia tri chinh cua de tai la gi?": nhan manh insight theo thoi gian/noi dung va quy trinh phan tich dung chuan.
- Neu hoi "Vi sao mo hinh chua cao?": tra loi bang 3 y: target lech, feature chua sau, outlier manh.
