# akonadi-workarounds

Workarounds for when Akonadi fails to do its job

## When it fails to create calendar entries on a CalDAV server

Try to create a calendar entry when the server is not reachable (say, you are
offline, or you typed the wrong password), and you may find that you end up
with no error messages, an entry that shows up perfectly fine, but that will
NEVER be synced to the server ever again.

The script `find-missing-entries` can help with that. You need to give it an
export of what akonadi thinks is in a remote calendar, an export of what really
is in the remote calendar, and you will get a directory of .ics files to upload
to your CalDAV server using cadaver.

### How to export what Akonadi thinks there is in the remote server

Open korganizer, disable (untick) all calendars except the one you want to
check, use *File/Export/Export as vCalendar*, save the file.

Note, this *could* be automated by fetching items directly from akonadi, but
the only information I found for getting data out of akonadi from python is
[here](https://forum.kde.org/viewtopic.php?f=43&t=94567), which is just a
request (unanswered) for help fixing an example script that some other poor
soul could not get to work. I could not, either.

### How to export what the server thinks there is in the collection

So far I just use this: 

```
scp server:/var/lib/radicale/collections/username/collectionname.ics server.ics
```

it can probably be automated with a simple HTTP request, but I cannot be
bothered to find how becasue exporting from akonadi is by far the most
cumbersome step here.

### How to upload the resulting entries

This is how I did it:
```
$ cadaver https://servername
[...]
Do you wish to accept the certificate? (y/n) y
Authentication required for ...:
Username: username
Password:
dav:/> cd username/collectionname.ics
dav:/username/collectionname.ics/> lcd missing.ics
dav:/username/collectionname.ics/> mput *.ics
[Matching... nnn matches.]
[...]
dav:/username/collectionname.ics/>
Connection to `servername' closed.
```

### Links

 * [Debian bug](https://bugs.debian.org/804272)
 * Search "akonadi silently fails to synchronize caldav" and cry.
