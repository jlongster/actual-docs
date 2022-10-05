import Key from '../components/Key'

Actual is a different kind of app. Instead of storing all of your data on the internet by default, it stores all of your data locally. That means it works regardless of your network connection, and you always have direct access to your data.

<img width="270" style={{ float: 'right', marginLeft: 40, marginTop: 40, marginBottom: 40 }} src="/images/undraw-sync.svg" />

We don't want to throw away the internet though. It's too useful. When your data is stored in the cloud, you can access it from any device easily, and you never have to worry about losing your data in case you drop your laptop or phone into a lake.

That's why we automatically sync all of your data to the cloud in the background. You get the best of both worlds: all data is local by default, but if internet is available, your data is seamlessly backed up and synced to all other devices. This is the opposite of most apps which heavily rely on the internet to be available.

For the super privacy-focused, it even allows for your data to be [end-to-end encrypted]() since all our server is doing is passing around changes. We don't care to read it or sell it to third parties.

Even if you don't use end-to-end encryption, we will always treat your data as if it's the most sensitive information in the world. It's always stored with **bank-level 256-bit AES encryption**, and we always transmit it encrypted with SSL/TLS.

## Getting started

Once you are logged in, if you have not created a file yet it will automatically create one for you. Go ahead and poke around the app and start setting up your budget.

If you have already created files, after logging in Actual will show you all the available files. Select one and it will download it and start syncing seamlessly. That's it.

