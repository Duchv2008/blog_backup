## Buổi 10. Realm

# Giới thiệu
[Realm](https://realm.io/realm-swift/) là một NoSQL SDK hỗ trợ mobile. Nó hướng tới việc xây dựng một ứng dụng theo hướng `Offline database first`

[Mobile SDK](https://www.mongodb.com/docs/realm/sdk/?_ga=2.23422522.1662947649.1664682350-1345446057.1661589982&_gac=1.194310751.1663169459.Cj0KCQjw94WZBhDtARIsAKxWG--O0m2GSyHY1IGZRS1l9bSUrRBgb5M8L0MBq1IV94dcPep_9BQmCDYaAs92EALw_wcB)

*Tức là ứng dụng vẫn có thể hoạt động dù cho không có kết nối mạng, dữ liệu sẽ được lưu trực tiếp trên thiết bị, người dùng vẫn có thể tiến hành mọi việc một cách thuận lợi.*

# NoSQL(Not Only SQL) là gì?

- NoSQL suất phát từ yêu cầu cần những database có khả năng lưu trữ dữ liệu với lượng cực lớn, truy vấn dữ liệu với tốc độ cao mà không đòi hỏi quá nhiều về năng lực phần cứng cũng như tài nguyên hệ thống và tăng khả năng chịu lỗi.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1664682598001/ojLBY4wdR.png align="left")

# Tại sao lại sử dụng realm?

- Truy vấn và trả về kết quả nhanh chóng.
- Hướng đối tượng: tổ chức dữ liệu dưới dạng đối tượng thay vì rows, documents hay column.
- Cross-platform.
- Mobile.

**Realm là một lựa chọn tuyệt vời thay thế cho SQLite và CoreData**

# Cài đặt Realm
## Yêu cầu
- Máy cài đặt cocoapods

Thêm thư viện vào trong podfile

```
pod 'RealmSwift', '~>10'
```

Cài đặt thư viện trong terminal

```
pod install
```

*Note: Realm SDK khá nặng nên hãy thêm thư mục `Pods` vào `.gitignore` để không đẩy lên repo github.*

# Định nghĩa model thể hiện cho một collection trong realm database

Ví dụ với collection là `PersonalInfo` với các thuộc tính là id, tên, ngày sinh.

# Import realm thư viện

```
import RealmSwift
```


```
import RealmSwift

class PersonalInfo: Object {
    @Persisted var name: String = ""
    @Persisted var birthdate: String = ""

    convenience init(name: String, birthdate: String) {
        self.init()
        self.name = name
        self.birthdate = birthdate
    }
}
```

Do  dữ liệu trong realm được lưu trữ dưới dạng object nên tất cả các model realm đề được kế thừa từ `Object`

@Persisted là một property wrapper (tìm hiệu thêm [tại đây](https://viblo.asia/p/tim-hieu-ve-property-wrapper-trong-swift-djeZ1P1QKWz))


# Mở Realm Database trước khi xử lý với dữ liệu.

```
let realm = try! Realm()
```

# Tạo mới

Tạo mới một instance todo với name là "Do laundry" và ownerId là user.id. Sau đó lưu vào realm database
```
let todo = Todo(name: "Do laundry", ownerId: user.id)
try! realm.write {
    realm.add(todo)
}
```

# Lấy toàn bộ instance trong TODO class.

```
let todos = realm.objects(Todo.self)
```

# Tìm kiếm theo điều kiện

```
let todosInProgress = todos.where {
    $0.status == "InProgress"
}
print("A list of all todos in progress: \(todosInProgress)")
```
# Cập nhật một instance.

```
let todoToUpdate = todos[0]
try! realm.write {
    todoToUpdate.status = "InProgress"
}
```

# Xóa một instance.

```
// All modifications to a realm must happen in a write block.
let todoToDelete = todos[0]
try! realm.write {
    // Delete the Todo.
    realm.delete(todoToDelete)
}
```

# Thực hành

- Viết app TODO list sử dụng Realm để lưu trữ dữ liệu.
+ Có text field và button cho phép tạo mới và chỉnh sửa
+ Tableview thể hiện list danh sách todo list.
+ Click vào 1 todo list đánh dấu là mark completed.
+ Có 1 ô search để tìm kiếm todo theo tên với text đã nhập.

[Demo video](https://drive.google.com/drive/u/0/folders/1K-LZapivbx-3VrYZHRhnFZkspFHd4xdf)

# Realm Studio
[Download](https://www.mongodb.com/docs/realm-legacy/products/realm-studio.html#download-studio)

Cho phép quản lý và xem database 1 cách trực quan qua phần mềm.