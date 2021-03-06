# in_rotation
Have you ever wanted to listen to the bops you're currently into, but don't
care for organizing playlists? Did you migrate to Spotify from Apple Music and
miss the 'Recently Added' smart playlist? Then In Rotation is for you.

Follow my [In Rotation](https://open.spotify.com/user/iraphas/playlist/3mPWxEIv08fdHtVrd2gMDr?si=mgkyPDmuSWicnHtQJQTw6g).

## Using the Playlist
- every day, the playlist is updated.
- never manually add songs.
- add songs by (removing and re)adding to your music.
- remove songs as you wish. removed songs will remain out as long as they're
  not _the most recently added_ song.

## Set up
Clone this repo, do `pip install spotipy`, and `cp secret.py.example
secret.py`. Then follow instructions in `secret.py` to fill out the necessary
information.

In order to use this script, you must select a playlist to be your "In
Rotation" playlist and get its id. To do so, run:
```bash
python get_playlist_id.py
```
This script will display the names of your playlists and ask you to select one.
It will display the id of the selected playlist.

We recomment you start with a playlist that has been nuked. To do this, and to
add the most recently added songs to it, do:
```bash
python nuke_in_rotation.py
```

Once your playlist is ready to go, you may update it by running:
```bash
python main.py
```

## How it Works
The definition of "recently added" is "all songs added in the last 2 weeks".
However, if there are less than 100 songs added in the last 2 weeks, the
definition is extended to fill up at least 100 songs.

This script will compare the most recently added songs to the songs in In
Rotation. Until a matching pair has been found, any song in Recently Added but
not in In Rotation will be added to In Rotation.

Once a matching pair has been found the rules change:
- any matching pairs will remain in In Rotation.
- any song in In Rotation but not in Recently Added will be removed (if you're
  wondering how you add older songs to Recently Added, see "How to Use" below)
- any song in Recently Added but not in In Rotation will be ignored (we assume
  you removed from In Rotation because you got sick of it, and won't add
  again)

## Advanced: Setting up a Cron Job
If you want to set up the script to run daily (the recommended frequency), you
may set up a cron job.

To do this, first make sure you have run `python main.py` at least once, so the
authorization token is created. Then, you will need to find the absolute path
of the python runtime which has spotipy installed. I'm using venv, so that is
`/home/raphael/dev/in_rotation/rot/bin/python` for me. To find yours, you can
do `which python`

Then, run `crontab -e`. This will open vim. Insert the following line:
```
0 4 *  *  *  cd /home/raphael/dev/in_rotation/ /home/raphael/dev/in_rotation/rot/bin/python /home/raphael/dev/in_rotation/main.py
```

The `cd` part is important because it guarantees that the `.cache-{email}`
file will be visibile to spotipy. Otherwise, the script will attempt to gain
authorization again, which requires user interaction.

If the token expires, the script will identify it and send a push notification
to the user with `push`, which you can install from
[here](https://github.com/iRapha/junkdrawer). Then modify `main.py` to call the
absolute path of the push binary you installed.

If the cron job seems to not be working, you may try appending
`>> /home/raphael/dev/in_rotation/cron_log.txt 2>&1` to visualize stdout. If
the job isn't running at all, you may want to restart cron with
`sudo /etc/init.d/cron restart`.
