# Tài Liệu Dự Án: Xử Lý và Chuẩn Bị Dataset 1 Tỷ Trích Dẫn Khoa Học

## Tổng Quan Dự Án

Dự án này nhằm mục đích chuẩn bị một dataset lớn chứa 1 tỷ trích dẫn khoa học (1 Billion Citation Dataset) để phục vụ cho các tác vụ xử lý ngôn ngữ tự nhiên (Natural Language Processing - NLP). Dữ liệu sau khi xử lý sẽ được sử dụng cho các ứng dụng như phân loại văn bản (text classification), mô hình chủ đề (topic modeling), hoặc phân tích ngữ nghĩa (semantic analysis).

## Công Nghệ Sử Dụng

### Thư Viện Chính
- **Pandas**: Xử lý và quản lý dữ liệu dạng bảng (DataFrame)
- **NumPy**: Tính toán số học và xử lý mảng
- **Joblib**: Thực hiện tính toán song song (parallel processing) để tăng tốc độ xử lý
- **NLTK (Natural Language Toolkit)**: Các công cụ xử lý ngôn ngữ tự nhiên bao gồm loại bỏ stopwords, stemming và lemmatization
- **spellchecker**: Sửa lỗi chính tả trong văn bản
- **BeautifulSoup**: Xóa các thẻ HTML từ dữ liệu
- **regex (re)**: Trích xuất thông tin từ chuỗi văn bản sử dụng biểu thức chính quy

### Phiên Bản Python
- Python 3.12.7

## Cấu Trúc Dữ Liệu

### Dữ Liệu Đầu Vào
- **File gốc**: `1 Billion Citation Dataset, v1 (151).csv`
- **Kích thước**: 4,516,057 dòng, 5 cột
- **Cột dữ liệu**:
  - `doi`: Mã định danh của tài liệu
  - `articleType`: Loại bài báo
  - `citationStyle`: Định dạng trích dẫn
  - `citationStringAnnotated`: Chuỗi trích dẫn được chú thích (dạng XML)
  - `Unnamed: 4`: Cột trống

### Dữ Liệu Đầu Ra
- **File cuối cùng**: `1 Billion Citation Dataset, v1 (150)(done_text).csv`
- **Kích thước cuối**: 4,300,380 dòng, 16 cột
- **Cột dữ liệu**:
  - Thông tin gốc: Authors, Title, Publisher, Journal, Year, Volume, Issue, Pages
  - Thông tin được xử lý: Title (đã qua xử lý NLP)

## Quy Trình Xử Lý

### Giai Đoạn 1: Cấu Hình và Thiết Lập

**Lớp Config**
### Giai Đoạn 2: Trích Xuất Dữ Liệu từ XML

**Hàm extract_fields(text)**
- Chức năng: Trích xuất các trường thông tin từ chuỗi XML
- Đầu vào: Chuỗi XML chứa thông tin trích dẫn
- Đầu ra: Dictionary chứa các trường đã trích xuất
- Chi tiết trích xuất:
  - Authors: Trích xuất tác giả từ thẻ <author>
  - Title: Trích xuất tiêu đề từ thẻ <title>
  - Publisher: Trích xuất nhà xuất bản từ thẻ <publisher>
  - Journal: Trích xuất tên tạp chí từ thẻ <container-title>
  - Year: Trích xuất năm từ thẻ <issued><year>
  - Volume: Trích xuất số tập từ thẻ <volume>
  - Issue: Trích xuất số phát hành từ thẻ <issue>
  - Pages: Trích xuất trang từ thẻ <page>
  - URL: Trích xuất URL từ thẻ <URL>

### Giai Đoạn 3: Làm Sạch Dữ Liệu Cơ Bản

**Hàm remove_punctuation(text)**
- Chức năng: Loại bỏ tất cả dấu câu từ văn bản
- Xóa các ký tự: ! " # $ % & ' ( ) * + , - . / : ; < = > ? @ [ \ ] ^ _ ` { | } ~ và các ký tự đặc biệt khác
- Ứng dụng: Chuẩn bị dữ liệu cho xử lý NLP

