## Chuyển đổi, sắp xếp và tinh gọn lại cấu trúc XML của các tập tin.

### Các công việc cần làm, tuỳ chỉnh theo từng ngôn ngữ

1. Chuẩn hoá tập tin XML, xoá các tag liên quan đến tei-c như teiHeader...
2. Đổi tên thẻ xml có ý nghĩa dựa trên nội dung

_Chú ý: nếu dùng vscode để search và replace thì phải lặp lại nhiều lần, đến khi nào không còn result nữa thì thôi._

**Removing multiple newlines into one**
```xml
\n\n+ => \n
```

**namo**
```xml
# Search by regex
<p rend="centre"([^>]+)?>([\. ]*)(නමො තස්‌ස භගවතො අරහතො සම්‌මාසම්‌බුද්‌ධස්‌ස)([\. ]*)</p>
# Replace
<namo>$3</namo>
```

**Removing `hi` tag has `rend="dot"`**
```xml
<hi rend="dot">.</hi>\s* => [EMPTY]
```

**Removing `hi` tag has `rend="paranum"`**
```xml
<hi rend="paranum">.*?</hi> => [EMPTY]
```

```xml
\srend="bodytext" => [EMPTY]
```

```xml
<p rend="centre">තස්‌සුද්‌දානං => <p>තස්‌සුද්‌දානං
```

```xml
<hi rend="bold">(.*?)</hi> => <b>$1</b>
```

**Replacing `gatha[1-3]` to `gatha`**
```xml
<p rend="gatha[1-3]">(.*?)</p> => <gatha>$1</gatha>
```

**Replacing `gathalast` to `gathalast`**
```xml
<p rend="gathalast">(.*?)</p> => <gathalast>$1</gathalast>
```

**Raplacing `<trailer rend="...">` has `<trailer>`**
```xml
<trailer[^>]+> => <trailer>
```

**Raplacing `<p rend="centre">` to `<ctr>`**
```xml
<p rend="centre">(.*?)</p> => <ctr>$1</ctr>
```
**Remove id from div**
```xml
<div id=".*?"([^>]+)> => <div$1>
```

**Replacing div[type]**
```
#book: book|subbook|pannasaka
#chapter: sutta|vagga|kanda|intro|khandaka|samyutta|peyyala|nipata|vimana
#title
#subhead|subsubhead
```









**Raplacing `<p rend="centre">` with text constant `niṭṭhita|niṭṭhitā|niṭṭhito` to `<end>`**
```xml
#search
#romn
<p rend="centre">(.*niṭṭhit.*)</p>
#sinh
<p rend="centre">(.*නිට්‌ඨිත.*)</p>
#replace
<end>$1</end>
```

**Raplacing `<p rend="centre">` with text constant `samatta|samattā|samatto` to `<end>`**
```xml
#search
#romn
<p rend="centre">(.*samatt.*)</p>
#sinh
<p rend="centre">(.*සමත්‌ත.*)</p>
#replace
<end>$1</end>
```

**Cleaning empty tags**
```xml
#step 1
#search
<p([^>]+)?>\s+</p>
#replace
<p$1></p>

#step 2
#search
<p></p>
#replace empty

#step 3: remove multiple newlines to one
#search
\n\n+
#replace empty
\n
```
**Search and modify empty gatha lines**
```
#step 1
#search
<p rend="gatha1"></p>
<p rend="gatha2"></p>
<p rend="gatha3"></p>
<p rend="gathalast"></p>

```

3. Tinh giản và chuẩn hoá lại các thẻ XML, ví dụ `<p rend="bodytext">text</p> -> <p></p>`
4. Sửa lỗi lệch dòng của các đoạn văn bản, ví dụ:

**Trong tập tin s0520m.nrf.xml, dòng 2454-2456 có đoạn sau:**

```xml
<p rend="centre">ථෙරස්‌ස මහාකච්‌චායනස්‌ස ජම්‌බුවනවාසිනො පෙටකොපදෙසො</p>
<p rend="bodytext">සමත්‌තො.</p>
```

Đoạn text "**සමත්‌තො.**" trong cặp thẻ `<p rend="bodytext">...</p>` là bị lỗi tách dòng, có thể do lỗi đánh máy hoặc sơ sót trong khẩu kiểm duyệt. Chính xác là đoạn này phải nằm trong cặp thẻ `<p rend="centre">...</p>` liền kề bên trên.

**Như thế đoạn trên sẽ được sửa lại như sau:**

```xml
<p rend="centre">ථෙරස්‌ස මහාකච්‌චායනස්‌ස ජම්‌බුවනවාසිනො පෙටකොපදෙසො සමත්‌තො.</p>
```

**Tìm như sau**

```xml
#Search "samatta|samattā|samatto"
#roman
<p([^>]+)?>(samatt.).</p>
#sinh
<p([^>]+)?>(සමත්‌ත.).</p>
```

```xml
#Search "niṭṭhita|niṭṭhitā|niṭṭhito"
#roman
<p([^>]+)?>(niṭṭhit.).</p>
#sinh
<p([^>]+)?>(නිට්‌ඨිත.).</p>
```

**Tìm kiếm loại trừ bằng regex**

```xml
<head rend="(?!chapter|book).*">
```

**Danh sách các tập tin bị lỗi/cố ý làm cho lệch dòng**

romn/vin11t.nrf.xml[20445-20453]
romn/vin12t.nrf.xml[2385-2393,3961-3965,4005-4013,7385-7393,9661-9669,9677-9685,10425-10433,10469-10477,10965-10973,11053-11061]
romn/vin13t.nrf.xml[12393-12397] (chưa chắc, research thêm)

_Các lỗi này sẽ được kiểm tra và sửa đổi liên tục. Để xác nhận các lỗi trên chúng tôi đã so sánh và đối chiếu với các bài kinh trong suttacentral.net cùng vài nguồn khác trên internet_

**Lỗi thiếu hangnum 2 tại file e1208n.nrf.xml**
Dòng 6319-6320 (sau khi remove multiple lines)
Nguồn tham khảo:
https://gretil.sub.uni-goettingen.de/gretil/2_pali/7_poe/jinava_u.htm
(keyword: "Migadāyatapovane")

**Tìm và chỉnh lại các gatha**
1. Tìm 2 gatha1|gatha2|gatha3 liền kề
2. 

<p ([a-z]+)="(?!(gatha1|gatha2|gatha3|gathalast)).*">(.*)</p>\n<p rend="gathalast">(.*)</p>\n<p ([a-z]+)="(?!(gatha1|gatha2|gatha3|gathalast)).*">(.*)</p>
<p ([a-z]+)="(?!(gatha1|gatha2|gatha3|gathalast)).*">(.*)</p>\n
<p rend="gathalast">(.*)</p>\n<p ([a-z]+)="(?!(gatha1|gatha2|gatha3|gathalast)).*">(.*)</p>