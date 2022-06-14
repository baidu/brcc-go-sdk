# Go SDK Guide



## Getting Started

### Installing

To start using brcc sdk, install Go and run `go get`:

```sh
$ go get https://github.com/baidu/brcc/brcc-go-sdk
```

This will retrieve the library and install the `brcc-go-sdk` command line utility into
your `$GOBIN` path.


### Importing brcc-go-sdk

```go
import brcc "github.com/baidu/brcc/brcc-go-sdk"
```


### Initial SDK from toml configuraiton
1. prepare brcc configuration 
```toml
serverUrl = "brcc.baidu-int.com"
projectName = "brcc-go-client"
envName = "debug"
versionName = "1.0"
apiPassword = "123456"
enableCallback = true
callbackInterval = 300
requestTimeout = 5
enableCache = true
cacheDir = "/tmp/brcc"
```
save above configuration to local file. eg. "brcc.toml"

2. initialize SDK

```go
// Initialize BRCC client by toml
name := "brcc.toml"
client, err := rcc.NewClientWithConf(name)
if err != nil {
	panic(fmt.Sprintf("init brcc error: %v", err.Error()))
}
```

### Initial SDK from Conf struct
```go
brccConf := &rcc.Conf{
    ProjectName:         "brcc-go-client",
    EnvName:             "debug",
    ServerUrl:           "brcc.baidu-int.com",
    ApiPassword:         "123456",
    VersionName:         "1.0",
    EnableCallback:      true,
    CallbackIntervalInt: 300,
    RequestTimeoutInt:   5,
    EnableCache:         true,
    CacheDir:            "/tmp/brcc",
}

client, err := rcc.NewClientWithConf(brccConf)
if err != nil {
    panic(fmt.Sprintf("init brcc error: %v", err.Error()))
}
```


#### get configuration items from brcc server. 
```go
// get configuration
client.GetValue(key, defaultValue)
// get keys
client.GetAllKeys()
```

#### bind with configuration items for struct type.
```go
type Test struct {
    A bool   `rcc:"test.a"`
    B int    `rcc:"test.b"`
    C string `rcc:"test.c"`
}

v := &Test{}
// Inject
client.Bind(v)
```

#### dynamic watch configuration items update
```go
// define update callback for Watch function
	callback := func(ce *rcc.ChangeEvent) {
	// it is recommended that defer to capture the coroutine panic
	defer func() {
		if r := recover(); r != nil {
			fmt.Println("watch update callback panic")
		}
	}()

	for key, change := range ce.Changes {
		if change.ChangeType == rcc.MODIFY || change.ChangeType.rcc.ADD  {
		// key, change.NewValue,  change.OldValue , change.ChangeType
		}
	}
}
client.Watch(callback)
```

