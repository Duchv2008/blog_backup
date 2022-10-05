## Buổi 11.3: FireStore

# Giới thiệu
Cloud Firestore là dịch vụ điện toán đám mây, NoSQL database lưu trữ data dạng documents, linh hoạt và dễ mở rộng.

Cũng giống như Firebase realtime database Cloud Firestore giúp cho việc đồng bộ dữ liệu giữa các ứng dụng phía client một các nhanh chóng (Realtime) và hộ trợ lưu offline data trong ứng dụng của bạn.

# Collection
Firebase bao gồm các Collection (có thể coi như các table trong SQL database). Các collection lưu trữ các documents (gọi là các field data cần lưu trữ).

## Một số tổ chức data phổ biến của firestore. [Data structure](https://firebase.google.com/docs/firestore/manage-data/structure-data)

### **Nested data trong documents**

![Screen Shot 2022-09-30 at 20.56.40.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1664546210389/isa1NAz8R.png align="left")

Có thể lồng các đối tượng phức tạp như mảng hoặc map trong document

**Ưu điểm:** Nếu có danh sách dữ liệu cố định, đơn giản mà muốn giữ trong document, thì điều này rất dễ dàng để thiết lập và sắp xếp hợp lý cấu trúc dữ liệu.

**Hạn chế**: Tùy chọn này không thể mở rộng như các tùy chọn khác, đặc biệt nếu dữ liệu mở rộng theo thời gian. 
Với danh sách lớn hơn hoặc ngày càng tăng, document cũng phát triển, điều này có thể dẫn đến thời gian truy xuất document chậm hơn.

**Trường hợp sử dụng khả thi là gì?**
Ví dụ: trong ứng dụng chat, có thể lưu trữ 3 phòng trò chuyện (room) được truy cập gần đây nhất của một người dùng dưới dạng danh sách lồng nhau trong hồ sơ của họ.

###  **Sub-collections**

![Screen Shot 2022-09-30 at 20.55.33.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1664546138817/CqVKZv_xH.png align="left")

Có thể tạo collection bên trong document khi có dữ liệu có thể mở rộng theo thời gian.

**Ưu điểm**: Khi danh sách dữ liệu phát triển, kích thước của parent document không thay đổi. Bạn cũng có đầy đủ các khả năng truy vấn trên các subCollection và có thể đưa ra các truy vấn group collection trên các sub-collection.

**Hạn chế**: Bạn không thể dễ dàng xóa các subCollections.

**Trường hợp sử dụng khả thi là gì?** 
Ví dụ: trong cùng một ứng dụng trò chuyện, bạn có thể tạo collection user hoặc messages trong các documents room.

###  **Root-level collections**

![Screen Shot 2022-09-30 at 20.56.12.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1664546178098/Iqz-m_rpP.png align="left")

Tạo collections ở cấp root-level để tổ chức các tập dữ liệu khác nhau.

**Ưu điểm**: Các Root-level collections phù hợp với các mối quan hệ nhiều-nhiều và cung cấp khả năng truy vấn mạnh mẽ trong mỗi bộ sưu tập.

**Hạn chế**: Việc lấy dữ liệu có thể ngày càng trở nên phức tạp khi cơ sở dữ liệu của bạn phát triển.

**Trường hợp sử dụng khả thi là gì?** 
Ví dụ: trong cùng một ứng dụng trò chuyện, bạn có thể tạo một collection user và collection khác cho room và message

# [Data của firestore hỗ trợ các kiểu dữ liệu sau](https://firebase.google.com/docs/firestore/manage-data/data-types):
1. Array
2. Boolean
3. Bytes
4. Date and time
5. Floating-point number
6. Geographical point
7. Integer
8. Map
9. Null
10. Reference
11. Text string

```
let docData: [String: Any] = [
    "stringExample": "Hello world!",
    "booleanExample": true,
    "numberExample": 3.14159265,
    "dateExample": Timestamp(date: Date()),
    "arrayExample": [5, true, "hello"],
    "nullExample": NSNull(),
    "objectExample": [
        "a": 5,
        "b": [
            "nested": "foo"
        ]
    ]
]
db.collection("data").document("one").setData(docData) { err in
    if let err = err {
        print("Error writing document: \(err)")
    } else {
        print("Document successfully written!")
    }
}
```

