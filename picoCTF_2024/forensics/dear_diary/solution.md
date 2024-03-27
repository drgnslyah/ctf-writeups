# Solution

## Challenge description
If you can find the flag on this disk image, we can close the case for good!
## Hints
If you're observing binary data raw in the terminal you may be misled about the contents of a block.
## Autopsy GUI
Booted up Autopsy GUI on Windows and there we can easily find the folder "secret-secrets" which contained the empty files `innocuous-text.txt` and `its-all-in-the-name`, and a script called `force-wait.sh`: 
```
#!/bin/ash
sleep 10
```

We can see that the files were modified in February, and the next step is to check the Autopsy Timeline. This could have been interesting, but unfortunately this ultimately leads to nothing, though we can see the log of events.

PS: I found out after the event that the Autopsy browser would have lead to different results, and that you can easily found the flag there. I learned a valuable lesson here: Autopsy GUI may be good to get a quick overview of the system, but unfortunately lacks some functions compared to the browser version.

### Rabbit hole
After spending a lot of time in Autopsy GUI and FTK Imager, I spent some time going down various rabbit holes. They might have lead to something, but I couldn't manage to get anything out of it.
For instance, I connected `its-all-in-the-name` to mean `"Dear Diary" = DD = data duplicator or 'dd'`, an incredibly powerful tool for disk manipulation. So I tried to "recover" the missing data from `innocuous-text.txt` with `dd`, but that got me nowhere.

## Manual inspection and solution
I unzipped the gz file to get `disk.flag.img`. 

Then I used `binwalk -e disk.flag.img`, which extracted the contents to `_disk.flag.img.extracted`:
```
DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
1048576       0x100000        Linux EXT filesystem, blocks count: 307200, image size: 314572800, rev 1.0, ext4 filesystem data, UUID=5863aa37-e62a-4e07-bc36-17730b630b63
584056832     0x22D00000      Linux EXT filesystem, blocks count: 478208, image size: 489684992, rev 1.0, ext4 filesystem data, UUID=5f123377-8fc2-4bdc-a5e4-de3e08830883
```
I then used `7z x 100000.ext` and `7z x 22D00000.ext` to extract all the content to my disk.

I ran `grep -roE 'innocuous' -A 10` on the folder and found a match in `[SYS]/Journal`:
```
$ file Journal   
Journal: data
```
Running `cat` or `strings` on the `Journal` file doesn't amount to much, so remembering the hint here helped me out, so instead I did:

```
xxd Journal | grep inno -A 10
```
And here I finally started to see the fruits of my labour:

![image](https://github.com/drgnslyah/ctf-writeups/assets/66890316/41a5df95-2d85-40e4-a748-040b56f0441c)


Piece together the parts to get the flag:

### picoCTF{1_533_n4m35_80d24b30}
