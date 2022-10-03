## Buổi 11.2: Firebase Realtime Database

# Giới thiệu
Firebase Realtime Database là cơ sở dữ liệu lưu trữ trên cloud. Cũng là một dịch vụ của google.
Dữ liệu được lưu dạng Json và được đồng bộ hóa thời gian thực (realtime) với những client kết nối đến.
Realtime database cũng hộ trợ lưu offline data trong ứng dụng của bạn.

# Database là gì?

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1664545686817/OwhmY3yUJ.png align="left")

Database là cơ sở dữ liệu, là một bộ sưu tập dữ liệu được tổ chức bày bản và thường được truy cập từ hệ thống máy tính hoặc tồn tại dưới dạng tập tin trong hệ quản trị cơ sở dữ liệu.

# Add thư viện
[Tham khảo thư viện](https://firebase.google.com/docs/ios/setup)
```
pod 'FirebaseDatabase'
```

# Enable dịch vụ
- Vào Firebase console
- Tìm đến Realtime Database
- Nhấn vào tạo Database
![Screen Shot 2022-09-28 at 23.20.43.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1664382046656/AiqygxQHZ.png align="left")

# Khởi tạo Realtime database trong code
```
var ref: DatabaseReference!

ref = Database.database().reference()
```

# Cách tổ chức dữ liệu [Structure data](https://firebase.google.com/docs/database/ios/structure-data)
## Nested data.
Database cho phép bạn lồng ghép đến 32 level. Điều này rất dễ làm chúng ta xa đà vào việc lồng ghép trong Json tree.

**Nhược điểm:**

- Khi bạn đọc data từ 1 cấp thì phải đọc tất cả các cấp con của nó.
- Khi bạn share quyền dữ liệu ở một level thì người đó có thể xem được toàn bộ nội dung của node con => Không đảm bảo về mặt bảo mật.
- Việc nested data cũng xảy ra vấn đề về việc trùng lặp dữ liệu.

Chính vì vậy hãy cố gắng tổ chức dữ liệu của bạn "phẳng" nhất có thể.

```
{
  "chats": {
    "one": {
      "title": "Historical Tech Pioneers",
      "messages": {
        "m1": { "sender": "ghopper", "message": "Relay malfunction found. Cause: moth." },
        "m2": { ... },
        // a very long list of messages
      }
    },
    "two": { ... }
  }
}
```

## Flatten data structures (Cấu trúc phẳng)
Dữ liệu được tách biệt hóa giúp cho việc query data hiệu quả hơn. Khắc phục được một số nhược điểm của cấu trúc dữ liệu dạng nested data.

```
{
// Chats chỉ bao gồm các thông tin của một cuộc hội thoại.
// Được lưu trữ dưới dạng các ID duy nhất
  "chats": {
    "one": {
      "title": "Historical Tech Pioneers",
      "lastMessage": "ghopper: Relay malfunction found. Cause: moth.",
      "timestamp": 1459361875666
    },
    "two": { ... },
    "three": { ... }
  },

/// Các thành viên của cuộc hội thoại dễ dàng được truy cập và được lưu trữ bởi ID phòng chat
  "members": {
    // we'll talk about indices like this below
    "one": {
      "ghopper": true,
      "alovelace": true,
      "eclarke": true
    },
    "two": { ... },
    "three": { ... }
  },

  // Các tin nhắn được tách biệt chúng ta có thể dễ dàng phân trang, query, tổ chức dữ liệu thông qua chat ID
  "messages": {
    "one": {
      "m1": {
        "name": "eclarke",
        "message": "The relay seems to be malfunctioning.",
        "timestamp": 1459361875337
      },
      "m2": { ... },
      "m3": { ... }
    },
    "two": { ... },
    "three": { ... }
  }
}
```

# Đọc và ghi dữ liệu với Firebase realtime database.
## Lấy một FIRDatabaseReference.

```
var ref: DatabaseReference!

ref = Database.database("DATABASE URL").reference("KEY")

# Example
ref = Database.database(url: "https://techmasterdemo-ad510-default-rtdb...").reference(withPath: "/messages")
```

## Ghi dữ liệu
```
self.ref.child("users").child(user.uid).setValue(["username": username])

hoặc 

self.ref.child("users/\(user.uid)/username").setValue(username)
```

Hoặc ghi dữ liệu với auto id
```
private func createMessage() {
    let body: [String: Any] = [
        "body": messageTF.text ?? "",
        "createdBy": Auth.auth().currentUser?.uid ?? ""
    ]

// Cách 1: ref.childByAutoId().setValue(body)

// Cách 2:
    ref.childByAutoId().updateChildValues(body) { error, ref in
        print(error)
    }
}
```

## Đọc dữ liệu bằng cách lắng nghe sự kiện.

Mỗi khi data có sự thay đổi như thêm, sửa, xóa thì event sẽ nhận kết quả cho client.

```
// Trả về toàn bộ data của bảng ref mỗi lần thay đổi giá trị
refHandle = postRef.observe(DataEventType.value, with: { snapshot in
  // ...
})

/// Khi có thêm mới data
ref.observe(.childAdded) { snapshot in
     print(snapshot)
 }

/// Khi xóa data
ref.observe(.childRemoved) { snapshot in
    print(snapshot)
}
 
/// Khi update 1 data
ref.observe(.childChanged) { snapshot in
    print(snapshot)
}
```

## Đọc dữ liệu 1 lần.

**Sử dụng getData()**

```
ref.child("users/\(uid)/username").getData(completion:  { error, snapshot in
  guard error == nil else {
    print(error!.localizedDescription)
    return;
  }
  let userName = snapshot.value as? String ?? "Unknown";
});
```

**Sử dụng observer**

```
let userID = Auth.auth().currentUser?.uid
ref.child("users").child(userID!).observeSingleEvent(of: .value, with: { snapshot in
  // Get user value
  let value = snapshot.value as? NSDictionary
  let username = value?["username"] as? String ?? ""
  let user = User(username: username)
  // ...
}) { error in
  print(error.localizedDescription)
}
```

## Chỉnh sửa dữ liệu
```
ref.child("users").child(user.uid).setValue(["username": username]) {
  (error:Error?, ref:DatabaseReference) in
  if let error = error {
    print("Data could not be saved: \(error).")
  } else {
    print("Data saved successfully!")
  }
}
```

## Xóa dữ liệu

Có thể sử dụng `removeValue` để xóa dữ liệu đến một tham chiếu cụ thể.
Có thể gán dữ liệu bằng `nil` với `setValue` hoặc `updateChildValues` để xóa 1 hoặc có thể là nhiều data cùng lúc.

```
private func deleteMessage(id: String, index: Int) {
    ref.child(id).removeValue()
}
```

