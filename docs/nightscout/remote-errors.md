## <span translate="no">Loop</span> data is not showing in <span translate="no">Nightscout</span>

* This is a <span translate="no">Loop</span> and/or <span translate="no">Nightscout</span> issue, not related to remote configuration
    * Review the [LoopDocs: <span translate="no">Nightscout</span> with <span translate="no">Loop</span>](update-user.md) page
    * Check out links on the [LoopDocs: <span translate="no">Nightscout</span> Troubleshooting](troubleshoot.md) page
* Make sure Looper's phone is connected to the internet so it can upload to <span translate="no">Nightscout</span>

## Improper Configuration

### <span translate="no">Nightscout</span> Config and <span translate="no">Loop</span> Build Method

Ensure your <span translate="no">Nightscout</span> version is at least version 14.2.6.

Verify that you performed all the [Remote Configuration](remote-config.md) steps for the <span translate="no">Nightscout</span> site including sending an override from the <span translate="no">Loop</span> phone to <span translate="no">Nightscout</span>.

#### BadDeviceToken

When the <span translate="no">Nightscout</span> config var LOOP_PUSH_SERVER_ENVIRONMENT does not match the <span translate="no">Loop</span> app build method; the error message contains the phrase `APNs delivery failed: BadDeviceToken`.

* If <span translate="no">Loop</span> was installed remotely (typically from TestFlight following GitHub Browser Build), you **must** have <span translate="no">Nightscout</span> config var `LOOP_PUSH_SERVER_ENVIRONMENT` set to `production`
* If <span translate="no">Loop</span> was built using Mac-Xcode, you **cannot** have `LOOP_PUSH_SERVER_ENVIRONMENT` as one of your <span translate="no">Nightscout</span> config vars

If you attempt to issue a command from <span translate="no">Nightscout</span> Careportal; after you hit submit and then OK, you will see the error message:

```
Error: APNs delivery failed: BadDeviceToken
```

If you attempt to issue a command using <span translate="no">Loop Caregiver</span>; after you authenticate the command, you will see the error message listed below and shown in the screen shot.

```
HTTP Error
Status Code: 500
body: APNs delivery failed: BadDeviceToken
```

![site and build method mismatch](img/site-build-mismatch.png){width="300"}
{align="center"}

### <span translate="no">Loop</span> `REMOTE_OVERRIDES_DISABLED`

