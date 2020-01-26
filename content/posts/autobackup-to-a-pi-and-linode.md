---
title: "Backup to a Raspberry Pi and Linode"
date: 2020-01-25T20:45:17-08:00
tags:
    - linode
    - bash
draft: false
---
<script async src="//s.imgur.com/min/embed.js" charset="utf-8"></script>

# Back it up
I've been wanting to backup some of my local files for a while.  Basic things like photos, music, PDFs, etc. I started by plugging a Raspberry Pi into a portable hard drive [like this one](https://www.newegg.com/seagate-model-sthn2000400-2tb/p/1Z4-002P-00VD2?Description=storage%20drive%20usb&cm_re=storage_drive_usb-_-1Z4-002P-00VD2-_-Product).  So I had local backups taken care of, cool!  But what if the hard drive lost power or I had a problem with the Pi itself?  Plus my laptop and Pi are in the same house so what if the house flooded or burned down?

This led me to [Linode's Object Storage](https://www.linode.com/products/object-storage/) and the pricing looked good so I thought I'd give it a shot.  It essentially works like S3 storage and many of the command line interface tools are compatible with both S3 and Linode Object Storage. 

### What you'll need
- Linode Account
- The basics of Object Storage: https://www.linode.com/docs/platform/object-storage/how-to-use-object-storage/
- Basic Bash skills
- Copy-paste some Pip Install commands


### Generating an API Token
The `API Token`, not to be confused with the `Key Pair` mentioned elsewhere, is used by the Linode-CLI to interact with your resources.  Depending on the scope of access you give to your token, the token can modify everything on your account or be restricted to a small subset of Linode resources.  In our case we'll be setting up a token that can only modify:

- Account data
- Object Storage data

<a href="https://imgur.com/yT6eQAf"><img src="https://i.imgur.com/yT6eQAf.png" title="source: imgur.com" /></a>


Go to https://cloud.linode.com/profile/tokens and click `Add a Personal Access Token`.  You'll see a series of radio buttons, here you'll select `Account` and `Object Storage`.  I named my token `test_token`.

Once your token is created you'll be given the actual token value 1 time.  Copy this somewhere safe or leave this popup up for the next step "Setting up Linode-CLI".

### Setting up Linode-CLI
Once you're logged in and find the link to Object Storage to [view your buckets](https://cloud.linode.com/object-storage/buckets) you should be ready to install the [Linode-CLI](https://www.linode.com/docs/platform/object-storage/how-to-use-object-storage/#linode-cli).

Run those pip commands to get programs you need and then run `linode-cli configure`. Select the defaults till they get to the part about the token.

Here's where you'll paste the value from your Personal Access Token.  Once that's pasted the configuration should be done and you'll have a new file at `$HOME/.config/linode-cli`.  This is basically a .toml file that contains the setup you did in the `linode-cli configure` step.  

If you see "Your OAuth token is not authorized to use this endpoint" you may need to double-check the API Token.  Its either wrong or missing either "Account" or "Object Storage" Read/Write access.

<a href="https://imgur.com/NrBpmzi"><img src="https://i.imgur.com/NrBpmzi.png" title="source: imgur.com" /></a>

If you ever need to make edits without redoing the configuration process you can edit $HOME/.config/linode-cli. 

### Create a Bucket and Upload a file
From this point on you can go back to [the basic guide](https://www.linode.com/docs/platform/object-storage/how-to-use-object-storage/#linode-cli) and run these two commands to verify everything is working:

`linode-cli obj mb my-example-bucket` This creates a bucket my-example-bucket.

`linode-cli obj put example.txt my-example-bucket` This uploads the file example.txt to your bucket.

Just don't forget to change the access to your bucket!  Run `linode-cli obj setacl --help` for more details. But just to be safe you can run `linode-cli obj setacl --acl-private YOUR-BUCKET` to set the whole bucket to private.

In my admittedly simple use-case I am simply looping over an array of directory names, zipping them, then sending them to the Pi and the Linode Bucket.  Here's the full Linode upload loop:

```
dirs=("Backup" "ovpns" "Pictures/Backgrounds" "Documents" "Music" "Audio/raw" "scripts")

for dir in "${dirs[@]}"
do
    fn=$(echo "$dir" | tr / _)
    linode-cli obj put /tmp/$fn.tar.gz $REMOTE_BACKUP
done
```

The `tr` only acts to remove the directory level separators.  `/` is not allowed in some cases so must be swapped out.

###### ! Check your bucket is not publicly accessible !
Go to `https://your-bucket.us-east-1.linodeobjects.com`. You should see an xml looking page saying "Access Denied" somewhere. This is assuming you don't want strangers accessing your bucket.

### Full Backup Solution
This is a 'dumb' backup solution but it does the job when I need it:
https://github.com/maxdobeck/scripts/blob/master/backup.sh

The final portion of the script will be the Linode Upload.  `$REMOTE_BACKUP` is the name of my bucket and you can see I'm simply calling the linode-cli tool.

