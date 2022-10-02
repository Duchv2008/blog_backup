## Tip cocoa pod.

Some simple tip fix error M1 relate to cocoapod.
You can try it. Maybe it will can help you fix something.

# Summary sh command.
```
sudo arch -x86_64 gem install ffi

arch -x86_64 pod install

sudo gem uninstall cocoapods -v 1.10.0
sudo gem install cocoapods -v 1.10.0

gem list cocoapods

post_install do |installer|
  installer.pods_project.targets.each do |target|
    target.build_configurations.each do |config|
      config.build_settings["EXCLUDED_ARCHS[sdk=iphonesimulator*]"] = "arm64"
    end
  end
end
```

# Can't pod install or pod install error.

You can try run follow step:

Install **ffi** gem if not
```
sudo arch -x86_64 gem install ffi
```

Pod install
```
arch -x86_64 pod install
```

# Fix M1 build in simulator
Copy below code and pasted to end file **Podfile** in your project.
```
post_install do |installer|
  installer.pods_project.targets.each do |target|
    target.build_configurations.each do |config|
      config.build_settings["EXCLUDED_ARCHS[sdk=iphonesimulator*]"] = "arm64"
    end
  end
end
```

If you still can't build, you can try add:
- Add *arm64* in ** EXCLUDED_ARCHS** in your project scheme

Re-build. if still error, you can try:
- Remove **VALID_ARCHS = "arm64 arm64e"** in your project scheme source code (open file *.xcodeproj* by sourcode.)


# Re-install cocoapod
```
sudo gem uninstall cocoapods -v <Cocoapod Version>
sudo gem install cocoapods -v <Cocoapod Version>

Example:
sudo gem uninstall cocoapods -v 1.10.0
sudo gem install cocoapods -v 1.11.0
```

Get cocoa pod release version in [Here](https://github.com/CocoaPods/CocoaPods/releases)

# List cocoa pod installed in your mac
```
gem list cocoapods
```