**Xử lý các cột dữ liệu**:
- Year, Issue, Volume: Chuyển đổi sang kiểu số (int), nếu không chuyển được sẽ là None
- Pages: Giữ nguyên nếu là số hoặc có định dạng "số–số", nếu không sẽ là "no information"
- URLs: Loại bỏ tất cả đường dẫn web từ các trường Authors, Publisher, Journal

**Kết quả**: Giảm từ 4,538,038 dòng xuống 4,300,380 dòng (loại bỏ 237,658 dòng không có tiêu đề)

### Giai Đoạn 4: Nhóm Tiêu Đề Duy Nhất

**Hàm groupby() kết hợp với apply()**
- Chức năng: Gom nhóm tất cả các dòng có tiêu đề giống nhau
- Kết quả: Tạo 4,644 tiêu đề duy nhất
- Mục đích: Tối ưu hóa xử lý bằng cách tránh xử lý cùng một tiêu đề nhiều lần

### Giai Đoạn 5: Xử Lý Ngôn Ngữ Tự Nhiên (NLP)

#### 5.1 Hàm remove_punctuation(text)
- Chức năng: Loại bỏ dấu câu
- Ký tự bị xóa: Tất cả dấu câu tiếng Anh và một số ký tự đặc biệt Unicode

#### 5.2 Hàm remove_stopwords(text)
- Chức năng: Loại bỏ các từ dừng (stopwords) của tiếng Anh
- Stopwords là: the, a, an, and, or, but, in, on, at, to, for, of, v.v.
- Tác dụng: Giảm kích thước dữ liệu và loại bỏ các từ không mang nhiều ý nghĩa

#### 5.3 Hàm remove_freqwords(text)
- Chức năng: Loại bỏ 10 từ xuất hiện nhiều nhất trong dataset
- Cơ sở: Sử dụng Counter từ thư viện collections để thống kê tần suất từ
- Tác dụng: Loại bỏ các từ thông dụng không mang thông tin quan trọng

#### 5.4 Hàm lemmatize_words(text)
- Chức năng: Chuyển đổi từ về dạng cơ bản (lemma)
- Công nghệ: Sử dụng WordNetLemmatizer từ NLTK
- Ví dụ: "running", "runs", "ran" --> "run"
- Tác dụng: Giảm độ phức tạp từ vựng, nhóm các biến thể của từ

#### 5.5 Hàm stem_words(text)
- Chức năng: Rút gọn từ về căn cơ bản (stem)
- Công nghệ: Sử dụng PorterStemmer từ NLTK
- Ví dụ: "running", "runs" --> "run"
- Khác biệt với lemmatization: Stemming là quá trình cơ học, không quan tâm ngữ pháp

#### 5.6 Hàm correct_spellings(text)
- Chức năng: Sửa lỗi chính tả trong văn bản
- Công nghệ: Sử dụng thư viện spellchecker
- Quá trình:
  1. Phát hiện các từ viết sai (misspelled words)
  2. Đề xuất từ chính xác gần nhất
  3. Nếu không tìm được từ chính xác, giữ nguyên từ gốc

#### 5.7 Hàm remove_urls(text)
- Chức năng: Loại bỏ tất cả đường dẫn web
- Mẫu regex: `https?://\S+|www\.\S+`
- Tác dụng: Loại bỏ URL không mang thông tin hữu ích

#### 5.8 Hàm remove_html1(text)
- Chức năng: Loại bỏ các thẻ HTML từ dữ liệu
- Mẫu regex: `<.*?>`
- Tác dụng: Chuẩn bị dữ liệu sạch để xử lý

#### 5.9 Hàm remove_html2(text)
- Chức năng: Loại bỏ HTML sử dụng BeautifulSoup
- Công nghệ: BeautifulSoup với parser "lxml"
- Tác dụng: Cách tiếp cận thay thế nếu remove_html1 không hiệu quả

