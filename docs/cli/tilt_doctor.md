---
title: Tilt CLI Reference
layout: docs
hideEditButton: true
---
## tilt doctor

Print diagnostic information about the Tilt environment, for filing bug reports

### Synopsis

Print diagnostic information about the Tilt environment, for filing bug reports

```
tilt doctor [flags]
```

### Options

```
  -h, --help   help for doctor
```

### Options inherited from parent commands

```
  -d, --debug                          Enable debug logging
      --klog int                       Enable Kubernetes API logging. Uses klog v-levels (0-4 are debug logs, 5-9 are tracing logs)
      --log-flush-frequency duration   Maximum number of seconds between log flushes (default 5s)
      --trace                          Enable tracing
      --traceBackend string            Which tracing backend to use. Valid values are: 'windmill', 'lightstep', 'jaeger' (default "windmill")
  -v, --verbose                        Enable verbose logging
```

### SEE ALSO

* [tilt](tilt.html)	 - Multi-service development with no stress

###### Auto generated by spf13/cobra on 17-Feb-2020
