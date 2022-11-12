# Simple project structure

Tất cả các project source code cũng nên được tổ chức thư mục 1 cách rõ ràng để có thể dễ maintain và source code trông đẹp và chuyên nghiệp hơn.


![Screen Shot 2022-11-12 at 16.22.56.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1668245044345/aj8o7AwVm.png align="left")

Mình sẽ hướng dẫn 1 cấu trúc thư mục đơn giản cho các mô hình cơ bản như MVC,  MVVM

*Lưu ý: Nhiều project có thể có ít hơn hoặc nhiều hơn. Tất cả đều phụ thuộc vào mô hình và cấu trúc source code của dự án đang tham gia. Đây không phải quy chuẩn để tuân theo*

# Cấu trúc thư mục:
## 1. Services
Đây sẽ là nơi bạn đặt các class mà bạn coi là service như firebase, realm, hoặc sqlite.
Nó có thể là giao tiếp với thư viện bên thứ 3. Hoặc 1 số service như Google Login, Twitter Login, Biometric login ....

## 2. Models
Đây sẽ là nơi bạn nên đặt các class để xử lý dữ liệu models trong ứng dụng.
Thường class ở đây có thể trùng với thư mục Parameter và Responses trong thư mục Network. Tuy nhiên bạn tách được ra thì tốt.

## 3. Extensions
Là những class để viết ra các extension cho các class sẵn của apple như String, Int, Double, Class... để code tiện lợi hơn.

## 4. CustomViews
Nơi bạn đặt các custom view để tái sử dụng.

## 5. Screens
Các màn hình trong ứng dụng của bạn

### 5.1 Login
Đây có thể là 1 màn hình hoặc nhiều màn hình con của 1 chức năng. Các màn hình con cũng có thể nhóm vào 1 thư mục

### 5.2 Cells
Đối với tableview hoặc collectionview bạn có thể vứt các cells custom vào 1 folder cho dễ nhìn

## 6. Utils hoặc  Utilities
Nơi bạn để các class xử lý vụn vặn hoặc bạn không biết nhét nó vô thư mục nào =))
Nó được coi là thư mục tiện ích như Logger.

## 7. Networks
Nơi bạn thực hiện call API, hoặc call sang 1 dịch vụ online nào đó.
Thường tách ra để call API.

### 7.1 Parameters
Nơi bạn có thể đặt các class phục vụ cho việc truyền data lên server thông qua API

### 7.2 Responses
Nơi bạn có thể đặt các class phục vụ cho việc nhận data từ server thông qua API

## 8. Core
Nơi các bạn có thể đặt các class base cho toàn project như BaseCustomView, BaseController .... 