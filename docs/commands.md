# Commands

```
Service: 6e40fff0-b5a3-f393-e0a9-e50e24dcca9e
Write Characteristic (Requests): 6e400002-b5a3-f393-e0a9-e50e24dcca9e
Notify Characteristic (Responses): 6e400003-b5a3-f393-e0a9-e50e24dcca9e
```

This set of commands was likely the first Characteristic added - some commands here have newer versions in [Big Data](bigdata.md). Some Commands send split arrays, and will contain a different structure depending on the array index ([example](commands.md#heart-rate)).

Each command listed has the following 16-byte structure:

```c
struct Command {
    uint8_t commandId;
    uint8_t data[14];
    uint8_t crc;
}
```

Notes:

- To calculate the CRC, add up `commandId` and each individual byte in `data` to a large enough integer (16-bit or 32-bit), then `&` with `0xFF`.
- The commandId is actually only 7 bits - the most signifigant bit is an error flag. `(commandId & 128) >> 7` will get you the error status.

## Set Time

ID: 1

```c
struct SetTimeRequest {
    uint8_t commandId = 1;
    uint8_t year; // % 2000
    uint8_t month; // January = 1
    uint8_t dayOfMonth;
    uint8_t hour;
    uint8_t minute;
    uint8_t second;
    char unused[8]
    uint8_t crc;
}
```

```c
struct SetTimeResponse {
    uint8_t commandId = 1;
    uint8_t supportsTemperature;
    uint8_t supportsPlate; //?
    uint8_t supportMenstruation;
    uint8_t supportFlags1;
    uint16_t width;
    uint16_t height;
    uint8_t useNewSleepProtocol;
    uint8_t maxWatchFace;
    uint8_t supportFlags2;
    uint8_t supportFlags3;
    uint8_t maxContacts; // maxContacts == 0 means 20 contacts, maxContacts > 0 means maxContacts * 10
    uint8_t supportFlags4;
    uint8_t crc;
}
```

```c
enum supportFlags1 : uint8_t {
    SUPPORT_CUSTOM_WALLPAPER    = 1 << 0,
    SUPPORT_BLOOD_OXYGEN        = 1 << 1,
    SUPPORT_BLOOD_PRESSURE      = 1 << 2,
    SUPPORT_FEATURE             = 1 << 3, //?
    SUPPORT_ONE_KEY_CHECK       = 1 << 4,
    SUPPORT_WEATHER             = 1 << 5,
    SUPPORT_WECHAT              = 1 << 6;
    SUPPORT_AVATAR              = 1 << 7;
}

enum supportFlags2 : uint8_t {
    SUPPORT_CONTACTS            = 1 << 0;
    SUPPORT_LYRICS              = 1 << 1;
    SUPPORT_ALBUM               = 1 << 2;
    SUPPORT_GPS                 = 1 << 3;
    SUPPORT_JIELIE_MUSIC        = 1 << 4;
}

enum supportFlags3 : uint8_t {
    SUPPORT_MANUAL_HEART_RATE   = 1 << 0;
    SUPPORT_ECARD               = 1 << 1;
    SUPPORT_LOCATION            = 1 << 2;
    RESERVED                    = 1 << 3;
    SUPPORT_MUSIC               = 1 << 4;
    SUPPORT_RTK_MCU             = 1 << 5; //?
    SUPPORT_EBOOK               = 1 << 6;
    SUPPORT_BLOOD_SUGAR         = 1 << 7;
}

enum supportFlags4 : uint8_t {
    RESERVED                        = 1 << 0;
    SUPPORT_BLOOD_PRESSURE_SETTINGS = 1 << 1;
    SUPPORT_4G                      = 1 << 2;
    SUPPORT_NAV_PICTURE             = 1 << 3;
    SUPPORT_PRESSURE                = 1 << 4;
    SUPPORT_HRV                     = 1 << 5;
}
```

## Camera

ID: 2

```c
struct CameraRequest {
    uint8_t commandId = 2;
    ActionRequest action;
    char unused[13];
    uint8_t crc;
}

enum ActionRequest : uint8_t {
    ACTION_INTO_CAMERA_UI = 4;
    ACTION_KEEP_SCREEN_ON = 5;
    ACTION_FINISH = 6;
}
```

```c
struct CameraResponse {
    uint8_t commandId = 2;
    ActionResponse action;
    char unused[13];
    uint8_t crc;
}
```

```c
enum ActionResponse : uint8_t {
    ACTION_INTO_CAMERA_UI = 1;
    ACTION_TAKE_PHOTO = 2;
    ACTION_FINISH = 3;
}
```

## Battery

ID: 3

```c
struct BatteryRequest {
    uint8_t commandId = 3;
    char unused[14];
    uint8_t crc = 3;
}
```

```c
struct BatteryResponse {
    uint8_t commandId = 3;
    uint8_t chargePercent;
    uint8_t isCharging;
    char unused[12];
    uint8_t crc;
}
```

## Write Phone Info

ID: 4

```c
struct WritePhoneInfoRequest {
    uint8_t commandId = 7;
    uint8_t clientVersionMajor; // Current QRing version = 2
    uint8_t clientVersionMinor; // Current QRing version = 10
    char phoneName[12]; // null-terminated
    uint8_t crc;
}
```

## Sport Request

TODO: Need response sample data

ID: 7

```c
struct SportRequest {
    uint8_t commandId = 7;
    uint8_t dayOffset;
    char unused[13];
    uint8_t crc;
}
```

## Reboot

ID: 8

```c
struct RebootRequest {
    uint8_t commandId = 8;
    uint8_t constant = 1;
    char unused[13];
    uint8_t crc = 9;
}
```


## Blood Pressure

ID: 13

```c
struct BloodPressureResponse {
    uint8_t commandId = 13;
    uint8_t dataType;
    char data[13]; //TODO
    uint8_t crc;
}
```

## Blood Pressure Conform

Not sure what this is used for.

ID: 14

```c
struct BloodPressureConformRequest {
    uint8_t commandId = 14;
    uint8_t success; // 0 = false, 255 = true
    uint8_t unused[13];
    uint8_t crc;
}
```

## Blink Twice

ID: 15

```c
struct BlinkTwiceRequest {
    uint8_t commandId = 15;
    uint8_t unused[14];
    uint8_t crc = 15;
}
```

## Phone Call

ID: 17

```c
struct PhoneCallResponse {
    uint8_t commandId = 17;
    uint8_t rejectCall; // boolean
    char unused[13];
    uint8_t crc;
}
```

## Sports Request

TODO: Need response sample

ID: 19

```c
struct SportsRequest {
    uint8_t commandId = 19;
    uint32_t time;
    uint8_t unused[13];
    uint8_t crc;
}
```

## Blood Pressure

Request with [Data Request](commands.md#data-request)

ID: 20

```c
struct BloodPressureResponse {
    uint8_t commandId = 20;
    uint32_t time;
    uint8_t dbp;
    uint8_t sbp;
    char unused[8];
    uint8_t crc;
}
```

## Heart Rate

Request with [Data Request](commands.md#data-request)

Split array

ID: 21

```c
struct HeartRateRequest {
    uint8_t commandId = 21;
    uint32_t unixTime; // UTC + Timezone offset in hours
    char unused[13];
    uint8_t crc;
}
```

```c
struct HeartRateResponse {
    uint8_t commandId = 21;
    uint8_t index;
}
```

If `index` is 0:

```c
struct HeartRateResponse {
    uint8_t commandId = 21;
    uint8_t index;
    uint8_t size;
    uint8_t range;
    char unused[11];
    uint8_t crc;
}
```

If `index` is 1:

```c
struct HeartRateResponse {
    uint8_t commandId = 21;
    uint8_t index;
    uint32_t utcTime;
    uint8_t heartRateData[9];
    uint8_t crc;
}
```

If `index` is > 1:

```c
struct HeartRateResponse {
    uint8_t commandId = 21;
    uint8_t index;
    uint8_t heartRateData[13];
    uint8_t crc;
}
```

## Personalization Settings

ID: 23

```c
struct PersonalizationSettingsRequest {
    uint8_t commandId = 23;
    uint8_t num1 = 1;
    uint8_t num2 = 2;
    uint8_t num3 = 3;
    char unused[11];
    uint8_t crc;
}
```

```c
struct PersonalizationSettingsResponse {
    uint8_t commandId = 23;
    uint8_t clockSetting;
    uint8_t powerOnSetting;
    uint8_t powerOffSetting;
    char unused[11];
    uint8_t crc;
}
```

## Weather Forecast

ID: 26

```c
struct WeatherForecastRequest {
    uint8_t commandId = 26;
    uint8_t index;
    uint32_t timestamp;
    uint8_t weatherType;
    uint8_t minDegrees;
    uint8_t maxDegrees;
    uint8_t humidity;
    uint8_t takeUmbrella; // 1 is true, 2 is false
    char unused[4];
    uint8_t crc = 26;
}
```

```c
struct WeatherForecastResponse {
    uint8_t commandId = 26;
    char unused[14];
    uint8_t crc = 26;
}
```

## Music Switch

ID: 28

```c
struct MusicSwitchRequest {
    uint8_t commandId = 28;
    uint8_t isPlaying;
    uint8_t progress;
    uint8_t volume;
    char songName[10];
    uint8_t crc;
}
```

```c
struct MusicSwitchResponse {
    uint8_t commandId = 28;
    uint8_t action;
    uint8_t enable;
    char unused[12];
    uint8_t crc;
}
```


## Music

ID: 29

```c
struct MusicCommandResponse {
    uint8_t commandId = 29;
    MediaAction action;
    char unused[13];
    uint8_t crc;
}
```

```c
enum MediaAction {
    Pause = 1,
    Previous = 2,
    Next = 3,
    VolumeUp = 4,
    VolumeDown = 5
}
```

## Realtime Heart Rate

Request with [Data Request](commands.md#data-request)

ID: 30

```c
struct RealtimeHeartrateRequest {
    uint8_t commandId = 30;
    uint8_t type; // Variable but only set to 3 in app
    char unused[13];
    uint8_t crc;
}
```

```c
struct RealtimeHeartrateResponse {
    uint8_t commandId = 30;
    uint8_t heartRate;
    char unused[13];
    uint8_t crc;
}
```

## Find Phone

ID: 34

```c
struct FindPhoneResponse {
    uint8_t commandId = 34;
    FindPhoneCmd findPhoneCmd;
    char unused[13];
    uint8_t crc;
}
```

```c
enum FindPhoneCmd : uint8_t {
    StartRinging = 1,
    StopRinging = 2
}
```

## Alarm

ID: 36

```c
struct AlarmRequest {
    uint8_t commandId = 36;
    uint8_t index;
    char unused[13];
    uint8_t crc;
}
```

```c
struct AlarmResponse {
    uint8_t commandId = 36;
    uint8_t index;
    uint8_t enabled;
    uint8_t hour;
    uint8_t minute;
    uint8_t weekMask[7];
    char unused[3];
    uint8_t crc;
}
```

## Sit Long

ID: 37 (Request)
ID: 38 (Response)

```c
struct SitLong {
    uint8_t commandId;
    uint8_t startHour;
    uint8_t startMinute;
    uint8_t endHour;
    uint8_t endMinute;
    uint8_t weekMask;
    uint8_t cycle;
    char unused[8];
    uint8_t crc;
}
```

## Drink Alarm

TODO: Does this have a response?

ID: 39

```c
struct SetDrinkAlarmRequest {
    uint8_t commandId = 39;
    uint8_t alarmIndex;
    uint8_t isEnabled;
    uint8_t hour;
    uint8_t minute;
    uint8_t weekMask[7]; // Array of booleans 
    char unused[3];
    uint8_t crc;
}
```

## Alarm

ID: 40

```c
struct AlarmRequest {
    uint8_t commandId = 40;
    uint8_t index;
    char unused[13];
    uint8_t crc;
}
```

```c
struct AlarmResponse {
    uint8_t commandId = 40;
    uint8_t index;
    uint8_t enabled;
    uint8_t hour;
    uint8_t minute;
    uint8_t weekMask[7]; // Seems the flags are split so byte 1 uses bit 1, byte 2 uses bit 2. Very inefficient.
    char unused[3];
    uint8_t crc;
}
```

## Blacklist

TODO: Response?

ID: 45

```c
struct BlackListRequest {
    uint8_t commandId = 45;
    uint8_t data = 1;
    char unused[13];
    uint8_t crc = 46;
}
```

## Package Length

TODO: Request?

ID: 47

```c
struct PackageLengthResponse {
    uint8_t commandId = 47;
    uint8_t packageLenth;
    char unused[13];
    uint8_t crc;
}
```

## Avatar

ID: 50

```c
struct DeviceAvatarRequest {
    uint8_t commandId = 50;
    char unused[14];
    uint8_t crc = 50;
}
```

```c
struct DeviceAvatarResponse {
    uint8_t commandId = 50;
    uint8_t screenType;
    uint16_t width;
    uint16_t height;
    char unused[9];
    uint8_t crc;
}
```

## Pressure

Split array

ID: 55

```c
struct PressureRequest {
    uint8_t commandId = 55;
    uint8_t index;
    uint8_t unused[13];
    uint8_t crc = 55;
}
```

```c
struct PressureResponse {
    uint8_t commandId = 55;
    uint8_t index;
}
```

If `index` is zero, read this:

```c
struct PressureResponse {
    uint8_t commandId = 55;
    uint8_t index;
    uint8_t totalArraySize;
    uint8_t range;
}
```

If `index` is 1, read this:

```c
struct PressureResponse {
    uint8_t commandId = 55;
    uint8_t index;
    uint8_t startOffset;
    uint8_t pressureData[12];
    uint8_t crc;
}
```

If `index` is > 1, read this:

```c
struct PressureResponse {
    uint8_t commandId = 55;
    uint8_t index;
    uint8_t offset;
    uint8_t pressureData[12];
    uint8_t crc;
}
```

## HRV

Split array

ID: 57

```c
struct HRVRequest {
    uint8_t commandId = 57;
    uint8_t index;
    char unused[13];
    uint8_t crc;
}
```

```c
struct HRVResponse {
    uint8_t commandId = 57;
    uint8_t index;
}
```

If `index` is 0, read this:

```c
struct HRVResponse {
    uint8_t commandId = 57;
    uint8_t index;
    uint8_t totalArraySize;
    uint8_t range;
    char unused[11];
    uint8_t crc;
}
```

If `index` is 1, read this:

```c
struct HRVResponse {
    uint8_t commandId = 57;
    uint8_t index;
    uint8_t startOffset;
    uint8_t HRVData[12];
    uint8_t crc;
}
```

If `index` is > 1, read this:

```c
struct HRVResponse {
    uint8_t commandId = 57;
    uint8_t index;
    uint8_t HRVData[13];
    uint8_t crc;
}
```

## Sports Data

ID: 67

```c
struct SportsDataRequest {
    uint8_t commandId = 67;
    uint8_t dayIndex;
    uint8_t unk1; // Statically set to 15
    uint8_t unk2; // Only ever set to 0
    uint8_t unk3; // Only ever set to 95
    char unused[10];
    uint8_t crc;
}
```

```c
struct SportsDataResponse {
    uint8_t commandId = 67;
    uint8_t year; // % 2000
    uint8_t month; // January = 1
    uint8_t day;
    uint8_t time;
    uint16_t calories;
    uint16_t steps;
    uint16_t distance;
    char unused[4];
    uint8_t crc;
}
```

## Sleep Data

Newer request in [Big Data](bigdata.md#sleep)

ID: 68

```c
struct SleepDataRequest {
    uint8_t commandId = 68;
    uint8_t dayOffset;
    uint8_t unk1; // Statically set to 15
    uint8_t unk2; // Only ever set to 0
    uint8_t unk3; // Only ever set to 95
    char unused[3]
    uint8_t crc;
}
```

```c
struct SleepDataResponse {
    uint8_t commandId = 68;
    uint8_t year; // value of 15 = 2015
    uint8_t month;
    uint8_t day; // starts at 1
    uint8_t time;
    uint8_t sleepQualities; //TODO
    char unused[3]
    uint8_t crc;
}
```

## Today's Sports

TODO: Request?

ID: 72

```c
struct TodaySportsResponse {
    uint8_t commandId = 72;
    uint32_t totalSteps : 24;
    uint32_t runningSteps : 24;
    uint32_t calories : 24;
    uint32_t walkingDistance : 24;
    uint16_t activityDuration;
    uint8_t crc;
}
```

## Find Device

On rings, enables the green light for 10 seconds.

ID: 80

```c
struct FindDeviceRequest {
    uint8_t commandId = 80;
    uint8_t data1 = 85;
    uint8_t data2 = 170;
    uint8_t unused[12];
    uint8_t crc;
}
```

## ANCS Request

TODO: What does ANCS stand for?

ID: 96

```c
struct ANCSRequest {
    uint8_t commandId = 96;
    uint8_t unk1 = 255;
    uint8_t unk2 = 159
    char unused[12];
    uint8_t crc;
}
```

## Message Push

ID: 97

```c
struct MessagePushResponse{
    uint8_t commandId = 97;
    char unused[2];
    uint16_t deviceSupport1;
    uint16_t deviceSupport2;
    uint16_t deviceSupport3;
    char unused[6];
    uint8_t crc;
}
```

## Data Request

ID: 105

```c
struct DataRequest {
    uint8_t commandId = 105;
    DataType dataType;
    DataAction dataAction;
    char unused[12];
    uint8_t crc;
}
```

```c
enum DataType : uint8_t {
    HeartRate = 1,
    BloodPressure = 2,
    BloodOxygen = 3,
    Fatigue = 4,
    HealthCheck = 5,
    RealtimeHeartRate = 6,
    ECG = 7,
    Pressure = 8,
    BloodSugar = 9,
    HRV = 10
}
```

```c
enum DataAction : uint8_t {
    Start = 1,
    Pause = 2,
    Continue = 3,
    Stop = 4
}
```

## Stop Data Request

This shares an enumeration with `105`, which supersets the features of this command. The 2 unknown fields are always set to 0 in the code, so I'm unsure of this command's purpose.

ID: 106

```c
struct DataRequest {
    uint8_t commandId = 106;
    DataType dataType;
    uint8_t unk1;
    uint8_t unk2
    char unused[11];
    uint8_t crc;
}
```

## 3rd Party Push Message

Split array

ID: 114

```c
struct PushMessageRequest {
    uint8_t commandId = 114;
    PushMessageSource source;
    uint8_t curDataLen;
    uint8_t curDataIndex;
    uint8_t data[11];
    uint8_t crc;
}
```

```c
enum PushMessageSource : uint8_t {
    SMS = 1,
    QQ = 2,
    WeChat = 3,
    PhoneAction = 4,
    Facebook = 5,
    WhatsApp = 6,
    Twitter = 7,
    Skype = 8,
    Line = 9
}
```

## App Activity

ID: 119

```c
struct AppActivityResponse {
    uint8_t commandId = 119;
    uint8_t gpsStatus; // Unkown enumeration
    uint32_t timestamp; // Only exists when status gpsStatus = 6
}
```

## Device Notify

ID: 115

```c
struct DeviceNotifyResponse {
    uint8_t commandId = 115;
    NotifyType dataType;
    // TODO: Seems to be variable data
}

enum NotifyType : uint8_t {
    HeartRate = 1,
    BloodPressure = 2,
    BloodOxygen = 3,
    Steps = 4,
    Temperature = 5,
    Activity = 7
    //TODO there's more here
} 
```