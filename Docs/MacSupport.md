# macOS 支持 / Mac support

MikuFlick64 1.1.6 build 13 is a Mac Catalyst application for Apple silicon, requiring macOS 14.0 or later. It is not an iOS IPA renamed as a Mac app. Intel Macs are not included.

MikuFlick64 1.1.6 build 13 使用 Mac Catalyst，要求 Apple Silicon 和 macOS 14.0+。提供独立的 Mac 应用与 DMG，不提供 Intel 包。

## 安装 / Install

Open the DMG and drag MikuFlick64.app to Applications. The bundle has an ad hoc signature, not Developer ID signing or Apple notarization. After checking the source and SHA256, the user may choose to approve the first launch in System Settings → Privacy & Security. Do not disable Gatekeeper or remove quarantine to bypass system checks. A downloaded, quarantined installation on another Mac has not been tested.

打开 DMG，将应用拖入 Applications。本包使用 ad hoc 签名，尚未 Developer ID 签名或公证；核对来源及 SHA256 后，由用户决定是否在“系统设置 → 隐私与安全性”批准首次运行。未在另一台 Mac 上验证下载后带隔离标记的安装流程。

## 两种画布 / Two canvases

The window's top buttons switch iPad Landscape (1024 × 768) / iPad Portrait (768 × 1024). The selected mode is remembered. The canvas scales uniformly into the available window area, with black margins where necessary. Buttons remain present in gameplay, pause, results and MV full-screen presentations. Switching the canvas does not recreate the session or seek the media.

顶部按钮切换 iPad 横屏 / 竖屏；画布按 4:3 / 3:4 等比缩放，必要时留黑边。记住选择，游戏、暂停、结算与 MV 内也保留按钮，切换不会重开歌曲或改变播放位置。iPhone / iPad 原来的布局和方向规则不受此包装影响。

The Mac app is sandboxed. It enables outgoing network connections and user-selected file read/write access. Imported media and downloads use app storage; original DLC media is not bundled. USB/iOS app data and old bundle identifiers are not automatically migrated.

## 构建 / Build

From the extracted source project, use Xcode with Mac Catalyst support. No personal development team is saved in the project. Rebuild the LGPL-only FFmpeg library if necessary:

```sh
DEVELOPER_DIR=/Applications/Xcode.app/Contents/Developer zsh Tools/build-mac-codecs.sh
xcodebuild -project MikuFlick64.xcodeproj -scheme MikuFlick64 -configuration Release \
  -destination 'platform=macOS,variant=Mac Catalyst,arch=arm64' \
  -derivedDataPath /tmp/mikuflick-mac CODE_SIGNING_ALLOWED=NO CODE_SIGNING_REQUIRED=NO build
```

For a public signed/notarized distribution, use your own Developer ID credentials and notarization process. For local ad hoc packaging, copy the product to a /tmp staging directory, add ThirdPartyNotices (NOTICE.md, both LGPL license texts, and SOURCE.txt linking the matching full source), then sign and package:

```sh
codesign --force --sign - --timestamp=none --entitlements Mac.entitlements /tmp/mikuflick-stage/MikuFlick64.app
python3 Tools/package_mac.py --app /tmp/mikuflick-stage/MikuFlick64.app \
  --output /path/to/MikuFlick64-macos-arm64.dmg
```

See Vendor/NOTICE.md and the exact FFmpeg source tarball in Vendor/Sources for relinking and rebuild information. The DMG includes third-party notices; the matching full editable app source and Mac codec build script are supplied in MikuFlick64-Source.zip.

## 验证范围 / Validation scope

Xcode 27.2 beta 2 produced native arm64 iPhoneOS and Mac Catalyst Release builds. Mac tests ran on macOS 27.2 / Apple silicon. Both canvas modes were checked on home, options, selection, gameplay and pause. Switching during active playback kept progress; switching during pause kept the same score/time. NORMAL 《恋は戦争》 autoplay completed: 83 COOL, 83 MAX COMBO, 15 interludes, score 42554 (24954 + 17600), Perfect. Autoplay result was not saved; HIGH SCORE remained 0. Final ad hoc sandbox bundle launched, and the DMG-contained app launched after a restart with the selected canvas retained.

MacDisplayTests checks 72 window dimensions, aspect ratios, bounds, invalid windows, saved-state fallback and six-language labels. RulesTests and InterfaceTests passed. DMG packaging checks arm64 / platform 6, version, all 1044 resource hashes, 11 built-in movies, 355 subtitle tracks, app-sandbox/file/network entitlements, signature and disk-image integrity.

This is not complete Mac pack-install/download, every-song manual gameplay, Bluetooth/USB controller, sustained 120 Hz, hardware audio latency or every macOS version certification. Minimum deployment version 14.0 is a build setting, not a record of running on macOS 14. No Intel build was produced. iOS build 13 was compiled and packaged, but was not newly installed/tested on physical iOS devices this round.