### Giai Đoạn 6: Hàm Xử Lý Tổng Hợp

**Hàm process(text, is_lower, rev_punctuation, rev_stopwords, stemming, rev_urls, rev_html1, lemmatizing, rev_frequent, spelling_correcting)**

Thực hiện các bước xử lý theo thứ tự:
1. Chuyển thành chữ thường (nếu is_lower=True)
2. Loại bỏ HTML (nếu rev_html1=True)
3. Loại bỏ URL (nếu rev_urls=True)
4. Loại bỏ dấu câu (nếu rev_punctuation=True)
5. Loại bỏ stopwords (nếu rev_stopwords=True)
6. Loại bỏ từ thường xuyên (nếu rev_frequent=True)
7. Lemmatization (nếu lemmatizing=True)
8. Sửa chính tả (nếu spelling_correcting=True)
9. Stemming (nếu stemming=True)

**Cấu hình xử lý sử dụng**:
- Bước 1: is_lower=True, rev_punctuation=True, rev_stopwords=True, lemmatizing=True, rev_frequent=True
- Bước 2: spelling_correcting=True, stemming=True (xử lý song song)
- Bước 3: stemming=True (xử lý lần cuối để đảm bảo kết quả nhất quán)

### Giai Đoạn 7: Xử Lý Song Song (Parallel Processing)

**Sử dụng Joblib.Parallel**
- Chức năng: Xử lý dữ liệu trên nhiều lõi CPU đồng thời
- Cấu hình: n_jobs=-1 (sử dụng tất cả lõi CPU có sẵn)
- Tác dụng: Tăng tốc độ xử lý dữ liệu lớn
- Kích thước batch: 100 dòng mỗi lô (có thể điều chỉnh)

### Giai Đoạn 8: Checkpoint Mechanism

**Cơ chế Checkpoint**
- Mục đích: Lưu tiến độ xử lý để có thể tiếp tục nếu bị gián đoạn
- Tần suất lưu: Mỗi 100 dòng
- File lưu: checkpoint.csv
- Lợi ích: Tiết kiệm thời gian nếu quá trình xử lý bị dừng

## Các Hàm Hỗ Trợ

### Hàm Xử Lý Dữ Liệu
1. **extract_fields(text)**: Trích xuất thông tin từ XML
2. **remove_punctuation(text)**: Loại bỏ dấu câu
3. **remove_stopwords(text)**: Loại bỏ stopwords
4. **remove_freqwords(text)**: Loại bỏ từ thường xuyên
5. **lemmatize_words(text)**: Chuyển đổi lemma
6. **stem_words(text)**: Rút gọn stem
7. **correct_spellings(text)**: Sửa lỗi chính tả
8. **remove_urls(text)**: Loại bỏ URL
9. **remove_html1(text)**: Loại bỏ HTML (regex)
10. **remove_html2(text)**: Loại bỏ HTML (BeautifulSoup)

### Hàm Điều Phối
- **process(text, ...)**: Hàm chính để xử lý văn bản với các tùy chọn linh hoạt

## Kết Quả Xử Lý

### Thống Kê Dữ Liệu
- Dòng gốc: 4,516,057
- Dòng sau trích xuất: 4,538,038
- Dòng sau lọc: 4,300,380 (loại bỏ 237,658 dòng không có tiêu đề)
- Tiêu đề duy nhất: 4,644

### Chất Lượng Dữ Liệu
- Missing values đã được xử lý
- Dữ liệu đã được chuẩn hóa
- Văn bản đã được làm sạch và xử lý NLP

## Ứng Dụng Tiềm Năng

1. **Text Classification**: Phân loại tài liệu khoa học theo lĩnh vực
2. **Topic Modeling**: Tìm ra các chủ đề chính trong dataset
3. **Semantic Analysis**: Phân tích ý nghĩa của các trích dẫn
4. **Citation Network Analysis**: Phân tích mạng lưới trích dẫn
5. **Machine Learning**: Huấn luyện các mô hình học máy cho NLP
