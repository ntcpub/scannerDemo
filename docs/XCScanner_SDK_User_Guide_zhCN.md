**扫码SDK接入文档**
-------------------

# 修改记录


| **版本号** | **日期**   | **内容**                                               |
| ---------- | ---------- | ------------------------------------------------------ |
| 1.0.0      | 2023/02/03 | 实现基本的扫码结果回调以及参数设置                     |
| 1.0.3      | 2023/02/12 | 增加更多的扫码控制及配置接口。                         |
| 1.0.4      | 2023/02/27 | 增加扫码服务的暂停和继续                               |
| 1.0.6      | 2023/03/09 | 支持版本信息、连续扫码、多条码支持及精确扫码相关接口。 |
| 1.0.7      | 2023/03/10 | 支持瞄准灯和补光灯控制接口。                           |

# 配置Maven仓库

> 配置Maven仓库

```
    maven {
        allowInsecureProtocol = true
        url "http://47.108.228.164:8081/nexus/service/local/repositories/releases/content/"
    }
```

**注意**：maven仓库的添加一般在在工程的build.gradle里面，也有的可能在settings.gradle里面

# 配置依赖

> 配置依赖的SDK，注意使用最新版本

```
    implementation('com.xcheng:scanner:1.0.6')
```

# 功能使用

## SDK初始化

最简单的情况下SDK初始化后，就可以使用扫码服务。

```java
    XcBarcodeScanner.init(Context context, ScannerResult scannerResult)
```

> 回调类

```java
    public interface ScannerResult {
        void onResult(String result);
    }
```

扫码SDK初始化后，就和系统的扫码服务建立了连接，此时扫码结果会通过ScannerResult回调通知回来。

示例代码：

```java
                    XcBarcodeScanner.init(this, new ScannerResult() {
                        @Override
                        public void onResult(String result) {
                            runOnUiThread(new Runnable() {
                                @Override
                                public void run() {
                                    Log.d(TAG, "result: " + result);
                                    TextView resultTextView = findViewById(R.id.textview_result);
                                    resultTextView.setText(result);
                                }
                            });
                        }
                    });
```

## SDK反初始化

在Activity消亡或切换到后台时，需要进行反初始化动作，避免再次初始化后回调异常。

示例代码：

```java
        @Override
        protected void onPause() {
            super.onPause();
  
            XcBarcodeScanner.deInit(this);
        }
```

## 开始/结束扫码动作

可以通过SDK提供的接口控制扫码服务触发扫码动作的开始和结束。

```java
    XcBarcodeScanner.startScan();
    XcBarcodeScanner.stopScan();
```

## 暂停/继续扫码服务

可以通过SDK提供的接口控制扫码服务的暂停和继续。

```java
    XcBarcodeScanner.suspendScanService();
    XcBarcodeScanner.resumeScanService();
```

## 获取扫码服务版本号

可以通过SDK提供的接口获取扫码服务版本信息。

```java
    String serviceVer = XcBarcodeScanner.getServiceVersion();
    Log.d(TAG, "Service ver: " + serviceVer);
```

## 获取SDK版本号

可以通过SDK提供的接口获取SDK自己的版本信息。

```java
    String sdkVer = XcBarcodeScanner.getSdkVersion();
    Log.d(TAG, "SDK ver: " + sdkVer);
```

# 进阶设置

## 开关条码类型支持

可以通过SDK提供的接口控制扫码服务开启或关闭制定的条码类型。

```java
    XcBarcodeScanner.enableBarcodeType(String barcodeType, boolean enabled);
```

所有扫码类型都定义在BarcodeType类中

```java
    public class BarcodeType {
        public static final String AZTEC = "Aztec";
        public static final String CODE11 = "Code11";
        public static final String CODE39 = "Code39";
        public static final String CODE93 = "Code93";
        public static final String CODE128 = "Code128";
        public static final String CODABAR = "Codabar";
        public static final String DOTCODE = "Dotcode";
        public static final String CODABLOCKF = "CODABLOCK F";
        public static final String DATAMATRIX = "DATAMATRIX";
        public static final String GS1_DATAMATRIX = "GS1DATAMATRIX";
        public static final String EAN8 = "EAN-8";
        public static final String EAN13 = "EAN-13";
        public static final String GS1_DATABAR = "GS1_DATABAR";
        public static final String HANXIN = "HANXIN";
        public static final String HK25 = "HK25";
        public static final String ITF25 = "ITF25";
        public static final String MATRIX25 = "MATRIX25";
        public static final String MAXICODE = "MAXICODE";
        public static final String MSI = "MSI";
        public static final String MICROPDF = "MICROPDF";
        public static final String PDF417 = "PDF417";
        public static final String USPS4ST = "USPS4ST";
        public static final String QRCODE = "QRCODE";
        public static final String INDUSTRIAL25 = "INDUSTRIAL25";
        public static final String TELEPEN = "TELEPEN";
        public static final String UPCA = "UPC-A";
        public static final String UPCE = "UPC-E";
        public static final String GS1_128 = "GS1-128";
        public static final String GS1_DATABAR_LIMITED = "GS1_DataBarLimited";
        public static final String GS1_DATABAR_EXPANDED = "GS1_DataBarExpanded";
        public static final String IATA25 = "IATA25";
        public static final String GRIDMATRIX = "Grid_Matrix";
    }
```

