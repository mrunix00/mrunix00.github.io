---
title: "The dumbest ransomware I've ever seen"
date: 2026-08-09T18:30:59+01:00
draft: false
---

A few days ago, I saw a Facebook post from an anonymous person who shared an APK for an app they claimed was an AI teaching app for high school students.

![Facebook post screenshot](facebook.jpg)

The app was very suspicious, considering that it was only 76 KB in size. In addition, someone who can afford LLM tokens can definitely afford to publish the app on the Play Store.

My suspicion was confirmed: the app turned out to be ransomware, and a horribly designed one at that.

![Ransomware screenshot](ransomware.png)

Aside from the horribly designed UI, we can see that the ransomware demands that the user pay the equivalent of **$300** in Bitcoin (which, at the time of writing, [has not been paid by anyone](https://www.blockchain.com/explorer/addresses/btc/bc1q44pvm04dug444umxd83q8pp9rp490tedwpsjwm)) and contact the attacker via Telegram at `@Dz_rasnom` (yes, with the typo).

It also threatens to leak all the user's personal data if the ransom is not paid within 48 hours. Funnily enough, **the countdown timer doesn't even work** unless the app's language is set to Arabic, and even then, it doesn't remember its state when the app is closed.

After decompiling the APK with [JADX](https://github.com/skylot/jadx), I was able to examine the source code and understand what the ransomware was doing. This led to some interesting findings:

- Instead of using a [C&C server](https://en.wikipedia.org/wiki/Botnet#Command_and_control), the ransomware uses **a Telegram bot** to send metadata about the victim, including the victim's device ID, device model, number of encrypted files, and, most importantly, the **private key** used for encryption.
- There's no code responsible for uploading the victim's files, nor is there any mechanism for remotely accessing the device, meaning that **the leak threat is fake** and only serves to scare the victim into paying the ransom.
- Both the public and private keys are **created locally on the victim's device** and stored locally in `shared_prefs/enc_state.xml`.

So we know that **the private key** is stored locally on the victim's device and that the leak threat is fake. We therefore need to find a way to extract the private key from the victim's device.

The thing about Android is that apps are isolated from each other, and data stored in `shared_prefs` is not accessible to other apps or even [adb](https://en.wikipedia.org/wiki/Android_Debug_Bridge).

The good news is that **adb** has a command called `run-as`, which allows you to run a shell command under an application's UID. This command is unavailable for ordinary production apps, but fortunately, **the ransomware app was actually built in debug mode**.

With the following command, we can read the contents of `enc_state.xml` and extract the key:

```
adb exec-out run-as my.app.test cat shared_prefs/enc_state.xml
```

This gives us the private key and the list of encrypted files:

```xml
<?xml version='1.0' encoding='utf-8' standalone='yes' ?>
<map>
    <string name="language">en</string>
    <string name="private_key">MIIEvQIBADANBgkqhkiG9w0BAQEFAASCBKcwggSjAgEAAoIBAQC09oQptjBrnLbsQBj3C2uKcjqgygAgFTuPviD2F7NBEron59YULyX9tE3WgbJuHMRi5M356H/zSGDFV+XVUzyNLn3AN53urnUbmB62/pmVvf0aMe3RK032HylMN6nKYsu3klz9vmC2hBXiV5XWQdE7O4GdsfP4MSKRqjF6FIs4hEZFmq70tUYqi9U/d+lFHGZ4tBRmOx58az48v7EGP3PiYHKy6Tdh4vFnPdXhA3BWwU5A/l1IDAmKj71QrwF3veNN+qCcp1gy6kTxflEGbJVQGIbR5UKKtE6F9JZN11PPBAxgMTXzFLLVL5XYnrfKn/URLr+mltbc3CtNB+rNwIrzAgMBAAECggEAGgiK29tUx5kcgbajliAY9lUCP9u/koircGhC9/78hTngo66zurn2W7NtgpSsVYX4RLKI5VEViIDFly5S0f0vAq/eXGGZQVtCCAMQNsrqz8LXdMYbtzyPyH6+zghbT1J7idWvC2aw0CEGwEX8YtNGJUt6v/PNVsvEHt4z+aLsome6yrxCRHFr+tk7ZjpZaM/vMipnuQ5UM7fZ1IC2jkGQHahesT114/xQZgYTcJlhP0fFLDL17kUGnR9+m1vxERQsgA9v70RuhNmq1L70caskLPmRNBIbuKccnoueO4glxySCXCOn/hbkFHO1aPZnz84++2xFh/5AtEp5wHhY2MG68QKBgQDwgl7GOwNe4oljM3eckeV2aKlAMT5uCikNaTP8szzA3RaNSI0RrT35JWruWuFYwtK6YSCNA8m1Hl9MAHerFd+tvvh+BLvgQSYbGYy1gS2PMeJMrX+SzesHnKs2MQIlLTe14xIJInA4IzOTP5Ua9pL6MOlmFk92BHGCba4Sc8hBAwKBgQDAnlGN4W6pc6pRu1QNh4xcH4MWLz0EF+k95AkH0KgGwhBDnmOu4egqrx5c2I9eIl/a+X/e7D6veHa9PHhm2+Bm44ExFA+bAkrr+6fGMlZyg0yXcIeGPI2de7uRSoZgbTtbJVv+VP4K1cJgt8nXdEuSmuzULrPVlk79axgU73BTUQKBgQDwNVVQ6PnJ17neATZSgwjJxWhdO5Gdt7oesdcBaUahXW87tp1mbT+7SdrNqqjszNBI497t2D2qDmahcsoSbqtTTavDuRintonHgTIeyaDmJq1dZd9HkTBPDvOVEUeSN+IvYFXQF7w65TWKuHNEVcaf6SADHP9J4NA5sy+rMEZuXwKBgHiHWL7wTl/IWc5U6w6gQAsxSMSsAIE8OR4aQDBct5eq/aRiSzwWbkGvXM96T5P27IZIU2R57eu89IgLe8fZVHztZV0BkZZMg/dQfLZ/rypu0JF+v78WywK9dyw+NwHFuDubkAoAwwqiocQFnU6tndB6EzHem3dOW9Ulm2cWQPJRAoGAUhQwQ/MUbhZil73APmIA5jm3l/3UF2wSKO/CwuLdDFgAA+Wfdq9gh5MPeIBpc9CNc2u6XX/yvO8sc3tflYBMolJr8rwiAvjcgfrWzSHnaJgBpu8t943WaOW9zab4m1QwXa++UdGwwyAFB5HmojBa/bXt+hcnjbvCit6S0iYMI5o=</string>
    <string name="encrypted_files">[{&quot;originalPath&quot;:&quot;\/storage\/emulated\/0\/Download\/download.jpeg&quot;,&quot;originalSize&quot;:0,&quot;encryptedPath&quot;:&quot;\/storage\/emulated\/0\/Download\/download.jpeg.enc&quot;,&quot;iv&quot;:&quot;k2KxBQTk5pdM2CHm7TQ2Wg==&quot;,&quot;encryptedAesKey&quot;:&quot;ceVErM+lBedOXjjDUuZIPXQc4TFgdPvO+GJ13MZzR37S0d8gg29Fyc\/yVHwjG6D1MTM12c4aoX72k9YhAZyaUtQdBz44fp3MpmtyAh353dqfNzHxZ7oqDFBOaqx+pKp2x\/1d5BS3EnB\/gnHc0oj4qcAOOgmRQfKimDS36EvP6CINriN8\/oAgGxAwROFkIPeaJ+vM3jKgOS1o+9KXW+7NR1ERX9LrtSo1UBNJhBiGPEOifm7+OOPIPO832oa3nKcRw38SVuUI289UVszlKrSO9dea6T7gF+6SIKDo6EYnlvEFhLid+EvoeGVk0Fsa2vaR\/nmV4mY\/7ilYvvWjE8CccQ==&quot;}]</string>
</map>
```

When we enter the value of **private_key** into the ransomware, we can restore our encrypted files!

![Decrypted screenshot](decrypted.png)
