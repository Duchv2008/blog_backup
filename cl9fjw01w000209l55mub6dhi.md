# Buổi 14: Google map, MapKit

# Giới thiệu google map.

# Cài đặt thư viện google map sdk vào project.

Điều kiện:

*   Xcode &gt;= 13
    
*   Cocoapods
    

Để sử dụng được google map chúng ta sử dụng thư viện [GoogleMaps](Link) và cài đặt thông qua cocoapods

```plaintext
pod 'GoogleMaps', '7.1.0'
```

# Tạo tài khoản và lấy Google map KEY.

Mỗi app sẽ được google cung cấp cho một đoạn mã token gọi là Google API key.

### Bước 1: Truy cập [Google API Console](https://console.cloud.google.com/).

### Bước 2: Tạo mới hoặc chọn 1 project có sẵn.

### Bước 3: Enable Google Maps SDK cho iOS.

### Bước 4: Mở page Credentials để lấy API key.

### Bước 5: Add API key vào project.

*   Mở file AppDelegate.swift
    

```plaintext
# AppDelegate.swift
import GoogleMaps
```

*   Trong func application(:didFinishLaunchingWithOptions:)
    

```plaintext
GMSServices.provideAPIKey("YOUR_API_KEY")
```

Thay thế YOUR\_API\_KEY bằng KEY đã lấy được ở bước trên.

# Tạo map

Chúng ta có 2 cách để tạo map trong iOS

\*\*Cách 1: \*\* Sử dụng code

```plaintext
import UIKit
import GoogleMaps

class ViewController: UIViewController {
  override func loadView() {
    // Create a GMSCameraPosition that tells the map to display the
    // coordinate -33.86,151.20 at zoom level 6.
    let camera = GMSCameraPosition.camera(withLatitude: -33.86, longitude: 151.20, zoom: 6.0)
    let mapView = GMSMapView.map(withFrame: CGRect.zero, camera: camera)
    view = mapView
  }
}
```

\*\*Cách 2: \*\* Kéo thả trong storyboard

Sử dụng google map:

```plaintext
import UIKit
import GoogleMaps

class ViewController: UIViewController {
    @IBOutlet weak var mapView: GMSMapView!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view, typically from a nib.
        let camera = GMSCameraPosition.camera(withLatitude: -33.86, longitude: 151.20, zoom: 6.0)
        self.mapView.camera = camera
    }
    
    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
}
```

# Google map camera?

## Latitude

Là vĩ độ trong đại lý

## Longitude

Là kinh độ trong đại lý

Để có thể view được map chúng ta sẽ thông qua một thứ gọi là google map camera. Camera sẽ hiển thị map dựa vào kinh độ và vĩ độ và độ zoom và có thể di chuyển trên map sang các vị trí khác nhau.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1662999348917/fpZ7pcf_e.png align="left")

Tạo camera

```plaintext
let camera = GMSCameraPosition(
  latitude: -33.8683,
  longitude: 151.2086,
  zoom: 16
)
```

Di chuyển vị trí camera

```plaintext
let fancy = GMSCameraPosition(
  latitude: -33,
  longitude: 151,
  zoom: 6,
  bearing: 270,
  viewingAngle: 45
)
mapView.camera = fancy
```

# Location là gì?

Location là vị trí được thể hiện ở trên map. Current location là vị trí hiện tại thiết bị điện thoại sử dụng map đang đứng.

Google SDK cung cấp sẵn cho chúng ta 1 button current location. Để sử dụng chúng ta cần bật nó lên.

Trước tiên cần xin cấp quyền Trong Info.plist

```plaintext
NSLocationAlwaysUsageDescription
```

```plaintext
mapView.settings.myLocationButton = true
mapView.isMyLocationEnabled = true
```

Ta có thể custom việc thực hiện bấm nút current location này thông qua delegate GMSMapViewDelegate

```plaintext
extention ViewController: GMSMapViewDelegate {
....
func didTapMyLocationButton(for mapView: GMSMapView) -> Bool {
        **/// Thực hiện các custom action khi bấm vào button.
        /// Ví dụ: Gửi tọa độ lên server**
        return false
    }
...
}
```

*Chú ý ở hàm didTapMyLocationButton return false để thực hiện chức năng mặc định của current location là show bản đồ vào vị trí hiện tại, nếu return true sẽ ko thực hiện chức năng mặc định này.*

# Marker là gì?

Google map marker là một thành phần không thể thiếu và rất hữu ích trong Google Maps. Map maker là một địa chỉ gắn lên một địa điểm cụ thể nào đó trên bản đồ , có thể sử dụng hình ảnh , biểu tượng hoặc hình ảnh dạng bitmap làm biểu tượng đánh dấu trên Google Maps .

Tạo marker

```plaintext
let marker = GMSMarker()
marker.position = CLLocationCoordinate2D(latitude: -33.86, longitude: 151.20)
marker.title = "Techmaster"
marker.snippet = "Học là có việc"
marker.map = self.mapView
```

Marker với ảnh

```plaintext
let positionLondon = CLLocationCoordinate2D(latitude: 51.5, longitude: -0.127)
let london = GMSMarker(position: positionLondon)
london.title = "Techmaster"
london.icon = UIImage(named: "techmaster_logo")
london.map = mapView
```

# Một số action hay sử dụng với map

## Tap (Click) vào map

```plaintext
func mapView(_ mapView: GMSMapView, didTapAt coordinate: CLLocationCoordinate2D) {
      // Sử lý các action khi click vào map
        self.mapView.clear()
        self.addMarker(coordinate) 
}
```

```plaintext
extension ViewController: CLLocationManagerDelegate {
    //Handle incoming location events.
    func locationManager(_ manager: CLLocationManager,
                         didUpdateLocations locations: [CLLocation]) {
        print(#function)
        
        manager.stopUpdatingLocation()
        if let location: CLLocation = locations.last {
            let camera = GMSCameraPosition.camera(withTarget: location.coordinate, zoom: 16)
            if googleMapView.isHidden {
                googleMapView.isHidden = false
                googleMapView.camera = camera
            } else {
                googleMapView.animate(to: camera)
            }
        }
    }
    
    // Handle authorization for the location manager.
    func locationManager(_ manager: CLLocationManager,
                         didChangeAuthorization status: CLAuthorizationStatus) {
        print(#function)
        
        if status == .authorizedWhenInUse {
            manager.startUpdatingLocation()
        }
    }
    
    // Handle location manager errors.
    func locationManager(_ manager: CLLocationManager,
                         didFailWithError error: Error) {
        print(#function)
        
        locationManager.stopUpdatingLocation()
        print("Error: \(error)")
    }
}
```

# Demo code với google map.

# Tài liệu tham khảo

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1662999919401/8Tr4g2oFN.png align="left")

https://developers.google.com/maps/documentation/ios-sdk/config