If you are interested in end-to-end encryption, you can [set it up](#end-to-end-encryption) now.

## Migrating from older versions

Version 0.0.120 changed the internal syncing format, which means all clients need to reset sync. The app guides you naturally through this, but if you are reading this you want to know more about what this means.

When you sync to the server, the app sends many small pieces of data representing changes in the app. These changes are what is synced around. Before version 0.0.120, these changes were represented as stringified JavaScript objects, but as of 0.0.120 onwards they are binary blobs. These two formats are incompatible, so when upgrading you need to "reset sync".

Resetting sync means clearing all sync data both locally and on the server, and starting clean. Once you do this, all new changes from then on will be stored in the new format. Be aware that it's important to choose the right copy of your data when resetting sync. See [What does "resetting sync" mean?](mean)

## End-to-end encryption

While your data is fully encrypted at rest and in transit on our servers, we have the keys and are still able to read your data. End-to-end encryption offers the ability for you to generate a key based on a password and encrypt it so that we can't even read it. Before your data leaves your device, it is encrypted using keys only you have.

This guarantees that only you will ever have access to your data. **This is optional** and using it requires you to enter a password whenever downloading cloud files (this only needs to be done once per device).

Data on your local device is still unencrypted. We recommend full disk encryption if you are interested in local encryption.

There are some things to consider with end-to-end encryption:

* **Pro**: Your data is fully secure and nobody except you can read it
* **Pro**: If you don't want to sync across devices, this still allows you keep a fully encrypted backup of your data
* **Con**: There is a performance hit because of encoding & decoding your data whenever syncing
* **Con**: If you lose your local data copy and forget your password, you can never recover your data

### Setting up end-to-end encryption

You can enable end-to-end encryption by opening a file, going into settings, and clicking "enable encryption" in the **Encryption** section. You will be asked to enter a password, and a key will be generated from it that will encrypted all your data from then on.

When downloading data on other devices, you will need to enter the same password to generate the key to be able to decrypt your data.

**Do not lose this password**. We will not be able to recover your data if you forget it because we cannot decrypt it. If you forget it and you still have a copy of your data locally, you can reset your key which will do a [sync reset](#what-does-resetting-sync-mean) and generate a new key.

## What does "resetting sync" mean?

There are many reasons why you might want to "start fresh" with syncing. This doesn't mean you lose any of your local data, but it means for one reason or another you want to forget about all synced data and start as if the current version of your file is the "true" version of it.

Since your data is local to each device and they all might not be up-to-date, **choose the right device** from which to reset sync. The usually won't matter, but it's important to realize that when you reset sync from one device, all unsynced changes from other devices will be reverted. Usually files are kept in sync recent enough that this isn't a problem. But if you happen to have a lot of changes that haven't been synced from one device (maybe it wasn't connected to the internet), make sure to do the sync reset from that device so it be comes the "true" version of your data.

A few scenarious where you want a sync reset:

* You restore from a backup. You wouldn't want to restore from a backup, only to find that it synced back up to where you were before! You want syncing to start fresh from the backup.
* You turn on end-to-end encryption. If you do this, the server needs to forget about any unencrypted data it already knows about. This requires starting fresh.
* In the worst case scenario, you have data that is out of sync. This should never happen, but just in case it ever does, you can manually reset sync from the file that you want to treat as the "true" version.

Resetting sync will clear all syncing data from the server, upload your existing data as the "true" version, and your device gets a new "sync id". All devices syncing data must have the same sync id.

After resetting, all other devices are now out-of-date. What happens when you try to run them? Actual will detect that syncing has been reset and tell you that they need to be reverted. Reverting a file will delete the local data, download the latest version of it, and assign the latest sync id generated by the reset. It will happily sync from then on.

**Actual will always guide you through this**. It tracks the status of all your files and will notify you if something is wrong and give you steps to fix the problem. If you want to manually reset sync, you can do that in settings.

### Creating an encryption key always does a sync reset

If you create a new encryption key, regardless if you already had one or not, it always needs to a sync reset. This is because you are changing how synced data is stored, so you won't be able to continue to use old synced data after creating an encryption key. Keep this in mind when creating keys.

## Debugging sync issues

When Actual detects a problem during syncing, you will see a notification with details and actions to solve the problem. Below are all the notifications you might see, with some greater detail about them.

**You will rarely see these messages**, and if you do Actual will guide you through how to fix the problem. If you are still having problems, [contact us](mailto:help@actualbudget.com).

### "This file is not a cloud file"

A "cloud file" is a file that has been registered with our server and is currently syncing data. Sometimes a file hasn't been registered yet so it can't send any data to sync.

Usually this happens when a new file is created when there is no internet connection. In that case, it creates a local file but our server doesn't know anything about it. When you are online you need to register it.

Local files without syncing are not supported. Even if you don't sync to multiple devices, syncing to our server creates a backup of your data and you never have to worry about losing it. If you are worried about privacy, enable end-to-end encryption.

### "Syncing has been reset on this cloud file"

If you reset sync on a device, all other devices will see this message when they try to sync. When you reset sync, it deletes all syncing data from our server (but not any local data) and treats your local file as the "true" version. Because all the syncing data has been reset, other devices cannot sync anymore.

When this happens, on other devices you will see an option to revert to the latest version. Simply reverting will get you syncing again. See [What does "resetting sync" mean?](what-does-resetting-sync-mean)

### "Missing encryption key"

You don't have the key to decrypt your data. Press "create key" to enter your password to create it so you can sync your data.

This might happen if log out and log back in. Right now, logging out deletes all your keys but leaves local files around, so you would be able to open the file but the key would be missing.

### "Your encryption key need to be reset"

Your local encryption key is different than the one that syncing data has been encrypted with. This shouldn't ever really happen, but something must have gone wrong when setting up encryption. If you see this message, you must reset your data with a new encryption key.

### "Unable to decrypt your data"

You have the right key, but for some reason decryption failed. This shouldn't ever happen and is likely an internal bug in the encryption layer. You can do a sync reset to create a new key, or contact help@actualbudget.com for support.

### "File needs upload"

Something must have gone wrong when doing a sync reset. This shouldn't ever really happen, but in the off chance that you see this message, click "Upload" to upload your data to fix it.

### "Your data is out of sync"

Unfortunately, Actual detected an inconsistency in your synced data. This only happens if there is a bug in the syncing process, and you should never see this. If it ever does happen,  doing a [sync reset](#what-does-resetting-sync-mean) will fix it.

### "Update required"

While syncing, your device received data that it couldn't apply because your version of Actual is out-of-date. You need to update Actual it will sync successfully.

### "Actual has updated the syncing format"

You will see this when upgrading to version 0.0.120. The internal syncing format has changed and you need to do a [sync reset](#what-does-resetting-sync-mean).
