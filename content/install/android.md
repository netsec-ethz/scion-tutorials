---
title: Running as an app (Android)
parent: Installation
nav_order: 40
---

# Running as an app (Android)

{% include alert type="warning" title="Warning: Beta version" content="The SCION Android apps are still in beta and are not guaranteed to work." %}

## Prerequisites
- Android device with at least Android 7 (henceforth referred to as “endhost”)
- Working scion AS with services set up to be reachable via IP from the endhost (c.f. step two in [Configuring a SCION end host](../config/setup_endhost.html#2-modify-as-configuration)).
- SCION endhost app, [available on the Play Store](https://play.google.com/store/apps/details?id=org.scionlab.endhost)
- SCION sensorfetcher app, [available on the Play Store](https://play.google.com/store/apps/details?id=org.scionlab.sensorfetcher) (optional)

## Import endhost configuration
1. From your AS' `gen` directory, transfer the `endhost` directory onto your endhost's user accessible storage.
2. In the endhost's `endhost` directory, find the `sd.toml` file and make the following changes:
    - Either:
        - Delete the `[log.file]` section entirely
        - Or replace the `[log.file]` section's `path` value with an absolute path that is accessible to you, e.g. `"/sdcard/logs/sd***.log"`.
    - Either:
        - Delete the `[trust_db]` section's entire `connection` assignment.
        - Or replace the `[trust_db]` section's `connection` value with an absolute path that is accessible to you.
    - Either:
        - Delete the `[path_db]` section's entire `connection` assignment.
        - Or replace the `[path_db]` section's `connection` value with an absolute path that is accessible to you.

## Starting the dispatcher and sciond
Open the app and push the “Start dispatcher” button. Your notification drawer should now have a new permanent entry called “Dispatcher service”.
Push the “Set endhost directory” button. In the dialog that appears, navigate to your endhost's `endhost` directory and press “OK”. Your notification drawer should now have a new permanent entry called “Sciond service”.
The log files (usually) located in `/sdcard/Android/data/org.scionlab.endhost/files/logs/` should now get populated and not contain error messages.
Apart from the `/sdcard/` prefix, the dispatcher logs are hardcoded to be in the aforementioned directory, while sciond's logs should either be in the aforementioned directory or where they've been configured to be in the previous step.

## Testing connectivity with scmp
In the text box, put in command line parameters for a call to the scmp application. These *have to be* newline-separated.
It is advised to set the `-c` flag to a non-zero value since there is currently no way to gracefully *interrupt* the scmp process, once started.
An example configuration would look as follows (mind the newlines!):
```
echo
-local
[endhost address]
-remote
19-ffaa:0:1303,[0.0.0.0]
-c
1
```
Tap the “Start scmp” button to test your endhost's connectivity.
You should notice movement in your log files.
With access to your endhost's logcat, you could even see scmp's stdout and stderr outputs.
If after some seconds, there is a notification titled “Scmp service” mentioning “Scmp returned with value 0”, you have SCION connectivity.

## Testing connectivity with sensorfetcher
Besides scmp being built into the SCION endhost app, the `sensorfetcher` has been ported to Android as a separate app. Both the local SCION address of the Android endhost and the remote SCION address of a running `sensorserver` have to be specified, as described [here](../apps/fetch_sensor_readings.html).