# Hướng dẫn sử dụng
[Firebase docx](https://firebase.google.com/docs/firestore)

# Add thư viện
[Tham khảo thư viện](https://firebase.google.com/docs/ios/setup)

```
pod 'FirebaseFirestore'
```

# Khởi tạo FireStore

```
import FirebaseFirestore

# Gọi firestore
let db = Firestore.firestore()
```

# Lắng nghe thay đổi của data

- Lắng nghe thay đổi của một document.

```
db.collection("cities").document("SF")
    .addSnapshotListener { documentSnapshot, error in
      guard let document = documentSnapshot else {
        print("Error fetching document: \(error!)")
        return
      }
      guard let data = document.data() else {
        print("Document data was empty.")
        return
      }
      print("Current data: \(data)")
    }

```

- Lắng nghe thay đổi của nhiều document theo điều kiện query

```
db.collection("cities")
.whereField("state", isEqualTo: "CA")
    .addSnapshotListener { querySnapshot, error in
        guard let documents = querySnapshot?.documents else {
            print("Error fetching documents: \(error!)")
            return
        }
        let cities = documents.map { $0["name"]! }
        print("Current cities in CA: \(cities)")
    }
```

- Lắng nghe thay đổi của cả collection:

```
db.collection("cities")
    .addSnapshotListener { querySnapshot, error in
        guard let documents = querySnapshot?.documents else {
            print("Error fetching documents: \(error!)")
            return
        }
        let cities = documents.map { $0["name"]! }
        print("Current cities in CA: \(cities)")
    }
```

- Lắng nghe theo trạng thái của collection.

```
private func listenMessages() {
    listener = ref.addSnapshotListener { querySnapshot, error in
        guard error == nil else {
            print("Error: \(error)")
            return
        }
        
        guard let snapshot = querySnapshot else {
            print("Error fetching snapshots: \(error!)")
            return
        }
        snapshot.documentChanges.forEach { diff in
            let data = diff.document.data()
            
            switch diff.type {
            case .added:
                let id = diff.document.documentID
                let body = data["body"] as? String ?? ""
                let createdBy = data["createdBy"] as? String ?? ""
                let message = Message(id: id, body: body, createdBy: createdBy)
                self.addNewMessageToTableView(message: message)
            case .modified:
                let id = diff.document.documentID
                let body = data["body"] as? String ?? ""
                let createdBy = data["createdBy"] as? String ?? ""
                let message = Message(id: id, body: body, createdBy: createdBy)
                self.updateMessageToTableView(newMessage: message)
            case .removed:
                let id = diff.document.documentID
                self.removeMessageToTableView(id: id)
            default:
                break
            }
        }
    }
}
```

- Hủy bỏ lắng nghe.

```
let listener = db.collection("cities").addSnapshotListener { querySnapshot, error in
    // ...
}

// Stop listening to changes
listener.remove()

```

# Thêm mới data

```
# Trong một class ViewController nào đó

var ref: DocumentReference? = nil
ref = db.collection("users").addDocument(data: [
    "first": "Ada",
    "last": "Lovelace",
    "born": 1815
]) { err in
    if let err = err {
        print("Error adding document: \(err)")
    } else {
        print("Document added with ID: \(ref!.documentID)")
    }
}
```
- db.collection("users"): Gọi đến collection trên là "users"
- addDocument: là hàm để thêm mới document vào trong collection users.
- data: là dạng dictionary (key-value)

# Đọc toàn bộ dữ liệu của một collection
```
db.collection("users").getDocuments() { (querySnapshot, err) in
    if let err = err {
        print("Error getting documents: \(err)")
    } else {
        for document in querySnapshot!.documents {
            print("\(document.documentID) => \(document.data())")
        }
    }
}
```
Để lấy ra documents trong một colllection sẽ được thực hiện thông qua hàm **getDocuments()**. Hàm sẽ trả về một instace của Firebase Query Snapshot.

Từ Firebase Query Snapshot có thể đọc được data thông qua **querySnapshot!.documents**