示例代码：

```java
    XcBarcodeScanner.enableBarcodeType(BarcodeType.QRCODE, true); // 开启QRCode支持
    XcBarcodeScanner.enableBarcodeType(BarcodeType.QRCODE, false); // 关闭QRCode支持
```

## 查询指定条码类型当前是否开启

所有扫码类型都定义在BarcodeType类中。

```java
    boolean isBarcodeTypeEnabled(String type)
```

## 输出方式设置

可以通过SDK提供的接口控制扫码服务输出扫码结果的方式。

```java
    XcBarcodeScanner.setOutputMethod(String method);
```

所有输出方式都定义在OutputMethod类中

```java
    public class OutputMethod {
        public static final String NONE = "NONE"; // No putput, application can get barcode data via callback.
        public static final String BROADCAST = "BROADCAST_EVENT"; // Output barcode data via broadcast.
        public static final String KEYBOARD = "KEYBOARD_EVENT"; // Output barcode data via keyboard simulate.
        public static final String CLIPBOARD = "CLIPBOARD_EVENT"; // Output barcode data via clipboard simulate.
        public static final String BROADCAST_KEYBOARD = "BROADCAST_EVENT/KEYBOARD_EVENT"; // Output barcode data via broadcast and keyboard simulate.
        public static final String BROADCAST_CLIPBOARD = "BROADCAST_EVENT/CLIPBOARD_EVENT"; // Output barcode data via broadcast and clipboard simulate.
    }
```

示例代码：

```java
    XcBarcodeScanner.setOutputMethod(OutputMethod.CLIPBOARD);
```

## 扫码提示设置

可以通过SDK提供的接口设置扫码成功提示。

```java
    XcBarcodeScanner.setSuccessNotification(String notification);
```

所有提示类型都定义在NotificationType类中

```java
    public class NotificationType {
        public static final String MUTE = "Mute";
        public static final String SOUND = "Sound";
        public static final String VIBRATOR = "Vib";
        public static final String SOUND_VIBRATOR = "Sound/Vib";
    }
```

示例代码：

```java
    XcBarcodeScanner.setSuccessNotification(NotificationType.SOUND);
```

## 扫码指示灯设置

可以通过SDK提供的接口设置扫码成功指示灯功能开关。

```java
    XcBarcodeScanner.enableSuccessIndicator(boolean enabled);
```

示例代码：

```java
    XcBarcodeScanner.enableSuccessIndicator(true); // 开启扫码指示灯功能
    XcBarcodeScanner.enableSuccessIndicator(false); // 关闭扫码指示灯功能
```

## 扫码超时设置

可以通过SDK提供的接口设置扫码超时时间。

```java
    XcBarcodeScanner.setTimeout(int seconds);
```

超时时间范围为1~9秒。

示例代码：

```java
    XcBarcodeScanner.setTimeout(5); // 设置超时时间为5秒
```

## 瞄准灯工作模式设置

可以通过SDK提供的接口设置瞄准灯的工作模式。

```java
    void setAimerLightsMode(int aimMode);
```

瞄准灯支持的工作模式在AimerMode类中定义：

```java
public class AimerMode {
    public static int ALWAYS_OFF; // Always Off.
    public static int TRIGGER_ON; // TurnOn while scanning.
    public static int ALWAYS_ON; // Always On.
}
```

示例代码：

```java
    XcBarcodeScanner.setAimerLightsMode(AimerMode.TRIGGER_ON); // 设置瞄准灯工作模式为：扫码时亮起
    XcBarcodeScanner.setAimerLightsMode(AimerMode.ALWAYS_OFF); // 设置瞄准灯工作模式为：一直关闭
```

**注意：** 如果激光瞄准灯，请勿使用常亮（ALWAYS_ON）工作模式。

## 补光灯工作模式设置

可以通过SDK提供的接口设置补光灯的工作模式。

```java
    void setFlashLightsMode(int flashMode);
```

补光灯支持的工作模式在FlashMode类中定义：

```java
public class FlashMode {
    public static int OFF;           // Always off.
    public static int ILLUME_ONLY;   // Only illume.
    public static int ILLUME_STROBE; // Illume and Strobe.
}
```

示例代码：

```java
    XcBarcodeScanner.setFlashLightsMode(FlashMode.ILLUME_STROBE); // 设置补光灯工作模式为：补光+闪光
    XcBarcodeScanner.setFlashLightsMode(FlashMode.ILLUME_ONLY);   // 设置补光灯工作模式为：仅补光
```

**注意：** 补光灯工作时，仅会在扫码时点亮，闪光（STROBE）模式会在图像传感器曝光的瞬间输出高亮光。


## 扫码结果大小写设置

可以通过SDK提供的接口设置扫码结果的大小写转换选项。

