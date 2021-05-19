# Hướng dẫn apply template thumbnail cho FE

## Giới thiệu
- Hiện tại BE đã code chụp HTML thành thumbnail và lưu vào DB
- Chụp thumbnail mỗi khi client tạo mới, hoặc cập nhật template
- Link thumbnail đã trả về FE

## Thao tác
- BE đang code trên branch `feature/MS-5623-create-thumnail-popup`, nhớ checkout qua nhánh này để sử dụng API
- Sau khi checkout, chạy lại migrate để tạo thêm column 
```
php artisan migrate
```
- Cần phải cấu hình ngrok mới tạo dc thumbnail, ko sẽ bị lỗi, vì docker ko request localhost được, ví dụ tạo subdomain là `phuvn`
```
ngrok.exe http --subdomain=phuvn 8989
```
- Cần phải chạy lại docker, vì có cài thêm package lên php container
```
docker-compose up --build
```

## API trả về
Link thumbnail trả về như sau

**Popup**
- Route `POST /v1/api/LG_BN_LIST`
- Thumbnail path `response.data.data[x].versions[x].thumbnail`

**Email**
- Rotue `POST /v1/api/UE_EM_LIST`
- Thumbnail path `response.data.data[x].versions[x].thumbnail`


**Lưu ý**
- Flow sẽ như sau:
```txt
Submit (Edit or Create) 
        |
        ▼
BE reset thumbnail về null 
        |
        ▼
Tạo job chụp thumbnail 
        |
        ▼
Trả danh danh sách versions về FE
        |
        ▼
Thực thi Job, tạo ra thumbnail lưu vào DB
```
- Hiện tại, BE chụp thumbnail sẽ chạy trong Job, nên việc chụp thumbnail sẽ delay vài giây
- Vì thế sau khi submit, back về trang danh sách, những thumbnail trả về sẽ bị null, cái này nhờ FE phối hợp để xử lý sao cho hợp lý, (có thể là cho 1 cái hình loading, rồi vài giây sau request lên lại để lấy thumbnail, rồi hiện ra)
- 1 vấn đề nữa là hiện tại html FE gửi lên server bị thiếu mất những cái form, input,..., nên khi chụp sẽ bị thiếu, nhờ FE xử lý thêm, truyền full HTML lên để BE chụp cho chính xác

Template phía FE
![](https://raw.githubusercontent.com/gitvophu/public-image/main/Screenshot%202021-05-19%20152257.png)

Template thumbnail sau khi chụp
![](https://raw.githubusercontent.com/gitvophu/public-image/main/Screenshot%202021-05-19%20152443.png)


