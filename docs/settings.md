# Settings

Shares the Service/Characteristic with Commands:
```
Service: 6e40fff0-b5a3-f393-e0a9-e50e24dcca9e
Write Characteristic (Requests): 6e400002-b5a3-f393-e0a9-e50e24dcca9e
Notify Characteristic (Responses): 6e400003-b5a3-f393-e0a9-e50e24dcca9e
```

Very similar to Commands, but a basic prefix structure for enabled. The Request/Response packet structure is identical for each of these.

Each packet will have the following 16-byte structure:

```c
struct Command {
    uint8_t commandId;
    Action action;
    uint8_t data[13];
    uint8_t crc;
}

enum Action : uint8_t {
    READ = 1,
    WRITE = 2,
    DELETE = 3
}
```
Notes:

- To calculate the CRC, add up the `commandId` and each individual byte in `data` to a large enough integer (16-bit or 32-bit), then `&` with `0xFF`.
- The commandId is actually only 7 bits - the most signifigant bit is an error flag. `(commandId & 128) >> 7` will get you the error status.

## Palm Screen

Id: 5

```c
struct PalmScreen {
    uint8_t commandId = 5;
    Action action;
    uint8_t isEnabled;
    uint8_t featureFlags;
    char unused[11];
    uint8_t crc;
}

enum featureFlags : uint8_t {
    IS_LEFT     = 1 << 0;
    NEED_PALM   = 1 << 1;
    UNKNOWN     = 1 << 2; // Always toggled on Write instance
}
```

## Do Not Disturb

ID: 6

```c
struct DoNotDisturb {
    uint8_t commandId = 6;
    Action action;
    uint8_t isEnabled;
    TimeRange dndTime; // 4 bytes
    char unused[11];
    uint8_t crc;
}
```

## Intell 

Not sure what this is used for

ID: 9

```c
struct IntellRequest {
    uint8_t commandId = 9;
    Action action;
    uint8_t unk1; // Boolean
    uint8_t unk2; // Integer
    char unused[11];
    uint8_t crc;
}
```

## User Data

ID: 10

```c
struct Userdata {
    uint8_t commandId = 10;
    Action action;
    uint8_t is24; // boolean (???)
    uint8_t usesMetric;
    uint8_t gender;
    uint8_t age;
    uint8_t height;
    uint8_t weight;
    uint8_t sbp; // Systolic blood pressure
    uint8_t dbp; // Diastolic blood pressure
    uint8_t warmingHeart;
    uint8_t open;
}
```

## Blood Pressure

ID: 12

```c
struct BloodPressure {
    uint8_t commandId = 12;
    Action action;
    uint8_t isEnabled;
    TimeRange time; // 4 bytes
    uint8_t multiple; //?
    char unused[10];
    uint8_t crc;
}
```

## Heart Rate

ID: 22

```c
struct HeartRate {
    uint8_t commandId = 22;
    Action action;
    uint8_t isEnabled;
    uint8_t heartInterval;
    char unused[11];
    uint8_t crc;
}
```

## Units

ID: 25

```c
struct Units {
    uint8_t commandId = 25;
    Action action;
    uint8_t isEnabled;
    uint8_t isCelcius;
    char unused[12];
    uint8_t crc;
}
```

## Display Time

ID: 31

```c
struct DisplayTime {
    uint8_t commandId = 31;
    Action action;
    uint8_t unk;
    uint8_t displayTime;
    uint8_t displayType; //TODO
    uint8_t alpha;
    uint8_t isCustom;
    uint8_t total;
    uint8_t type;
    char unused[6];
    uint8_t crc;
}
```

## Health Targets

ID: 33

```c
struct HealthTargetsResponse {
    uint8_t commandId = 33;
    Action action;
    int32_t steps : 24;
    int32_t calories : 24;
    int32_t distance : 24;
    int16_t sport;
    int16_t sleep;
    char unused;
    uint8_t crc;
}
```

## Menstruation

TODO: Needs testing, many of these variable names are guesses.

ID: 43

```c
struct Menstruation {
    uint8_t commandId = 43;
    Action action;
    uint8_t switch; // ?
    uint8_t during; // ?
    uint8_t cycle;
    uint8_t daysUntilNext;
    uint8_t isAlarmEnabled;
    uint8_t alarm1;
    uint8_t alarm2;
    uint8_t alarmHour;
    uint8_t alarmMin;
    char unused[4];
    uint8_t crc;
}
```

## Blood Oxygen

ID: 44

```c
struct BloodOxygen {
    uint8_t commandId = 44;
    Action action;
    uint8_t isEnabled;
    char unused[12];
    uint8_t crc;
}
```

## Pressure

ID: 54

```c
struct Pressure {
    uint8_t commandId = 54;
    Action action;
    uint8_t isEnabled;
    char unused[12];
    uint8_t crc;
}
```

## HRV

ID: 56

```c
struct HRVSetting {
    uint8_t commandId = 54;
    Action action;
    uint8_t isEnabled;
    char unused[12];
    uint8_t crc;
}
```