```java
    XcBarcodeScanner.setTextCase(String textCase);
```

所有转换选项都定义在TextCaseType类中

```java
    public class TextCaseType {
        public static final String NONE = "NONE_CASE";
        public static final String UPPER = "UPPER_CASE";
        public static final String LOWER = "LOWER_CASE";
    }
```

示例代码：

```java
    XcBarcodeScanner.setTextCase(TextCaseType.NONE);  // 设置扫码结果大小写不转换
    XcBarcodeScanner.setTextCase(TextCaseType.UPPER); // 设置扫码结果转换为大写
    XcBarcodeScanner.setTextCase(TextCaseType.LOWER); // 设置扫码结果转换为小写
```

## 扫码结果前缀设置

可以通过SDK提供的接口为扫码结果添加指定的前缀。

```java
    XcBarcodeScanner.setTextPrefix(String prefix);
```

示例代码：

```java
    XcBarcodeScanner.setTextPrefix("<");  // 设置扫码结果前缀为“<”
    XcBarcodeScanner.setTextPrefix("Empty"); // 设置扫码结果前缀为空
```

## 扫码结果后缀设置

可以通过SDK提供的接口为扫码结果添加指定的后缀。

```java
    XcBarcodeScanner.setTextSuffix(String prefix);
```

示例代码：

```java
    XcBarcodeScanner.setTextSuffix(">");  // 设置扫码结果后缀为“>”
    XcBarcodeScanner.setTextSuffix("Empty"); // 设置扫码结果后缀为空
```

## 设置连续扫码间隔时间

可以通过SDK提供的接口设置连续扫码的间隔时间。

```java
    setLoopScanInterval(int ms);
```

示例代码：

```java
    XcBarcodeScanner.setLoopScanInterval(100); // 设置连续扫码间隔时间为100毫秒
```

## 获取连续扫码状态

可以通过SDK提供的接口获取当前是否正在进行连续扫码。

```java
    boolean isLoopScanRunning();
```

## 开始/停止连续扫码

可以通过SDK提供的接口触发连续扫码。

```java
    startLoopScan();
    stopLoopScan();
```

示例代码：

```java
    // 开始连续扫码
    XcBarcodeScanner.setLoopScanInterval(100); // 设置连续扫码间隔时间为100毫秒
    XcBarcodeScanner.startLoopScan(); // 开始连续扫码
  
    // 结束连续扫码
    if (XcBarcodeScanner.isLoopScanRunning()) { // 检查当前是否正在连续扫码
      XcBarcodeScanner.stopLoopScan(); // 停止连续扫码
    }
```

## 设置多条码参数

可以通过SDK提供的接口，设置多条码扫描参数。

```java
    void setMultiBarcodes(int numberOfBarcodes, boolean fixedNumber);
```

参数说明：

```text
    numberOfBarcodes - 最大单次扫描的条码数量，范围：1~20
    fixedNumber - 是否固定条码数量，true表示固定条码数量，false表示非固定条码数量。
```

注：固定条码数量是指必须达到指定数量的条码才能解码成功；非固定条码数量则是解出了几个条码就输出几个条码。

示例代码：

```java
    // 设置多条码数量为3，且固定条码数量
    XcBarcodeScanner.setMultiBarcodes(3, true);
  
    // 设置多条码数量为1（每次只尝试解出1个条码）
    XcBarcodeScanner.setMultiBarcodes(1, false);
```

注：当多条码数量设置为1时，就是默认的单条码模式，此时固定条码数量的设置值无实际意义。

## 设置条码识别范围

可以通过SDK提供的接口，设置条码识别的范围，典型应用场景为一维码精准扫描。

```java
    void setScanRegionSize(int regionSize);
```

支持的regionSize定义在RegionSizeType类中：

```java
    public class RegionSizeType {
        public static final int VIEWSIZE_100 = 0; // 100% 画幅
        public static final int VIEWSIZE_75 = 1;  // 75% 画幅
        public static final int VIEWSIZE_50 = 2;  // 50% 画幅
        public static final int VIEWSIZE_25 = 3;  // 25% 画幅
        public static final int VIEWSIZE_12 = 4;  // 12% 画幅
        public static final int VIEWSIZE_6 = 5;  // 6% 画幅
        public static final int VIEWSIZE_3 = 6;  // 3% 画幅
        public static final int VIEWSIZE_1 = 7;  // 1% 画幅
        public static final int VIEWSIZE_1D = 8;  // 一维码专用画幅（图像的中间几行）
    }
```

注：固定条码数量是指必须达到指定数量的条码才能解码成功；非固定条码数量则是解出了几个条码就输出几个条码。

示例代码：

```java
    // 设置条码识别精度为一维码精准识别
    XcBarcodeScanner.setScanRegionSize(RegionSizeType.VIEWSIZE_1D);
  
    // 设置条码识别精度为最低（整个100%画面范围内的条码都识别）
    XcBarcodeScanner.setScanRegionSize(RegionSizeType.VIEWSIZE_100);
```

注：如果开启了多条码识别，推荐使用100%画幅的识别范围。
