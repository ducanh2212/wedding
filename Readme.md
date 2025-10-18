# Thiệp cưới Văn Tùng & Thu Hương

## Kết nối sổ lưu bút với Google Sheet

Biểu mẫu "Gửi lời chúc" trên trang thiệp cưới sẽ gửi dữ liệu (Title, Content, Time, Image) tới một Google Sheet thông qua Google Apps Script. Thực hiện các bước dưới đây để cấu hình:

1. **Mở Google Sheet** mà bạn muốn lưu trữ lời chúc (ví dụ: [`1id-bz_Y82MjYEthEUCg2Z0NEGq5wVsbIpKouHesrbe4`](https://docs.google.com/spreadsheets/d/1id-bz_Y82MjYEthEUCg2Z0NEGq5wVsbIpKouHesrbe4/edit?gid=0#gid=0)).
2. Vào menu **Extensions → Apps Script** và thay thế nội dung mặc định bằng đoạn mã sau:

   ```javascript
   const SHEET_ID = '1id-bz_Y82MjYEthEUCg2Z0NEGq5wVsbIpKouHesrbe4';
   const SHEET_NAME = 'Sheet1';

   function doPost(e) {
     try {
       const payload = JSON.parse(e.postData.contents);
       const sheet = SpreadsheetApp.openById(SHEET_ID).getSheetByName(SHEET_NAME);
       sheet.appendRow([
         payload.Title || '',
         payload.Content || '',
         payload.Time || '',
         payload.Image || '',
         new Date(),
       ]);

       return ContentService.createTextOutput(
         JSON.stringify({ result: 'success', message: 'Lời chúc đã được ghi lại.' })
       ).setMimeType(ContentService.MimeType.JSON);
     } catch (error) {
       return ContentService.createTextOutput(
         JSON.stringify({ result: 'error', message: error.message })
       )
         .setMimeType(ContentService.MimeType.JSON)
         .setStatusCode(500);
     }
   }
   ```

3. Nhấn **Deploy → New deployment**, chọn loại **Web app**, thiết lập:
   - *Execute as*: **Me (tài khoản của bạn)**
   - *Who has access*: **Anyone**

   Sau khi triển khai, sao chép URL Web app.
4. Trong file `index.html`, thay giá trị `data-sheet-endpoint` ở phần sổ lưu bút bằng URL vừa sao chép.

Kể từ đó, mỗi lời chúc gửi từ website sẽ tự động xuất hiện trong Google Sheet với bốn cột `Title`, `Content`, `Time`, `Image` và thêm một cột thời gian ghi nhận.