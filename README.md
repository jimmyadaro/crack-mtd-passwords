# crack-mtd-passwords

> Bypass "mac-torrent-download" passwords using John The Ripper

We all know that download something from the "mac-torrent-download" site is a pain in the ass (mainly because it has Ads for _every single click_ we make), so this is a simple guide to crack the passwords that the site sometimes doesn't give you. You can read more about that in the "_Site issues_" section all the way down.

## Install JTR

You'll need to install [John The Ripper](https://github.com/magnumripper/JohnTheRipper), simply download the "jumbo" version from their website: https://www.openwall.com/john/

Search for "Download the latest John the Ripper jumbo release" and download the file you need. In my case, using macOS I'd download `john-1.9.0-jumbo-1.tar.gz`

## Crack password

Once you have it downloaded, `cd` to the directory, go to the `/src` folder and execute `configure` and `make` :

```
$ cd /path/to/john-1.9.0-jumbo-1/src
$ ./configure && make
```

Wait until the configuration is done (may take a while), then go back to `/run` :

```
$ cd ../run
```

There we will use `rar2jhon` which is the way _JTR_ will get the hash of our `.rar` file. We have to save that file as `rar.hashes` (usually is passed as `file.txt` but this way is more organized).

```
// Inside the "run" directory
./rar2john '/path/to/our/mdt/download/YOUR_FILE_NAME.rar' > '/path/to/our/mdt/download/rar.hashes'
```

Note that if you're searching for the same functionality on `.zip` files you can use `zip2jhon`

Then, just crack the password using that hash and the "mask" provided by a [Reddit user](https://www.reddit.com/r/Piracy/comments/dqma6c/mactorrentdownload_password_link_unavailible/fcxlkw8/):

```
// Inside the "run" directory
./john '/path/to/our/mdt/download/rar.hashes' -mask='mac-torrent-download.net_[0-9a-z][0-9a-z][0-9a-z]'
```

There you can see this regex: `[0-9a-z][0-9a-z][0-9a-z]` That's because the MTD site generates passwords using that pattern appended to the string "mac-torrent-download.net_"

JTR will try to crack the file password using that pattern, which makes the cracking more efficient in both time and CPU workload. **Remember JTR can be a high-volume CPU workload** (some passwords may take days or weeks, depends on the hardware where it's running).

I've tried this using an _Early 2011 Macbook Pro_, which has an Intel i5 at 2.3 GHz and it cracked the password in **about 4 minutes**. So a newer CPU will do it even faster.

After executing the previous command, we'll something like this:

```
Warning: detected hash type "RAR5", but the string is also recognized as "RAR5-opencl"
Use the "--format=RAR5-opencl" option to force loading these as that type instead
Using default input encoding: UTF-8
Loaded 1 password hash (RAR5 [PBKDF2-SHA256 128/128 AVX 4x])
Cost 1 (iteration count) is 32768 for all loaded hashes
Press 'q' or Ctrl-C to abort, almost any other key for status
```

If during the execution we press any key that is not the "q" key (e.g. "b", "h", whatever) we'll get a current status log. Here I've pressed it 5 times:

```
0g 0:00:00:09 1.93% (ETA: 19:31:49) 0g/s 99.66p/s 99.66c/s 99.66C/s mac-torrent-download.net_0p0..mac-torrent-download.net_3p0
0g 0:00:00:14 3.03% (ETA: 19:31:45) 0g/s 100.7p/s 100.7c/s 100.7C/s mac-torrent-download.net_831..mac-torrent-download.net_b31
0g 0:00:00:47 7.81% (ETA: 19:34:04) 0g/s 77.46p/s 77.46c/s 77.46C/s mac-torrent-download.net_8t2..mac-torrent-download.net_bt2
0g 0:00:02:13 24.97% (ETA: 19:32:55) 0g/s 87.56p/s 87.56c/s 87.56C/s mac-torrent-download.net_kz8..mac-torrent-download.net_nz8
0g 0:00:03:33 41.21% (ETA: 19:32:39) 0g/s 90.25p/s 90.25c/s 90.25C/s mac-torrent-download.net_4ue..mac-torrent-download.net_7ue
```

This is the log of tries that JTR has done.

When it already gets the password, will end the executing echo-ing something like this:

```
mac-torrent-download.net_h2h (/path/to/our/mdt/download/MY_FILE_NAME.rar)
1g 0:00:04:05 DONE (2020-06-23 19:28) 0.004069g/s 90.02p/s 90.02c/s 90.02C/s mac-torrent-download.net_g2h..mac-torrent-download.net_j2h
Use the "--show" option to display all of the cracked passwords reliably
Session completed
```

To get the password, we'll simply use:

```
$ ./john '/path/to/our/mdt/download/rar.hashes' --show
```

Which will output something like this:

```
/path/to/our/mdt/download/MY_FILE_NAME.rar:mac-torrent-download.net_h2h
```

So, in this case the file password is `mac-torrent-download.net_h2h`

# Site issues

The MTD site is infested with Ads, and you cannot download anything unless they save those Ads cookies in your browser, cookies that they will use later to confirm you disabled our beloved AdBlock (or any ad-blocking software for that matter).

Since you may bypass the Ads by just deleting the HTML element with the pop-up, we can download any torrent and still getting a password by just cracking it.

Also, even if we follow the site guides, we have no direct link in the site to the [password page](https://mac-torrent-download.net/pw.php) that **sometimes doesn't show the correct password** for the downloaded files. Shoutout to the [Reddit post](https://www.reddit.com/r/Piracy/comments/dqma6c/mactorrentdownload_password_link_unavailible/fcxlkw8/) where they provided that link.

All this makes even harder the task to download anything from that site.

> ![Fig 1](https://i.imgur.com/8uY9JMi.png)
> Fig. 1: The password page with an incorrect (empty) password, even when we have all Ads being dispayed.