You can build Loop with [Build-Time Features](../build/code-customization.md#build-time-features) as part of code customization.

If you added this Build-Time Flag: `REMOTE_OVERRIDES_DISABLED`

You will not see any errors, but nothing will happen when you issue any kind of remote command.

**Solution**: Remove  `REMOTE_OVERRIDES_DISABLED` from  `LoopConfigOverride.xcconfig` file and rebuild the <span translate="no">Loop</span> app.

### Incorrect Password (OTP) Error

The references to Caregiver below is the person sending the commands. There are specific <span translate="no">Loop Caregiver</span> app insructions that you modify for your authenticator. You must have the <span translate="no">Loop</span> phone with you to troubleshoot this problem.

* The Apple clock should be set to automatic on both the Looper's phone and Caregiver’s device.
    * If the clock is incorrect, even slightly, remote commands will fail.
* Check if One-Time Passwords (OTP) align between Caregiver and <span translate="no">Loop</span>.
    * In <span translate="no">Loop</span>: Settings -> Services -> <span translate="no">Nightscout</span>
    * In <span translate="no">Loop Caregiver</span>: Settings -> Tap on Loopers Name
    * Observe the 6-digit OTP as they change
* If the OTP don't match, you can reset it:
    * **Warning**: If there are multiple devices (or people) sending remote commands, this procedure **resets the OTP for all**
    * <span translate="no">Loop</span>: Settings -> Services -> <span translate="no">Nightscout</span> -> One-Time Password -> Tap Reload button
        * The QR code is different as soon as you hit `Reload`
    * <span translate="no">Loop Caregiver</span>: Delete the Looper's profile from <span translate="no">Loop Caregiver</span> and add the Looper again by scanning their new QR code
    * Authenticators for every device used to send remote commands must be updated
        * Delete the OTP configuration
        * Add the new QR code

## Undelivered or Expired Commands

Apple Push Notifications will often not make it to an app, either due to your settings or intentional limitations by Apple. This error can appear in various forms:

* Push notification banner never shows on Looper’s device.
* Push notification banner shows but nothing happens in <span translate="no">Loop</span> (no error or success message afterwards)
* Error message shows that Password (OTP) is expired

While <span translate="no">Loop</span> does not have control over Push Notification timely delivery, there are things that can be done to mitigate these issues. Note that rebuilding <span translate="no">Loop</span>, <span translate="no">Loop Caregiver</span> or <span translate="no">Nightscout</span> is generally not going to help.

Check these settings on the Looper’s device, not the Caregivers. Several of these are related to Apple suppressing notifications.

* Notifications
    * Settings -> Notifications -> <span translate="no">Loop</span>
    * Turn on “Allow Notifications”
    * Turn on “Time Sensitive Notifications”
* Focus Modes
    * For all focus modes (ex: Do Not Disturb, Sleep), make sure <span translate="no">Loop</span> is listed as an app allowing Notifications.
    * To Adjust
        * Settings -> Focus
        * Select the focus mode (ex: Do Not Disturb, Sleep)
        * Under “Allow Notifications, tap “Apps”
        * Add “<span translate="no">Loop</span>” to the list.               
        * Turn on “Time Sensitive Notifications”.
* Background App Refresh
    * Settings -> General -> Background App Refresh
    * Select “On” up top
    * Activate <span translate="no">Loop</span> toggle in list
* Lower Power Mode
    * Turn off if able

Some other things to try on the Looper’s phone:

* Reboot phone
    * This sometimes resets Apple’s push notification limit.
* Try wifi instead of cellular, if able
    * Apple may not deliver notifications on cellular as often as wifi.
* Charge the phone
    * If the battery is low, iOS may not deliver notifications to save battery life.
* Limit the number of <span translate="no">Loop</span> commands you send in a short period. Apple may throttle notifications if too many are received. (i.e. no more than 1 or 2 per hour may help).
* Consider disabling “spammy” notifications from other apps. This is only a theory, but it is possible that other apps can cause the system to throttle all notifications, including <span translate="no">Loop</span>’s.
* Wait 24 hours or so as it often just takes time for the push notification limits to “reset”.
* iOS 15.3.x: Note there are reports of Remote notifications not being received to the Loopers device for iOS version 15.3 and 15.3.1. This is fixed in iOS version 15.4.

## How to Ask for Help

This is helpful information to share when requested by helpers. If you are not using <span translate="no">Loop Caregiver</span>, review the response seen on the <span translate="no">Nightscout</span> site.

1. Activate an override _from within <span translate="no">Loop</span>_. Does <span translate="no">Nightscout</span> show the active override?
1. Activate an override _from <span translate="no">Nightscout</span>_. Does it change the active override in <span translate="no">Loop</span>?
1. Do errors show in <span translate="no">Loop Caregiver</span> or <span translate="no">Nightscout</span> Careportal when you send a remote command?
    * Share screenshots of error if any
1. Do errors show in iOS Notifications on the Looper’s device?
    * Check their Notification history in iOS by swiping down 
    * Share screenshots of errors if any
1. What <span translate="no">Loop</span> version are you using? Released (main) or development (dev)? Approximately when did you update last?
    * The minimum version that support remote bolus and carbs is <span translate="no">Loop</span> 3
1. What iOS version is being used on the Looper’s device?
    * Note that iOS 15.3.x had notification issues
    * Update to a newer version
1. How did you build <span translate="no">Loop</span>?
    * Build with Xcode to device (typical)?
    * Using AppCenter or Diawai? A special environment variable must be set LOOP_PUSH_SERVER_ENVIRONMENT=production

Mention which troubleshooting steps you have completed so we know whether to ask about these again.


## Other Errors

Once you've set up remote commands, you may encounter errors when trying to run them via <span translate="no">Nightscout</span> or iOS Shortcuts.  Below are the most common and typical solutions.

1. **`Error: <span translate="no">Loop</span> notification failed: Could not find deviceToken in loopSettings`** You might see this in either <span translate="no">Nightscout</span> or Shortcuts.  The error is most commonly caused by <span translate="no">Loop</span> not pointing to the right <span translate="no">Nightscout</span> instance or you haven't yet run an override locally (with the <span translate="no">Loop</span> app) before trying to run one remotely.  
    **Solution:** Confirm the <span translate="no">Loop</span> app is pointing to the right <span translate="no">Nightscout</span> site (and there are no extra spaces or a slash (`/`) at the end, and always run an override for a few seconds in the <span translate="no">Loop</span> app before you try to run one remotely.
2. **`Error: cannot POST/api/v2/notifications/loop`** You might see this in iOS Shortcuts.  This means <span translate="no">Nightscout</span> is not updated correctly and you are running a version of <span translate="no">Nightscout</span> that doesn't yet support remote overrides.   
   **Solution:** Follow the [Remote Configuration](#remote-config.md) documentation.
3. **`Error: {“status”:401,”message”:”Unauthorized”,”description”:Invalid\\/Missing”}`** You might see this in iOS Shortcuts.  This is caused by having an incorrect `API_SECRET` in the shortcut.  
    **Solution:** Double check the `API_SECRET` is correct and that there are no spaces at the end.
4. **`Error: APNs delivery failed: InvalidProviderToken`** You might see this in either <span translate="no">Nightscout</span> or Shortcuts.  This is caused because your `LOOP_APNS_KEY_ID` and `LOOP_DEVELOPER_TEAM_ID` are swapped in *Heroku*.   
   **Solution:** Double check what's listed in your *Apple Developer Account* and compare it to the config variables in *Heroku*. Your `Team_ID` is next to your name in the top right corner.  The other code is your `Key_ID`. Get the IDs in the correct location in *Heroku* to resolve the error.